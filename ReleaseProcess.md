#summary Ostinato Release Process

This document is not useful for users of Ostinato. This is for use by the development team and acts as a formal documentation of the Release Process and a checklist.

# Release Process #
Verify top of the tree code base compiles and works on all platforms
  * Windows
  * Linux
  * FreeBSD
  * OS X

While the code builds, update the changelog

Create the releasedir
  1. Bump the version number in version.pri and commit-push
  1. Create a directory $(VERSION). Create 2 subdirs inside - 'src' and 'winbin'
  1. cd $(VERSION)/src
  1. Clone the repo as ostinato-$(VERSION) - all subsequent steps will operate on this clone
  1. Modify version.pri so that the revision is hardcoded instead of using 'hg identify -i'; append a '@' at the end of the hg revision hash to identify it as a 'src pkg' revision
  1. Delete the .hg directory and .hgignore file
  1. Create ostinato-src-$(VERSION).tar.gz
  1. Delete the ostinato-$(VERSION) directory
  1. Unarchive the .tar.gz
  1. Build
```
   qmake -config release
   make
   make install
```
  1. Download and unpack the last bin-win32 zip archive into the winbin directory and replace ostinato.exe and drone.exe
  1. Run and check the version/revision in both Ostinato and Drone
  1. Sanity test as required
  1. Empty out ostinato.ini
  1. Create archive ostinato-bin-win32-$(VERSION).zip
  1. Repeat the above build/run/test steps for all platforms viz. Linux, FreeBSD, Mac OS X

Linux Packaging
  1. Create a branch of home:pstavirs:ostinato - use web
  1. osc co home:pstavirs:branches:home:pstavirs:ostinato
  1. osc rm ostinato-src-$(OLD-VERSION).tar.gz
  1. Replace ostinato-src-$(OLD-VERSION).tar.gz with ostinato-src-$(VERSION).tar.gz
  1. osc add ostinato-src-$(VERSION).tar.gz
  1. Make other spec file changes - refer last diff on OBS for the same
  1. Remove any patches previously applied
  1. Commit and repeat cycle till all builds are successful

Updating .spec file
1. Change Version:
2. Change Source0: (.tar.gz)
TBD

Mac OS X Packaging
  1. Compile protobuf as "intel 32 bit only" (commands below unchecked)
```
  ./configure --disable-dependency-tracking
  make CFLAGS="-arch i386" CPPFLAGS="-arch i386"  LDFLAGS="-arch i386"
  sudo make install
```
  1. Compile QT as "intel 32 bit only"
```
  ./configure -arch x86 -cocoa
  make
  sudo make install
```
  1. Modify PATH (to bin directory) to use the above Qt e.g.
```
  export PATH=/usr/local/Trolltech/Qt-4.6.3/bin:$PATH
```
  1. Extract and Build
```
 tar zxvf ostinato-src-$(VER).tar.gz
 cd ostinato-$(VER)
 qmake -spec macx-g++ PREFIX=<path>
 make && make install
```
  1. TODO:mac-deploy-qt
  1. Copy HOWTO-INSTALL.txt into the `<path-given-as-PREFIX>/Ostinato` folder
  1. Create soft link to /Applications in the `<path-given-as-PREFIX>/Ostinato` folder
  1. Create dmg. create-dmg is available from http://www.yoursway.com/free/
```
 ./create-dmg --volname Ostinato --icon-size 96 ostinato.dmg <path-given-as-PREFIX>/Ostinato/
```


Upload and announce the release
  1. On OBS, submit the branch project to the main project and then accept it
  1. Update ChangeLog Wiki
  1. Upload ostinato-src-$(VERSION).tar.gz; "Ostinato $(VERSION) (Source)" Labels:  Type-Source, OpSys-All
  1. Upload ostinato-bin-win32-$(VERSION).zip; "Ostinato $(VERSION) (Win32 Binaries)" Type-Archive, OpSys-Windows
  1. Upload ostinato-bin-osx-i386-$(VERSION).dmg; "Ostinato $(VERSION) (OS X Universal Disk Image)", Type-Archive, OpSys-OSX
  1. Mark older release downloads as Deprecated
  1. Wait for openSUSE\_12.1 packages to be published on OBS, before updating SUSEstudio
  1. On SUSEstudio, resync the home:pstavirs repository
  1. Build the LiveCD/USB and publish it
  1. Update the News Wiki page
  1. Update Project News and Breaking News on the home page
  1. Update Roadmap page - mark release as DONE
  1. Update FAQ page if required
  1. Update User Guide page if required
  1. Tweet!
  1. Post a release announcement on freshmeat.net
  1. Post a release announcement on Ostinato mailing list
  1. Inform Ostinato maintainers
  1. Do a hg pull -u on the wiki repository
  1. Update PAD file - Version, Release Date, Size (Bytes), Download URL, Description (optional)
  1. Commit and push the PAD file into the wiki repository
  1. Verify http://wiki.ostinato.googlecode.com/hg/html/pad.xml
  1. Send update email to softpedia editors
  1. Update qt-apps.org - Version, Win32 binary URL, Description (optional), Changelog
  1. Submit to http://opensourcetesting.org/submit_news.php

Home Page Announcement Template
```
*Breaking News (yyyy-mm-dd): Ostinato version $(ver) released!* [http://ostinato.org/wiki/ChangeLog ChangeLog]
----
*News*: 20 Gbps using Ostinato! [http://bit.ly/hmOAuY Full Story]<br>
```