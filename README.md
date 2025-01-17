# Useful Exiftool Commands (cheat sheet / examples)

Several Exiftool commands I have used to edit/sort my pictures and improve metadata. 
Using this as my personal notepad, so to speak; might be useful for someone.

When it comes to mass editing lots of files, nothing beats the command line. ExifTool is great for this.


### Exiftool show all available EXIF tags of a file
`exiftool -G1 -a -s <filename>`

### Exiftool validate a file and show warnings and errors
`exiftool -validate -warning -error -a FILE`

### Exiftool write all available date fields in EXIF data to first 15 characters of the file name (f. ex. useful for files that are named like 20200503-134310.mp4), for .movs and .mp4s:
`exiftool -v -overwrite_original "-datetimeoriginal<${filename;$_=substr($_,0,15)}" "-createdate<${filename;$_=substr($_,0,15)}" "-FileCreateDate<${filename;$_=substr($_,0,15)}" "-FileModifyDate<${filename;$_=substr($_,0,15)}" "-TrackCreateDate<${filename;$_=substr($_,0,15)}" "-MediaCreateDate<${filename;$_=substr($_,0,15)}" "-MetaDataDate<${filename;$_=substr($_,0,15)}" "-MediaModifyDate<${filename;$_=substr($_,0,15)}" "-TrackModifyDate<${filename;$_=substr($_,0,15)}" "-ModifyDate<${filename;$_=substr($_,0,15)}" -ext m4v -ext mov -ext mp4 .`

### Exiftool rename file to CreateDate of images. This will change the file names to something like "20221028-150847.jpg".
`exiftool -v "-filename <${CreateDate}.%e" -d %Y%m%d-%H%M%S .`

### Exiftool write all available date fields in EXIF data to first 15 characters of the file name (f. ex. useful for files that are named like 20200503-134317.jpg), for .jpgs:
`exiftool -v -overwrite_original "-ImageUniqueID<NewGUID" "-Photoshop:IPTCDigest=" "-CurrentIPTCDigest=" "-XMP-xmp:CreatorTool=" "-datetimeoriginal<${filename;$_=substr($_,0,15)}" "-createdate<${filename;$_=substr($_,0,15)}" "-MetaDataDate<${filename;$_=substr($_,0,15)}" "-ModifyDate<${filename;$_=substr($_,0,15)}"  -ext jpg .`

You could also add the following to the above command to change the system file and date:

`"-FileCreateDate<${filename;$_=substr($_,0,15)}" "-FileModifyDate<${filename;$_=substr($_,0,15)}"`.

Beware that this might interfere with backup software or Onedrive.

### Exiftool set all dates of PDF files to first 10 characters of the file name (f. ex. useful for files that are named like "2020-03-21 Text.pdf")
`exiftool -v -overwrite_original "-FileModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" "-xmp:CreateDate<${filename;$_=substr($_,0,10)} 13:00:00" "-xmp:ModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" "-pdf:CreateDate<${filename;$_=substr($_,0,10)} 13:00:00" "-pdf:ModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" "-CreateDate<${filename;$_=substr($_,0,10)} 13:00:00" "-FileCreateDate<${filename;$_=substr($_,0,10)} 13:00:00" "-FileAccessDate<${filename;$_=substr($_,0,10)} 13:00:00" "-ModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" -ext pdf .`

### CMD (Ghostscript one-liner) to create JPG images of PDF's in a directory; max two pages
```for %I in (*.pdf) do ( gswin64c.exe -dNOPAUSE -dBATCH -dNumRenderingThreads=4 -sDEVICE=jpeg -r300 -dJPEGQ=80 -dFirstPage=1 -dLastPage=2 -sOutputFile="%~nI_p%02d.jpg" "%~I" )```

