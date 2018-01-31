# Intel Computer Vision SDK | Hello World Tutorial


## Prerequisite checklist
- [ ] Verify HW compatibility
- [ ] Install OpenCL and other dependencies
- [ ] Install the CV SDK and set environment variables

## Install the Hello World tutorial support files (trained models, videos, images)

#### 1. Create new directory opt/intel/tutorials/cvsdk

	mkdir opt/intel/tutorials/cvsdk

#### 2. Navigate to the new directory

	cd opt/intel/tutorials/cvsdk

#### 3. Download/clone tutorial files to current directory (opt/intel/tutorials/cvsdk) which includes the following sub-directories: models, samples, videos, images

	git clone https://github.com/intel-iot-devkit/computer-vision-hello-world.git



# Part 1: How to optimize and deploy a deep learning model for pedestrian detection (~15mins)


## Introduction and learning goals

#### 1. Introduction and overview of what you’re about to learn and why you would care

This tutorial will walk you through the basics of using the Deep Learning Deployment Toolkit’s Inference Engine (included in the Intel® Computer Vision SDK). Here, inference is the process of using a trained neural network to infer meaning from data (e.g., images). In the code sample that follows, a video (frame by frame) is fed to the Inference Engine (our trained neural network) which then outputs a result (classification of an image). Inference can be done using various neural network architectures (AlexNet*, GoogleNet*, etc.). 

This example uses a Single Shot MultiBox Detector (SSD) on GoogleNet model. The Inference Engine requires that the model be converted to IR (Intermediate Representation) files. This tutorial will walk you through the basics taking an existing model (GoogleNet) and converting it to IR (Intermediate Representation) files using the Model Optimizer.
	
#### 2. Provide an example of what success looks like in this module

> Show a side-by-side comparison of the pedestrian video clip: original vs with bounding boxes
	
#### 3. Conceptual diagram: E2E video application developer journey map

> (continue to show this throughout the module as it changes? i.e. ‘you are here’)

## Optimize a deep-learning model using the Intel® Model Optimizer

#### 1. Navigate to the sample application directory

	cd opt/intel/tutorials/cvsdk/samples/ped_detection

#### 2. Run the Intel Model Optimizer on the trained Caffe model — generates two fi les (.xml and .bin) in <current_dir>/artifacts

> Model Optimizer converts a trained Caffe model to be compatible with the Intel Inference Engine and optimizes it for Intel
architecture
> These are the files you would include with your C++ application to apply inference to visual data
> Note: if you continue to train/update the Caffe model, you would then need to re-run the MO to convert/optimize
again

#### 3. Exit super user mode
	
	exit

## Use the optimized models and Inference Engine in a pedestrian detection application

#### 1. Open the sample app source code to view the lines which call the IR.

> Explain how the application is calling the IR and setting parameters to define what they want to do. Give an example of how this can be changed to achieve different results and mention the related tutorial to learn more.

#### 2. Close the source file

#### 3. Build the sample application

 	make

#### 4. Run the pedestrian detection sample application to use the Inference Engine on a video 
*Explain the parameters prior to running the app*

	./IEobjectdetection -i opt/intel/tutorials/cvsdk/videos/vtest.avi -fr 200 -m artifacts/VGG_VOC0712_SSD_300x300_deploy/VGG_VOC0712_SSD_300x300_deploy.xml -d CPU -l pascal_voc_classes.txt

> You should see a video play with people walking across and red bounding boxes around them. You should also
see the output in the console showing the objects found and the confidence level. The higher the confidence level, the more likely the model is correctly identifying and drawing bounding boxes around pedestrians in the video. (for example: 0.83 is more confident than 0.23)

## \(Optional) Explore using different parameters to see how they affect the results

#### 1. *define some suggested activities here and describe what should happen/what it means*

## Part 1 recap

#### 1. *Provide a quick recap of what you did and why you care*


# Part 2: Re-purpose a Pedestrian Detection application to identify cars (~10mins)


## Introduction and learning goals

#### 1. Introduction and overview of what you’re about to learn and why you would care

This tutorial will walk you through the basics of using the Deep Learning Deployment Toolkit’s Inference Engine (included in the Intel® Computer Vision SDK). Here, inference is the process of using a trained neural network to infer meaning from data (e.g., images). In the code sample that follows, a video (frame by frame) is fed to the Inference Engine (our trained neural network) which then outputs a result (classification of an image). Inference can be done using various neural network architectures (AlexNet*, GoogleNet*, etc.). 

This example uses a Single Shot MultiBox Detector (SSD) on GoogleNet model. The Inference Engine requires that the model be converted to IR (Intermediate Representation) files. This tutorial will walk you through the basics taking an existing model (GoogleNet) and converting it to IR (Intermediate Representation) files using the Model Optimizer.
	
#### 2. Provide an example of what success looks like in this module

> Show a side-by-side comparison of the pedestrian video clip: original vs with bounding boxes
	
#### 3. Conceptual diagram: E2E video application developer journey map

> (continue to show this throughout the module as it changes? i.e. ‘you are here’)

## Update the pedestrian detection application code to target cars instead of people

#### 1. Navigate to the sample application directory (cd opt/intel/tutorials/cvsdk/samples)
#### 2. Create a copy of the ped_detection folder and rename it car_detection (cp -r ped_detection car_
detection)
#### 3. Open the object detection sample app source code to view the lines which call the IR.
#### 4. *David to define the steps to update the app.
#### 5. Save and close the source file.
#### 6. Build the sample application
#### 7. Run the updated sample application to call the Inference Engine

./IEobjectdetection -i opt/intel/tutorials/cvsdk/videos/vtest.avi -fr 200 -m artifacts/VGG_VOC0712_SSD_300x300_deploy/VGG_
VOC0712_SSD_300x300_deploy.xml -d CPU -l pascal_voc_classes.txt

## \(Optional) Explore using different parameters to see how they affect the results

#### 1. *define some suggested activities here and describe what should happen/what it means*

## Part 2 recap

#### 1. *Provide a quick recap of what you did and why you care*
#### 2. *link to the next logical tutorial(s) in the learning path

## Additional resources

#### 1. CV SDK landing page (IDZ)
#### 2. Developer guide
#### 3. API references
