Correspondence analyses of the Mueller Report
================

# The gist

  - The Mueller Report recently dropped and \#rstats community quickly
    went to work.
  - I saw two analyses on word frequencies ([by Mara
    Averick](https://github.com/batpigandme/tidymueller)) and
    co-occurence networks ([by Chris
    Bail](https://github.com/cbail/mueller_report)).
  - I wanted to apply correspondence analyses to slightly modified
    versions of their data
      - akin to how [we analyzed the NeuroSynth
        database](https://www.biorxiv.org/content/10.1101/157826v3).
      - Correspondence analysis is a technique like principal components
        analysis, but designed for categorical, count, non-negative, and
        similar (i.e., χ2-friendly) data
  - The goals of the analyses were several fold:
      - Treat the data in a bi-variate way to get information for *both*
        the pages and the words/lemmas.
      - Assess the similarity and grouping of *pages* within each data
        set, i.e., with respect to preprocessing/preparation of the data
      - Compute the similiarity between the component structures from
        each analysis, i.e., do we see similar or different component
        structures based on the different versions (words vs. lemmas) of
        the data.

## Short version of the results

  - Surprisingly: Both versions of the data produce effectivley
    identical results for approximately the first 8 (possibly more)
    components
      - I did not expect this because the two data sets are different
        (words are individual words, lemmas are particular words,
        letters/keys, or phrases/n-grams)
  - The beginning and end pages of the Mueller Report are effectively
    orthogonal to one another, with highly specific words/lemmas for
    each
  - There is an extraordinary density of repeated terms in the middle of
    the report

# Methods

## Cleaning and preprocessing

In `./R/0_read_preprocess.save.R`, I use the same base cleaning steps as
in [batpigandme/tidymueller](https://github.com/batpigandme/tidymueller)
and [cbail/mueller\_report](https://github.com/cbail/mueller_report). I
use some slight modifications of those for extra cleaning and
maintaining particular data features or the data shapes. This file
exports some `.rda` files for subsequent use.

In `./R/1_make_clean_matrices.R`, I reshape the data into a rectangle
(pages x words or pages x lemmas), then prune out any excessively
infrequent rows and columns, and finally export the “analysis ready”
matrices as `.csv` files.

## Analyses

All analyses were performed here in `README.rmd`. I applied
correspondence analysis (CA) to two versions of the data:

  - pages x words: each cell contains the frequency of that word on that
    page (via
    [batpigandme/tidymueller](https://github.com/batpigandme/tidymueller))

  - pages x lemmas: each cell contains the frequency of that lemma on
    that page (via
    [cbail/mueller\_report](https://github.com/cbail/mueller_report))

I used CA from one of my packages:
[ExPosition](https://cran.r-project.org/web/packages/ExPosition/index.html).
CA is a technique like principal components analysis (PCA), but designed
for categorical, count, non-negative, and similar (i.e., χ2-friendly)
data. When data are proportional, compositional, and generally
χ2-friendly, PCA is not suitable. That’s because PCA wants to compute a
covariance (or correlation) matrix from the data. In this case, the data
should not (cannot\!) be normalized down the columns, so PCA is not
appropriate. CA normalizes the data with respect to both the rows and
columns, and provides a χ2-projection onto a Euclidean space. CA also
has innumerable nearly magical properties (one of those magical
properties is that if you have a network with a clique, CA produces
eigenvalues of exactly 1 for every clique; that’s something for later…).

Like PCA, CA produces orthogonal components that we rank order by their
explained variance. So, the first component explains the most amount of
variance, while the second component explains the next most amount of
possible variance, where the second component is orthogonal to the
first. The third component explains the next most variance after the
second, where the third component is orthogonal to the second (and thus
the first), and so on. For each of these components we can plot the
component scores for the rows (pages) and columns (words or lemmas)
separately on component maps. Because CA is a bi-variate technique, so
the scales of the component scores for the rows and columns are the same
(with caveats; also something for later\!).

These analyses were done for two reasons: (1) emphasize the pages (not
the words), and (2) assess how similar the results are from each type of
text preprocessing. Other analyses have focused on words and word
frequencies. While that is part of the results here, it’s not much
different from what other analyses find. Instead, the emphasis here is
on the pages. We’re using CA here to extract the components of pages and
words, but we can take a closer look at the pages to see if some kind of
structure emerges from the similarity of pages. Once we’ve done both
analyses, we can then find out if processing the document in two
relatively different ways produces similar or different results, with
respect to the overall structure of page similarity.

Results were plotted with [ggplot2](https://ggplot2.tidyverse.org/) and
[corrplot](https://cran.r-project.org/web/packages/corrplot/index.html).
Scree plot code adapted (mostly stolen\!) from [Matt
Kmiecik](https://mattkmiecik.com/post-Exploring-11-Years-of-Chicago-Blackhawks-Data-using-Principal-Components-Analysis.html).

# Correspondence analyses of the Mueller Report

The pages x words data matrix was 436 rows (pages) by 2910 columns
(words). The pages x lemmas data matrix was 448 rows (pages) by 2647
columns (lemmas).

## Explained variance

Each analysis produces more than 400 components. The explained variance
per component can be visualized with a Scree plot, which shows the
components in descending (explained variance) order from the first to
the last. The Scree plot tells us how much variance each component
explains, and where the “Scree” (junk at the bottom of a slope) happens.

![](README_files/figure-gfm/vis_scree-1.png)<!-- -->

Both analyses show that the first 15 or so components explain
considerably more variance (proportionally) than the remaining
components. The “Scree” part happens at around 100 components (the bend)
but I’m not going to interpret that many components (this is an exercise
for the reader).

## Component maps

When we plot pages (or words, or lemmas) on Component maps, we can
identify overall structures within the data. We can also see how various
pages (or words, or lemmas) are related. On the components, if we see
any particular pages, words, or lemmas (a.k.a “items”) near one another,
then we know they are positively correlated. If we see any items far
apart from one another along a line, then they are negatively
correlated. If we see any items near or at a 90 degree angle, then they
are orthogonal to one another.

![](README_files/figure-gfm/vis_components-1.png)<!-- -->

The above component maps show the pages x words analysis on the top and
the pages x lemmas on the bottom. On the left side we see the component
scores for the pages and on the right we see the component scores for
the words (top) and lemmas (bottom). The pages have been colored along a
gradient. We’re only going to focus on the pages (the words are more
numerous and dense).

First, I’m not into component/factor rotations, but these components
scream “rotate me\!”. Interestingly, we see a nearly perfect orthogonal
structure emerge for both, but just slightly off from the components
(axes). Second, both analyses show quite an interesting structure of the
pages: the beginning of the document (blue) is at a near perfect right
angle to the end of the document (red). Furthermore, the beginnings and
ends of the document are unique, as we can see that these are very far
from the center of the graph (origin). Thus the words found on those
pages are both (1) rarely used and (2) exist almost exclusively on those
pages. As for the middle (purple-ish dots) of the document, there are
frequent uses of many of the same words.

## Similarity between analyses

Given that the two analyses were on two differently processed data sets,
I was curious if these two data sets produced similar or different
results. To do so, I computed the correlations (and visualized with a
correlogram via `corrplot`) between the component scores of each
analysis for the pages common to both. Some pages for each data set were
dropped because of excessively low frequency of words or lemmas.

![](README_files/figure-gfm/vis_corrplot-1.png)<!-- -->

The `corrplot` above shows that the first 8 (possibly 11, or 13)
components are virtually identical. I did not expect that\! It seems
that for both of these preprocessing approaches, the same low rank
structure emerges. Between the first 8 components, the Rv-coefficient
(akin to a multivariate R-squared) was 0.899.

# Conclusions & More Analyses

So what can we conclude from this? First is that both preprocessing
approaches produce almost identical results by the end of analyses. This
suggests that the granularity of the words is sufficient enough compared
to the lemmas. But that also means that the separate words in phrases
picked up by the lemmas are also likely co-occuring in high frequency
both within and across pages.

We also see that, generally, there are completely different word usages
at the beginning and end of the document. Other components may also help
tease apart where highly similar (or nearly orthogonal) sections of the
document exist (again: an exercise for the reader).

Also just amongst these analyses are a lot of results to interpret. For
example, what are the words/lemmas that underly the page structure that
makes the beginning and end so orthogonal? (another exercise for the
reader\!)

As for more analyses, I had hinted as some *very* cool properties of CA
when it comes to graphs. In the future (the distant future, like 1999 or
so) I plan to come back to these data because this is a highly rich
dataset that can be analyzed in a number of ways. There are two
particular branches of analyses for the future:

  - A thresholded version of these data (0 vs. 1 for just absence
    vs. presence of a word; no need for counts) is effectively a graph.
    CA has nearly magical properties that allows us to detect global
    network structures and cliques (if they exist) from either the
    crossproduct (adjacency or weighted adjancency) matrices or just
    directly on the rectangular (binary) matrices (both ways produce
    identical results). To note: the current results are also
    effectively showing what amounts to bi-variate graph structures: a
    global network structure for the pages and a global network
    structure for the words/lemmas.

  - Analyses to detect anomalous pages and words. With a technique we
    [recently
    developed](https://www.biorxiv.org/content/10.1101/333005v2.abstract)
    (that is an extension of the minimum covariance determinant) we can
    identify both (1) the robust sub-structure in the data and (2) any
    anomalous (outlier) words.
