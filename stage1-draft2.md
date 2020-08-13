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


# Overarching Principles

## Datatypes

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

- **Number**: An integer or decimal number in base 10 using U+002E (`.`, the period of full stop) as the floating point with no thousands-separators, as defined in [XMP Part 1 §8.2.1.4](https://wwwimages2.adobe.com/content/dam/acom/en/devnet/xmp/pdfs/XMP%20SDK%20Release%20cc-2016-08/XMPSpecificationPart1.pdf#G5.869673).

- **DateTime**: A date in the proleptic Gregorian calendar, optionally with time in a 24-hour clock, conforming to the subset of ISO 8601 format specified by the W3C in [NOTE-datetime](https://www.w3.org/TR/NOTE-datetime).

- **Closed set**: One of a fixed set of options, given in the specification.

Whitespace normalization discards any leading or trailing whitespace, and replaces any other whitespace with a single space (U+0020) character, as specified by the W3C in [XML 1.1](https://www.w3.org/TR/xml11/).
Whitespace in this context is a consecutive sequence of one or more characters from the set {tab (U+0009), newline (U+000A), carriage return (U+000D), space (U+0002)}.

## Do not remove metadata

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

## Selective Implementation

If
    
- a user provides additional metadata about an image, and 
- the metadata provided is of a type covered in this recommendation, and
- the user does not request that the metadata be kept private

then a compliant application shall embed the metadata in the image upon export as specified in this document.

It is not necessary for an application to handle all of the metadata discussed in this recommendation to be considered compliant with these recommendations.
An application that does not handle metadata at all, neither accepting nor removing it, is trivially compliant.
An application that only tags the names of individuals in a photo and has no other metadata is compliant if it embeds that metadata according to the Person section and does not otherwise edit or remove metadata.
Etc.

## Going beyond this spec

Applications may implement metadata beyond the scope of this recommendation, either by implementing other metadata standards or by designing their own metadata system.
Note, however, that metadata that is covered by these recommendations must be embedded according to them, even if it is also embedded in additional ways.

# Metadata to Write

This section uses the following prefixes:

| Prefix        | URI                                                        |
| :------------ | :--------------------------------------------------------- |
| `dc`          | `http://purl.org/dc/elements/1.1/`                         |
| `Iptc4xmpExt` | `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`              |
| `mwg-coll`    | `http://www.metadataworkinggroup.com/schemas/collections/` |
| `photoshop`   | `http://ns.adobe.com/photoshop/1.0/`                       |
| `rdf`         | `http://www.w3.org/1999/02/22-rdf-syntax-ns#`              |
| `exif`        | `http://ns.adobe.com/exif/1.0/`                            |

## Album

### Summary

| Field | Type | Stores |
| :---- | :--- | :----- |
| `mwg-coll:Collections` | `rdf:Bag` | Any number of albums |
| `mwg-coll:CollectionName` | Line String | Name of one album |
| `mwg-coll:CollectionURI` | IRI | Identifier of one album |

### Details

Album membership is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `mwg-coll:Collections`
- which shall contain 1 `rdf:Bag`
- which shall contain 0 or more `rdf:li` with `rdf:parseType="Resource"`
- each of which shall contain at least one of
    - 1 `mwg-coll:CollectionName` with a line-text content
    - 1 `mwg-coll:CollectionURI` with a valid IRI as its content

## Caption

### Summary

| Field | Type | Stores |
| :---- | :--- | :----- |
| `dc:description` | AltLang block | Image caption |
| `dc:title` | AltLang line | Image title |

### Details

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

## Date

### Summary

| Field | Type | Stores |
| :---- | :--- | :----- |
| `photoshop:DateCreated` | DateTime | Date of depicted scene |

### Details

Date is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `photoshop:DateCreated`
- which shall contain a date or date-time string conforming to the subset of ISO 8601 format specified in <https://www.w3.org/TR/NOTE-datetime>

## Event

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:Event` | AltLang block | Description of event depicted scene is a part of |

### Details

Events are encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `Iptc4xmpExt:Event`
- which shall contain 1 `rdf:Alt`
- which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
- each of which shall contain a free-text description of the event in the given human language

## Location

### Summary

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:LocationShown` | `rdf:Bag` | One\* location |
| `exif:GPSLatitude` | Number | Degrees north of equator of one location |
| `exif:GPSLongitude` | Number | Degrees east of prime meridian of one location  |
| `Iptc4xmpExt:LocationName` | AltLang block | Full name of one location |
| `Iptc4xmpExt:LocationId` | IRI | Identifier of one location |

\* IPTC allows more than one location in an image, but the FHMWG recommends only one be used

### Details

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

## Objects and People

Objects and people are stored inside image regions.

### Image Region

#### Summary

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:ImageRegion` | `rdf:Bag` | Any number of image regions |
| `rdf:li` |  | One region boundary and one\* person or object |
| `Iptc4xmpExt:ArtworkOrObject` |  | An object |

\* IPTC allows arbitrary content in regions, but the FHMWG recommends only one object or person per region

#### Details

Image regions are encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `Iptc4xmpExt:ImageRegion`
- which shall contain 1 `rdf:Bag`
- which shall contain 1 or more `rdf:li`, each with the `rdf:parseType='Resource'`
- each of which shall contain one `Iptc4xmpExt:RegionBoundary` with `rdf:parseType='Resource'`, and one `Iptc4xmpExt:PersonInImageWDetails` or `Iptc4xmpExt:ArtworkOrObject`

### Region Boundaries

#### Summary

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:RegionBoundary` |  | A region boundary |
| `Iptc4xmpExt:rbShape` | Closed set | "`rectangle`" or "`circle`" or "`polygon`" |
| `Iptc4xmpExt:rbUnit` | Closed set | "`relative`"\*
| `rbX` | Number | (from left edge) left of rectangle; center of circle; or coordinate of a vertex |
| `rbY` | Number | (from top edge) top of rectangle; center of circle; or coordinate of a vertex |
| `rbW` | Number | width of rectangle |
| `rbH` | Number | height of rectangle |
| `rbBx` | Number | radius of circle in x-axis coordinates |
| `rbVertices` | `rdf:Seq` | sequence of vertexes of polygon |

\* IPTC also allows "`pixel`", but the FHMWG recommends only "`relative`" be used

#### Details

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

#### Whole-image Region

A special region boundary is known as the "whole-image region boundary":
a `relative` `rectangle` 
with `rbX` = 0, `rbY` = 0, `rbW` = 1, and `rbH` = 1.
This special region shall be used for both of the following:

- persons or objects depicted in the image but whose region is unknown
- persons or objects directly related to the image but depicted in it

### Object

#### Summary

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:ArtworkOrObject` | | A person |
| `Iptc4xmpExt:AOTitle` | AltLang block | The short descrition or title of the object |


#### Details

The `Iptc4xmpExt:ArtworkOrObject` shall contain

- one `Iptc4xmpExt:AOTitle`
- which shall contain 1 `rdf:Alt`
- which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
- each of which shall contain a free-text content in the given human language, which *shall* be the an identifying title for the object in that language.


### Person

#### Summary

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:PersonInImageWDetails` | | A person |
| `Iptc4xmpExt:PersonName` | AltLang line | The name of one person |
| `Iptc4xmpExt:PersonDescription` | AltLang block | The description of one person |
| `Iptc4xmpExt:PersonId` | IRI | An identifier of one person |



#### Details

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



# Additional Considerations

## Field-specific recommendations

### Album
### Caption
### Date
### Event
### Location
### Person and Object

## Handling Unicode and Languages

## XML namespaces

## Treating XMP as XML/RDF

