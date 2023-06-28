# DATA620004_Final_Project
- BYOL based on Resnet18 at https://github.com/JDmunkJoey/BYOL_Resnet18
- Resnet18 at https://github.com/JDmunkJoey/Resnet18_Cifar10
- 
# BYOL based on Resnet18 - Implementation
PyTorch implementation of "Bootstrap Your Own Latent: A New Approach to Self-Supervised Learning" by J.B. Grill et al.

[Link to paper](https://arxiv.org/abs/2006.07733)

This repository includes a practical implementation of BYOL with:
- Benchmarks on vision datasets (**CIFAR-10** for train & val)
- CIFAR-10 could be downloaed at https://drive.google.com/file/d/1oZiZIZWROOynincRSt7c-MRXF_poVJe1/view?usp=sharing
- Support for PyTorch **<= 1.5.0**
- Optimizer: Adam
- Pre-training epochs: 50
- Batch size: 192
- Learning rate: 3e-4
- Checkpoint epochs: 5
- Experimental environment：
Miniconda: conda3
Python: 3.8(ubuntu18.04)
Cuda: 11.1
GPU: RTX 3090(24GB) * 1
CPU: 15 vCPU Intel(R) Xeon(R) Platinum 8336C CPU @ 2.30GHz
RAM: 80GB


This repository includes a practical implementation of BYOL with:
- Benchmarks on vision datasets (**CIFAR-10** for train & val)
- CIFAR-10 could be downloaed at https://drive.google.com/file/d/1oZiZIZWROOynincRSt7c-MRXF_poVJe1/view?usp=sharing
- Support for PyTorch **<= 1.5.0**
- Optimizer: Adam
- Pre-training epochs: 50
- Batch size: 192
- Learning rate: 3e-4
- Checkpoint epochs: 5
- Experimental environment：
Miniconda: conda3
Python: 3.8(ubuntu18.04)
Cuda: 11.1
GPU: RTX 3090(24GB) * 1
CPU: 15 vCPU Intel(R) Xeon(R) Platinum 8336C CPU @ 2.30GHz
RAM: 80GB

## Usage
### Pre-training
To run pre-training using BYOL with the default arguments (1 node, 1 GPU), use:
```
python3 main.py
```

Which is equivalent to:
```
python3 main.py --nodes 1 --gpus 1
```
The pre-trained models are saved every *n* epochs in \*.pt files, the final model being `model-final.pt`

My pre-trained model 'yiqiao-byol-model-final.pt' could be downloaded at https://drive.google.com/file/d/11Sq2ynFkBFeh7H7E_B8G0FWQ-TUqRIQr/view?usp=sharing

### Finetuning & Linear classification protocol
Finetuning a model ('linear evaluation') on top of the pre-trained, frozen ResNet model can be done using:
```
python3 logistic_regression.py --model_path=./model-final.pt
```

With `model_final.pt` being file containing the pre-trained network from the pre-training stage.

## Arguments
```
--image_size, default=224, "Image size"
--learning_rate, default=3e-4, "Initial learning rate."
--batch_size, default=42, "Batch size for training."
--num_epochs, default=100, "Number of epochs to train for."
--checkpoint_epochs, default=10, "Number of epochs between checkpoints/summaries."
--dataset_dir, default="./datasets", "Directory where dataset is stored.",
--num_workers, default=8, "Number of data loading workers (caution with nodes!)"
--nodes, default=1, "Number of nodes"
--gpus, default=1, "number of gpus per node"
--nr, default=0, "ranking within the nodes"
```

## Results
### Training 
#### loss
![](BYOL-master/../BYOL_Resnet18/accuracy%20of%20train.png) 
#### Accuracy
![](BYOL-master/../BYOL_Resnet18/accuracy%20of%20train.png)
### Linear classification protocol (evaluation) 
#### loss
![](BYOL-master/../BYOL_Resnet18/loss%20of%20test.png)
#### Accuracy
![](BYOL-master/../BYOL_Resnet18/accuracy%20of%20test.png)
#### final testing performance
The performance (accuracy) of testing the fine-tuned model after throwing away fc layer is 79.96%.

# Resnet18  - Implementation
PyTorch implementation of Resnet18 without pre-training model.
This repository includes a practical implementation of Resnet18 with:
- Benchmarks on vision datasets (**CIFAR-10** for train & val)
- CIFAR-10 could be downloaed at https://drive.google.com/file/d/1oZiZIZWROOynincRSt7c-MRXF_poVJe1/view?usp=sharing
- Support for PyTorch **<= 1.5.0**
- Optimizer: Adam
- Pre-training epochs: 50
- Batch size: 128
- Learning rate: 0.1
- Experimental environment：
Miniconda: conda3
Python: 3.8(ubuntu18.04)
Cuda: 11.1
GPU: RTX 4090(24GB) * 1
CPU: 15 vCPU Intel(R) Xeon(R) Platinum 8375C CPU @ 2.90GHz
RAM: 80GB

## Augmentation
### Random flip
```python
transforms.RandomHorizontalFlip()
```

### Random crop after filling
We just fill an image of size '32x32' to '40x40' and then crop it randomly to '32x32'.
```python
transforms.RandomCrop(32, padding=4)
```

### Cutout operation
The Cutout operation will randomly block blocks of several sizes of the picture, and the sizes and blocks can be set according to your needs.
```python
Cutout(n_holes=1, length=16)
```

## Modification
Considering that the picture size of the 'CIFAR10' dataset is too small, the '7x7' downsampling convolution and pooling operation of the 'ResNet18' network are prone to lose some information, so in the experiment, we removed the '7x7' downsampling layer and the maximum pooling layer, and replaced it with a '3x3' downsampling convolution. At the same time, the step size and fill size of the convolutional layer are reduced, so that the information of the original image can be preserved as much as possible.

## Strategy
In the training of the model, the strategy we adopt is: set the initial learning rate to 0.1, every time the loss of the verification set after 10 epoch training does not decrease, the learning rate becomes the original 0.5, and a total of 50 epochs are trained. In training, our batch_size is 128 and the optimizer is' SGD ':
```python
optimizer = optim.SGD(model.parameters(), lr=lr, momentum=0.9, weight_decay=5e-4)
```

## Usage
### Train
To run pre-training using BYOL with the default arguments (1 node, 1 GPU), use:
```
python train.py
```
My pre-trained model 'yiqiao-resnet18-model-final.pt' could be downloaded at https://drive.google.com/file/d/11t7SGAYaujr4HszhxtVhMxIRo8u2nZtG/view?usp=sharing

### Test
```
python test.py
```

## Results
### Train
#### loss
![](ResNet18-Cifar10/../Resnet18_Cifar10/loss%20of%20train.png)
#### Accuracy
![](ResNet18-Cifar10/../Resnet18_Cifar10/accuracy%20of%20train.png)
### Test
#### loss
![](ResNet18-Cifar10/../Resnet18_Cifar10/loss%20of%20test.png)
#### Accuracy
![](ResNet18-Cifar10/../Resnet18_Cifar10/accuracy%20of%20test.png)
#### Final testing performance
The performance (accuracy) of testing the fine-tuned model after throwing away fc layer is 88.94%.
