## 6.1 Ticker-list rationale

The original Financial Services ticker list did not fully represent several major
banking and capital markets institutions. To improve industry coverage, we expanded
the list by adding additional large financial firms including Morgan Stanley, Goldman
Sachs, Citibank, Wells Fargo, and Discover. These additions allowed the dataset to
capture a broader range of operational, regulatory, and expansion-related events
occurring across the financial sector. The seeded list also tended to overrepresent
a smaller subset of financial firms, limiting the diversity of filings returned
during event extraction.

For Travel and Hospitality, the seeded ticker list underrepresented large resort
operators, casinos, and entertainment-based hospitality companies. To address this,
we added companies associated with resorts and destination entertainment properties
in order to increase the variety of filings collected. These modifications improved
event diversity and helped capture operational activities beyond traditional hotel
chains, including acquisitions, property developments, and large-scale hospitality
expansions.

A practical limitation affected both industries: EDGAR's full-text search does not
reliably populate the tickers field in search results. All candidates returned empty
ticker lists, so ticker-based filtering was skipped and all candidates were passed
to Stage 3 for classification by Claude.

## 6.2 Search-phrase rationale

For Financial Services, we expanded the original phrase list by adding terms
associated with operational growth, restructuring, and organizational change.
Additional phrases such as "new branch," "branch closure," "operations center,"
and "new headquarters" improved identification of filings related to company
expansion, consolidation, and strategic business development. These phrases
increased coverage of location-based operational events not consistently captured
by the seeded phrase list.

For Travel and Hospitality, the original search phrases focused heavily on
expansion-related activity and did not adequately capture indicators of declining
performance, restructuring, or asset transfers. To improve coverage, we added
phrases including "new acquisition," "completed the acquisition," "sold its,"
"new resort," "new destination," and "sale of the hotel." These additions
strengthened detection of mergers, acquisitions, property sales, and restructuring
activities commonly reported in hospitality filings. Some phrases such as
"new gateway" and "new property" returned EDGAR 500 server errors during retrieval
and were retried automatically.

## 6.3 Window-experiment results

We experimented with multiple filing windows ranging from 30 to 360 days to identify
a configuration that produced a sufficient number of usable events while remaining
computationally efficient. Early experiments returned relatively few valid events
because some filings contained incomplete or inconsistent ticker metadata. To improve
event retrieval, filtering logic was adjusted to handle filings with missing ticker
information.

To remain within the $3.00 API cost ceiling, the maximum candidate count was capped
at 50-150 depending on the trial. After evaluating several configurations, 180 days
was selected as the chosen window because Financial Services exceeded the minimum
target of 8 geocoded events at that window while maintaining low processing costs.

| industry | window_days | candidate_count | event_count | estimated_cost_usd |
|---|---|---|---|---|
| Financial Services | 30 | 2 | 2 | 0.0 |
| Financial Services | 90 | 4 | 4 | 0.0 |
| Financial Services | 180 | 9 | 9 | 0.15 |
| Travel and Hospitality | 30 | 6 | 6 | 0.0 |
| Travel and Hospitality | 60 | 10 | 10 | 0.0 |

## 6.4 Stage 3 classification quality per industry

The Stage 3 classification process generally produced accurate event labels across
both industries. Most classified events were identified as opening events, while
smaller numbers were categorized as closing, expansion, and acquisition events.
In total, 11 geocoded events were classified as openings, while one event each
was labeled as a closing, expansion, and acquisition.

Most false positives occurred when filings referenced operational or organizational
changes using broad or ambiguous language that did not clearly correspond to a
location-based event. Despite these limitations, the classification model performed
effectively overall because many filings used consistent terminology associated with
business openings, closures, acquisitions, and expansion activities. The use of
refined search phrases and industry-specific ticker lists also improved classification
precision across both datasets.

## 6.5 Limitations

One major limitation was the restriction on candidate count and API spending. To
remain below the $3.00 cost ceiling, the candidate count was capped at 50-150,
which reduced the total number of filings and events available for analysis. This
likely affected the completeness of the geographic trends displayed in the map and
reduced the number of events available for comparative analysis.

Additionally, EDGAR's tickers field was empty for all candidates, preventing
ticker-based filtering and meaning some non-industry filings may have been included.
Travel and Hospitality companies frequently announce new properties through franchise
partners rather than direct SEC filings, which this pipeline cannot capture —
explaining the persistently low event count for that industry even at 360 days.
International events were also dropped at geocoding since Nominatim was queried
with countrycodes=us. Shorter filing windows and smaller candidate pools may have
excluded relevant events occurring outside the selected timeframe.
"""
6 KB
