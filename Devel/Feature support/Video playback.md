
>[!missing] State
>Unimplemented

>[!abstract] Affected Titles
> * Beatmania IIDX 25-26

>[!info]
>Opposite to what the feature may imply, video playback/codecs itself should be fine, it's the audio that's the issue (for video-only files, yes)
>Affected titles use 64-bit winmm.dll and audio graph a lot, resulting in once again filters crashes (which aren't even in use)
>Patches to fix those winmm.dll crashes include disabling one callback (with fail branch of `"Could not create source filter to graph!  hr=0x%x"`), which seems to result in no video stream being outputted, may be some flag, may be whole graph? being uninitialized, hard to tell without extensive winmm internals knowledge 



# Video playback
>[!missing]- **State**
`Could not create source filter to graph!  hr=0x80040218`

>[!note]- Notes
>`winegstreamer: failed to create glupload, are 32-bit GStreamer "base" plugins installed?` even though 32bit plugin is present may imply issues on proton-ge side, possibly need to test with variousn proton version, if no change move to dependency tree - something (from d3d9 family) may need native implementation

