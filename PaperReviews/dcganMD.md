# Unsupervised Representation Learning with Deep Convolutional Generative Adversarial Networks

* Introduce Deep Convolutional Generative Adversarial Networks (DCGANs)
* After training, can use parts of the generator and descriminator networks as feature extractors for supervised tasks

### Model Architecture
Key to scaling GANs using CNNs, resulting in stable training across a range of datasets, are three advancements:
* All convolutional net (replace maxpooling with strided convolutions).
    * This allows the net to sample its own spatial downsampling
    * Used in both G and D nets
* Eliminating fully connected layers
    * They found global average pooling helped model stabilty but hurt convergence speed
    * Noise vector, Z, is reshaped into a 4D tensor and is convolved
    * For D, last convolution layer output is flattened and fed into a sigmoid output
* Batch Normalisation
    * Stabilises learning by normalising the input to each unit to have zero mean and unit variance
    * This deals with training problems which arise due to poor initialisation, and helps gradient to flow in deeper networks
    * **Critical** to getting deep generators to begin learning, and preventing mode collapse
    * Directly applying batchnorm to all layers resulted in sample oscillation and model instability

  * ReLU used in G, except for output layer (uses Tanh)
      * Observed that using a bounded activation allowed the model to learn more quickly rto saturate and cover the color space of the training distribution
  * Leaky ReLU used in D

### Training
* Trained on Large-scale scene understanding (LSUN), ImageNet-1k, and a newly assembled Faces dataset.
* No preprocessing applied besides scaling to the range of tanh function ([-1,1])
* minibatch size: 128
* Weights initialised from mean 0, std 0.02 normal distribution
* Slope of leak in leaky RelU was 0.2
* Adam optim used
    * 0.0002 learning rate
    * 0.5 beta 1

### Generator
* Sample 100 dimension Z, and reshape into 4x4x1024
* Conv 1: into 8x8x512
* Conv 2: into 16x16x256
* Conv 3: into 32x32x128
* Conv 4: into 64x64x3 (rgb)
* Stride 2 in all convolutions
