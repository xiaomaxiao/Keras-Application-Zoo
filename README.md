# Keras | Application Zoo - DIY Deep Learning for Vision

<p align="center"> <img src="https://i.imgur.com/aT94WEj.png" /> </p>

[![GitHub license](https://img.shields.io/github/license/GKalliatakis/Keras-Application-Zoo.svg)](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/LICENSE)


## Introducing Keras Application Zoo: A library for reusable deep learning models in Keras.

Keras Application Zoo is a public clearinghouse to publish, discover, and reuse parts of machine learning modules in Keras. By a module, we mean a self-contained piece of a [Keras Applications](https://keras.io/applications/)-like model, along with its weights, that can be reused across other, similar tasks. By reusing a module, a developer can train a model using a smaller dataset, improve generalization, or simply speed up training. 

Lots of researchers and engineers have made their deep learning models public in various frameworks for different tasks with all kinds of architectures and data. These models are learned and applied for problems ranging from simple regression, to large-scale visual classification. 

However, [Keras](https://github.com/fchollet/keras) does not contain the degree of pre-trained models that come complete with [Caffe](http://caffe.berkeleyvision.org/). 


To lower the friction of sharing these models, we introduce the Keras Application Zoo:

- A **central GitHub repo** for sharing popular deep learning models with **Keras code & weights files**
- Contains **ONLY additional** deep learning models which are **not yet available within `keras.applications` module itself or [Keras community contributions official extension repository](https://github.com/farizrahman4u/keras-contrib)**
- Tools to upload/download model info to/from GitHub, and to download trained Keras Applications-like binaries
- Models can be used for prediction, feature extraction, and fine-tuning just like the genuine canned `keras.applications` architectures 
- **No separate models configuration files in a declarative format**. Models are described in Python code, which is compact, easier to debug, and allows for ease of extensibility

**BENEFIT FROM NETWORKS THAT YOU COULD NOT PRACTICALLY TRAIN YOURSELF BY TAKING KERAS TO THE ZOO!**

Read the official documentation at [Keras.io](https://keras.io).

------------------

## Usage
All architectures are compatible with both TensorFlow and Theano, and upon instantiation the models will be built according to the image dimension ordering set in your Keras configuration file at ~/.keras/keras.json. For instance, if you have set image_dim_ordering=tf, then any model loaded from this repository will get built according to the TensorFlow dimension ordering convention, "Width-Height-Depth".

Pre-trained weights can be automatically loaded upon instantiation (weights='places' argument in model constructor for all scene-centric models and the familiar weights='imagenet' for the rest). Weights are automatically downloaded.

------------------
## Available models

### Models for image classification with weights trained on [ImageNet](http://www.image-net.org/):
- [ResNet152](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/resnet152.py)
- [ResNet101](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/resnet101.py) 

### Models for image classification with weights trained on [Places](http://places2.csail.mit.edu/):
- [VGG16-places365](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/vgg16_places_365.py)
- [VGG16-hybrid1365](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/vgg16_hybrid_places_1365.py)

------------------
## Examples 

### Classify ImageNet classes with ResNet152

```python
from resnet152 import ResNet152
from keras.preprocessing import image
from imagenet_utils import preprocess_input, decode_predictions

model = ResNet152(weights='imagenet')

img_path = 'elephant.jpg'
img = image.load_img(img_path, target_size=(224, 224))
x = image.img_to_array(img)
x = np.expand_dims(x, axis=0)
x = preprocess_input(x)

preds = model.predict(x)
print('Predicted:', decode_predictions(preds))
```

### Classify Places classes with VGG16-places365

```python
import os
import urllib2
import numpy as np
from PIL import Image
from cv2 import resize

from vgg16_places_365 import VGG16_Places365

TEST_IMAGE_URL = 'http://places2.csail.mit.edu/imgs/demo/6.jpg'

image = Image.open(urllib2.urlopen(TEST_IMAGE_URL))
image = np.array(image, dtype=np.uint8)
image = resize(image, (224, 224))
image = np.expand_dims(image, 0)

model = VGG16_Places365(weights='places')
predictions_to_return = 5
preds = model.predict(image)[0]
top_preds = np.argsort(preds)[::-1][0:predictions_to_return]

# load the class label
file_name = 'categories_places365.txt'
if not os.access(file_name, os.W_OK):
    synset_url = 'https://raw.githubusercontent.com/csailvision/places365/master/categories_places365.txt'
    os.system('wget ' + synset_url)
classes = list()
with open(file_name) as class_file:
    for line in class_file:
        classes.append(line.strip().split(' ')[0][3:])
classes = tuple(classes)

print('--SCENE CATEGORIES:')
# output the prediction
for i in range(0, 5):
    print(classes[top_preds[i]])

```

### Extract features from images with VGG16-hybrid1365

```python
import urllib2
import numpy as np
from PIL import Image
from cv2 import resize

from vgg16_hybrid_places_1365 import VGG16_Hubrid_1365

TEST_IMAGE_URL = 'http://places2.csail.mit.edu/imgs/demo/6.jpg'

image = Image.open(urllib2.urlopen(TEST_IMAGE_URL))
image = np.array(image, dtype=np.uint8)
image = resize(image, (224, 224))
image = np.expand_dims(image, 0)

model = VGG16_Hubrid_1365(weights='places', include_top=False)
features = model.predict(image)
```
------------------

### Documentation for individual models

|                                                       Model                                                       |  Size  | Top-1 Accuracy | Top-5 Accuracy |  Parameters |
|:-----------------------------------------------------------------------------------------------------------------:|:------:|:--------------:|:--------------:|:-----------:|
|            [ResNet152](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/resnet152.py)            | 232 MB |      77.6%     |      93.8%     |  60,495,656 |
| [ResNet101](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/resnet101.py)                       | 170 MB | 76.4%          | 92.9%          |  44,476,712 |
|      [VGG16-places365](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/vgg16_places_365.py)     | 518 MB |     55.24%     |     84.91%     | 135,755,949 |
| [VGG16-hybrid1365](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/vgg16_hybrid_places_1365.py) | 534 MB |                |                | 139,852,949 |

The top-1 and top-5 accuracy refers to the model's performance on the ImageNet or Places validation dataset

------------------

## Licensing 
- All code in this repository is under the MIT license as specified by the [LICENSE file](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/LICENSE).
- The VGG16-places365 and VGG16-hybrid1365 weights are ported from the ones [released by CSAILVision](https://github.com/CSAILVision/places365) under the [MIT license](https://github.com/CSAILVision/places365/blob/master/LICENSE).
- The ResNet-152 weights are ported from ones [released by adamcasson](https://github.com/adamcasson/resnet152).
- The ResNet-101 weights are ported from ones [released by flyyufelix](https://gist.github.com/flyyufelix/65018873f8cb2bbe95f429c474aa1294)

We are always interested in how these models are being used, so if you found them useful or plan to make a release of code based on or using this package, it would be great to hear from you. 

Additionally, don't forget to cite this repo if you use these models:

    @misc{GKalliatakis_Keras_Application_Zoo,
    title={Keras-Application-Zoo},
    author={Grigorios Kalliatakis},
    year={2017},
    publisher={GitHub},
    howpublished={\url{https://github.com/GKalliatakis/Keras-Application-Zoo}},
    }

## Other Models 
More models to come!

We hope you find Keras Application Zoo useful in your projects! To stay in touch, you can star :star: the GitHub project.

------------------

## Contributing to Keras Application Zoo

We love your input! We want to make contributing to this project as easy and transparent as possible, whether it's:

- Reporting a bug
- Discussing the current state of the code
- Submitting a fix
- Proposing new features
- Becoming a maintainer

###  We Develop with GitHub :octocat: 
We use GitHub to host code, to track issues and feature requests, as well as accept pull requests.

1. Check for [open issues](https://github.com/GKalliatakis/Keras-Application-Zoo/issues) or open a fresh one to start a discussion around a feature idea or a bug.
2. Fork [the repository](https://github.com/GKalliatakis/Keras-Application-Zoo) on GitHub to start making your changes (branch off of the master branch).
3. Write a test that shows the bug was fixed or the feature works as expected.
4. If you feel uncomfortable or uncertain about an issue or your changes, don't hesitate to contact us.

When you submit code changes, your submissions are understood to be under the same [MIT License](https://github.com/GKalliatakis/Keras-Application-Zoo/blob/master/LICENSE) that covers the project. Feel free to contact the maintainers if that's a concern.

### Report bugs using GitHub's issues
We use GitHub issues to track public bugs. Report a bug by [opening a new issue](https://github.com/GKalliatakis/Keras-Application-Zoo/issues); it's that easy!

