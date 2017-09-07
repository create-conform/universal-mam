# Universal MAM Specification
Version 0.1 Draft 1
2017-09-07
Nick Verlinden

## Introduction

This spec is created out of the need to store clip data and metadata in a way that all applications can use, without the need to
create a full blown, database-backed media asset management system.
The data structure does not use any database technology, but instead the plain filesystem. This ensures portability.

## Clips

Clips can be:
* Single files (ex.: .mp4, .mov, .avi, ...)
* Folders containing multiple files (ex.: CinemaDNG, PNG Sequence, ...)

Clips can be single files, or folders that contain multiple files, such as CinemaDNG. An md5 checksum is created to identify the clip.
When a checksum is being created a file with the original filename but added extension .umlock is created. When the checksum is generated, 
it will be stored inside the umlock file, and the .umlock extension is changed into .md5. Using this md5, you can find the clip data in
the clip data storage folder (wich could be anywhere, it has to be known by the client software).

### Clip Storage Folder

This is a folder where clip data is stored in subfolder that have the md5 sum as its name. This folder can be anywhere. the client needs to know where it is. Clip data can also be split up over multiple clip storage folders. Example: store transcodes and metadata on different locations.

### Clip Data

Clip data is stored in a subfolder of a clip storage folder.

```
    -> /[MD5]/
    -> /[MD5]/logs/
    -> /[MD5]/specs/0.1.umlock
    -> /[MD5]/metadata/file.json, streams.json, exif.json, transcodes.json
    -> /[MD5]/markers/[milliseconds]_[count].json
    -> /[MD5]/thumbs/[frame]_[vresolution].jpg
    -> /[MD5]/transcodes/[MD5]{_a_[acodec]_[achannels]ch_[asamplerate]khz_[abitdepth]bit_[abitrate]}{_v_[vcodec]_[vresolution]_[vbitdepth]_[vbitrate]}.[extension]
    -> /[MD5]/app/[ID]/
 ```

#### Clip Data Folders

##### Logs

Contains log files for actions performed on the clip. This could be a video transcode, or when the file was backuped or restered, ...

##### Specs

Contains .umlock files to indicate version support. These indicate the spec supported in order to use the clip data folder.
If files are created in a future spec; say 0.3; and a 0.1.umlock file is present, means that a client that supports the 0.1 spec will be able to use
the clip data.

##### Metadata

File | Description
---- | ----
file.json | Contains original file name, file size, computer name, user name for folders, contains a list of the above. 
streams.json | Contains stream data.
exif.json | Contains exif data.
transcodes.json | Contains transcoded versions that should be available. This information can be used to recreate the transcoded versions when they are lost.

TODO: Examples.

##### Markers

Every json file is a marker. Its name is composed of the timecode in milliseconds and a count number for when multiple markers are made on the same timecode.
TODO: Example.

##### Thumbs

Every json file is a thumbnail. Its name is composed of the timecode in milliseconds and the vertical resolution.

Example: 
```
12056_288p.jpg
```

##### Transcodes

This is the folder where transcodes of the clip are stored. This could be proxies for displaying in a web interface, or exports to other formats.

TODO: what to do with naming in case of multiple video or audio streams?

File name composition:
```
[MD5]{_a_[acodec]_[achannels]ch_[asamplerate]khz_[abitdepth]bit_[abitrate]}{_v_[vcodec]_[vresolution]_[vbitdepth]_[vbitrate]}.[extension]
```

Media Examples:
```
Audio + Video
[MD5]_a_aac_5.1ch_48khz_24bit_192kbps_v_h264_576p_8bit_2mbps.mp4

Audio + Video
[MD5]_a_aac_2ch_44.1khz_16bit_192kbps_v_h264_576p_8bit_2mbps.mp4

Audio Only
[MD5]_a_aac_2ch_44.1khz_16bit_192kbps.mp4

Video only
[MD5]_v_h264_576p_8bit_2mbps.mp4
```

##### Apps

Here is where apps can save custom clip data.
Ever app needs to create a subfolder with a unique hash as its name. Within that folder apps are free to do as they please.