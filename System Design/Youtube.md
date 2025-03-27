#### Learnings
- Divide the video into chunks and store different resolutions of the same chunk so as to allow adaptive bitrate streaming, this would ensure that we pull in the lower quality chunk whenever we have poor internet quality, this can be controlled at the CDN level.
- Videos are not stored as is but are compressed using codecs
- Post processing of the video in S3 is essential for segmentation and adaptive bitrate streaming.


#### Points about Videos
- Codecs: Encoder/Decoders
- Video Container: File Format for storing Video data.
- Bitrate: Number of bits transferred over a period of time measured in kbps and mbps
- Manifest Files: Text based files giving details about video formats. They serve as an index to these segments.
- 