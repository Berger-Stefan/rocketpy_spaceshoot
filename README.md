# RocketPy Space Shoot

RocketPy notebook project for simulating a two-stage rocket flight and exporting
the resulting trajectory as KML.

## Contents

- `simulation.ipynb` - main two-stage rocket simulation.
- `convert_files.ipynb` - helper notebook for splitting aerodynamic drag CSVs
  into power-on and power-off files.
- `files/` - motor thrust curves, drag curves, and the OpenRocket design file
  used by the main simulation.
- `getting_started/` - RocketPy getting-started example assets and notebook.
- `trajectory.kml` - previously exported trajectory output.

## Requirements

- Python 3.12
- [uv](https://docs.astral.sh/uv/) for dependency management

The Python dependencies are declared in `pyproject.toml` and locked in
`uv.lock`.

## Setup

Install the locked environment:

```bash
uv sync
```

Start Jupyter:

```bash
uv run jupyter notebook
```

Then open `simulation.ipynb`.

## Running the Main Simulation

The main workflow is in `simulation.ipynb`:

1. Create a RocketPy `Environment`.
2. Load first-stage and second-stage solid motors from `files/*.eng`.
3. Build the two rocket stages using local drag curves from `files/*.csv`.
4. Simulate the first-stage flight.
5. Start the second-stage flight from the staging state.
6. Plot the combined trajectory and export `trajectory.kml`.

## Current Run Status

Dependency installation and imports work with:

```bash
uv run python -c "import rocketpy, pandas, matplotlib"
```

The full notebook does not currently execute as-is because this cell depends on
RocketPy's GFS forecast fetch:

```python
env.set_atmospheric_model(type="Forecast", file="GFS")
```

On 2026-06-29, NOMADS returned an OpenDAP retirement HTML response for the GFS
endpoint, which caused RocketPy/netCDF4 to retry and then block in that cell.

An offline smoke test using RocketPy's built-in standard atmosphere did run
successfully with the same local motor and drag files. It produced:

- first-stage apogee: `16339.68 m`
- second-stage apogee: `89393.91 m`
- second-stage final time: `302.11 s`
- KML export: `/tmp/rocketpy_spaceshoot_trajectory_smoke.kml`

To run the notebook without live GFS forecast data, replace the forecast line
with:

```python
env.set_atmospheric_model(type="standard_atmosphere")
```

## Notes

- `getting_started/getting_started.ipynb` contains tutorial cells that install
  packages and download files with `pip` and `curl`. Those are not needed for
  this repository when using `uv sync`.
- Running the final notebook cell writes `trajectory.kml` in the repository
  root.
