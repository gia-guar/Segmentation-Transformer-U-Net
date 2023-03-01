# Brain-Tumor-Segmentation-U-Net
Development of an U-Net capable of segmenting Brain Lower Grade Glioma MRI images.

![dataset-cover](https://user-images.githubusercontent.com/49094051/222287903-3bdd485e-1352-4c56-9bb6-737dc427b81d.png)


Data availability: https://www.kaggle.com/datasets/mateuszbuda/lgg-mri-segmentation <br>
__LGG Segmentation Dataset__ <br>
Dataset used in:<br>
*Mateusz Buda, AshirbaniSaha, Maciej A. Mazurowski "Association of genomic subtypes of lower-grade gliomas with shape features automatically extracted by a deep learning algorithm." Computers in Biology and Medicine, 2019.*

## Introduction
Lower-grade gliomas (LGG) are a group of WHO grade II and grade III brain tumors. As opposed
to grade I which are often curable by surgical resection, grade II and III are infiltrative and tend
to recur and evolve to higher-grade lesion. Predicting patient outcomes based on histopathological data for these tumors is inaccurate and suffers from inter-observer variability [1].
This dataset contains brain MR images together with manual FLAIR abnormality segmentation masks.
The images were obtained from The Cancer Imaging Archive (TCIA).
They correspond to 110 patients included in The Cancer Genome Atlas (TCGA) lower-grade glioma collection with at least fluid-attenuated inversion recovery (FLAIR) sequence and genomic cluster data available.

The images are then 3 different scans combined - T1, Flair and T1 contrast [2].

## U-Net Model
The model was chosen following the literature featuring this dataset [3] . It consist of a 4 layers U-Net shown below:

![Capture](https://user-images.githubusercontent.com/49094051/222289398-b18a5a81-7a57-4c56-b5c2-dbdad5552623.PNG)


## Results and performances
*... coming soon ...*

## Further imporvements
 - [ ] Implement DICE loss for better performances
 - [ ] compare FLAIR data With original pre-contrast + FLAIR + post-contrast data
 - [ ] make a report on the original study [3] reproducibility


[1] Cancer Genome Atlas Research Network. Comprehensive, integrative genomic analysis of
diffuse lower-grade gliomas. New England Journal of Medicine, 372(26):2481–2498, 2015.

[2] Kaggle Discussion topic https://www.kaggle.com/datasets/mateuszbuda/lgg-mri-segmentation/discussion/359225

[3] Mateusz Buda, AshirbaniSaha, Maciej A. Mazurowski "Association of genomic subtypes of lower-grade gliomas with shape features automatically extracted by a deep learning algorithm." Computers in Biology and Medicine, 2019. https://arxiv.org/abs/1906.03720
