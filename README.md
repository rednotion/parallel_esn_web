<br/><br/>

Final Project for Harvard CS205: Computing Foundations for Computational Science ([course website](http://iacs-courses.seas.harvard.edu/courses/cs205/index.html))
**Contributors:** Zachary Blanks, Cedric Flamant, Elizabeth Lim, Zhai Yi

## Package Instructions
Github Repo: [link](https://github.com/zblanks/parallel_esn)

(Technical description of the software design, code baseline, dependencies, how to use the code, and system and environment needed to reproduce your tests)

## Project Overview
Echo State Networks (ESN) are recurrent neural networks making use of a single layer of sparsely connected nodes ('reservoir'). They are often used for time series tasks, and can be less computationally intensive other than deep learning methods. However, ESNs require fine tuning of many parameters, including the input weights, the reservoir (e.g. how many nodes in the reservoir, what is the spectral radius, etc). This has usually been done through either (a) sequential testing and optimization; or (b) instantiating many random instances, and then picking the best performing set of parameters. Depending on the length of the input data and the size of the reservoir, ESNs can thus be computationally intensive to train. In addition, we have to repeat this training many times before arriving at a good set of parameters. 

We propose to make use of parallel computing architectures to not only make this process **faster**, but also **smarter**. We do this through:
1. Setting the reservoir to be a _small world network_ with key properties to be defined 
2. Using _bayesian optimization_ to iteratively find the best set of parameters
3. Training the network faster through distributed computing with multiple nodes and multiple threads (_OpenMP_ and _MPI_)

- Description of problem and the need for HPC and/or Big Data
- Description of solution and comparison with existing work on the problem

## Echo State Networks
- Description of your model and/or data in detail: where did it come from, how did you acquire it, what does it mean, etc.
- Technical description of the parallel application and programming models used

An ESN is made up of the following components:
1. Input data `u(t)`
2. Input weight matrix `W_in`
3. Reservoir matrix `W`: In our set-up, this will be a _small world network_ that can be defined by (a) the number of nodes and (b) the spectral radius `p`
4. An output weight matrix `W_out` that is trained so as to minimize the least squares error on the validation set. The `W_out` matrix can then be used with any new input data to produce predictions.

![width = 8cm](https://github.com/rednotion/parallel_esn_web/blob/master/Screenshot%202019-04-30%20at%206.34.15%20PM.png?raw=true)

_(To be completed: How training an ESN works)_

## Bayesian Optimization


## Architecture & Features
- Technical description of the platform and infrastructure 
- Description of advanced features like models/platforms not explained in class, advanced functions of modules, techniques to mitigate overheads, challenging parallelization or implementation aspects...

### Computing Architecture
The set-up of the Parallel ESN is depicted in the figure below: There will be one leader node that manages the bayesian optimization. It distributes a set of paramater to each worker node to try, and upon completion of the ESN training, the worker node will report back the validation error associated with those parameters, for the leader node to update it's posterior belief before distributing new parameters. The computing architecture of this process represents **coarse-grained parallelism**.
![width = 8cm](https://github.com/rednotion/parallel_esn_web/blob/master/Screenshot%202019-04-30%20at%206.35.07%20PM.png?raw=true)

### Other features
In addition to coarse-grained parallelism, we also attempt to optimize the training of each individual ESN for **fine-grained parallelism**. In addition to Cythonizing parts of the function (providing typing information), we also use **multi-threading** for the matrix multiplication operations, since those account for a large proportion of computation.

Finally, the entire algorithm is also downloadable and implementable as a **Python package**, which can be accessed at the GitHub repo [here](https://github.com/zblanks/parallel_esn). The package is fully functional, including testing checks, error messages, and examples. 

## Data 
- Description of your model and/or data in detail: where did it come from, how did you acquire it, what does it mean, etc.

## Empirical Testing & Results
- Performance evaluation (speed-up, throughput, weak and strong scaling) and discussion about overheads and optimizations done


## Conclusions
Discussion about goals achieved, improvements suggested, lessons learnt, future work, interesting insights…

## Citations
Kawai, Y., Tokuno, T., Park, J., & Asada, M. (2017). Echo in a small-world reservoir: Time-series prediction using an economical recurrent neural network. 2017 Joint IEEE International Conference on Development and Learning and Epigenetic Robotics (ICDL-EpiRob). doi:10.1109/devlrn.2017.8329797

Lukoševičius, M. (2012). A Practical Guide to Applying Echo State Networks. Lecture Notes in Computer Science Neural Networks: Tricks of the Trade, 659-686. doi:10.1007/978-3-642-35289-8_36

https://www.pdx.edu/sites/www.pdx.edu.sysc/files/Jaeger_TrainingRNNsTutorial.2005.pdf
https://arxiv.org/pdf/1611.05193.pdf
