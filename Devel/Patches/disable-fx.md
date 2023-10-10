>[!info]
> Disables effectors
> Effectors init order should be distortion->chorus->flanger->gargle->(loops)


# Quick and dirty
>[!warning]
>Should work quite reliably for any version of IIDX27-31
>Leaving out IIDX25-26 until I make sure patch doesn't cause video playback to break

1. In hex editor search for these patterns and patch first 5 bytes of pattern to NOP (0x90)
```log
distortion: E8 4A A2 00 00 90
chorus: E8 9A 99 00 00 90
flanger: E8 CA 89 00 00 90
gargle: E8 AA 82 00 00 90
```

# How to identify
> [!danger] *Missing necessary documentation*
