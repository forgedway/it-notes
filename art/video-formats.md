# Basic information

### Video codec
A video codec is a protocol that defines how to compress and decompress a
video. It has the following characteristics:
  - Available modes (lossy or/and lossless);
  - Compress ratio and quality;
  - Decode performance;
  - HDR support;
  - Device support and license.

**Lossy vs lossless:**
- **Consumer-grade codecs:**  
These codecs use lossy compression mode. They usually even technically
don't support lossless compression.
- **Professional codecs:**  
These codecs mostly use high-quality lossy compressions algorithms so
they could render intermediate files without visual quality loss. Some
professional codecs have lossless compression, but they aren't used much.

**Decoding performance:**  
Performance of decoding is heavily dependent on hardware support:
- **Modern codecs** are very efficient but can struggle without dedicated chips on
older devices.
- **Older codecs** are less efficient but have universal hardware support,
allowing even weak devices to decode video smoothly.

### Video container(format)
A video container is a file format that allows to store and exchange video
files. It might contain the following:
  - Video tracks;
  - Audio tracks;
  - Subtitles;
  - Chapter markers;
  - Attachments (Fonts for subtitles, cover art and so on).

Some video containers, such as `mp4`, are designed to support only a specified
set of video and audio codecs. Other containers, such as `mkv`, are designed
to support any video and audio codecs.

The video and audio tracks are stored as separate streams with their data
chunks interleaved with eachother. It allows the player to read the tracks
simultaneously and synchronize them during playback. Additional metadata, like
cover arts or chapters are stored as single chunks of data. Subtitles,
however, might be stored as a separate stream or as a single chunk of data.

### I, P, B-frames
These are three types of frames in video codecs:
  - `I-frame` (Intra-coded frame):
    - Contains full information to decode.
  - `P-frame` (Predicted frame):
    - Contains only the changes from a previous `I` or `P` frame.
  - `B-frame` (Bidirectional frame):
    - Contains changes based on both previous and subsequent `I` or `P` frames.

| Type | Size | Decode complexity (memory and calculation) |
| - | - | :-: |
| `I-frame` | Heavy | Light |
| `P-frame` | Medium | Medium |
| `B-frame` | Light | Heavy |

### Progressive and interlaced scan
There are 2 ways of displaying and keeping video stream:
- `Progressive scanning`
- `Interlaced scanning`

The `interlaced` way means that each frame of the video has only half lines of
the picture. Either odd or even lines. The frames with odd and even lines are
displayed one after another and the video looks smooth on old TVs. It's not
used much in the digital space, but surely is used in modern broadcasting.

The `progressive` way opposes the interlaced way and displays/keeps frames
with all pixels.

Sometimes you can see `p` and `i` at the end of video denotations, like this:
  - 720i60 means:
    - 1280x720 resolution;
    - interlaced scanning;
    - 60 fields (half-frames) per second.
  - 1080p60 means:
    - 1920x1080 resolution;
    - progressive scanning;
    - 60 fps.

### Animation formats vs video formats
| Parameter | Video formats | Animation formats |
| - | - | - |
| Can contain | Video or audio tracks, subtitles, a cover, chapter markers and so on. | A sequence of frames (with timing data) |
| Available codecs | A set of codecs (e. g. `VP8`/`VP9`/`AV1` for `WebM`) | Usually only one codec (e. g. `VP8` for `WebP`) |
| Optimization | For size (using `I-frames`, `P-frames` and `B-frames`) | For performance (using `I-frames` and `P-frames`) |
| Use case | For full-featured video | For short animation |

### MPEG
`MPEG` (Moving Picture Experts Group) is an alliance of working groups that
sets different standards for such things as file formats, video and audio
codecs. Among others, `MPEG` developed the `MPEG-4`, `MPEG-H`, `MPEG-I` groups
of standards that have a huge impact on video formats overall. All standards
are free to read, but different parts of them have different patent policies
about implementing and distributing.

