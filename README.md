# Session 6 Assignment

## Part 1

### Important Steps

![](excel_screenshot.png)
- The Excel file and the supporting screenshot describes the forward and backpropagation passes of a simple neural network with an input, hidden and output layer, each made up of 2 neurons.
- A single epoch consists of a forward pass and backpropagation

#### Forward Pass
1. Input layer neurons (i_1, i_2) store the input information
2. Input neurons are multipled with corresponding weights (w_1, w_2, w_3, w_4) and linearly combined to get values of hidden layer neurons (h_1, h_2)
3. Hidden layer neurons are passed through a non-linear sigmoid function to obtain hidden layer activations (a_h1, a_h2)
4. Hidden layer activations are multiplied with corresponding weights ($w_5, w_6, w_7, w_8)$ and linearly combined to get values of output layer neurons (o_1, o_2)
5. Output layer neurons are passed through a non-linear sigmoid function to obtain output layer activations (a_o1, a_o2)
6. Output layer activations are compared with target values (t_1, t_2) via a Mean Squared Error (MSE) loss function to determine the loss (E_1, E_2), both of which are combined to give total loss of the epoch (E_tot)

#### Backpropagation
1.  Partial derivatives of E_tot is calculated wrt all parameters of the model
2.  Chain rule is leveraged to calculate partial derivatives of E_tot wrt weights far away from it in the DAG (e.g. w1)
3.  Sigmoid is differtiable for all values of x. Its derivative is used wherever activation is involved

Low learning rates like 0.1 make our model underpowered for the data we have. In contrast, high learning rates make the network learn faster. This should be obviously confirmed with a separate test set. Training loss reaching low values in fewer epochs with high learning rates like 2.0 do not matter if test set loss is high. A visual comparison can be found in 'loss_for_different_learning rates.png'

## Part 2

The solution notebook is present in the S6 folder. Final validation accuracy is 99.43% with a neural network that satisfies the assignment constraint of under 20K parameters. More details can be found in the model summary below:

```
----------------------------------------------------------------
        Layer (type)               Output Shape         Param #
================================================================
            Conv2d-1            [-1, 8, 28, 28]              72
              ReLU-2            [-1, 8, 28, 28]               0
       BatchNorm2d-3            [-1, 8, 28, 28]              16
           Dropout-4            [-1, 8, 28, 28]               0
            Conv2d-5            [-1, 8, 28, 28]             576
              ReLU-6            [-1, 8, 28, 28]               0
       BatchNorm2d-7            [-1, 8, 28, 28]              16
           Dropout-8            [-1, 8, 28, 28]               0
         MaxPool2d-9            [-1, 8, 14, 14]               0
           Conv2d-10            [-1, 8, 14, 14]              64
           Conv2d-11           [-1, 14, 14, 14]           1,008
             ReLU-12           [-1, 14, 14, 14]               0
      BatchNorm2d-13           [-1, 14, 14, 14]              28
          Dropout-14           [-1, 14, 14, 14]               0
           Conv2d-15           [-1, 14, 14, 14]           1,764
             ReLU-16           [-1, 14, 14, 14]               0
      BatchNorm2d-17           [-1, 14, 14, 14]              28
          Dropout-18           [-1, 14, 14, 14]               0
        MaxPool2d-19             [-1, 14, 7, 7]               0
           Conv2d-20             [-1, 14, 7, 7]             196
           Conv2d-21             [-1, 24, 7, 7]           3,024
             ReLU-22             [-1, 24, 7, 7]               0
      BatchNorm2d-23             [-1, 24, 7, 7]              48
          Dropout-24             [-1, 24, 7, 7]               0
           Conv2d-25             [-1, 24, 7, 7]           5,184
             ReLU-26             [-1, 24, 7, 7]               0
      BatchNorm2d-27             [-1, 24, 7, 7]              48
          Dropout-28             [-1, 24, 7, 7]               0
        MaxPool2d-29             [-1, 24, 3, 3]               0
           Conv2d-30             [-1, 24, 3, 3]             576
           Conv2d-31             [-1, 32, 3, 3]           6,912
             ReLU-32             [-1, 32, 3, 3]               0
      BatchNorm2d-33             [-1, 32, 3, 3]              64
          Dropout-34             [-1, 32, 3, 3]               0
           Conv2d-35             [-1, 10, 3, 3]             320
        AvgPool2d-36             [-1, 10, 1, 1]               0
================================================================
Total params: 19,944
Trainable params: 19,944
Non-trainable params: 0
----------------------------------------------------------------
Input size (MB): 0.00
Forward/backward pass size (MB): 0.67
Params size (MB): 0.08
Estimated Total Size (MB): 0.75
----------------------------------------------------------------
```
Used the following intuitions to build the network:
- Upon visually inspecting a few images, 5x5 pixels were enough for my eye to detect edges etc. As such, used 2 convolution layers in the first block followed by transition blocks. Similar assessment for substructures of numbers (for e.g. the arc of a 2 etc)
- Batch Norm and Dropout (with low percentage as the network has very few params) added after every feature extracting layer, save for the last output layer
- Used a pyramidal architecture scheme instead of squeeze and expand
  - Note: I used 14 as the channel size in the second block (instead of the more optimized 16) in order to make model parameters under 20K
- Leveraged transition blocks made up of Max Pooling and 1x1 convolution layers
- Used a Learning Rate scheduler with an initial lr of 0.1 to drop by 10% every 10 epochs. Without the scheduler, I noticed a minima was missed (i.e., test set accuracy went up till 98% after 9-10 epochs and started dropping and then rose again but then, 20 epochs were up) 
- **Clarification:** I answered "True" to whether I used a Fully Connected Layer. Reason: 1x1 convolution layer used at the end is a superset of the Fully Connected Layer and is functionally very similar to a Fully Connected Layer
