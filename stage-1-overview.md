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

The relevant vocabulary prefix is `http://www.metadataworkinggroup.com/schemas/collections/`;
the relevant structure names are `Collections`, `CollectionName`, and `CollectionURI`.

### Caption

An image may have a title and/or a description.
The title should be a short descriptive title of the image itself.
The description may be of any length and contain any information the user cares to add.

The relevant vocabulary is the Dublin Core, with prefix `http://purl.org/dc/elements/1.1/`;
the relevant structure names are `title`, `description`.

### Date

An image can record the date of the depicted scene.
Note that this is the date of the scene, not the date of the image creation;
while those may be the same for photographs, they generally differ for illustrations.

Many other date fields exist in other metadata, such as image creation dates, image modification dates, etc. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the date of the depicted scene.

The relevant vocabulary is Adobe Photoshop's schema, with prefix `http://ns.adobe.com/photoshop/1.0/`;
the relevant structure name is `DateCreated`.

### Event

An image can identify one event, a scene of which the image depicts.
Events are stored as free text with no internal structure.

The relevant vocabulary is the IPTC Extensions schema, with prefix `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`;
the relevant structure name is `Event`.

### Location

An image can identify one location where the primary focus of the depicted scene is found.
A location may have a latitude/longitude pair, a name, and/or an ID.

Many other location properties exist in the IPTC standard, such as the political jurisdictional hierarchy of city/state/country, etc. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the GPS coordinates, name, and ID of a location.

IPTC allows storing more than one location in a single image's metadata. Because the meaning of multiple locations is not uniformly understood, we recommend against using multiple locations.

GPS coordinates always identify a single precise point, but real locations may cover a larger area or be imprecisely located. Coordinate regions and imprecision may be added in a future version of this recommendation.

Locations use terms from two vocabularies.
The IPTC Extensions schema, with prefix `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`,
provides structure names `LocationShown`, which contains `LocationName` and `LocationId` as well as GPS coordinates.
The EXIF schema, with prefix `http://ns.adobe.com/exif/1.0/`,
provides structure names `GPSLatitude` and `GPSLongitude`.


### Objects and People

**TO DO: resume writing here**
