Readme · MD
# Do CNNs Trained on Brain MRI Tumor Classification Rely on Clinically Meaningful Features or Dataset Artifacts?
 
**TL;DR:** A ResNet-18 classifier hits 96.1% accuracy on brain tumor MRI classification. Grad-CAM reveals it's partly cheating using skull-boundary position as a shortcut instead of actual tumor morphology. When we removed that shortcut, accuracy dropped to 93.5%, proving a chunk of that "high performance" was spurious correlation, not clinical signal.
 
This matters because a model that looks accurate in testing but is right for the wrong reasons is a liability in real clinical deployment.
 
---
 
## Why This Project Exists
 
High test accuracy is not the same as clinical trustworthiness. Medical imaging models are known to exploit shortcuts such as scanner artifacts, borders, positional cues instead of learning the actual pathology. We wanted to find out whether that was happening here, quantify how much of the model's performance depended on it, and test whether a straightforward intervention could fix it.
 
Short answer: partially. The full story, including a Grad-CAM analysis that shows exactly where and why the model breaks, is in the [full report](./report.pdf).
 
## What I Did
 
- Trained a **ResNet-18** (pretrained on ImageNet, fine-tuned on 4-class brain MRI classification) on the Sartaj Brain Tumor MRI dataset
- Built a custom preprocessing + train/val/test pipeline with a fixed random seed for reproducibility across a 3-person team
- Implemented **Grad-CAM from scratch**, hooking into `layer4[1].conv2` to visualize which regions of the MRI actually drove each prediction
- Diagnosed a **location-based shortcut**: the model was using tumor position relative to the skull boundary as a proxy for tumor type, most visibly in glioma → meningioma misclassifications
- Designed and ran a **preprocessing intervention** (center-cropping + augmentation) specifically targeted at removing that shortcut
- Compared baseline vs. intervention models across accuracy/precision/recall, confusion matrices, and Grad-CAM attention patterns to see whether the fix actually worked, not just whether accuracy moved
## Key Finding
 
| | Baseline | Intervention |
|---|---|---|
| Accuracy | 96.1% | 93.5% |
| Precision | 96.0% | 93.8% |
| Recall | 96.4% | 93.6% |
 
The accuracy **drop** is the finding, not a failure. Once the skull-boundary shortcut was removed, the model lost the crutch it had been leaning on, meaning a meaningful share of the baseline's headline accuracy wasn't coming from real tumor understanding.
 
## Tech Stack
 
- **PyTorch / torchvision** — ResNet-18 architecture, pretrained weights, model fine-tuning
- **Grad-CAM** — custom implementation via forward/backward hooks on the final convolutional block
- **scikit-learn** — train/val/test splitting, precision/recall/confusion matrix computation
- **Matplotlib** — training curves, confusion matrix heatmaps, Grad-CAM overlays
- **Google Colab (T4 GPU)** — training environment
- **Sartaj Brain Tumor Classification (MRI) dataset** — glioma, meningioma, pituitary, no-tumor classes
 
## Next Steps
 
This is an active research direction, not a closed project. I'm continuing to explore ways to make the shortcut-detection and mitigation approach more rigorous and generalizable beyond this dataset. More to come.
 
## Full Report
 
The complete writeup — related work, full methodology, all four Grad-CAM figure sets, and the discussion of limitations — is available [here](./report.pdf).
