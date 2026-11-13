# keshif-plus-custom

A maintained fork of [Keshif+](https://github.com/keshifme/keshif-plus) used by
[explore1.portic](https://github.com/gegedenice/explore1.portic) and
[DataLab Explorer](https://github.com/smartbiblia-solutions/datalab-explorer).
Not an official Keshif release; see [NOTICE.md](NOTICE.md) for licensing.

## What is here

`dist/` is the deliverable: an ES module build that host applications copy as-is.

| File | Role |
|---|---|
| `dist/keshif.js` | **Readable canonical source.** All fixes are made here. |
| `dist/keshif.min.js` (+ `.map`) | Generated from `keshif.js` with `tools/` (terser 5.44.1). Never edit by hand. |
| `dist/keshif.css`, `keshif.min.css`, `assets/` | Styles, Font Awesome 6 Free, Leaflet images |
| `dist/vendor*.js`, `papaparse*.js` | Bundled dependencies, unchanged from the baseline |

`src/` is upstream's TypeScript and is kept for reference only: it does not
reproduce the distributed build this fork starts from (see the first fork commit).

## History

1. Baseline: the pre-built Keshif+ distribution shared by Portic and DataLab.
2. Portic corrections (see [CHANGES-PORTIC.md](CHANGES-PORTIC.md)): `dist/keshif.js`,
   `keshif.min.js` and its map are byte-identical to Portic's `static/dist`.
3. Host-application hooks, all opt-in (see below).
4. `dist/assets/` (upstream's `.gitignore` excluded it), unchanged from the baseline.

Each change is one commit; `git log -p dist/keshif.js` is the patch ledger.

## Host options added by this fork

```js
import * as kshf from "./keshif.min.js";
kshf.Base.loadWebFonts = false;          // skip the external Google WebFont request
new kshf.Browser({
  confirmLargeRecordView: false,         // no blocking modal above 5,000 records
  minRecordViewHeight: 460,              // reserved height of the central view
  onSelectionChange: (ids) => {},        // exact included record IDs after each filter change
  recordDisplay: {
    mapConfig: { emptyView: [[0, 0], 1] },  // view when no record has coordinates
    scatterLabels: false, compactPointDOM: true,
  },
});
browser.destroy();                       // unregister and remove a dashboard
```

## Making a change

```bash
cd tools && npm ci && npm run minify
```

Rules, so existing applications keep working:

- Edit `dist/keshif.js` only, then regenerate the minified files; commit both together.
- Defaults never change in a minor update. New behaviour is an option whose default is the
  previous behaviour; option names and exports are never renamed or removed.
- Before a consumer upgrades, run its test suite against the new `dist/`
  (for Portic: `npm test` and the browser suites `explore`, `urls`, `record-views`,
  `view-lifecycle`), and check that the included record IDs for fixed filters are unchanged.
- Consumers pin an exact commit or tag and upgrade deliberately.

----

# keshif-plus

Keshif+ is the rapid visual data exploration web app for structured tabular data.

With Keshif+, you can easily create full interactive data analysis environments with simple data configurations.

For sample dashboards, build the code, and open the samples within samples directory.

----

## What’s new with Keshif+?

Here are just a few highlights of significant changes, compared to the earlier release at https://github.com/adilyalcin/Keshif .

### Visualizations
- New Timeseries data type with record time-series visualization, with slope charts, bump charts, and index charts.
- New Map chart for showing individual record position.
- Dynamic point-cluster maps for point-location data
- Support for stacked and side-by-side charts
- Multiple color themes, with support for further customizations
- Extended break-down modes to support alternative %-analysis
- Number of maximum comparasions increased to five

### User Interface
- General refinements in UI design and look and feel
- Updated to font awesome v5 Pro fonts.
- Rich and dynamic data tooltips across all visualizations.

### Technical
- A new ES2016 module based implementation
- Build system using rollup.js
- Updated to d3 v7.3
- Integrated DOMPurify for sanitizing user input.
- Integrated popper and tippy.js libraries for tooltip management
- New translation module

----

## Installation

To install dependencies:
- npm install

To create the keshif+ packages:
- npm run rollup

or

- npm run rollup-watch 

This will create dist/assets/ directory, which is needed for full functionality.

To run sample dashboards on your computer:

- npm run start

Open sample dashboards by navigation to /samples/ directory, and specify the id parameter

- For example: http://localhost:5000/samples/?id=cars

### Installing icons

- Keshif uses font-awesome for icons, and you need a subscription to Font Awesome Pro to display all icons correctly.
- Use fontawesome.yaml with the Font Awesome Pro Subsetter application to generate the webfont & css files, and place them inside ./src/assets directory before creating keshif packages via rollup.

---

## Author

This software is fully designed and implemented by M. Adil Yalcin.

For inquiries, please contact adil@keshif.me .

---

## License

Copyright (C) 2017-2023 Keshif, LLC

This program is free software: you can redistribute it and/or modify
it under the terms of the BSD 3-Clause License.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU Affero General Public License for more details.

For full license terms, see the LICENSE file in this repository.

---

- This software is partially based on Keshif software released in 2016, (c) University of Maryland, with license at https://github.com/adilyalcin/Keshif/blob/master/LICENSE.

- For other licensing options, please contact adil@keshif.me .
