# Injector Preset Catalog

A dependency-free, static website to select an injector profile and export it for TunerStudio.

This site keeps injector data separate as open-source JSON files. When you select an injector and a fuel pressure, the tool:
1. Calculates flow at that pressure.
2. Interpolates dead-time data to fit the format required by the firmware.
3. Generates an injector-only `.msq` file to import directly into TunerStudio.

## Demo / Live Website

The catalog is published and available live on GitHub Pages:
https://matheusschmitz.github.io/injector-preset-web/

## Display units

Pressure can be displayed as kPa, bar, or psi. Flow can be displayed as cc/min, lb/h, or g/s. The mass-flow display uses a gasoline density of 0.72 g/ml. These are visual input/output conversions only: generated `.msq` files and contributed JSON profiles always store pressure in kPa and flow in cc/min.

## Output behavior

The tool needs no uploaded tune. It generates a partial `.msq` containing only:

- `injector_flow`
- `injectorFlowAsMassFlow`
- `fuelReferencePressure`
- `injector_battLagCorrBattBins`
- `injector_battLagCorrPressBins`
- `injector_battLagCorrTable`

The source profile may have more pressure and voltage points than rusEFI supports. The user selects the injector reference pressure used for `injector_flow` and `fuelReferencePressure`. The separate minimum and maximum dead-time pressures are used to interpolate the full characterization to the 2 pressure rows × 8 voltage columns expected by the current rusEFI table.

The exported file is intentionally not a full tune and must not be used as a replacement for an ECU-specific tune.

## Adding injector data

Open `add-injector.html` to create a profile. The browser editor opens the GitHub new-file page with a correctly shaped profile, ready for the contributor to commit and open a pull request. Prefer official manufacturer data; profiles using any other source must be labelled `community`.

`flowModel.kind` accepts:

- `curve`: use linear interpolation/extrapolation of `flowModel.points`, each with `pressureKpa` and `flowCcMin`.
- `sqrtFromReference`: calculate `reference.flowCcMin * sqrt(operatingPressure / reference.pressureKpa)` when only one manufacturer flow point exists.

The example profile is intentionally marked as community data.
