title: "编译可供Android使用的FFmpeg库"
date: 2015-05-06 14:23:42
tags:
categories:
- Android
---

FFmpeg是一套强大的音、视频处理工具。常用来对音频、视频进行合并或分离、拼接、剪裁，音频视频的转码等，还有非常强大的视频采集、视频抓图、给视频加水印等功能。主要组件：
- libavformat：用于各种音视频封装格式的生成和解析，包括获取解码所需信息以生成解码上下文结构
和读取音视频帧等功能；
- libavcodec：用于各种类型声音/图像编解码；
- libavutil：包含一些公共的工具函数；
- libswscale：用于视频场景比例缩放、色彩映射转换；
- libpostproc：用于后期效果处理；
- ffmpeg：该项目提供的一个工具，可用于格式转换、解码或电视卡即时编码等；
- ffsever：一个 HTTP 多媒体即时广播串流服务器；
- ffplay：是一个简单的播放器，使用ffmpeg 库解析和解码，通过SDL显示；

## 编译FFmpeg

+ 下载[ffmpeg]()

本测试使用的版本为2.6.1， 使用其它版本时请灵活变通

+ 编译成互相独立的动态库

ffmpeg的每一个组件单独编译成.so库，通过android的jni技术，使用c调用这些.so库中的方法，
实现我们想要的功能。

``` sh
#!/bin/sh
# NDK的路径，根据自己的安装位置进行设置
NDK=/Users/uniflor/Apps/android-ndk
PLATFORM=$NDK/platforms/android-14/arch-arm
# 工具链的路径，根据编译的平台不同而不同
TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64

CPU=arm
PREFIX=$(pwd)/android/$CPU
ADDI_CFLAGS="-marm"
ADDITIONAL_CONFIGURE_FLAG=

./configure \
    --enable-shared \
    --disable-static \
    --disable-doc \
    --disable-ffplay \
    --disable-ffprobe \
    --disable-ffserver \
    --disable-symver \
    --disable-avdevice \
    --disable-protocols \
    --enable-protocol=file \
    --disable-doc \
    --enable-cross-compile \
    --prefix=$PREFIX \
    --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
    --target-os=linux \
    --arch=arm \
    --sysroot=$PLATFORM \
    --extra-cflags="-Os -fpic $ADDI_CFLAGS" \
    --extra-ldflags="$ADDI_LDFLAGS"
```

等待几分钟，命令运行结束后会在ffmpeg目录下生成一个anroid/arm的目录，
目录中会有提取的头文件和动态库。
生成以下动态库如下：
- libavcodec.so
- libavdevice.so
- libavfilter.so
- libavformat.so
- libavutil.so
- libswresample.so
- libswscale.so

每个库的作用，请自行百度或google, 生的动态库要进行精简或扩展功能的请参考ffmpeg官方文档来
调整上面的configure的参数。

+ 编译libffmpeg.so

如果感觉生成的库太多，调用时一一加载麻烦，可以把这些库合并成一个库。

``` sh
#!/bin/sh
# NDK的路径，根据自己的安装位置进行设置
NDK=/Users/uniflor/Apps/android-ndk
PLATFORM=$NDK/platforms/android-14/arch-arm
# 工具链的路径，根据编译的平台不同而不同
TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64

CPU=arm
PREFIX=$(pwd)/android/$CPU
ADDI_CFLAGS="-marm"
ADDITIONAL_CONFIGURE_FLAG=

./configure \
    --disable-doc \
    --disable-ffmpeg \
    --disable-ffplay \
    --disable-ffprobe \
    --disable-ffserver \
    --disable-symver \
    --disable-doc \
    --enable-cross-compile \
    --prefix=$PREFIX \
    --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
    --target-os=linux \
    --arch=arm \
    --sysroot=$PLATFORM \
    --extra-cflags="-Os -fpic $ADDI_CFLAGS" \
    --extra-ldflags="$ADDI_LDFLAGS" \

make clean
make
make install

# 合并生成的静态库

$TOOLCHAIN/bin/arm-linux-androideabi-ld \
    -rpath-link=$PLATFORM/usr/lib \
    -L$PLATFORM/usr/lib \
    -soname libffmpeg.so \
    -shared -nostdlib  \
    -Bsymbolic \
    --whole-archive --no-undefined \
    -o $PREFIX/libffmpeg.so \
    libavcodec/libavcodec.a \
    libavfilter/libavfilter.a \
    libswresample/libswresample.a \
    libavformat/libavformat.a \
    libavutil/libavutil.a \
    libswscale/libswscale.a \
    -lc -lm -lz -ldl -llog \
    $TOOLCHAIN/lib/gcc/arm-linux-androideabi/4.9/libgcc.a
```

