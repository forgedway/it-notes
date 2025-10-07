# ffprobe
`ffprobe` shows information about video or audio files. It can be used for
getting codec names, durations, resolutions or debugging file errors.


By default it shows summary, but can be specified to show info about streams,
container, chapters and so on.

```sh
# Show summary about a file:
> ffprobe -hide_banner ./source.mp4
```

<details> <summary>Flags</summary>

- `-hide_banner` - hide prefix (with the ffmpeg version).
</details>
<details> <summary>Result</summary>

```
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from './source.mp4':
  Metadata:
    major_brand     : isom
    minor_version   : 512
    compatible_brands: isomiso2avc1mp41
    encoder         : Lavf61.7.100
  Duration: 00:00:30.02, start: 0.000000, bitrate: 13064 kb/s
  Stream #0:0[0x1](und): Video: h264 (High 4:4:4 Predictive) (avc1 / 0x31637661),
    yuv420p(tv, bt709, progressive), 750x314 [SAR 1:1 DAR 375:157], 12807 kb/s, 24 fps,
    24 tbr, 12288 tbn (default)
      Metadata:
        handler_name    : VideoHandler
        vendor_id       : [0][0][0][0]
  Stream #0:1[0x2](und): Audio: aac (LC) (mp4a / 0x6134706D), 48000 Hz, stereo, fltp,
    259 kb/s (default)
      Metadata:
        handler_name    : SoundHandler
        vendor_id       : [0][0][0][0]
```
</details>

## Show specific info

These are useful flags to show different kind of information:
- `-show_format` - to show container info (`mp4`, `mkv` and so on).
- `-show_streams` - to show audio and video streams.
- `-show_chapters` - to show chapters.

```
# Information about the container:
> ffprobe -v quiet -show_format ./source.mp4
```
<details> <summary>Flags</summary>

- `-v quiet` - be completely silent.
- `-show_format` - show only the container (`mp4` in the case).
</details>
<details> <summary>Result</summary>

```
[FORMAT]
filename=./source.mp4
nb_streams=2
nb_programs=0
nb_stream_groups=0
format_name=mov,mp4,m4a,3gp,3g2,mj2
format_long_name=QuickTime / MOV
start_time=0.000000
duration=30.016000
size=49018155
bit_rate=13064540
probe_score=100
TAG:major_brand=isom
TAG:minor_version=512
TAG:compatible_brands=isomiso2avc1mp41
TAG:encoder=Lavf61.7.100
[/FORMAT]
```
</details>

---
```sh
# Information about the streams of a file:
> ffprobe -v quiet -show_streams ./source.mp4
```
<details> <summary>Flags</summary>

- `-v quiet` - be completely silent.
- `-show_streams` - show only audio and video streams.
</details>
<details> <summary>Result</summary>

