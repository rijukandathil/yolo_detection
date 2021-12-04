# Detection of Cars and Person Using Darknet-YoloV4

#### Clone the repository
```
git clone
cd yolo_detection
```
#### Download the dataset and unzip the folder

```
wget https://evp-ml-data.s3.us-east-2.amazonaws.com/ml-interview/openimages-personcar/trainval.tar.gz
tar -xvzf trainval.tar.gz

```
#### The data set is in COCO format and we have to convert to YOLO format.
```
python3 COCO2YOLO.py -j trainval/annotations/bbox-annotations.json -o trainval/images/
```
#### Compile darknet on Linux (using make)
 Before make, we have to edit the makefile
*  set `GPU=1`, `CUDNN=1`, `OPENCV=1` (make sure that you have correctly installed cuda, cudnn and opencv)
* Then `make` 

#### Train the model with our coustom dataset
1. Download the pre-trained weights-file (162 MB): [yolov4.conv.137](https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v3_optimal/yolov4.conv.137) .
2. Create file `yolo-obj.cfg` with the same content as in `yolov4-custom.cfg`.
   > change line batch to `batch=64` and `subdivisions=64`
   > change line `max_batches` to `8000`
   > change line `steps` to `6400` and `7200` (80% and 90% of `max_batches`)
   > change line `classes=80` to `2` in each of 3 yolo-layers
   > change `filters=255` to `filters=21`**(classes + 5)x3** in the 3 convolutional before each yolo-layers

3. Create file `obj.names` in the directory `data\`, with objects names - each in new line.
   >   person
   >   car

4. Create file `obj.data` in the directory `data\`, containing:
   > classes = 2
   > train  = data/train.txt
   > valid  = data/test.txt
   > names = data/obj.names
   > backup = backup/

5. Put image files (.jpg) and annotated files (.txt) in the directory `data\obj\`.
6. Run `generate_train.py` to create file train.txt in directory `data\`, with filenames of the images, each filename in new line.

   > data/obj/image_000001979.jpg
   > data/obj/image_000000934.jpg
   > data/obj/image_000001241.jpg
   > data/obj/image_000000145.jpg

7. Train the model by running `./darknet detector train data/obj.data cfg/yolo-obj.cfg yolov4.conv.137` and weights are saved.


![](https://i.imgur.com/7Drj1BJ.png)

#### Testing the model on a coustom image
* The trained weights can be downloaded from [Google Drive](https://drive.google.com/file/d/10P616BtblhM_jLz0h-gc0LSrbcYyObF0/view?usp=sharing).
* Open `yolo-obj.cfg`:
  > change line batch to `batch=1` and `subdivisions=1
* Run `./darknet detector test data/obj.data cfg/yolo-obj.cfg yolo-obj_final.weights test.jpg`.

![](https://i.imgur.com/sdXoqP8.jpg)

