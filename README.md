# Product Extension Specification

- **Title:** Product
- **Identifier:** <https://stac-extensions.github.io/product/v0.1.0/schema.json>
- **Field Name Prefix:** product
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @m-mohr

This document explains the Product Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

This extension provides a generic framework to describe products related to the data in a STAC catalog.
A product is a package offer of the STAC item and thus describes properties specific to the product packaging and its distribution.
Several products may be specified at assets level, each with its own properties.
This extension is intended to be adapted by other extensions that provide best practises or definitions about the product like
specific dictionaries for the product type or the timeliness of the product.

- Examples:
  - [Item example](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [Collection example](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [x] Collections
- [x] Item Properties (incl. Summaries in Collections)
- [x] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [ ] Links

| Field Name                  | Type   | Description                                                  |
| --------------------------- | ------ | ------------------------------------------------------------ |
| product:type                | string | The product type.                                            |
| product:timeliness          | string | The average expected timeliness of the product as an [ISO 8601 Duration](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| product:timeliness_category | string | A proprietary category identifier for the timeliness of the product. |
| product:acquisition_type    | string | The acquisition type of the product.                         |

> \[!IMPORTANT]  
> `product:timeliness` is REQUIRED if `product:timeliness_category` is provided.

### Additional Field Information

#### product:type

The product type in this extension is a free-form text that providers can freely use to descibe their product types.
Some extensions may specify more specific rules for this field.

This field superceedes the `sar:product_type` field.

#### product:acquisition_type

The product acquisition type in this extension is an enumeration text that providers can use to describe their product types.
As described in the [specifications](https://docs.ogc.org/is/10-157r4/10-157r4.html#24), Table 5, property `acquisitionType`:

> Used to distinguish at a high level the appropriateness of the acquisition for "general" use,
> whether the product is a nominal acquisition, special calibration product or other.

Admitted values are:
 - `nominal`
 - `calibration`
 - `other`

Sentinel-2 [Annex A (page 90)](https://sentinel.esa.int/documents/247904/2047089/Sentinel-2_Cal-Val_Phase-E2.) provides the calibration sites so some
acquisitions over those areas will be acquired for calibration purposes.
The `product:acquisition_type` field brings is the possibility to "flag" products as `nominal`, `calibration`
or `other` (not `nominal`, not `calibration`).

[Sentinel-1](https://sentinels.copernicus.eu/web/sentinel/-/copernicus-sentinel-1-calibration-campaign-on-going-in-europe) provides few acquisitions
in given dates and orbits that were acquired in a different mode. Those products would have `calibration`.

#### Timeliness

Below you can find an example that shows how the timeliness fields could be used.

The Copernicus programme releases products on three levels of timeliness:

| Name                | Description                                                  | `product:timeliness`    | `product:timeliness_category` |
| ------------------- | ------------------------------------------------------------ | ----------------------- | ----------------------------- |
| Near Real-Time      | Delivered less than 3 hours after data acquisition.          | e.g. `PT3H` (3 hours)   | `NRT`                         |
| Short Time-Critical | Delivered within 36 (Sentinel-6) to 48 (Sentinel-3) hours after data acquisition. | e.g. `PT36H` (36 hours) | `STC`                         |
| Non Time-Critical   | Delivered typically within 1 month after data acquisition.   | e.g `P1M` (1 month)     | `NTC`                         |

> \[!WARNING]
>
> Be careful when specifying the durations for `product:timeliness`.
> It is recommended to closely reflect the semantics of timeliness as specified by the provider.
> For example, if the timeliness is 36 hours, specify  `PT36H` instead of  `P1DT12H`, although allowed:
>
> > The standard does not prohibit date and time values in a duration  representation from exceeding their "carry over points".
> > Thus, `PT36H` could be used as well as `P1DT12H` for representing the same duration.
> > But keep in mind that `PT36H` is not the same as  `P1DT12H` when switching from or to Daylight saving time.
>
> Source: <https://en.wikipedia.org/wiki/ISO_8601#Durations>

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```
