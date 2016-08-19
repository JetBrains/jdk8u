#How JetBrains Runtime is organised
##Workspaces

[github.com/JetBrains/jdk8u](https://github.com/JetBrains/jdk8u/)  
[github.com/JetBrains/jdk8u_corba](https://github.com/JetBrains/jdk8u_corba/)  
[github.com/JetBrains/jdk8u_hotspot](https://github.com/JetBrains/jdk8u_hotspot/)  
[github.com/JetBrains/jdk8u_jaxp](https://github.com/JetBrains/jdk8u_jaxp/)  
[github.com/JetBrains/jdk8u_jaxws](https://github.com/JetBrains/jdk8u_jaxws/)  
[github.com/JetBrains/jdk8u_jdk](https://github.com/JetBrains/jdk8u_jdk/)  
[github.com/JetBrains/jdk8u_langtools](https://github.com/JetBrains/jdk8u_langtools/)  
[github.com/JetBrains/jdk8u_nashorn](https://github.com/JetBrains/jdk8u_nashorn/)  
    
Usually, we do not modify any of the repositories except [jdk](https://github.com/JetBrains/jdk8u_jdk/) module. Other repositories are needed for build purposes.
##Getting sources
__OSX, Linux:__
```
git config --global core.autocrlf true
git clone git@github.com:JetBrains/jdk8u.git
cd jdk8u
./getModules.sh
```

__Windows:__
```
git config --global core.autocrlf false
git clone git@github.com:JetBrains/jdk8u.git
cd jdk8u
getModules.bat
```

#Configure Local Build Environment
##Linux
```
sudo apt-get install git zip bzip2 unzip tar curl
sudo apt-get install ccache make gcc g++ ca-certificates ca-certificates-java
sudo apt-get install libxext-dev libxrender-dev libxtst-dev libxt-dev
sudo apt-get install libasound2-dev libcups2-dev libfreetype6-dev
git clone git@github.com:JetBrains/jdk8u.git
cd jdk8u
./getModules.sh
download jdk8 from Oracle into /home/user/jdk1.8.0_102
export JAVA_HOME=/home/user/jdk1.8.0_102
sh ./configure
make
```

##Windows

Install:

* Cygwin x64 (http://www.cygwin.com/)
  Required packages (binutils, cpio, diffutils, file, gawk, gcc-core, m4, unzip, zip)
* Windows SDK 7.1 offline installer [GRMSDKX_EN_DVD.iso](https://download.microsoft.com/download/F/1/0/F10113F5-B750-4969-A255-274341AC6BCE/GRMSDKX_EN_DVD.iso)
Run Setup\SDKSetup 
* Visual Studio Express 2010 offline installer [VS2010Express1.iso](http://download.microsoft.com/download/1/E/5/1E5F1C0A-0D5B-426A-A603-1798B951DDAE/VS2010Express1.iso)
* [DirectX 9.0 SDK](http://www.microsoft.com/en-us/download/details.aspx?id=6812)
* [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

Build Freetype:
* Download [sources](https://www.freetype.org/download.html)
* Execute the following script in builds\windows\vc2010
```
(echo ^<?xml version="1.0" encoding="utf-8"?^>
echo ^<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003"^>
echo ^<PropertyGroup Label="Globals"^>
echo ^<TargetName^>freetype^</TargetName^>
echo ^<UserIncludeDirectories^>.^</UserIncludeDirectories^>
echo ^</PropertyGroup^>
echo ^<ItemDefinitionGroup^>
echo ^<ClCompile^>
echo ^<ForcedIncludeFiles^>jb_custom.h^</ForcedIncludeFiles^>
echo ^</ClCompile^>
echo ^</ItemDefinitionGroup^>
echo ^</Project^>) > freetype.user.props
(echo #ifndef __JB_CUSTOM_H__
echo #define __JB_CUSTOM_H__
echo #define FT_EXPORT^(x^) __declspec^(dllexport^) x
echo #define FT_BASE^(x^) __declspec^(dllexport^) x
echo #endif) > jb_custom.h
```
* Build with VC Express for **x64** or **win32** target
* Put freetype.dll, freetype.lib, freetype.exp in lib folder at the same level with include:
```
freetype
  include
  lib
```

Configure and run make  in cygwin shell 
* __32 bit__ (use --with-msvcr-dll=/cygdrive/c/windows/SysWOW64/msvcr100.dll on 64 bit windows)
```    
  cd /cygdrive/c/jdk8/
 ./configure  --with-target-bits=32 --with-freetype=/cygdrive/freetype/  
  make images
```
* __64 bit__
```    
  cd /cygdrive/c/jdk8/
 ./configure  --with-target-bits=64 --with-freetype=/cygdrive/freetype/  
  make images
```