**Some significant parts:**
| Group | Part | Defines | Royalty-free |
| - | - | - | - |
| MPEG-4 | 2 | Original video compression (`DivX`, `Xvid`) | ✅ (❌ until 2015) |
| MPEG-4 | 3 | `ACC` | ❌ |
| MPEG-4 | 10 | `AVC` (`H.264`) | ⚠️ |
| MPEG-4 | 14 | `MP4` | ✅ |
| MPEG-H | 2 | `HEVC` (`H.265`) | ❌ |
| MPEG-I | 3 | `VVC` (`H.266`) | ❌ |

# Codecs
We'll examine delivery codecs for end users and intermediate codecs for
professionals.

### Delivery codecs
Delivery codecs are codecs used to deliver the final video to end users. Their
goal is to achieve a high compression ratio and good sequential decoding
performance.

#### Old Standards (`DivX` and `Xvid`)
`DivX` is a proprietary video codec that was released in 1999 and `Xvid` is
its opensource alternative from 2001. These are old codecs that have mostly
been replaced by modern codecs, but still might be in use because of their
fast compression and good support on old devices. They are both
implementations of `MPEG-4` part 2 and fully compatible with each other.

#### Proprietary Standards (`H.264`, `H.265`, `H.266`)
These standards are open to read, but not royalty-free to implement and
distribute.

**`H.264`(`AVC`) (2003)**
- ✅ **Efficiency:** Achieves ~50% better compression efficiency than `DivX`
  and `Xvid`.
- ✅ **Support:** Ubiquitous. The most widely supported video codec ever.
- ❌ **Licensing:** Not royalty-free, even for opensource products.
- 💡 **Comment:** The undisputable industry standard.

**`H.265`(`HEVC`) (2013)**
- ✅ **Efficiency:** Achieves ~50% better compression efficiency than `H.264`.
- ⚠️  **Support:** Strong in modern hardware, but not as universal as `H.264`.
- ❌ **Licensing:** It has a messy licensing involving multiple patent entities.
- 💡 **Comment:** The adoption was severely hampered by the licensing.

**`H.266`(`VVC`) (2020)**
- ✅ **Efficiency:** Achieves ~50% better compression efficiency than `H.265`.
- ❌ **Support:** Extremely limited. Further adoption is improbable.
- ❌ **Licensing:** A technically impressive codec but the high fees
  incentivize adoption of `AV1` instead.

#### Open Standards (`VP8`, `VP9`, `AV1`)
These royalty-free codecs mostly compete with `MPEG`, proprietary standards
(`H.264`, `H.265`, `H.266`).

**`VP8` (2008)**
- ⚠️ **Efficiency:** Its compression efficiency is considered inferior to
  `H.264`.
- ✅ **Support:** Every major browser supports it.
- 💡 **Comment:** The first major royalty-free competitive to `H.264`. It was
  made free after Google acquired On2.

**`VP9` (2013)**
- ✅ **Efficiency:** Its efficiency is slightly less than `H.265`, but still
  strongly better than `H.264`.
- ✅ **Support:** It has bettern support than `H.265`, though not ubiquitous
  like `H.264`.
- 💡 **Comment:** A modern, royalty-free workhorse for the web.

**`AV1` (2018)**
- ✅ **Efficiency:** Superior to `H.265` and rivaling with `H.266`.
- ⚠️  **Support:** Already has a good support and growing
- ⚠️  **Encoding:** Has the most computationally expensive encoding among codecs.
- 💡 **Comment:** An impressive codec that poised to become the new standard.


### Intermediate codecs
Their purpose is to keep high quality and be light in decoding random places.
They are used to render intermediate representation for further editing or to
be placed in archives.

#### `ProRes` (2007) and `DNxHR` (2014)
`Apple ProRes` and `Avid DNxHR` are families of high quality lossy video
codecs developed by Apple and Avid respectively. They are two mainstream
standards family for professional editing. `Apple ProRes` was developed and
optimized for Mac(Final Cut Pro) and `Avid DNxHR` for Windows(Avid Media
Composer).

