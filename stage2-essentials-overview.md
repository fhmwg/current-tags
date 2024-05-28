# FHMWG Essential Metadata Recommendation — Stage 2 Technical Summary

This document is a technical summary of the essential metadata recommendations for family history media 
which may be found at <https://github.com/fhmwg/current-tags/blob/stage2-essentials/stage2-essentials.md>. If there are any discrepancies between this summary and the full recommendations, the full recommendations apply.

This recommendation is stage 2 in the Family History Working Group (now operating under the name and website Save Metadata) roadmap and represents a further iteration on the recommendations published in stage 1. 

## Purpose

This is a recommendation for embedding family history metadata that is essential for the consistent capturing, sharing, interoperability and preservation of the data about what an image depicts – e.g. the traditional "writing on the back of the photo" – within a digital image itself in a machine-readable (non-visual) format.

There are thousands of metadata properties in use today, many of which overlap in full or in part with other properties and many of which are incompletely specified and used in inconsistent ways. The primary goal of this recommendation is to identify and clarify the meaning of a small subset of these existing properties which we have identified as essential to family history.

## Overarching principles

In the most broad terms, these recommendations assume an implementation that adheres to the following general basic principles:

1. **Don't Remove Metadata**. Even if the metadata is not a form you understand or is a duplicate of other fields, some other tool may have a use for it. Leave it in the file.

2. **At Least These Fields**. There are hundreds of metadata properties and fields available, of which we recommend using just one. You are welcome to read and write as many of the other fields as you wish, as long as you also use the one we recommend.
3. **Only Embed What You Collect**. You are only expected to embed family history data that you already collect from your users. You are not expected to collect any new data that is not already collected by your software. However, you may need to modify the *format* of the data you collect in order to align with these recommendations.

## The Metadata Pipeline

In order to aid in the implementation of our standard and overarching principles (see implementation levels further below), it is helpful to understand how we describe the stages of metadata interaction within a given software data pipeline (“system”). We define five stages of interactions that the metadata of user-generated, uploaded, imported, or contributed files (e.g. media) may experience in any given software system:

1. **RETAIN:** The first thing a system must decide is whether to retain or strip the metadata already embedded in media that is uploaded, imported or added by a user. There are many reasons for a business to choose to strip the metadata, from reducing file size, to protecting privacy, to managing liability. By retaining metadata, the system makes no modification to the original file or its metadata and stores that original for the duration of their relationship with the user or owner of that file.

2. **READ:** A system may read some or all of the embedded file metadata into a separate file, database or other data structure. At this step, the metadata is no longer bound uniquely to the original file and can be manipulated by the system independently. Reading metadata does not imply metadata retention. A given system might both read and then strip metadata.

3. **PRESENT:** The system can present the metadata stored in the database to an end user. The system can be designed to present only a subset of available metadata fields to and can modify the format of the metadata for presentation purposes.
   
4. **EDIT:** A system may allow a user to make changes to the metadata, whether outside of the original file or embedded within it. Typically, user-specified changes to metadata are stored first in some data structure, whether temporarily or indefinitely, and then potentially embedded into the file.
   
5. **SEARCH:** A system may allow users to search for files based on metadata fields associated with the file, either in a separate data structure or embedded within the file.

## Core Goals

Any individual software system will have a metadata pipeline that achieves some if not all of these stages for some if not all possible metadata fields. However, the goal of the FHMWG essentials recommendations is to foster an interoperable ecosystem of software products that go beyond this internal pipeline and achieve two additional goals for image files specifically:
1. **INTEROPERATE:** systems must interoperate so that users can import or export files and their metadata following shared conventions for file and metadata formats. Metadata interoperability may be achieved by adherence to existing metadata standards for reading and writing metadata as well as any method of information transfer including but not limited to direct API access, exportable metadata structures (.csv, .xls, .json, etc.), embedding edited metadata into original files, etc.
   
2. **EMBED** systems must have the ability to write metadata stored outside of the original files in independent data structures back into the original files themselves. e.g. interoperability is achieved via file embedded metadata.
If the metadata is embedded then when a user downloads the photo it travels with the photo. This is the ultimate step for digital preservation.

## Implementation Levels

No matter how thoroughly you choose to implement these FHMWG standards, you are helping to enhance the value of digital media for family history provided that you follow our overarching principles and the implementation guidance below.
We have defined three levels to guide service providers to implement our recommendations. The goal is for providers to work towards reaching the highest level of implementation by starting from the lowest. Providers already close to achieving the requirements of a given level should ideally work towards coverage of that level before moving up to the next level.

**Level 1: Preserve Metadata**

