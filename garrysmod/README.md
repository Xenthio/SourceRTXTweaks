# Garry's Mod RTX Tweaks

This is a set of unofficial patches for Garry's Mod to work better with RTX Remix, fixing missing shadows, light leaks, and crashes.

DISCLAIMER: Garry's Mod is a VAC protected game. By using these patches, you accept that I am not responsible for any issues or damages resulting from the use of these patches. Do not use Garry's Mod in VAC protected servers while using RTX Remix, these patches, or any other modification.

Now that that's out of the way, BPS patches are provided to apply the following patches to your game. I recommend the use of [Floating IPS](https://www.romhacking.net/utilities/1040/) to perform the patching, as it is lightweight, simple, and straightforward to use. These patches are built against Build ID 8912316, June 10, 2022. All technical info statements below are in the context of a disassembler such as IDA Pro or Ghidra.

## Contents

[Patches](https://github.com/BlueAmulet/SourceRTXTweaks/tree/main/garrysmod#patches)  
[Crash Fixes](https://github.com/BlueAmulet/SourceRTXTweaks/tree/main/garrysmod#crashes)  
[Recommendations](https://github.com/BlueAmulet/SourceRTXTweaks/tree/main/garrysmod#recommendations)

## Patches

### c_frustumcull

**engine.dll:**  
244FC0: Change `55 8B EC` to `32 C0 C3`  
245050: Change `55 8B EC` to `32 C0 C3`

**client.dll:**  
361C00: Change `55 8B EC` to `32 C0 C3`

### r_forcenovis

**client.dll:**  
254522: Change `00` to `01`

### r_frustumcullworld

**engine.dll:**  
EFD36: Change `7E` to `EB`  
EFDD3: Change `75` to `EB`

ECC45: Change `75` to `EB`

## Crashes

### failed to lock vertex buffer in CMeshDX8::LockVertexBuffer

Credits to [@khang06](https://github.com/khang06) for this fix, found [here](https://github.com/khang06/misc/tree/master/reversing/source/portalrtxvbfix)

<details>  
<summary>Technical info</summary>

Search for "CMeshMgr::FindOrCreateVertexBuffer (dynamic VB)" and go to the function referencing this string.  
At the top of the function should be a function call taking two arguments, go inside this function.  
This function should consist of a single call followed by a value return:  
`function_call(0, a1, a2, v3);`  
After the function call and eax has been loaded, add in the following instructions:  
```  
test   eax,eax  
jne    +0x2  
mov    al,0x4  
```

</details>

**shaderapidx9:**  
35020: Change `83 C4 10 8B E5 5D C3 CC CC CC` to `85 C0 75 02 B0 04 8B E5 5D C3`

### gm_construct, gm_flatgrass, and other shader based skybox maps crash on load

Go inside `garrysmod\bin` and rename `game_shader_generic_garrysmod.dll` to something else.  
This will remove shaders for shader based skyboxes, and post processing effects which aren't used with RTX Remix anyway.  
Note: While the maps will load now, they still lack a skybox and may or may not have a functioning sun.

## Recommendations

Part of the process to using RTX Remix with GMod is to replace the menu files with ones from [here](https://github.com/robotboy655/gmod-lua-menu)  
If you removed the `include( "loading.lua" )` line from `garrysmod\lua\menu\menu.lua`, then also go into `garrysmod\lua\menu\background.lua` and make the following change:  
```  
local function ShouldBackgroundUpdate()

	return false

end  
```  
This will prevent an endless spam of script errors.  