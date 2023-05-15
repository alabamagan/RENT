## Modifications in this forked repo

### Summary of modifications

This is a summary of modifications made to the original RENT in this forked repo:

* For bootstrapping before each repeated Elastic Net run, the bootstrapping was done with stratification based on the target label distribution.
* Boosting was added to Elastic Net for `RENT_Regression()`. The rationale is that even the current selection based on learnt E-Net coefficients, but these coefficients were optimized with a soft-margin and ignored the sample points that lies in the soft-margin. By taking into account the weighted coefficient of the weaker learning in the boosted ensemble of Elastic Nets, this could be improved.
* Coefficients of features learnt were further ranked by their normalized mean and variance, which translate to their importance and stability. An option `n_features` were added to suggest the maximum number of features to return.

### Purpose

This forked package was not suppose to be used independently. Feature selection is a task where noise in the data is almost certainly expected. There is strong evidence that boosting is susceptible to the influence of noise in the training data such that you might see a drop in stability in general if you use the boosting setting. This is because, with boosting, slight changes in the training dataset (due to the resampling inner-loop of RENT) will lead to various irrelevant features being selected. The boosted RENT was not intended to be used alone, but together with bagging. In more laymen terms, boosting RENT allows it to select more relevant features, but would also result in selecting more noise features. Luckily, these noisy features are also susceptible to changes in the training data that we can apply an extra layer of bagging to cancel out (or at least minimize).

Therefore, this repo should be used with an extra layer of bagging (BB-RENT). See more [here](https://github.com/alabamagan/mri_radiomics). Note that this package is not officially released and you can go to the branch `pre-release` for the nightly build.

Example
-------

Everything remains the same as the main repo, except there is now an additional option to use boosting instead of just elastic net:

```python
model = RENT.RENT_Regression(data=pd.DataFrame(features),
                             target=_targets.ravel(),
                             feat_names=_features_names,
                             C=C,
                             l1_ratios=l1_ratio,
                             autoEnetParSel=False,
                             poly='OFF',
                             testsize_range=(1/float(n_splits), 1/float(n_splits)),
                             K=n_trials,
                             random_state=0,
                             verbose=1,
                             scale=False,
                             boosting=boosting) # BRENT or RENT
```

Below are links to Jupyter-notebooks that illustrate how to use RENT for

* [classification](https://github.com/NMBU-Data-Science/RENT/blob/master/examples/Classification_example.ipynb)
* [regression](https://github.com/NMBU-Data-Science/RENT/blob/master/examples/Regression_example.ipynb)
* [hyperparameter search](https://github.com/NMBU-Data-Science/RENT/blob/master/examples/Extensive_hyperparameter_search.ipynb)

Requirements
------------

Make sure that Python 3.5 or higher is installed. A convenient way to install Python and many useful packages for scientific computing is to use the [Anaconda Distribution](https://www.anaconda.com/products/individual)

* numpy >= 1.11.3
* pandas >= 1.2.3
* scikit-learn >= 0.22
* scipy >= 1.5.0
* hoggorm >= 0.13.3
* hoggormplot >= 0.13.2
* matplotlib >= 3.2.2
* seaborn >= 0.10

Installation
------------

To install the package with the pip package manager, run the following command:
`python3 -m pip install git+https://github.com/NMBU-Data-Science/RENT.git`

Documentation
-------------

Documentation is available at [ReadTheDocs](https://rent.readthedocs.io/en/latest/). It provides detailed explanation of methods and their inputs.

Citing RENT
-----------

If you use RENT in a report or scientific publication, we would appreciate citations to the following paper:

Jenul et al., (2021). RENT: A Python Package for Repeated Elastic Net Feature Selection. Journal of Open Source Software, 6(63), 3323, https://doi.org/10.21105/joss.03323

Bibtex entry:

@article{RENT,
doi = {10.21105/joss.03323},
url = {https://doi.org/10.21105/joss.03323},
year = {2021},
publisher = {The Open Journal},
volume = {6},
number = {63},
pages = {3323},
author = {Anna Jenul and Stefan Schrunner and Bao Ngoc Huynh and Oliver Tomic},
title = {RENT: A Python Package for Repeated Elastic Net Feature Selection},
journal = {Journal of Open Source Software}
}
