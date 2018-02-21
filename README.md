# Intel® Computer Vision SDK (Intel® CV SDK) | Hello World Tutorial


## Prerequisite checklist
- [ ] [Verify hardware compatibility](https://software.intel.com/en-us/computer-vision-sdk?cid=sem43700020075377675&intel_term=computer+vision+sdk&gclid=CjwKCAiA9f7QBRBpEiwApLGUit1KXgtbu46anzhcsxJVBltKW-JOxPzucCmBxVDZwI_1H4FYgQZ-3RoC96sQAvD_BwE&gclsrc=aw.ds)
- [ ] [Install OpenCL® and other dependencies](https://software.intel.com/en-us/articles/opencl-drivers)
- [ ] [Install the Intel® CV SDK and set environment variables](https://software.intel.com/en-us/cvsdk-installguide-installing-on-linux-os)

## Install the Hello World tutorial support files (models, videos, images, sample apps)

#### 1. Create the tutorial directory

	mkdir -p /opt/intel/tutorials/cvsdk_hello_world

#### 2. Change ownership of the tutorials directory to current user

	sudo chown –R <user.user> /opt/intel/tutorials/cvsdk_hello_world

#### 3. Navigate to the new directory

	cd /opt/intel/tutorials/cvsdk_hello_world

#### 4. Download and clone the tutorial content to the current directory (opt/intel/tutorials/cvsdk_hello_world). Four sub-directories are created: models, samples, videos, images

	git clone https://github.com/hunnel/cvsdk_hello_world.git


# Part 1: Optimizing and deploying a deep learning model for pedestrian detection (~15 minutes)


## Introduction and learning goals

#### What you’re about to learn

This tutorial uses a Single Shot MultiBox Detector (SSD) on a trained GoogleNet* model to walk you through the basic steps of using the Deep Learning Deployment Toolkit’s Inference Engine. The Inference Engine is included in the Intel® CV SDK, and you downloaded the trained model in the preparatory steps above. Although this tutorial uses GoogleNet, on your own you can perform inference on other neural network architectures, such as AlexNet*.

You will begin this tutorial by using the Model Optimizer to convert the trained model to two Intermediate Representation (IR) files. Your result will be one .xml file and one .bin file. The Inference Engine requires this model conversion so it can use the IR as input.

You will then use Inference on the IR files. Inference is the process of using a trained neural network to interpret meaning from data, such as images. The code sample in this tutorial uses images by feeding a short video of pedestrians, frame-by-frame, to the Inference Engine (the trained neural network). The result is image classification output -- text on a screen that displays information about the image, and a video that shows each pedestrian surrounded by a box.

The photo below shows an example frame from the inferred video. The red boxes around the individuals are the result of using the Inference Engine to identify pedestrians. In the original video, the boxes do not exist. The Inference Engine identified the objects in the video that it inferred to be pedestrians and drew the identifying boxes around them.

![alt text](https://github.com/hunnel/cvsdk_hello_world/blob/master/images/1-obj_detect_ped_demo_3.jpg "pedestrian detection success")

#### Overview of an end-to-end Computer Vision Application

The figure below shows you the full end-to-end computer vision application process. Some of the components shown are not part of the Intel® CV SDK, but are included in the diagram to help illustrate a complete E2E CV process.  

For example, the Intel® CV SDK requires a trained model. The first two columns of the figure cover aquiring a deep-learning model and refining it for your application. If you need to retrain the model after optimization using in the Intel® CV SDK, you will need to return to this part of the process, retrain your model, then re-run the Model Optimizer to generate new .IR files. For this tutorial, a trained model is provided.

After your trained model is completed, you are ready to use the Intel® CV SDK in a developer environment to write an application and optimize its performance on Intel® hardware. During this phase of development you use the Model Optimizer on the pre-trained model. In addition to optimizing your model, you also need to integrate the components within your application by calling the .IR files and the Inference Engine. Depending on your specific needs, you may also utilize other Intel® tools, like the Intel® Media SDK, in your application.

Once you have used the Model Optimizer and integrated the applicable components, you are ready to deploy your application at the edge. This piece of the process uses the Inference component of the Intel® CV SDK. In this tutorial, deployment is represented by using the video provided with this tutorial instead of using data directly from a video camera or on a separate piece of hardware.

![alt text](https://github.com/hunnel/cvsdk_hello_world/blob/master/images/e2e_cv_diagram.png "End-to-end computer vision workflow")

In the figure:
- The light blue boxes are the focus of this tutorial.
- The dark gray boxes are column headings to show where the work happpens.
- The light gray boxes shows the overall part of the process accomplished by each column.
- The white boxes are not addressed by this tutorial.
- The blue text tells you which piece of software is needed to perform the action in the box, whether or not they are addressed by this tutorial.

#### Why this tutorial is important for you

Deep learning is a complex process. To be successful at optimizing and deploying your own models, it is necessary to understand the process flow of moving from a trained model to an executable application. The illustration above gives you an overview of the process flow, and the steps in the tutorial reinforce selected steps in this process.

This video in this tutorial prepares you for more difficult deep learning scenarios. For example, this tutorial provides the basis of code for security functions like:

- Triggering an action if Inference detects someone entering an environment. For example, a homeowner might want to be alerted if a person walks onto their property, but not an animal.
- Comparing the number of people leaving an environment to the number of people who entered the environment. For example, a store might want to be alerted at closing time that someone might still be on the premises.



## Optimize a deep-learning model using the Model Optimizer (MO)

#### 1. Go to the sample application directory

	cd /opt/intel/tutorials/cvsdk/samples/ped_detection

#### 2. Run the Model Optimizer on the trained Caffe* model. This step generates one .xml file and one .bin file, both in the directory  <current_dir>/artifacts

	```sudo su```
	```source /opt/intel/computer_vision_sdk_2017.1.163/bin/setupvars.sh```
	```python runMO.py -w SSD_GoogleNetV2_caffe/SSD_GoogleNetV2.caffemodel -d SSD_GoogleNetV2_caffe/SSD_GoogleNetV2_Deploy.prototxt```

> The Model Optimizer converts a trained Caffe model to be compatible with the Intel Inference Engine and optimizes it for Intel
architecture
> These are the files you would include with your C++ application to apply inference to visual data
> Note: if you continue to train/update the Caffe model, you would then need to re-run the Model Optimizer to convert/optimize
again

#### 3. Verify creation of the optimized model files (the IR files)

#### 4. Exit super user mode

	exit

## Use the optimized models and Inference Engine in a pedestrian detection application

#### 1. Open the sample app source code to view the lines that call the Inference Engine.

> Explain how the application is calling the inference engine and setting parameters to define what they want to do. Give an example of how this can be changed to achieve different results and mention the related tutorial to learn more.

#### 2. Close the source file

#### 3. Build the sample application

 	make

#### 4. Run the pedestrian detection sample application to use the Inference Engine on a video
*Explain the parameters prior to running the app*

##### *Note: If you get an error related to "undefined reference to 'google::FlagRegisterer...", try uninstalling libgflags-dev: sudo apt-get remove libgflags-dev*

	./IEobjectdetection -i opt/intel/tutorials/cvsdk/videos/vtest.avi -fr 200 -m artifacts/VGG_VOC0712_SSD_300x300_deploy/VGG_VOC0712_SSD_300x300_deploy.xml -d CPU -l pascal_voc_classes.txt

> You should see a video play with people walking across and red bounding boxes around them. You should also
see the output in the console showing the objects found and the confidence level. The higher the confidence level, the more likely the model is correctly identifying and drawing bounding boxes around pedestrians in the video. (for example: 0.83 is more confident than 0.23)

## \(Optional) Explore using different parameters to see how they affect the results

#### 1. *define some suggested activities here and describe what should happen/what it means*

## Part 1 recap

> You can now be successful with these tasks:
- Running the Model Optimizer against a trained Cafe model.
- Identifing the two required Intermediate Representation (IR) files, and their directory location.
- Locating the source code that runs the Inference Engine.
- Building an Inference Engine.
- Running an application that uses the Inference Engine with a video to accomplish a task, in this case, identifying pedestrians.
- Modifying Inference Engine parameters.


# Part 2: Re-purpose a Pedestrian Detection application to identify cars (~10 minutes)


## Introduction and learning goals

#### 1. What you’re about to learn and why it is important

In Part 1 of this tutorial series, you used the Inference Engine to identify parameters by putting a bounding box around each person. In this tutorial, you will build on your Intel CV SDK skills by using the same sample source code to identify cars.

#### 2. Provide an example of what success looks like in this module

> Show a side-by-side comparison of the pedestrian video clip: original vs with bounding boxes

#### 3. Conceptual diagram: E2E video application developer journey map

> (continue to show this throughout the module as it changes? i.e. ‘you are here’)

## Update the pedestrian detection application code to target cars instead of people

#### 1. Navigate to the sample application directory (cd opt/intel/tutorials/cvsdk/samples)
#### 2. Create a copy of the ped_detection folder and name the new directory car_detection (cp -r ped_detection car_
detection)
#### 3. Open the object detection sample app source code to view the lines that call the IR.
#### 4. *David to define the steps to update the app*
#### 5. Save and close the source file.
#### 6. Build the sample application.
#### 7. Run the updated sample application to call the Inference Engine.

./IEobjectdetection -i opt/intel/tutorials/cvsdk/videos/vtest.avi -fr 200 -m artifacts/VGG_VOC0712_SSD_300x300_deploy/VGG_
VOC0712_SSD_300x300_deploy.xml -d CPU -l pascal_voc_classes.txt

## \(Optional) Explore using different parameters to see how they affect the results

#### 1. *define suggested activities here and describe what should happen/what it means*

## Part 2 recap

#### 1. *Provide a quick recap of what you did and why you care*
#### 2. *link to the next logical tutorial(s) in the learning path*

## Additional resources

- [CV SDK landing page (IDZ)](https://software.intel.com/en-us/computer-vision-sdk?cid=sem43700020075377675&intel_term=computer+vision+sdk&gclid=CjwKCAiA9f7QBRBpEiwApLGUit1KXgtbu46anzhcsxJVBltKW-JOxPzucCmBxVDZwI_1H4FYgQZ-3RoC96sQAvD_BwE&gclsrc=aw.ds)
- [Developer guide](https://software.intel.com/en-us/cvsdk-inference-engine-apiref)
- [API references](https://software.intel.com/en-us/inference-engine-devguide)

OpenCL is a trademark of Apple Inc. used by permission by Khronos
Intel is a registered trademark of Intel Corporation
* Other brands and names may be the property of others
