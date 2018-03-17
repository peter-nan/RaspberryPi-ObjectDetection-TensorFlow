# Object Detection Using Tensorflow on the Raspberry Pi

Script for object detection from training new model on dataset to exporting quantized graph

## Step 1. Setup

### Using docker registry
This is fastest way to use the repo
```
docker pull docker.nanonets.com/pi_training
```
OR

### Building locally
Copy dataset with `images` folder containing all training images and `annotations` folder containing all respective annotations inside data folder in repo which will be mounted by docker as volume

Ideally should run this script using nvidia-docker

#### Docker build script
Should run this script from repository root
```
docker build -t pi_training -f docker/Dockerfile.training.gpu .
docker image tag pi_training docker.nanonets.com/pi_training
```
------

## Step 2. Starting training
Tensorboard will be started at port 8000 and run in background
You can specify -h parameter to get help for docker script

```
sudo nvidia-docker run -p 8000:8000 -v `pwd`:data:data nanonets.docker.com/pi_training -m train -a ssd_mobilenet_v1_coco -e ssd_mobilenet_v1_coco -p '{"batch_size":8,"learning_rate":0.003}'
```

### Usage
```
run.sh [-m mode] [-a architecture] [-h hparams] [-e experiment_id] [-c checkpoint]
```

Do stuff with FILE and write the result to standard output. With no FILE
or when FILE is -, read standard input.

	-m          mode: train or export
	-h          display this help and exit
	-p          comma separated key value pairs of hyperparemeters
	-e          experiment id. Used as path inside data folder to run current experiment
	-c          when mode is export, used to specify checkpoint to use for export

------

## Step 3. Exporting trained model
```
sudo nvidia-docker run -v `pwd`:data:data nanonets.docker.com/pi_training -m export -a ssd_mobilenet_v1_coco -e ssd_mobilenet_v1_coco -c /data/0/model.ckpt-8998
```
