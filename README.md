# Brain Tumor Segmentation Deep Architectures
Development of U-Net(s) capable of segmenting Brain Lower Grade Glioma MRI images.

![dataset-cover](https://user-images.githubusercontent.com/49094051/222287903-3bdd485e-1352-4c56-9bb6-737dc427b81d.png)


Data availability: https://www.kaggle.com/datasets/mateuszbuda/lgg-mri-segmentation <br> 
__LGG Segmentation Dataset__ <br>
Dataset used in:<br>
*Mateusz Buda, AshirbaniSaha, Maciej A. Mazurowski "Association of genomic subtypes of lower-grade gliomas with shape features automatically extracted by a deep learning algorithm." Computers in Biology and Medicine, 2019.*<br>
Licence: Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)


### Challenges & what I'm learning
Due to hardware limitations, I could not fit the whole Dataset in the GPU, as I usually would, with tensorflow.data.Dataset. I found out data generators to be much more light on RAM. Still the model is huge and I can only train batches of 16 elements. This will affect the performances, however, longer training could be a solution. It's also been interesting to understand implementative choices made by other users on Kaggle. For example, adding more layers on the deeper blocks was an interesting choice to investigate.<br>
Transformers are ground braking architectures. Playing with novel hybrid structures was fun and thought me a lot; in particular, investigating the balance between Convolutional and Tranformer blocks allowed me to understand better the strength of local feature extraction compared to the global attention approach offered by transformers. In particular, I could build practical knowledge of the situations that coul really benefit from transformers in segmentation. 

## Introduction
Lower-grade gliomas (LGG) are a group of WHO grade II and grade III brain tumors. As opposed
to grade I which are often curable by surgical resection, grade II and III are infiltrative and tend
to recur and evolve to higher-grade lesion. Predicting patient outcomes based on histopathological data for these tumors is inaccurate and suffers from inter-observer variability [1].

## Dataset
This dataset contains brain MR images together with manual FLAIR abnormality segmentation masks. The images are then 3 different scans combined - T1, Flair and T1 contrast [2].
The images were obtained from The Cancer Imaging Archive (TCIA).
They correspond to 110 patients included in The Cancer Genome Atlas (TCGA) lower-grade glioma collection with at least fluid-attenuated inversion recovery (FLAIR) sequence and genomic cluster data available.


*No real preprocessing nor post processing was applied; these steps should be instead implemented for better real-world applicability. However, this project only serves didactical purposes to understand U-Net and segmentation tasks better, so, real-wolrd effectiveness goes beyond my scope.*

