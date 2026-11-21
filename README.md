# Customizable, Rich, Supplemental Information Metadata (CRSim)
I woke up one day and decided that Xesam and MPRIS was not enough metadata for me, so i came up with CRSim. (an alternate name for CRSim is ChRiSalIs Metadata)

CRSim was made for [Chrysalis](https://github.com/NicholasJDi/Chrysalis), but it's useful enough that if it becomes known I kind of expect it to be used in other places. (the fact that CRSim's flattened form cannot be accessed with `playerctl --format`  is a massive issue that for now makes it rather unusable)

## The Rules
### Customizable
CRSim has no limitations on what data it provides, but Plugins (sections of CRSim that are not in the Standard) should always use a single Namespace, such as `crsim:example.*`.

### Rich
CRSim is much more flexible than Xesam and MPRIS, as it uses an Object format. (and is intended to be represented as an Object internally)

But using an Object comes with its own problems, which are solved by Reserved Tags. (any Tag starting with a `_` are Reserved by CRSim and are not allowed to be exposed by Programs via the CRSim Object.)

#### `_default`:
`_default` allows you to set a Tag at the same level as an Object.

```JSON
{
  "example":{
    "_default":"tag",
    "test":"tag inside object"
  }
}
```

```
crsim:example 'tag'
crsim:example.test 'tag inside object'
```

#### `_processed`:
`_processed` allows you to set a Tag at the same level as an Object but prefixed with an `_`, `_processed` Tags should not be saved in Files as they are meant to be decided during Runtime.

`_processed` Tags should never be duplicate data, always a reference or identifier. (as to avoid making MPRIS unreadable)

> [!NOTE]
> Data already obtainable via Xesam or MPRIS should not have an `_processed` version. (in accordance with [Supplemental](#supplemental))

```JSON
{
  "example":{
    "_default":"tag",
    "_processed":"processed tag",
    "test":"tag inside object"
  }
}
```

```
crsim:example 'tag'
crsim:example.test 'tag inside object'
crsim:_example 'processed tag'
```

#### `_internal`:
`_internal` means a CRSim Tree is not meant to be saved to Files or shown with MPRIS.

```JSON
{
  "example":{
    "_default":"tag",
    "_processed":"processed tag",
    "secret":{
      "_internal":true,
      "key":"test",
      "_default":"hi",
      "_processed":"hello"
    },
    "test":"tag inside object"
  }
}
```

```
crsim:example 'tag'
crsim:example.test 'tag inside object'
crsim:_example 'processed tag'
```

### Supplemental
CRSim is a Supplemental Ontology for Programs to read from alongside Xesam and MPRIS, it is not meant to replace them. (I originally wanted CRSim to be Static Metadata only, but Xesam has literally NO support for Lyrics or Mixes, and I thought it too taxing to force Programs reading from CRSim to calculate Lyrics, which is why `_processed` exists.)

### Information
CRSim Tags are meant to be Short and Informational, Tag Names are in camelCase, shorter Names are preferred but understandability takes precedence.

> [!NOTE]
> User Generated Tag Names such as Lyric Translation Names should be in PascalCase.

CRSim Values should stay as consistent as possible, but reusing a Tag with an accurate Name in a different context should be preferred over creating a new Tag.

### Metadata
CRSim flattens Arrays into JSON Strings, unfolds Dictionaries into more CRSim Tags, and Converts all other values into Strings.

A Song's CRSim Tags might look like this:

```
crsim:title 'somewhere, someday,'
crsim:album 'Perfer not to say'
crsim:artists '["Tanger"]'
crsim:contributors '["Lorem Ipsum"]'
crsim:type 'album'
crsim:art.artists '["Tanger"]'
crsim:art.override 'file:///home/nicholasjdi/chrysalis/covers/override/Tanger - somewhere, someday, (with lorem ipsum).jpg'
crsim:art.album 'file:///home/nicholasjdi/chrysalis/album/Perfer not to say.png'
crsim:art.artist 'file:///home/nicholasjdi/chrysalis/Tanger.png'
crsim:art.background.artists '["Tanger"]'
crsim:art.background.override 'file:///home/nicholasjdi/chrysalis/backgrounds/override/Tanger - somewhere, someday, (with lorem ipsum).png'
crsim:track '8'
crsim:tracks '8'
crsim:disc '3'
crsim:discs '3'
crsim:bpm '174'
```

#### Definitions
When referring to things in CRSim here's what they mean:
##### Tree
A CRSim Tree is simply an object (`crsim:art` would be a Tree)
##### Array
A CRSim Array is a traversable Array in the form of a Tree (A normal Array is referred as a JSON Array)

```JSON
{
  "mix":{
    "_default":2,
    "1":{
      "title":"title1"
    },
    "2":{
      "title":"title2"
    }
  }
}
```

##### Swap Metadata
Swap Metadata is Metadata that can change slightly depending on what the User wants (this is usually limited to uri's, but can sometimes be needed for other Metadata)

## Plans
- CRSim will probably be changing a lot before [Chrysalis](https://github.com/NicholasJDi/Chrysalis) gets out of Alpha (any changes to CRSim will not affect the version number until Chrysalis reaches Beta)
- I would like to have the CRSim site host extensive data that other sites do not provide, such as proper lyrics, and Metadata would only have one consistent version, unlike MusicBrainz and such (just to make things more simple, it would just be a single CRSim file with all outward links being Swap Metadata)
- I would kind of like CRSim to support any media type, but for now and the foreseeable future CRSim will just be for Music
