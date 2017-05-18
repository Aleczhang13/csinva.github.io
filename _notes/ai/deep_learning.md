---
layout: notes
section-type: notes
title: Deep Learning
category: ai
---

* TOC
{:toc}

# neural networks
- basic perceptron update rule
    - if output is 0 but should be 1: raise weights on active connections by d
    - if output is 1 but should be 0: lower weights on active connections by d
- transfer / activation functions
    - sigmoid(z) = $\frac{1}{1+e^{-z}}$
    - Binary step
    - TanH
    - Rectifier = ReLU
- "deep" - more than 1 hidden layer
- regression loss = $\frac{1}{2}(y-\hat{y})^2$
- classification loss = $-y log (\hat{y}) - (1-y) log(1-\hat{y})$ 
    - can't use SSE because not convex here
- multiclass classification loss $=-\sum_j y_j ln \hat{y}_j$
- **backpropagation** - application of *reverse mode automatic differentiation* to neural networks's loss
	- apply the chain rule from the end of the program back towards the beginning
		- $\frac{dx_N}{dx_i} = \frac{dx_N}{dx_{i+1}} (\frac{\partial x_{i+1}}{\partial x_i})$
	- N is output
	- if dependency graph is more complex, need to apply multi-dimensional chain rule and sum
	- $\frac{dx_N}{dx_i} = \sum_{j:i\in \pi (j)} \frac{dx_N}{dx_{j}} (\frac{\partial x_{j}}{\partial x_i})$
	- pipeline
	    - initialize weights ($\frac{dx_N}{dx_N}=1$)
	    - for each batch
		- run network forward to compute outputs, loss
		- run compute gradients with backprop
		- update weights with SGD

# training
- *vanishing gradients problem* - neurons in earlier layers learn more slowly than in later layers
- *exploding gradients problem* - gradients are significantly larger in earlier layers than later layers
- *batch normalization* - whiten inputs to all neurons (zero mean, variance of 1)
	- do this for each input to the next layer
- *dropout* - randomly zero outputs of p fraction of the neurons during training
	- like learning large ensemble of models that share weights
	- at test time can multiply all neurons' outputs by p
	- or during training divide all neurons' outputs by p
- *softmax* - takes vector z and returns vector of the same length
	- makes it so output sums to 1 (like probabilities of classes)
	
# CNNs
- kernel here means filter
- convolution G- takes a windowed average of an image F with a filter H where the filter is flipped horizontally and vertically before being applied
- G = H $\ast$ F
    - if we do a filter with just a 1 in the middle, we get the exact same image
    - you can basically always pad with zeros as long as you keep 1 in middle
    - can use these to detect edges with small convolutions
    - can do Guassian filters
- landmark AlexNet on ImageNet competition
- convolutions typically sum over all color channels
- weight matrices have special structure (Toeplitz or block Toeplitz)
- input layer is usually centered (subtract mean over training set)
- usually crop to fixed size (square input)
- receptive field - input region
- stride m - compute only every mth pixel
- downsampling
    - max pooling - backprop error back to neuron w/ max value
    - average pooling - backprop splits error equally among input neurons
- data augmentation - random rotations, flips, shifts, recolorings
- residual networks - connect the input to end skipping layers
    - make it easy to learn the identity function
    - want to preserve info through layers
    - help with vanishing/exploding gradients
    
# RNNs
- feedforward NNs have no memory so we introduce recurrent NNs
- able to have memory
- could theoretically unfold the network and train with backprop
- truncated - limit number of times you unfold
- $state_{new} = f(state_{old},input_t)$
- ex. $h_t = tanh(W h_{t-1}+W_2 x_t)$
- train with backpropagation through time (unfold through time)
    - truncated backprop through time - only run every k time steps
- error gradients vanish exponentially quickly with time lag

## LSTMs
- have gates for forgetting, input, output
- easy to let hidden state flow through time, unchanged
- gate $\sigma$ - pointwise multiplication
    - multiply by 0 - let nothing through
    - multiply by 1 - let everything through
- forget gate - conditionally discard previously remembered info
- input gate - conditionally remember new info
- output gate - conditionally output a relevant part of memory
- GRUs - similar, merge input / forget units into a single update unit