The most accessible level that any software system developer can achieve is to retain and preserve the metadata already present in a file provided by a user. A system at the preserve level retains the original file metadata undisturbed in whatever state it might exist, whether or not it matches our recommended structure. This step is absolutely critical to the preservation mission of the FHMWG.

**Level 2: Present Metadata**

The next level requires the system to read metadata that is already present in the file provided by a user and present it to the user via a user interface. At this level the system may choose to read data from any fields it chooses. However, the system should use the metadata fields in these recommendations when storing the data, enabling search and when presenting it back to the user.

**Level 3: Put Metadata Back**

In this final level, the system allows the user to edit metadata, saves any changes made by the user, and writes the changes back into the files using the metadata fields in these recommendations, e.g. the metadata is embedded. These fields must be the ones presented to the user in the user interface when accepting edits. 

**NOTES:** 
* Information about the implementation level of the system should be made available to the user via support documentation as each level has a unique set of consequences that is important for the user to understand.
* Each level is inclusive of the last. Level 2 assumes level 1 preservation steps are being taken to make sure existing metadata is not lost. Level 3 assumes level 2 presentation steps are being taken to present metadata using our recommendations.
* It is not required that metadata be embedded after every user edit. Metadata may be retained by the system provided that they are embedded into the files at the point when files are exported from the system. 
* The user may be presented with options to determine whether the system retains or embeds metadata, overriding any system presets or defaults.



## General Background on Image Metadata

Photo metadata may be written in one of three formats:

