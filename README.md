# Counterfactual-Image-Generation-using-CycleGAN
## Image-to-Image Translation for Medical XAI: Counterfactual Insights into Pneumonia Detection
This project addresses the critical need for transparent and interpretable decision-making in medical image classification, where traditional methods often struggle to provide nuanced explanations. Recognizing the limitations of current approaches, particularly in conveying textural information, the project aims to pioneer a novel model-agnostic solution using adversarial image-to-image translation.

The primary objective is to generate realistic counterfactual images for image classifiers operating in medical contexts. By harnessing advanced techniques from adversarial learning, the project seeks to overcome the challenges associated with existing methods, such as saliency maps, which may not effectively capture the intricate nuances of decision-making processes rooted in texture and structure.

The proposed approach holds promise for enhancing the interpretability of deep learning models, especially in high-stakes domains like healthcare, where transparent decision-making is paramount. By providing realistic counterfactual explanations, the project aims to empower clinicians and stakeholders with insights into classifier decisions, thereby fostering trust and facilitating more informed medical interventions.


### Python 3.11.0 recommended
### Create a virtual envireonment
```
python -m venv myenv
```
### Activate the virtul environment
```
.\myenv\Scripts\activate
```
Train the CycleGAN
```

python pipeline.py --model_type "cycle-gan" --image_size 512 --batch_size 4 --epochs 10 --train_dir "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\data\rsna-pneumonia-dataset\train" --val_dir "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\data\rsna-pneumonia-dataset\val" --test_dir "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\data\rsna-pneumonia-dataset\test" --project "CycleGAN-CounterFactual Explanation" --job_name "Generator-and-Discriminator-training" --checkpoint_dir "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\models/gan" --classifier_path "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\models\swin_t-epoch15-val_loss0.50.ckpt"
```