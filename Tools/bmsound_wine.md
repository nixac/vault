> [!danger] *Library should be considered as unstable, first iteration guide*

>[!info] About
>This tool translates bemani's audio through providing bridge between wasapi exclusive and pipewire output node
>Expects [specific spicetools builds](https://codeberg.org/nixac/spicetools) in order to run


# Advanced configuration


>[!warning]
>As of writing only 44.1kHz stereo 16-bit audio should be considered supported
>This doesn't mean host has to switch to such format (resamping is done by pipewire server)

## Library
Library itself uses `audio.*` keys defined in [`linux.json`](/Devel/Config%20File%20Specification#Valid%20keys)
Right now you shouldn't need to adjust anything outside of fpc (frames per chunk), which you may want to decrease to get lower latencies (not lower than what your pipewire server is set to accept through `default.clock.min-quantum`) or increase if you get choppy sound (same limitation applies through `default.clock.max-quantum`) - power of 2 values are recommended, although not required

         

## Pipewire

> [!danger] *Missing necessary documentation*

# Development notes
See [feature listing documentation](/Devel/Feature%20support/Audio%20Backend%20(Pipewire))
