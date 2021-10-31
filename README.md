# Useful Exiftool Commands (cheat sheet / examples)

Several Exiftool commands I have used to edit/sort my pictures and improve metadata. 
Using this as my personal notepad, so to speak; might be useful for someone.

When it comes to mass editing lots of files, nothing beats the command line. ExifTool is great for this.


### Exiftool show all available EXIF tags of a file
`exiftool -G1 -a -s <filename>`

### Exiftool Write all available EXIF data from .mp4 files to corresponding XMP files
`exiftool -v -v -r -overwrite_original -ext mp4 -tagsfromfile @ -all:all -xmp:all -exif:all -iptc:all -n -srcfile %d%f.xmp -r .` 

### Exiftool write all available date fields in EXIF data to first 15 characters of the file name (f. ex. useful for files that are named like 20200503-134367.mp4), for .movs and .mp4s:
`exiftool -overwrite_original "-datetimeoriginal<${filename;$_=substr($_,0,15)}" "-createdate<${filename;$_=substr($_,0,15)}" "-FileCreateDate<${filename;$_=substr($_,0,15)}" "-FileModifyDate<${filename;$_=substr($_,0,15)}" "-TrackCreateDate<${filename;$_=substr($_,0,15)}" "-MediaCreateDate<${filename;$_=substr($_,0,15)}" "-MetaDataDate<${filename;$_=substr($_,0,15)}" "-MediaModifyDate<${filename;$_=substr($_,0,15)}" "-TrackModifyDate<${filename;$_=substr($_,0,15)}" "-ModifyDate<${filename;$_=substr($_,0,15)}" -ext mov -ext mp4 .`

### Exiftool write all available date fields in EXIF data to first 15 characters of the file name (f. ex. useful for files that are named like 20200503-134367.jpg), for .jpgs:
`exiftool -v -overwrite_original "-datetimeoriginal<${filename;$_=substr($_,0,15)}" "-createdate<${filename;$_=substr($_,0,15)}" "-FileCreateDate<${filename;$_=substr($_,0,15)}" "-FileModifyDate<${filename;$_=substr($_,0,15)}" "-MetaDataDate<${filename;$_=substr($_,0,15)}" "-ModifyDate<${filename;$_=substr($_,0,15)}"  -ext jpg .`

### Exiftool set all dates of PDF files to first 10 characters of the file name (f. ex. useful for files that are named like "2020-03-21 Text.pdf")
`exiftool -v -overwrite_original "-FileModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" "-xmp:CreateDate<${filename;$_=substr($_,0,10)} 13:00:00" "-xmp:ModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" "-pdf:CreateDate<${filename;$_=substr($_,0,10)} 13:00:00" "-pdf:ModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" "-CreateDate<${filename;$_=substr($_,0,10)} 13:00:00" "-FileCreateDate<${filename;$_=substr($_,0,10)} 13:00:00" "-FileAccessDate<${filename;$_=substr($_,0,10)} 13:00:00" "-ModifyDate<${filename;$_=substr($_,0,10)} 13:00:00" -ext pdf .`

### Exiftool Write all (incl. GPS location) tags FROM .mp4 files TO corresponding XMP files:
`exiftool -v -v -ext mp4 -overwrite_original -tagsfromfile %d%f.mp4 -all:all %d%f.xmp .`

### Exiftool move all files to a directory structure "2020\05\03", without renaming the files themselves:
`exiftool -v -ext jpg -ext mp4 "-Directory<CreateDate" -d %Y\%m\%d\  .`

### Exiftool move all files to a directory structure "2020\05\03", AND rename the files to format "20200503-134367.jpg" (Year-Month-Day--Hour-Minute-Second):
`exiftool -v -r -d %Y\%m\%d\%Y%m%d-%H%M%S%%-c.%%e "-filename<CreateDate" .`

### Exiftool write current file name to Title and Comment EXIF fields:
`exiftool -r -overwrite_original "-xpcomment<${filename" "-comment<${filename" "-title<${filename" "-xptitle<${filename" .`

### Exiftool find all photos without GPS location tag:
`exiftool -p "$directory/$filename" -ext jpg -q -q -r -if "not $gpslatitude" .`

### Exiftool find all photos Without "createdate" EXIF tag:
`exiftool -p "$directory/$filename" -r -if "(not $createdate)" .`

### Exiftool find all photos Without "datetimeoriginal" EXIF tag:
`exiftool -p "$directory/$filename" -r -if "(not $datetimeoriginal)" .`

### Exiftool find all photos Without any date EXIF tag:
`exiftool -p "$directory/$filename" -r -if "(not $datetimeoriginal or $createdate)" .`

### Exiftool remove all makernotes:
`exiftool -overwrite_original -makernotes= .`

### Exiftool import all image data from JSON files (from Google Takeout), and write to EXIF data of corresponding photos:
`exiftool -overwrite_original -v -r -d %s -tagsfromfile "%d/%F.json" "-GPSAltitude<GeoDataAltitude" "-GPSLatitude<GeoDataLatitude" "-GPSLatitudeRef<GeoDataLatitude" "-GPSLongitude<GeoDataLongitude" "-GPSLongitudeRef<GeoDataLongitude" "-ModifyDate<PhotoTakenTimeTimestamp" "-CreateDate<PhotoTakenTimeTimestamp" "-DateTimeOriginal<PhotoTakenTimeTimestamp" -ext jpg -overwrite_original`

### Exiftool find all photos that have NO Microsoft Face tag but HAVE an XMP-MWG Face tag, and add a keyword to those:
`exiftool -r -ext jpg -overwrite_original -m -v -if "($RegionName) and (not $RegionRectangle)" -Keywords+="Has-MS-Face-but-no-XMP-face" .`

### Exiftool create a .txt file with all photos, and listing who is on which photo (face tags):
`exiftool -T -Directory -Filename -RegionPersonDisplayName -r -ext jpg . > PeopleTags.txt`

### Exiftool sort photos to subfolders of Camera make and camera model
`exiftool -r -v -overwrite_original -ext jpg "-filename<\${make;} ${model;}\%f.%e" .`

### Exiftool Remove ALL metadata from a file
`exiftool -overwrite_original -all= <filename>`

### Exiftool add CreateDate Exif Property and Copy DateTimeOriginal Exif Property Value into It
Useful for if many pictures do not have the CreateDate exif-property, but do have the DateTimeOriginal exif-property. If you want the CreateDate exif-property to have the same value as the DateTimeOriginal exif property:

`exiftool -overwrite_original '-createdate<datetimeoriginal' -r -if '(not $createdate and $datetimeoriginal)' <your directory>`
