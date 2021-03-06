# VersatileHDPMixtureModels.jl


This package is the code for our UAI '20 paper titled "Scalable and Flexible Clustering of Grouped Data via Parallel and Distributed Sampling in Versatile Hierarchical Dirichlet Processes". <br>
[Paper](https://www.cs.bgu.ac.il/~orenfr/papers/Dinari_UAI_2020.pdf),
[Supplemental Material](https://www.cs.bgu.ac.il/~orenfr/papers/Dinari_UAI_2020_supmat.pdf) <br>


### What can it do?

This package allows to perform inference in the *vHDPMM* setting, as described in the paper, or as an alternative, it can perform inference in *HDPMM* setting.

#### A note on scalability

With the recent release (0.1.1) we have added threads support (instead of multiprocessing) as default. to enable multiprocessing instead add `mp=true` to the fit functions.
Using the multithreaded version, we can now handle more groups, much more. Just to emphasize, we have recently used it with 7k groups, summing to a total of 220MIL data points, each data point a `D=256` histogram. Convergance took only 4 hours. In another scenario we have used it for topic modeling, with 84K documents, each between 100 to 300 words, convergance took about an hour.

### Quick Start

1. Get Julia from [here](https://julialang.org/), any version above 1.1.0 should work, install, and run it.
2. Add the package `]add VersatileHDPMixtureModels`.
3. Add some processes and use the package:
```
using Distributed
addprocs(2)
@everywhere using VersatileHDPMixtureModels
```
4. Now you can start using it!
* For the HDP Version:
```
# Sample some data from a CRF PRIOR:
# We sample 3D data, 4 Groups, with $\alpha=10,\gamma=1$. and variance of 100 between the components means.
crf_prior = hdp_prior_crf_draws(100,3,10,1)
pts,labels = generate_grouped_gaussian_from_hdp_group_counts(crf_prior[2],3,100.0)


#Create the priors we opt to use:
#As we want HDP, we set the local prior dimension to 0, and the global prior dimension to 3
gprior, lprior = create_default_priors(3,0,:niw)

#Run the model:
model = hdp_fit(pts,10,1,gprior,100)

#Get results:
model_results = get_model_global_pred(model[1]) # Get global components assignments
##
```

* Running the vHDP full setting:
```
#Generate some data:
#We generate gaussian data, 20K pts each group, Global Dim= 2, Local Dim = 1, 3 Global components, 5 Local in each group, 10 groups:
pts,labels = generate_grouped_gaussian_data(20000, 2, 1, 3, 5, 10, false, 25.0, false)

#Create Priors:
g_prior, l_prior = create_default_priors(2,1,:niw)


#Run the model:
vhdpmm_results = vhdp_fit(pts,2,100.0,1000.0,100.0,g_prior,l_prior,50)

#Get global and local assignments for the points:
vhdpmm_global = Dict([i=> create_global_labels(vhdpmm_results[1].groups_dict[i]) for i=1:length(data)])
vhdpmm_local = Dict([i=> vhdpmm_results[1].groups_dict[i].labels for i=1:length(data)])
```


### Examples:
[Coseg with super pixels](https://nbviewer.jupyter.org/github/BGU-CS-VIL/VersatileHDPMixtureModels.jl/blob/master/examples/Coseg.ipynb) <br>
[vHDP as HDP](https://nbviewer.jupyter.org/github/BGU-CS-VIL/VersatileHDPMixtureModels.jl/blob/master/examples/vHDPasHDPGMM.ipynb) <br>
[Missing data experiment](https://nbviewer.jupyter.org/github/BGU-CS-VIL/VersatileHDPMixtureModels.jl/blob/master/examples/MissingData.ipynb) <br>
[Synthethic data experiemnt](https://nbviewer.jupyter.org/github/BGU-CS-VIL/VersatileHDPMixtureModels.jl/blob/master/examples/SynthethicData.ipynb)

### License

This software is released under the MIT License (included with the software). Note, however, that if you are using this code (and/or the results of running it) to support any form of publication (e.g., a book, a journal paper, a conference paper, a patent application, etc.) then we request you will cite our paper:

```
@inproceedings{dinari2020vhdp,
  title={Scalable and Flexible Clustering of Grouped Data via Parallel and Distributed Sampling in Versatile Hierarchical {D}irichlet Processes},
  author={{Dinari, Or and Freifeld, Oren},
  booktitle={UAI},
  year={2020}
}
```

### Misc

For any questions: dinari at post.bgu.ac.il

Contributions, feature requests, suggestion etc.. are welcomed.



