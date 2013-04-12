# What is it?

A vanilla precompiled binary of [PortAudio](http://portaudio.com) for Windows.

## Why do we want it?

Because compiling for five different audio subsystems on two different architectures is a pain in the postprocessor. Especially since you need things like multiple versions of multi-architecture device driver kits...

## How was it done?

See the [Configuration](Configuration.md) file for all the ghastly and gristly details.

## What do I get?

You get [PortAudio](http://portaudio.com) [SVN r1889](https://subversion.assembla.com/svn/portaudio/portaudio/) as of April 11, 2013 built with Microsoft Visual Studio 2012 on Windows 7 (x64) host using Steinberg's [ASIO SDK](http://www.steinberg.net/en/company/developer.html) version 2.2, using the Microsoft `v7.1` and `v8.0a` software development kits as required for WDM/KS and DS on all operating systems and architectures.

**All audio subsystems were enabled.** Specifically `PA_USE_ASIO=1`, `PA_USE_DS=1` (DirectSound), `PA_USE_WMME=1` (MME), `PA_USE_WASAPI=1`, and `PA_USE_WDMKS=1` (WDM/KS). The `PAWIN_USE_WDMKS_DEVICE_INFO` preprocessor symbol was also defined.

All `Debug`, `Release`, and `ReleaseMinDependency` configurations were generated for both `x32` and `x64` architectures, resulting in six end-targets. **For normal use, I recommend the `ReleaseMinDependency` configuration unless you _really_ know what you're doing.**

# Where are they?

You can download, use, or abuse any of the following:

* **Probably all you need** is [**portaudio-r1889-build.zip**](portaudio-r1889-build.zip), including libs, dlls, and the header
* [portaudio-r1889-src-orig.zip](portaudio-r1889-src-orig.zip) is the orignal source tree as pulled from subversion
* [portaudio-r1889-src-built.zip](portaudio-r1889-src-built.zip) is the entire project
    * after all the stuff noted in [Configuration](Configuration.md)
    * minus the ASIO SDK and built binaries
    * but including all the VS conversion and build logs...
    * ... which are _huge_ and mostly useless

**Enjoy!**
