# Deformable GANs for Pose-based Human Image Generation.
### Requirment
* python2
* Numpy
* Scipy
* Skimage
* Pandas
* Tensorflow
* Keras
* tqdm 

### Training
In orger to train a model:
1. Create folder market-dataset with 2 subfolder (test and train). Put the test images in test images in test/ and train images in train/.
2. Download pose estimator (conversion of this https://github.com/ZheC/Realtime_Multi-Person_Pose_Estimation) [pose_estimator.h5](https://yadi.sk/d/blgmGpDi3PjXvK). Launch ```python compute_cordinates.py.``` It will compute human keypoints. Alternativlly you can download key points estimations from (https://yadi.sk/d/suymftBy3S7oKD).
3. Create pairs dataset with ```python create_pairs_dataset.py```. It define pairs for training.
4. Run ```python train.py``` (see list of parameters in cmd.py)
To replicate the experiment in the paper use following setup:
#### Market
##### baseline
```CUDA_VISIBLE_DEVICES=0 python train.py --output_dir output/baseline --checkpoints_dir output/baseline --warp_skip none --dataset market --l1_penalty_weight 10 --batch_size 4 --number_of_epochs 90```
##### dsc
```CUDA_VISIBLE_DEVICES=0 python train.py --output_dir output/dsc --checkpoints_dir output/dsc --warp_skip mask --dataset market --l1_penalty_weight 10 --batch_size 4 --number_of_epochs 90```
##### full
```CUDA_VISIBLE_DEVICES=0 python train.py --output_dir output/full --checkpoints_dir output/full --warp_skip mask --dataset market --l1_penalty_weight 0.01 --nn_loss_area_size 3 --batch_size 4 --content_loss_layer block1_conv2 --number_of_epochs 90```
##### feature matching
```CUDA_VISIBLE_DEVICES=0 python train.py --output_dir output/fm --checkpoints_dir output/fm --warp_skip mask --dataset market --l1_penalty_weight 0.5 --batch_size 4 --content_loss_layer block2_conv1 --number_of_epochs 90```

#### Fashion
##### baseline
```CUDA_VISIBLE_DEVICES=0 python train.py --output_dir output/baseline --checkpoints_dir output/baseline --warp_skip none --dataset fasion --l1_penalty_weight 10 --batch_size 4 --number_of_epochs 90```
##### dsc
```CUDA_VISIBLE_DEVICES=0 python train.py --output_dir output/dsc --checkpoints_dir output/dsc --warp_skip mask --dataset fasion --l1_penalty_weight 10 --batch_size 4 --number_of_epochs 90```
##### full
```CUDA_VISIBLE_DEVICES=0 python train.py --output_dir output/full --checkpoints_dir output/full --warp_skip mask --dataset fasion --l1_penalty_weight 0.01 --nn_loss_area_size 5 --batch_size 2 --content_loss_layer block1_conv2 --number_of_epochs 90```
##### feature matching
```CUDA_VISIBLE_DEVICES=0 python train.py --output_dir output/fm --checkpoints_dir output/fm --warp_skip mask --dataset fasion --l1_penalty_weight 0.5 --batch_size 4 --content_loss_layer block2_conv1 --number_of_epochs 90```
### Testing
0. Download checkpoints (https://yadi.sk/d/dxVvYxBw3QuUT9).
1. Run ```python test.py --generator_checkpoint path/to/generator/checkpoint``` (and same parameters as in train.py). It generate images and compute inception score, SSIM score and their masked versions.
2. To compute ssd_score. Download pretrained on VOC 300x300 model from https://github.com/weiliu89/caffe/tree/ssd. Put it in the ssd_score forlder. Run ```python compute_ssd_score.py --input_dir path/to/generated/images --img_index 2```

#### Additional notes
Both training and testing require large amount of disk space, because ```compute_pose_map_batch``` in ```pose_dataset.py``` store intermediate pose_maps on disk. This help to improve both training and testing time. If you don't have enogh space comment lines 64,65,66 and 69 in ```pose_dataset.py```
