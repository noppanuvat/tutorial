# Under Construction
# การแสดงภาพ Video ด้วย Gstreamer
ใช้ข้อมูลจาก Video Player 2 สำหรับอธิบายเป็นหลัก 

#วิธีใช้  gst-launch

* รูปแบบคำสั่ง 

```
gst-launch element1 ! element2 ! ...! element n 
```
`gst-launch` เป็นโปรแกรมที่พร้อมใช้งาน โปรแกรมนี้ใช้ไลบรารี่ GStreamer 
element แทนชื่อตัวกระทำกับข้อมูลเช่นการแปลงข้อมูล การส่งภาพ รับภาพ กรองข้อมูล เรียกรวมว่า element หากติดตั้งปกติจะพบ element ประมาณ 200elements

เครื่องหมาย `!` ใช้เป็นเครื่องหมายคั่นระหว่าง element เพื่อใช้บอกว่า ข้อมูลจาก element ซ้ายเมื่อประมวลเสร็จจะส่งต่อให้ element ขวา และส่งต่อไปเรื่อยๆ หากมีเครื่องหมาย `!` เราเรียกการส่งต่อนี้ว่า pipeline

* คำสั่งเล่นไฟล์ video

```bash
gst-launch --gst-fatal-warnings playbin2 uri=file:///mnt/sdb1/movies1.mp4 video-sink="mfw_v4lsink" audio-sink="alsasink" max-size-buffers="1" max-size-bytes="0" max-size-time="0"
```

หรือ

```bash
gst-launch filesrc location=/mnt/sdb1/movies1.mp4 typefind=true ! aiurdemux name=demux demux. ! queue max-size-buffers=0 max-size-time=0 ! vpudec ! mfw_v4lsink demux. ! queue max-size-buffers=0 max-size-time=0 ! beepdec ! audioconvert ! 'audio/x-raw-int, channels=2' ! alsasink
```

## ประเภทปลั๊กอิน

**GStreamer** เป็นเฟรมเวิร์คด้าน multimedia ที่พัฒนาโดยทีม GNOME เป็นทีมใหญ่ที่พัฒนาซอฟต์แวร์บนระบบปฏบัติการลินุกซ์ หน้าจอ X-windows ชื่อ GNOME มาจากทีมนี้ การมี GStreamer ทำให้การติดต่อข้อมูลด้าน multimedia ทำได้สะดวกขึ้นและสำคัญตรงฟรี ซึ่งก่อนนี้มีไลบรารีอยู่ส่วนหนึ่งมาในรูปแบบ codec ที่ไม่ฟรี ซึ่งในส่วนที่ codec มีลิขสิทธิ์จะจัดให้อยู่ในกลุ่มปลั๊กอินประเภท gst-plugins-ugly จะกล่าวในรายละเอียดในลำดับต่อไป เครื่องมือสำหรับพัฒนาต่อมีในรูปแบบไลบรารี่ที่เขียนด้วย C มีคู่มือสำหรับนักพัฒนาในเว็บ https://gstreamer.freedesktop.org/documentation/ 


|    **Plugin name**   |                                **Description**                               |
|:----------------:|:------------------------------------------------------------------------:|
| gst-plugins-base |ปลั๊กอินพื้นฐาน ใช้งานได้ดี ไม่มีปัญหาด้านลิขสิทธิ์ อีลีเมนท์ที่มีในปลั๊กอินท์ถือว่าสมบูรณ์ มีเอกสารคู่มือ |
| gst-plugins-good |ใช้งานได้ดี มีคุณภาพ ส่วนใช้อยู่ใช้สัญญาอนุญาต LGPL                                  |
| gst-plugins-ugly |ใช้งานได้ดี มีคุณภาพ อาจจะติดปัญหาด้านลิขสิทธิ์                                      |
| gst-plugins-bad  |กลุ่ม อีลีเมนท์ที่อยู่ในขั้นตอนพัฒนา ต้องการการทดสอบ ส่วนมากเป็นปลั๊กอินใหม่ๆ               |

