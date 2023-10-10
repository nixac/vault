
# .dll<->dll.so<->.so valid pair

In order to create a library that allows use of linux shared libraries directly from within wine process we use [winelib](https://wiki.winehq.org/Winelib)

> [!info]
> Example for release build of `bmsound-wine.dll`, a wrapper dll that forwards linux specific pipewire's API from `bmsound-pw.so` (separate linux project unaware of windows environment)


> [!example]- bmsw.c
> ```c
> #include "bmsound-pw/bmsound_pw.h"  
> #include <windef.h>
> 
> 
> int WINAPI BmswClientInit(const char *title)  
> {  
>     return pw_client_init(title); // linux-side call to bmsound-pw.so
> }
> ```

> [!example]- bmsound-wine.dll.spec
> ```shell
> # Exported functions names to wrapper function names
> @ stdcall BmswClientInit(ptr) BmswClientInit
> ```

> [!note] 
> More about [implementing dll](https://wiki.winehq.org/Developer_Hints)


## Building
```shell
# Compilation stage of bmsound-wine
winegcc -fPIC -c src/bmsound-wine/bmsw.c -o build/Release/x64/bmsound-wine.d/bmsw.o -fms-extensions -std=gnu99 -m64 -O3 -DNDEBUG -Ilib/include -Ilib/include/x64 -Ibuild/Release/x64/include -iquotesrc/bmsound-wine -Ibin/Release/x64/include/bmsound-pw
# Linking stage for bmsound-wine.dll.so
winegcc -shared -o build/Release/x64/bmsound-wine -mno-cygwin src/bmsound-wine/bmsound-wine.dll.spec build/Release/x64/bmsound-wine.d/bmsw.o -fms-extensions -Wno-microsoft-anon-tag -Wno-narrowing -Wno-conversion -std=gnu99 -m64 -O3 -DNDEBUG -l:bmsound-pw.so -Lbuild/Release/x64
# Linking stage for bmsound-wine.dll (this is a fake that calls .so variant instead)
winebuild -m64 --dll --fake-module -E src/bmsound-wine/bmsound-wine.dll.spec -o build/Release/x64/bmsound-wine/bmsound-wine.dll build/Release/x64/bmsound-wine/bmsw.o

```

## Running

>[!note]
> Library pair we just built are considered a builtin library, which are expected to be located in lib directories of each runtime.
> Steps below are needed to correctly load libraries from user provided directory

>[!warning]- Proton compatibility
> In case of proton additional patch below is necessary as at the time of writing proton doesn't preserve user provided `WINEDLLPATH`
> ```diff
> --- upstream/proton
> +++ mod/proton
> @@ -1462,6 +1462,8 @@
>          prepend_to_env_str(self.env, ld_path_var, g_proton.lib64_dir + ":" + g_proton.lib_dir, ":")
>  
>          self.env["WINEDLLPATH"] = g_proton.lib64_dir + "/wine:" + g_proton.lib_dir + "/wine"
> +        if len(os.getenv("WINEDLLPATH") or ""):
> +            self.env["WINEDLLPATH"] = os.getenv("WINEDLLPATH").strip(":") + ":" + self.env["WINEDLLPATH"]
>  
>          self.env["GST_PLUGIN_SYSTEM_PATH_1_0"] = g_proton.lib64_dir + "gstreamer-1.0" + ":" + g_proton.lib_dir + "gstreamer-1.0"
>          self.env["WINE_GST_REGISTRY_DIR"] = g_compatdata.path("gstreamer-1.0/")
> 
> ```

```shell
# for user libraries at "$PWD/modules" or system paths (.dll/dll.so/.so)
LD_LIBRARY_PATH="$PWD/modules" WINEDLLPATH="$PWD/modules" proton waitforexitandrun 'spice64.exe' -io -iidx -modules modules -iidxsounddevice wasapi -audiobackend pipewire -audiodummy -eamaint 1 -ea -graphics-force-single-adapter -monitor 0 -nolegacy -touchemuforce --iidxtdj -w
```


