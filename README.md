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
#### Requirements <br>
* Python 3.6
* Pytorch version == 1.1.0+
* torch version == 0.3.0+
* GPU is recommended




