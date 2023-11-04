>[!tip] Looking for additional data
>If you know a possible solution, tested with different setup or found another issue [you can report on it through an issue tracker](https://codeberg.org/nixac/pages/issues)

>[!info]
>You may want to checkout [Game Support](Game%20Support.md) and `/Devel/<SPECIFIC_GAME>` if your issue doesn't seem to be listed here



# Framerate beyond 60
>[!attention]- **State**
>Resolved (Nvidia) / Unknown (AMD/Intel)

>[!question]- **Cause**
>Multi-monitor setup with varying refresh rates (AMD/Intel behaviour untested)

>[!todo] **Solution**
>Defining `__GL_SYNC_DISPLAY_DEVICE=<DEVICE_NAME>` environment variable, where `<DEVICE_NAME>` is a port name matching monitor used for game in `xrandr -q` output, this switches default vsync behaviour (which konami uses) to take monitor refresh rate from specified monitor instead of one with the lowest refresh rate, this requires specified monitor set up exactly to targeted refresh rate (which can be done temporarily with `xrandr` as well)

>[!note]- **Notes**
>* more on this behaviour [here](https://download.nvidia.com/solaris/535.113.01/README/openglenvvariables.html) (nvidia specific fix)
>* proper fix would most likely require spoofing actual refresh rate checks calls to always report 120/60 (no such patch currently)
>* `-graphics-force-refresh <VALUE>` doesn't affect the behaviour at slightest


# Subscreen doesn't detect input
>[!attention]- **State**
>Known partial workaround (invalid for windowed mode)

>[!question]- **Cause**
>Unknown, but probably caused by disabled/unavailable touch API in wine

>[!todo] **Solution**
>1. Add `-touchemuforce` as spice launch argument
>2. Launch game in fullsreen (see [quirks](#Known%20quirks))
>3. Use subcreen button (default F12) to toggle subscreen


# Launching in fullscreen
>[!done]- **State**
Resolved

>[!question]- **Cause**
>Multi-monitor setups

>[!todo] **Solution**
>Add spice argument `-graphics-force-single-adapter`, may want to combine this with `-monitor <ID>` for more complicated setups

# Crashes beyond IIDX24
>[!done]- **State**
>Resolved (linux specific)

>[!question]- **Cause**
>* filter functions that 64-bit iidx uses are unimplemented/broken in wine's 64-bit built-in/native libraries
>* no audio backend plays well with wine (this includes both asio and wasapi, both iidx and spice implementations)

>[!todo] **Solution**
> >[!abstract] 
> >- [ ] link to per style patches, they disable all in game effectors!
> >- [ ] address to not use wasapi shared patches (they work, but audio will be most likely garbled)
> >- [ ] use pipewire backend for spice

# Some old keybinds stay even after unbinding everything
>[!done]- **State**
>Resolved

>[!question]- **Cause**
> May happen when you directly migrate your `spicetools.xml` config file between machines/versions, most likely bug in config migration between spice versions

>[!todo] **Solution**
>1. Backup your current game/keybinds configuration for the game in questions
>2. Navigate to `<PREFIX>/drive_c/users/steamuser/AppData/Roaming/spicetools.xml` and remove at least `<buttons>..</buttons>` bracket under specific `<game name=""></game>` title issue happens for
>3. You may prefer to remove whole `<game name=""></game>` bracket and reset all settings for specific game (including lights, card config etc.) to be on safe side, as this issue happens due to key duplicates in config and there may be more old config residue

# Missing effectors beyond IIDX24
>[!missing]- **State**
>Unresolved, known possible fix, unknown implementation ETA

>[!question]- **Cause**
>* caused by [workaround necessary at the moment](#Crashes%20beyond%20IIDX24)
>* 64-bit wine libraries not providing [`IDirectSoundFXWavesReverb`](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ee418241(v=vs.85)) [`IDirectSoundFXDistortion`](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ee418218(v=vs.85)) [`IDirectSoundFXChorus`](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ee418208(v=vs.85))  [`IDirectSoundFXFlanger`](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ee418225(v=vs.85)) [`IDirectSoundFXGargle`](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ee418228(v=vs.85))
>* 64-bit wine libraries implementation of [`IDirectSoundFXEcho`](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ee418221(v=vs.85)) seems to not do anything

>[!todo] **Solution**
>> [!missing]

# No video playback prior to IIDX27

>[!missing]- **State**
>Unresolved, possibly fixable through some library overrides

>[!question]- **Cause**
>* unknown cause in case of iidx24- (probably missing libraries)
>* patches for audio graph in case of iidx25-26

>[!todo] **Solution**
>> [!missing]


# Game keeps maxing one of CPU threads at idle
>[!done]- **State**
>Known and intentional, alternative endpoint available

>[!question]- **Cause**
>This is caused by [bmsound_wine](/Tools/bmsound_wine)'s "notif_spice" profile endpoint

>[!todo] **Solution**
>> [!attention] 　
>> You can use alternative audio endpoint by defining ["signal_spice" profile through config](/Devel/Config%20File%20Specification#Using%20alternative%20audio%20backends)
>> This endpoint will become the default once [this refactor is completed](https://codeberg.org/nixac/bmsound_wine/issues/2)
>> Since winelib had issues with calling back to wine's address space from unix thread, "notif_callback" endpoint profile (more optimal for use with spice) cannot be used
>> To keep latency penalty at the lowest, alternating busy lock between threads was introduced as a temporary solution
>> CPU scheduling on most kernels should handle this well enough for end-user to not notice any additional stutters
>> That being said, this may cause stutters on low-end systems and higher battery usage for mobile devices



# My hooks won't hook
>[!done]- **State**
>Known cause (out-of-scope)

>[!question]- **Cause**
>* VA of dll base is most likely hard-coded

>[!todo] **Solution**
>If you can, ask maintainer of your hook to properly fetch dll's base address from inside hook in question, alternatively if source code of the hook is available you can create issue on [this tracker](https://codeberg.org/nixac/pages) and patched hook should be provided, below is usually all that's needed to make hook work consistently
>```c
>// wrong (right most of the time)
>static const long VA_BASE = 0x180000000
>
>// correct (always right)
>const long VA_BASE = (long)GetModuleHandle("game.dll")
>```

>[!note]- **Notes**
>As this is usually caused by author's [wrong presumption](https://stackoverflow.com/questions/8716375/is-dll-always-have-the-same-base-address) that virtual address of the game's dll is guaranteed at runtime (often `0x180000000`), this is not really something that can be reliably worked around from here
>While wine makes sure to map libraries to their preferred base addresses where possible, in cases with multiple libraries requesting the same VA, next libraries with the same base address will be reallocated
>This is the case for example with `bm2dx.dll` and `avs2-core.dll`, which both target the same address (with `avs2-core.dll` loading first, while author probably intended for `bm2dx.dll` to map there instead)

