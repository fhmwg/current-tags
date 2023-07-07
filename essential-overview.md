# FHMWG Essential Metadata Recommendation 1 — Technical Summary

This document is a technical summary of the full normative specification
which may be found at <https://github.com/fhmwg/current-tags/blob/master/essential.md>.  The Album, Event, Image Region and Object has been removed from the FHMWG Recommendation Stage 1.  Other minor changes have been made to accomodate People and Location using more popular methodology. 

## Purpose

This is a minimum standard for embedded family history metadata in images:
that is, data about what the image depicts that are stored in machine-readable (non-visual) format within the image file itself.

There are thousands of metadata fields in the use in the wild today, many of which overlap in full or in part with other fields and many of which are incompletely specified and used in inconsistent ways. The primary goal of this recommendation is to identify and clarify the meaning of a small subset of these existing metadata fields that already have some popularity which we recommend as core to family history applications.

This recommendation makes use of XMP, a standard produced by Adobe for storing RDF/XML in image metadata; and IPTC, which provides one of the more popular RDF vocabularies for XMP. Because those are complicated and nuanced technologies, we describe the resulting standard only in terms of XML; it is our intent that a full understanding of RDF, XMP, and IPTC is not necessary to implement these recommendations.

## Overarching principles

1. **Don't Remove Metadata**. Even if the metadata is not a form you understand or is a duplicate of other fields, some other tool may have a use for it. Leave it in the file.

2. **At Least These Fields**. There are hundreds of data fields available, of which we recommend using just one. You are welcome to read and write as many of the other fields as you wish, as long as you also use the one we recommend.

3. **IDs are IRIs**. Internationalized Resource Identifiers (IRIs) are a polyglot extension to URIs, themselves an extension to URLs to allow storing identifiers that are not web pages. Since names, dates, etc are not enough to uniquely identify people and places, IRIs are recommended to make matching people between multiple images less error-prone. Because URLs are valid IRIs, IRIs also allow linking to external resources.

## Summary of recommendation

### Title

An image may have a title.
The title should be a short human-readable name or reference for the digital file. 

### Description

An image may have a description.
The description may be of any length and contain any information the user cares to add. The description could also include a caption for the image.

### Date

An image can record the date of the depicted scene.
Note that this is the date of the scene, not the date of the image creation;
while those may be the same for photographs, they generally differ for scanned images.

Many other date fields exist in other metadata, such as image creation dates, image modification dates, etc. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the date of the depicted scene.


### Location Shown in Image

An image can identify one location where the primary focus of the depicted scene is found. A location can have a full name, identifier, sublocation, city, state, country, and GPS coordinates.


Many other location properties exist in the IPTC standard. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the full name, identifier, sublocation, city, state, country, and GPS coordinates.

IPTC allows storing more than one location in a single image's metadata. Because the meaning of multiple locations is not uniformly understood, we recommend against using multiple locations.

GPS coordinates always identify a single precise point, but real locations may cover a larger area or be imprecisely located. Coordinate regions and imprecision may be added in a future version of this recommendation.

### Person In Image (Person Name)

An image may depict persons either visually or by close association (i.e. my grandpa's house is associated with my grandpa even though grandpa is not visually in the photo). The image may have a list of person names associated with the image.


### Person Face Tag
A person in an image may be referenced by a face tag (coordinates within the image).  In addition to the face tag, the person name should be added to the list of persons in the image.


## Metadata properties

The key metadata properties used in this recommendation are listed below.
The full XML of the XMP metadata contains a variety of RDF/XML structures to help represent concepts like "any number of" substructures and "human language of" strings, which are not included in this summary.

What follows uses the following namespace abbreviations:

| Prefix            | URI or Reference                                                        |
| :------------     | :--------------------------------------------------------- | 
| `Iptc4xmpExt`     | `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`              |
| `Iptc4xmpCore`    | `http://iptc.org/std/Iptc4xmpCore/1.0/xmlns/`              |
| `mwg-rs`          | `http://www.metadataworkinggroup.com/schemas/regions/`     |
| `dc`          | `http://purl.org/dc/elements/1.1/`                         |
| `iim`          | [IPTC Information Interchange Model (IIM)](https://www.iptc.org/IIM/) |
| `exif`          | [Exchange Image File Format (EXIF)](https://web.archive.org/web/20190624045241if_/http://www.cipa.jp:80/std/documents/e/DC-008-Translation-2019-E.pdf ) |


### Title

Reference:  [IPTC Core 7.25. Title](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#title)  

| Property             | Type | Stores |
| :------------------- | :--- | :----- |
| `Iptc4xmpCore:Title` | `dc:title` | Image title |


### Description

Reference:  [IPTC Core 7.11. Description](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#description)  


| Property | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpCore:Description`| `dc:description` | Image description |

### Date

References:
 
[IPTC Core 7.10. Date Created](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#date-created)
   
| Property | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpCore:DateCreated`| `photoshop:DateCreated` | Date of depicted scene |
| `iim:DateTimeCreated` | 2:55 Date Created + 2:60 Time Created | Date of depicted scene |
| `exif:DateTimeOriginal` | 0x9003 DateTimeOriginal, 0x9011 OffsetTimeOriginal | Date of depicted scene |


### Location Shown In the Image

References:

[IPTC Extension 11.22. Location Shown in the Image](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#location-shown-in-the-image)

[IPTC Extension 12.10. Location structure](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#location-structure)


| Property | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:LocationShown` | *nested elements* | One\* location, primary focus of image |
| `nested Location Structure` | *nested elements* | One location structure |
| `   LocationShown:Sublocation` | Iptc4xmpExt:Sublocation | most specific sublocation such as address, landmark, near, probably |
| `   LocationShown:City` | Iptc4xmpExt:City | name of city |
| `   LocationShown:ProvinceState` | Iptc4xmpExt:ProvinceState | name of subregion of country such as a province or state |
| `Iptc4xmpCore:Location` | Text | Location name of one location |
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
| `Iptc4xmpExt:PersonInImage` | *nested elements* | A person |

#### Person Face Tag

| Field | Type | Stores |
| :---- | :--- | :----- |
| `mwg-rs:Regions` | *nested elements* | with RegionStuct |
