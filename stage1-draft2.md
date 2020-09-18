---

title: FHMWG Stage 1 Recommendations

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
    1. [Album](#2.1)
    1. [Caption](#2.2)
    1. [Date](#2.3)
    1. [Event](#2.4)
    1. [Location](#2.5)
    1. [Objects and People](#2.6)
        1. [Image Region](#2.6.1)
        1. [Region Boundaries](#2.6.2)
        1. [Object](#2.6.3)
        1. [Person](#2.6.4)
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

# 1. Overarching Principles   <a name="1"></a>

All FHMWG recommendations relate to reading and writing [XMP-encoded metadata](https://www.adobe.com/devnet/xmp.html).
Within XMP, many FHMWG recommendations are aligned with the [IPTC standard](https://iptc.org/standards/photo-metadata/iptc-standard/).

## 1.1. Datatypes   <a name="1.1"></a>

The following datatypes are used to represent values:

- **AltLang**: a set of language-tagged translations of the same text.

    Language tags are defined by the IETF in [BPC 47](https://tools.ietf.org/rfc/bcp/bcp47.txt).
    The special value `x-default` is used to mean `und-i-default`,
    as introduced in [a Google Webmaster blog post](https://webmasters.googleblog.com/2013/04/x-default-hreflang-for-international-pages.html) and adopted by several XMP toolchains.

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
| `Iptc4xmpExt` | `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`              |
| `mwg-coll`    | `http://www.metadataworkinggroup.com/schemas/collections/` |
| `photoshop`   | `http://ns.adobe.com/photoshop/1.0/`                       |
| `rdf`         | `http://www.w3.org/1999/02/22-rdf-syntax-ns#`              |
| `exif`        | `http://ns.adobe.com/exif/1.0/`                            |

## 2.1. Album   <a name="2.1"></a>

### 2.1.1. Summary   <a name="2.1.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `mwg-coll:Collections` | `rdf:Bag` | Any number of albums |
| `mwg-coll:CollectionName` | Line String | Name of one album |
| `mwg-coll:CollectionURI` | IRI | Identifier of one album |

### 2.1.2. Details   <a name="2.1.2"></a>

Album membership is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `mwg-coll:Collections`
- which shall contain 1 `rdf:Bag`
- which shall contain 0 or more `rdf:li` with `rdf:parseType="Resource"`
- each of which shall contain at least one of
    - 1 `mwg-coll:CollectionName` with a line-text content
    - 1 `mwg-coll:CollectionURI` with a valid IRI as its content

## 2.2. Caption   <a name="2.2"></a>

### 2.2.1. Summary   <a name="2.2.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `dc:description` | AltLang block | Image caption |
| `dc:title` | AltLang line | Image title |

### 2.1.2. Details   <a name="2.1.2"></a>

Image description is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `dc:description`
    - which shall contain 1 `rdf:Alt`
    - which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
    - each of which shall contain a free-text content in the given human language
- and shall contain 0 or 1 `dc:title`
    - which shall contain 1 `rdf:Alt`
    - which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
    - each of which shall contain a human-readable name for identifying this image

## 2.3. Date   <a name="2.3"></a>

### 2.3.1. Summary   <a name="2.3.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `photoshop:DateCreated` | DateTime | Date of depicted scene |

### 2.3.2. Details   <a name="2.3.2"></a>

Date is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `photoshop:DateCreated`
- which shall contain a date or date-time string conforming to the subset of ISO 8601 format specified in <https://www.w3.org/TR/NOTE-datetime>

## 2.4. Event   <a name="2.4"></a>

### 2.4.1. Summary   <a name="2.4.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:Event` | AltLang block | Description of event depicted scene is a part of |

### 2.4.2. Details   <a name="2.4.2"></a>

Events are encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `Iptc4xmpExt:Event`
- which shall contain 1 `rdf:Alt`
- which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
- each of which shall contain a free-text description of the event in the given human language

## 2.5. Location   <a name="2.5"></a>

### 2.5.1. Summary   <a name="2.5.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:LocationShown` | `rdf:Bag` | One\* location |
| `exif:GPSLatitude` | Number | Degrees north of equator of one location |
| `exif:GPSLongitude` | Number | Degrees east of prime meridian of one location  |
| `Iptc4xmpExt:LocationName` | AltLang block | Full name of one location |
| `Iptc4xmpExt:LocationId` | IRI | Identifier of one location |

\* IPTC allows more than one location in an image, but the FHMWG recommends only one be used

### 2.5.2. Details   <a name="2.5.2"></a>

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
    - 1 `Iptc4xmpExt:LocationId` with a valid IRI as its content

## 2.6. Objects and People   <a name="2.6"></a>

Objects and people are stored inside image regions.

### 2.6.1. Image Region   <a name="2.6.1"></a>

#### 2.6.1.1. Summary   <a name="2.6.1.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:ImageRegion` | `rdf:Bag` | Any number of image regions |
| `rdf:li` | *nested XML elements* | One region boundary and one\* person or object |
| `Iptc4xmpExt:RegionBoundary` | *nested XML elements* | A region boundary |
| `Iptc4xmpExt:ArtworkOrObject` | *nested XML elements* | An object |
| `Iptc4xmpExt:PersonInImageWDetails` | *nested XML elements* | A person |

\* IPTC allows arbitrary content in regions, but the FHMWG recommends only one object or person per region

#### 2.6.1.2. Details   <a name="2.6.1.2"></a>

Image regions are encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `Iptc4xmpExt:ImageRegion`
- which shall contain 1 `rdf:Bag`
- which shall contain 1 or more `rdf:li`, each with the `rdf:parseType='Resource'`
- each of which shall contain one `Iptc4xmpExt:RegionBoundary` with `rdf:parseType='Resource'`, and one `Iptc4xmpExt:PersonInImageWDetails` or `Iptc4xmpExt:ArtworkOrObject`

### 2.6.2. Region Boundaries   <a name="2.6.2"></a>

#### 2.6.2.1. Summary   <a name="2.6.2.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:RegionBoundary` | *nested XML elements* | A region boundary |
| `Iptc4xmpExt:rbShape` | Closed set | "`rectangle`" or "`circle`" or "`polygon`" |
| `Iptc4xmpExt:rbUnit` | Closed set | "`relative`"\*
| `rbX` | Number | (from left edge) left of rectangle; center of circle; or coordinate of a vertex |
| `rbY` | Number | (from top edge) top of rectangle; center of circle; or coordinate of a vertex |
| `rbW` | Number | width of rectangle |
| `rbH` | Number | height of rectangle |
| `rbRx` | Number | radius of circle in x-axis coordinates |
| `rbVertices` | `rdf:Seq` | sequence of vertexes of polygon |

\* IPTC also allows "`pixel`", but the FHMWG recommends only "`relative`" be used

#### 2.6.2.2. Details   <a name="2.6.2.2"></a>

The `Iptc4xmpExt:RegionBoundary` shall contain

- one `Iptc4xmpExt:rbShape` with content being one of the following exact strings: "`rectangle`", "`circle`", or "`polygon`"
- one `Iptc4xmpExt:rbUnit` with being one of the following exact strings: "`pixel`" or "`relative`". All applications *should* use `relative`; support for reading `pixel` is *optional*.
- additional fields dependent on shape
    - a `rectangle` has an `rbX`, `rbY`, `rbW`, and `rbH`

        The rectangle's top-left corner is at (`rbX`, `rbY`)
        and it is `rbW` wide and `rbH` tall.
    - a `circle` has `rbX`, `rbY`, and `rbRx`

        The circle's center is at (`rbX`, `rbY`)
        and it is radius is `rbRx`, as measured in the X-axis coordinates.
        Note that for non-square images the area is still a circle, not an ellipse.
    - a `polygon` has a `rbVertices`, which contains
        - an `rdf:Seq`, which containts
        - 3 ore more `rdf:li` with `rdf:parseType='Resource'`, each of which contain
        - an `rbX` and an `rbY`, one of the vertices of the polygon

        IPTC does not define the meaning of a self-intersecting polygonal region, and neither does the FHMWG. Implementations *should* avoid them where possible.

X coordinates are measured with 0 as the left boundary of the image and either 1 (for `relative` units) or image width (for `pixel` units) as the right boundary.
Y coordinates are measured with 0 as the top boundary of the image and either 1 (for `relative` units) or image height (for `pixel` units) as the bottom boundary.

#### 2.6.2.3. Whole-image Region   <a name="2.6.2.3"></a>

A special region boundary is known as the "whole-image region boundary":
a `relative` `rectangle`
with `rbX` = 0, `rbY` = 0, `rbW` = 1, and `rbH` = 1.
This special region shall be used for both of the following:

- persons or objects depicted in the image but whose regions are unknown
- persons or objects directly related to the image but not depicted in it

### 2.6.3. Object   <a name="2.6.3"></a>

#### 2.6.3.1. Summary   <a name="2.6.3.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:ArtworkOrObject` | *nested XML elements* | An object |
| `Iptc4xmpExt:AOTitle` | AltLang block | The short descrition or title of the object |


#### 2.6.3.2. Details   <a name="2.6.3.2"></a>

The `Iptc4xmpExt:ArtworkOrObject` shall contain

- one `Iptc4xmpExt:AOTitle`
- which shall contain 1 `rdf:Alt`
- which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
- each of which shall contain a free-text content in the given human language, which *shall* be the an identifying title for the object in that language.


### 2.6.4. Person   <a name="2.6.4"></a>

#### 2.6.4.1. Summary   <a name="2.6.4.1"></a>

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:PersonInImageWDetails` | *nested XML elements* | A person |
| `Iptc4xmpExt:PersonName` | AltLang line | The name of one person |
| `Iptc4xmpExt:PersonDescription` | AltLang block | The description of one person |
| `Iptc4xmpExt:PersonId` | IRI | An identifier of one person |



#### 2.6.4.2. Details   <a name="2.6.4.2"></a>

The `Iptc4xmpExt:PersonInImageWDetails` shall contain at least one of the following:

- 0 or 1 `Iptc4xmpExt:PersonName`
    - which shall contain 1 `rdf:Alt`
    - which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
    - each of which shall contain a free-text content in the given human language, which *shall* be the persons name in that language.
- 0 or 1 `Iptc4xmpExt:PersonDescription`
    - which shall contain 1 `rdf:Alt`
    - which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
    - each of which shall contain a free-text content in the given human language, which *shall* be a description of the person.
- 0 or more `Iptc4xmpExt:PersonId`, each with a valid IRI as its content








# 3. Additional Considerations   <a name="3"></a>

## 3.1. Field-specific recommendations   <a name="3.1"></a>

### 3.1.1. Album   <a name="3.1.1"></a>

FHMWG-recommended album metadata stores zero or more collections a given image belongs to.
It does not store

- Hierarchies of collections
- Positions within a collection
- Collections with more than one identifier
- Description of collection

"Collection" or "Album" are both suitable names for user interface components describing this metadata.
"Name" and "Title" are suitable terms for the name of an album.
"URL" and "Identifier" are suitable terms for the ID of an album.

If an application believes it has access to all images in an album, it is recommended that an ID for that album be generated.
A recommend path for album identifiers is

- If a `mwg-coll:CollectionURI` was provided on import, use that
- Otherwise, if the implementation or user provides a durable world-readable URL at which to access the album, use that
- Otherwise, use a URL created as a UUID encoded as a URN, as specfiied in <https://tools.ietf.org/html/rfc4122>. Version 4 (random) UUIDs are *recommended*.

If two albums are identified with the same name but distinct URIs, they should be treated as distinct albums.

If two albums are identified with the same URI but distinct names, they should be treated as a single album with multiple names.

An album without a name may be presented using an auto-generated name such as "Untitled Album 3".
Auto-generated names should not be exported as metadata.

An album without a URI is ambiguous as to its identity, and that ambiguity should be displayed to the user in some way. If the user interface needs to guess if two non-URI albums with the same name are the same album or not, it is recommended that they be considered the same only if the (album name, providing user) pairs match.

#### 3.1.1.1. Other metadata of interest   <a name="3.1.1.1"></a>

The FHMWG is unaware of other widely-used album identifiers.

#### 3.1.1.2. Example   <a name="3.1.1.2"></a>

The following represents an image belonging to two albums, one with a URI and one without:

| Album name | Album URI |
|:-----------|:----------|
| My Vacation Photos | `http://www.flickr.com/photos/myvacation` |
| Beautiful Sunset Set | |


````xml
<rdf:RDF xmlns:rdf=“http://www.w3.org/1999/02/22-rdf-syntax-ns#”>
<rdf:Description xmlns:mwg-coll=“http://www.metadataworkinggroup.com/schemas/collections/”>
    <mwg-coll:Collections>
      <rdf:Bag>

<rdf:li rdf:parseType=“Resource”>
<mwg-coll:CollectionName>My Vacation Photos</mwg-coll:CollectionName>
<mwg-coll:CollectionURI>http://www.flickr.com/photos/myvacation</mwg-coll:CollectionURI>
</rdf:li>

<rdf:li rdf:parseType=“Resource”>
<mwg-coll:CollectionName>Beautiful Sunset Set</mwg-coll:CollectionName>
</rdf:li>

      </rdf:Bag>
    </mwg-coll:Collections>
  </rdf:Description>
</rdf:RDF>

````

#### 3.1.1.3. Future extensions   <a name="3.1.1.3"></a>

Albums are the only FHMWG-recommended textual metadata that does not support AltLang.
AltLang support may be added in the future.

There is a known desire to store album descriptions as well as album titles.

There is a known desire to store each image's ordinal within a collection. In addition to requiring a new metadata field, this raises concerns about distinct elements claiming the same position.

There is a known desire to store metadata about a collection, such as creator, purpose, date, etc. This would require the collection itself to be an entity about which metadata can be recorded.


### 3.1.2. Caption   <a name="3.1.2"></a>

FHMWG-recommended caption metadata stores a title and a description, with the later containing any information the caption-writer wishes to add. The intent is for this to capture any metadata that other structured metadata fields cannot.
It does not store structured information

Where possible, implementations should encourage placing information in other metadata fields.

It is common for captions to replicate some information included elsewhere in the metadata;
for example, peopled portrayed in the image may be identified in person meatadata and also in the caption so that the caption may discuss the relationships between the people or the like.
This inevitably leads to the possibility of conflicting information.
Because it is not possible to programmatically determine which metadata is most accurate, implementations should display all metadata to the user and not attempt to perform automated resolution of conflicts between captions and other metadata.

#### 3.1.2.1. Other metadata of interest   <a name="3.1.2.1"></a>

If the `dc:descrioption` is not present, it is recommended that the following be consulted instead:

1. EXIF UserComment (tag 0x9286)

If the `dc:title` is not present, it is recommended that the following be consulted instead:

1. XMP `photoshop:Headline`
2. EXIF ImageDescription (tag 270/0x10E)

IPTC also defines `photoshop:CaptionWriter`, which may be useful for applications that wish to record who authored a caption. Note, however, that `photoshop:CaptionWriter` is limited to a single name. The FHMWG is not aware of any existing metadata suitable for storing the contributions of multiple metadata authors and editors.

#### 3.1.2.2. Example   <a name="3.1.2.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description xmlns:dc='http://purl.org/dc/elements/1.1/'>
  <dc:description>
   <rdf:Alt>
    <rdf:li xml:lang='eng'>My aunt Judy's pet rabbit</rdf:li>
   </rdf:Alt>
  </dc:description>
  <dc:title>
   <rdf:Alt>
    <rdf:li xml:lang='eng'>Judy's Rabbit</rdf:li>
   </rdf:Alt>
  </dc:title>
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
- dates with time information but without tome zone information


### 3.1.4. Event   <a name="3.1.4"></a>

FHMWG-recommended caption metadata stores a free-text description of the event during which the depicted scene occurred.
It does not store any structured event information, such as

- The duration of the event
- The location of the event
- The cause of the event

#### 3.1.4.1. Other metadata of interest   <a name="3.1.4.1"></a>

No other event-relevant metadata is known.

#### 3.1.4.2. Example   <a name="3.1.4.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description xmlns:Iptc4xmpExt='http://iptc.org/std/Iptc4xmpExt/2008-02-29/'>
  <Iptc4xmpExt:Event>
   <rdf:Alt>
    <rdf:li xml:lang='eng'>The 1973 Jones family reunion</rdf:li>
   </rdf:Alt>
  </Iptc4xmpExt:Event>
 </rdf:Description>
</rdf:RDF>
```

#### 3.1.4.3. Future extensions   <a name="3.1.4.3"></a>

No unfilled requirements related to events have been identified.



### 3.1.5. Location   <a name="3.1.5"></a>

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


#### 3.1.5.1. Other metadata of interest   <a name="3.1.5.1"></a>

It is recommended that missing location data be filled in the following order if `Iptc4xmpExt:LocationShown`'s GPS coordinates are missing:

1. geocode `Iptc4xmpExt:LocationShown`'s address components
2. geocode IPTC legacy location fields (`photoshop:City` etc)
3. `Iptc4xmpExt:LocationCreated`'s GPS coordinates
4. geocode `Iptc4xmpExt:LocationCreated`'s address components
5. geocode IPTC IIM address (2:90 city, 2:92 sub-location, 2:95 province/state, etc)
6. EXIF's `GPSLatitude` and `GPSLongitude`, as modified by `GPSLatitudeRef` and `GPSLongitudeRef`

It is recommended that EXIF's GPS coordinates be updated any time the `LocationSown` is updated so that tools reading EXIF will correctly locate the image.

IPTC has many more location parts, including altitude and various region-specific jurisdiction names.

#### 3.1.5.2. Example   <a name="3.1.5.2"></a>

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
       <rdf:li xml:lang='en'>Salt Lake City (city), Utah (state), USA (nation) as of 2020-07-24</rdf:li>
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

#### 3.1.5.3. Future extensions   <a name="3.1.5.3"></a>

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




### 3.1.6. Person and Object   <a name="3.1.6"></a>

FHMWG-recommended person and object metadata stores objects, structures, etc directly depicted in the media; people directly depicted in the media; and people not depicted by who contributed to or were otherwise relevant to the depiction (e.g., a witness of the event such as the photographer, painter, drafter, etc.)
It does not store other people involved in the provenance chain (digitizer, editor, metadata author, etc).

A person that is not in the depiction but is part of the original depiction event, such as a photographer or painter, shall use the whole-image region boundary.

Implementations that do not have region-of-image support, or that have such support but are unable to determine the correct region for one or more persons, shall use the while-image region boundary for any people they add to the image metadata.

A region should be selected such that an intelligent observer can identify which person is being indicated from the region. This may be mean that a region may include just part of the person and/or some content around the person.

Multiple regions' boundaries may overlap.
Multiple regions may have geometrically identical boundaries.

Regions need not identify an image of the person directly; it is appropriate to identify, e.g., the image of a signature with the person who signed it.

Implementations should assume that each entry is independent and valid unless a user makes a different decision.
Even having multiple regions with the same `Iptc4xmpExt:PersonId` is in generally permitted as a person may appear in multiple regions of an image (e.g., in a mirror, by having something else occlude part of them, by signing the document in multiple places, etc).


#### 3.1.6.1. Other metadata of interest   <a name="3.1.6.1"></a>

IPTC allows arbitrary content in regions, including regions with multiple people and objects, regions with other regions inside, etc.
To limit complexity, this recommendation only permits a single person or object per region.
Implementations understanding more complicated regions should convert them into one region per person or object.

IPTC also allows persons outside of any region structure.
To limit complexity, this recommendation only permits persons inside regions.
Implementations understanding unregioned persons should convert them into one region per person or object, using the whole-image region boundary unless a smaller region can be identified.

IPTC also allows the `Iptc4xmpExt:PersonInImage` field which contains only a person's name. These should be converted to `Iptc4xmpExt:PersonInImageWDetails`.

Information about people not depicted can be found in many other metadata fields, including but not limited to

- XMP `dc:creator`
- IPTC IIM 2:80 By-Line
- XMP `plus:ImageCreator`
- EXIF 0x13B "Artist"

How and whether to reading these is left up to individual implementations.

Each `Iptc4xmpExt:PersonInImageWDetails` may also include
`Iptc4xmpExt:PersonCharacteristic`, which is discouraged for family history use. Its' value is a set of elements from a controlled vocabulary (e.g., to aid in searching for images with particular characteristics from a library of stock images), and a suitable vocabulary for family history is not known.

In addition to `Iptc4xmpExt:AOTitle`, each `Iptc4xmpExt:ArtworkOrObject` may also include

- `Iptc4xmpExt:AOPhysicalDescription` a free-text desciption of the object's physical (not personal or historical) characterstics
- `Iptc4xmpExt:AODateCreated` and/or `Iptc4xmpExt:AOCircaDateCreated`, both describing the date the object was created (one as an ISO 8601 datetime, the other as free text)
- `Iptc4xmpExt:AOContentDescription`, only appropriate for an object that is itself a depiction of something else, such as a family portrait in the background of a scene
- `Iptc4xmpExt:AOCreator`, a description of who created the object
- IPTC defines several other fields that apply to photographs of artwork

#### 3.1.6.2. Example   <a name="3.1.6.2"></a>

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description rdf:about=''
  xmlns:Iptc4xmpExt='http://iptc.org/std/Iptc4xmpExt/2008-02-29/'>

  <Iptc4xmpExt:ImageRegion>
   <rdf:Bag>

    <rdf:li rdf:parseType='Resource'>
     <Iptc4xmpExt:PersonInImageWDetails>
      <rdf:Bag>
       <rdf:li rdf:parseType='Resource'>
        <Iptc4xmpExt:PersonId>
         <rdf:Bag>
          <rdf:li>https://www.familysearch.org/blog/en/author/gordonclarke/</rdf:li>
          <rdf:li>http://example.com/FHMWG/Clarke,Gordon</rdf:li>
         </rdf:Bag>
        </Iptc4xmpExt:PersonId>
        <Iptc4xmpExt:PersonName>
         <rdf:Alt>
          <rdf:li xml:lang='en-US'>Gordon Clarke</rdf:li>
          <rdf:li xml:lang='ja'>クラーク・ゴードン</rdf:li>
         </rdf:Alt>
        </Iptc4xmpExt:PersonName>
       </rdf:li>
      </rdf:Bag>
     </Iptc4xmpExt:PersonInImageWDetails>
     <Iptc4xmpExt:RegionBoundary rdf:parseType='Resource'>
      <Iptc4xmpExt:rbShape>rectangle</Iptc4xmpExt:rbShape>
      <Iptc4xmpExt:rbUnit>relative</Iptc4xmpExt:rbUnit>
      <Iptc4xmpExt:rbX>0.6</Iptc4xmpExt:rbX>
      <Iptc4xmpExt:rbY>0.2</Iptc4xmpExt:rbY>
      <Iptc4xmpExt:rbW>0.3</Iptc4xmpExt:rbW>
      <Iptc4xmpExt:rbH>0.8</Iptc4xmpExt:rbH>
     </Iptc4xmpExt:RegionBoundary>
    </rdf:li>

   </rdf:Bag>
  </Iptc4xmpExt:ImageRegion>

 </rdf:Description>
</rdf:RDF>
```

#### 3.1.6.3. Future extensions   <a name="3.1.6.3"></a>

There is a known desire to indicate name parts (given, preferred, family, patronymic, matronymic, honorific, etc).

There is a known desire to indicate relationships between people, familial and otherwise.

There is a known desire to distinguish between "not depicted" and "depicted at an unknown location".

There is a known desire to tag content that is neither a human nor an object, such as animals, visual phenomena, etc.





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
and can can indicate many things about text including language, script, dialect, and more.
The language subtag registry is frequently updated with new languages, but maintained in a way that ensures old tags never lose their meaning.

A special quasi-language-tag `x-default` was introduced by Google in 2013 to mean "the default language".
Semantically, this means the same thing as the standard tag `und-i-default`: an undetermined language to be shown by default.
Some applications are known to only edit the specific language tag `x-default`, so it is recommended that it always be provided.

As with other metadata, conformant application must not discard or edit language-tagged data in an AltLang simply because the language it unknown to the application.
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
it is recommended to always export data using the default namespaces given in the beginning of [Metadata to Write].

## 3.4. Treating XMP as RDF/XML   <a name="3.4"></a>

XMP is officially [RDF/XML](https://www.w3.org/TR/rdf-syntax-grammar/): that is, RDF expressed in XML.
RDF is a very flexible data format, including the ability to encode cyclic graphs and other data that cannot be trivially represented in nested structures.
However, all common XMP vocabularies use tree-structured data that has a canonical nested representation in RDF/XML.
That canonical structure is given in the descriptions of this document rather than the more general RDF description.

The FHMWG wishes to support applications that parse XMP as XML without understanding RDF/XML.
To that end, implementations that chose to use a full RDF toolchain for XMP data should ensure that the XMP data is exported in the nested representation described in this document.
The RDF toolchains known to the authors of this document use that representation by the default.
Those that don't should provide settings or flag to select it (relevant rules in the RDF/XML specification include "omitting blank nodes" and "omitting nodes").

## 3.5. Length Limits   <a name="3.5"></a>

Neither XMP nor IPTC impose any limits on the length of string payloads, and neither does the FHMWG.
Implementations should be prepared to store and display strings of arbitrary lengths.

In particular, [captions](#2.2) often contain many distinct pieces of information in somewhat verbose prose and may extend into tens of thousands of characters or more.
