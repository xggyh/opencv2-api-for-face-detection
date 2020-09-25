Laboro TV Face Models: RetinaFace trained with Laboro TV Face dataset
======

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
<br>

#### Requirements<br>

* Python 3.6<br>
* Pytorch version == 1.1.0+<br>
* torch version == 0.3.0+<br>
* GPU is recommended
<br>

#### Installion<br>

```linux
git clone https://github.com/laboroai/LaboroTVFaceModels.git
cd ./LaboroTVFaceModels/LaboroTVFaceModels/
```
<br>

### Training process<br>
#### Pretrained weight<br>
We provide Resnet50 and Mobilenet0.25 as backbone network to train model. 
* For Resnet50 pretrained model is offered by torch model
* For Mobilenet0.25 pretrained model is offered by this [repository](https://github.com/biubug6/Pytorch_Retinaface), they trained the Mobilenet0.25 model on imagenet dataset and get 46.58% in top 1. Mobilenet0.25 pretrained weight link: [Google Drive](https://drive.google.com/file/d/1bilHHmGKfuqjQ3V7loqLRGgpAP8KHIKV/view?usp=sharing) and after downloading the model could be put as follows:
```linux
  ./weights/
      mobilenetV1X0.25_pretrain.tar
```
<br>

#### Check network configuration<br>

Before training, you need to check network configuration (e.g. batch_size, min_sizes and steps etc..) in the ```./data/config.py```, and we used the Tesla V100 GPU provided by colab during the training process. The memory is 16GB and if you train in this condition:<br>
* You can adjust batchsize to 32 when your backbone is Mobilenet0.25 and batchsize to 8 when your backbone is Resnet50<br>
* If you have multiple GPU，you can increase the batchsize number to 32 when your backbone is Resnet50<br>
<br>

#### Train the model using code:
* Multiple GPU: ```CUDA_VISIBLE_DEVICES=0,1,2,3 python train.py --network resnet50```<br>
* Single GPU: ```CUDA_VISIBLE_DEVICES=0 python train.py --network mobile0.25```
<br>

#### Train method:<br>
The train process consists of two phases, in which the learning_rate is changed:<br>
* For Resnet50, first train this model with lr==1e-3 to the 95th epoch, we use the 95th epochs' weight to do fine-tune, decrease the lr to 1e-5 and after 3 or 4 epoch and then we get the best weight.
* For Mobilenet0.25, first train this model with lr==1e-3 to the 200th epoch, we use the 155th and 180th epochs' weight to do fine-tune, decrease the lr to 1e-5 and after 10 epoch and then we get the best weight.
<br>

### Evaluation<br>
#### Evaluate on TVFace Dev-dataset<br>
You first need to download the groundtruths file and place it in:```./val_dataset_evaluate/```. [download link](https://drive.google.com/drive/folders/1E7tBOl37US0PJ6UhcVGfXPr2My2aar8X?usp=sharing)


 