## U-Net Models
The first model can be found in the notebook "Brain_Tumor_Segmentation_Model_1.ipynb" replicates the literature featuring this dataset [3](https://arxiv.org/pdf/1906.03720.pdf). It has modest dimensions (7,774,049 total parameters). It consist of a 5 "floors" U-Net with double Conv2D layer per each floor:
<p align="center">
 <img src = "https://user-images.githubusercontent.com/49094051/226310448-48aa09a2-ff94-4e7d-8910-d57c111071b2.PNG" />
</p>

The second model (Brain_Tumor_Segmentation_Model_2.ipynb) consists of a 4 layers ("floors") U-Net. The deeper floors feature a triple stack of Conv2D layers, this should allow more elaborated processing at a higher, semantic, level. It's not a small model, it is made of 10,655,809 trainable params, for this reason, a high number of epochs is implmented. Callbacks will be put in place in future to prevent overfitting, however, the phenomenon does not dramatically affect the performances even after 100 training epochs. Learning reached a plateau after ~35 epochs on the validation set.

### TransUNet
The architecture was inspired by J. Cheng et al. in their paper *TransUNet: Transformers Make Strong Encoders for Medical Image Segmentation* [4](https://arxiv.org/pdf/2102.04306.pdf)'. It consist of a 4 layers U-Net with some Transformer (encoder) blocks at the bottom which exert attention on the feature maps produced by the Convolutional Layers. The idea was first proposed in the original [Vision Transformer paper](https://arxiv.org/pdf/2010.11929.pdf), it basically involve combining spatial information of CNN with attention and global processing of transformers architectures. You can see how I implemented it in Brain_Tumor_Segmentation_TransUNET.ipynb . It is not really comparable in terms of number of parameters (10 times bigger than the bigger Unet model). The authors, in fact, do not train the net from scratch, they pre train it on imagenet. I might do the same  in fututre but right now I just want to build the net and see where does it bring me. 
<p align="center">
   <img src="https://user-images.githubusercontent.com/49094051/226309019-accf19c1-0c21-4818-84e2-8817b2f74dea.PNG" />
</p>

## Results and performances
### UNet model 1
The first model has comparable training metrics to the second, however, it seems to be less specific that the bigger model. Visual inspection of generated predictions highlights some sparse activations of the model when the labels show no glioma:

![e1734b3d-b951-11ed-acbc-f02f74239260](https://user-images.githubusercontent.com/49094051/222588665-7d071b33-e93e-4a41-872d-df27b7a1549f.png)
*First model underperforming with negative examples*

This can possibly be solved applying post processing techniques to reduce the false positive rate [3]. In particular, it is possible to make more coherent predictions if three dimensional predictions are assembled. Skull stripping might be also an effective technique to reduce false positives since many of the wrong activations happen at the boudary between gray matter and skull.

### UNet model 2
The second model shows decent results, however, it finds some difficulties when the shape of the glioma is irregular. Model history and more outputs can be found in this repository. 

![output](https://user-images.githubusercontent.com/49094051/222453086-47270671-ff42-47c1-9ac5-15009c48bfde.png)
*Second model (bigger one)*

### TransUnet
Considering the fact that the transformer blocks were training from scratch, I'm pretty happy of the results. The net is better at recognizing complex shapes even if some times it is not accurate with easier shapes. The conclusion I draw from this experiment is that including tranformers can bring some benefits but is a delicate matter: in this task in particular most of the information is local so the benefits of transformers are few while the cost in terms of training is considerable. In the original paper, transformers helped a lot because in that case multi organ segmentation was performed and, in that task, the anatomical/global information of the organs' position was extremely relevant.  
<p align="center">
 <img src = "https://user-images.githubusercontent.com/49094051/226317562-4a941f7f-e57e-468a-b50c-09dc16a10723.png"/>
 <img src = "https://user-images.githubusercontent.com/49094051/226317692-2fd440e7-a81c-43bc-8efc-5e20c2ef1936.png"/>
 <img src = "https://user-images.githubusercontent.com/49094051/226317777-93585b3f-bd5a-42bc-b7f8-d9da27be48fa.png"/>
</p>



## Further imporvements and limitations
 - [ ] Implement DICE loss for better performances
 - [ ] compare FLAIR data with original pre-contrast + FLAIR + post-contrast data
 - [ ] add callback measures to prevent overfitting
 - [x] commend the original study [3] and identify critical aspects of the model
 - [ ] Pre train on ImageNet

### Resources and Literature
[1] Cancer Genome Atlas Research Network. Comprehensive, integrative genomic analysis of
diffuse lower-grade gliomas. New England Journal of Medicine, 372(26):2481–2498, 2015.

[2] Kaggle Discussion topic https://www.kaggle.com/datasets/mateuszbuda/lgg-mri-segmentation/discussion/359225

[3] Mateusz Buda, AshirbaniSaha, Maciej A. Mazurowski "Association of genomic subtypes of lower-grade gliomas with shape features automatically extracted by a deep learning algorithm." Computers in Biology and Medicine, 2019. https://arxiv.org/abs/1906.03720

[4] "TransUNet: Transformers Make Strong Encoders for Medical Image Segmentation" Jieneng Chen, Yongyi Lu, Qihang Yu, Xiangde Luo,Ehsan Adeli, Yan Wang, Le Lu, Alan L. Yuille, and Yuyin Zhou, https://arxiv.org/pdf/2102.04306.pdf

[5] "AN IMAGE IS WORTH 16X16 WORDS: TRANSFORMERS FOR IMAGE RECOGNITION AT SCALE" https://arxiv.org/pdf/2010.11929.pdf

