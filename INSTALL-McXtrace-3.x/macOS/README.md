# Installation of McXtrace 3.1 on macOS 

## Supported macOS releases
* macOS 11.x-13.x (Big Sur and later) are fully supported python tool set, partially
  supported perl tool set). Supported on both Intel and Apple Silicon,
  via separate installation bundles.

## Steps to perform

* Download the Intel package:
  [McXtrace 3.1 for macOS on Intel](http://download.mcxtrace.org/mcxtrace-3.1/mac/mcxtrace-mac_x86_64/mcxtrace-3.1_x86_64.tgz)
  or M1 package:
  [McXtrace 3.1 for macOS on Apple Silicon / M1 ](http://download.mcxtrace.org/mcxtrace-3.1/mac/mcxtrace-mac_arm64/mcxtrace-3.1_arm64.tgz)
  and unpack it (e.g. double-clicking should work)

* Open the resulting folder

* Drag the McXtrace-3.1.app and the McXtrace-3.1-environment.command to
/Applications

* Run the Check-PY-dependencies.command script to check for / install
  basic compiler support and  Python tool dependencies (right-click and "open"). Please follow
  on-screen instructions. The Silicon / M1 version requires the Arm
  version of Homebrew installed in /opt.

* Optionally run the Check-PL-dependencies.command script to check for
  / install basic compiler support  Perl tool dependencies (right-click and "open"). Please follow
  on-screen instructions.

* Once the app and dependencies have been installed on your harddrive, optionally use the gui to make your McCode
the default mcstas/mcxtrace on your machine:
 * mxgui    (python) - Use "File -> Set as default"
 * Furhter menu points in the same place allows to configure the app to run the perl or python UI

* If you need support for NeXus output, please follow the instructions at 
  https://github.com/McStasMcXtrace/McCode/wiki/Install-the-NeXus-data-format
* In case of issues installing / using this app bundle, please contact mcxtrace-users@mcxtrace.org
