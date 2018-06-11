# Hierarchical Recurrent Neural Network for Skeleton Based Action Recognition


* This introduces a model, comprising of a set of LSTMs, in series and parallel, which take as input 2D coordinates of body parts, and output an action

### Base Model
* First layer has 5 LSTMs
    * Take as input coordinates relating to left arm, right arm, trunk, left leg, right leg, respectively
* Second layer has 4 LSTMs
    * Output from one of (left arm, right arm, left leg, right leg) LSTMs concatenated with output from trunk LSTM, used as the four inputs for layer 2
* Third layer has 2 LSTMs
    * Output from layer 2 concatenated as before, so third layer input is one of (upper body, lower body)
* Fourth layer has 1 LSTM
    * "Fusion layer" - i.e. information from all limbs
* Fifth layer is fully connected (FC)
* Softmax layer for classification output

### Comparitive Architectures
The authors train and test six similar models, to see what contributes most to classification accuracy

1. Hierarchicallly bidirectional RNN (**HBRNN-L**), which is the model outlined above, but with bidirectional LSTMs instead of unidirectional
2. **HURNN-L**, same as (1.), but with unidirectional LSTMs
3. Deep bidirectional rnn (**DBRNN-L**) - several RNNs with the whole human skeleton as input
4. **DURNN-L**, same as (3.) but with unidirectional LSTMs
5. **DBRNN-T**, same as (3.) but with tanh activation in all layers
6. **DURNN-T**, same as (4.) but with tanh activation in all layers

* (1.) and (2.) assess if bidirectionality is a benefit
* (3.) and (4.) assess if the hierarchical architecture is beneficial
* (5.) and (6.) assess if tanh activations are beneficial

### Pre-processing
* Centre point defined as equal to (hip_centre + hip_left + hip_right)/3
* Savitzky-Golay smoothing filter applied to improve signal to noise ratio of the raw data
* Frames from videos sampled in fixed intervals to reduce computational cost (every 16 in Berkeley MHAD dataset, and every 4 for HDM05 dataset)

### Results
* Achieve state of the art results (average accuracy) in MSR Action3D Dataset with the **HBRNN-L** model
    * **HBRNN-L**: 94.49%
    * **HURNN-L**: 93.57%
    * **DBRNN-L**: 92.61%
    * **DURNN-L**: 89.86%
    * **DBRNN-T**: 83.52%
    * **DURNN-T**: 77.11%

### Discussion
* Models with **L** suffix are easy to overfit
    * Adding input noise, weight noise, and early stopping used to avoid overfitting
    * Found that adding weight noise is more effective
    * Dropout does not work in this model
* Trained for 50 seconds per epoch, and required only 30 epochs to achieve > 98% validation accuracy
* 52.46 ms for inference (C++ implementation on a CPU 3.2GHz system)

* Unidirectional models far more computationally efficient than bidirectional, so in online use-cases a uni model should be used
