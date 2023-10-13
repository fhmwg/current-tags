---

title: FHMWG Recommendation 1

author:

  - Luther Tychonievich
  - Gordon Clarke
  - Peter Krogh
  - Maureen Taylor
  - Robert Friedman
  - Christopher Desmond
  - Jeff Looman
  - James Tanner
  - Nancy Desmond
  - Michael Felts
  - Russell Lynch

...


# Contents


1. [Overarching Principles](#1)
1. [Metadata to Write](#2)
    1. [Title](#2.1)
    1. [Description](#2.2)
    1. [Date](#2.3)
    1. [Location](#2.4)
    1. [People Names](#2.5)
    1. [People Faces](#2.6)
1. [Additional Considerations](#3)
    1. [Field-specific recommendations](#3.1)
        1. [Album](#3.1.1)
        1. [Caption](#3.1.2)
        1. [Date](#3.1.3)
        1. [Event](#3.1.4)
        1. [Location](#3.1.5)
        1. [Person and Object](#3.1.6)
    1. [Handling Unicode and Languages](#3.2)
    1. [XML namespaces](#3.3)
    1. [Treating XMP as RDF/XML](#3.4)
    1. [Length Limits](#3.5)
    1. [Archival quality](#3.6)

# 1. Overarching Principles   <a name="1"></a>

All FHMWG recommendations relate to reading and writing [XMP-encoded metadata](https://www.adobe.com/devnet/xmp.html).
Within XMP, many FHMWG recommendations are aligned with the [IPTC standard](https://iptc.org/standards/photo-metadata/iptc-standard/).

## 1.1. Datatypes   <a name="1.1"></a>

The following datatypes are used to represent values:

- **AltLang**: a set of language-tagged translations of the same text.

    Language tags are defined by the IETF in [BPC 47](https://tools.ietf.org/rfc/bcp/bcp47.txt).
    
    The default language to display when a locale-specific language is not available is defined in two ways by XMP.
    Quoting from [the XMP specification part 1, pages 22 and 23](https://wwwimages2.adobe.com/content/dam/acom/en/devnet/xmp/pdfs/XMP%20SDK%20Release%20cc-2016-08/XMPSpecificationPart1.pdf#G5.860493):
    
    > A default value, if known, should be the first array item. The order of other array items is not specified by this document.
    >
    > An xml:lang value of "x-default" may be used to explicitly denote a default item. If used, the "x-default" item shall be first in the array and its simple text value should be repeated in another item in which xml:lang specifies its actual language. However, an "x-default" item may be the only item, in which case there is only a default value in no defined language.
    
    Notably, XMP does not use the BCP 47 standard `i-default` language subtag. It is recommended that every AltLang have an `x-default` entry. 
    
    Two AltLang variants are used:

    - **AltLang line**: may be whitespace-normalized
    - **AltLang block**: may not be whitespace-normalized

- **Line string**: a string with no defined language. May be whitespace-normalized.

- **IRI**: An Internationalized Resource Identifier, as defined by the IETC in [RFC 3987](https://tools.ietf.org/html/rfc3987).

- **Number**: An integer or decimal number in base 10 using U+002E (`.`, the period or full stop) as the floating point with no thousands-separators, as defined in [XMP Part 1 §8.2.1.4](https://wwwimages2.adobe.com/content/dam/acom/en/devnet/xmp/pdfs/XMP%20SDK%20Release%20cc-2016-08/XMPSpecificationPart1.pdf#G5.869673).

- **DateTime**: A date in the proleptic Gregorian calendar, optionally with time in a 24-hour clock, conforming to the subset of ISO 8601 format specified by the W3C in [NOTE-datetime](https://www.w3.org/TR/NOTE-datetime).

- **Closed set**: One of a fixed set of options, given in the specification.

Whitespace normalization discards any leading or trailing whitespace, and replaces any other whitespace with a single space (U+0020) character, as specified by the W3C in [XML 1.1](https://www.w3.org/TR/xml11/).
Whitespace in this context is a consecutive sequence of one or more characters from the set {tab (U+0009), newline (U+000A), carriage return (U+000D), space (U+0020)}.

## 1.2. Do not remove metadata   <a name="1.2"></a>

An application shall not remove a metadata field simply because it does not understand the meaning of the field.
All metadata, understood or not, shall be preserved unless it violates an application's code of conduct.

Applications must not edit provided metadata except under the following three cases:

1. User-moderated editing is permitted, including
    1. user-initiated edits and
    2. application-initiated edits presented to the user for approval.
2. Converting non-IRI values to unicode normal forms C or D.
3. Other cases specifically permitted for a particular field or datatype.

Applications may remove a metadata field entirely if it violates an application's code of conduct and no edit is permitted under the three cases above.
Note that removing a field is distinct from editing it to be the empty string: removing a field means removing its XMP element or EXIF key:value pair entirely.

## 1.3. Selective Implementation   <a name="1.3"></a>

If

- a user provides additional metadata about an image, and
- the metadata provided is of a type covered in this recommendation, and
- the user does not request that the metadata be kept private

then a compliant application shall embed the metadata in the image upon export as specified in this document.

It is not necessary for an application to handle all of the metadata discussed in this recommendation to be considered compliant with these recommendations.
An application that does not handle metadata at all, neither accepting nor removing it, is trivially compliant.
An application that only tags the names of individuals in a photo and has no other metadata is compliant if it embeds that metadata according to the Person section and does not otherwise edit or remove metadata.
Etc.

## 1.4. Going beyond this spec   <a name="1.4"></a>

Applications may implement metadata beyond the scope of this recommendation, either by implementing other metadata standards or by designing their own metadata system.
Note, however, that metadata that is covered by these recommendations must be embedded according to them, even if it is also embedded in additional ways.

# 2. Metadata to Write   <a name="2"></a>

This section uses the following prefixes:

| Prefix        | URI                                                        |
| :------------ | :--------------------------------------------------------- |
| `dc`          | `http://purl.org/dc/elements/1.1/`                         |
| `exif`        | `http://ns.adobe.com/exif/1.0/`                            |
| `Iptc4xmpExt` | `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`              |
| `mwg-rs`      | `http://www.metadataworkinggroup.com/schemas/regions/`     |
| `photoshop`   | `http://ns.adobe.com/photoshop/1.0/`                       |
| `rdf`         | `http://www.w3.org/1999/02/22-rdf-syntax-ns#`              |
| `stArea`      | `http://ns.adobe.com/xmp/sTypeArea#`                       |

## 2.1. Title   <a name="2.1"></a>

### 2.1.1. Summary   <a name="2.1.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpCore:Title` | `dc:title` | Image title |

### 2.1.2. Details   <a name="2.1.2"></a>

Title is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `dc:title`
- which shall contain 1 `rdf:Alt`
- which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
- each of which shall contain a human-readable name for identifying this image


## 2.2. Description   <a name="2.2"></a>

### 2.2.1. Summary   <a name="2.2.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpCore:Description` | `dc:description`| Image description |

### 2.1.2. Details   <a name="2.1.2"></a>

Image description is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `dc:description`
- which shall contain 1 `rdf:Alt`
- which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
- each of which shall contain a free-text content in the given human language

## 2.3. Date   <a name="2.3"></a>

### 2.3.1. Summary   <a name="2.3.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpCore:DateCreated` | `photoshop:DateCreated` | Date of depicted scene |

### 2.3.2. Details   <a name="2.3.2"></a>

Date is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `photoshop:DateCreated`
- which shall contain a date or date-time string conforming to the subset of ISO 8601 format specified in <https://www.w3.org/TR/NOTE-datetime>

## 2.4. Location   <a name="2.5"></a>

### 2.4.1. Summary   <a name="2.5.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:LocationShown` | `rdf:Bag` | One\* location |
| `exif:GPSLatitude` | Number | Degrees north of equator of one location |
| `exif:GPSLongitude` | Number | Degrees east of prime meridian of one location  |
| `Iptc4xmpExt:LocationName` | AltLang block | Full name of one location |
| `Iptc4xmpExt:LocationId` | IRI | Identifier of one location |

\* IPTC allows more than one location in an image, but the FHMWG recommends only one be used

### 2.4.2. Details   <a name="2.5.2"></a>

Location shown is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `Iptc4xmpExt:LocationShown`
- which shall contain 1 `rdf:Bag`
- which shall contain 0 or more `rdf:li` with `rdf:parseType="Resource"`
    - However, support for more than one `rdf:li` in this `rdf:Bag` is optional; implementations *should* create a single primary location where possible, but *must not* remove additional locations without user input.
- each of which shall contain at least one of
    - 1 `exif:GPSLongitude` and ` `exif:GPSLatitude`, each containing a number
    - 1 `Iptc4xmpExt:LocationName`
        - which shall contain 1 `rdf:Alt`
        - which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
        - each of which shall contain a free-text content in the given human language, which *shall* be a description of the depicted location.
    - 1 or more `Iptc4xmpExt:LocationId` with a valid IRI as its content



### 2.5. Person Name   <a name="2.5"></a>

#### 2.5.1. Summary   <a name="2.5.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:PersonInImage` | `rdf:Bag` of Line Strings | list of names of persons in image |

#### 2.5.2. Details   <a name="2.5.2"></a>

Person name is encoded as 

- The top-level `rdf:Description`
- shall contain 0 or 1 `Iptc4xmpExt:PersonInImage`
- which shall contain 1 `rdf:Bag`
- which shall contain 1 or more `rdf:li`
- each of which shall contain the name of one person in the image

Note that `Iptc4xmpExt:PersonInImage` does not support language tags or AltLangs.


### 2.6. Person Face   <a name="2.6"></a>

#### 2.6.1. Summary   <a name="2.6.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `mwg-rs:Regions` | nested elements | one RegionList |
| `mwg-rs:RegionList` | `rdf:Bag` | one more more Regions |
| `mwg-rs:Type` | Closed set | FHMWG supports `Face`; mwg-rs also allows `Pet`, `BarCode`, and `Focus` |
| `mwg-rs:Title` | Line string | A short descriptive title of the contents of the region; preferably the name of the person |
| `mwg-rs:Description` | Block string | A longer description of the contents of the region |
| `mwg-rs:Type` | Closed set | FHMWG supports `Face`; mwg-rs also allows `Pet`, `BarCode`, and `Focus` |
| `mwg-rs:Area` | nested elements | a region as a center point and extents |
| `stArea:unit` | Closed set | always the string `normalized` |
| `stArea:x` | Number | Center of region, in relative horizontal units between 0 and 1 |
| `stArea:y` | Number | Center of region, in relative vertical units between 0 and 1 |
| `stArea:w` | Number | Width of region, in relative horizontal units between 0 and 1 |
| `stArea:h` | Number | Height of region, in relative vertical units between 0 and 1 |
| `stArea:d` | Number | Diameter of region, in the smaller of the units of `stArea:w` and `stArea:h` |


#### 2.6.2. Details   <a name="2.6.2"></a>

Face regions are encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `mwg-rs:Regions`
- which shall contain 1 `mwg-rs:RegionList`
- which shall contain 1 `rdf:Bag`
- which shall contain 1 or more `rdf:li` with `rdf:parseType="Resource"`
- which shall contain all of the following:
    - `mwg-rs:Type` with line string value "`Face`"
    - `mwg-rs:Title` with line string value naming the person in this region
    - `mwg-rs:Area`, which shall contain
        - `stArea:unit` with value "`normalized`"
        - `stArea:x` with value of a number between 0 and 1; this represents the center of the area, with 0 as the left edge of the image and 1 as the right edge
        - `stArea:y` with value of a number between 0 and 1; this represents the center of the area, with 0 as the top edge of the image and 1 as the bottom edge
        - optionally, one of
            - `stArea:d`, the diameter of a square or circular region, given in the smaller of the units of `stArea:x` and `stArea:y`
            - both `stArea:w` and `stArea:h`, the width and height (respectively) of a rectangular or elliptical region, with `w` using the same coordiantes as `x` and `h` using the same coordinates as `y`
    - optionally, `mwg-rs:Description` with a block string value giving a longer description of the person than does `mwg-rs:Title`

Note that `mwg-rs:Title` and `mwg-rs:Description` do not support language tags or AltLangs.

Although Person Face can store all the information that [Person Name](#2.5) stores,
[Person Name](#2.5) is more widely supported;
thus, applications should copy any `mwg-rs:Title` into a `Iptc4xmpExt:PersonInImage`.





# 3. Additional Considerations   <a name="3"></a>

## 3.1. Field-specific recommendations   <a name="3.1"></a>

### 3.1.1. Title   <a name="3.1.1"></a>

The title field is intended to be short and displayable as a line or two of text in most user interfaces. Longer information should be placed in the description field instead. If the title is longer than an application displays, the application may display a prefix of the title with an indicator that the title has been truncated for display. Note this truncation is for display only: implementations must not truncate existing longer titles upon export.

#### 3.1.2.1. Other metadata of interest   <a name="3.1.2.1"></a>

If the `dc:title` is not present, it is recommended that the following be consulted instead:

1. XMP `photoshop:Headline`
2. EXIF ImageDescription (tag 270/0x10E)

#### 3.1.2.2. Example   <a name="3.1.2.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description xmlns:dc='http://purl.org/dc/elements/1.1/'>
  <dc:title>
   <rdf:Alt>
    <rdf:li xml:lang='x-default'>Judy's Rabbit</rdf:li>
   </rdf:Alt>
  </dc:title>
 </rdf:Description>
</rdf:RDF>
```


### 3.1.2. Description   <a name="3.1.2"></a>

FHMWG-recommended description may containing any information the caption-writer wishes to add. The intent is for this to capture any metadata that other structured metadata fields cannot.
It does not store structured information

Where possible, implementations should encourage placing information in other metadata fields.

It is common for descriptions to replicate some information included elsewhere in the metadata;
for example, peopled portrayed in the image may be identified in person meatadata and also in the description so that the description may discuss the relationships between the people or the like.
This inevitably leads to the possibility of conflicting information.
Because it is not possible to programmatically determine which metadata is most accurate, implementations should display all metadata to the user and not attempt to perform automated resolution of conflicts between captions and other metadata.

#### 3.1.2.1. Other metadata of interest   <a name="3.1.2.1"></a>

If the `dc:descrioption` is not present, it is recommended that the following be consulted instead:

1. EXIF UserComment (tag 0x9286)

IPTC also defines `photoshop:CaptionWriter`, which may be useful for applications that wish to record who authored a description. Note, however, that `photoshop:CaptionWriter` is limited to a single name. The FHMWG is not aware of any existing metadata suitable for storing the contributions of multiple metadata authors and editors.

#### 3.1.2.2. Example   <a name="3.1.2.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description xmlns:dc='http://purl.org/dc/elements/1.1/'>
  <dc:description>
   <rdf:Alt>
    <rdf:li xml:lang='x-default'>My aunt Judy's pet rabbit</rdf:li>
    <rdf:li xml:lang='en'>My aunt Judy's pet rabbit</rdf:li>
   </rdf:Alt>
  </dc:description>
 </rdf:Description>
</rdf:RDF>
```

#### 3.1.2.3. Future extensions   <a name="3.1.2.3"></a>

There is a known desire to store the following captions types separately:

- caption as written on image
- printed caption accompanying image
- caption supplied by user of digital tool

There is a known desire to store the authorship and edit dates of all captions. `photoshop:CaptionWriter` may provide a partial solution to this.

There is a known desire to include style markup in captions. Embedded HTML may be a solution to this, but is not directly permitted as part of XMP and raises issues about validation and markup-unaware implementations.

There is a known desire to include links between portions of captions and other metadata fields. RDFa and xlink may provide a solution to this, but are quite heavy-handed.



### 3.1.3. Date   <a name="3.1.3"></a>

FHMWG-recommended date metadata stores the date of the depicted scene.
Various other dates are sometimes present in metadata, but are of less interest from a family history perspective. These include

- Creation date of digital file -- the correct date if this was a direct-to-digital photograph, but not in most other cases
- Modification date of digital file
- Copyright date
- Date burned on image by camera -- the correct date if this is a photograph and the camera's clock was set correctly
- Creation date of the depiction -- the correct date for a photograph or portrait, but not for a tapestry or illustration

User interfaces should make clear that this is not an image modification date: it is the date of the depicted scene.

Modifications to the file, including users changing pixel values, are generally not enough to change the `photoshop:DateCreated` unless they fundamentally change what scene is being depicted.

XMP recommends recording times using timezone offsets, as that provides more information than converting to UTC.

IPTC recommends truncating any unavailable information; for example, if the depicted scene occurred sometime in April 1830, use `1830-04` not `1830-04-15` or the like.

#### 3.1.3.1. Other metadata of interest   <a name="3.1.3.1"></a>

The following metadata are defined to store the following dates.
Note that many user interfaces list these simply as "date" so the accuracy of these definitions may vary from one image to another.
These are listed in approximate order from most likely to correctly represent the date of the depicted scene to least likely.

| Standard    | Tag                 | Date type                         |
|-------------|---------------------|-----------------------------------|
| IPTC IIM    | 2:55 and 2:60       | Date of depicted scene            |
| EXIF        | DateTimeOriginal    | Creation of first image           |
| Dublin Core | date                | A date associated with the media  |
| EXIF        | DateTimeDigitized   | Creation of first digital file    |
| IPTC IIM    | 2:62 and 2:63       | Creation of first digital file    |
| XMP         | CreateDate          | Creation of digital file          |
| EXIF        | DateTime            | Modification date digital file    |
| XMP         | ModifyDate          | Modification date digital file    |
| XMP         | MetadataDate        | Modification date of metadata     |

If `photoshop:DateCreated` metadata field is absent but an alternative date field is present, it is recommended that the first available entry in the above list be use as a default value (converting from the EXIF format to the ISO 8601 format if appropriate). It is recommended that the user be asked for confirmation before entering this metadata into `photoshop:DateCreated`.

Many other dates may also be present in metadata, such as copyright date, date of licensing, publication date, date of particular modification action, etc.

The IPTC IIM tags 2:55 (Date Created) and 2:60 (Time Created) were superseded by the current IPTC `photoshop:DateCreated`.
If both an IIM field and `photoshop:DateCreated` are present, `photoshop:DateCreated` is most likely to be correct.

#### 3.1.3.2. Example   <a name="3.1.3.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description xmlns:photoshop='http://ns.adobe.com/photoshop/1.0/'>
  <photoshop:DateCreated>2020-07-16T08:28:17-04:00</photoshop:DateCreated>
 </rdf:Description>
</rdf:RDF>
```

#### 3.1.3.3. Future extensions   <a name="3.1.3.3"></a>

There is a known desire to store date information that XMP's subset of ISO 8601 cannot:

- approximate dates
- date ranges (for depictions of events that covered multiple days)
- dates before 0001-01-01
- dates with time information but without time zone information



### 3.1.4. Location   <a name="3.1.4"></a>

FHMWG-recommended location metadata stores GPS coordinates of a characteristic point within the depicted scene and a textual description of the location of the depicted scene.
It does not store

- GPS precision
- GPS regions
- Jurisdiction-name-based locations
- A distinction between current and historical location

IPTC clarifies that latitude and longitude are defined per WGS 84 (also called WGS 1984 or EPSG:4326), meaning positive latitudes are north of the equator and positive longitudes are east of the meridian.

Place names generally fit into some kind of jurisdictional hierarchy, but the levels and their names vary by region and change over time. It is recommended that the full place name be given, in smallest to largest region order, with jurisdiction titles and the date at which the names applied, in the free-text "`LocationName`". Specific metadata fields for some jurisdiction types (such as "city" and "state") are provided in some metadata standards, but are inadequate for the general case so the `LocationName` should be used even if those other fields are populated.

Applications should encourage users to use the location description that existed at the time of the depicted event.

IPTC allows image metadata to include more than one location per image.
The FHMWG recommends storing only a single location, which should be the location of the most important depicted elements (generally of the people in the foreground).

GPS coordinates fail to capture that locations may be regions, not points. For example, a picture of the city of Dubai might be coded as being a point in Dubai such as 25.2047397, 55.2707065, even if that specific point (where Al Safa Street crosses over Sheikh Zayed Road) is not visible in the image.

GPS coordinates fail to capture that locations may be approximate. For example, a picture of a headstone in the Sleepy Hollow cemetery in Sleepy Hollow New York may be coded as 41.089715, -73.862005 (the main entrance to the cemetery) if the coordinates of the specific headstone is not known.

The coordinates of a conceptual location change over time by nature of plate tectonics. For example, an earthquake on 11 March 2011 moved portions of Japan 2.4 meters (about 0.000025° longitude) and regions of the sea floor ten times that far. It is generally considered best practice to represent current, not historical, coordinates when possible.

The number of digits provided for a coordinate should not be taken to imply accuracy or region size.

Although IPTC unambiguously defines latitude and longitude to store decimal numbers, several tools instead store them as numbers followed by a letter: `N` or `S` for latitude, `E` or `W` for longitude.
If `N` or `E` is present, it should be discarded; if `S` or `W` is present, it should be removed and the sign of the number flipped.


#### 3.1.4.1. Other metadata of interest   <a name="3.1.4.1"></a>

It is recommended that missing location data be filled in the following order if `Iptc4xmpExt:LocationShown`'s GPS coordinates are missing:

1. geocode `Iptc4xmpExt:LocationShown`'s address components
2. geocode IPTC legacy location fields (`photoshop:City` etc)
3. `Iptc4xmpExt:LocationCreated`'s GPS coordinates
4. geocode `Iptc4xmpExt:LocationCreated`'s address components
5. geocode IPTC IIM address (2:90 city, 2:92 sub-location, 2:95 province/state, etc)
6. EXIF's `GPSLatitude` and `GPSLongitude`, as modified by `GPSLatitudeRef` and `GPSLongitudeRef`

It is recommended that EXIF's GPS coordinates be updated any time the `LocationShown` is updated so that tools reading EXIF will correctly locate the image.

IPTC has many more location parts, including altitude and various region-specific jurisdiction names.

#### 3.1.4.2. Example   <a name="3.1.4.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description
    xmlns:Iptc4xmpExt='http://iptc.org/std/Iptc4xmpExt/2008-02-29/'
    xmlns:exif='http://ns.adobe.com/exif/1.0/'>
  <Iptc4xmpExt:LocationShown>
   <rdf:Bag>
    <rdf:li rdf:parseType='Resource'>
     <Iptc4xmpExt:LocationName>
      <rdf:Alt>
       <rdf:li xml:lang='x-default'>Salt Lake City (city), Utah (state), USA (nation) as of 2020-07-24</rdf:li>
      </rdf:Alt>
     </Iptc4xmpExt:LocationName>
     <exif:GPSLatitude>40.7596198</exif:GPSLatitude>
     <exif:GPSLongitude>-111.8867975</exif:GPSLongitude>
    </rdf:li>
   </rdf:Bag>
  </Iptc4xmpExt:LocationShown>
 </rdf:Description>
</rdf:RDF>
```

#### 3.1.4.3. Future extensions   <a name="3.1.4.3"></a>

There is a known desire to store location information that IPTC cannot:

- approximate coordinates, with estimates of how approximate they are
- coordinate regions

IPTC does allow location data inside image regions. The complexity of these was deemed too involved for this release, but may be recommended in a future release.

There is a known desire to add structured but versatile place name metadata; an example spec might look like

- an ordered list, from smallest to largest, of
- component names, each containing
- the name (e.g. "New York") and the name of the type of region being named (e.g. "City")
- ... all coupled with an "as of" date

There are multiple other proposals for such historical place hierarchies under consideration.



### 3.1.5. Person Name   <a name="3.1.5"></a>

FHMWG-recommended person name metadata stores people directly depicted in the media; people directly depicted in the media; and people not depicted by who contributed to or were otherwise relevant to the depiction (e.g., a witness of the event such as the photographer, painter, drafter, etc.)
It does not store other people involved in the provenance chain (digitizer, editor, metadata author, etc).

Implementations should assume that each entry is independent and valid unless a user makes a different decision.
Even having multiple regions with the same name is generally permitted as a person may appear in multiple regions of an image (e.g., in a mirror, by having something else occlude part of them, by signing the document in multiple places, etc)
or multiple people may have the same name.

Person Face also stores the name of a person. Because Person Name is more widely supported, it is recommended that applications copy any `mwg-rs:Title` into a `Iptc4xmpExt:PersonInImage`.


#### 3.1.5.1. Other metadata of interest   <a name="3.1.5.1"></a>

IPTC also allows a `Iptc4xmpExt:PersonInImageWDetails` which has much more detail about each person, not just a single name.

Information about people not depicted can be found in many other metadata fields, including but not limited to

- XMP `dc:creator`
- IPTC IIM 2:80 By-Line
- XMP `plus:ImageCreator`
- EXIF 0x13B "Artist"

How and whether to reading these is left up to individual implementations.

#### 3.1.5.2. Example   <a name="3.1.5.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description rdf:about=''
  xmlns:Iptc4xmpExt='http://iptc.org/std/Iptc4xmpExt/2008-02-29/'>
  <Iptc4xmpExt:PersonInImage>
   <rdf:Bag>
    <rdf:li>Gordon Clarke</rdf:li>
    <rdf:li>クラーク・ゴードン</rdf:li>
   </rdf:Bag>
  </Iptc4xmpExt:PersonInImage>
 </rdf:Description>
</rdf:RDF>
```

#### 3.1.5.3. Future extensions   <a name="3.1.5.3"></a>

There is a known desire to indicate name parts (given, preferred, family, patronymic, matronymic, honorific, etc).

There is a known desire to indicate relationships between people, familial and otherwise.


### 3.1.6. Person Face   <a name="3.1.6"></a>

Person Face is only for persons depicted in the image. A person that is not in the depiction but is part of the original depiction event, such as a photographer or painter, should use the Person Name instead.

Person Face may be used to mark persons who are visible in the image, but whose faces are not visible. In that case, the region should indicate the most identifiable part of the person.

Because regions are given in relative units to the size of the image, they need to be updated if the image is cropped or padded but do not need to be updated if the image is scaled or resized.

Regions may also extend off the edge of the image; if so, they should be cropped to the image bounds.

Multiple regions may overlap.

Regions do not distinguish between circular and square regions, nor between rectangular and elliptical regions. Each application may decide which to use in displaying regions to the user.

Person Face also stores the name of a person. Because Person Name is more widely supported, it is recommended that applications copy any `mwg-rs:Title` into a `Iptc4xmpExt:PersonInImage`.


#### 3.1.6.1. Other metadata of interest   <a name="3.1.6.1"></a>

IPTC has a separate region specification, `Iptc4xmpExt:ImageRegion`. This is both more flexible and less widely supported.

#### 3.1.6.2. Example   <a name="3.1.6.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description rdf:about=''
  xmlns:mwg-rs='http://www.metadataworkinggroup.com/schemas/regions/'
  xmlns:stArea='http://ns.adobe.com/sType/Area#'>
  <mwg-rs:Regions rdf:parseType=“Resource”>
   <mwg-rs:RegionList>
    <rdf:Bag>
     <rdf:li rdf:parseType=“Resource”>
      <mwg-rs:Area stArea:x=“0.4” stArea:y=“0.3” stArea:w=“0.06” stArea:h=“0.09” stArea:unit=“normalized”/>
      <mwg-rs:Type>Face</mwg-rs:Type>
      <mwg-rs:Title>Chris Desmond</mwg-rs:Title>
     </rdf:li>
     <rdf:li rdf:parseType=“Resource”>
      <mwg-rs:Area stArea:x=“0.6” stArea:y=“0.4” stArea:d=“0.1” stArea:unit=“normalized”/>
      <mwg-rs:Type>Face</mwg-rs:Type>
      <mwg-rs:Title>Maureen Taylor</mwg-rs:Title>
     </rdf:li>
    </rdf:Bag>
   </mwg-rs:RegionList>
  </mwg-rs:Regions>
 </rdf:Description>
</rdf:RDF>
```

#### 3.1.6.3. Future extensions   <a name="3.1.6.3"></a>

There is a known desire to distinguish between "not depicted" and "depicted at an unknown location".





## 3.2. Handling Unicode and Languages   <a name="3.2"></a>

All XMP data is written in Unicode, and use of Unicode-aware programming APIs are recommended.
At a minimum, it is important to know that

- Not all characters occupy the same number of bytes
- Not all glyphs occupy the same number of characters
- Not all identical glyphs have identical character sequences
- Not all adjacent editor cursor positions are one character apart
- Not all parts of text flow in the same direction
- Not all text is properly sorted in code-point order

See [unicode.org](https://home.unicode.org) for more.

All human-readable IPTC strings use AltLang structures to provide a set of alternative presentations in different languages.
Languages are expressed by language tags, as defined in [BCP 47](https://tools.ietf.org/rfc/bcp/bcp47.txt),
which consist of one or more language subtags, as defined in the [IANA Language Subtag Registry](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry),
and can indicate many things about text including language, script, dialect, and more.
The language subtag registry is frequently updated with new languages, but maintained in a way that ensures old tags never lose their meaning.

The quasi-language-tag `x-default` used by XMP

- is optional
- if present, must be the first alternative in the AltLang
- if present, is recommended to have an alternative with the same payload
- is semantically equivalent to the standard tag `und-i-default`: an undetermined language to be shown by default
- is the only tag edited by some generic metadata editing programs

Because of the last point above, it is recommended that `x-default` be used in evert AltLang.

As with other metadata, a conformant application must not discard or edit language-tagged data in an AltLang simply because the language is unknown to the application.
Users should be asked to indicate the language in which they are working;
if they do not do so, user-edited values should be given the `x-default` language tag.

It is an error to include the same language tag in an AltLang more than once.

There is no known way to indicate one value as the original language and others as translations thereof.


## 3.3. XML namespaces   <a name="3.3"></a>

XMP is encoded as RDF/XML, and XML uses namespaces to name its elements.
Namespace prefixes used are given in the XML, and are not semanitcally significant.
Thus the following two XMP datasets are semantically identical:

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description xmlns:photoshop='http://ns.adobe.com/photoshop/1.0/'>
  <photoshop:DateCreated>2020-07-16T08:28:17-04:00</photoshop:DateCreated>
 </rdf:Description>
</rdf:RDF>
```

```xml
<RDF xmlns='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <Description xmlns:p='http://ns.adobe.com/p/1.0/'>
  <p:DateCreated>2020-07-16T08:28:17-04:00</p:DateCreated>
 </Description>
</RDF>
```

Despite this, incomplete XML parsers are common that treat namespaces as significant and would fail to parse the second of the above examples.
To maximize compatibility with such systems,
it is recommended to always export data using the default namespaces given in the beginning of [Metadata to Write](#2).

## 3.4. Treating XMP as RDF/XML   <a name="3.4"></a>

XMP is officially [RDF/XML](https://www.w3.org/TR/rdf-syntax-grammar/): that is, RDF expressed in XML.
RDF is a very flexible data format, including the ability to encode cyclic graphs and other data that cannot be trivially represented in nested structures.
However, all common XMP vocabularies use tree-structured data that can be represented as nested XML without identifiers and pointers.

Despite this pointer-free simplicity of XMP compared to general RDF/XML, it is unfortunately the case that each of the major XMP processing toolchains uses a slightly different representation of the XMP when writing to a file.
For example,

- some will split the data into several different `rdf:RDF` elements, while others will merge them;
- some will represent an element with `rdf:parseType='Resource'` as an element without that attribute containing a nested `rdf:Description` element;
- some will convert text-valued sub-elements of an `rdf:Description` element into attributes of that element instead

These and other variations in representation are all allowed by RDF/XML, and thus by XMP, without change in the meaning of metadata being represented. Further, many XMP toolchains do not provide the ability for the user to select the representation variation to use during output.

Because of this, metadata parsers should use a fully-compliant RDF/XML parser.
Metadata producers may bypass this by providing the nested XML described in [Metadata to Write], or may produce any equivalent RDF/XML.
If possible, it is recommended that metdata producers match the structure listed above as closely as their XMP toolset allows;
relevant rules to apply in the RDF/XML specification include "omitting blank nodes" and "omitting nodes."

## 3.5. Length Limits   <a name="3.5"></a>

Neither XMP nor IPTC impose any limits on the length of string payloads, and neither does the FHMWG.
Implementations should be prepared to store and display strings of arbitrary lengths.

In particular, [captions](#2.2) often contain many distinct pieces of information in somewhat verbose prose and may extend into tens of thousands of characters or more.

## 3.6. Archival quality <a name="3.6"></a>

It should be noted that this document permits editing metadata in a destructive way (i.e., without preserving its previous content).
It is thus not an archival standard.
However, it is our intent that an implementation can be compliant with both this recommendation
and with archival standards by not permitting users to modify existing metadata.

There is a known desire to add append-only edit logs or other archival-quality metadata editing.
