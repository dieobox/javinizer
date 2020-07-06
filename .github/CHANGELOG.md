# Change Log

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/)
and this project adheres to [Semantic Versioning](http://semver.org/).

## [1.7.0]
### Added
- Added setting to match JAV files using regular expressions
    - Regex match will not perform any string transformations, so the movie ID in your filename will need to match the website metadata exactly
    - This is intended for users who have previously sorted files using a unique template and are unable to match using Javinizer's default matcher
    ```
    # Default values
    [General]
    regex-match=false
    regex=([a-zA-Z|tT28]+-\d+z{0,1}Z{0,1}e{0,1}E{0,1})(?:-pt){0,1}(\d{1,2})?
    ```
- **Experimental** Added JAVLibrary integration with setting movies as "Owned" when sorting with Javinizer
    ```
    # Default values
    [JAVLibrary]
    set-owned=True
    username=
    session-cookie=
    ```

### Fixed
- Fixed `-GetThumbs` and `-UpdateThumbs` functionality
    - Removed PoshRSJob dependency, instead using PowerShell Core native `ForEach-Object -Parallel` for multi-threaded webpage scraping
        - There will no longer be a progress bar displayed
- Fixed error output due to missing native dependencies when checking for Javinizer module updates

## [1.6.0]
### Added
- Initial Jav321 scraper functionality
    - Setting scrape-jav321
- Actress thumb url scraping for JavBus scraper

## [1.5.0]
### Added
- Initial JavBus scraper functionality
    - Setting scrape-javbus, scrape-javbusja
- Setting toggle for r18.com actress name language scraping to csv when using -GetThumbs or -UpdateThumbs
    - scrape-actress-en
    - scrape-actress-ja
- Setting `max-path-length` to allow user to define maximum path length of sorted files
- Enhanced `-ViewLog` functionality and parameters
    - Colored output for ERROR and WARN log messages
    - -ViewLog <String> (List, Object, Table, Grid)
    - -Entries <Int>
    - -Order <String> (Asc, Desc) Default: Desc
    - -LogLevel <String> (Info, Error, Warn, Debug)
    - Examples:
        - `Javinizer -ViewLog List`
        - `Javinizer -ViewLog Table -LogLevel Error -Entries 10`
        - `Javinizer -ViewLog Object | Where-Object {$_.message -like 'Skipped*'}`

### Removed
- Host output when Javinizer function is started/stopped

## [1.4.2]
### Fixed
- `-MoveToFolder` and `-RenameFile` being incorrectly applied when `-Multi` is applied
- Files being moved/named incorrectly when `rename-file=False`
- nfo files being named incorrectly when `rename-file=False` and `create-nfo-per-file=True`

## [1.4.1]
### Fixed
- Fixed Javinizer update check

## [1.4.0]
### Added
- Ability to select which file extensions to use with Javinizer with setting `included-file-extensions`
- Ability to exclude files that match a string/wildcard value with setting `excluded-file-strings`
    - This uses the `-Exclude` parameter on cmdlet `Get-ChildItem` which supports string paths/strings with wildcard (*) Regex is NOT supported
- Ability to omit the creation of a nfo file for a movie with setting `create-nfo`
- Ability to create a nfo file per movie file sorted (for multi-part videos) with setting `create-nfo-per-file`
    - This is now set as default, as this is a requirement for metadata to be loaded with movies into Emby/Jellyfin
- Better logging functionality and setting of a static log path with setting `log-path`
    - View logs from console with `Javinizer -ViewLog`
    - Output as a PowerShell object, so you can specify pipeline elements (e.g `Javinizer -ViewLog | Select-Object -First 10 | Sort-Object timestamp -Descending | Format-Table -Wrap`)

### Changed
- Removed r18 from default description
- Logging to JSON formatted

### Fixed
- `-MoveToFolder`, `-RenameFile`, `-Force` parameters when using `-Multi` sort not being passed through
- Duplicates being added to r18-thumbs.csv
- Poster images failing to be cropped from Python when using Windows UNC paths (e.g \\server\jav\unsorted\)

## [1.3.0]
### Added
- Additional language support for existing scraper sources
    - JavLibrary ZH (CN) `scrape-javlibraryzh=True`
    - JavLibrary JA `scrape-javlibraryja=True`
    - R18 ZH `scrape-r18zh=True`
- Original Japanese actress entries to r18-thumbs.csv
- Alternate language selection for description translation `translate-description-language=en`
- Parameter `-ImportSettings` to specify an external settings file
    - Example: `Javinizer -Path C:\Downloads\JAV-Unsorted -ImportSettings C:\settings-template1.ini -Multi`
    - This is for those of you running Javinizer in automation or want to just specify different presets when sorting
- Parameter `-MoveToFolder` and `-RenameFile` to set true/false value for `move-to-folder` and `rename-file` in the commandline
    - Example: `Javinizer -Path C:\Downloads\JAV-Sorted -Recurse -MoveToFolder:$false -RenameFile:$false`
    - This is for when you want to refresh metadata for already sorted videos but don't want to manually adjust your settings file

### Changed
- Default naming of `poster.jpg` -> `folder.jpg`
    - This allows the poster image to show up as the default folder thumbnail in Windows File explorer
    - I have not noticed any issues with Plex/Emby/Jellyfin for this naming convention but let me know if there is a conflict
- Default throttling of thumbnail updates to 5
    - Requests have been erroring out due to the speed of the requests and Cloudflare throttling
- Update check will only occur once per session by checking global variable

### Fixed
- Actresses from DMM not being properly added to nfo when `first-last-name-order=false`
- R18 thumbnail csv adding duplicate entries with reversed order when `first-last-name-order=false`

## [1.2.0]
### Added
- Functionality to download actress images that are pulled from r18-thumbs.csv
- Functionality to check for Javinizer module updates on startup
    - Setting `check-updates=<True/False>`

### Fixed
- Issue where actresses scraped from JAVLibrary found in the r18-thumbs.csv file but not matching the correct casing (upper/lowercase) would erroneously be replaced with the last entry in r18-thumbs.csv

## [1.1.15] 03-08-2020
### Fixed
- Hotfix for 1.1.13 - Actresses failing to write to metadata properly when falling back to secondary or greater priority

## [1.1.14] 03-08-2020
### Fixed
- Hotfix for 1.1.13 - Actors/screenshots metadata failing to download when setting `move-to-folder=false`

## [1.1.13] 03-07-2020
### Added
- Basic logging functionality for sorted and skipped files
    - Logs will be written by default, and cannot be turned off
    - View using `Javinizer -OpenLog`
- Additional file renaming strings
    - \<ACTORS>
    - \<SET>
    - \<LABEL>
    - \<ORIGINALTITLE>
- Setting option
    - `actors-name-delimiter=", "`

### Changed
- Cap of 215 max path length for created directories

### Fixed
- Genres failing to write to metadata properly when falling back to secondary or greater priority
- Actors/screenshots metadata failing to download when setting `move-to-folder=false`

## [1.1.12] 02-19-2020
### Added
- More flexible metadata file naming in settings
    - poster-file-string="poster"
    - thumbnail-file-string="fanart"
    - trailer-file-string="\<ID>-trailer"
    - nfo-file-string="\<ID>"
    - screenshot-folder-string="extrafanart"
    - screenshot-img-string="fanart"
    - actorimg-folder-string=".actors"

### Changed
- Console output now includes timestamps
- Condensed warning output on files failed to sort

### Fixed
- Files not failing to sort when genres are null

## [1.1.11] 02-08-2020
### Fixed
- Error on creating cloudflare session with Javlibrary.com
- Script failing when certain criteria are met when downloading actress thumbnails

## [1.1.10] 12-29-2019
### Added
- `-Strict` parameter to not clean filenames when scraping
- `rename-file` setting functionality

### Changed
- R18/DMM matching function to be more accurate and resilient
- JAVLibrary matching function to be more accurate and resilient

### Fixed
- Matching for r18 videos with only 1 returned search result or 0 matched search results

## [1.1.9] - 12-26-2019
### Fixed
- Additional error with downloading actress images with single actress video

## [1.1.8] - 12-26-2019
### Fixed
- Fixed single-word actresses appending underscore `_` to filename when downloading actress images
- Director and Genre metadata fields being cut off by slash `/` in Plex, replaced text with `-`

## [1.1.7] - 12-23-2019
### Changed
- Behavior when description translation fails to let original DMM description be written to nfo metadata

### Fixed
- Description metadata being set to null when `translate-description` is set to False
- JAVLibrary maker being set in director field when there is no director
- Additional R18 censors

## [1.1.6] - 12-19-2019
### Fixed
- `-UpdateThumbs` parameter erroring out on actress written due to missing ReversedFullName

## [1.1.5] - 12-19-2019
### Fixed
- Running Javinizer without `-Multi` parameter fails to sort any files
- Having setting `move-to-folder=False` writing non-video files to the root `-Path` directory

## [1.1.4] - 12-18-2019
### Changed
- Throttle limit from 5 --> 15
- `<set>` nfo metadata added by default, `<tag>` still optional

### Fixed
- Most redundant error messages when running multisort with multi-part videos
- Invalid trailer in aggregated object if R18 trailers not found

## [1.1.3] - 12-16-2019
### Changed
- Tag from `Series: <tag>` to `<tag>`

### Fixed
- Null series being added as a tag with `add-series-as-tag` true
- R18 series string not uncensoring censored words
- Additional R18 censored words

## [1.1.2] - 12-15-2019
### Fixed
- Additional issues to writing missing actresses to `r18-thumbs.csv`

## [1.1.1] - 12-15-2019
### Fixed
- Videos with 2+ actresses not writing to `r18-thumbs.csv` if missing

## [1.1.0] - 12-15-2019
### Added
- Add `SetEmbyActorThumbs` parameter to push actor thumbnails to your Emby/Jellyfin instance
- Add `BackupSettings` parameter to backup configuration files to an archive
- Add `RestoreSettings` parameter to restore configuration files from an archive to module root

### Changed
- Actor role tag `Actress` for each actress

### Fixed
- JAVLibrary metadata title null when scraping non-standard title such as T28-*

## [1.0.1] - 12-14-2019
## Added
- Verbose messages for start/end of each file sort to more easily diagnose where issues arise during `multi` sort

### Fixed
- Null/invalid actresses being added to `r18-thumbs.csv` when found missing actress
- Direct file specified in `Path` parameter erroring due to DestinationPath being set to the file rather than its directory

## [1.0.0] - 12-14-2019 **Production-ready release**
### Added
- Setting `download-actress-img` to download actress images to video's local `.actors` folder
- Parameter `GetThumbs`, `UpdateThumbs`, and `OpenThumbs` to update r18 actress and thumburl csv file
- Parameter `Recurse` to find all video files recursively from sort path
- Feature to attempt to match actresses with missing thumburl to r18-thumbs.csv file
- Feature to automatically add scraped R18 actresses/thumburls to `r18-thumbs.csv file`
- Feature to normalize JAVLibrary genres to their R18 counterparts with setting `normalize-genres`
- `move-to-folder` functionality to allow user to not move file to new folder when sorting
- `minimum-filesize-to-sort` functionality to set minimum filesize video to sort
- `m4v` and `rmvb` video match support

### Changed
- Script to start cloudflare session before multi-sort begins
- All file downloads to run asynchronously except cover image

### Fixed
- Calling a relative destination path errored when running using `-Multi`
- Actress thumburl being ignored for single actress videos if actress priority set as `r18,javlibrary`

## [0.1.9] - 12-8-2019
### Added
- Parameter `OpenSettings` to open your settings file
- Parameter `Help` to display comment-based help for Javinizer usage

### Fixed
- Single actress sames scraped from R18 with trailing space

## [0.1.8] - 12-8-2019
### Changed
- Various parts for compatibility with PowerShell Gallery releases

## [0.1.7] - 12-8-2019
### Added
- Muli-threaded sorting functionality with parameter `Multi`
- Ability to select name order for actress with setting `first-last-name-order`
- Comment based help `PS> Help Javinizer`

### Changed
- Default DestinationPath will be set to your `Path` parameter rather than your settings `output-path`

### Fixed
- Javlibrary failing to match if the first result is error
- R18 failing to match video if ID doesn't return results, but ContentID does
- R18 finding `----` as ID set to null
- Actress thumburls being assigned to wrong actress
- File displayname being affected by `max-title-length`
- Invalid label on DMM when label is null
- Metadata values nulled if first priority did not return results
- Error message when actress is present but thumburl is null

## [0.1.6] - 12-6-2019
### Added
- `<RUNTIME>` as filename option
- Add setting `max-title-length`

### Changed
- Filename string replacement of `/` to `-`
- Keep empty `<thumb></thumb>` tag when actress thumburl is null

### Removed
- Alternate actress names surrounded by parentheses in metadata

### Fixed
- Brackets `[]` in filename titles erroring
- Writing actresses to nfo for videos with more than one actress

## [0.1.5] - 12-4-2019
### Added
- Settings being written in debug output
- T28/T-28 video recognition support
- String replacement for censored words on R18.com
- Error throw messages on unsuccessful download of images/trailer

### Changed
- Non-creation of `extrafanart` directory if no screenshots are downloaded
- More settings defaults to javlibrary
- Python dependency to Python3
- Unix operating systems will now call `python3` rather than `python`

### Removed
- Spaces between ` - ` in  part number and trailer

### Fixed
- Genres failing to be written to nfo if first priority setting did not find video
- Actresses failing to be written to the nfo if there was only one actress
- Description translation failing due to unicode errors from Python 2 versions
- Having multipart videos in the sort directory causes non-multipart videos to have part number assigned to it
- JAVLibrary url search fail on first non-matched result
- Set `Test-Path` to literalpath for Unix compatibility
- Titles in directory name failing due to special characters not being removed

## [0.1.4] - 12-3-2019
### Changed
- .NET CultureInfo class conversion to hard coded month values for R18 data scrape
- Better catch for separation between multi-part and single videos

### Fixed
- Genre not being output in nfo
- Rating vote count not added in aggregated data object and nfo

## [0.1.3] - 12-2-2019
### Changed
- Setting `add-series-as-tag` set default false
- Remove debug output for `Convert-HTMLCharacter`

### Fixed
- Fixed `<trailer>` nfo data not appearing in scraped .nfo file
- Fixed javlibrary data object not being output
- Fix spacing in nfo between `<mpaa>` and next tags

## [0.1.2] - 12-1-2019
### Added
- Multi-part video support
- `<mpaa>XXX</mpaa>` tag in nfo metadata
- Better console output for skipped files
- Setting `download-trailer-vid` to allow downloading of movie trailers

### Changed
- `<rating>` tag in nfo metadata to legacy format
- Cloudflare `$session` scope to `global` instead of `script`
- `-Find` parameter better search output

### Fixed
- R18 series title scrape not working properly
- Function stopping on first skipped file
- Directory match Write-Host under the single match
- JAVLibrary url parsing and multiple result check
- Comma separated format with an extra space ', ' instead of ','
- Type 'attemping' to 'attempting'

## [0.1.1] - 11-30-2019
### Fixed
- Try/catch for directory sort

## [0.1.0] - 11-30-2019
### Added
- Initial functionality release
- Web searching with `-Find`
- File and directory sort with `-Path` and `-Apply`