```
[STREAM]
index=0
codec_name=h264
codec_long_name=H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10
profile=High 4:4:4 Predictive
codec_type=video
codec_tag_string=avc1
codec_tag=0x31637661
width=750
height=314
coded_width=750
coded_height=314
closed_captions=0
film_grain=0
has_b_frames=0
sample_aspect_ratio=1:1
display_aspect_ratio=375:157
pix_fmt=yuv420p
level=30
color_range=tv
color_space=bt709
color_transfer=bt709
color_primaries=bt709
chroma_location=left
field_order=progressive
refs=1
is_avc=true
nal_length_size=4
id=0x1
r_frame_rate=24/1
avg_frame_rate=24/1
time_base=1/12288
start_pts=0
start_time=0.000000
duration_ts=368640
duration=30.000000
bit_rate=12807440
max_bit_rate=N/A
bits_per_raw_sample=8
nb_frames=720
nb_read_frames=N/A
nb_read_packets=N/A
extradata_size=50
DISPOSITION:default=1
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
DISPOSITION:non_diegetic=0
DISPOSITION:captions=0
DISPOSITION:descriptions=0
DISPOSITION:metadata=0
DISPOSITION:dependent=0
DISPOSITION:still_image=0
DISPOSITION:multilayer=0
TAG:language=und
TAG:handler_name=VideoHandler
TAG:vendor_id=[0][0][0][0]
[/STREAM]
[STREAM]
index=1
codec_name=aac
codec_long_name=AAC (Advanced Audio Coding)
profile=LC
codec_type=audio
codec_tag_string=mp4a
codec_tag=0x6134706d
sample_fmt=fltp
sample_rate=48000
channels=2
channel_layout=stereo
bits_per_sample=0
initial_padding=0
id=0x2
r_frame_rate=0/0
avg_frame_rate=0/0
time_base=1/48000
start_pts=0
start_time=0.000000
duration_ts=1440768
duration=30.016000
bit_rate=259309
max_bit_rate=N/A
bits_per_raw_sample=N/A
nb_frames=1408
nb_read_frames=N/A
nb_read_packets=N/A
extradata_size=5
DISPOSITION:default=1
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
DISPOSITION:non_diegetic=0
DISPOSITION:captions=0
DISPOSITION:descriptions=0
DISPOSITION:metadata=0
DISPOSITION:dependent=0
DISPOSITION:still_image=0
DISPOSITION:multilayer=0
TAG:language=und
TAG:handler_name=SoundHandler
TAG:vendor_id=[0][0][0][0]
[/STREAM]
```
</details>

---

```
# Information about chapters:
> ffprobe -v quiet -show_chapters ./file.mp4
```
<details> <summary>Flags</summary>

- `-v quiet` - be completely silent.
- `-show_chapters` - show chapters.
</details>
<details> <summary>Result</summary>

```
[CHAPTER]
id=-2497154633967824570
time_base=1/1000000000
start=0
start_time=0.000000
end=958833000000
end_time=958.833000
TAG:title=Chapter 01
[/CHAPTER]
[CHAPTER]
id=-5494031666136408814
time_base=1/1000000000
start=958833000000
start_time=958.833000
end=1789375000000
end_time=1789.375000
TAG:title=Chapter 02
[/CHAPTER]
```
</details>


## Specific formats
When ffmpeg shows different kind of information `-show_format`, `-show_streams`,
`-show_chapters`, it uses custom format `[TAG][/TAG]`. It can be changed with
several flags (aliases):
- `-print_format`, `-of`, `-output_format`

Json as output an format:

```
> ffprobe -v quiet -print_format json -show_format ./source.mp4
```

<details> <summary>Flags</summary>

- `-v quiet` - be completely silent.
- `-print_format json` - change the output format to `json`.
- `-show_format` - show only the container (`mp4` in the case).
</details>
<details> <summary>Result</summary>

```
{
    "format": {
        "filename": "./source.mp4",
        "nb_streams": 2,
        "nb_programs": 0,
        "nb_stream_groups": 0,
        "format_name": "mov,mp4,m4a,3gp,3g2,mj2",
        "format_long_name": "QuickTime / MOV",
        "start_time": "0.000000",
        "duration": "30.016000",
        "size": "49018155",
        "bit_rate": "13064540",
        "probe_score": 100,
        "tags": {
            "major_brand": "isom",
            "minor_version": "512",
            "compatible_brands": "isomiso2avc1mp41",
            "encoder": "Lavf61.7.100"
        }
    }
}
```
</details>

---

`default` as an output format:

```
> ffprobe -v quiet -print_format default -show_format ./source.mp4
```

<details> <summary>Flags</summary>

- `-v quiet` - be completely silent.
- `-print_format default` - use the default output format.
- `-show_format` - show the container (`mp4` in the case).
</details>
<details> <summary>Result</summary>

```
[FORMAT]
filename=./source.mp4
nb_streams=2
nb_programs=0
nb_stream_groups=0
format_name=mov,mp4,m4a,3gp,3g2,mj2
format_long_name=QuickTime / MOV
start_time=0.000000
duration=30.016000
size=49018155
bit_rate=13064540
probe_score=100
TAG:major_brand=isom
TAG:minor_version=512
TAG:compatible_brands=isomiso2avc1mp41
TAG:encoder=Lavf61.7.100
[/FORMAT]
```
</details>

