Laboro TV Face Models: RetinaFace trained with Laboro TV Face dataset
======
Introduction
---
### About RetinaFace model
This RetinaFace model was trained with our Laboro TV Face dataset, on the basis of the [official RetinaFace model](https://github.com/deepinsight/insightface/tree/master/RetinaFace) and [Retinaface model by Pytorch](https://github.com/biubug6/Pytorch_Retinaface), We chose the pytorch version of the retinaface model, and used the resnet50 and mobilenet0.25 respectively as the backbone to train and fine-tune our TV face dataset.
### How well is the performance
After training by our dataset (training method is below), we evaluated our model by five task:
* Our Laboro dev-dataset<br>
* Our Laboro test-dataset<br>
* WiderFace easy evaluation dataset<br>
* WiderFace medium evaluation dataset<br>
* WiderFace hard evaluation dataset<br>

When we used resnet50 as the backbone,the accuracy is higher and model performed better, but when we used mobilenet as the backbone the inference speed was much faster. So the two backbones could be selected according to actual conditions of use.

| backbone | accuracy on tv-dev | accuracy on tv-test | accuracy on widerface easy | accuracy on widerface medium | accuracy on widerface hard |
|:--------:|:------------------:|:-------------------:|:--------------------------:|:----------------------------:|:--------------------------:|
|resnet50  |91.14%              |89.13%               |86.37%                      |85.63%                        |79.59%                      |
|mobilenet0.25  |85.54%              |83.51%               |--.--%                      |--.--%                        |--.--%                      |
### License

![](https://camo.githubusercontent.com/0e32abe541a386cbaf8370777b4b55c35d31657d/68747470733a2f2f692e6372656174697665636f6d6d6f6e732e6f72672f6c2f62792d6e632f342e302f38387833312e706e67)

This work is licensed under a [Creative Commons Attribution-NonCommercial 4.0 International License.](https://creativecommons.org/licenses/by-nc/4.0/)<br>
For commercial use, please [contact Laboro.AI Inc.](https://laboro.ai/contact/other/)

About our Laboro TV dataset
---
### Introduction of Laboro TV dataset
The laboro face dataset is a dataset of pictures screenshotted from TV. All pictures are taken at a certain moment of different programs. There are 33,935 pictures in total, and 22,811 pictures contain faces. The total number of faces is 68,896 and average one picture which contains faces has 3.02 faces. The resolutions of these images are 1920 x 1080. This dataset can be used in tv face detection.
### Annotation details
The annotation of each picture contains:<br>
* The number of faces contained in this picture (if there is no face in picture, there is no label below)<br>
* The position of the boundingbox (x, y, w, h)<br>
* Three levels according to the degree of blur, shaknesthree, occurrence(1 heavy, 2 normal, 3 without) and two different posts of the face(atypical, typical).<br>
```
example:
001.jpg
2
27, 56, 14, 17, 1, 0, 0, 0
513, 311, 5, 7, 2, 1, 0, 0
```
Train with Our Model
---
### Preparation for training<br>
#### Dataset - Laboro TV Face <br>

* We filtered the pictures containing human faces from the total dataset for training, development and testing. and we only train the position of the face bounding box labels, without training on blur, shakiness, occlusion, and pose labels.<br>
* The total number of pictures containing human faces is 22811, of which are devided into 17799 for train, 1263 for dev, and 3749 for test.<br>
* We also provide the organized dataset we used as followed directory structure. <br>
Link: from Google Drive [tvface](https://drive.google.com/drive/folders/1zT16rpWvVJrnDKG13mU6rkMXlZP9F01E?usp=sharing)
and after downloading you need to organise the dataset directory as follows:<br>
```
  ./data/tvface/
    train/
      images/
      label.txt
    val/
      images/
      tvval_list.txt
    test/
      images/
      tvtest_list.txt
```

#### Requirements<br>

* Python 3.6<br>
* Pytorch version == 1.1.0+<br>
* torch version == 0.3.0+<br>
* GPU is recommended

#### Installion<br>

```linux
git clone https://github.com/laboroai/LaboroTVFaceModels.git
cd ./LaboroTVFaceModels/LaboroTVFaceModels/
```

### Training process<br>
#### Pretrained weight<br>
We provide Resnet50 and Mobilenet0.25 as backbone network to train model. 
* For Resnet50 pretrained model is offered by torch model
* For Mobilenet0.25 pretrained model is offered by this [repository](https://github.com/biubug6/Pytorch_Retinaface), they trained the Mobilenet0.25 model on imagenet dataset and get 46.58% in top 1. Mobilenet0.25 pretrained weight link: [Google Drive](https://drive.google.com/file/d/1bilHHmGKfuqjQ3V7loqLRGgpAP8KHIKV/view?usp=sharing) and after downloading the model could be put as follows:
```linux
  ./weights/
      mobilenetV1X0.25_pretrain.tar
```

#### Check network configuration<br>

Before training, you need to check network configuration (e.g. batch_size, min_sizes and steps etc..) in the ```./data/config.py```, and we used the Tesla V100 GPU provided by colab during the training process. The memory is 16GB and if you train in this condition:<br>
* You can adjust batchsize to 32 when your backbone is Mobilenet0.25 and batchsize to 8 when your backbone is Resnet50<br>
* If you have multiple GPU，you can increase the batchsize number to 32 when your backbone is Resnet50<br>

#### Train the model using code:
* Multiple GPU: ```CUDA_VISIBLE_DEVICES=0,1,2,3 python train.py --network resnet50```<br>
* Single GPU: ```CUDA_VISIBLE_DEVICES=0 python train.py --network mobile0.25```

#### Train method:<br>
The train process consists of two phases, in which the learning_rate is changed:<br>
* For Resnet50, first train this model with lr==1e-3 to the 95th epoch, we use the 95th epochs' weight to do fine-tune, decrease the lr to 1e-5 and after 3 or 4 epoch and then we get the best weight.
* For Mobilenet0.25, first train this model with lr==1e-3 to the 200th epoch, we use the 155th and 180th epochs' weight to do fine-tune, decrease the lr to 1e-5 and after 10 epoch and then we get the best weight.
<br>

### Evaluation<br>
#### Evaluate on TVFace Dev-dataset<br>
You first need to download the dev groundtruths file and place it in:```./val_dataset_evaluate/```.  [download link](https://drive.google.com/drive/folders/1SnRVaS_l6U4yg6bfPCDdfupAza55mYUH?usp=sharing)<br>
* Generate txt file:
```linux
python val_tvface.py  --trained_model your_trained_model --network resnet50 or mobile0.25
```
* Evaluation:
```linux
cd val_dataset_evaluate/
python eval.py
```
* Result will be saved in ```./result/```<br>

#### Evaluate on TVFace Test-dataset<br>
You first need to download the test groundtruths file and place it in:```./test_dataset_evaluate/```.  [download link](https://drive.google.com/drive/folders/1YA60ZBHgFe3TPcpmNXTsEVWC5Fxm2oGi?usp=sharing)<br>
* Generate txt file:
```linux
python test_tvface.py  --trained_model your_trained_model --network resnet50 or mobile0.25
```
* Evaluation:
```linux
cd test_dataset_evaluate/
python eval.py
```
* Result will be saved in ```./result/```
