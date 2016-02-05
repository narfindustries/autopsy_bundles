# How to Setup an Autopsy Dev box to build and run 64bit Autopsy (including as JA locale) #

## Create VM ##
- OS: Win7 Pro
- VM DISK: 50 GB
- RAM: 6+ GB
- CPU: 2+

Create dir on host that is mounted/shared r/w by the VM, you'll need it later. 
- This share should have around 8-10GB of space.

## Install Visual Studio 2013 ##
- Visual Studio 2010 Express works too, since the source projects are all pre-configured to use 2010, and it doesn't require the project conversion step when building the build targets.
- Note: you just need the C/C++ components, no need for OS dev or MS SQL stuff.

## Install Windows SDK 7.1 ##
SDK 7.1 comes with VS 2010, but not any newer VS versions. So, if you are using a more recent Visual Studio version, then you have to also install Windows SDK 7.1.
- Download and install SDK 7.1: http://msdn.microsoft.com/en-us/windowsserver/bb980924.aspx
- Download and install SDK 7.1 update: https://www.microsoft.com/en-us/download/details.aspx?id=4422
- Append the SDK 7.1 bins location to the PATH:
	- `C:\Program Files (x86)\Microsoft Visual Studio 10.0\Common7\IDE` 

## Install Chocolatey and some terminal tools ##
- cinst git ant Cygwin cyg-get python3 python2
	- ant MUST be ver. >= 1.7.1
	- get cygwin64 (64bit)

## Install Oracle Java 8 JDK 64bit ##
You can do this manually by getting it from Oracle's website, or use cinst to get it.
- cinst jdk8
- Edit windows Environment variables to set JAVA_HOME and JDK_HOME to your JDK directory

## Setup TheSleuthKit Dependencies ##

Download zlib source from http://www.zlib.net and unzip it.
- Rename the dir to zlib.
- Do not need to build it.

Download bzip2 source from http://www.bzip2.org and unzip it next to zlib.
- Rename the dir to bzip2.
- Do not need to build it.

Download libewf_64bit and place it next to bzip2 and zlib
- git clone https://github.com/sleuthkit/libewf_64bit

Build libewf_64bit
- Open with Visual Studio (msvcpp/libewf.sln)
	- When you open it, it may ask you to convert the project to Visual Studio 2013, say YES. Else it won't work.
- Set to x64 and Release in Configuration Manager
- Build libewf_dll
- Set Windows Environment Varaible LIBEWF_HOME to the libewf_64bit dir.

Download libvhdi_64bit and place it next to libewf_64bit
- https://github.com/sleuthkit/libvhdi_64bit

Build libvhdi_64bit
- Open with Visual Studio (msvcpp/libvhdi.sln)
- When you open it, it will ask you to convert the project to VS 2013, SAY NO!!!!
- Set to x64 and Release in Configuration Manager
- Build libvhdi
- Set Windows Environment Variable LIBVHDI_HOME to the libvhdi_64bit dir.

Download lbvmdk_64bit and place it next to the others
- https://github.com/sleuthkit/libvmdk_64bit

Build libvmdk_64bit
- Open with Visual Studio (libvmdk/msvcpp/libvmdk.sln)
- When you open it, it will ask you to convert the project to VS 2013, SAY NO!!!!
- Set to x64 and Release in Configuration Manager
- Set WINVER
	- in libvmdk/common/config_winapi.h:26, copy the "#define WINVER" line and paste it below the comment block (uncomment it)
- Build libvmdk
- Set Windows Environment Variable LIBVMDK_HOME to the libvmdk_64bit/libvmdk dir.

Download and install PostgreSQL 9.4 or above. 
- You can either download the full installer or just the ZIP file. The official releases are from: http://www.postgresql.org/download/
- Need 64 bit version to create 64 bit TSK and Autopsy
- The PostgreSQL server/service does not need to be running on your host unless you plan to use it in Autopsy, so go ahead and stop it and prevent it from starting at boot if you want.

Set Windowns Environment Variable
- set POSTGRESQL_HOME_64=C:\Program Files\PostgreSQL\9.4

Logout/Login to set all new ENV vars which are now needed to build TSK


## Setup TheSleuthKit ##

Download TSK
- git clone git://github.com/sleuthkit/sleuthkit.git
- switch to develop branch!!

Build TSK
- Open with Visual Studio (win32/tsk-win.sln)
	- When you open it, it may ask you to convert it to Visual Studio 2013, say YES. (Note: it should also work if you say NO provided the vs projects are set to use the VS 2010 libs (win sdk7.1))
- Set to x64 and Release_PostgreSQL in Configuration Manager
- Build the libtsk_jni solution

Build TSK java bindings
- in a terminal, go to sleuthkit/bindings/java
- type 'ant dist-PostgreSQL' to build it

Set Windows Environment Variable
- set TKS_HOME to the sleuthkit directory

Logout/Login again.

## Setup Autopsy ##

Download source (use develop branch from Autopsy site)
- git clone  https://github.com/sleuthkit/autopsy.git
- switch to develop branch!!

Build Autopsy
- Open terminal at root of autopsy dir
- run "ant" to build it
- run "ant run" to run it

- alternatively, do  "ant run -Drun.args.extra='-J-Duser.language=ja' " to run it with the JA  (Note: this requires that the JA language pack is installed if doing this on an EN VM)

## Using Autopsy ##
Download test images and run an ingest, so you have something to work with.
- Open terminal at root of autopsy dir
- run "ant test-download-imgs"
- this will get 2 images at 1.5GB each.
- The images will be saved to autopsy/test/input/
- (optional) Move these images to that mounted share you created, if you don't want these inside the VM.

In order to click around the UI, you need a case that you can open. If you don't have one already created, run an ingest (it takes a few hours).
- Run Autopsy
- Create a new case and set your case directory (optionally, to be on the mounted share e:/share/testcase)
- Open "nps-2008-jean.E01" image that you just downloaded.
- Select all modules and run an ingest, this will take a while.

Once the ingest is completed, you can click around the UI and run reports.
If you close Autopsy, you can get back in by running 'ant run' and opening the existing Case and then you can browse around in the UI.

