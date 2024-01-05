# GoogleTakeout-PhotoMetadataFixer

Fix metadata of exported photos via GoogleTakeout. It is useful when you want to:
- upload an album to a dedicated service to create and print photo book
- upload your photos to another cloud provider

## Prerequisite

The script relies on the powerfool exiftool command. See its [dedicated website](https://exiftool.org) to install it.

## Script

```
# Suffix used to identify edited files. Should be -edited for UK/US users.
EDITED_FILE_SUFFIX="-modifié"

echo Remove the "$EDITED_FILE_SUFFIX" suffix from the name of edited files and remove related unedited files
for edited_file in *$EDITED_FILE_SUFFIX* ; do
    original_file=$(echo "$edited_file" | sed s/$EDITED_FILE_SUFFIX//g)
    rm "$original_file"
    mv -v "$edited_file" "./${original_file}"
done

# Bonus for mac users who wish to convert HEIC files
echo Convert .HEIC to .jpeg
for file in *.HEIC; do
    sips -s format jpeg "$file" --out "${file%.HEIC}.jpeg"
    mv -v "$file.json" "${file%.HEIC}.jpeg.json"
    rm $file
done

echo Update metadata with exiftool
exiftool -d "%s" -tagsfromfile %f.%e.json "-DateTimeOriginal<PhotoTakenTimeTimestamp" "-FileCreateDate<PhotoTakenTimeTimestamp" "-FileModifyDate<PhotoTakenTimeTimestamp" -overwrite_original -ext jpg -ext jpeg -ext mp4 -ext mov .

echo Remove .json files
rm *.json
```
