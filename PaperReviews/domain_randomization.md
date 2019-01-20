# Training Deep Networks with Synthetic Data: Bridging the Reality Gap by Domain Randomization
by Tremblay et al. (NVIDIA)

* This paper trains object detection models on computer modelled scenes containing cars, evaluated on real data
* Extends _domain randomization_ to a non-trivial task. This augments the generated data to achieve performance comparable to models trained on real (expensive) data
* Provides ablation study to determine which aspects of domain randomization are the most valuable

### Synthetic data
This paper is concerned with detecting cars in images. The authors use an internally created Unreal Engine (UE4) plugin to generate 1200 x 400 images at 30Hz.
The synthetic data are randomly generated scenes containing at least one 3D model of a car. The authors randomize several properties of the images to generate noisy data capable of training a model robust enough for real-world use.
The non-constant properties are:
    - Number of cars present in the scene
    - Type of cars. Models are selected from a set of 36 generic sedan and hatchbacks
    - Introduction of distractor objects in the scene, with varying numbers, types, colours, and model scales.
    - Texture on the target objects and background photograph
    - Location of the virtual camera with respect to the scene
    - Angle of the camera with respect to the scene
    - Number of locations of point lights (from 1 to 12), in addition to a planar light for ambient illumination
    - Visibility of the ground plane

The authors note that all of these properties are trivial to implement.
The generated scenes are cartoonish and bizarre, not representing a typical road scene one would reasonable expect to see. The aim of this work is to force the network to ignore unimportant details in the scene and to focus on extracting features from the target object instead.

### Models
*Faster R-CNN*. This model uses a reason proposal network (RPN) to generate candidate regions of interest. In a second stage, features are cropped from the image using the proposed regions and fed into a feature extractor. The authors train this model with a momentum value of 0.9 and a learning rate of 0.0003. inception Resnet V2 pretrained on ImageNet was used as the feature extractor.
*R-FCN*. Comparable to Faster R-CNN, but with quicker inference speeds. The authors train this model with RMSProp with an initial LR of 0.0001, a decay step of 10000, a decay factor of 0.95, momentum and decay values of 0.9, and epsilon of 1.0.
*SSD* uses a single feed-forward CNN to directly predict object classes. The authors use the same training strategy as Faster R-CNN, but uses a Resnet101 pretrained on ImageNet as the feature extractor.

In this work 100k synthetic images are generated, containing no more than 14 cars each. In addition to the previously described DR techniques, each image is given a random brightness, contrast, and Gaussian noise. Furthermore, "classical" augmentation techniques such as random flips, box jitter, and random crop are used.

The authors compare DR to other techniques by also training the models on the 2.5K images of the Virtual KITTI (VKITTI) dataset, which are a set of very realistic computer modelled road scenes.

All models are tested on the KITTI (real data) dataset.

### Results
The overall top performing model is Faster R-CNN trained on VKITTI, with Faster R-CNN DR coming in a close second. For the other models, the DR trained version outperformed the VKITTI model.

The authors show that by fine-tuning on real data after training, the DR model can outperform VKITTI. The hypothesis for this result is that the advantage VKITTI provides in appearing closer to the test data becomes negligible as real data is introduced for all models.

*Ablation study*. By training models with one of the augmentation technique systematically switched off, the authors find that randomized brightness, contrast, and gaussian noise, in addition to the "classical" augmentation techniques, contribute the least to the success of a DR trained model. Removing the distractor objects contribute the most, as they force the network to ignore nearby patterns and to deal with partially occluded target objects.

### Conclusions
This paper has shown that domain randomization of synthetic data can be used to train a model to achieve state-of-the-art scores in complex tasks. Randomly generated data is orders of magnitude quicker and easier (and therefore cheaper) to generate than both artistically created virtual data and real-world data.
The scope of the application of this work is fairly narrow, so it will be interesting to see how well DR data techniques scale when training on increasingly complex tasks, such as detection of tens of object types.

When faced with a data-shortage problem, this paper has shown that generating fake data can be a powerful method for boosting results.
