# FHMWG Essential Metadata Recommendation 1 — Technical Summary

This document is a technical summary of the essential metadata recommendations for family history media 
which may be found at <https://github.com/fhmwg/current-tags/blob/master/essential.md>. If there are any discrepancies between this summary and the full recommendations, the full recommendations apply.

## Purpose

This is a minimum standard for embedding family history metadata that enables the consistent capturing, sharing, interoperability and preservation of the "writing on the back of the photo" within the digital image itself in a machine-readable (non-visual) format.

Photo metadata may be writtten in one of three formats:

* [Exchange Image File Format (EXIF)](https://web.archive.org/web/20190624045241if_/http://www.cipa.jp:80/std/documents/e/DC-008-Translation-2019-E.pdf), standards for devices (like cameras and scanners) that embed metadata. 
* [IPTC Information Interchange Model (IIM)](https://www.iptc.org/IIM/), IPTC's first multi-media news exchange format. 
* [Extensible Metadata Platform (XMP)](https://www.adobe.com/products/xmp.html), an ISO standard for embedding metadata in a format that cane be embedded, read, and interpreted consistently and is also extensible.

EXIF metadata is the oldest format and is written as an offset in the file with a known length that was agreed upon among device manufacturers.  This format does not allow the set of metadata to be extended.  It also requires that all the fields to fit within the length given and either truncated or padded to make them fit.

IIM metadata allowed more properties to be stored in the file and stored than as key, value pairs.  It duplicated some of the core EXIF data in addition to adding new fields, but did not allow new properties to be added.  Best practices recommended synching the IIM and EXIF properties that stored the same data.

XMP format was developed by Adobe as an open standard and then adopted as an ISO standard.  In addition to storing the data as key, value pairs, it allowed for extending the metadata properties by adding new schema definitions.  IPTC defined its IIM metadata as core XMP metadata properties and recommended synching between the XMP IPTC core fields and the corresponding IIM and EXIF data.

There are thousands of metadata fields in use in the wild today, many of which overlap in full or in part with other fields and many of which are incompletely specified and used in inconsistent ways.  The [International Press Telecommunications Council (IPTC)](https://iptc.org/) is the global standards body of the news media and publishes the [IPTC Photo Metadata Standard](https://iptc.org/standards/photo-metadata/iptc-standard/).  The IPTC Photo Metadata Standard is recgonized and accepted industry wide and provides guidance on how to use IPTC defined XMP metadata in a way that achieves interoperability.

The essential family history metadata recommendations closely follow the IPTC Photo Metadata Standard which provides clarity on how to read, write, and synchronize standard photo metadata. Most of the metadata that is important to family historians is also core to the IPTC photo metadata and has semantically equivalent metadata properties in the IIM and EXIF format.  IPTC provides [guidelines for mapping](https://iptc.org/std/photometadata/documentation/mappingguidelines/) between its XMP data and the older IIM and EXIF formats.  Additonally, IPTC publishes [interopability tests](https://iptc.org/standards/photo-metadata/interoperability-tests/) to validate that the embedded metadata is written correctly and consistently to the IPT standards.

The primary goal is to provide recommendations around sharing essential family history metadata in accordance with existing popular metadata standards and guidelines that promote interoperability between software and formats for preserving the "writing on the back of the photo" which includes these elements:
* Title
* Description
* Date 
* Location (names and geotags)
* People (names and face tags)

In general, we require one XMP property per element and recommend synching it to the appropriate IIM or EXIF properties also, in accordance with the IPTC Photo Metadata Standard.  The goal is to define a consistent way to capture, share, and preserve these elements of essential family history metadata so that it can be consistently read and interpreted by software applications, even if the metadata was originally captured in one of the older (IIM or EXIF) format and not in the XMP format.


## Overarching principles

1. **Don't Remove Metadata**. Even if the metadata is not a form you understand or is a duplicate of other fields, some other tool may have a use for it. Leave it in the file.

2. **At Least These Fields**. There are hundreds of metadata properties and fields available, of which we recommend using just one. You are welcome to read and write as many of the other fields as you wish, as long as you also use the one we recommend.

## Summary of recommendation

### Title

An image may have a title.

The title should be a short human-readable name or reference for the digital file. 

### Description

An image may have a description.

The description may be of any length and contain any information the user cares to add. The description could also include a caption for the image.

### Date

An image can record the date of the depicted scene which may be just a year or a year and month.

Note that this is the date of the scene, not the date of the image creation;
while those may be the same for photographs, they generally differ for scanned images.

Many other date fields exist in other metadata, such as image creation dates, image modification dates, etc. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes the date of the depicted scene.


### Location (Names and GeoTags)

An image can identify one location where the primary focus of the depicted scene is found. 

A location can have names such as a full name, identifier, sublocation, city, state, country, as well as GPS coordinates. At least one of the location name elements are required. Location dentifier and GPS coordinates are optional.

IPTC allows storing more than one location in a single image's metadata. Because the meaning of multiple locations is not uniformly understood, we recommend against using multiple locations.

Many other location properties exist in the IPTC standard. As with all metadata, implementations may chose to support those if they wish, but this recommendation only includes fields in the IPTC locationShownInImage property. Best practices recommend synching the location elements with the appropriate core XMP properties as well as IIM and EXIF properties.

GPS coordinates always identify a single precise point, but real locations may cover a larger area or be imprecisely located.  The location names can help convey the scope of the GPS coordinates.

### People (Names and Face Tags)

An image may depict persons either visually in the image or by close association (i.e. my grandpa's house is associated with my grandpa even though grandpa is not visually in the photo). 

The image may have a list of person names associated with the image.

A person in an image may be referenced by a face tag (coordinates within the image). Person face tags are encouraged, but optional. In addition to the face tag, the person name must be added to the list of persons in the image.


## Metadata properties

The key metadata properties used in this recommendation are listed below.
Low-level details are not provided in this summary, but reference links to the appropriate standards definition are provided for further information.

What follows uses the following namespace abbreviations for XMP metadata :

| Prefix            | Namespace URI                                              |
| :------------     | :--------------------------------------------------------- | 
| `Iptc4xmpExt`     | `http://iptc.org/std/Iptc4xmpExt/2008-02-29/`              |
| `Iptc4xmpCore`    | `http://iptc.org/std/Iptc4xmpCore/1.0/xmlns/`              |
| `mwg-rs`          | `http://www.metadataworkinggroup.com/schemas/regions/`     |
| `dc`          | `http://purl.org/dc/elements/1.1/`                         |
| `exif`  |  `http://ns.adobe.com/exif/1.0/` |

In addition to the XMP metadata, there are semantically equivalent properties in the Information Interchange Model (IIM) and Exchange Image File Format (EXIF) sections which may be kept in sync with the XMP data.  The following abbreviations are used for IIM and EXIF metadata.
| Prefix            | Reference   |
| :------------     | :--------------------------------------------------------- |    
| `IIM`          | [IPTC Information Interchange Model (IIM)](https://www.iptc.org/IIM/) |
| `EXIF`          | [Exchange Image File Format (EXIF)](https://web.archive.org/web/20190624045241if_/http://www.cipa.jp:80/std/documents/e/DC-008-Translation-2019-E.pdf ) |


### Title

Reference:  [IPTC Core 7.25. Title](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#title)  

| Property             | Type | Exiftool Handle | Stores |
| :------------------- | :--- | :----- |:---|
| `Iptc4xmpCore:Title` | `dc:title` | XMP:Title |Image title |

Optional semantically equivalent IIM field to sync with Title

| Optional field   | Specification | Exiftool Handle |
| :------------------- | :--- | :----- |
| `IIM:ObjectName` | 2:05 Object Name |IPTC:ObjectName |




### Description

Reference:  [IPTC Core 7.11. Description](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#description)  


| Required Property  | Type | Exiftool Handle | Stores |
| :------------------- | :--- | :----- | :--- |
| Iptc4xmpCore:Description| dc:Description |XMP:Description  | Image description |

Optional semantically equivalent IIM and EXIF fields to sync with Description

| Optional Property | Specification |Exiftool Handle |
| :---- | :--- | :----- |
| `IIM:Caption-Abstract` | 2:120 Caption-Abstract | IPTC:Caption-Abstract|
| `EXIF:ImageDescription` | 270/0x010E ImageDescription | EXIF:ImageDescription|



### Date

Reference:  [IPTC Core 7.10. Date Created](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#date-created)
   
| Property             | Type | Exiftool Handle | Stores |
| :------------------- | :--- | :----- |:---|
| `Iptc4xmpCore:DateCreated`| `photoshop:DateCreated` | XMP:DateCreated| Date of depicted scene |

Recommended semantically equivalent IIM and EXIF fields to sync with Date 

| Optional Property | Specification | Exiftool Handle |
| :---- | :--- | :----- |
| `IIM:DateTimeCreated` | 2:55 Date Created + 2:60 Time Created | IPTC:DateCreated & IPTC:TimeCreated |
| `EXIF:DateTimeOriginal` | 0x9003 DateTimeOriginal | EXIF:DateTimeOriginal|


### Location 

References:

[IPTC Extension 11.22. Location Shown in the Image](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#location-shown-in-the-image)

[IPTC Extension 12.10. Location structure](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#location-structure)

The location is stored in a structure that contains a full name for the location, which may contain jurisdictions (such as county) that are not supported in the other jurisidiction name fields.
There is only one location represented in the LocationShownInImage structure, so all the subfields should be consistent.  That is, GPS info, location names, and identifiers should represent the same physical location.
Within the LocationShownInImage structure, a location may be defined by at least one of the following criteria
* the full name of the location in LocationName, 
* at least one of the jurisdictional locations such as sublocation/address, city, state, country, or
* GPS coordinates.

![LocationNamesAndGeotags](https://github.com/fhmwg/current-tags/assets/702496/d4edf519-aac8-4ab7-8644-2c295c0f5f20)


#### Location - Names and Identifier

FamilySearch uses the LocationName and LocationId with its place authority standards. 
If you are not using the LocationName, use the sublocation, city, state, and country fields in the location structure in LocationShownInImage and synch them to the approprioate IIM and EXIF fields.

Required fields in LocationShownInImage LocationName and/or at least one of Sublocation, City, ProvinceState, and Country fields


| Property             | Type | Exiftool Handle | Stores |
| :------------------- | :--- | :-----          |:---|
| `Iptc4xmpExt:LocationShown` | *nested elements* | |One\* location, primary focus of image |
| `  Location Structure` | *nested structure* | |Structure containing identifiers, names, GPS data |
| `   LocationShown:Sublocation` | Iptc4xmpExt:Sublocation | XMP:LocationShownSublocation|most specific sublocation such as address, landmark, near, probably |
| `   LocationShown:City` | Iptc4xmpExt:City |XMP:LocationShownCity| name of city |
| `   LocationShown:ProvinceState` | Iptc4xmpExt:ProvinceState|XMP:LocationShownProvinceState | name of subregion of cuntry such as a province or state |
| `   LocationShown:CountryName` | Iptc4xmpExt:CountryName |XMP:LocationShownCountryName| name of the country |
| `   LocationShown:LocationName` | Iptc4xmpExt:LocationName|XMP:LocationShownLocationName | full name of location, including county if desired |
| `   LocationShown:LocationIdentifier` | Iptc4xmpExt:LocationId |XMP:LocationShownLocationId| globally unique identifier such as FamilySearch place identifer, may have more than 1 |

Recommended semantically equivalent XMP and IIM fields to sync

References:

[IPTC Core 7.24. Sublocation (legacy)](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#sublocation-legacy)

[IPTC Core 7.2. City (legacy)](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#city-legacy)

[IPTC Core 7.19. Province or State (legacy)](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#province-or-state-legacy)

[IPTC Core 7.4. Country (legacy)](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#country-legacy)


| Field | Sync With | Specification | Exiftool Handle | 
| :---- | :--- | :----- | :----- |
| LocationShown:Sublocation |`Iptc4xmpCore:Sublocation (legacy)` | `Iptc4xmpCore:Location` |XMP:Location|
| LocationShown:City | `Iptc4xmpCore:City (legacy)`  | `photoshop:City` | XMP:City |
| LocationShown:ProvinceState |`Iptc4xmpCore:Province or State(legacy)`  | `photoshop:State`  | XMP:State |
| LocationShown:CountryName | `Iptc4xmpCore:Country (legacy)`  | `photoshop:Country` | XMP:City |
|Iptc4xmpCore:Sublocation (legacy) |`IIM:Sublocation`  | `2:92 Sublocation` |  IPTC:Sub-location |
|Iptc4xmpCore:City (legacy) | `IIM:City`  | `2:90` | IPTC:City |
|Iptc4xmpCore:Province or State (legacy) |`IIM:Province/State`  | `2:95 Province/State` | IPTC:Province-State |
|Iptc4xmpCore:Country (legacy) | `IIM:Country/Primary Location Name`  | `2:101 Country/Primary Location Name`  | IPTC:Country-PrimaryLocationName|

#### Location - Geo Tags

GPS coordinates always identify a specific point, however, many family photos do not have a specific address, just a city or state.  In this case, the location names fields may indicate the scope of the GPS coordinates.  For example, if only the state is known, the GPS coordinates will indicate a specific point in the state, but the jurisdictional names would not have a sublocation/address or city, indicating that only the state is known. Additionally using near or probably in the sublocation field would also indicate the intended precision of the GPS coordinates.

| Property             | Type | Exiftool Handle | Stores |
| :---- | :--- | :----- | :----- |
| `Iptc4xmpExt:LocationShown` | *nested elements* | |One\* location, primary focus of image |
| `  Location Structure` | *nested structure* | |Structure containing identifiers, names, GPS data |
| `    LocationShown:GPS-Latitude` | exif:GPSLatitude |XMP:LocationShownGPSLatitude| GPS Latitude|
| `    LocationShown:GPS-Longitude` | exif:GPSLongitude | XMP:LocationShownGPSLongitude| GPS Longitude |

Recommended semantically equivalent EXIF fields to sync with LocationShown geotags:

References:

[XMP Namespaces > exif](https://developer.adobe.com/xmp/docs/XMPNamespaces/exif/)

|Field | Sync With | Specification | Exiftool Handle |
| :---- | :--- | :----- |:---|
| LocationShown:GPS-Latitude | `EXIF:GPSLatitudeRef`  | `Tag 0x0001` GPSLatitudeRef | EXIF:GPSLatitudeRef|
|                            | `EXIF:GPSLatitude`  | `Tag 0x0002` GPSLatitude  | EXIF:GPSLatitude |
| LocationShown:GPS-Longitude | `EXIF:GPSLongitudeRef` |  `Tag 0x0003` GPSLongitudeRef  | EXIF:GPSLongitudeRef|
|                             |`EXIF:GPSLongitude`  | `Tag 0x0004` GPSLongitude | EXIF:GPSLongitude |


\* IPTC allows more than one location in an image, but the FHMWG recommends only one be used

### People (Names and Face Tag)

People can be identified by face tag or by name. Person names are required.  Fact tags are optional, but if used, should be synched with the list of person names in the image.  

Face tags are written as image regions.  MWG (Metadata Working Group) Guidelines for Handling Image Metadata added image region properties in 2010, which have been widely used in the industry to record face tags in images.  IPTC image regions were added about 10 years later in 2019.  Although MWG appears to be a stale entity at this time, MWG regions are still the de facto standard used by Adobe products and everyone else.  For interoperability, we are recommending that we use the de facto standard.  

![People Face Tags and Names](https://github.com/fhmwg/current-tags/assets/702496/34e3bfae-0dc2-4e7d-99d5-73384d730885)

#### Person Names

Reference:  [IPTC Extension 11.30. Person Shown in the Image](https://iptc.org/std/photometadata/specification/IPTC-PhotoMetadata#person-shown-in-the-image)

| Property             | Type | Exiftool Handle | Stores |
| :---- | :--- | :----- | :----- |
| `Iptc4xmpExt:PersonShownInTheImage` |`Bag of Iptc4xmpExt:PersonInImage`  | XMP:PersonInImage| list of names of persons in image, including those with face tags |

#### Person Face Tag

Reference:  [Metadata Working Group Regions schema](https://exiv2.org/tags-xmp-mwg-rs.html)
Documentation:   MWG Regions of type “Face” in
 [Guidelines for Handling Image Metadata](https://web.archive.org/web/20120131102845/http://www.metadataworkinggroup.org/pdf/mwg_guidance.pdf) page 53

| Property             | Type | Exiftool Handle | Stores |
| :---- | :--- | :----- | :----- |
| `mwg-rs:Regions` | *nested elements* | XMP:Region*| list of coordinates and names for face tags in image |

Recommended semantically equivalent XMP fields to sync with face tags:

| Property | SyncWith  | Stores |
| :---- | :--- | :----- |
| `Iptc4xmpExt:PersonShownInTheImage` |`Bag of Iptc4xmpExt:PersonInImage`  | list of names of persons in image, including those with face tags |
