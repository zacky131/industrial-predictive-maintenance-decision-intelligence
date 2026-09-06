# Data

## Selected Dataset

NASA C-MAPSS Turbofan Engine Degradation Simulation Data Set, beginning with subset FD001.

## Source

- [Official NASA/Data.gov catalog record](https://catalog.data.gov/dataset/cmapss-jet-engine-simulated-data)
- [NASA DASHlink dataset record](https://c3.ndc.nasa.gov/dashlink/resources/139/)
- Dataset citation: A. Saxena and K. Goebel (2008), *Turbofan Engine Degradation Simulation Data Set*, NASA Ames Prognostics Data Repository.

## Why It Was Selected

C-MAPSS provides ordered run-to-failure trajectories for multiple engines, measurements available before failure, a Remaining Useful Life target, and an official engine-level test design. These properties support credible lead-time reasoning and maintenance prioritization better than a current-state failure label.

## How to Obtain It

The archive was acquired from the stable URL published in the official catalog and extracted without modifying its contents. To reproduce the acquisition from the repository root:

```bash
mkdir -p data/raw/cmapss
curl -fL "https://data.nasa.gov/docs/legacy/CMAPSSData.zip" -o data/raw/CMAPSSData.zip
unzip -q data/raw/CMAPSSData.zip -d data/raw/cmapss
```

After acquisition, verify the archive contents against the official catalog description before any analysis. Do not substitute an unofficial mirror without documenting provenance and file equivalence.

The local archive was verified on Day 9:

```text
Filename: data/raw/CMAPSSData.zip
Size: 12,425,978 bytes
SHA-256: 74bef434a34db25c7bf72e668ea4cd52afe5f2cf8e44367c55a82bfd91a5a34f
ZIP integrity: PASS
FD001 train: 20,631 rows × 26 columns, 100 engines
FD001 test: 13,096 rows × 26 columns, 100 engines
FD001 test RUL: 100 rows × 1 column
```

The checksum is a local reproducibility baseline; it has not been matched to an authoritative published checksum.

## Expected Local Path

```text
data/
├── raw/
│   ├── CMAPSSData.zip
│   └── cmapss/
├── interim/
└── processed/
```

## License / Usage Notes

The official federal catalog marks the resource as publicly accessible but does not display an explicit dataset license. Cite NASA and the dataset authors, retain the source documentation, and confirm the applicable redistribution terms before republishing any raw files.

## Do Not Commit Raw Data

Raw data is intentionally excluded through `.gitignore`. The downloaded archives and extracted source files are not tracked or committed; users can reproduce acquisition with the source link and commands above.
