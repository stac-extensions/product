# Product Extension Specification

- **Title:** Product
- **Identifier:** <https://stac-extensions.github.io/product/v0.1.0/schema.json>
- **Field Name Prefix:** product
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @m-mohr

This document explains the Product Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

This extension provides a generic framework to describe products across domains better in STAC.

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

| Field Name                  | Type   | Description       |
| --------------------------- | ------ | ----------------- |
| product:type                | string | The product type. |
| product:timeliness          | string | The average expected timeliness of the product as an [ISO 8601 Duration](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| product:timeliness_category | string | A proprietary category identifier for the timeliness of the product. |

> \[!IMPORTANT]  
> `product:timeliness` is REQUIRED if `product:timeliness_category` is provided.

### Additional Field Information

#### product:type

The product type in this extension is a free-form text that providers can freely use to descibe their product types.
Some extensions may specify more specific rules for this field.

This field superceedes the `sar:product_type` field.

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
