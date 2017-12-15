### 1. 安装ffmpeg  
	brew install ffmpeg
### 2. 安装ffmpeg依赖  
查看安装信息

	brew info ffmpeg

会显示依赖安装情况❌表示未安装

	brew install 需要安装的依赖

### 3. 视频转换
查看视频信息，只有视频编码h.264，音频编码aac的mp4视频可以转m3u8

	ffprobe trailer.mp4(视频文件+后缀 我的源文件就是mp4)

显示如下信息

	ffprobe version 3.4 Copyright (c) 2007-2017 the FFmpeg developers
	built with Apple LLVM version 9.0.0 (clang-900.0.37)
	configuration: --prefix=/usr/local/Cellar/ffmpeg/3.4 --enable-shared --enable-pthreads --enable-version3 --enable-hardcoded-tables --enable-avresample --cc=clang --host-cflags= --host-ldflags= --enable-gpl --enable-libmp3lame --enable-libx264 --enable-libxvid --enable-opencl --enable-videotoolbox --disable-lzma
	libavutil      55. 78.100 / 55. 78.100
	libavcodec     57.107.100 / 57.107.100
	libavformat    57. 83.100 / 57. 83.100
	libavdevice    57. 10.100 / 57. 10.100
	libavfilter     6.107.100 /  6.107.100
	libavresample   3.  7.  0 /  3.  7.  0
	libswscale      4.  8.100 /  4.  8.100
	libswresample   2.  9.100 /  2.  9.100
	libpostproc    54.  7.100 / 54.  7.100
	Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'trailer.mp4':
	Metadata:
	  major_brand     : mp42
	  minor_version   : 0
	  compatible_brands: mp42mp41
	  creation_time   : 2017-11-27T13:16:02.000000Z
	Duration: 00:04:02.47, start: 0.000000, bitrate: 10342 kb/s
	  Stream #0:0(eng): Video: h264 (Main) (avc1 / 0x31637661), yuv420p(tv, bt709), 1920x1080, 10018 kb/s, 25 fps, 25 tbr, 25k tbn, 50 tbc (default)
	  Metadata:
	    creation_time   : 2017-11-27T13:16:02.000000Z
	    handler_name    : Alias Data Handler
	    encoder         : AVC Coding
	Stream #0:1(eng): Audio: aac (LC) (mp4a / 0x6134706D), 48000 Hz, stereo, fltp, 317 kb/s (default)
	  Metadata:
	    creation_time   : 2017-11-27T13:16:02.000000Z
	    handler_name    : Alias Data Handler

视频转mp4

	ffmpeg -i 视频源文件.后缀 -acodec copy -vcodec copy out.mp4

视频如果太大可以压缩

	ffmpeg -i input_file -s 320x240 output_file

分辨率自己看着改

mp4转ts

	ffmpeg -i out.mp4 -c copy -bsf:v h264_mp4toannexb output.ts

视频切片

	ffmpeg -i output.ts -c copy -map 0 -f segment -segment_list playlist.m3u8 -segment_time 5 output%03d.ts

命令里边的5为每段视频5秒，可自行调整，完成之后会生成一个playlist.m3u8和一堆.ts,切片到这里就完成了。

### 4. 页面插入视频

试了两种方式  
一种是video.js + videojs-contrib-hls.js  
这种方式不兼容IE10-  
[video.js](https://github.com/videojs/video.js)  
[videojs-contrib-hls](https://github.com/videojs/videojs-contrib-hls)

	<video id=example-video width=960 height=540 class="video-js vjs-default-skin" controls>
	    <source src="./video/playlist.m3u8" type="application/x-mpegURL">
	</video>
	<script src="video.js"></script>
	<script src="videojs.hls.min.js"></script>
	<script>
	    var player = videojs('example-video');
	    player.play();
	</script>

有点小瑕疵，视频播放时间未垂直居中，这都不重要，自己改一下样式就好了。

另一种方式用ckplayer  
官网：[http://www.ckplayer.com/](http://www.ckplayer.com/)
下载之后解压找到`crossdomain.xml`和`ckplayer`文件夹放到根目录下

	<script type="text/javascript" src="ckplayer/ckplayer.js"></script>
	<div id="video" style="width:600px;height:400px;"></div>
	<script>
	    var videoObject = {
	        container:'#video',
	        variable:'player',
	        autoplay:true, //强制使用ckplay做播放器
	        video:'./video/playlist.m3u8'
	    };
	    var player=new ckplayer(videoObject);
	<script>

视频会带ckplay的水印，但是不影响。
