# Brain-Tumor-Segmentation-U-Net
Development of U-Net(s) capable of segmenting Brain Lower Grade Glioma MRI images.

![dataset-cover](https://user-images.githubusercontent.com/49094051/222287903-3bdd485e-1352-4c56-9bb6-737dc427b81d.png)


Data availability: https://www.kaggle.com/datasets/mateuszbuda/lgg-mri-segmentation <br> 
__LGG Segmentation Dataset__ <br>
Dataset used in:<br>
*Mateusz Buda, AshirbaniSaha, Maciej A. Mazurowski "Association of genomic subtypes of lower-grade gliomas with shape features automatically extracted by a deep learning algorithm." Computers in Biology and Medicine, 2019.*<br>
Licence: Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)


### Callenges & what I'm learning
Due to hardware limitations, I could not fit the whole Dataset in the GPU, as I usually would, with tensorflow.data.Dataset. I found out data generators to be much more light on RAM. Still the model is huge and I can only train batches of 16 elements. This will affect the performances, however, longer training could be a solution. It's also been interesting to understand implementative choices made by other users on Kaggle. For example, adding more layers on the deeper blocks was an interesting choice to investigate.

## Introduction
Lower-grade gliomas (LGG) are a group of WHO grade II and grade III brain tumors. As opposed
to grade I which are often curable by surgical resection, grade II and III are infiltrative and tend
to recur and evolve to higher-grade lesion. Predicting patient outcomes based on histopathological data for these tumors is inaccurate and suffers from inter-observer variability [1].

## Dataset
This dataset contains brain MR images together with manual FLAIR abnormality segmentation masks. The images are then 3 different scans combined - T1, Flair and T1 contrast [2].
The images were obtained from The Cancer Imaging Archive (TCIA).
They correspond to 110 patients included in The Cancer Genome Atlas (TCGA) lower-grade glioma collection with at least fluid-attenuated inversion recovery (FLAIR) sequence and genomic cluster data available.


*No real preprocessing nor post processing was applied; these steps should be instead implemented for better real-world applicability. However, this project only serves didactical purposes to understand U-Net and segmentation tasks better, so, real-wolrd effectiveness goes beyond my scope.*

## U-Net Model
The first model can be found in the notebook "Brain_Tumor_Segmentation_Model_1" replicates the literature featuring this dataset [3]. It has modest dimensions (7,774,049 total parameters). It consist of a 5 "floors" U-Net with double Conv2D layer per each floor

The second model consist of a 4 layers ("floors") U-Net. The deeper floors feature a triple stack of Conv2D layers, this should allow more elaborated processing at a higher, semantic, level. It's not a small model, it is made of 10,655,809 trainable params, for this reason, a high number of epochs is implmented. Callbacks will be put in place in future to prevent overfitting, however, the phenomenon does not dramatically affect the performances even after 100 training epochs. Learning reached a plateau after ~35 epochs on the validation set.

## Results and performances
### Smaller model
The first model instead, has comparable training metrics, however, it seems to be less specific that the bigger model. Visual inspection of generated prediction highlights some sparse activations of the model when the label show no glioma:

![e1734b3d-b951-11ed-acbc-f02f74239260](https://user-images.githubusercontent.com/49094051/222588665-7d071b33-e93e-4a41-872d-df27b7a1549f.png)
*First model underperforming with negative examples*

This can possibly be solved applying post processing techniques to reduce the false positive rate [3], in particular, it is possible to make more coherent predictions if a three dimensional prediction is assembled. Skull stripping might be also an effective technique to reduce false positives since many of the wrong activation happen at the boudary between gray matter and skull.

### Bigger model
The second model shows decent results, however, it finds some difficulties when the shape of the glioma is irregular. Model history and more outputs can be found in this repository.

![output](https://user-images.githubusercontent.com/49094051/222453086-47270671-ff42-47c1-9ac5-15009c48bfde.png)
*Second model (bigger one)*

## Further imporvements and limitations
 - [ ] Implement DICE loss for better performances
 - [ ] compare FLAIR data with original pre-contrast + FLAIR + post-contrast data
 - [ ] add callback measures to prevent overfitting
 - [x] commend the original study [3] and identify critical aspects of the model

[1] Cancer Genome Atlas Research Network. Comprehensive, integrative genomic analysis of
diffuse lower-grade gliomas. New England Journal of Medicine, 372(26):2481–2498, 2015.

[2] Kaggle Discussion topic https://www.kaggle.com/datasets/mateuszbuda/lgg-mri-segmentation/discussion/359225

[3] Mateusz Buda, AshirbaniSaha, Maciej A. Mazurowski "Association of genomic subtypes of lower-grade gliomas with shape features automatically extracted by a deep learning algorithm." Computers in Biology and Medicine, 2019. https://arxiv.org/abs/1906.03720
