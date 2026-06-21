# Irish Passive-Active Retrofit Optimisation Pilot

This repository is a small proof-of-work project for an SEAI Energise Fellowship application. It demonstrates a transparent workflow for evaluating passive retrofit measures together with air-to-water heat-pump design assumptions for a representative Irish dwelling.

The current version is intentionally lightweight:

- a representative Irish semi-detached dwelling archetype;
- passive retrofit variables: wall/roof/floor insulation, glazing, airtightness, ventilation, shading;
- active heat-pump variables: heat-pump capacity, design flow temperature, emitter upgrade, heat-pump type;
- a physics-based reduced-order evaluator for quick screening;
- a Pareto search that identifies non-dominated retrofit packages;
- an EnergyPlus IDF generator for later dynamic-simulation validation when EnergyPlus and an Irish EPW weather file are available.

It is not presented as a full national-stock model. It is a pilot to show methodological readiness: how passive measures, heat-pump performance and optimisation can be connected in a reproducible workflow.

## Why This Matters

Irish retrofit policy is built around BER improvement, heat-pump readiness and large-scale heat-pump deployment. A sequential pathway can make a home heat-pump ready, but may not identify the best combined solution for cost, emissions, comfort and seasonal COP. This pilot shows how a passive-active package can be evaluated as one decision.

## Repository Structure

```text
data/
  archetypes/irish_semi_detached_1980s.json
  weather/README.md
docs/
  assumptions.md
  section3_narrative.md
results/
  README.md
scripts/
  run_pilot.py
  export_energyplus_batch.py
src/
  retrofit_pilot/
    model.py
    optimise.py
    energyplus_idf.py
```

## Quick Start

Run the pilot optimisation:

```bash
python scripts/run_pilot.py
```

This creates:

- `results/pilot_scenarios.csv`
- `results/pareto_front.csv`
- `results/summary.md`

Export EnergyPlus IDF files for the top Pareto scenarios:

```bash
python scripts/export_energyplus_batch.py --limit 10
```

This creates EnergyPlus input files in `energyplus_cases/`. The files are intended for later validation using EnergyPlus with an Irish EPW weather file.

## Methodological Note

The reduced-order evaluator estimates heating demand from heat-loss coefficients and degree-hour approximations, then estimates heat-pump electricity use using a temperature-dependent Carnot-style COP relationship:

```text
COP = eta * T_sink / (T_sink - T_source)
```

The relationship is bounded to avoid unrealistic COP values. This is a screening model, not a substitute for EnergyPlus validation. The intended fellowship workflow is:

1. define Irish dwelling typologies;
2. simulate passive-active retrofit cases in EnergyPlus;
3. train a physics-informed surrogate from EnergyPlus outputs;
4. optimise on the surrogate;
5. re-simulate final solutions in EnergyPlus.

## How To Cite

See `CITATION.cff`. If archived on Zenodo, add the generated DOI to the citation file.