* [Exchange Image File Format (EXIF)](https://web.archive.org/web/20190624045241if_/http://www.cipa.jp:80/std/documents/e/DC-008-Translation-2019-E.pdf), standards for devices (like cameras and scanners) that embed metadata. 
* [IPTC Information Interchange Model (IIM)](https://www.iptc.org/IIM/), IPTC's first multi-media news exchange format. 
* [Extensible Metadata Platform (XMP)](https://www.adobe.com/products/xmp.html), an ISO standard for embedding metadata in a format that cane be embedded, read, and interpreted consistently and is also extensible.

EXIF metadata is the oldest format and is written as an offset in the file with a known length that was agreed upon among device manufacturers.  This format does not allow the set of metadata to be extended.  It also requires that all the fields fit within the length given and either truncated or padded to make them fit.

IIM metadata allowed more properties to be stored in the file and stored than as key, value pairs.  It duplicated some of the core EXIF data in addition to adding new fields, but did not allow new properties to be added.  Best practices recommended synching the IIM and EXIF properties that stored the same data.

XMP format was developed by Adobe as an open standard and then adopted as an ISO standard.  In addition to storing the data as key, value pairs, it allowed for extending the metadata properties by adding new schema definitions.  IPTC defined its IIM metadata as core XMP metadata properties and recommended synching between the XMP IPTC core fields and the corresponding IIM and EXIF data.

The [International Press Telecommunications Council (IPTC)](https://iptc.org/) is the global standards body of the news media and publishes the [IPTC Photo Metadata Standard](https://iptc.org/standards/photo-metadata/iptc-standard/).  The IPTC Photo Metadata Standard is recognized and accepted industry wide and provides guidance on how to use IPTC defined XMP metadata in a way that achieves interoperability.

## Essential Family History Metadata Compatibility

The essential family history metadata recommendations closely follow the IPTC Photo Metadata Standard which provides clarity on how to read, write, and synchronize standard photo metadata. Most of the metadata that is important to family historians is also core to the IPTC photo metadata and has semantically equivalent metadata properties in the IIM and EXIF format.  IPTC provides [guidelines for mapping](https://iptc.org/std/photometadata/documentation/mappingguidelines/) between its XMP data and the older IIM and EXIF formats.  Additionally, IPTC publishes [interoperability tests](https://iptc.org/standards/photo-metadata/interoperability-tests/) to validate that the embedded metadata is written correctly and consistently to the IPT standards.

This recommendation leverages and remains in accordance with existing popular metadata standards and guidelines that promote interoperability between software and formats for preserving the "writing on the back of the photo" which includes these elements:
* Title
* Description
* Date 
* Location (names and geotags)
* People (names and face tags)

In general, we require one XMP property per element and recommend synching it to the appropriate IIM or EXIF properties also, in accordance with the IPTC Photo Metadata Standard.  The goal is to define a consistent way to capture, share, and preserve these elements of essential family history metadata so that it can be consistently read and interpreted by software applications, even if the metadata was originally captured in one of the older (IIM or EXIF) format and not in the XMP format.

## A Note on Exiftool Handles

Keeping metadata properties straight can be a challenge.  The names are similar and it's not always clear which property is actually being written.

Exiftool is an open-source software for metadata management that uses handles for metadata fields based on groups and tag names to uniquely identify metadata properties. The exiftool handle is provided for each metadata field using the group and tag name displayed when using the -G -s options for exiftool.  Exiftool group names are EXIF, IPTC (for IIM properties), and XMP.  If the tag names are part of a structure, the tag name is flattened so that each field has a unique name.

The exiftool handle is an easy way to keep the metadata straight, with one set of names that can be used to document, read, and write metadata properties.

## Recommendation Summary

### Title

An image may have a title.

The title should be a short human-readable name or reference for the digital file. 

### Description

An image may have a description.

The description may be of any length and contain any information the user cares to add. The description could also include a caption for the image.

### Date

An image can record the date of the depicted scene. The precision and accuracy of the date may vary, e.g. the date may only be specified by a year, or a month within a year, etc.

Note that this is the date of the scene depicted in the image, and may not be the date of the image’s creation. While the depicted date and the creation date may be the same for digital photographs, they generally differ for scanned images or artistic representations.

Many other date fields exist in other metadata, such as image creation dates, image modification dates, etc. As with all metadata, implementations may choose to support those if they wish, but this recommendation only addresses the date of the depicted scene.

### Location (Names and GeoTags)

An image can identify one location where the primary focus of the depicted scene is found. 

A location can have names such as a full name, identifier, sublocation, city, state, country, as well as GPS coordinates. At least one of the location name elements are required. Location identifier and GPS coordinates are optional.

IPTC allows storing more than one location in a single image's metadata. Because the meaning of multiple locations is not uniformly understood, we recommend against using multiple locations.

Many other location properties exist in the IPTC standard. As with all metadata, implementations may choose to support those if they wish, but this recommendation only includes fields in the IPTC locationShownInImage property. Best practices recommend synching the location elements with the appropriate core XMP properties as well as IIM and EXIF properties.

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
   
| Required Property             | Type | Exiftool Handle | Stores |
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

The location is stored in a structure that contains a full name for the location, which may contain jurisdictions (such as county) that are not supported in the other jurisdiction name fields.
There is only one location represented in the LocationShownInImage structure, so all the subfields should be consistent.  That is, GPS info, location names, and identifiers should represent the same physical location.
Within the LocationShownInImage structure, a location may be defined by at least one of the following criteria
* the full name of the location in LocationName, 
* at least one of the jurisdictional locations such as sublocation/address, city, state, country, or
* GPS coordinates.

![LocationNamesAndGeotags](https://github.com/fhmwg/current-tags/assets/702496/d4edf519-aac8-4ab7-8644-2c295c0f5f20)


#### Location - Names and Identifier

FamilySearch uses the LocationName and LocationId with its place authority standards. 
If you are not using the LocationName, use the sublocation, city, state, and country fields in the location structure in LocationShownInImage and sync them to the appropriate IIM and EXIF fields.

Required:  In the LocationShownInImage property, the LocationName and/or at least one of Sublocation, City, ProvinceState, and Country fields are required.


| Property             | Type | Exiftool Handle | Stores |
| :------------------- | :--- | :-----          |:---|
| `Iptc4xmpExt:LocationShown` | *nested elements* | |One\* location, primary focus of image |
| `  Location Structure` | *nested structure* | |Structure containing identifiers, names, GPS data |
| `   LocationShown:Sublocation` | Iptc4xmpExt:Sublocation | XMP:LocationShownSublocation|most specific sublocation such as address, landmark, near, probably |
| `   LocationShown:City` | Iptc4xmpExt:City |XMP:LocationShownCity| name of city |
| `   LocationShown:ProvinceState` | Iptc4xmpExt:ProvinceState|XMP:LocationShownProvinceState | name of subregion of country such as a province or state |
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

GPS coordinates always identify a specific point corresponding to a complete address with jurisdictional names. However, many family photos do not have a specific address. Therefore GPS coordinates are not required if the LocationShown property is used.

If a GPS coordinate is specified, the location names fields may still be used to indicate the precision or accuracy of the GPS coordinates. For example, if only the state is specified in the LocationShown property, the GPS coordinates may be erroneously indicating an overspecified point in the state. The jurisdictional name fields in the LocationShown property would not have a sublocation/address or city, indicating that only the state is known. Additionally using near or probably in the sublocation field would also indicate the intended precision of the GPS coordinates.

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

People can be identified by face tag or by name. **Person names are required.**  Face tags are optional, but if used, should be synced with the list of person names in the image.  

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