## Select information
There are useful flags to filter information:
- `-select_streams <v|a>` - to show only video or audio streams
- `-show_entries <section_name>` - to show only a specific entries (like
  duration, index or number of frames).

---
```
> ffprobe -v quiet -print_format json -show_streams -select_streams v ./source.mp4
```
<details> <summary>Flags</summary>

- `-v quiet` - be completely silent.
- `-print_format json` - print in `json` format.
- `-show_streams` - show streams.
- `-select_streams v` - show only video streams.
</details>
<details> <summary>Result</summary>

```
{
  "streams": [
    {
      "index": 0,
      "codec_name": "h264",
      "codec_long_name": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
      "profile": "High 4:4:4 Predictive",
      "codec_type": "video",
      "codec_tag_string": "avc1",
      "codec_tag": "0x31637661",
      "width": 750,
      "height": 314,
      "coded_width": 750,
      "coded_height": 314,
      "closed_captions": 0,
      "film_grain": 0,
      "has_b_frames": 0,
      "sample_aspect_ratio": "1:1",
      "display_aspect_ratio": "375:157",
      "pix_fmt": "yuv420p",
      "level": 30,
      "color_range": "tv",
      "color_space": "bt709",
      "color_transfer": "bt709",
      "color_primaries": "bt709",
      "chroma_location": "left",
      "field_order": "progressive",
      "refs": 1,
      "is_avc": "true",
      "nal_length_size": "4",
      "id": "0x1",
      "r_frame_rate": "24/1",
      "avg_frame_rate": "24/1",
      "time_base": "1/12288",
      "start_pts": 0,
      "start_time": "0.000000",
      "duration_ts": 368640,
      "duration": "30.000000",
      "bit_rate": "12807440",
      "bits_per_raw_sample": "8",
      "nb_frames": "720",
      "extradata_size": 50,
      "disposition": {
        "default": 1,
        "dub": 0,
        "original": 0,
        "comment": 0,
        "lyrics": 0,
        "karaoke": 0,
        "forced": 0,
        "hearing_impaired": 0,
        "visual_impaired": 0,
        "clean_effects": 0,
        "attached_pic": 0,
        "timed_thumbnails": 0,
        "non_diegetic": 0,
        "captions": 0,
        "descriptions": 0,
        "metadata": 0,
        "dependent": 0,
        "still_image": 0,
        "multilayer": 0
      },
      "tags": {
        "language": "und",
        "handler_name": "VideoHandler",
        "vendor_id": "[0][0][0][0]"
      }
    }
  ]
}
```
</details>

---
```
> ffprobe -v quiet -print_format default=noprint_wrappers=1 -show_entries stream=index:stream=duration ./source.mp4
```
<details> <summary>Flags</summary>

- `-v quiet` - be completely silent.
- `-print_format default=` - print in `default` format (with options after `=` sign):
  - `noprint_wrappers=1` - don't print `[STREAM]` and `[/STREAM]` tags.
- `-show_entries` - show only specified entries:
    - `stream=index` - show indices in all streams.
    - `stream=duration` - show durations in all streams.
</details>
<details> <summary>Result</summary>

```
index=0
duration=30.000000
index=1
duration=30.016000
```
</details>

---
```
> ffprobe \
  -v quiet \
  -select_streams v \
  -show_entries stream=width,height \
  -print_format csv=print_section=0 \
  ./source.mp4
```
<details> <summary>Flags</summary>

- `-v quiet` - be completely silent.
- `-select_streams v` - select only video streams.
- `-show_entries stream=width,height` - show only width and height sections of streams.
- `-print_format csv=` - print in `csv` format with flags:
  - `print_section=0` - print without section names (width and height in the case).
</details>
<details> <summary>Result</summary>

```
750,314
```
</details>
