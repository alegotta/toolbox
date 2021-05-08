# Multimedia

Dependency: `ffmpeg`

## Basic Syntax

```bash
ffmpeg [<input_options> -i <input_device>]+ [<output_options> <output_device>]*
```

To get information about a resource, just specify the input URL without the output.

### Devices

Devices can be **files**, **network resources** or **physical devices**. Normally, it is sufficient to specify the URL and ffmpeg will automatically use the most appropriate driver. Sometimes, this needs to be **enforced** by using the `-f` flag:

|Virtual device (`-f`)|Description|Physical Device (`-i`)|
|:--:|:--:|:--:|
|`x11grab`|**User screen** (on X.Org)|`$DISPLAY`|
|`v4l2`|**Webcam**|`/dev/video[0-9]`|
|`pulse`|**Audio**|`default` or as read from `pactl list short sources`|

For example, to record video and audio from the webcam:

```bash
ffmpeg -f v4l2 -video_size 1920x1080 -i /dev/video0 -f pulse -i default out.mp4
```

To record just audio *(see below for specific options)*

```bash
ffmpeg  -f pulse -i default -ac 2 -ar 48000 -compression_level 8 out.flac
```

## Streams

There are four types of streams:

- `v`: **video**
- `a`: **audio**
- `s`: **subtitles**
- `d`: **data stream**

### Stream Selection

Streams may be chosen by using `-map [-]<input_file_index>[:<stream_type>][:<stream_index>]`. Note that the minus symbol specifies exclusion; the index count starts from 0.

Start by previewing the stream list with `ffmpeg -i <input>`, then operate as needed.

#### Default Behaviour

A video file may have multiple streams of the same type, e.g. two audio tracks. If not selecting a particular index, ffmpeg will pick ONE stream per type, using the following rules:

- for video, the stream with the **highest resolution**;
- for audio, the stream with the **most channels**;
- for subtitles, the **first** stream.

To copy ALL streams, use `-map 0`. To remove all streams of a particular type, use `-<type>n`.

#### Examples

**Split** input into two files, one with just audio (single track) and one with just video (all video streams):

```bash
ffmpeg -i <input> -map 0:1 audio.opus -map 0:v video.mkv
```

**Merge** audio and video tracks:

```bash
ffmpeg -i video.mkv -i audio.opus -map 0:v -map 1:a <output>
```

Output just stream 0 and 2:

```bash
ffmpeg -i <input> -map 0:0 -map 0:2 <output>
```

Output ALL streams but the second and the subtitles:

```bash
ffmpeg -i <input> -map 0 -map -0:2 -sn <output>
```

Output all video streams, plus all the ones marked with english language (see the [ISO codes](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)):

```bash
ffmpeg -i <input> -map 0:v -map 0:m:language:eng <output>
```

## Containers

A container is responsible of holding a number of different stream inside a file. Common containers are `mkv`, `mp4`, `mov`, `avi`.

To convert from one container to another, just specify the output extension:

```bash
ffmpeg -i input.mp4 output.mkv
```

## Codecs

A codec is responsible of memorising a single stream, using some algorithm to represent video, audio or text as bits. It can be **lossless** (no quality loss, just compression) or **lossy** (with quality loss, the actual result may be tuned via parameters).

They are selected with `-c[:<type>][:<stream>] <codec>`. The ones supported on the local machine can be obtained by typing `ffmpeg -codecs`. The special value `copy` takes the input streams 1:1 from input file, and bypasses transcoding. Note that this is NOT the default.

In general:

- Constant Rate Factor (CRF): best quality, but bigger file size

### Video

#### Global Options

- `-vf scale=<format>,setsar=1:1`: scale to the specified pixel format (e.g. 1920x1080)

- `-aspect[:stream] <aspect_ratio>`: scale to the specified aspect ratio (e.g. 16:9, 4:3)

- `-b[:stream] <bitrate>:` set the bitrate for Constant Bit Rate mode (CBR)

- `-q[:stream] <quality>:` set the quality for Variable Bit Rate mode

#### Valid for H.264 and H.265

- `preset` (compression speed): ultrafast, superfast, veryfast, faster, fast, medium *(default)*, slow, slower, veryslow
- `tune`: film, animation, grain, stillimage, fastdecode, zerolatency
- `crf`: set the quality for constant quality mode
- `qp`: set constant quantization rate control method parameter

TODO: Explore best settings, see [here](https://slhck.info/video/2017/03/01/rate-control.html) and [here](https://write.corbpie.com/ffmpeg-encoding-speed-testing-and-analysis/).

### Audio

#### Global Options

- `-ar[:stream] <frequency>`: set the sample rate

- `-ac[:stream] <channels>`: set the number of channels

- `-b[:stream] <bitrate>:` set the bitrate for Constant Bit Rate mode (CBR)

- `-q[:stream] <quality>:` set the quality for Variable Bit Rate mode

|Codec|FFMPEG library|Type|*Reasonable* Bitrate|Quality|Compression Level|Notes|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|**FLAC**|`flac`|Lossless|-|-|0-12||
|**OPUS**|`libopus`|Lossy|64-128|- *(VBR by default)*|0-10|See [Reccomended Bitrates](https://wiki.xiph.org/Opus_Recommended_Settings#Recommended_Bitrates)|
|**OGG**|`libvorbis`|Lossy|90-256|2-9|-|
|**AAC**|`aac`|Lossy|100-256|0.1-2|-|
|**MP3**|`libmp3lame`|Lossy	|128-320|5-0|0-9|

## Editing

### Alter Duration

- `-ss`: start at particular time unit
- `-sseof`: as before, but count from the end of file (use negative time)
- `-to`: stop at particular time unit
- `-t`: stop after the specified duration (useful e.g. for recordings)
- `-fs`: stop after the file has reached the specified size

Example: output from 5 to 15 seconds:

```ffmpeg
ffmpeg -ss 00:00:05 -t 00:00:10 -i <input> <output>
ffmpeg -ss 00:00:05 -to 00:00:15 -i <input> <output>
```

## Hardware Acceleration

### Decoding

Use `-hwaccel[:stream] <accel>`, with accel equal to none (default), auto, vdpau, vaapi, qsv, dxva2

Supported APIs depend on platform, run `ffmpeg -hwaccels` to get available ones. You may need to install special drivers.

TODO: explore hardware-specific codecs, see [there](https://trac.ffmpeg.org/wiki/HWAccelIntro).

## Execution Options

### Batch Execution

Use `-nostdin` and specify either `-y` (answer yes to queries) or `-n` (answer no and fail).

### Logging

#### Reduce verbosity

Use `-hide_banner`, then specify the logging level with `-loglevel <value>`, with value equal to info, warning, error, ...

#### Log to file

Use the `FFREPORT` environment variable. It *optionally* accepts the file name and/or log level:

```bash
FFREPORT=file=report.log:level=info ffmpeg -i <input> <output>
```

--

#### Sources

- https://ffmpeg.org/ffmpeg.html
- https://trac.ffmpeg.org/
- https://github.com/Matroska-Org/matroska-test-files
