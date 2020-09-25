Laboro TV Face Models: RetinaFace trained with Laboro TV Face dataset
======
Introduction
---
### About RetinaFace model
This RetinaFace model was trained with our labora TV Face dataset, on the basis of the [official RetinaFace model](https://github.com/deepinsight/insightface/tree/master/RetinaFace) and [Retinaface model by Pytorch](https://github.com/biubug6/Pytorch_Retinaface), We chose the pytorch version of the retinaface model, and used the resnet50 and mobilenet0.25 respectively as the backbone to train and fine-tune our TV face dataset.
### How well is the performance
| backbone | accuracy on tv-dev | accuracy on tv-test | accuracy on widerface easy | accuracy on widerface medium | accuracy on widerface hard |
|:--------:|:------------------:|:-------------------:|:--------------------------:|:----------------------------:|:--------------------------:|
|resnet50  |91.14%              |89.13%               |86.37%                      |85.63%                        |79.59%                      |



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





 



