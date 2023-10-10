
# Crash during shader creation
>[!tip] Crash point: 0x180176996

>[!done]- **State**
Resolved

>[!todo] **Solution**
>```python
># crash at 0x180176996, shader compilation fails for D3DXAssembleShader() - crash with missing callback Direct3DShaderValidatorCreate()
>util.protontricks("d3dcompiler_43")
>util.protontricks("d3dx9_43")
># this exact override needed for subscreen to be created
>util.set_environment("WINEDLLOVERRIDES", "d3dcompiler_43=n,b")
>```


>[!note]- Notes
>shouldn't jump at 0x1801769A3 (in windows), crashing callback runs only once, but underhood d3d9 call runs a lot (and triggers multiple times before crash implying it works on linux)
>this implies that it is missing parameter_1 for this specific call
>patching out to NOP and then JMP results in missing character for title screen on windows, out-of-frame crash on linux, seems to be actual shader compilation issue
>
> possible dxvk incompatibility, see https://github.com/doitsujin/dxvk/issues/1377
> fixed by using native d3dcompiler_43.dll+d3dx9_43.dll (all that's needed)


