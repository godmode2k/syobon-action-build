# SyobonAction Win32 Build Guide


Environment
----------
> Build all and Tested on Windows 10 <br>
> SyobonAction_rc2, SDL1.x based, VS2022, Windows 10 <br>


<img src="https://github.com/godmode2k/syobon-action-build/raw/main/screenshots/007_000 run.jpg" width="50%" height="50%">
<br>


```sh
You can also see the directory in the screenshots.
SEE: <screenshots> directory


-------------------------------------------------------------------
0. SyobonAction and Dependencies
-------------------------------------------------------------------
[SyobonAction]
http://opensyobon.sourceforge.net/
 - SyobonAction_rc2_src.tar.gz
 - SyobonAction_rc2_data.tar.gz

(Download)
https://sourceforge.net/projects/opensyobon/files/src/SyobonAction_rc2_src.tar.gz/download
https://sourceforge.net/projects/opensyobon/files/src/SyobonAction_rc2_data.tar.gz/download


[Dependencies]
SDL1.x based
https://www.libsdl.org/release/
https://www.libsdl.org/projects/SDL_image/release/
https://www.libsdl.org/projects/SDL_mixer/release/
https://www.libsdl.org/projects/SDL_ttf/release/
https://sourceforge.net/projects/sdlgfx/

(Download)
https://www.libsdl.org/release/SDL-devel-1.2.15-VC.zip
https://www.libsdl.org/projects/SDL_image/release/SDL_image-devel-1.2.12-VC.zip
https://www.libsdl.org/projects/SDL_mixer/release/SDL_mixer-devel-1.2.12-VC.zip
https://www.libsdl.org/projects/SDL_ttf/release/SDL_ttf-devel-2.0.11-VC.zip
https://sourceforge.net/projects/sdlgfx/files/SDL_gfx-2.0.27.tar.gz/download


-------------------------------------------------------------------
1. Source code
-------------------------------------------------------------------
Win32 (x86 only)

Visual Studio 2022 (VS2022)

1.1. Main Project: Create an Empty Project (Empty Project: C++, Windows, Console)
(Add a SyobonAction Source files)
Add: (VS2022: Solution Explorer > Solution > ProjectName > Header Files)
 - DxLib.h, joyconfig.h, main.h
Add: (VS2022: Solution Explorer > Solution > ProjectName > Source Files)
 - DxLib.cpp, loadg.cpp, main.cpp

1.2. Adds Existing Project (SDL_gfx: SDL_gfx_VS2022.vcxproj)
Add: (VS2022: Solution Explorer > Solution > Add > Existing Project)
 - SDL_gfx_VS2022.vcxproj
Retarget Projects: (VS2022: Solution Explorer > Solution > SDL_gfx > Retarget Projects)


-------------------------------------------------------------------
2. Main Project: Headers
-------------------------------------------------------------------
[Configuration: All Configurations]
[*] Project Property Pages > Configuration Properties > C / C++ > General > Additional Include Directories
Add:
 - SDL-devel-1.2.15-VC\SDL-1.2.15\include
 - SDL_image-devel-1.2.12-VC\SDL_image-1.2.12\include
 - SDL_mixer-devel-1.2.12-VC\SDL_mixer-1.2.12\include
 - SDL_ttf-devel-2.0.11-VC\SDL_ttf-2.0.11\include
 - SDL_gfx-2.0.27


-------------------------------------------------------------------
3. SDL_gfx Project: Headers, LIbs
-------------------------------------------------------------------
[Configuration: All Configurations]
[*] SDL_gfx Project Property Pages > Configuration Properties > C / C++ > General > Additional Include Directories
Add:
 - SDL-devel-1.2.15-VC\SDL-1.2.15\include

[Configuration: All Configurations]
[*] SDL_gfx Project Property Pages > Configuration Properties > Linker > General > Additional Library Directories
Add:
 - SDL-devel-1.2.15-VC\SDL-1.2.15\lib\x86

[Configuration: All Configurations]
[*] SDL_gfx Project Property Pages > Configuration Properties > Linker > General > Input > Additional Dependencies
Add:
 - SDL.lib


-------------------------------------------------------------------
4. Main Project: Libs (x86 only)
-------------------------------------------------------------------
[Configuration: All Configurations]
[*] Project Property Pages > Configuration Properties > Linker > General > Additional Library Directories
Add:
 - SDL-devel-1.2.15-VC\SDL-1.2.15\lib\x86
 - SDL_image-devel-1.2.12-VC\SDL_image-1.2.12\lib\x86
 - SDL_mixer-devel-1.2.12-VC\SDL_mixer-1.2.12\lib\x86
 - SDL_ttf-devel-2.0.11-VC\SDL_ttf-2.0.11\lib\x86

[Configuration: Debug]
Add: SDL_gfx-2.0.27 path
 - SDL_gfx-2.0.27\Debug

[Configuration: Release]
Add: SDL_gfx-2.0.27 path
 - (PROJECT_DIR)\Release


[Configuration: All Configurations]
[*] Project Property Pages > Configuration Properties > Linker > General > Input > Additional Dependencies
Add:
 - SDLmain.lib;SDL.lib;SDL_image.lib;SDL_mixer.lib;SDL_ttf.lib;SDL_gfx.lib


-------------------------------------------------------------------
5. Errors
-------------------------------------------------------------------
5.1 Main Project: UTF-8 encoding
[Configuration: All Configurations]
[*] Project Property Pages > Configuration Properties > C / C++ > Command Line > Additional Options
Add:
 - /utf-8

5.2 DxLib.h: SDL headers
Add: (SEE: Index 2. Headers)
#include <SDL.h>
#include <SDL_rotozoom.h>
#include <SDL_gfxPrimitives.h>
#include <SDL_image.h>
#include <SDL_mixer.h>
#include <SDL_ttf.h>


5.3 DxLib.h: setlocale()
Add:
#include <locale.h>


5.4 DxLib.cpp: vsprintf()
Add:
//vsprintf(newstr, str, args);
vsprintf_s(newstr, strlen(str) + 16, str, args);


5.5 DxLib.cpp: Unable to init SDL_mixer: DirectSound CreateSoundBuffer: Invalid parameter
Add:
//if (sound && Mix_OpenAudio(22050, AUDIO_S16SYS, AUDIO_CHANNELS, 1024)) {
if (sound && Mix_OpenAudio(22050, AUDIO_S16SYS, 2, 4096)) {


5.6 loadg.cpp: strcasecmp()
Add:
//if(!strcasecmp(argv[i], "-nosound")) sound = false;
if(!_stricmp(argv[i], "-nosound")) sound = false;


5.7 SDL_gfx Project: SDL_gfxPrimitives.c: lrint()
...
#elif defined(_M_IX86)
Add:
//    comment-out: __inline long int lrint(double flt) { ... }
/*
__inline long int lrint(double flt) {
    ...
}
*/
#elif defined(_M_ARM)
...


5.8 Main Project: unresolved external symbol __imp____iob_func referenced in function _ShowError
Create a stub file(iob_stub.cpp)
Add: (VS2022: Solution Explorer > ProjectName > Source Files)
    Filename: iob_stub.cpp
    #include <stdio.h>
    extern "C" { FILE __iob_func[3] = { *stdin, *stdout, *stderr }; }


5.9 SDL_gfx Project: Post-Build (Copy files)
[Configuration: Debug]
[*] SDL_gfx Project Property Pages > Configuration Properties >Build Events > Post-Build Event > Command Line
copy "$(TargetPath)" "$(SolutionDir)\Test\$(Configuration)"
copy "$(ProjectDir)\..\SDL-1.2\VisualC\SDL\Win32\Debug\SDL.dll" "$(SolutionDir)\Test\$(Configuration)"

Error: MSB3073 ...

Ignore error message or adds 'echo'
[Configuration: Debug]
[*] SDL_gfx Project Property Pages > Configuration Properties >Build Events > Post-Build Event > Command Line
Add: (echo)
echo copy "$(TargetPath)" "$(SolutionDir)\Test\$(Configuration)"
echo copy "$(ProjectDir)\..\SDL-1.2\VisualC\SDL\Win32\Debug\SDL.dll" "$(SolutionDir)\Test\$(Configuration)"


-------------------------------------------------------------------
6. Run (PROJECT_ROOT/output directory)
-------------------------------------------------------------------
Copy DLLs and SyobonAction_rc2_data to <output> directory.

[SDL-devel-1.2.15-VC]
SDL.dll

[SDL_image-devel-1.2.12-VC]
SDL_image.dll

[SDL_ttf-devel-2.0.11-VC]
SDL_ttf.dll

[SDL_mixer-devel-1.2.12-VC]
SDL_mixer.dll
libFLAC-8.dll
libmikmod-2.dll
libogg-0.dll
libvorbis-0.dll
libvorbisfile-3.dll
smpeg.dll

[SDL_gfx-2.0.27]
SDL_gfx.dll

[SyobonAction_rc2_data.tar.gz]
BGM/
res/
SE/
icon.ico


Main Project: [Post-Build (Copy files to <output> directory)]
[Configuration: All Configurations]
[*] Project Property Pages > Configuration Properties >Build Events > Post-Build Event > Command Line
Add:
copy $(TargetDir)$(TargetFileName) $(SolutionDir)..\output
copy $(TargetDir)SDL_gfx.dll $(SolutionDir)..\output


[Run]
PROJECT_ROOT/output/(PROJECT_NAME).exe
```