### Exiftool set all dates of JPG files to first 10 characters of the file name (f. ex. useful for files that are named like "2020-03-21 blabla.jpg")
`exiftool -v -overwrite_original "-datetimeoriginal<${filename;$_=substr($_,0,10)}  13:00:00" "-createdate<${filename;$_=substr($_,0,10)}  13:00:00" "-FileCreateDate<${filename;$_=substr($_,0,10)}  13:00:00" "-FileModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" "-MetaDataDate<${filename;$_=substr($_,0,10)} 13:00:00" "-ModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" "-FileAccessDate<${filename;$_=substr($_,0,10)} 13:00:00" -ext jpg .`

### Exiftool Write all (incl. GPS location) tags FROM .mp4 files TO corresponding XMP files:
`exiftool -v -v -r -ext mp4 -overwrite_original -tagsfromfile %d%f.mp4 -all:all -xmp:all -exif:all -composite:all -quicktime:all -iptc:all -gps:all %d%f.xmp .`

### Exiftool Write all (incl. GPS location) tags FROM .xmp files TO corresponding MP4 video files:
`exiftool -v -r -overwrite_original -tagsfromfile %d%f.xmp -all:all -xmp:all -exif:all -composite:all -quicktime:all -iptc:all -gps:all -ext m4v -ext mov -ext mp4 -ext avi .`

### Exiftool copy all Digikam Tags list to LastKeyword and drop XPkeywords, where there is no "Locatie" keyword in XMP-dc:Subject.
`exiftool -overwrite_original -r -if "not $XMP-dc:Subject=~/Locatie/" "-XMP-dc:Subject<XMP-digiKam:TagsList" "-XMP-microsoft:LastKeywordXMP<XMP-digiKam:TagsList" -XMP-microsoft:LastKeywordIPTC= -IPTCDigest= -IFD0:XPKeywords= -ext jpg -ext jpeg -ext mp4 -ext m4v -ext mov .`

### Exiftool move all files to a directory structure "2020\05\03", without renaming the files themselves:
`exiftool -v -ext jpg -ext mp4 "-Directory<CreateDate" -d %Y\%m\%d\ .`

### Exiftool move all files to a directory structure "2020\05", without renaming the files themselves:
`exiftool -v -ext jpg -ext mp4 "-Directory<CreateDate" -d %Y\%m\ .`

### Exiftool move all files to a directory structure (based on month) "\05", without renaming the files themselves:
`exiftool -v -ext jpg -ext mp4 "-Directory<CreateDate" -d %m\ .`

### Exiftool move all video files from the Pictures folder to the Videos folder, under a directory structure "2020\05\03".
`cd "%userprofile%\OneDrive\Afbeeldingen\Eigen foto's"`

`exiftool -r -v -ext mp4 -ext mov -ext m4v "-Directory<C:\Users\<USER>\OneDrive\Video's\$CreateDate" -d %Y\%m\%d\ .`

### Exiftool move all files to a directory structure "2020\05\03", AND rename the files to format "20200503-134367.jpg" (Year-Month-Day--Hour-Minute-Second):
`exiftool -v -r -d %Y\%m\%d\%Y%m%d-%H%M%S%%-c.%%e "-filename<CreateDate" .`

### Exiftool write current file name to Title and Comment EXIF fields:
`exiftool -r -overwrite_original "-xpcomment<${filename" "-comment<${filename" "-title<${filename" "-xptitle<${filename" .`

### Exiftool find and list all photos without GPS location tag:
`exiftool -p "$directory/$filename" -ext jpg -q -q -r -if "not $gpslatitude" .`

### Exiftool Validate images (list all images with warnings or errors):
`exiftool  -r -validate -warning  -a -ext:jpg .`

### Exiftool find and list all photos Without "createdate" EXIF tag:
`exiftool -p "$directory/$filename" -r -if "(not $createdate)" .`

### Exiftool find and list all photos where the DateTimeOriginal does not match the file name:
`exiftool -r -d "%Y%m%d" -if "$FileName !~ /^$DateTimeOriginal/" -p "$directory/$filename" . `

