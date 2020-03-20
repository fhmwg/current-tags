# FHMWG Standards Drafts

Stage 1 of the FHMWG standards process is the drafting of recommendations about the use of existing metadata tags for family history purposes.

FHMWG has a larger mission, but this initial project is limited to the use of existing photo embedded metatdata tags.

## Topical Areas

The board has identified the following areas for metadata standardisation during the stage-1 work:

- Album
- Caption
- Dates
- Event
- Location
- Objects
- People
- Rights
- Sources

It is within scope for these to be combined (e.g. source and rights tags may prove to be sufficiently related to be worth merging) or further split up (e.g., sources and provenance may prove to be sufficiently varied to be worth splitting). These shall be considered an initial list, not a final mandate.

Note that the word "subject" is used differently in different contexts. Sometimes it means the broad overviews (e.g., "1980 Jones family reunion"); sometimes a general term for things in the scene (e.g., Objects and People).

Also within scope for the standards teams is 

- Specifications, documentation
- Quality, conformance, testing
- Sample Code

## Proposed task flow

1. For each topic
    1. Perform inventory of relevant tags in [EXIF](http://www.cipa.jp/std/documents/e/DC-X008-Translation-2019-E.pdf), [IPTC](https://iptc.org/standards/photo-metadata/iptc-standard/), and [XMP](https://www.adobe.com/devnet/xmp.html)
    1. If needed, draft "how these tags differ" text
    1. Create recommendations on which tags should be used
        1. include which to read and which to write
        1. include how to resolve conflicting information in equivalent-meaning tags
    1. If applicable, draft "avoiding malicious/tracking/marketing payloads" recommendations for free-text fields
    1. If applicable, draft "we recommend these authorities or formats" for identifier-valued fields (e.g., URI, IRI, UUID, GUID)
    1. Interview potential implementers to collect intitial feedback on recommendations; return to 1.iii. as needed
    1. Create minimal draft open-source software for implementing recommendation
1. Coordination
    1. Unify formatting and style of recommendations and sample code
    1. Coordinate on cross-cutting ideas where a single decision could serve multiple teams
    1. Create combined recommendation document

We anticipate additional activities after these steps are completed, including seeking consensus, defining compliance certification processes, coordinating with other metadata organizations, etc. Those will be added to this document if/when they are more clearly defined.

## Repository structure

One folder shall be created for each working team. At present, we are not imposing any common structure on these folders, nor mandating that github be used prior to final product sharing if a team would rather work using a different toolchain. Files outside of folders shall be limited to those applying across all working teams.

We may, at some future time, create separate folders for the final recommendation document(s) and the reference code.

Branches may be used to experiment with alternative approaches. It is expected that the master branch will contain the "main" development work for each team.

At present, we are discouraging the creation of forks and separate repositories.
