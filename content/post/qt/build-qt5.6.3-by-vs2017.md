---
title: "Build Qt5.6.3 by VS2017"
date: 2023-07-14T16:38:38+08:00
tags: [qt]
---

::https://www.lmlphp.com/user/57824/article/item/909827/
::https://www.lmlphp.com/user/57824/article/item/1240941/
::https://codeleading.com/article/20444650630/
::https://www.cnblogs.com/findumars/p/6021518.html
::https://blog.csdn.net/qing666888/article/details/85062214
::https://doc.qt.io/archives/qt-5.6/configure-options.html

::https://s3.jcloud.sjtu.edu.cn/899a892efef34b1b944a19981040f55b-oss01/github-release/oneclick/rubyinstaller2/releases/download/RubyInstaller-3.2.2-1/mirror_clone_list.html
::https://download.qt.io/official_releases/jom/
::https://www.openssl.org/source/old/1.0.2/openssl-1.0.2u.tar.gz
::https://zhuanlan.zhihu.com/p/586085846?utm_id=0
::https://www.nasm.us/pub/nasm/releasebuilds/2.14.02/win32/
::mysql
::https://blog.csdn.net/weixin_43042683/article/details/106773118

```
set MSVC_PATH=C:\Program Files\Microsoft Visual Studio\2017\Community
set QMAKESPEC=win32-msvc2017
set QT5_SRC_PATH=C:\Qt\qt-everywhere-opensource-src-5.6.3
set QT5_INSTALL_PATH=C:\Qt\-static-vs2017
set PERL_PATH=C:\Strawberry\perl
set PYTHON_PATH=C:\Python27
set RUBY_PATH=C:\Ruby32
set JOM_PATH=C:\Qt\jom_1_1_3
::set OPENSSL_SRC_PATH=C:\Qt\openssl-1.0.2u\src
set OPENSSL_PATH=C:\Qt\openssl-1.0.2u

set PATH=%QT5_SRC_PATH%\qtbase\bin;%QT5_SRC_PATH%\qtbase\lib;%QT5_SRC_PATH%\gnuwin32\bin;%PATH%
set PATH=%PERL_PATH%\bin;%PYTHON_PATH%;%PYTHON_PATH%\Scripts;%RUBY_PATH%\bin;%JOM_PATH%;%PATH%
set LIB=%OPENSSL_PATH%\lib;%LIB%
set INCLUDE=%OPENSSL_PATH%\include;%INCLUDE%
set PATH=%OPENSSL_PATH%\bin;%PATH%
call "%MSVC_PATH%\VC\Auxiliary\Build\vcvarsall.bat" x86

::shadow-building
mkdir qt-build
cd qt-build

call %QT5_SRC_PATH%\configure ^
-prefix %QT5_INSTALL_PATH% ^
-debug-and-release ^
-force-debug-info ^
-confirm-license ^
-opensource ^
-static ^
-nomake examples ^
-nomake tests ^
-skip qtandroidextras ^
-skip qtconnectivity ^
-skip qtmacextras ^
-skip qtsensors ^
-no-compile-examples ^
-qt-sql-sqlite ^
-qt-sql-odbc ^
-plugin-sql-sqlite ^
-plugin-sql-odbc ^
-opengl desktop ^
-platform win32-msvc2017 ^
-target xp ^
-qt-zlib ^
-qt-pcre ^
-qt-libpng ^
-qt-libjpeg ^
-ssl ^
-openssl-linked ^
-I "%OPENSSL_PATH%\include" ^
-L "%OPENSSL_PATH%\lib" ^
-L "C:\Program Files\Microsoft SDKs\Windows\v7.1A\Lib" ^
OPENSSL_LIBS="-lssleay32 -llibeay32 -lUser32 -lAdvapi32 -lGdi32" ^
-no-directwrite ^
-mp


jom /J 16
jom install

xcopy /y /k "%OPENSSL_PATH%\lib\libeay32.dll" "%QT5_INSTALL_PATH%\lib\"
xcopy /y /k "%OPENSSL_PATH%\lib\ssleay32.dll" "%QT5_INSTALL_PATH%\lib\"

pause
```
