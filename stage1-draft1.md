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

...

Table of contents (links only for drafted sections)

- [Album](#album)
- [Caption](#caption)
- Date
- Event
- [Location](#location)
- Object
- [People](#people)
- Rights




# Album

## What this metadata stores

Zero or more collections a given image belongs to.
Each collection is identified by one human-readable string and/or one URI.

## What this metadata does not store

- Hierarchies of collections
- Positions within a collection
- Collections with more than one identifier

## What other standards does this use

- Defined by the Metadata Working Group <http://web.archive.org/web/20180822085951/http://metadataworkinggroup.com/pdf/mwg_guidance.pdf>
- Embedded within XMP <https://www.adobe.com/devnet/xmp.html>
- Using IRIs <https://tools.ietf.org/html/rfc3987>

## Formal Specification

### XMP structure

Using the prefixes

- `mwg-coll` for `http://www.metadataworkinggroup.com/schemas/collections/`
- `rdf` for `http://www.w3.org/1999/02/22-rdf-syntax-ns#`

album membership is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `mwg-coll:Collections`
- which shall contain 1 `rdf:Bag`
- which shall contain 0 or more `rdf:li` with `rdf:parseType="Resource"`
- each of which shall contain either or both of
    - 1 `mwg-coll:CollectionName` with a free-text content
    - 1 `mwg-coll:CollectionURI` with a valid IRI as its content

Implementations *should* whitespace-normalize the contents of `mwg-coll:CollectionName` and `mwg-coll:CollectionURI`.
Implementations *should not* use empty strings (or strings that whitespace-normalize to the empty string) for the content of the `mwg-coll:CollectionName` or `mwg-coll:CollectionURI` elements; if such are encountered, they *should* be treated as if the element was not provided.

It is *recommended* that 

- User Interfaces refer to `mwg-coll:CollectionName` as "Album Name" or "Collection Name"
- User Interfaces refer to `mwg-coll:CollectionURI` as "Album URL" or "Collection URL" if it uses the `http` or `https` scheme; otherwise as "Album identifier" or "Collection identifier"
- A `mwg-coll:CollectionURI` be added to every collection if the implementation believes it has access to all images in the album; the content *should* be determined as
    - If a `mwg-coll:CollectionURI` was provided on import, use that
    - Otherwise, if the implementation or user provides a durable world-readable URL at which to access the album, use that
    - Otherwise, use a URL created as a UUID encoded as a URN, as specfiied in <https://tools.ietf.org/html/rfc4122>. Version 4 (random) UUIDs are *recommended*.

## Guidelines

### Interpretation

If two albums are identified with the same name but distinct URIs, they *should* be treated as distinct albums.

If two albums are identified with the same URI but distinct names, they *should* be treated as a single album with multiple names.

An album without a name *may* be presented using an auto-generated name which *should* be "Untitled Album" optionally followed by a small positive integer. Auto-generated names *should not* be exported as metadata.

An album without a URI is ambiguous as to its identity, and that ambiguity *should* be displayed to the user in some way. If the user interface needs to guess if two non-URI albums with the same name are the same album or not, it is *recommended* that they be considered the same only if the (album name, providing user) pairs match.

> Should we *require* providing a URI? Or both a URI and a name? --- Luther 

### Other metadata of interest

The FHMWG is unaware of other widely-used album identifiers.

> I vaguely remember that EXIF had some capture-time information that could possibly be used to pre-populate albums? --- Luther

### Resolving conflicting metadata

The FHMWG is unaware of album conflicts that need to be resolved on import.

## Example

The following represents an image belonging to two albums, one with a URI and one without:

| Album name | Album URI |
|:-----------|:----------|
| My Last Vacation Photos | `http://www.flickr.com/photos/myvacation` |
| Beautiful Sunset Set | |


````xml
<rdf:RDF xmlns:rdf=“http://www.w3.org/1999/02/22-rdf-syntax-ns#”>
<rdf:Description xmlns:mwg-coll=“http://www.metadataworkinggroup.com/schemas/collections/”> 
    <mwg-coll:Collections>
      <rdf:Bag>

<rdf:li rdf:parseType=“Resource”>
<mwg-coll:CollectionName>My Last Vacation Photos</mwg-coll:CollectionName> 
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

## Future extensions

There is a known desire to store position within a collection. In addition to requiring a new metadata field, this raises concerns about distinct elements claiming the same position.

There is a known desire to store metadata about a collection, such as creator, purpose, date, etc. This would require the collection itself to be an entity about which metadata can be recorded.


# Caption

## What this metadata stores

Free-text description in any language containing any information the caption-writer wishes to add.

## What this metadata does not store

Structured information.
Where possible, implementations should encourage placing information in other metadata fields.

## What other standards does this use

- Defined by the Dublin Core <http://purl.org/dc/elements/1.1/description>
- Recommended by IPTC <https://www.iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#description>
- Embedded within XMP <https://www.adobe.com/devnet/xmp.html>
- With language tags defined in IETF BPC 47 <https://tools.ietf.org/rfc/bcp/bcp47.txt>

## Formal Specification

### XMP structure

Using the prefixes

- `dc` for `http://purl.org/dc/elements/1.1/`
- `rdf` for `http://www.w3.org/1999/02/22-rdf-syntax-ns#`

image description is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `dc:description`
- which shall contain 1 `rdf:Alt`
- which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
- each of which shall contain a free-text content in the given human language

To the degree possible, implementations should require users to have translations of the same content in each language they provide.

Implementations *must not* create multiple `rdf:li` in the same `rdf:Alt` with equivalent `xml:lang` attributes, as defined by BPC 47. If such prohibited multiples are encountered on import, implementations *may* handle them in an implementation-defined way.

XMP does not provide a method for distinguishing the order of language alternatives nor for indicating the prefered alternative.

> Should stage 1 be limited to a single language only? Simpler, but also not fully compatible with some existing XMP.
>
> Perhaps it is enough to say something like "implementations that are not able to handle language alternatives *may* deiscard all but one language alternative."

## Guidelines

### Interpretation

It is common for captions to replicate some information ncluded elsewhere in the metadata;
for example, peopled portrayed in the image may be identified in person meatadata and also in the caption so that the caption may discuss the relationships between the people or the like.
This inevitably leads to the posiblity of conflicting information.
Because it is not possible to programatically determine which metadata is most accurate, implementations *should* display all metadata to the user and not attempt to perform automated resolution of conflicts between captions and other metadata.

### Other metadata of interest

If present, the EXIF ImageDescription (tag 270/0x10E) is an ASCII-only non-language-tagged title possibly containing comments.

If present, the EXIF UserComment (tag 0x9286) is a non-language-tagged comment possibly containing a caption.

If present, the XMP `dc:title` is a short identifying title of the image.
`dc:title` was defined for the purpose of recording the official title of published works;
when encountered on family photos its intented meaning cannot in general be infered.

If present, the XMP `photoshop:Headline` is a short descriptive summary of the image.
`photoshop:title` was defined for the purpose of recording the suggested headline to accompany images in news articles;
when encountered on family photos its intented meaning cannot in general be infered.

IPTC also defines `photoshop:CaptionWriter`, which may be useful for applications that wish to record who authored a caption. Note, however, that `photoshop:CaptionWriter` is limited to a single name. We are not aware of any existing metadata suitable for storing the contributions of multiple metadata authors and editors.

### Resolving conflicting metadata

It is *recommended* that implementations encountering an image without a caption pre-populate it the first of the following that is available:

1. the EXIF ImageDescription
2. the `photoshop:Headline`
3. the `dc:title`
4. the file name of the image file

When populating a language-tagged string from a non-language-tagged field, the language tag `und` *shall* be used.

Some existing generic metadata tools use the language tag `x-default`. This *should* be replaced with a registered language tag, generally either be the prefered language of the caption writer or `und`.


## Example

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description xmlns:dc='http://purl.org/dc/elements/1.1/'>
  <dc:description>
   <rdf:Alt>
    <rdf:li xml:lang='eng'>My aunt Judy's pet rabbit</rdf:li>
   </rdf:Alt>
  </dc:description>
 </rdf:Description>
</rdf:RDF>
```

## Future extensions

There is a known desire to store the following captions types separately:

- caption as written on image
- printed caption accompanying image
- caption supplied by user of digital tool

There is a known desire to store the authorship and edit dates of all captions. `photoshop:CaptionWriter` may provide a partial solution to this.

There is a known desire to include style markup in captions. Embedded HTML may be a solution to this, but is not directly permitted as part of XMP and raises issues about validation and markup-unaware implmentations.

There is a known desire to include links between portions of captions and other metadata fields. RDFa and xlink may provide a solution to this, but is quite heavy-handed.

There is a known desire to indicate which language is the original and which are translations. Various solutions could work (e.g., adding `xml:lang` to `dc:description`, adding a `fhmwg:original="true"` attribute to `rdf:li`, etc) but all have implementation challenges when interacting with FHMWG-unaware tools.

# Date

# Event

# Location

The location team has recommended that both geographic (e.g., GPS) and jusrisdictional data be used. However, they have recommended against the fields provided for this in existing standards.

A fixed hierarchy of jurisdictions, such as that provided by IPTC, leads to misleading historical data and should not be used in family history applications.
Instead, a custom hierarchy of jurisdictions should be permitted for each location.
Furthermore, applications *should* encourage users to use the hierarchy that existing at the time of the depicted event.

Additionally, a survey of existing online and desktop tools suggested that most are storing a hierarchy of jurisdictions but are not storing (or are storing internally but not exposing) the meaning of each level in the hierarchy.
Thus, from a practical adoption standpoint, the meaning of each level should be optional but recommended.

A numeric latitute-longitude pair, such as that ptovided by IPTC, leads to misleading understanding of location precision and should not be used in family history applications.
Instead, imprecise coordinates should be supported, as for example by extension of the image region structure or the like.



# Object



# People

## What this metadata stores

- People directly depicted in the media
- People not depicted by who contributed to or were otherwise relevant to the depiction (e.g., a witness of the event such as the photographer, painter, drafter, etc.)

## What this metadata does not store

- Other people involved in the provenance chain (digitizer, editor, metadata author, etc)

## What other standards does this use

- Defined by IPTC <https://www.iptc.org/std/photometadata/specification/IPTC-PhotoMetadata>
- Embedded within XMP <https://www.adobe.com/devnet/xmp.html>

## Formal Specification

While IPTC has many different ways to store people, the FHMWG recommends only one:
the image has a region which has a person.

### XMP structure

Using the prefixes

- `rdf` for `http://www.w3.org/1999/02/22-rdf-syntax-ns#`
- `Iptc4xmpExt` for `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`

image description is encoded as

- The top-level `rdf:Description`
- shall contain 0 or 1 `Iptc4xmpExt:ImageRegion`
- which shall contain 1 `rdf:Bag`
- which shall contain 1 or more `rdf:li`, each with the `rdf:parseType='Resource'`
- each of which shall contain one `Iptc4xmpExt:RegionBoundary` with `rdf:parseType='Resource'`, and one `Iptc4xmpExt:PersonInImageWDetails` or `Iptc4xmpExt:ArtworkOrObject`

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

The `Iptc4xmpExt:PersonInImageWDetails` shall contain

- one `Iptc4xmpExt:PersonName`
- which shall contain 1 `rdf:Alt`
- which shall contain 1 or more `rdf:li`, each with the `xml:lang` attribute set to a distinct language tag
- each of which shall contain a free-text content in the given human language, which *shall* be the persons name in that language.

The `Iptc4xmpExt:PersonInImageWDetails` may additionally contain a `Iptc4xmpExt:PersonDescription` and/or `Iptc4xmpExt:PersonId`.

## Guidelines

### Whole-image region

A special region boundary is known as the "whole-image region boundary":
a `relative` `rectangle` covering the entire image.

```xml
<Iptc4xmpExt:RegionBoundary rdf:parseType='Resource'>
  <Iptc4xmpExt:rbShape>rectangle</Iptc4xmpExt:rbShape>
  <Iptc4xmpExt:rbUnit>relative</Iptc4xmpExt:rbUnit>
  <Iptc4xmpExt:rbX>0</Iptc4xmpExt:rbX>
  <Iptc4xmpExt:rbY>0</Iptc4xmpExt:rbY>
  <Iptc4xmpExt:rbW>1</Iptc4xmpExt:rbW>
  <Iptc4xmpExt:rbH>1</Iptc4xmpExt:rbH>
</Iptc4xmpExt:RegionBoundary>
```

This region boundary is used for a variety of purposed, as outlined below.

### Persons not depicted

A person that is not in the depiction but is part of the original depiction event, such as a photographer or painter, *shall* use the whole-image region boundary.

### Implementations without knowledge of regions

Implementations that do not have region-of-image support, or that have such support but are unable to determine the correct region for one or more persons, *shall* use the while-image region boundary for any people they add to the image metadata.

### Multi-person regions

IPTC allows arbitrary content in regions, including regions with multiple people and objects, regions with other regions inside, etc.
To limit complexity, this recommendation only permits a single person or object per region.
Implementations understanding more complicated regions *should* convert them into one region per person or object.

IPTC also allows persons outside of any region structure.
To limit complexity, this recommendation only permits persons inside regions.
Implementations understanding unregioned persons *should* convert them into one region per person or object, using the whole-image region boundary unless a smaller region can be identified.

### Interpretation

A region should be such that an intelligent observer can identify which person is being indicated from the region. This may be mean that a region may include just part of the person, or more than just the person, etc.

Multiple regions' boundaries may overlap.
Multiple regions may have geometrically identical boundaries.

Regions need not identify an image of the person directly; it is appropriate to identify, e.g., the image of a signature with the person who signed it.

### Other metadata of interest

Information about people can be found in many other metadata fields, including but not limited to

- XMP `dc:creator`
- IPTC IIM 2:80 By-Line
- XMP `plus:ImageCreator`
- XMP `Iptc4xmpExt:PersonInImage`
- XMP `Iptc4xmpExt:PersonInImageWDetails` outside of any region
- EXIF 0x13B "Artist"

How and whether to reading these is left up to individual implementations.

In addition to the `Iptc4xmpExt:PersonName`, each `Iptc4xmpExt:PersonInImageWDetails` may also include

- `Iptc4xmpExt:PersonCharacteristic`, which is discouraged for family history use. Its' value is a set of elements from a controlled vocabulary (e.g., to aid in searching for images with particular characteristics from a library of stock images), and a suitable vocabulary for family history is not known.
- `Iptc4xmpExt:PersonDescription`, which contains an `rdf:Alt` of `xml:lang`-tagged `rdf:li`, each being a description of the person in the given human language.
- `Iptc4xmpExt:PersonId`, which contains an `rdf:Bag` of `rdf:li`, each being a URI identifying the person.

Reading and writing `Iptc4xmpExt:PersonDescription` and `Iptc4xmpExt:PersonId` is encouraged.

### Resolving conflicting metadata

We are aware of no data conflicts in persons.
Implementations should assume that each entry is independent and valid unless a user makes a different decision.
Even having multiple regions with the same `Iptc4xmpExt:PersonId` is in generally permitted as a person may appear in multiple regions of an image (e.g., in a mirror, by having something else occlude part of them, by signing the document in multiple places, etc).

## Example

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

## Future extensions

There is a known desire to indicate which language is the original and which are translations. Various solutions could work (e.g., adding `xml:lang` to `Iptc4xmpExt:PersonName`, adding a `fhmwg:original="true"` attribute to `rdf:li`, etc) but all have implementation challenges when interacting with FHMWG-unaware tools.

There is a known desire to indicate name parts (given, preferred, family, patronymic, matronymic, honorific, etc).


# Rights

