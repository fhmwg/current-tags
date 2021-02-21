# Outdated draft

This document has been superseded by [stage1.md](stage1.md).

# A guide to image metadata

This is a **preliminary draft** representing my personal first-pass reading and has not been tested to ensure it is accurate.

## Overview

Common raster image file formats (such as JPEG, PNG, etc) contain three broad categories of data:

- Header information, which describes how the image data is structured to represent pixels. This generally includes the width and height of the image in pixels; the color pallette used; and the compression scheme.
- Image data, which defines the color of each pixel in the raster.
- Metadata, which provides other data about the image.

Metadata can be organized in many ways, but a common organization is to have a set of key-value pairs. EXIF is one common format for these pairs, using 16-bit integers as keys with some numeric and some string values, depending on the key. Key-value pairs are attractive because it is simple, allowing image capturing hardware to set appropriate fields automatically. As such, the specification of the keys tends to focus on things the cameras or scanners can determine or can be configured to set: focal length, exposure time, date and time of capture, capture device manufacturer, GPS coordinates of device, device owner, saved-to file name, etc.

There are usually also a few fields set aside for more human-moderated purposes. Many of these have been motivated by the professional photographer market, including things like news category codes, copyright and other rights tracking, etc. Included in these are typically three more open-ended fields of note:

- Title, a generally short description of the image
- Description or comment, a longer free-text field in which anything might appear
- XMP field, whose value is an entire XML document

