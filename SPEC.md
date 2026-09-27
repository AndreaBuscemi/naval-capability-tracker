# Naval Capability Tracker — Specification v1

## Objective

Build a continuous quantitative record of activity at Mediterranean naval
shipyards and bases, published as analysis in English, differentiated by
primary-source access to Italian-language defence material.

The asset is the longitudinal archive, not the software. Ingestion continuity
outranks everything else: a missed week is a permanent hole in the record.

## Methodological commitments (settled — do not relitigate)

- **Measure over time, don't identify.** Hull-length estimates, dock occupancy,
  counts above a size threshold. Classification comes from textual sources and
  human judgment, never from pixels.
- **Server-side processing only.** openEO on Copernicus Data Space. Raw scenes
  are never downloaded or stored. Derived metrics only.
- **Public repo, open methodology.** Sell judgment, not numbers.
- **AIS is a subtractive filter**, used to remove identified commercial traffic,
  not a detector. Naval units frequently do not transmit.
- **Zero marginal cost** is a hard constraint, not a preference.

## Architecture (fixed)

**Compute.** openEO on Copernicus Data Space Ecosystem. 10,000 free credits per
month; a batch job costs roughly 8. Five targets processed weekly is about
20 jobs/month, roughly 2% of the quota.

**Orchestration.** GitHub Actions, public repository, unlimited standard-runner
minutes. Weekly cron schedule.

> **Hazard:** scheduled workflows are disabled automatically after 60 days of
> repository inactivity. The weekly data commit resolves this under normal
> operation, but a silent pipeline failure will eventually kill the schedule.
> Monitoring is mandatory, not optional.

**Storage.** The git repository itself. CSV/Parquet of derived metrics only.
Imagery is never committed — `.gitignore` enforces this.

**Secrets.** CDSE credentials in GitHub Secrets. Personal GitHub account, never
corporate.

**Frontend.** GitHub Pages, deferred until Layer 2 is solid.

**Licensing.** Code MIT. Derived datasets CC BY 4.0 (covers the EU sui generis
database right, which CC BY 3.0 does not). Copernicus attribution is a licence
obligation, not a courtesy.

## Layers (build in order — never invert 2 and 3)

1. **Ingestion** — Sentinel-1 on fixed targets; procurement data;
   Polymarket/Kalshi.
2. **Derivation** — dock occupancy, hull counts, size distributions,
   AIS-filtered contacts, cross-market coherence indices.
3. **Public surface** — Substack with charts, later a dashboard.
4. **Services** — commissioned research, licensed datasets, domain book.

## Targets v1

| Site | Approx coords | Role |
|---|---|---|
| Toulon | 43.12 N, 5.90 E | **Validation** — CdG movements publicly reported, gives ground truth |
| Riva Trigoso | 44.25 N, 9.42 E | **Edge** — hull construction, upstream of Muggiano |
| Muggiano / La Spezia | 44.08 N, 9.87 E | **Edge** — fitting-out and submarines |
| Taranto | 40.47 N, 17.22 E | **Signal** — main Italian fleet base |
| Tartus | 34.90 N, 35.87 E | **Signal** — post-Aug 2026 Russian access arrangement, open question |

Tier 2, to add once the pipeline is stable:

| Site | Approx coords | Rationale |
|---|---|---|
| Gölcük | 40.72 N, 29.83 E | Turkish submarine construction; most dynamic programme in the region |
| Navantia Cartagena | 37.58 N, 0.98 W | S-80 programme; enables three-way European submarine pipeline comparison |
| Souda Bay | 35.50 N, 24.13 E | NATO/US eastern Mediterranean hub |

Coordinates are indicative only — they locate the site, nothing more. AOI
polygons are drawn manually, tight around the basin or quay of interest.
Tighter polygon means less clutter and fewer credits consumed.

## Known technical hazards

**Port clutter is the primary obstacle.** Cranes, sheds and quay infrastructure
produce very strong radar returns that mask hulls. The AOI selection criterion
is not "where is the vessel" but "where is the vessel spatially separable from
infrastructure". Dry docks and isolated quays are good; basins abutting
buildings are not. Inspect every site on optical imagery before fixing the
polygon.

**Revisit frequency is not uniform.** Sentinel-1 coverage varies by latitude and
acquisition plan. Verify the actual number of available acquisitions per target
over the past 12 months before assuming a weekly cadence.

**Repository bloat.** Never commit imagery. Metrics only. The repo stays in the
megabyte range, not gigabytes.

## Non-goals

- Vessel classification from imagery
- Real-time alerting
- Commercial imagery procurement before a first paying customer
- Any content touching payments, disputes, fraud, or risk
- Betting tips or trading advice

## Operator context

Solo operator, employed full-time; the project runs on evenings and weekends.
Solutions must survive two-week gaps in attention. Strong Python/SQL and cloud
background, no prior remote sensing experience. Italian/English bilingual —
this is a core analytical asset, giving primary-source access to Italian
parliamentary defence documents, procurement notices and industry
communications that anglophone analysts reach only via translation and delay.
