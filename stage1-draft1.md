---
title: FHMWG Stage 1 Recommendations
author:
    - Luther Tychonievich
    - Gordon Clarke
    - Peter Krogh
    - Maureen Taylor
...

Table of contents (links only for drafted sections)

- [Album](#album)
- [Caption](#caption)
- Date
- Event
- Location
- Object
- People
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

If present, the XMP `dc:title` is a short identifying title of the image.
`dc:title` was defined for the purpose of recording the official title of published works;
when encountered on family photos its intented meaning cannot in general be infered.

If present, the XMP `photoshop:Headline` is a short descriptive summary of the image.
`photoshop:title` was defined for the purpose of recording the suggested headline to accompany images in news articles;
when encountered on family photos its intented meaning cannot in general be infered.

IPTC also defines `photoshop:CaptionWriter`, which may be useful for applications that wish to record who authored a caption. Note, however, that `photoshop:CaptionWriter` is limited to a single name. We are not aware of any existing metadata suitable for storing the contributions of multiple metadata authors and editors.

### Resolving conflicting metadata

It is *recommended* that implementations encountering an image without a caption pre-populate it with
the ImageDescription if present; otherwise the photoshop:Headline if present; otherwise the dc:title if present. When so populating a language-tagged string from a non-language-tagged field, the language tag `und` *shall* be used.

If the language tag `x-default` is encountered upon read, it *should* be replaced with a registered language tag, which should either be the prefered language of the caption writer, if known, or `und`.


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

There is a known desire to include links between portions of captions and other metadata fields. RDFa may provide a solution to this, but is quite heavy-handed.

There is a known desire to indicate which language is the original and which are translations. Various solutions could work (e.g., adding `xml:lang` to `dc:description`, adding a `fhmwg:original="true"` attribute to `rdf:li`, etc) but all have implementation challenges when interacting with FHMWG-unaware tools.

# Date

# Event

# Location

# Object

# People

# Rights

