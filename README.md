# Agentic site selection: scouting film locations with CARTO

Materials for the SDSC 2026 hands-on workshop. You build two CARTO Workflows that describe every scouting area in the 11 western US states, publish two Workflows as MCP tools (`find_landscapes` and `score_locations`), and give a Builder map an AI Agent that scouts locations from a creative brief.

**Start with the step-by-step guide: https://k-mickow.github.io/sdsc26-film-scouting/**

## What's here

| Path | What it is |
|---|---|
| `index.html` | The workshop guide (served at the link above) |
| `data/` | The three tables to upload before the tasks, with a README on each column and where the data comes from |
| `workflows/` | Import files for each workflow: Look (fast and full), Logistics, and the two MCP tools |
| `agent_instructions.md` | Instructions for the map's AI Agent (also usable as a Claude project prompt) |

## Using the files

- **Tables:** in CARTO Data Explorer, open your dataset's folder, click *Import data*, and keep the table names.
- **Workflows:** on the Workflows page, click *Import workflow* and pick a `.sql` file. Each file uses `YOUR_PROJECT.YOUR_DATASET` for table names; after importing, change it to your own dataset in the components the guide lists.

Film incentives and film locations were researched with Claude, with sources cited on every row. Land cover was derived from NLCD land cover classes. Incentive rates are headline figures for a teaching exercise; confirm details with each state's film office.
