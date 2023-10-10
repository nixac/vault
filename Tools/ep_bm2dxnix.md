>[!info] About
>This tool supports launching IIDX through chainloading ep_bm2dx->ep_protonnix->proton(-ge)->spicetools
>Expects specific directory structure to work (that follows one used by steam)

# Directory structure

| Path | Overrideable | Description |
| --- | --- | --- |
| / | &#10004; (--root) | Steam library structured directory (that is including `steamapps/` in its root), default being `$ep_bm2dxnix_dir/../.steam/root/`  |
| /steamapps/common/ | &#10004; (--dumps) | Directory including each arcade game in its root  |
| /steamapps/common/Beatmania IIDX 26/contents | &#10008; | Main directory of game to be launched, spicetools executable in root |
| /steamapps/common/Beatmania IIDX 26/devel/debug/x64dbg | &#10008; | Main directory of x64dbg debugger |
| /steamapps/common/proton-ge-8.16 | &#10004; (through [`linux.json`](/Devel/Config%20File%20Specification#Valid%20keys)) | Path to proton installation root direrectory |
| /steamapps/compatdata/u573_bm2dx_w8 | &#10004; (through [`linux.json`](/Devel/Config%20File%20Specification#Valid%20keys)) | Path to proton prefix root direrctory |

^03e3f7



# Common use examples
>[!example]
>See script's help command for up-to-date examples
>```shell
>ep_bm2dxnix --help
>```

^0dc839


