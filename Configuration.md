# Windows Build Configuration and Notes

Build Host and Sofware Versions:

* [**PortAudio**](http://portaudio.com) [SVN r1889](https://subversion.assembla.com/svn/portaudio/portaudio/) (as of April 11, 2013)
* Visual Studio 2012 on Windows 7 (x64)
* Windows SDK for Windows 8 (November 15, 2012)
    * Provides the DirectX SDK
    * All components downloaded
* Microsoft Windows SDK for Windows 7 and .NET Framework 4, Version 7.1 (May 19, 2010)
    * Needed for the `ksguid.lib` and `ksuser.lib` import files for the WDM/KS driver
    * Downloaded [from here](http://www.microsoft.com/en-us/download/details.aspx?id=8279) as per [wikipedia](http://en.wikipedia.org/wiki/Microsoft_Windows_SDK)
    * Alternatively, just unzip the archive that I made for myself (but cannot redistribute, due to Microsoft's license, sorry)
* Windows Driver Kit (WDK) 8 (August 15, 2012)
    * Required for the WDM/KS driver
* Windows Driver Frameworks Co-Installers (August 17, 2012)
    * Appears required for older OSes
* Steinberg's [ASIO SDK](http://www.steinberg.net/en/company/developer.html) version 2.2

## Build Procedure

Modified from the original PortAudio [documentation](http://portaudio.com/docs/v19-doxydocs/compile_windows.html) for building on Windows.

1. Install Visual Studio 2012 on a 64-bit Windows 7 host.
2. Install the Windows 8 Sofware Development Kit.
3. Install the Driver development kit.  The "WDF Co-Installers" will be installed next.
4. Install the Windows Driver Frameworks Co-Installers.
4. From the ASIO SDK archive, copy the `ASIOSDK2` folder to `src\hostapi\asio\`.
5. Rename the copied `ASIOSDK2` folder to `ASIOSDK`.
6. Open `build\msvc\portaudio.sln` and allow the VS Wizard to update it.
7. Verify that no errors were encountered in the conversion. The warnings appear safe to ignore.
8. Edit the `portaudio` project `Properties`.
    * Select `All Platforms` for the following changes
    * For the `Release` and `ReleaseMinDependency` configurations:
        * Set `C/C++ / Code Generation / Floating Point Model` to `Fast`
        * Set `C/C++ / Optimization / Omit Frame Pointers` to `Yes`
9. Save and close Visual Studio.

You will have a freshly-created `portaudio.vcxproj` file. Open it in a text editor. Be *careful* to _**not**_ open the old `portaudio.vcproj` by mistake. In fact, you should probably delete `portaudio.vcproj` specifically to prevent this.

In `portaudio.vcxproj`, using a text editor (because it is way easier than trying to do it via Visual Studio), make the following changes:

* Replace all occurences of `PA_USE_ASIO=0` with `PA_USE_ASIO=0`
* Replace all occurences of `PA_USE_DS=0` with `PA_USE_DS=1`
* Remove the `PA_WDMKS_NO_KSGUID_LIB` and `PAWIN_WDMKS_NO_KSGUID_LIB` definitions.

For all configurations the `PA_USE_ASIO`, `PA_USE_DS` (DirectSound), `PA_USE_WMME` (MME), `PA_USE_WASAPI`, and `PA_USE_WDMKS` (WDM/KS) should all be defined and set to `1`. The `PAWIN_USE_WDMKS_DEVICE_INFO` preprocessor symbol should also be defined.

Next, and again in the `portaudio.vcxproj` file, replace all `<AdditionalDependencies>` blocks with the following. For *x86* targets, use

    <AdditionalDependencies>\Program Files\Microsoft SDKs\Windows\v7.1\Lib\ksuser.lib;\Program Files\Microsoft SDKs\Windows\v7.1\Lib\ksguid.lib;%(AdditionalDependencies)</AdditionalDependencies>`

and for *x64* targets, use

    <AdditionalDependencies>\Program Files\Microsoft SDKs\Windows\v7.1\Lib\x64\ksuser.lib;\Program Files\Microsoft SDKs\Windows\v7.1\Lib\x64\ksguid.lib;%(AdditionalDependencies)</AdditionalDependencies>

or whatever paths actually point to the `ksguid.lib` and `ksuser.lib` import libraries.

**Re-open `portaudio.sln` and batch-clean then batch-build all configurations.**

All six targets should build with no errors, and only harmless warnings.

Note that in the ASIO SDK `asiolist.cpp` file, there are two `"conversion from 'size_t' to 'DWORD', possible loss of data"` warnings. These are caused by `strlen` being used as an argument to `CharLowerBuffer` at lines 24 and 31. In practice, the truncation should (ha!) never be an issue since the first comparison is for a CLSID and the second is for a registry key name.

