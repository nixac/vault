> [!info]
> Optional per-game config file
> File expected at `prop/linux.json` of game root (usually `contents`), integrated into automatization tools and bmsound libraries





>[!example]
> Below is an example config file with all defaults
>>[!warning]
>>Every key is optional and not required
>>Do not define keys you aren't sure you need
>>Usually all you will need is just `network.url` and `network.pcbid` or no config file at all
>>You may want to check some [examples](#Common%20examples) as well
>```json
>{
>    "video": {
>        "profile": "stable",
>        "display": 0,
>        "refresh_rate": 0,
>        "fsr": -1
>    },
>    "audio": {
>        "profile": "notif_spice",
>        "fpc": 64,
>        "channels": 2,
>        "depth": 16
>    },
>    "network": {
> 	   "url": "dummy",
> 	   "pcbid": null
>    },
>    "extra": {
>        "hooks": [],
>        "devel": [],
>        "lightning_support": true,
>        "dll_override": null,
>        "rt_override": null,
>        "pfx_override": null
>    }
>}
>```



# Valid keys

| Key | Default Value | Description |
| --- | --- | --- |
| audio.profile | "notif_spice" | bmsound-wine API profile that should be used, alters audio behaviour, "stable" will be default in future |
| audio.fpc | 64 | frames per chunk, amount of frames generated each process, `fpc*100/44100` for latency in ms, lower values equal lower latency |
| audio.channels | 2 | frame channel count (2/4/6/8) |
| audio.depth | 16 | sample depth in bits (16/24/32) |
| video.display | 0 (primary) | monitor by index to be used, vsync target by default |
| video.refresh_rate | 0 (currently active) | value to use for display, wrong value may alter game speed |
| video.profile | "stable" (adjust per-style) | alters window type, includes "force_fullscreen", "force_window", recommended left as "stable" for best match per-style |
| video.display | 0 (primary) | monitor by xorg ID to be used, source of refresh rate (missing wayland logic) |
| video.refresh_rate | 0 (currently active) | value to use for display, wrong value may alter game speed (missing wayland logic) |
| video.fsr | -1 (disabled) | [FidelityFX Super Resolution](https://www.amd.com/en/technologies/radeon-software-fidelityfx), value being sharpening power in scale 0-5 (sharpest-smoothest), not sure about its impact on latency |
| network.url | "dummy" (spice dummy network) | EA integration URL (null to read from ea3-config.xml) |
| network.pcbid | null (read from ea3-config.xml) | EA integration machine PCBID |
| extra.hooks | [] | list of additional hooks, located in modules directory (ex. `["omnimix-banner.dll","omnimix-dataloader.dll"]`) |
| extra.devel | [] (use defaults) | list of additional spice arguments added in the specified order, when running with --dbg1 enabled (ex. `["-graphicsverbose"]`) |
| extra.lightning_support | true | enables lightning model for supported titles (not recommended, unstable) |
| extra.dll_overddide | null (use default) | overrides game's main .dll name (not recommended, unstable) |
| extra.rt_override | null (use default) | overrides proton runtime used for this title (not recommended) |
| extra.pfx_override | null (use default) | overrides proton prefix used for this title (not recommended) |

# Common examples

## Connection with ambiguous network
>[!example]- linux.json
>```json
>{
>    "network": {
> 	   "url": "http://127.0.0.1:1108/",
> 	   "pcbid": "00010203040506070809"
>    }
>}
>```

## Forced fullscreen on second 120hz monitor
>[!example]- linux.json
>```json
>{
>    "video": {
>        "profile": "force_fullscreen",
>        "display": 1,
>        "refresh_rate": 120
>    }
>}
>```

## Using additional hooks
>[!example]- linux.json
>```json
>{
>    "extra": {
>        "hooks": [
>            "omnimix_banner.dll",
>            "fervidex.dll"
>        ]
>    }
>}
>```

## Forcing 60hz LDJ for IIDX27+
>[!warning]
> This usage is not properly tested and therefore considered unsupported
> While it may work it's not recommended unless you are sure you need it and ready to debug issues yourself if any would be to arise

>[!example]- linux.json
>```json
>{
>    "video": {
>        "profile": "force_window",
>        "display": 0,
>        "refresh_rate": 60
>    },
>    "extra": {
>        "lightning_support": false
>    }
>}
>```

## Using vanilla proton in separate prefix
>[!warning]
> This usage is not properly tested and therefore considered unsupported
> It is recommended to always use proton-ge as it provides built-in support for winetricks
> Prefix-per-game approach is unnecessary as automatization tools should handle incompatibilities between titles if any internally and create prefixes as needed

>[!example]- linux.json
>```json
>{
>    "extra": {
>        "rt_override": "Proton - Experimental",
>        "pfx_override": "proton_vanilla_arcade"
>    }
>}
>```

