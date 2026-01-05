
Prep for web mon monthly system migration
=========================================

Use Case: Seasonal Mean Anomolies
---------------------------------

Goal
++++

Produce seasonal mean anomolies for precipitation for GloSea

Inputs
++++++
  1. date forecast issued from
  2. definition of the forecast ensemble: lagged, 2 members a day, 21 days
  3. forecast member files containing monthly mean precipitation data
  4. definition of the hindcast ensemble: members per day, climatology method (weighting etc)
  5. hindcast climatology files containing monthly mean precipitation data (or seasonal?)

Outputs
+++++++
  1. files containing seasonal mean anomolies of geophysical fields about the hindcast climatology


Steps
+++++
  1. calculate the days in the lagged ensemble
  2. read the months in the season required from the forecast member monthly mean files
  3. calculate the seasonal mean for each member
  4. calculate the ensemble mean over forecast members
  5. calculate the hindcast climatology (with appropriate weighting etc)
  6. calculate difference between forecast ensemble mean and hindcast climatology
  7. save seasonal mean precipitation anomoly

Notes
+++++
  1. Runs weekly on a Monday
  2. Step 2 can be more complex than it appears as we have a lagged ensemble and so some months are not present in some files, and we have to deal with year boundaries etc.

Extensions
++++++++++

  1. calculate the monthly mean anomolies rather than seasonal means.
     * this is done by omitting the calculation of seasonal means and just use the monthly mean data
  2. calculate for near surface temperature or some other geophysical fields.
     * extra input: a list of fields to process
     * extra steps: need to loop over field

Use Case: Weekly Mean Anomolies
-------------------------------

Goal
++++

Produce weekly mean anomolies for precipitation for GloSea

Inputs
++++++
  1. date forecast issued from
  2. definition of the forecast ensemble: lagged, 4 members a day, 7 days
  3. forecast member files containing daily mean precipitation data
  4. definition of the hindcast ensemble: members per day, climatology method (weighting etc)
  5. hindcast files containing daily precipitation data 

In this case the inputs are daily fields, as that is what is produced by GloSea.

Outputs
+++++++
  1. files containing weekly mean anomolies of precipitation about the hindcast climatology


Steps
+++++
  1. calculate the days in the lagged ensemble
  2. read the days in the week required from the forecast member daily mean files
  3. calculate the weekly mean for each member
  4. calculate the ensemble mean over forecast members
  5. calculate the hindcast climatology (with appropriate weighting etc)
  6. calculate difference between forecast ensemble mean and hindcast climatology
  7. save weekly mean precipitation anomoly

Notes
+++++

  1. Runs daily
  2. Need more clarity on this use case around ensemble design, particularly for the hindcasts
  3. It may be more efficient to calculate all weekly means as a seperate step (as is done in web monitoring currently)
  4. The processing is broadly similar to the seasonal case, though there are differences due to different inputs, ensemble design and time sampling.

Notes on the implementation
---------------------------

The current implementation is set up for monthly mean data, with 21 day lagged forecast ensemble and a combine climatology based on 4 closest hindcasts dates.

``ForecastData`` class would need adapting to use daily or weekly mean data and a different ensemble design.  To support daily/weekly data would need to add something like ``shared.dates.avail_month`` but for daily/weekly data.  I think different ensemble designs (e.g. 7 day rather than 21) is fairly straight forward.

``GSDSCube`` uses months and years to build time constraints for load and extract.  These could be adapted to use weekly data. Or we might be able to have ``GSDSCube`` wrapper ``TimeSeries`` class for just the weekly data?  (I think this second approach would be a stepping stone - we'd aim to refactor over time to  make ``GSDSCube`` a do nothing wrapper that could just be replaced with ``TimeSeries`` and ``MonthlyTimeSeries``.
