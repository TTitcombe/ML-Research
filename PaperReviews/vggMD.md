# VERY DEEP CONVOLUTIONAL NETWORKS FOR LARGE-SCALE IMAGE RECOGNITION

* Achieved first and second in ImageNet challenge 2014 in localisation and classification tracks, respectively
* state-of-the-art results in several datasets (generalises)

### Intro
* best-performing submissions to ILSVRC-2013 used smaller receptive window size and smaller stride of the first convolutional layer
* this paper assesses effect of depth on performance
    * fixes other params
    * steadily increase network depth (feasible due to use of small, 3x3, filters) in all layers
    * [Two best performing models released](http://www.robots.ox.ac.uk/~vgg/research/very_deep/)

### Configurations
* Preprocessing: subract mean RGB value (computed on training set) from each pixel
* Image passed through stack of 3x3 filters
    * 1x1 used in one configuration. This can be thought of as linear transformation of the input channels (which is followed by non-linearity)
* Spatial pooling is carried out by 5 max-pooling layers
    * These follow some, not all, of convolution layers
    * Max-pooling is a 2x2 windows, with stride 2
* Stack of convolution layers followed by three Fully Connected (FC) layers
    * First 2 have 4096 channels
    * Final has 1000 channels (one for each class in dataset)
    * Softmax layer final activation
* All hidden layers have ReLU

#### Net A
* conv3-64 (3x3 convolution, 64 units)
* maxpool
* conv3-128
* maxpool
* conv3-256
* conv3-256
* maxpool
* conv3-512
* conv3-512
* maxpool
* conv3-512
* conv3-512
* maxpool
* FC layers
* softmax

** Note that successive nets are the same, but with an extra layer added before each maxpool, with Net B having an extra conv before the first maxpool, and Net E having extra conv before all maxpools **

** Net C is the same as Net D, but with a couple of the convolution layers preceeding a maxpool switched to a 1x1 convolution **

** 133 Millions parameters in Net A **

Number of parameters are smaller than shallower nets with greater convolution sizes

### Discussion
* Stack of three 3x3 convolutions have an effective receptive field of 7x7
* Benefits of three 3x3 over a single 7x7:
    * Three non-linear rectification layers, so more discriminative
    * Fewer parameters - 7x7 requires 81% more
* 1x1 convolutions increases the non-linearity of decision function without affecting receptive field

### Training
* Batch size: 256
* Momentum: 0.9
* L2 penalty: 5 x 10e(-4)
* Dropout for first two FC layers: 0.5
* Learning rate: 10e(-2) initially, decreased by factor of 10 when validation accuracy stopped improving
    * LR dropped 3 times in total
* 370k iterations (74 epochs)
    * Fewer epochs to converge due to implicit regularisation imposed by greater depth ?
    * Pre-initialisation of certain layers

* Trained a small net (random initialisation)
* For larger nets, initialised first and last layers with weights of small net
* Intermediate layers initialised randomly
* Random inits were normal, mean 0, variance 10e(-2)
* Biases initialised to 0