Similarities:
- Compression Ratio: They both have comparable compression efficiency.
- Licensing: They both are proprietary families of codecs and licensing fees
  should be paid for those who implement encoding for commercial purposes.
- Technical:
  - Subsampling: `4:4:4`, `4:2:2` and `4:2:0` modes are supported.
  - Frames: Only `I-frames` are used to encode frames for fast random access
  decoding.
  - Quantization: Some data is lost during quantization but not much so it
  wouldn't visually affect the quality.

#### `CineForm` (2002)
`CineForm` is an open and free (since 2017) codec. Despite being technically
comparable with `ProRes` and `DNxHR`, it hasn't gained such good support in
the industry.

#### `FFV1` (2003)
`FFV1` is a lossless, free and opensource codec, developed by `FFmpeg
project`. It's suitable for video editing and archiving. The only drawback is
it doesn't usually have hardware acceleration.
- ✅ **Quality:** Lossless;
- ✅ **Compression ratio:** Superior to `ProRes`;
- ✅ **Licensing:** Free and opensource;
- ✅ **Software support:** Strong;
- ❌ **Hardware support:** Doesn't have;

# Containers (Formats)
All mainstream containers are open and royalty-free.


### Delivery containers
The purpose of delivery containers is to deliver videos to the end user.

#### `AVI` (1992)
It's an outdated container that has been replaced by other containers. It
might still be in use with `Xvid` codec though. The standard doesn't specify a
subset of mandatory codecs or restrict to one. It has a lot of disadvantages
compared to modern, mainstream containers:
- It has bad support of the new codecs `H.264`, `H.265`, `VP9`, `AV1`.
- It's prone to audio/video desynchronization.
- The original standard had 2GB limit file size.
- It can't store multiple audio tracks, text-based subtitles, menus, chapters,
  metadata, attachments (like covers) and so on.

#### `MP4` (2001)
It's the industry standard for media distribution and streaming. It's the most
supported format among others. It has a suitable internal structure for video
streaming (like on youtube). Its standard has a set of codecs that should be
supported. The set includes codecs such as `H.264`, `H.265`, `MPEG-4 part 2`
(`DivX`, `XVid`), `AV1` (not universal yet) and so on. The small set of
officially specified codecs is beneficial for universal support.

#### `MKV` (Matroska) (2002)
It's a modern, flexible and open-source container that is designed to hold
almost any video codec. It fits to be a delivery container (storage and
streaming), but not efficient to be a container for editing. The big set of
supported codecs might hinder the interoperability among different software
and devices.

#### `WEBM` (2010)
It's a modern, flexible and open-source container that is based on `MKV`. It
was designed to be a format for web. To achieve high adoption among different
browsers and devices it was stripped of many features from `MKV`.

Changes from `MKV`:
- It's stripped of any attachments and menu.
- It restricts subtitle formats to WebVTT format.
- It restricts chapters to simple markers without nested chapters.
- It restricts video codecs to a small set of open and royalty-free:
  `VP8`, `VP9`, `AV1`.


### Intermediate containers
The purpose of intermediate containers is to represent videos for further
editing or archives.

#### `MOV` (1991)
`MOV` is the industry standard format for professional editing. It became that
way because it has a flexible internal structure and it's suitable for editing.

First of all, it has the index that contains pointers to media data and their
timestamps. It means that media data and timestamps are stored separately,
unlike `MKV` or `WEBM`. While `MP4` stores 2 copies of timestamps: with media
data and in index. This allows `MOV` to easily reposition fragments of tracks
on the timeline without shifting data or accessing the media data chunks.

Second of all, the index is flexible. It can be placed anywhere in the file
and even be split into several parts, unlike `MP4` that mostly stores the
index at the start of the file for streaming optimization.

These features allow for in-place editing. The internal flexibility and the
ability to contain professional codecs like `ProRes` and `DNxHR` make it
perfect for video editing.
