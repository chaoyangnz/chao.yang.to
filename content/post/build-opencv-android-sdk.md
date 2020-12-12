---
categories:
- IoT
date: 2017-11-03T11:36:00Z
tags:
- IoT
- CV
title: Build OpenCV 3.3 Android SDK on Mac OSX
toc: true
---

By default, the official OpenCV Android SDK doesn't contain the contrib libraries, like Aruco.

# prerequisite
- NDK r10e

https://dl.google.com/android/repository/android-ndk-r10e-darwin-x86_64.zip

- Android SDK 

Note: please degrade the Android SDK tools version, 25.2.4 is fine for me
http://dl-ssl.google.com/android/repository/tools_r25.2.5-macosx.zip
Go to `~/Library/Android/sdk`, rename `tools` to `tools.bak`, then unzip the `tools_rxxx.zip` to `sdk/tools` directory.
Why? because otherwise, `android` command has been deprecated)

- opencv 3.3.1

`git clone https://github.com/opencv/opencv.git`

- opencv_contrib

`git clone https://github.com/opencv/opencv_contrib.git`

- CMake

`brew install cmake ninja ant`

# How to do it

Assume the following is my unziped directories in my home.
```
$ ls
opencv
opencv_contrib
android-ndk-r10e
```

Then run these commands:
```bash
$ cd ~/opencv/ && mkdir build
$ cd ~/opencv/platforms/android
$ ./build_sdk.py --ndk_path ~/android-ndk-r10e --sdk_path ~/Library/Android/sdk --extra_modules_path ~/opencv_contrib/modules ~/opencv/build ~/opencv
```

Go to `~/opencv/build/sdk`, you can see all are there.

That's it. Bingo!