The XMP contains an XML document, which theoretically can be any XML at all. However, it is almost universally [RDF XML](https://www.w3.org/TR/rdf-syntax-grammar/), a syntax for describing arbitrary graph data (in the computing sense of graph being nodes and edges),
and usually used in one of several limited RDF standards.
IPTC is one such standard that has particularly good penetration.

Because XMP is in XML, it can have more elaborate structure than simple key-value pairs; for example, EXIF can refer to "the artist"'s name, but XMP can have any number of creators, each with their own job title, etc. Hence, XMP (in particular, the IPTC subset of XMP) is commonly used when humans are adding more interesting metadata.

Summary

- Most image file formats have some key:value pair metadata
- If capture devices create metadata, it tends to go there
- Some keys may have human-targeted values, like titles and descriptions
- One particular key is used to store an XML document as metadata
- That XML can be in many formats, but IPTC is particularly common
- IPTC fields have more flexibility than simple key:value pairs, meaning they are commonly used for human-created metadata

## Example

Consider the task of tagging two people in an image.

IPTC defines an [Image Region Structure](https://www.iptc.org/std/photometadata/specification/IPTC-PhotoMetadata-2019.1.html#image-region-structure)
which can have a [Region Boundary](https://www.iptc.org/std/photometadata/specification/IPTC-PhotoMetadata-2019.1.html#region-boundary) and arbitrary other metadata.
It also defines a [Person Shown in the Image](https://www.iptc.org/std/photometadata/specification/IPTC-PhotoMetadata-2019.1.html#person-shown-in-the-image), which contains their names as text,
as well as a [Person structure](https://www.iptc.org/std/photometadata/specification/IPTC-PhotoMetadata-2019.1.html#person-structure) which can have their name, a physical description, a canonical identifier like a URL for the person, etc.

Let's try putting this all together.
First, this is XML so we need a single outer tag;
since its RDF XML, that outer tag is 

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <!-- content goes here -->
</rdf:RDF>
```

RDF XML uses the notion of a non-RDF tag being a node
and it containing an `rdf:Description` for any properties it might have.
IPTC often uses a "bag" structure to allow multiple copies of a property,
serialized with an `rdf:Bag` containing an `rdf:li` for each item.
IPTC also uses other namespaces for properties if they are known,
so we'll commonly have multiple namespaces in the XML.

Let's see what an empty description might look like:

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description rdf:about=''
  xmlns:Iptc4xmpExt='http://iptc.org/std/Iptc4xmpExt/2008-02-29/'
  xmlns:dc='http://purl.org/dc/elements/1.1/'
  xmlns:xmp='http://ns.adobe.com/xap/1.0/'>
 </rdf:Description>
</rdf:RDF>
```

Note we've added three namespaces:
`xmlns:Iptc4xmpExt` is used for IPTC-created ideas like regions;
`xmlns:dc` is used for the Dublin core, which is the kind of metadata library catalogs generally want;
and `xmlns:xmp` is used for Adobe's XMP core tags, mostly replicating the kinds of things we'd find in EXIF or the like.
ITPC also sometimes uses a dozen other namespaces, such as

- `http://ns.adobe.com/photoshop/1.0/` for some tags created by Adobe Photoshop
- `http://ns.adobe.com/xap/1.0/rights/` for the XMP Rights specification.
- `http://ns.useplus.org/ldf/xmp/1.0/` for the Picture Licensing Universal System
- ...

We have two people in the image and we want to specify where they are, so we'll use the Image Region tag for that.
We can have more than one region, so we put an `rdf:Bag` inside the ImageRegion to store the different regions, using `rdf:li` with parse-type `Resource` to specify each of the regions.

```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description rdf:about=''
  xmlns:Iptc4xmpExt='http://iptc.org/std/Iptc4xmpExt/2008-02-29/'
  xmlns:dc='http://purl.org/dc/elements/1.1/'
  xmlns:xmp='http://ns.adobe.com/xap/1.0/'>

  <Iptc4xmpExt:ImageRegion>
   <rdf:Bag>

    <rdf:li rdf:parseType='Resource'>
     <!-- the region containing one person goes here -->
    </rdf:li>

    <rdf:li rdf:parseType='Resource'>
     <!-- the other person's region goes here -->
    </rdf:li>

   </rdf:Bag>
  </Iptc4xmpExt:ImageRegion>

 </rdf:Description>
</rdf:RDF>
```

I should note that you probably don't want to handle all the nuances of bags and items and parse types yourself RDF XML libraries are available in most languages.

Let's show both the Person structure and the Person in Image field to show how they are different

```xml
    <rdf:li rdf:parseType='Resource'>
     <Iptc4xmpExt:PersonInImage>
      <rdf:Bag>
       <rdf:li>Luther Tychonievich</rdf:li>
      </rdf:Bag>
     </Iptc4xmpExt:PersonInImage>
     <Iptc4xmpExt:RegionBoundary rdf:parseType='Resource'>
      <Iptc4xmpExt:rbShape>circle</Iptc4xmpExt:rbShape>
      <Iptc4xmpExt:rbUnit>relative</Iptc4xmpExt:rbUnit>
      <Iptc4xmpExt:rbX>0.25</Iptc4xmpExt:rbX>
      <Iptc4xmpExt:rbY>0.25</Iptc4xmpExt:rbY>
      <Iptc4xmpExt:rbRx>0.2</Iptc4xmpExt:rbRx>
     </Iptc4xmpExt:RegionBoundary>
    </rdf:li>
```

The above defines a circular region using relative coordinates, where 0,0 is the top-left corner of the image and 1,1 is the bottom-right corner, with a radius of 20% of the width of the image.
It says the name of the person in that region is Luther Tychonievich.

What if your tool doesn't handle regions?
IPTC allows persons outside of a region,
but you can also use the entire image as the region:

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

Let's also try the more involved person structure, where the person has a name but also two identifying IRIs and a transliteration of their name:

```xml
    <rdf:li rdf:parseType='Resource'>
     <Iptc4xmpExt:PersonInImageWDetails>
      <rdf:Bag>
       <rdf:li rdf:parseType='Resource'>
        <Iptc4xmpExt:PersonId>
         <rdf:Bag>
          <rdf:li>https://www.familysearch.org/tree/person/G391-Y6D</rdf:li>
          <rdf:li>http://example.com/people/clarke/gordon+curtis/1914-09-12</rdf:li>
         </rdf:Bag>
        </Iptc4xmpExt:PersonId>
        <Iptc4xmpExt:PersonName>
         <rdf:Alt>
          <rdf:li xml:lang='en'>Gordon Clarke</rdf:li>
          <rdf:li xml:lang='ja'>ゴードン・クラーク</rdf:li>
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
```

Putting it all together:


```xml
<rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>
 <rdf:Description rdf:about=''
  xmlns:Iptc4xmpExt='http://iptc.org/std/Iptc4xmpExt/2008-02-29/'
  xmlns:dc='http://purl.org/dc/elements/1.1/'
  xmlns:xmp='http://ns.adobe.com/xap/1.0/'>

  <Iptc4xmpExt:ImageRegion>
   <rdf:Bag>

    <rdf:li rdf:parseType='Resource'>
     <Iptc4xmpExt:PersonInImage>
      <rdf:Bag>
       <rdf:li>Luther Tychonievich</rdf:li>
      </rdf:Bag>
     </Iptc4xmpExt:PersonInImage>
     <Iptc4xmpExt:RegionBoundary rdf:parseType='Resource'>
      <Iptc4xmpExt:rbShape>circle</Iptc4xmpExt:rbShape>
      <Iptc4xmpExt:rbUnit>relative</Iptc4xmpExt:rbUnit>
      <Iptc4xmpExt:rbX>0.25</Iptc4xmpExt:rbX>
      <Iptc4xmpExt:rbY>0.25</Iptc4xmpExt:rbY>
      <Iptc4xmpExt:rbRx>0.2</Iptc4xmpExt:rbRx>
     </Iptc4xmpExt:RegionBoundary>
    </rdf:li>

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

All of this would then be placed inside the value of the XMP key-value pair in the metadata of an image file,
possibly with some additional markers and wrappers to help that format handle it properly.

For example, inside a JEPG file (JFIF) it is marked with an APP1 byte sequence (the bytes FF E1) in a two-string payload, the first being the identifier `http://ns.adobe.com/xap/1.0/` and the second being the XML above with an `xpacket` header and `<x:xmpmeta xmlns:x='adobe:ns:meta/'>` wrapping element, as specified in the JFIF implementation of how to embed XMP using EXIF.
