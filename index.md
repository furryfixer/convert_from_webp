# convert_from_webp
Usage:
```convert_from_webp ```<*file1.webp*> <*file2.webp*> . . .

A bash script to convert EITHER or BOTH .webp images or animations to other formats.
- **Will differentiate between still pics and animations**
- **Will process multiple files, allowing mixture of pics/animations**
- **Will average frame delay/duration where variable in webp animations to better approximate original playback speed**

This script is not particularly fast, both because it is bash, but also because it averages the frame durations to process webp animations.  This creates better results for webp animations that vary delay times between frames. The script requires at least "**libwebp-tools**", and optionally, "**libnotify**". Basenames or full pathnames may be used, but arguments must be files, not directories (no recursion). Default ouput formats are **PNG** for pics and **GIF** for animations. This may be changed by editing the first two lines after the comments in the script.

The script works fine from the command line but was created to be used as a "custom action" in thunar after the selectiion one or more webp files in a directory.  To use, download to **/usr/local/bin** or **/usr/bin** and make executable.
```
#!/bin/bash
#####################################################################
# CONVERT_FROM_WEBP by furryfixer
#
# A bash script to convert EITHER or BOTH .webp images or animations
# to other formats.
#  * Will differentiate between still pics and animations
#  * Will process multiple files, allowing mixture of pics/animations
#  * Will average frame delay/duration if variable in webp
#      animations to better approximate original playback speed
# Requires "libwebp-tools" and optionally, libnotify. Basenames or
# full pathnames may be used, but arguments must be files,
# not directories (no recursion). Default ouput formats are:
# PNG for pics and GIF for animations. Change in first two lines 
# if other formats are desired.
#####################################################################

pic_ext="png"        # Format must be understood by libwebp/ convert 
animation_ext="gif"  # Format must be understood by libwebp/ convert

while (( "$#" )); do
   dlay=${DELAY:-0}
   loops=${LOOP:-0}
   sum_dur=0
   full_filename=`realpath $1`
   prefix=`echo -n $full_filename | sed -e 's/^\(.*\).webp$/\1/'`
   [[ -z $prefix ]] && prefix=$full_filename
   basenm=`basename $prefix`
   n=`webpinfo -summary $full_filename | grep frames | sed -e 's/.* \([0-9]*\)$/\1/'`
   if [[ $n -eq 1 ]]; then
      dwebp $prefix.webp -o $prefix.png
      ext=$pic_ext
   else
      webpinfo -summary $full_filename | grep Duration | sed -e 's/.* \([0-9]*\)$/\1/g' > /tmp/webp_durs
      if [[ $dlay -eq 0 ]]; then
         while read -r dur; do      # sum and average durations if not all same
            sum_dur=$(( $sum_dur+$dur ))
         done < /tmp/webp_durs
         ms_delay=$((sum_dur / n + 5)) # add 5 to round properly below (not floating-point)
         dlay=$((ms_delay / 10))
      fi
      convert -delay $dlay -dispose none $full_filename -coalesce -loop $loops -layers optimize $prefix.gif
      ext=$animation_ext
   fi
   notify-send "Converted "$basenm".webp to "$basenm"."$ext
   echo "Converted "$basenm".webp to "$basenm.$ext
   shift
```