## API
โครงการหลักๆของ GStreamer คือการจัดเตรียมไลบรารี่สำหรับใช้พัฒนาซอฟต์แวร์ ซึ่งได้เตรียมโปรแกรมชุดหนึ่งสำหรับเรียกใช้งานได้ และเป็นเรื่องที่ผมจะพูดถึงในครั้งนี้ ชื่อโปรแกรม 

- gst-launch
- gat-inspect

โปรแกรม gst-launch(ชื่อ gst-launch-0.10 และ gst-launch-1.0 ขึ้นกับเวอร์ชั่น) เป็นโปรแกรมสื่อกลางที่เรียกใช้อีลิเมนท์(elements) ที่ GStreamer ได้เตรียมไว้จำนวนอีลีเมนท์ที่มีขึ้นอยู่กับการคอมไพล์โปรแกรม ซึ่งโปรแกรม gst-inspect มีเพื่อดูว่า gst-launch ใช้อีลีเมนท์ใดได้บ้าง สำหรับเฟิร์มแวร์เวอร์ชั่น 56.00.240B มีดังนี้

```bash
# gst-inspect
app:  appsrc: AppSrc
app:  appsink: AppSink
typefindfunctions: video/x-ms-asf: asf, wm, wma, wmv
typefindfunctions: audio/x-musepack: mpc, mpp, mp+
typefindfunctions: audio/x-au: au, snd
typefindfunctions: video/x-msvideo: avi
typefindfunctions: audio/qcelp: qcp
typefindfunctions: video/x-cdxa: dat
typefindfunctions: video/x-vcd: dat
typefindfunctions: audio/x-imelody: imy, ime, imelody
typefindfunctions: audio/midi: mid, midi
typefindfunctions: audio/riff-midi: mid, midi
typefindfunctions: audio/mobile-xmf: mxmf
typefindfunctions: video/x-fli: flc, fli
typefindfunctions: application/x-id3v2: mp3, mp2, mp1, mpga, ogg, flac, tta
typefindfunctions: application/x-id3v1: mp3, mp2, mp1, mpga, ogg, flac, tta
typefindfunctions: application/x-apetag: mp3, ape, mpc, wv
typefindfunctions: audio/x-ttafile: tta
typefindfunctions: audio/x-mod: 669, amf, dsm, gdm, far, imf, it, med, mod, mtm, okt, sam, s3m, stm, stx, ult, xm
typefindfunctions: audio/mpeg: mp3, mp2, mp1, mpga
typefindfunctions: audio/x-ac3: ac3, eac3
typefindfunctions: audio/x-dts: dts
typefindfunctions: audio/x-gsm: gsm
typefindfunctions: video/mpeg-sys: mpe, mpeg, mpg
typefindfunctions: video/mpegts: ts, mts
typefindfunctions: application/ogg: anx, ogg, ogm
typefindfunctions: video/mpeg-elementary: mpv, mpeg, mpg
typefindfunctions: video/mpeg4: m4v
typefindfunctions: video/x-h263: h263, 263
typefindfunctions: video/x-h264: h264, x264, 264
typefindfunctions: video/x-nuv: nuv
typefindfunctions: audio/x-m4a: m4a
typefindfunctions: application/x-3gp: 3gp
typefindfunctions: video/quicktime: mov
typefindfunctions: image/x-quicktime: qif, qtif, qti
typefindfunctions: image/jp2: jp2
typefindfunctions: video/mj2: mj2
typefindfunctions: text/html: htm, html
typefindfunctions: application/vnd.rn-realmedia: ra, ram, rm, rmvb
typefindfunctions: application/x-pn-realaudio: ra, ram, rm, rmvb
typefindfunctions: application/x-shockwave-flash: swf, swfl
typefindfunctions: video/x-flv: flv
typefindfunctions: text/plain: txt
typefindfunctions: text/uri-list: ram
typefindfunctions: application/x-hls: m3u8
typefindfunctions: application/sdp: sdp
typefindfunctions: application/smil: smil
typefindfunctions: application/xml: xml
typefindfunctions: audio/x-wav: wav
typefindfunctions: audio/x-aiff: aiff, aif, aifc
typefindfunctions: audio/x-svx: iff, svx
typefindfunctions: audio/x-paris: paf
typefindfunctions: audio/x-nist: nist
typefindfunctions: audio/x-voc: voc
typefindfunctions: audio/x-sds: sds
typefindfunctions: audio/x-ircam: sf
typefindfunctions: audio/x-w64: w64
typefindfunctions: audio/x-shorten: shn
typefindfunctions: application/x-ape: ape
typefindfunctions: image/jpeg: jpg, jpe, jpeg
typefindfunctions: image/gif: gif
typefindfunctions: image/png: png
typefindfunctions: image/bmp: bmp
typefindfunctions: image/tiff: tif, tiff
typefindfunctions: image/x-portable-pixmap: pnm, ppm, pgm, pbm
typefindfunctions: video/x-matroska: mkv, mka
typefindfunctions: video/webm: webm
typefindfunctions: application/mxf: mxf
typefindfunctions: video/x-mve: mve
typefindfunctions: video/x-dv: dv, dif
typefindfunctions: audio/x-amr-nb-sh: amr
typefindfunctions: audio/x-amr-wb-sh: amr
typefindfunctions: audio/iLBC-sh: ilbc
typefindfunctions: audio/x-sid: sid
typefindfunctions: image/x-xcf: xcf
typefindfunctions: video/x-mng: mng
typefindfunctions: image/x-jng: jng
typefindfunctions: image/x-xpixmap: xpm
typefindfunctions: image/x-sun-raster: ras
typefindfunctions: application/x-bzip: bz2
typefindfunctions: application/x-gzip: gz
typefindfunctions: application/zip: zip
typefindfunctions: application/x-compress: Z
typefindfunctions: subtitle/x-kate: no extensions
typefindfunctions: audio/x-flac: flac
typefindfunctions: audio/x-vorbis: no extensions
typefindfunctions: video/x-theora: no extensions
typefindfunctions: application/x-ogm-video: no extensions
typefindfunctions: application/x-ogm-audio: no extensions
typefindfunctions: application/x-ogm-text: no extensions
typefindfunctions: audio/x-speex: no extensions
typefindfunctions: audio/x-celt: no extensions
typefindfunctions: application/x-ogg-skeleton: no extensions
typefindfunctions: text/x-cmml: no extensions
typefindfunctions: application/x-executable: no extensions
typefindfunctions: audio/aac: aac, adts, adif, loas
typefindfunctions: audio/x-spc: spc
typefindfunctions: audio/x-wavpack: wv, wvp
typefindfunctions: audio/x-wavpack-correction: wvc
typefindfunctions: application/postscript: ps
typefindfunctions: image/svg+xml: svg
typefindfunctions: application/x-rar: rar
typefindfunctions: application/x-tar: tar
typefindfunctions: application/x-ar: a
typefindfunctions: application/x-ms-dos-executable: dll, exe, ocx, sys, scr, msstyles, cpl
typefindfunctions: video/x-dirac: no extensions
typefindfunctions: multipart/x-mixed-replace: no extensions
typefindfunctions: application/x-mmsh: no extensions
typefindfunctions: video/vivo: viv
typefindfunctions: audio/x-nsf: nsf
typefindfunctions: audio/x-gym: gym
typefindfunctions: audio/x-ay: ay
typefindfunctions: audio/x-gbs: gbs
typefindfunctions: audio/x-vgm: vgm
typefindfunctions: audio/x-sap: sap
typefindfunctions: video/x-ivf: ivf
typefindfunctions: audio/x-kss: kss
typefindfunctions: application/pdf: pdf
typefindfunctions: application/msword: doc
typefindfunctions: application/octet-stream: DS_Store
typefindfunctions: image/vnd.adobe.photoshop: psd
typefindfunctions: application/x-yuv4mpeg: y4m
typefindfunctions: image/x-icon: no extensions
typefindfunctions: image/x-degas: no extensions
ffmpegcolorspace:  ffmpegcolorspace: FFMPEG Colorspace converter
encoding:  encodebin: Encoder Bin
aacdec.imx:  mfw_aacdecoder: aac audio decoder
ipucsc.imx:  mfw_ipucsc: IPU-based video converter
audiopeq.imx:  mfw_audio_pp: audio post equalizer
subparse: subparse_typefind: srt, sub, mpsub, mdvd, smi, txt, dks
subparse:  subparse: Subtitle parser
subparse:  ssaparse: SSA Subtitle Parser
audiorate:  audiorate: Audio rate adjuster
v4lsink.imx:  mfw_v4lsink: v4l2 video sink
alsa:  alsamixer: Alsa mixer
alsa:  alsasrc: Audio source (ALSA)
alsa:  alsasink: Audio sink (ALSA)
videorate:  videorate: Video rate adjuster
coreelements:  capsfilter: CapsFilter
coreelements:  fakesrc: Fake Source
coreelements:  fakesink: Fake Sink
coreelements:  fdsrc: Filedescriptor Source
coreelements:  fdsink: Filedescriptor Sink
coreelements:  filesrc: File Source
coreelements:  funnel: Funnel pipe fitting
coreelements:  identity: Identity
coreelements:  input-selector: Input selector
coreelements:  output-selector: Output selector
coreelements:  queue: Queue
coreelements:  queue2: Queue 2
coreelements:  filesink: File Sink
coreelements:  tee: Tee pipe fitting
coreelements:  typefind: TypeFind
coreelements:  multiqueue: MultiQueue
coreelements:  valve: Valve element
audioresample:  audioresample: Audio resampler
aiur.imx: webm: webm
aiur.imx:  aiurdemux: aiur universal demuxer
playback:  playbin: Player Bin
playback:  playbin2: Player Bin 2
playback:  playsink: Player Sink
playback:  subtitleoverlay: Subtitle Overlay
videotestsrc:  videotestsrc: Video test source
coreindexers:  memindex: A index that stores entries in memory
wavparse:  wavparse: WAV audio demuxer
volume:  volume: Volume
vpu.imx:  vpudec: VPU-based video decoder
vpu.imx:  vpuenc: VPU-based video encoder
decodebin:  decodebin: Decoder Bin
vorbisdec.imx:  mfw_vorbisdecoder: vorbis audio decoder
mp3enc.imx:  mfw_mp3encoder: mp3 audio encoder
uridecodebin:  decodebin2: Decoder Bin
uridecodebin:  uridecodebin: URI Decoder
mp3dec.imx:  mfw_mp3decoder: mp3 audio decoder
audioparsers:  aacparse: AAC audio stream parser
audioparsers:  amrparse: AMR audio stream parser
audioparsers:  ac3parse: AC3 audio stream parser
audioparsers:  dcaparse: DTS Coherent Acoustics audio stream parser
audioparsers:  flacparse: FLAC audio parser
audioparsers:  mpegaudioparse: MPEG1 Audio Parser
amrdec.imx:  mfw_amrdecoder: amr audio decoder
audioconvert:  audioconvert: Audio converter
v4lsrc.imx:  mfw_v4lsrc: v4l2 based camera src
id3demux:  id3demux: ID3 tag demuxer
aacpdec.imx:  mfw_aacplusdecoder: aac plus audio decoder
audiotestsrc:  audiotestsrc: Audio test source
h264parse:  h264parse: H264Parse
isink.imx:  mfw_isink: IPU-based video sink
beep.imx: ac3: ac3
beep.imx: 3ca: ac3
beep.imx:  beepdec: beep audio decoder
beep.imx:  beepdec.vorbis: Vorbis decoder
beep.imx:  beepdec.ac3: AC3 decoder
beep.imx:  beepdec.wma: WMA decoder
beep.imx:  beepdec.mp3: MP3 decoder
beep.imx:  beepdec.aac: AAC LC decoder
staticelements:  bin: Generic bin
staticelements:  pipeline: Pipeline object

Total count: 40 plugins (3 blacklist entries not shown), 198 features
```

