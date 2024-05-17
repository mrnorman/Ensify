# Ensify

Ensify is meant to make ensembles easy to create inside an MPI executable. It captures stdout and stderr for each ensemble, and it can run the same executable on different inputs for each ensemble on a different set of the MPI tasks.

* Add `#include "Ensify.h"` to the includes (above `using namespace std` as otherwise, there are clashes)
* Add `ensify::init(argc,argv);` after `MPI_Init`
* Add `ensify::finalize();` just before `MPI_Finalize`
* Change `MPI_COMM_WORLD` to `ensify::comm()` at every occurrence in the code
* Change the `argc` and `argv` from `main()` to `ensify::argc()` and `ensify::argv()`
  - These behave identically to the `argc` and `argv` passed into `main()`, but they are unique to each individual ensemble. `argv` contains the executable name followed by the per-ensemble "command line" arguments specified in `ensembles.yaml`.
  - If you wish, you can also access the arguments after the executable name with `ensify::argvec()` as a `std::vector<std::string>`
* Run the executable with an ensemble YAML input as the only parameter.
  - Individual ensemble command line parameters are specified per-ensemble in the YAML input.
  - E.g., `srun -n 16 -c 4 --gpus-per-task=1 ./executable ./ensembles_ensify.yaml`
* From here, the code should seamlessly execute as many ensembles as you specified in `ensembles.yaml` passed in as the only parameter to the executable.

### Enssify ensemble input format

The Ensify ensemble input is given as input to the executable. There is a list of ensemble entries. Each ensemble entry contains the beginning rank ID, the ending rank ID, a list of inputs (what you would've originally had after the executable name in `srun`), the file to contain stdout for this ensemble, and the file to contain stderr for this ensemble.

```YAML
# rank_beg , rank_end , arguments_list , stdout_filename , stderr_filename
ensembles: [
             [ 0   , 7   , ["./input/ensemble1.config"] , "ens1.out" , "ens1.err" ] , 
             [ 8   , 15  , ["./input/ensemble2.config"] , "ens2.out" , "ens2.err" ] ,
           ]
```

You can add as many entries as you want, but be sure that the inputs for each ensemble lead to different output filenames and / or directories so they do not clash with one another.

