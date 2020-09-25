Laboro TV Face Models: RetinaFace trained with Laboro TV Face dataset
======

Train with Our Model
---
### Preparation for training<br>
#### Dataset - Laboro TV Face <br>
1 We filtered the pictures containing human faces from the total dataset for training, development and testing. and we only train the position of the face bounding box labels, without training on blur, shakiness, occlusion, and pose labels. The total number of pictures containing human faces is 22811, of which are devided into 17799 for train, 1263 for dev, and 3749 for test.<br>
We provide the pictures that have been divided, and after downloading you need to organise the dataset directory as follows:
‘’‘
./data/widerface/
    train/
      images/
      label.txt
    val/
      images/
      wider_val.txt
      ’‘’



