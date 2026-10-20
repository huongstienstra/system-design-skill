# Data pipelines: ETL, analytics, events

## Questions
Ranked; re-rank for the prompt. The default follows each one.

1. How fresh must the output be? Default: hourly.
2. Sources and formats? Default: an app database plus JSON events.
3. Volume per day? Default: 10 million events, 1 KB each.
4. Who reads the output, and with what access? Default: internal analysts, with no personal data exposed.
5. How much history to keep? Default: 2 years.
6. Accuracy rules: dedupe, late data, refunds, corrections? Default: dedupe by event ID; accept events up to 3 days late.
7. Exports or downstream tools? Default: a warehouse plus a BI dashboard.

## Scale dimensions
- Ingest: events per day ÷ 86,400 = events/sec, then × peak factor.
- Storage: daily volume × retention days ÷ compression (about 5x for columnar).
- Backfill: time to reprocess the full history at the pipeline's throughput.
- Late-data window: how many partitions stay open for updates.
- Query cost: data scanned per dashboard refresh × refreshes per day.

## Usual failures
- A source changes its schema without warning.
- Duplicate events from retries inflate totals.
- Late or out-of-order events land in a partition that is already closed.
- A job fails halfway, and a rerun double-writes the output.
- A backfill competes with the live pipeline for capacity.
- The source has an outage; the gap is silent unless something alerts on it.
- Personal data flows into a table that a wide audience can read.
- A dashboard shows numbers that disagree with the source of truth.
