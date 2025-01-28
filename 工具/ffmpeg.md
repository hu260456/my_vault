#### 基础知识

- 容器/文件
    - 如flv, mp4等
    - （多）视频流
    - （多）音频流
    - （多）字幕
- 数据帧/数据包（Frame/Packet）：
    - Frame对应压缩前的数据
    - Packet对应压缩后的数据
- 编解码器（Codec）：
    - 以帧为单位实现原始数据与压缩数据之间的转换
- 复用（mux）：
    - 把不同的流按照某种容器的规则放入容器
- 解复用（demux）：
    - 把不同的流从某种容器中解析出来

#### 帮助

- ffmpeg
    - 基本信息：`ffmpeg -h`
    - 高级信息：`ffmpeg -h long`
    - 所有信息：`ffmpeg -h full`
- ffplay
    - help：`ffplay -h`
- ffprobe
    - help：`ffprobe -h`

#### ffmpeg

|命令|备注|
|-|-|
|-buildconf|显示编译配置|
|-formats|显示可用复用器与解复用器|
|-muxers|显示可用复用器|
|-demuxers|显示可用解复用器|
|-codecs|显示可用编解码器|
|-encoders|显示可用编码器|
|-decoders|显示可用解码器|
|-bsfs|显示可用比特流filter|
|-protocols|显示可用协议|
|-filters|显示可用过滤器|
|-pix_fmts|显示可用像素格式|
|-layouts|显示标准声道名字|
|-sample_fmts|显示可用的音频采样格式|
|-colors|显示可用的颜色名称|
|-i|设定输入流|
|<输出文件的全名>|输出此文件|
|-f|设定输出格式|
|-ss|设定开始时间|
|-t|设定播放时间|
|-vframes|设定输出视频帧数|
|-aframes|设定输出音频帧数|
|-b / -b:v|设定视频码率|
|-b:a|设定音频码率|
|-r|设定帧速率|
|-ar|设定采样率|
|-ac|设定音频的Channel数|
|-vcodec|设定视频编解码器|
|-acodec|设定音频编解码器|
|-s 640x480|设定图像的宽与高|
|-vn|不处理视频（copy表示同原始数据）|
|-an|不处理音频|
|-vf|视频过滤器|
|-af|音频过滤器|
|-aspect 4:3|设定横纵比4:3等|

- 查看具体分类所支持的格式：
    - `ffmpeg -h muxer=flv`
    - `ffmpeg -h filter=atempo`
    - `ffmpeg -h encoder=libx264`
- 过滤器
-vf "scale=1024:576" 缩放后的宽高为1024:576，其中一个参数为-1表示让ffmpeg自动推断
-vf "transpose=2" 旋转90度
-vf "scale=-1:720,transpose=2" 不同过滤器组合使用
-vf "crop=400:400:100:100" 裁剪区域，四个参数代表w:h:x:y(宽高和坐标)
-vf "crop=iw/3:ih/3" 裁剪区域，宽高为原始视频的1/3

#### ffplay

| 命令行参数                   | 备注                  |
| ----------------------- | ------------------- |
| -volume 50              | 以50%的音量打开视频         |
| -x width                | 以一定宽度显示             |
| -y height               | 以一定高显示              |
| -video_size size        | 指定分辨率               |
| -pixel_format format    | 指定像素格式              |
| -framerate              | 指定帧率                |
| -ac                     | 指定通道（PCM）           |
| -ar                     | 指定采样率（PCM）          |
| -f                      | 指定采样格式（PCM）         |
| -fs                     | 全屏启动                |
| -an                     | 禁用音频                |
| -vn                     | 禁用视频                |
| -sn                     | 禁用字幕                |
| -ss pos                 | 从pos开始播放            |
| -to pos                 | 播放到pos              |
| -t duration             | 设置播放的长度             |
| -bytes                  | 按字节进行定位拖动           |
| -seek_interval interval | 自定义左右键拖动间隔          |
| -nodisp                 | 关闭图形化显示窗口           |
| -noborder               | 无边框窗口               |
| -f fmt                  | 强制使用设置的格式解析         |
| -window_title title     | 设置窗口标题              |
| -loop 2                 | 循环播放2次              |
| -vf filter              | 设置视频滤镜              |
| -af filter              | 设置音频滤镜              |
| -stats                  | 默认选项，显示各种信息         |
| -nostats                | 关闭上述选项              |
| -fast                   | 非标准化规范的多媒体兼容优化      |
| -genpts                 | 生成pts               |
| -sync type              | 同步类型，设置主时钟，默认为audio |
| -ast 3                  | 播放索引为3的音频流          |
| -vst 3                  | 播放索引为3的视频流          |
| -sst 3                  | 播放索引为3的字母流          |
| -autoexit               | 视频播放完退出             |
| -acodec codec           | 强制使用一个音频解码器进行音频解码   |
| -vcodev codec           | 强制使用一个视频解码器进行视频解码   |
| -scodev codev           | 强制使用一个字幕解码器进行字幕解码   |
| -framedrop              | 视频不同步则丢弃视频帧         |
| -noframedrop            | 禁用上选项               |
| -showmode mode          | 设置显示模式              |

显示模式，默认为0，如果视频不存为则为2
0：显示视频；1：显示音频波形；2：显示音频频谱

|应用内快捷键|备注|
|-|-|
|q, ESC|退出播放|
|f|全屏切换|
|p, SPC|暂停|
|m|mute|
|9/0|减小/增大音量|
|a|循环切换音频流（audio）|
|v|循环切换视频流（video）|
|t|循环切换字幕流（subtitle）|
|c|循环切换节目|
|w|循环求换过滤器或显示模式|
|s|逐帧播放（slice）|
|<left>/<right>|向后/向前拖动10s|
|<up>/<down>|向后/向前拖动1min|
|鼠标右键|?|
|鼠标左键双击|全屏切换|

#### ffplay 过滤器

|命令|备注|
|-|-|
|-transpose=arg|视频旋转|
|-vf flag|视频反转(flag: hflip, vflip)|
