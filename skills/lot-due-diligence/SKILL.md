---
name: lot-due-diligence
description: Check a land lot in Australia, New Zealand, Canada or the UK with the Map My Lot tools before a purchase, a build or an offer. Use when the user asks what a lot is, how big it is, what limits it (flood, bushfire, coastal, heritage, easements, zoning, slope), or wants a lot report, a comparison of lots, or to watch a lot for changes.
license: Proprietary. Copyright Map My Lot.
metadata:
  version: "1.0.0"
  connector: https://mapmylot.com/mcp
---

# Lot due diligence with Map My Lot

Map My Lot holds official cadastre and planning data for Australia (QLD, NSW, VIC, TAS, ACT), New Zealand, Canada (BC, MB, NB, YK) and the UK (England, Wales, Scotland). Give the user facts from it, in this order, and nothing it does not say.

## Order of checks

1. **Find the lot.** Call `mml_find_lot` with the address, lot/plan, UK postcode, INSPIRE ID or coordinates. If it returns options or asks for more detail, ask the user. Keep the `row_id`.
2. **Outline and size.** Report the area, perimeter and lot/plan from the result. Mention the map link (`view_url`).
3. **Constraints.** Call `mml_lot_facts` with the `row_id`. For each layer answered, say whether it is present and how much of the lot it covers.
4. **Building questions.** If the user asks about building, extending, subdividing or a granny flat, call `mml_build_facts` and report zone, area, frontage, constraints and slope as facts. Pass on its caveat.
5. **Surroundings.** From the facts, mention the suburb comparison and nearby features if they matter to the user.
6. **Report.** If the user wants the full report, call `mml_report_link` and give the link or the wording it returns.

## Rules

- **Three states, never two.** A layer is present, not present, or not known. A layer in `locked_layers` was checked but its answer is locked for this account: say it is locked. A layer that was not checked has no data there: say it is unknown. Never say "no flood risk", "no bushfire risk" or "no constraints" for a locked or unchecked layer.
- **When answers are locked,** say the result's `locked_line` once, in your own words if needed, and keep its link. Do not repeat it for each layer.
- **Facts, not verdicts.** Never say a lot can or cannot be built on, subdivided or approved. Map My Lot gives facts from public maps, not planning advice.
- **Cite.** Name the data sources and their dates from `sources`, and include any attribution `statement` the result carries when you quote the data.
- **Point to people who decide.** For building, planning or zoning: the local council. For boundaries and fences: a licensed surveyor. For a purchase: a conveyancer or solicitor. For flood or bushfire in depth: the council or the state agency named in the sources.
- **Limits.** Each new lot found counts toward the user's daily lookups. Do not look up lots the user did not ask about. If `quota_exceeded` comes back, tell the user what it says.
- **Several lots.** To compare, use `mml_compare_lots` (it may need Pro). It gives facts per lot and does not rank them; do not declare a winner.
- **Saving and watching.** Save a lot with `mml_save_lot` when the user asks. Watch a lot or its suburb with `mml_watch` when the user wants to hear about changes; stop one with `mml_unwatch`.
