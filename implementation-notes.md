# Outdated draft

This document has been superseded by [stage1.md](stage1.md).

Portions of this document have been incorporated into and updated by the Stage 1 recommendation;
other portions, while potentially useful to some parties, have been determined not to belong in the recommendation itself.

----


# Vetting and censoring

An application *must not* remove a field simply because it does not understand the meaning of the field.
All metadata, understood or not, *shall* be perserved *unless* it violates an application's code of conduct.

There are various reasons why an application may wish to reject certain metadata.
This section provides common rules for such cases.

Applications *must not* edit provided metadata except under the following four cases:

1. User-moderated editing is permitted, including 
    1. user-initiated edits
    2. application-initiated edits presented to the user for approval
2. Converting values identified as "free text" to unicode normal forms C or D
3. Whitespace-normalizing values identified as "line text"
4. Other cases specifically permitted for a particular field

Applications may remove a metadata field entirely if it violated an application's code of conduct and no edit is permitted under the four cases above. Note that removing a field is distinct from editing it to be the empty string: removing a field means removing its XMP element or EXIF key:value pair entirely.

## Malicious URLs

URL, URI, IRL, and IRI (collectively called URI hereinafter) fields may be vetted to ensure they are not URLs of malicious sites

- A version 3, 4, or 5 UUID encoded as a URN, as specfiied in <https://tools.ietf.org/html/rfc4122>, is never malicious.
- The maliciousness of other URIs is not defined in this spec.
- If a malcious URI, is encountered, it should be replaced by a version-5 UUID created using the empty-string namespace and hashing the rejected URI.
- If the field in question requires a visitable link (i.e., it must be a URL, not a URI) and cannot otherwise be edited, then the entire field may be removed.
- As with all fields, an application *must not* edit a provided URI without user involvement

When used as an identifier, a URI and its corresponding version-5 UUID encoded as a URN should be considered equivalent, unless the URI is a version 3, 4, or 5 UUID.

> Because taking a version-5 UUID is not idempotent, the above exception for UUIDs prevents the cornder case of needing to check the UUID of the UUID of the UUID of ... of the URI. Versions 1 and 2 are not excluded because they are sometimes considered security risks.


## Inappropriate text

Any free-text field might contain content that violates a site's code of conduct,
such as abusive or inappropriately-shared prviate content.
As with all fields, inappropriate text *must not* be edited without user involvement;
if user involvement is unable to resolve the issue, the entire field *may* be removed.

## Length- and content-limiting

Applications *may* use various automated properties of fields as a proxy for appropriateness.

- Content *may* be limited to reject certain delimiters and Unicode control characters. However, values intended for human viewing *must not* be limited to ASCII or other subsets of code points that exclude some human languages.

- Content *may* be limited by length, but such limits should be generous.

As with other cases, neither characters nor length should be enforced with automated editing; a user *must* mediate any editing. If an application choses to remove metadata because it is too long or uses illegal characters, it *should* alert the user it has done so and indicate the reason it is being removed.

# Partial implementation

To be fully compliant with these recommendations, an application will

1. Preserve metadata provided with an imported image though all internal manipulations of the image and embed it again upon export. This preservation applies to all metadata representable in the same way in both the import and export file format, except as permitted elsewhere in this recommendation, whether or not the application understand the meaning of the metadata.

2. If
    
    - a user provides additional metadata about an image, and 
    - the metadata provided is of a type covered in this recommendation, and
    - the user does not request that the metadata be kept private:
    
    then the application embeds the metadata in the image upon export as specified in this recommendation.

It is not necessary for an application to handle all of the metadata discussed in this recommendation to be considered compliant with these recommendations.
An application that does not handle metadata at all, neither accepting nor removing it, is trivially compliant.
An application that only tags the names of individuals in a photo and has no other metadata is compliant if it embeds that metadata according to the [Person] section and does not otherwise edit or remove metadata.
Etc.

# Going beyond this spec

Applications *may* implement metadata beyond the scope of this recommendation, either by implementing other metadata standards or by designing their own metadata system.
Note, however, that to be compliant with these recommendations they *must* obey this specification, including

- Metadata that is covered by these recommendations *must* be embedded according to them; it *may* also be embedded in additional ways.

- Metadata must not be edited without user moderation.