### Exiftool find and list all photos where the CreateDate does not match the file name:
`exiftool -r -d "%Y%m%d" -if "$FileName !~ /^$CreateDate/" -p "$directory/$filename" .`

### Exiftool find and list all photos Without "datetimeoriginal" EXIF tag:
`exiftool -p "$directory/$filename" -r -if "(not $datetimeoriginal)" .`

### Exiftool find and list all photos Without any date EXIF tag:
`exiftool -p "$directory/$filename" -r -if "(not $datetimeoriginal or $createdate)" .`

### Exiftool REPAIR images metadata (reads all data, and writes it back - without makernotes. This fixes most common issues:
`exiftool -overwrite_original -all= -tagsfromfile @ -all:all -unsafe -icc_profile -ExifVersion=0232 -makernotes= .`

### Exiftool remove all makernotes:
`exiftool -overwrite_original -makernotes:all= .`

### Exiftool import all image data from JSON files (from Google Takeout), and write to EXIF data of corresponding photos:
`exiftool -overwrite_original -v -r -d %s -tagsfromfile "%d/%F.json" "-GPSAltitude<GeoDataAltitude" "-GPSLatitude<GeoDataLatitude" "-GPSLatitudeRef<GeoDataLatitude" "-GPSLongitude<GeoDataLongitude" "-GPSLongitudeRef<GeoDataLongitude" "-ModifyDate<PhotoTakenTimeTimestamp" "-CreateDate<PhotoTakenTimeTimestamp" "-DateTimeOriginal<PhotoTakenTimeTimestamp" -ext jpg -overwrite_original`

### Exiftool find all photos that have NO Microsoft Face tag but HAVE an XMP-MWG Face tag, and add a keyword to those:
`exiftool -r -ext jpg -overwrite_original -m -v -if "($RegionName) and (not $RegionRectangle)" -Keywords+="Has-MS-Face-but-no-XMP-face" .`

### Exiftool find all photos that have NO Microsoft (MXP-MP) Face tag but HAVE a name in the Digikam /Mensen/ tree:
`exiftool -p "$directory/$filename" -r -if "($XMP-digiKam:TagsList=~/INSERT-NAME-HERE/i) and (not $RegionPersonDisplayName=~/INSERT-NAME-HERE/i)" .`

### Exiftool find all photos that have NO Microsoft (MXP-MP) Face tag but HAVE a name in the Digikam /Mensen/ tree and add a tag to those:
`exiftool -r -ext jpg -overwrite_original -m -if "($XMP-digiKam:TagsList=~/INSERT-NAME-HERE/i) and (not $RegionPersonDisplayName=~/INSERT-NAME-HERE/i)" -XMP-digiKam:TagsList+="Check" .`

### Exiftool create a .txt file with all photos, and listing who is on which photo (face tags):
`exiftool -T -Directory -Filename -RegionPersonDisplayName -r -ext jpg . > PeopleTags.txt`

### Exiftool sort photos to subfolders of Camera make and camera model
`exiftool -r -v -overwrite_original -ext jpg "-filename<\${make;} ${model;}\%f.%e" .`

### Exiftool Remove ALL metadata from a file
`exiftool -overwrite_original -all= <filename>`

### Exiftool add CreateDate Exif Property and Copy DateTimeOriginal Exif Property Value into It
Useful for if many pictures do not have the CreateDate exif-property, but do have the DateTimeOriginal exif-property. If you want the CreateDate exif-property to have the same value as the DateTimeOriginal exif property:

`exiftool -overwrite_original '-createdate<datetimeoriginal' -r -if '(not $createdate and $datetimeoriginal)' <your directory>`

### Exiftool batch copy ALL tags from .MOV to .MP4 video files after converting with ffmpeg
The .movs and .mp4s should be in the same directory with the same file name, only a different extension

`exiftool -v -tagsfromfile %d%f.mov "-all:all>all:all" -overwrite_original -ext mp4 .`


