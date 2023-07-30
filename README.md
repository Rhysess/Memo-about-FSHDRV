# Memo-about-FSHDRV
This is the memo of the paper about few-shot HDR video reconstruction. We will continue to update and improve the relevant explanations of this paper. We also welcome you to inquire about specific details of interest through GitHub issues.



## Networks

Main network in the paper are the combination of original flow network and proposed merging network. 

### Flow Network
The following figure shows the concrete structure of flow network which is one modified version of Spynet[1]. We keep totally same structure and setting as Kalantari's work[2] and Chen's work[3]. For detailed code implementation, you can refer to the following website: https://github.com/guanyingc/DeepHDRVideo/blob/master/models/archs/flow_networks.py.

<img src="https://github.com/Rhysess/Memo-about-FSHDRV/assets/90193309/cbdbc165-75d2-4184-9f4e-cc7ebc5e5758" width = "500">

### Proposed Merging Network
The following figure is the overall structure of proposed merging networks. It can be regarded as a combination of four parts: hybrid dilated convolution based feature extraction module, attention module, encoder and decoder. The input 5 frames include 3 original frames (and their HDR domains) and 2 aligned frames(also HDR domains). Thus channels of each frames is 6 (3+3).

<img src="https://github.com/Rhysess/Memo-about-FSHDRV/assets/90193309/bbbdbe2f-a9ed-4fba-b12e-fe22d8a758e1" width = "500">

For each hybrid dilated convolution module shown in the following figure, the concrete setting of dilated layer is:
1. input channel = 6, output channel = 32, k=3, stride=2, pad=1, dilation=1
2. input channel = 6, output channel = 32, k=3, stride=2, pad=1, dilation=2
3. input channel = 6, output channel = 32, k=3, stride=2, pad=1, dilation=3
 
while the setting of fusion convolution layer is:
1. input channel = 96, output channel = 32, k=3, stride=1, pad=1, dilation=1

<img src="https://github.com/Rhysess/Memo-about-FSHDRV/assets/90193309/97e76a2d-92b9-45a8-bd70-a5f9259e79b2" width = "500">

For each attention module shown in the following figure, the concrete setting is:
1. input channel = 64, output channel = 64, kernel_size=3, padding=1,
2. input channel = 64, output channel = 32, kernel_size=3, padding=1,

<img src="https://github.com/Rhysess/Memo-about-FSHDRV/assets/90193309/73f2812b-35a0-4bd4-9c4a-4b2f37426436" width = "500">

For encoder-decoder part, you can refer to: https://github.com/guanyingc/DeepHDRVideo/blob/master/models/archs/weight_EG19net.py. The main reference is that we remove the second layer in Downsampling part and modify the input channels consistency with previous output (160 dimensions).



## Settings

For loss calculation, as mentioned in the paper, initial values of β, α, θ and λ are set to 0, 0.01, 1 and 5×10−6. β will increase to 0.05 along the Gaussian curve (our function: 0.05*math.e**((-0.01*(30-epoch)*(30-epoch))), but more choices are optional) and θ will increase 0.1 every 10 epochs. Other implementation details you can check the paper. 


<img src="https://github.com/Rhysess/Memo-about-FSHDRV/assets/90193309/b5f7b0f3-a95f-46e3-893e-3c35fc4527de" width = "160">,

<img src="https://github.com/Rhysess/Memo-about-FSHDRV/assets/90193309/2050dc61-db35-446a-bf90-cb1256b25f64" width = "180">,

<img src="https://github.com/Rhysess/Memo-about-FSHDRV/assets/90193309/b5b3c1a9-333e-4042-881d-b8b811b7930b" width = "220">


## Data

As mentioned in the paper, all training samples (labeled and unlabeled) and testing sequences could be achieved through following urls:

1. https://www.hdm-stuttgart.de/vmlab/hdm-hdr-2014/
2. http://www.hdrv.org/Resources.php
3. https://github.com/guanyingc/DeepHDRVideo-Dataset/
4. https://web.ece.ucsb.edu/~psen/PaperPages/HDRVideo/

Related indexes 5-shot labeled samples can also be checked in the folder. More information will be released in the future.




## References
[1] Ranjan, Anurag, and Michael J. Black. "Optical flow estimation using a spatial pyramid network." Proceedings of the IEEE conference on computer vision and pattern recognition. 2017.

[2] Kalantari, Nima Khademi, and Ravi Ramamoorthi. "Deep hdr video from sequences with alternating exposures." Computer graphics forum. Vol. 38. No. 2. 2019.

[3] Chen, Guanying, et al. "Hdr video reconstruction: A coarse-to-fine network and a real-world benchmark dataset." Proceedings of the IEEE/CVF International Conference on Computer Vision. 2021.
