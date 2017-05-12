# FLOWMAPR

This repository houses the FLOW-MAP algorithm code, which was developed in R and originally published in Zunder et al. A Continuous Molecular Roadmap to iPSC Reprogramming Through Progression Analysis of Single Cell Mass Cytometry. Cell Stem Cell. 2015.

## Code Status
Please go to the Google Doc (https://docs.google.com/document/d/1O72i3V-hatKQc2_croKxpnPF5-nqCjOr8yxuHh4f67w/edit) and write your bugs/issues/suggestions there until public code release.

<!--
### Known Issues (“Bugs”)

### Planned Features
* improve “load” to allow loading cluster tables/matrix 

### Requested Features
* edges ranked within Gephi to remove/add to scale connectivity as we go
* add density rank of the vertices to nodes so that amount of downsampling can be changed, to remove/add nodes
* density rank can also be used to change amount of outlier removal
* adjust ratio of edges within same time point to edges between time points
* save template (panel, experimental set-up), at least save FLOWMAPR_run.R with set variables
-->

## Getting Started

The instructions below demonstrate how to install this package directly from Github to get the latest release.

### Software and Package Prerequisites:
Install version 3.3.0 or later of R. As of this release, we recommend using version 3.3.0.

In order to install a package from github, you will need the devtools package. You can install this package with the following commands:

```
install.packages("devtools")
library(devtools)
```

FLOWMAPR package depends on several packages, which can be installed using the below commands:

```
install.packages("Rclusterpp") 
install.packages("SDMTools") 
install.packages("igraph")
install.packages("robustbase")
source("http://bioconductor.org/biocLite.R")
biocLite("flowCore")
biocLite("spade")
```

Lastly, FLOWMAPR utilizes the R/C++ implementation of ForceAtlas2 as made available in the scaffold package from the NOlan Lab. Instructions to install that package are available here: https://github.com/nolanlab/scaffold. After installing all dependencies, you can install the package with the following commands:

```
library(devtools)
install_github("nolanlab/scaffold")
```

### Installing FLOWMAPR:

To currently get the FLOWMAPR R package up and working on your computer:

1. Make a github account if you haven't already.
2. Get access to repo zunderlab/FLOWMAP for your github account.
3. Create a token by going to this site when logged into your github account: https://github.com/settings/tokens/new
4. Check off "repo" in the settings for your token.
5. Click generate token and copy/save the provided code (your PAT) somewhere.
6. Open R studio and load devtools using `library(devtools)`. If you don't have devtools you may have to install it with `install.packages("devtools")` and then use `library(devtools)`.
7. Type the following into R studio: `install_github(repo = "zunderlab/FLOWMAP", auth_token = "PAT")` but replace PAT in quotations with your code in quotations. This should start installing all library dependencies so it may take a bit to finish. Check that it finishes without ERROR messages, though it may print WARNINGS.

### Installing the GUI

1. The GUI has a package dependancy for Shiny, TclTk, and Rhandsontable. Install these with:
```
install.packages("shiny")
install.packages("tcltk")
install.packages("rhandsontable")
```
2. The GUI is accessed by running:
```
FLOWMAPR::LaunchGUI()
```

### Updating FLOWMAPR:

To quickly update your FLOWMAPR R package up and get the latest version from GitHub:

1. Open R studio and load devtools using `library(devtools)`.
2. Type the following into R studio: `install_github(repo = "zunderlab/FLOWMAP", auth_token = "PAT")` but replace PAT in quotations with your code in quotations.
3. Load FLOWMAPR using `library(FLOWMAPR)`.

If the above commands run without error, you should have the latest version of FLOWMAPR.

## Running FLOWMAPR

To run a FLOWMAP analysis on your data set or an example data set:

0. Make your data available and parseable by FLOWMAP. For MultiFLOWMAP, you must specify the "files" variable as a directory wherein each subfolder represented samples at the same time. Please make sure the time labels can be parsed and sorted with proper labels (e.g. "01", "02", "04", "06", "10" vs. "1", "2", "4", "6", "10" where it would sort as "1" and "10" first instead of "10" last). To properly label each condition within the timepoint, please put the Condition as the first part of the file name separated by "-" or "." characters. <!--**Note: FLOWMAPR only works if there are no non-FCS files in the directory or subdirectories specified by files.**-->
1. Once you have successfully installed and loaded FLOWMAPR using `library(FLOWMAPR)`, if you are working in R Studio, you should see `FLOWMAPR::FLOWMAP()` autocomplete if you type it into the command line.
2. Establish variable names (you can copy the way they are assigned from the FLOWMAP_run.R file to declare each variable).  Some variables you have to assign are:
  * `seed.X` - an integer that sets the seed, can be re-used to reproduce results, default is set to 1
  * `mode` - what type of FLOW-MAP you want to run, this can be "single" - one condition, multiple timepoints, "multi" - multiple conditions, multiple timepoints or "one" - one condition, one timepoint
  * `files` - the directory where you can find the FCS files to be used
  * `var.remove` - any channels you want completely excluded from analysis
  * `var.annotate` - rename channels as you see fit, the names you provide will the ones used to print out the PDFs
  * `clustering.var` - which channels to use to influence the graph shape
  * `cluster.numbers` - how many clusters to generate from each subsampled file, recommended ratio 1:2 from subsample (if subsample = 1000, recommended cluster.numbers = 500), default is set to 100
  * `distance.metric` - choose "manhattan" or "euclidean" for most cases, default is set to "manhattan"
  * `minimum` - minimum number of edges allotted based on density, affects connectivity, recommended default is 2
  * `maximum` - maximum number of edges allotted based on density, affects connectivity, recommended default is 5
  * `per` - affects connectivity, recommended default is 1
  * `save.folder` - where you want the output files to be saved to, default is set to current directory or getwd() result
  * `subsamples` - how many cells to randomly subsample from each FCS file, default is set to 200
  * `name.sort` - sort FCS files according to name in alphabetical/numerical order, default is set to TRUE for sorting
  * `downsample` - use SPADE density-dependent downsampling, in which case you may want to specify and pass optional variables `exclude.pctile`, `target.pctile`, `target.number`, `target.percent`, default is set to FALSE for downsampling
  * `seed.X` - set this for reproducibility, default is set to 1
  * `savePDFs` - produce PDF files or only produce graphml files, default is set to TRUE for printing all results
  * `which.palette` - optional argument for savePDFs functionality, can be “jet” (rainbow) or “bluered” or “CB” (the colorblind-friendly option), default is set to "bluered"

3. Run `FLOWMAPR::FLOWMAP()` as a command in R Studio, but pass the variables that you assigned into FLOWMAP function. A full example is provided below.
4. Check that it saves an output folder with reasonable looking PDFs and graphml files.

### Example Code:

```
library(FLOWMAPR)
files <- "/Users/mesako/Desktop/SingleFLOWMAP"
mode <- "single"
save.folder <- "/Users/mesako/Desktop"
var.annotate <- list("marker1" = "marker1", "marker2" = "marker2")
var.remove <- c()
clustering.var <- c("marker1", "marker2")
subsamples <- 200
cluster.numbers <- 100
distance.metric <- "manhattan"
per <- 1
minimum <- 2
maximum <- 5
seed.X <- 1
name.sort <- FALSE
downsample <- FALSE
savePDFs <- TRUE
which.palette <- "bluered"

FLOWMAP(mode = mode, files = files, var.remove = var.remove, var.annotate = var.annotate,
        clustering.var = clustering.var, cluster.numbers = cluster.numbers,
        distance.metric = distance.metric, minimum = minimum, maximum = maximum,
        per = per, save.folder = save.folder, subsamples = subsamples,
        name.sort = name.sort, downsample = downsample, seed.X = seed.X,
        savePDFs = savePDFs, which.palette = which.palette)
```

#### Template for a FLOWMAPR Run in R

You can also access example FLOWMAPR runs as .R files, which you can then modify to work with your own data.

To access a SingleFLOWMAP run without downsampling (random subsampling):
```
file.edit(system.file("tools/run_FLOWMAPR.R", package = "FLOWMAPR"))
```

To access a SingleFLOWMAP run with SPADE downsampling:
```
file.edit(system.file("tools/run_downsample_FLOWMAPR.R", package = "FLOWMAPR"))
```
You will need to change the `files` and the `save.folder` to point to folders on your own computer. Furthermore, you will need to update the `var.annotate`, `var.remove`, and `clustering.var` parameters to reflect channels in your own data. You can change all other parameters based on your analysis needs.

### Example Data:

An example (synthetic) data set is available as raw FCS files with the FLOWMAPR package for testing purposes. You can access these data sets by finding their directory on your computer using the following commands after you have installed and loaded FLOWMAPR.

To access the SingleFLOWMAP data (one condition, one time course data in a single folder of FCS files):
```
files <- system.file("extdata/SingleFLOWMAP", package = "FLOWMAPR")
```

To access the MultiFLOWMAP data (two conditions, one time course data in a folder that contains subfolders of FCS files, wherein each subfolder is numbered according to sequential timepoints):
```
files <- system.file("extdata/MultiFLOWMAP", package = "FLOWMAPR")
```

Supply this `files` variable as the `files` parameter in a `FLOWMAPR::FLOWMAP()` command.


### Practical Guidelines for Running FLOWMAPR:

FLOWMAPR is an R package for visualization of high-dimensional data, though ultimately producing visualizations is a subjective process. If you are not sure where to start or what settings to use, here are some basic guidelines for your analysis:

0. Consider what your question is for your dataset. FLOWMAPR can just be used to explore a given timecourse dataset, but generally your choice of settings will be simplified if you have a directed question. For example, you may be interested in how a biological process changes with different treatment conditions, or how different subpopulations change in a given set of markers during a process. 
1. Analyze your timecourse data by some conventional means (heatmaps, histogram, dotplots, contour plots) to get an intuition for the data and to be able to answer, at least partially, the following questions:
+ What are some of the different subpopulations in my data, especially those of interest to my question? What fraction of the total number of cells do each of these subpopulations represent? This info can inform whether you choose to downsample (`downsample <- TRUE`) or randomly subsample, or your clustering ratio (`subsample` : `cluster.numbers`).
+ What markers do or do not change across the timecourse? Generally, you will not want to include completely uninformative markers as clustering variables (`clustering.var`). This choice can be guided by the data (what you observe changing) and biological expert knowledge (what you know should change in the process you profiled).
+ What are some expected trajectories? That is, what are some cell subpopulations you expect to observe, and what changes should you observe in these cells over time. Any expert knowledge can help you know what to look for (confirmation in your results) after a FLOWMAPR run before you investigate novel findings.
+ Are there any parameters that are useless or unrelated to your biological question? You can try not removing any markers, but we generally recommend you include markers with no relevance (e.g. DNA, Event_length, Eubeads) in `var.remove` so that they do not carry through the analysis and no PDFs are generated for them. This step will save small amounts of time and make the graph less inconvenient to navigate in Gephi.
2. Start off with a small number of clusters and generally keep the clustering ratio larger (`subsample` close to, either equal to or slightly less than, `cluster.numbers`). Though the resulting figures may not be fully representative of the variation in the data, these settings will allow you to quickly iterate through different configurations of edge settings and different choices of clustering variables. Given that a graph with about 1200 total nodes or clusters takes 2 minutes to run, try starting with cluster numbers set to approx. 1200 / # of files. For example, if you have a single timecourse with 5 FCS files, try setting `cluster.numbers <- 250` and `subsamples <- 500`.
3. You will need to do several iterations of FLOWMAPR to try to arrive at appropriate settings for `minimum`, `maximum`, and `per` as well as `clustering.var`. The order in which you proceed depends on the results you see, so you may need to reverse the order of the steps (4-5) below. For example, the default edge settings may be wrong for any `clustering.var` you try, in which case you should tweak edge settings and then compare different `clustering.var`.
4. Try using the default edge settings for `minimum`, `maximum`, and `per` with different options for `clustering.var`. These results will show you how informative different sets of markers are. You should try to narrow down to a particular marker set that you can use to refine the edge settings.
5. Once you select `clustering.var`, you can change edge settings `minimum`, `maximum`, and `per` to try to arrive at the maximal separation within your data. Generally you want to arrive at a graph that best resolves difference and allows for spread of different trajectories in the data, so that it captures as much info from the high-dimensional shape of the data as possible. Here are some guides for how to tweak these edge settings:
+ If the graph is too interconnected (hairball-like), try reducing `maximum`. You can try reducing `minimum` to 1, but generally we recommend that `minimum` is at least 2. Try moving `maximum` to being at most `minimum` + 1.
+ If the graph is not interconnected enough (spiky, single nodes radiating out), try increasing the `minimum` and/or `maximum`.
+ We generally suggest keeping `per` at 1, but if you try all reasonable combinations of `minimum` and `maximum`, you can reduce `per` to reduce connectedness or increase `per` to increase connectedness.
+ Be careful as it's possible for graphs to essentially become tangled as they are processed with a force-directed layout. If results do not look useful, check for these tangles that can be resolved in Gephi. Additionally, the force-directed layout step is a computationally intensive and time-consuming step, so it is possible within the R package that the process does not complete. These graphs can be resolved to a stable shape in Gephi.
6. Once you arrive at a FLOW-MAP with the "best" settings, repeat the analysis with multiple settings of `seed.X` to produce "technical replicates" of your analysis. For steps that involve randomness, such as random subsampling of your FCS files, you will want to try to reproduce your FLOW-MAP figures with different samplings.

#### Timing for FLOWMAPR Runs

In a singleFLOWMAP with no downsampling (uses random subsampling), 1200 total nodes takes about 2 min to produce results (including PDFs). In comparison, 3000 total nodes takes about 6 min to produce all results and 6000 total nodes takes about 21 min. These all ran with a `subsample` : `cluster.numbers` ratio of 2:1.

## Using the GUI
0. Make sure all FSC files that are to be tested are within one folder.
1. When `FLOWMAPR::launch_GUI()` is run, a dialogue box of the header "FLOWMAP" should appear. 
2. Enter in all of the relevant information which pertains to the type of experiment that is being analyzed.
3. When "Submit" is pressed, a new window should appear which runs with Shiny.
4. Select the order of the FCS files that you wish to use. If there is only 1 FCS file, choose the number of the file that is shown.
5. Press "Generate Parameters."
6. Two things will now happen: an interactive table will appear with all the parameters and options for selecting and deselecting them for analysis, and the menus for "Similar Fields" and "Different Fields" will autopopulate as an aid to help you choose relevant channels.
7. If there needs to be any channel that needs to be merged, select the files from the "Different Fields" window, enter the new merged name in "Select New Merge Name", and press "Merge Selected Diff". This will automatically remove the channels from "Different Fields", add the merged name to "Similar Fields", and will update the table with new annotations.
8. The different parameters will by default be checked for removal, and the user must check the parameters for clustering. If the user wishes to rename a parameter, they can click on the name under "annotate" and type a new name. 
9. Press "Write File" once the appropriate parameters have been checked and renamed to run the FLOW-MAP algorithm and generate the FLOW-MAP results (PDFs, graphml files, etc. in a new folder).

## Contributing

????

## Versioning

???

## Authors

* **Eli Zunder** - *Initial work*
* **Melissa Ko** - *???*

See also the list of [contributors](https://github.com/zunderlab/FLOWMAP/graphs/contributors) who participated in this project.

## License

This project is licensed under the GNU GPLv3 License - see the [LICENSE.md](LICENSE.md) file for details.

## Acknowledgments

* ???
