---
title: FHMWG Stage 1 Recommendations
author:
    - Luther Tychonievich
    - Gordon Clarke
    - Peter Krogh
...

This document is a working draft for review and comment only.
It has not been vetted or approved by any group.


# General principles

## Vetting and censoring

There are various reasons why an application may wish to reject certain metadata.
This section provides common rules for such cases.

Applications *must not* edit provided metadata except under the following four cases:

1. User-moderated editing is permitted, including 
    a. user-initiated edits
    b. application-initiated edits presented to the user for approval
2. Converting values identified as "free text" to unicode normal forms C or D
3. Whitespace-normalizing values identified as "line text"
4. Other cases specifically permitted for a particular field

Applications may remove a metadata field entirely if it is deemed inappropriate and no edit is permitted under the four cases above. Note that removing a field is distinct from editing it to be the empty string: removing a field means removing its XMP element or EXIF key:value pair entirely.

An application *should not* remove a field simply because it does not understand the meaning of the field.
All metadata, understood or not, *should* be perserved unless an application has reason to beleive it is inappropriate.

### Malicious URLs

URL, URI, IRL, and IRI (collectively called URI hereinafter) fields may be vetted to ensure they are not URLs of malicious sites

- A version 3, 4, or 5 UUID encoded as a URN, as specfiied in <https://tools.ietf.org/html/rfc4122>, is never malicious.
- The maliciousness of other URIs is not defined in this spec.
- If a malcious URI, is encountered, it should be replaced by a version-5 UUID created using the empty-string namespace and hashing the rejected URI.
- If the field in question requires a visitable link (i.e., it must be a URL, not a URI) and cannot otherwise be edited, then the entire field may be removed.
- As with all fields, an application *must not* edit a provided URI without user involvement

When used as an identifier, a URI and its corresponding version-5 UUID encoded as a URN should be considered equivalent, unless the URI is a version 3, 4, or 5 UUID.

> Because taking a version-5 UUID is not idempotent, the above exception for UUIDs prevents the cornder case of needing to check the UUID of the UUID of the UUID of ... of the URI. Versions 1 and 2 are not excluded because they are sometimes considered security risks.


### Inappropriate text

Any free-text field might contain abusive or inappropriately-shared prviate content.
As with all fields, inappropriate text *must not* be edited without user involvement;
if user involvement is unable to resolve the issue, the entire field *may* be removed.

### Length- and content-limiting

Applications *may* use various automated properties of fields as a proxy for appropriateness.

- Content *may* be limited to reject certain delimiters and Unicode control characters. However, values intended for human viewing *must not* be limited to ASCII or other subsets of code points that exclude some human languages.

- Content *may* be limited by length, but such limits should be generous.

As with other cases, neither characters nor length should be enforced with automated editing; a user *must* mediate any editing. If an application choses to remove metadata because it is too long or uses illegal characters, it *should* alert the user it has done so and indicate the reason it is being removed.

## Partial implementation

To be fully compliant with these recommendations, an application will

1. Preserve metadata provided with an imported image though all internal manipulations of the image and embed it again upon export. This preservation applies to all metadata representable in the same way in both the import and export file format, except as permitted elsewhere in this recommendation, whether or not the application understand the meaning of the metadata.

2. If
    
    a. a user provides additional metadata about an image, and 
    b. the metadata provided is of a type covered in this recommendation, and
    c. the user does not request that the metadata be kept private:
    
    then the application embeds the metadata in the image upon export as specified in this recommendation.

It is not necessary for an application to handle all of the metadata discussed in this recommendation to be considered compliant with these recommendations.
An application that does not handle metadata at all, neither accepting nor removing it, is trivially compliant.
An application that only tags the names of individuals in a photo and has no other metadata is compliant if it embeds that metadata according to the [Person] section and does not otherwise edit or remove metadata.
Etc.

## Going beyond this spec

Applications *may* implement metadata beyond the scope of this recommendation, either by implementing other metadata standards or by designing their own metadata system.
Note, however, that to be compliant with these recommendations they *must* obey this specification, including

- Metadata that is covered by these recommendations *must* be embedded according to them; it *may* also be embedded in additional ways.

- Metadata must not be edited without user moderation.

# Metadata Categories

## Album

### What this metadata stores

Zero or more collections a given image belongs to.
Each collection is identified by one human-readable string and/or one URI.

### What this metadata does not store

- Hierarchies of collections
- Positions within a collection
- Collections with more than one identifier

#### What other standards does this use

- Defined by the Metadata Working Group <http://web.archive.org/web/20180822085951/http://metadataworkinggroup.com/pdf/mwg_guidance.pdf>
- Embedded within XMP <https://www.adobe.com/devnet/xmp.html>
- Using IRIs <https://tools.ietf.org/html/rfc3987>

### Formal Specification

#### XMP structure

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
    - 1 `mwg-coll:CollectionURI` with a valie IRI as its content

Implementations *should* whitespace-normalize the contents of `mwg-coll:CollectionName` and `mwg-coll:CollectionURI`.
Implementations *should not* use empty strings (or strings that whitespace-normalize to the empty string) for the content of the `mwg-coll:CollectionName` or `mwg-coll:CollectionURI` elements; if such are encountered, they *should* be treated as if the element was not provided.

It is *recommended* that 

- User Interfaces refer to `mwg-coll:CollectionName` as "Album Name" or "Collection Name"
- User Interfaces refer to `mwg-coll:CollectionURI` as "Album URL" or "Collection URL" if it uses the `http` or `https` scheme; otherwise as "Album identifier" or "Collection identifier"
- A `mwg-coll:CollectionURI` be added to every collection if the implementation believes it has access to all images in the album; the content *should* be determined as
    - If a `mwg-coll:CollectionURI` was provided on import, use that
    - Otherwise, if the implementation or user provides a durable world-readable URL at which to access the album, use that
    - Otherwise, use a URL created as a UUID encoded as a URN, as specfiied in <https://tools.ietf.org/html/rfc4122>. Version 4 (random) UUIDs are *recommended*.

### Guidelines

#### Interpretation

If two albums are identified with the same name but distinct URIs, they *should* be treated as distinct albums.

If two albums are identified with the same URI but distinct names, they *should* be treated as a single album with multiple names.

An album without a name *may* be presented using an auto-generated name which *should* be "Untitled Album" optionally followed by a small positive integer. Auto-generated names *should not* be exported as metadata.

An album without a URI is ambiguous as to its identity, and that ambiguity *should* be displayed to the user in some way. If the user interface needs to guess if two non-URI albums with the same name are the same album or not, it is *recommended* that they be considered the same only if the (album name, providing user) pairs match.

> Should we *require* providing a URI? Or both a URI and a name? --- Luther 

#### Other metadata to read

The FHMWG is unaware of other widely-used album identifiers to be read.

> I vaguely remember that EXIF had some capture-time information that could possibly be used to pre-populate albums? --- Luther

#### Resolving conflicting metadata

The FHMWG is unaware of album conflicts that need to be resolved on import.

### Example

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


## Caption
## Date
## Event
## Location
## Object
## People
## Rights
## Sources
