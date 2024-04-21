# ffmpag

## ffmpeg 简介

ffmpeg是一套跨平台的，用于音视频录制、转换、流化等操作的完善的解决方案，它是业界最负盛名的开源音视频框架之一。许多软件都是基于ffmpeg开发的，如格式工厂、各种xx影音等。

ffmpeg 是一套开源库以及命令行工具的集合，用户既可以使用命令行直接对音视频进行操作（CLI），也可以调用其开源库进行个性化的功能开发（SDK）。

如果要在自己的程序中使用 ffmpeg ，那么使用它的 SDK 是最好的选择。当前 ffmpeg 包含以下几个库：

* libavcodec : 编/解码
* libavfilter : 帧级操作（如添加滤镜）
* libavformat : 文件 I/O 和 封装/解封装
* libavdevice : 设备视频文件的封装/解封装
* libavutil : 通用工具集合
* libswresample : 音频重采样、格式处理、混音等
* libpostproc : 预处理
* libswscale : 色彩处理和缩放
