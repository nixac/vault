>[!tip] Incomplete
>Currently this guide applies only to Beatmania IIDX
>If you found inconsistencies or want to improve this guide - [you can report on it through an issue tracker](https://codeberg.org/nixac/pages/issues)

>[!info]
If you know what you are doing, probably [requirements](#Requirements) and [known issues](Confirmed%20Issues.md) is all you will need.

# Basic setup
> [!danger] *First iteration of this guide, while tested beforehand and self-contained, issues may araise*

>[!warning] Foreword
This guide assumes any arch based distribution for the sake of simplicity (but anything x86_64/glib should work), tested setups include Artix Linux and Gentoo.
Quick setup below makes use of various wrappers provided in [this repository](https://codeberg.org/nixac/automatization) making setup somehow consistent, even on fresh installs.
That doesn't mean you have to use them as it's mostly automatization and integration of proton without steam.

>[!abstract] 　
>1. Guide will use `LDJ-20210915` for setup, if you are unsure of what you are doing, that's the recommended version to follow with
>2. Guide will use `/var/games/` as starting directory for installation and expects game at `/var/games/iidx/LDJ-20210915`, therefore this step has to be completed if not done before
>```shell
>sudo usermod -aG games $USER
># restart system (or relogin user)
>```
>3. Get everything listed in [requirements](#Requirements), if you are unsure of what you are doing, below commands should be equal to completing this step (other than installing steam)
>```shell
>mkdir -p /var/games/iidx/requirements
>##    common system libraries utilized in wine    ##
># Follow steps from https://github.com/lutris/docs/blob/master/WineDependencies.md#archendeavourosmanjaroother-arch-derivatives
>##    automatization utils (skip if you have your own setup in mind)
>cd /var/games/iidx
>git clone https://codeberg.org/nixac/automatization --recurse-submodules
>##    proton ge (includes patching)    ##
>cd /var/games/iidx/requirements
>wget https://github.com/GloriousEggroll/proton-ge-custom/releases/download/GE-Proton8-16/GE-Proton8-16.tar.gz
>tar -xf GE-Proton8-16.tar.gz
>mv GE-Proton8-16 proton-ge-8.16
>cd proton-ge-8.16
>patch -p1 < /var/games/iidx/automatization/proton.patch
>cp /var/games/iidx/automatization/0005731108.py protonfixes/gamefixes/
>##    pipewire (recommended to follow guide for distribution you use instead)    ##
>sudo pacman -S pipewire pipewire-pulse pipewire-jack pipewire-alsa wireplumber gst-plugin-pipewire
>systemctl --user enable pipewire pipewire-pulse wireplumber
>systemctl --user start pipewire pipewire-pulse wireplumber
>sudo usermod -aG realtime,audio $USER
># restart system (or relogin user)
>##    get all custom requirements (most likely what you are missing)    ##
>cd /var/games/iidx/requirements
>wget https://codeberg.org/nixac/spicetools/releases/download/v0.1.0_2023-09-29/spicetools.tar.gz
>mkdir spicetools
>tar -xf spicetools.tar.gz -C spicetools
>git clone https://codeberg.org/nixac/bmsound_wine --recurse-submodules
>cd bmsound_wine
>make -Rs build TARGET_ARCH=x64 TARGET_TYPE=Release
>mv bin/Release/x64 ../bmsw
>```
>4. This is how your `/var/games/iidx` should look like by now, you may safely delete any directories not listed here
>```dirtree
>- /var/games/iidx/automatization
>- /var/games/iidx/requirements/proton-ge-8.16
>- /var/games/iidx/requirements/spicetools
>- /var/games/iidx/requirements/bmsw
>```
>5. Link `/var/games/iidx/.steam` to steam's game library, in this case we assume you use single library (so how default steam installation works)
>```shell
>cd "/var/games/iidx"
>ln -sfn $HOME/.steam .steam
>```
>6. Add game (in this case `LDJ-20210915`) and proton-ge to your library (in this case `proton-ge-8.16`), for person following step-by-step this would be a correct command
>```shell
>mv "/var/games/iidx/LDJ-20210915" "/var/games/iidx/.steam/root/steamapps/common/Beatmania IIDX 28"
>mv "/var/games/iidx/requirements/proton-ge-8.16" "/var/games/iidx/.steam/root/steamapps/common/proton-ge-8.16" 
>```
>7. Move all .dll files from `contents` to `contents/modules` if this isn't the case already, this should already be the case for `LDJ-20210915` 
>```shell
>mv "/var/games/iidx/.steam/root/steamapps/common/Beatmania IIDX 28/contents/"*.dll "/var/games/iidx/.steam/root/steamapps/common/Beatmania IIDX 28/contents/modules/"
>```
>8. Add bmsound_wine (audio solution for wine) and spicetools (wine fork) to game's dump, for person following step-by-step this would be a correct command
>```shell
>mv "/var/games/iidx/requirements/bmsw/bmsound-"* "/var/games/iidx/.steam/root/steamapps/common/Beatmania IIDX 28/contents/modules/"
>mv "/var/games/iidx/requirements/spicetools/spice"* "/var/games/iidx/.steam/root/steamapps/common/Beatmania IIDX 28/contents/"
>```
>9. Apply wine patches through <a target="" href="patcher/">provided patcher</a> if there are any, in case of `LDJ-20210915` you need a Bistrover specific fix
>10. Set your monitor to target refresh rate (so 120hz for this guide), example below assumes you want game on main monitor
>```shell
>xrandr
># replace device (--output) with output of command above at line like this "DP-0 connected primary ....", where you would replace "DP-0" with the one listed in your output
># replace resolution (--mode) with your native resolution, you don't need to target specific resolution (as long as ratio is correct)
>xrandr --output DP-0 --mode 1920x1080 --rate 120
>```
>11. Run ep_bm2dxnix to setup prefix and start spicetools config, if you followed step-by-step until now, this should be enough
>```shell
>/var/games/iidx/automatization/ep_bm2dxnix 28 --cfg
>```
>12. After you are done close config tool and run ep_bm2dxnix again to start the game, if you followed step-by-step until now, this should be enough
>```shell
>/var/games/iidx/automatization/ep_bm2dxnix 28
>```
>13. If everything worked you can safely delete `/var/games/iidx/requirements/`, but know that you will probably need to rebuild bmsound_wine on bigger system updates
>14. You can check [advanced setup](/Beatmania%20IIDX/Advanced%20Setup.md) for further/optional setup steps




# Requirements
* proton-ge with required patches as specified by guide (tested versions >8.11, recommended 8.16)
* pipewire (probably pre-configured to target lower latencies, but not necessary for getting games running)
* [this spice fork](https://codeberg.org/nixac/spicetools) (extends on spice2x at the moment, tuned for linux, but works on windows)
* [bmsound_wine](https://codeberg.org/nixac/bmsound_wine) (acts as bridge between spice and pipewire)
* optional, although recommended [extra tooling](https://codeberg.org/nixac/automatization)
* optional, although asumed in quick guide steam installation (steam installs itself in `~/.steam`, if `~/.steam/root` exists that should be enough)
* implies [common system libraries used by wine](https://github.com/lutris/docs/blob/master/WineDependencies.md#archendeavourosmanjaroother-arch-derivatives) being pre-installed (you probably have done this already, unless you haven't worked with proton/wine directly before)
* implies Xorg (although wayland should work, listed [workarounds](Confirmed%20Issues.md) may target X11)
* implies Nvidia (although AMD/Intel may work, listed [workarounds](Confirmed%20Issues.md) may be nvidia specific)

# Known quirks
Lists things with no known resolution at the moment, you may consider these as demerits compared to windows setup.
* windowed modes(`-w`)  don't detect touch events from subscreen, [subscreen works fine with `-touchemuforce` and fullscreen](Confirmed%20Issues.md#Subscreen%20doesn't%20detect%20input)
* IIDX 30 Resident's subscreen is missing most functionality
* multiple older titles don't support video playback
* all titles don't support effector