# Utility-Scale-Solar-Performance-and-Diagnostic-Pipeline
This project is an end-to-end automated data pipeline and interactive Tableau dashboard designed to ingest raw SCADA and meteorological (MET) station data, standardize it, and mathematically isolate Soiling Losses from Equipment Downtime.

Architecture
Python (Pandas, NumPy): Automated ETL, timestamp alignment, and commercial feature engineering.
Tableau: Interactive diagnostic dashboarding with synchronized filter actions.
Data Source: 34 days of simulated 15-minute interval SCADA data for a 22-inverter solar facility and its central MET station from Kaggle

The Python pipeline performs several critical transformations before the data ever touches Tableau:
Timestamp Standardization: The generation data and weather data were logged in conflicting formats (DD-MM-YYYY HH:MM vs YYYY-MM-DD HH:MM:SS). The pipeline enforces a strict datetime64 standard to allow accurate left-joining of MET conditions to specific inverter outputs.
The Availability Filter: Created a binary operational flag. If irradiation is present but DC Power is 0, the inverter is flagged as "Offline/Tripped." This is strictly separated from efficiency calculations to prevent downtime from skewing the soiling index.
True Soiling Index (Normalized DC): Calculates instantaneous specific yield (DC_POWER / IRRADIATION) strictly during peak sun hours (10:00 - 14:00) while the equipment is online.
Thermal Delta Isolation: Calculates the exact heat buildup on the panels (MODULE_TEMPERATURE - AMBIENT_TEMPERATURE) to flag thermal stress events exceeding a 25°C safety threshold.

Dashboard Results

https://public.tableau.com/views/SolarPlantOperationalDIagnostics/Dashboard1?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link

Commercial Insights & Dashboard Results

1. Systemic Environmental Event (Week 22 Soiling/Clipping)
The weekly aggregation heatmap revealed a severe, array-wide drop in specific yield during Week 22, followed by an immediate recovery to baseline in Week 23. Because the availability scorecard confirmed the equipment was online (99.9% uptime), this visualizes a definitive systemic event—such as heavy dust accumulation followed by a site-wide wash, or severe array-wide thermal clipping.
2. Localized Hardware Failures (Revenue at Risk)

While 20 inverters performed at a healthy baseline, the pipeline successfully isolated two specific inverters (1 and 6) that were actively bleeding revenue. The downtime scorecard proved they were suffering from repeated trips during peak irradiance, dropping to ~91% uptime in the final weeks.

3. AC/DC Conversion Stability
Despite severe thermal spikes on May 23 and May 30 (where module temperatures exceeded 60°C), the Inverter Efficiency Heatmap remained uniformly stable at a median of 97.7%. This diagnostic proves that the plant's internal AC/DC conversion hardware survived the thermal stress without experiencing internal clipping or heat-induced degradation.
