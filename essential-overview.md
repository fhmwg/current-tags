# FHMWG Essential Metadata Recommendation 1 — Technical Summary

This document is a technical summary of the full normative specification
which may be found at <https://github.com/fhmwg/current-tags/blob/master/essential.md>.  The Album, Event, Image Region and Object has been removed from the FHMWG Recommendation Stage 1.  Other minor change have been changes to accomodate People and Location using more popular methodology. 

## Purpose

This is a minimum standard for embedded family history metadata in images:
that is, data about what the image depicts that are stored in machine-readable (non-visual) format within the image file itself.

There are thousands of metadata fields in the use in the wild today, many of which overlap in full or in part with other fields and many of which are incompletely specified and used in inconsistent ways. The primary goal of this recommendation is to identify and clarify the meaning of a small subset of these existing fields which we recommend as core to family history applications of metadata that already have some popularity.

This recommendation makes use of XMP, a standard produced by Adobe for storing RDF/XML in image metadata; and IPTC, which provides one of the more popular RDF vocabularies for XMP. Because those are complicated and nuanced technologies, we describe the resulting standard only in terms of XML; it is our intent that a full understanding of RDF, XMP, and IPTC is not necessary to implement these recommendations.

## Overarching principles

1. **Don't Remove Metadata**. Even if the metadata is not a form you understand or is a duplicate of other fields, some other tool may have a use for it. Leave it in the file.

2. **At Least These Fields**. There are hundreds of date fields available, of which we recommend using just one. You are welcome to read and write as many of the other fields as you wish, as long as you also use the one we recommend.

3. **IDs are IRIs**. Internationalized Resource Identifiers (IRIs) are a polyglot extension to URIs, themselves an extension to URLs to allow storing identifiers that are not web pages. Since names, dates, etc are not enough to uniquely identify people and places, IRIs are recommended to make matching people between multiple images less error-prone. Because URLs are valid IRIs, IRIs also allow linking to external resources.

## Summary of recommendation

### Title

An image may have a title.
The title should be a short descriptive title of the image itself.

### Description/Caption

An image may have a description.
The description may be of any length and contain any information the user cares to add. The description could also include a caption for the image.

### Date

An image can record the date of the depicted scene.
Note that this is the date of the scene, not the date of the image creation;
while those may be the same for photographs, they generally differ for illustrations.

Many other date fields exist in other metadata, such as image creation dates, image modification dates, etc. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the date of the depicted scene.

The readability of two other dates are are required if the XMP is not found. These additional dates would be a IIM value and a EXIF value.
On read: read the XMP value, if not there then read the IIM value, if not there read the EXIF value.  If the user changes the date created, it is written to all three places.  This would maintain backwards compatibility and interoperability between older and new formats and software.



### Location Text

An image can identify one location where the primary focus of the depicted scene is found. The location text is the name.  The location latitude/longitude pair would be stored in the Location GeoTags.


Many other location properties exist in the IPTC standard, such as the political jurisdictional hierarchy of city/state/country, etc. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the LocationShown,Location, City, State, Country, and Country Code.

IPTC allows storing more than one location in a single image's metadata. Because the meaning of multiple locations is not uniformly understood, we recommend against using multiple location

GPS coordinates always identify a single precise point, but real locations may cover a larger area or be imprecisely located. Coordinate regions and imprecision may be added in a future version of this recommendation.

### Location GeoTags

An image can identify one location where the primary focus of the depicted scene is found.   The location latitude/longitude pair would be stored in the Location GeoTags.

GPS coordinates always identify a single precise point, but real locations may cover a larger area or be imprecisely located. Coordinate regions and imprecision may be added in a future version of this recommendation.

### Person In Image (Person Name)

Each person referenced with this tag will need a Person Face Tag by a region with a region structure.


### Person Face Tag
Referencing a person withing a image will need a Person Face Tag and a Person Name

## Metadata fields

The key metadata fields used in this recommendation are listed below.
The full XML of the XMP metadata contains a variety of RDF/XML structures to help represent concepts like "any number of" substructures and "human language of" strings, which are not included in this summary.

What follows uses the following namespace abbreviations:

| Prefix        | URI                                                        |
| :------------ | :--------------------------------------------------------- |
| `dc`          | `http://purl.org/dc/elements/1.1/`                         |
| `Iptc4xmpExt` | `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`              |
| `mwg-coll`    | `http://www.metadataworkinggroup.com/schemas/collections/` |
| `photoshop`   | `http://ns.adobe.com/photoshop/1.0/`                       |
| `rdf`         | `http://www.w3.org/1999/02/22-rdf-syntax-ns#`              |
| `exif`        | `http://ns.adobe.com/exif/1.0/`                            |



### Title

| Field | Type | Stores |
| :---- | :--- | :----- |
| `dc:title` | Text| Image title |

### Description

| Field | Type | Stores |
| :---- | :--- | :----- |
| `dc:description` | Text | Image caption |

### Date

| Field | Type | Stores |
| :---- | :--- | :----- |
| `photoshop:DateCreated` | DateTime | Date of depicted scene |
| `EXIF:DateTimeOriginal` | DateTime | Date of depicted scene |
| `IIM:DateTimeCreated` | DateTime | Date of depicted scene |


### Location Name

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:LocationShown` | *nested elements* | One\* location |
| `ptc4xmpCore:Location` | Text | Location name of one location |
| `photoshop:City` | Text | City name of one location  |
| `photoshop:State` | Text | State name of one location |
| `photoshop:Country` | Text| Country name of one location |
| `Iptc4xmpCore:CountryCode` | Number | Country Code of one location |

\* IPTC allows more than one location in an image, but the FHMWG recommends only one be used

### Location GeoTag

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:LocationShown` | *nested elements* | One\* location |
| `EXIF GPS longitude` | Number | Location longitude of one location |
| `EXIF GPS latitude` | Number | Location longitude of one location |

#### Person In Image (Person Name)

| Field | Type | Stores |
| :---- | :--- | :----- |
| ` Iptc4xmpExt:PersonInImage` | *nested elements* | A person |

#### Person Face Tag

| Field | Type | Stores |
| :---- | :--- | :----- |
| ` mwg-rs:Regions` | *nested elements* | with RegionStuct |
