## Volk

One opensource library that permits get access to vector instructions in the processor, without any assembly code. Volk is a part of GNURadio initiative, with the objective that gets better performance to DSP application. Good for DSP, hum, good for anyone? Windows. What… in Windows. Yes, in Windows. I work in a company the develop only for this platform. And we have some issues with performance is some specific points of our legacy code. Then volk maybe is a good idea. But I do not find Volk for Windows on the internet. Nether build from source is working.

https://libvolk.org

https://github.com/gnuradio/volk

## Time to patch and think

Volk developer makes a crazy work good with the CMake build. But compiling to Visual Studio 2013–2015 doesn’t work. I found forums and posts about some issue with MMX instructions, this is no more compatible with the Microsoft compilers. But my version already has this patches. My Hypothesis, another incompatibility in recent versions of the compiler. Making a change in the CMakeLists, I got a workaround this problem. The project now compiling and linking. If the flag LV_HAVE_SEE is enabled, I give linkage errors. Then only create a condition if the compiler is VS 2013 or above, not enable the SSE flag.
## change lib/CMakeLists.txt

https://github.com/0um/volk/commit/9c1456c0dec765ed1801d45e2d839257b9a781ca
```
#MSVC 64 bit does not have MMX, overrule it
 if (${SIZEOF_CPU} EQUAL 64 AND MSVC)
     OVERRULE_ARCH(mmx "No MMX for Win64")
    +	if (MSVC_VERSION GREATER 1700)
    +            OVERRULE_ARCH(sse "No SSE for Win64 Visual Studio 2013")
    +        endif()
 endif()
```

## CI AppVeyor

Then I try to understand why the CI of the project not working with my modifications, maybe I have made a mistake. The project has a YMLfile to the AppVeyor CI, but cloning doesn’t. I don’t have success to create my personal YML file, something with the Boost lib, showing in the error log. The AppVeyor image has various version of the Boost, but setting the BOOST_ROOT doesn’t work for the volk. Neither set the path to the C:\Library files. Have to be the same that the original YML file, install Boost modules dependencies with NuGet. And pass on arguments in the compiler configuration. Then the build compiles, and generate the artifacts.

https://ci.appveyor.com/project/0um/volk