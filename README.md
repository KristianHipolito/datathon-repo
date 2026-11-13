# Water Boys Datathon Repository: Drainage objects & flooding predictions

# Our API datasets:
https://data.texas.gov/api/v3/views/8kc5-95uk/query.json
https://services.arcgis.com/g1fRTDLeMgspWrYp/arcgis/rest/services/StormInlets/FeatureServer/0/query?outFields=*&where=1%3D1&f=geojson


Suggested changes from our first drafted notebook:
The OpenAI-style client, server address, key and model name (now also overridable by environment variable).
The overall flow: load metadata, match image files, test one image, batch with checkpoints, StormInlets download, spatial step.
The image-to-base64 helper, the JSON parser and the file-matching logic.
The caution about not treating “no visible flooding” as “no flood”.
keep my paths
Change
Prompt: I rewrote the rules. “Overflow” now means water visibly coming out of or backing up at a drain, and “infrastructure visible” means a drain is actually in view.
Label checking: any label outside yes/no/uncertain is converted to “uncertain”. It tries the server’s JSON mode and falls back to prompt-only JSON, and retries failed images up to 3 times.
Batching: it runs several requests at once (WORKERS = 4). On resume it retries images that errored. Yours skipped any image already in the CSV, including failed ones.
StormInlets download: it now asks the server for the total count first and requests WGS84 coordinates. It pages in a fixed order and stops when it has them all, then caches the file and warns if the counts differ.
TCEQ: I replaced the v3/views/.../query.json call with the /resource/8kc5-95uk.json endpoint, paged and with coordinate detection. Yours only printed 5 raw rows. Mine downloads it and uses it in the matching.
Spatial matching: yours measured the nearest-inlet distance for flood images only, using the footprint or a point fallback. Mine checks inlets inside the footprint for every image, as you chose. It has no point fallback, and the nearest distance is now informational only.
Added
A Qwen connection check cell.
A footprint validity check, a duplicate-ID check and a coverage check (images outside the San Antonio inlet area are “not assessed”).
Active-inlet counts, inlet IDs and street names, and a sewer-overflow-in-footprint column.
One final CSV with the three answers per image, summary cross-tables, a PNG and an interactive HTML map.
An optional Qwen plain-language summary per image.
numpy and folium in the install cell.
Filtering
if there is no filtering aspect, add it as some images are from Houston area and do not align with San Antonio dataset.
