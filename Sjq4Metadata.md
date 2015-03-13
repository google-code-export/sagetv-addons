# Introduction #

When you queue up tasks from various menus or tasks are queued up in response to a core event, SJQv4 will automatically attach metadata about the object associated with the event or menu from which the task was queued.  This document lists all of the metadata that will be attached to tasks for specific events.

# Accessing the Metadata #

The metadata provided is accessed in one of two ways:

  * If you're in an SJQ ScriptEnv script (i.e. Groovy script) then you access the metadata via the `SJQ4_METADATA` map.  It's a map of string keys to string values.  The keys of the map are the `Variables` in the tables below.  Remember, that all values in the map are **strings** and need to be converted as required.
  * If you're in an executable, shell script, batch file, etc. (i.e. anything other than a ScriptEnv script) then these maps are available as environment variables in your running process.  Access to the values is implementation dependant.  In a Windows batch file you'd access the env vars like `%SJQ4_TYPE%` and in a Linux shell script it would be like `$SJQ4_TYPE`.  If you're in a Java program, C#, Perl, etc. then see your programming API for details on how to access environment variables within your programs.

# Object Metadata Maps #

The tables below describe the metadata attached for each of the supported Sage object types.  For the values column, a `fixed width` string denotes a literal value (i.e. that's the actual value of the variable you will see at runtime).  An _italic_ value is a description of what the value will be (i.e. _Integer_ means that the value will be an integer number).  **NOTE:** All values in the map are actually strings and need to be converted as required.  So even though the type may say _Integer_, it's only describing the format of the value.  You must convert it to an integer within your Groovy script. Please be aware that only a limited subset of each object type's values are actually exposed in the metadata maps.  If the data you need is not exposed then you simply have to access the object directly in your script and pull out whatever data you want.  This is possible because the type of object and the unique id (key) will always be exposed to the metadata map (when possible).

## Airing ##

| **Variable** | **Value** | Description | Example | Since |
|:-------------|:----------|:------------|:--------|:------|
| `SJQ4_TYPE` | `Airing` | The type of object the data refers to |  | 4.0.0.0 |
| `SJQ4_ID`  | _Integer_ | The unique airing id of the object | `123400` | 4.0.0.0 |
| `SJQ4_TITLE`    | _String_  | The title of the airing object | `Family Guy` | 4.0.0.0 |
| `SJQ4_EPISODE`  | _String_  | The episode name of the airing | `Stewie Loves Lois` | 4.0.0.0|
| `SJQ4_START`    | _Long_    | The start time of the airing   | `1234567890` | 4.0.0.0 |
| `SJQ4_END`      | _Long_    | The end time of the airing     | `1234567890` | 4.0.0.0 |
| `SJQ4_CHAN_NAME` | _String_  | The channel name the airing is on | `KCPQDT` | 4.0.0.0 |
| `SJQ4_CHAN_NUM`  | **_String_** | The channel number the airing is on | `291` | 4.0.0.0 |

To access more data from an Airing object in the SJQ ScriptEnv, simply access the object directly.

```
Object airing = AiringAPI.GetAiringForID(SJQ4_METADATA.get("SJQ4_ID").toInteger())
println AiringAPI.GetExtraAiringDetails(airing)
```

## Show ##

| **Variable** | **Value** | Description | Example | Since |
|:-------------|:----------|:------------|:--------|:------|
| `SJQ4_TYPE` | `Show` | The type of object the data refers to |  | 4.0.0.0 |
| `SJQ4_ID`  | _String_ | The unique show id of the object | `EP012345678` | 4.0.0.0 |
| `SJQ4_TITLE`    | _String_  | The title of the airing object | `Family Guy` | 4.0.0.0 |
| `SJQ4_EPISODE`  | _String_  | The episode name of the airing | `Stewie Loves Lois` | 4.0.0.0|
| `SJQ4_CATEGORY`    | _String_    | The show's category   | `Comedy` | 4.0.0.0 |
| `SJQ4_SUBCATEGORY`      | _String_    | The show's subcategory     | `Animated` | 4.0.0.0 |

To access more data from a Show object in the SJQ ScriptEnv, simply access the object directly.

```
Object show = ShowAPI.GetShowForExternalID(SJQ4_METADATA.get("SJQ4_ID"))
println ShowAPI.GetPeopleInShow(show)
```

## Channel ##

| **Variable** | **Value** | Description | Example | Since |
|:-------------|:----------|:------------|:--------|:------|
| `SJQ4_TYPE` | `Channel` | The type of object the data refers to |  | 4.0.0.0 |
| `SJQ4_ID`  | _Integer_ | The channel id | `1234` | 4.0.0.0 |
| `SJQ4_NAME` | _String_ | The channel name | `TSN2HD` | 4.0.0.0 |
| `SJQ4_NUM`    | _String_  | The channel number | `269` | 4.0.0.0 |
| `SJQ4_NETWORK`  | _String_  | The channel's network | `Specialty cable channel` | 4.0.0.0|
| `SJQ4_DESC`    | _String_    | The channel's description   | `TSN2 (HD)` | 4.0.0.0 |

To access more data from a Channel object on the SJQ ScriptEnv, simply access the object directly.

```
Object channel = ChannelAPI.GetChannelForStationID(SJQ4_METADATA.get("SJQ4_ID").toInteger())
println ChannelAPI.IsChannelViewable(channel)
```

## MediaFile ##

| **Variable** | **Value** | Description | Example | Since |
|:-------------|:----------|:------------|:--------|:------|
| `SJQ4_TYPE` | `MediaFile` | The type of object the data refers to |  | 4.0.0.0 |
| `SJQ4_ID`  | _Integer_ | The unique id of the object | `123400` | 4.0.0.0 |
| `SJQ4_MAX_SEGMENT_INDEX`    | _Integer_  | The number of segments in this media file - 1; so a media file with one segment would have 0 for this value | `0` | 4.0.0.0 |
| `SJQ4_PATH`  | _String_  | The directory where this media file is stored | `D:\tv` | 4.0.0.0|
| `SJQ4_LAST_SEGMENT`    | _String_    | The file name of the last segment of this media file; the last segment **always** refers to the file being written to for files that are actively recording   | `FamilyGuy-149325-0.ts` | 4.0.0.0 |
| `SJQ4_LAST_SEGMENT_TYPE` | _String_ | The file type of the last segment (i.e. the file extension) | `ts` | 4.0.0.1342 |
| `SJQ4_LAST_SEGMENT_NAME` | _String_ | The file name, less the file extension | `FamilyGuy-149325-2` | 4.0.0.1342 |
| `SJQ4_SEGMENT_0`      | _String_    | The file name of the first segment of this recording; if there is more than one segment for this media file then there will be additional entries in the map (`SJQ4_SEGMENT_1`, `SJQ4_SEGMENT_2`, etc.)     | `FamilyGuy-149325-2.ts` | 4.0.0.0 |
| `SJQ4_SEGMENT_0_TYPE` | _String_ | The file extension only for the given segment; if there is more than one segment for this media file there will be additonal entries in the map | `ts` | 4.0.0.1342 |
| `SJQ4_SEGMENT_0_NAME` | _String_ | The file name of the segment, less the file extension | `FamilyGuy-149325-2` | 4.0.0.1342 |

To access more data from a MediaFile object in the SJQ ScriptEnv, simply access the object directly.

```
Object mediaFile = MediaFileAPI.GetMediaFileForID(SJQ4_METADATA.get("SJQ4_ID").toInteger())
println MediaFileAPI.GetSize(mediaFile)
```