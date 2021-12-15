# Bay_Stratification
Graphics to show vertical stratification in water quality in Casco Bay, Maine.

While we did not report on stratification of the water column in our most recent
State of Casco Bay report, we reviewed data that provides insight into
hydrodynamic processes.  We review some of that data here.

Data is derived from Maine Department of Environmental Protection (DEP) and
Friends of Casco Bay vertical profile data. DEP put considerable effort over the
past few years into collecting data along estuary transects.  FOCB data
complements the DEP data by providing repeated observations from fixed locations
over a period of up to thirty years.

The focus of the code and graphics provided in this repository is developing
graphic summaries of seasonal and spatial patterns in stratification in several
of Casco Bay's sub-estuaries, especially the  Presumpscot and Royal estuaries.
Data is also provided here to develop similar graphics for the Fore and
Harraseeket estuaries.  The code and plots depend principally on the DEP data.
We include the FOCB data here as well to facilitate future analysis.

## Supporting software
We developed a small R package to facilitate display of depth profile data in R.
The R notebooks in this repository depend on that package. The package,
`tdggraph` can be downloaded from a github repository at
https://github.com/CBEP-SoCB/tdggraph.
