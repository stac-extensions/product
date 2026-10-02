# Product Extension Specification

- **Title:** Product
- **Identifier:** <https://stac-extensions.github.io/product/v1.1.0/schema.json>
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
| product:status              | string | The lifecycle/status of the product.                         |
| product:quality_status      | string | The quality status of the product: `nominal` or `degraded`.  |

> \[!IMPORTANT]  
> `product:timeliness` is REQUIRED if `product:timeliness_category` is provided.

### Additional Field Information

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

#### product:type

The product type in this extension is a free-form text that providers can freely use to descibe their product types.
Some extensions may specify more specific rules for this field.

This field superceedes the `sar:product_type` field.

#### product:acquisition_type

The product acquisition type describes the purpose of the acquisition.
It is similar to the `acquisitionType` field from the
[OGC® Earth Observation Metadata profile of Observations & Measurements , Table 5](https://docs.ogc.org/is/10-157r4/10-157r4.html#24):

> Used to distinguish at a high level the appropriateness of the acquisition for "general" use,
> whether the product is a nominal acquisition, special calibration product or other.

Allowed values are:

- `nominal`
- `calibration`
- `other`

Sentinel-2 [Annex A (page 90)](https://sentinels.copernicus.eu/documents/247904/2047089/Sentinel-2_Cal-Val_Phase-E2)
provides the calibration sites so some acquisitions over those areas will be acquired for calibration purposes.
The `product:acquisition_type` field brings the possibility to "flag" products as `nominal`, `calibration`
or `other` (not `nominal`, not `calibration`).

[Sentinel-1](https://sentinels.copernicus.eu/web/sentinel/-/copernicus-sentinel-1-calibration-campaign-on-going-in-europe) provides few acquisitions
in given dates and orbits that were acquired in a different mode. Those products would have `calibration`.

#### product:status

Refers to product status.
It is similar to the `status` field (of kind `StatusValue`) from the
[OGC® Earth Observation Metadata profile of Observations & Measurements , Table 5](https://docs.ogc.org/is/10-157r4/10-157r4.html#24):

Allowed values are:

- `archived`
- `acquired`
- `cancelled`
- `failed`
- `planned`
- `potential`
- `rejected`
- `qualitydegraded` (**deprecated**, see below)
- `accepted`

The value `accepted` is an addition to the OGC list.
It means that the product passed its quality control or validation checks and is fit for downstream use.
It is the positive counterpart of `rejected`.

In the OGC model, `archived` is the nominal value, but it only tells that the product is in the archive.
OGC refines `archived` with a separate `statusSubType` field (`ON-LINE` or `OFF-LINE`).
This extension does not define a status subtype.
Instead, `accepted` states directly that the product is usable, with a single field.

> \[!WARNING]
> The value `qualitydegraded` is **deprecated** and may be removed in a future major version.
> This follows OGC 10-157r4, which deprecates it in favour of the `productQualityStatus` element.
> Use [`product:quality_status`](#productquality_status) with the value `degraded` to describe the product quality,
> and keep `product:status` for the lifecycle or disposition of the product.
> For example, replace `"product:status": "qualitydegraded"` with
> `"product:status": "accepted"` and `"product:quality_status": "degraded"`.

##### Relationship with the Order Extension

`product:status` and `order:status` may appear similar but they describe different entities and lifecycle concerns.

- `order:status` describes the lifecycle or execution state of a request, order, or processing transaction.
- `product:status` describes the disposition or usability status of the resulting catalogued product artifact itself.

A processing order may therefore complete successfully while the generated product is later considered unsuitable for downstream use.

For example, in a processing chain, a derived product may be successfully generated and catalogued,
 but later excluded from downstream processing after quality control validation:

```json
{
  "properties": {
    "product:status": "rejected"
  }
}
```

#### product:quality_status

Indicates whether the quality of the product is degraded or not.
It is similar to the `productQualityStatus` field from the
[OGC® Earth Observation Metadata profile of Observations & Measurements , Table 5](https://docs.ogc.org/is/10-157r4/10-157r4.html#24):

> Indicator that specifies whether the product quality is degraded or not.
> This optional field shall be provided if the product has passed a quality check.

Allowed values are:

- `nominal`: the product passed the quality check without degradation.
- `degraded`: the product passed the quality check, but its quality is degraded.

Do not set this field if no quality check was done on the product.

`product:quality_status` and `product:status` are independent.
`product:status` tells whether the product can be used, and `product:quality_status` tells the quality of a usable product.
For example, a product can be `accepted` with a `degraded` quality.

##### Mapping from GEODES `product_validity`

The [GEODES STAC API](https://geodes.cnes.fr/metadonnees-offertes-par-lapi-stac-de-geodes/) of CNES
uses a boolean `product_validity` field.
It tells whether the product has passed quality checks or meets specific predefined criteria.
The recommended mapping is:

| `product_validity` | `product:status` | `product:quality_status` |
| ------------------ | ---------------- | ------------------------ |
| `true`             | `accepted`       | `nominal` or `degraded`  |
| `false`            | `rejected`       | not set                  |
| not set            | not set          | not set                  |

```json
{
  "properties": {
    "product:status": "accepted",
    "product:quality_status": "nominal"
  }
}
```

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
