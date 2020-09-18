# FHMWG REC 1 — Summary

## Purpose

This is a standard for embedded metadata in images:
that is, data about what the image depicts that are stored in machine-readable (non-visual) format within the image file itself.

There are thousands of metadata fields in the use in the wild today, many of which overlap in full or in part with other fields and many of which are incompletely specified and used in inconsistent ways. The primary goal of this recommendation is to identify and clarify the meaning of a small subset of these existing fields which we recommend as core to family history applications of metadata.

This recommendation makes use of XMP, a standard produced by Adobe for storing RDF/XML in image metadata; and IPTC, which provides one of the more popular RDF vocabularies for XMP. Because those are complicated and nuanced technologies, we describe the resulting standard only in terms of XML; it is our intent that a full understanding of RDF, XMP, and IPTC is not necessary to implement these recommendations.

## Overarching principles

1. **Don't Remove Metadata**. Even if the metadata is not a form you understand or is a duplicate of other fields, some other tool may have a use for it. Leave it in the file.

2. **At Least These Fields**. There are hundreds of date fields available, of which we recommend using just one. You are welcome to read and write as many of the other fields as you wish, as long as you also use the one we recommend.

3. **IDs are IRIs**. Internationalized Resource Identifiers (IRIs) are a polyglot extension to URIs, themselves an extension to URLs to allow storing identifiers that are not web pages. Since names, dates, etc are not enough to uniquely identify people and places, IRIs are recommended to make matching people between multiple images less error-prone. Because URLs are valid IRIs, they also allow linking to external resources.

## Summary of recommendation

### Albums

An image can belong to any number of albums.
Each album has a name, an IRI, or both.

### Caption

An image may have a title and/or a description.
The title should be a short descriptive title of the image itself.
The description may be of any length and contain any information the user cares to add.

### Date

An image can record the date of the depicted scene.
Note that this is the date of the scene, not the date of the image creation;
while those may be the same for photographs, they generally differ for illustrations.

Many other date fields exist in other metadata, such as image creation dates, image modification dates, etc. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the date of the depicted scene.

### Event

An image can identify one event, a scene of which the image depicts.
Events are stored as free text with no internal structure.

### Location

An image can identify one location where the primary focus of the depicted scene is found.
A location may have a latitude/longitude pair, a name, and/or an IRI.

Many other location properties exist in the IPTC standard, such as the political jurisdictional hierarchy of city/state/country, etc. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the GPS coordinates, name, and ID of a location.

IPTC allows storing more than one location in a single image's metadata. Because the meaning of multiple locations is not uniformly understood, we recommend against using multiple locations.

GPS coordinates always identify a single precise point, but real locations may cover a larger area or be imprecisely located. Coordinate regions and imprecision may be added in a future version of this recommendation.

### Objects and People

The FHMWG recommends that when tagging people or objects in an image, each be tagged with a region of the image. IPTC provides a daunting level of flexibility in region tagging; the FHMWG recommends a simple subset of this, where each person or object is inside its own region. A special "whole image" region is defined for tools that lack regional tagging functionality and for tagging people and objects relevant to, but not depicted within, the image.

An image can identify any number of image regions.
Each region should have either one person or one object.
Each object has a short free-text description.
Each person has a name and/or a description and/or any number of IRIs.

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


### Album

| Field | Type | Stores |
| :---- | :--- | :----- |
| `mwg-coll:Collections` | *nested elements* | Any number of albums |
| `mwg-coll:CollectionName` | Text | Name of one album |
| `mwg-coll:CollectionURI` | IRI | Identifier of one album |

### Caption

| Field | Type | Stores |
| :---- | :--- | :----- |
| `dc:description` | Text | Image caption |
| `dc:title` | Text| Image title |

### Date

| Field | Type | Stores |
| :---- | :--- | :----- |
| `photoshop:DateCreated` | DateTime | Date of depicted scene |

### Event

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:Event` | Text | Description of event depicted scene is a part of |

### Location

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:LocationShown` | *nested elements* | One\* location |
| `exif:GPSLatitude` | Number | Degrees north of equator of one location |
| `exif:GPSLongitude` | Number | Degrees east of prime meridian of one location  |
| `Iptc4xmpExt:LocationName` | Text | Full name of one location |
| `Iptc4xmpExt:LocationId` | IRI | Identifier of one location |

### Objects and People

#### Image Regions

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:ImageRegion` | *nested elements* | Any number of image regions |
| `Iptc4xmpExt:RegionBoundary` | *nested elements* | A region boundary |


#### Objects

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:ArtworkOrObject` | *nested elements* | An object |
| `Iptc4xmpExt:AOTitle` | Text | The short descrition or title of the object |

#### People

| Field | Type | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:PersonInImageWDetails` | *nested elements* | A person |
| `Iptc4xmpExt:PersonName` | Text | The name of one person |
| `Iptc4xmpExt:PersonDescription` | Test | The description of one person |
| `Iptc4xmpExt:PersonId` | IRI | An identifier of one person |





