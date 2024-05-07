# Insta360 GO 3 notes

## EXIF tag limitations and fixes

### The original files do not have model information.

The raw files include `Make=Insta360` but lack a `Model` tag.

Solution:

1. Use `exiftool` to add these tags.

    ```sh
    exiftool -Make="Insta360" -Model="GO 3" PRO_VID_000.mp4
    ```

### The iOS app does not export any metadata.

Solution:

1. Copy EXIF tags from the original file to the new file.

    ```sh
    exiftool -TagsFromFile PRO_VID_000.mp4 -all:all exported.mp4
    ```

### The camera saves local timestamps without a timezone so it gets interpreted as UTC.

Solution:

1. Add a shortcut for all video date tags (`AllDates` is insufficient).

    ```perl
    %Image::ExifTool::UserDefined::Shortcuts = (
        VideoDates => [
            'Quicktime:CreateDate',
            'Quicktime:ModifyDate',
            'Quicktime:TrackCreateDate',
            'Quicktime:TrackModifyDate',
            'Quicktime:MediaCreateDate',
            'Quicktime:MediaModifyDate',
        ],
    );
    ```

2. Use the shortcut to adjust timezone. I'm in UTC-7 time zone, I use `+=7`. Adjust accordingly.

    ```sh
    exiftool -AllDates+=7 -VideoDates+=7 PRO_VID_000.mp4
    ```


References:

- shortcut idea: https://exiftool.org/forum/index.php?topic=12444.msg67299#msg67299