等待几分钟，命令运行结束后会在ffmpeg目录下生成一个anroid/arm的目录, 目录中会有提取的头文件和生成的libffmpeg.so库。


## 以命令行的形式来使用ffmpeg

在编译ffmpeg时，使用了参数 --disable-ffmpeg， 是不会生成ffmpeg工具的，不过生成了，
在android应用下也无法直接使用。但我们可以使用jni技术，间接调用ffmpeg的命令行方式。

- 为android项目创建jni文件夹

    创建jni文件夹，添加文件Android.mk和Application.mk

- 添加Application.mk文件

    内容如下：

``` makefile
APP_ABI := armeabi
APP_ABI += armeabi-v7a
APP_PLATFORM := android-14
```

- 复制ffmpeg文件

复制上面android文件夹下的头文件文件夹include和ffmpeg.h, ffmpeg.c, ffmpeg_opt.c, ffmpeg_filter.c，
cmdutils.c, cmdutils.h, cmdutils_common_opts.h, config.h, libffmpeg.so到jni目录下

- 修改ffmpeg.c和ffmpeg.h

找到ffmpeg.c，把int man(int argc, char **argv)改名为
int run(int argc, char **argv)

找到ffmpeg.h, 在文件末尾添加函数申明: int run(int argc, char **argv);

- 修改cmdutils.c

找到cmdutils.c中的exit_program函数，注释到 exit(ret), 添加 return ret；
并修改函数的返回类型为int, 找到cmdutils.h中exit_program的申明，也把返回类型修改为int。

- 生成接口文件com.example.ffmpeg.FFmpegKit.java

``` java
package com.example.ffmpeg;

import java.util.ArrayList;

public class FFmpegKit {
    
    private ArrayList<String> commands;
    
    
    public FFmpegKit() {
        this.commands = new ArrayList<String>();
        this.commands.add("ffmpeg");
    }

    public native static int run(String[] commands);
}
```

- 生成头文件com_example_ffmpeg_FFmpegKit.h

使用javah生成

- 添加com_example_ffmpeg_FFmpegKit.c

``` c
#include "com_example_ffmpeg_FFmpegKit.h"
#include "ffmpeg.h"
#include <string.h>


/*
 * Class:     com_example_ffmpeg_FFmpegKit
 * Method:    run
 * Signature: (Ljava/lang/String;)I
 */
JNIEXPORT jint JNICALL Java_com_example_ffmpeg_FFmpegKit_run(JNIEnv *env,
        jclass obj, jobjectArray commands) {

    int argc = (*env)->GetArrayLength(env, commands);
    char *argv[argc];

    __android_log_print(ANDROID_LOG_ERROR,"Kit","argc %d\n", argc);
    int i;
    for (i = 0; i < argc; i++) {
        jstring js = (jstring) (*env)->GetObjectArrayElement(env, commands, i);
        argv[i] = (char*) (*env)->GetStringUTFChars(env, js, 0);
        __android_log_print(ANDROID_LOG_ERROR,"Kit","argv %s\n", argv[i]);
    }
    return run(argc, argv);
}
```

Android.mk文件内空为

``` makefile
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE := ffmpeg
LOCAL_SRC_FILES := libffmpeg.so
include $(PREBUILT_SHARED_LIBRARY)

include $(CLEAR_VARS)
LOCAL_MODULE := ffmpeginvoke
LOCAL_SRC_FILES := com_example_ffmpeg_FFmpegKit.c ffmpeg.c ffmpeg_opt.c cmdutils.c ffmpeg_filter.c
LOCAL_C_INCLUDES := $(LOCAL_PATH)
LOCAL_LDLIBS := -llog -lz -ldl
LOCAL_SHARED_LIBRARIES := ffmpeg

include $(BUILD_SHARED_LIBRARY)
```

- 编译接口


- 测试接口

``` java
public class MainActivity extends Activity {

    static {
        System.loadLibrary("ffmpeg");
        System.loadLibrary("ffmpeginvoke");
    }
    
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        String base = Environment.getExternalStorageDirectory().getPath();
        Log.e("PATH", base);
        String[] commands = new String[9];
        commands[0] = "ffmpeg";
        commands[1] = "-i";
        commands[2] = base + "/input.mp4";
        commands[3] = "-i";
        commands[4] = base + "/input.mp3";
        commands[5] = "-strict";
        commands[6] = "-2";
        commands[7] = "-y";
        commands[8] = base + "/merge.mp4";
        int result = FFmpegKit.run(commands);
        Log.e("RESULT", result + "**********************");
    }
}
```