จากรายการอีลิเมนส์ด้านบน เทียบกับคำสั่ง gst-launch ที่เรียกใช้เพื่อแสดงผล MP4 จาก 

```
#gst-inspect playbin2

(gst-inspect-0.10:13835): GLib-WARNING **: getpwuid_r(): failed due to unknown user id (0)
Factory Details:
  Long name:	Player Bin 2
  Class:	Generic/Bin/Player
  Description:	Autoplug and play media from an uri
  Author(s):	Wim Taymans <wim.taymans@gmail.com>
  Rank:		none (0)

Plugin Details:
  Name:			playback
  Description:		various playback elements
  Filename:		/usr/lib/gstreamer-0.10/libgstplaybin.so
  Version:		0.10.35
  License:		LGPL
  Source module:	gst-plugins-base
  Source release date:	2011-06-15
  Binary package:	GStreamer Base Plug-ins source release
  Origin URL:		Unknown package origin

GObject
 +----GstObject
       +----GstElement
             +----GstBin
                   +----GstPipeline
                         +----GstPlayBin2

Implemented Interfaces:
  GstChildProxy
  GstStreamVolume

Pad Templates:
  none

Element Flags:
  no flags set

Bin Flags:
  no flags set

Element Implementation:
  Has change_state() function: gst_play_bin_change_state

Clocking Interaction:
  element requires a clock
  element is supposed to provide a clock but returned NULL

Indexing capabilities:
  element can do indexing
Element has no URI handling capabilities.

Pads:
  none

Element Properties:
  name                : The name of the object
                        flags: readable, writable
                        String. Default: null Current: "playbin20"
  async-handling      : The bin will handle Asynchronous state changes
                        flags: readable, writable
                        Boolean. Default: false Current: false
  message-forward     : Forwards all children messages
                        flags: readable, writable
                        Boolean. Default: false Current: false
  delay               : Expected delay needed for elements to spin up to PLAYING in nanoseconds
                        flags: readable, writable
                        Unsigned Integer64. Range: 0 - 18446744073709551615 Default: 0 Current: 0
  auto-flush-bus      : Whether to automatically flush the pipeline's bus when going from READY into NULL state
                        flags: readable, writable
                        Boolean. Default: true Current: true
  uri                 : URI of the media to play
                        flags: readable, writable
                        String. Default: null Current: null
  suburi              : Optional URI of a subtitle
                        flags: readable, writable
                        String. Default: null Current: null
  source              : Source element
                        flags: readable
                        Object of type "GstElement"
  flags               : Flags to control behaviour
                        flags: readable, writable
                        Flags "GstPlayFlags" Default: 0x00000057, "native-video | soft-volume | text | audio | video" Current: 0x00000057, "native-video | soft-volume | text | audio | video"
                           (0x00000001): video            - Render the video stream
                           (0x00000002): audio            - Render the audio stream
                           (0x00000004): text             - Render subtitles
                           (0x00000008): vis              - Render visualisation when no video is present
                           (0x00000010): soft-volume      - Use software volume
                           (0x00000020): native-audio     - Only use native audio formats
                           (0x00000040): native-video     - Only use native video formats
                           (0x00000080): download         - Attempt progressive download buffering
                           (0x00000100): buffering        - Buffer demuxed/parsed data
                           (0x00000200): deinterlace      - Deinterlace video if necessary
  n-video             : Total number of video streams
                        flags: readable
                        Integer. Range: 0 - 2147483647 Default: 0 Current: 0
  current-video       : Currently playing video stream (-1 = auto)
                        flags: readable, writable
                        Integer. Range: -1 - 2147483647 Default: -1 Current: -1
  n-audio             : Total number of audio streams
                        flags: readable
                        Integer. Range: 0 - 2147483647 Default: 0 Current: 0
  current-audio       : Currently playing audio stream (-1 = auto)
                        flags: readable, writable
                        Integer. Range: -1 - 2147483647 Default: -1 Current: -1
  n-text              : Total number of text streams
                        flags: readable
                        Integer. Range: 0 - 2147483647 Default: 0 Current: 0
  current-text        : Currently playing text stream (-1 = auto)
                        flags: readable, writable
                        Integer. Range: -1 - 2147483647 Default: -1 Current: -1
  subtitle-encoding   : Encoding to assume if input subtitles are not in UTF-8 encoding. If not set, the GST_SUBTITLE_ENCODING environment variable will be checked for an encoding to use. If that is not set either, ISO-8859-15 will be assumed.
                        flags: readable, writable
                        String. Default: null Current: null
  audio-sink          : the audio output element to use (NULL = default sink)
                        flags: readable, writable
                        Object of type "GstElement"
  video-sink          : the video output element to use (NULL = default sink)
                        flags: readable, writable
                        Object of type "GstElement"
  vis-plugin          : the visualization element to use (NULL = default)
                        flags: readable, writable
                        Object of type "GstElement"
  text-sink           : the text output element to use (NULL = default textoverlay)
                        flags: readable, writable
                        Object of type "GstElement"
  volume              : The audio volume, 1.0=100%
                        flags: readable, writable
                        Double. Range:               0 -              10 Default:               1 Current:               1
  mute                : Mute the audio channel without changing the volume
                        flags: readable, writable
                        Boolean. Default: false Current: false
  frame               : The last frame (NULL = no video available)
                        flags: readable
                        MiniObject of type "GstBuffer"
  subtitle-font-desc  : Pango font description of font to be used for subtitle rendering
                        flags: writable
                        String. Default: null  Write only
  connection-speed    : Network connection speed in kbps (0 = unknown)
                        flags: readable, writable
                        Unsigned Integer. Range: 0 - 4294967 Default: 0 Current: 0
  buffer-size         : Buffer size when buffering network streams
                        flags: readable, writable
                        Integer. Range: -1 - 2147483647 Default: -1 Current: -1
  buffer-duration     : Buffer duration when buffering network streams
                        flags: readable, writable
                        Integer64. Range: -1 - 9223372036854775807 Default: -1 Current: -1
  av-offset           : The synchronisation offset between audio and video in nanoseconds
                        flags: readable, writable
                        Integer64. Range: -9223372036854775808 - 9223372036854775807 Default: 0 Current: 0
  ring-buffer-max-size: Max. amount of data in the ring buffer (bytes, 0 = ring buffer disabled)
                        flags: readable, writable
                        Unsigned Integer64. Range: 0 - 4294967295 Default: 0 Current: 0

Element Signals:
  "about-to-finish" :  void user_function (GstElement* object,
                                           gpointer user_data);
  "video-changed" :  void user_function (GstElement* object,
                                         gpointer user_data);
  "audio-changed" :  void user_function (GstElement* object,
                                         gpointer user_data);
  "text-changed" :  void user_function (GstElement* object,
                                        gpointer user_data);
  "video-tags-changed" :  void user_function (GstElement* object,
                                              gint arg0,
                                              gpointer user_data);
  "audio-tags-changed" :  void user_function (GstElement* object,
                                              gint arg0,
                                              gpointer user_data);
  "text-tags-changed" :  void user_function (GstElement* object,
                                             gint arg0,
                                             gpointer user_data);
  "source-setup" :  void user_function (GstElement* object,
                                        GstElement* arg0,
                                        gpointer user_data);

Element Actions:
  "get-video-tags" :  GstTagList user_function (GstElement* object,
                                                gint arg0);
  "get-audio-tags" :  GstTagList user_function (GstElement* object,
                                                gint arg0);
  "get-text-tags" :  GstTagList user_function (GstElement* object,
                                               gint arg0);
  "convert-frame" :  GstBuffer user_function (GstElement* object,
                                              GstCaps* arg0);
  "get-video-pad" :  GstPad user_function (GstElement* object,
                                           gint arg0);
  "get-audio-pad" :  GstPad user_function (GstElement* object,
                                           gint arg0);
  "get-text-pad" :  GstPad user_function (GstElement* object,
                                          gint arg0);

Children:
  playsink0
```


การส่งข้อมูลผ่านแต่ละอีลีเมนส์มีลักษณะเป็นลำดับเราเรียกว่า pipeline จากคำสั่งด้านบน เริ่มต้นจาก playbin2  video-sink -> audio-sink -> 
แปลงเป็น Flow graph (แปลตามความเข้าใจตนเอง) 



