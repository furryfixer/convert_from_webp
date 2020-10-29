# convert_from_webp
Usage:
```convert_from_webp ```<*file1.webp*> <*file2.webp*> . . .

A bash script to allow linux users to convert EITHER or BOTH .webp images or animations to other formats.
- **Will differentiate between still pics and animations**
- **Will process multiple files, allowing mixture of pics/animations**
- **Will average frame delay/duration where variable in webp animations to better approximate original playback speed**

This script is not particularly fast, both because it is bash, but also because it averages the frame durations to process webp animations.  This creates better results for webp animations that vary delay times between frames. The script requires at least "**libwebp-tools**", and optionally, "**libnotify**". Basenames or full pathnames may be used, but arguments must be files, not directories (no recursion). Default ouput formats are **PNG** for pics and **GIF** for animations. This may be changed by editing the first two lines after the comments in the script.

The script works fine from the command line but was created to be used as a "custom action" in thunar after the selectiion one or more webp files in a directory.  To use, download to **/usr/local/bin** or **/usr/bin** and make exectuable.
