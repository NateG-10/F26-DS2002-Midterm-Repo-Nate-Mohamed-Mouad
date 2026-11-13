# Helene forecast advisory 9 — Q4 source and validation

- Issuer: NOAA National Hurricane Center, storm AL092024.
- Issued: September 25, 2024, 15:00 UTC.
- Original: https://www.nhc.noaa.gov/archive/2024/al09/al092024.fstadv.009.shtml
- Retrieved from NOAA mirror: https://test-nhc-west.woc.noaa.gov/archive/2024/al09/al092024.fstadv.009.shtml
- Retrieved and verified: November 13, 2026.
- Local source: `nhc_helene_advisory_009.txt`, the HTML page's advisory `<pre>` text, with HTML entities decoded and outer whitespace trimmed. No forecast coordinates were changed.
- SHA-256 of the saved text: `7e41469c97f6de7b20cba8f538b4034af91741aa1e6d12b9a52d7d5f4d97b613`.

The main NHC URL returned HTTP 403 in the browsing tool. The NOAA mirror was accessible. The first shell download failed because sandbox DNS access was restricted; the authorized download succeeded. Notebooks use the saved text and do not require a live NHC request.

Both notebooks parse the advisory's current center plus seven forecast/outlook positions through September 29 at 12:00 UTC. The September 25 12:00 UTC historical center is excluded. Forecast positions are checked against the coordinates verified on the NOAA page. The final September 30 outlook says dissipated and supplies no position.

The forecast was issued late on the final day of the September 19–25 comparison window, after the exploratory September 10–14 spike. It is not evidence of the forecast available throughout that week or a cause of the earlier spike. Notebook prose documents the spherical segment-distance method and its limits.

## Execution and validation

Nate's scratch notebook was completed and executed before its Q1/Q4 cells were copied into the team notebook. Final runs used separate fresh Python 3.11 Jupyter kernels. The first sandbox kernel launch failed on local port permissions; authorized execution succeeded. All 6 scratch code cells and all 13 team code cells executed with real saved outputs and no errors. The team weather API request succeeded. The team execution runner allowed errors to continue so an unrelated network failure would not prevent Q1/Q4 execution; inspection found zero error outputs.

Checks passed for inclusive window lengths (4, 5, 7 days), category/daily quantity preservation, unique store reference keys, complete many-to-one store joins, 24,521 dated positive rows, 47,897 positive units, a 360-cell store/day grid, all 12 sales stores, and 11 geocoded stores. Tallahassee is excluded only from distance calculations. Missing-date exclusions and signed-net/baseline sensitivity checks remain in the notebooks.

Distance tests cover an interior point, an off-track interior projection, both endpoints, and all forecast vertices. An independent spherical-vector sampling calculation at no more than 0.1 km spacing agrees with store distances to within 0.000002 km. This is a numerical check, not a claim about forecast or coordinate accuracy.

Q1/Q4 source cells and their saved outputs are exactly identical between notebooks. All original team cell sources outside Q1/Q4 are unchanged. The full team run refreshed its outputs; two regenerated standalone Q5 PNG files were restored byte-for-byte to their original versions to keep the file changes scoped. No commit or push was performed.
