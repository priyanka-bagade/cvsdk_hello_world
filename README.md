

# Intel® Computer Vision SDK | Hello World Tutorial

<br>

### Prerequisite checklist
- [ ] [Verify hardware compatibility](https://software.intel.com/en-us/computer-vision-sdk?cid=sem43700020075377675&intel_term=computer+vision+sdk&gclid=CjwKCAiA9f7QBRBpEiwApLGUit1KXgtbu46anzhcsxJVBltKW-JOxPzucCmBxVDZwI_1H4FYgQZ-3RoC96sQAvD_BwE&gclsrc=aw.ds)
- [ ] [Install OpenCL® and other dependencies](https://software.intel.com/en-us/articles/opencl-drivers)
- [ ] [Install the Intel® CV SDK and set environment variables](https://software.intel.com/en-us/cvsdk-installguide-installing-on-linux-os)

<br>

### Install the tutorial support files

#### 1. Create the tutorial directory

	mkdir -p /opt/intel/tutorials/cvsdk_hello_world

#### 2. Change ownership of the tutorial directory to current user

	sudo chown –R <user.user> /opt/intel/tutorials/cvsdk_hello_world

#### 3. Navigate to the new directory

	cd /opt/intel/tutorials/cvsdk_hello_world

#### 4. Download and clone the tutorial content to the current directory (opt/intel/tutorials/cvsdk_hello_world).

	git clone https://github.com/hunnel/cvsdk_hello_world.git

<br>

## Part 1: Optimize and deploy a deep learning model for pedestrian detection (~15 minutes)

### What you’re about to learn

This tutorial uses a Single Shot MultiBox Detector (SSD) on a trained GoogleNet* model to walk you through the basic steps of using the Deep Learning Deployment Toolkit’s Inference Engine. The Inference Engine is included in the Intel® CV SDK, and you downloaded the trained model in the preparatory steps above. Although this tutorial uses GoogleNet, on your own you can perform inference on other neural network architectures, such as AlexNet*.

You will begin this tutorial by using the Model Optimizer to convert the trained model to two Intermediate Representation (IR) files. Your result will be one .xml file and one .bin file. The Inference Engine requires this model conversion so it can use the IR as input.

You will then use Inference on the IR files. Inference is the process of using a trained neural network to interpret meaning from data, such as images. The code sample in this tutorial uses images by feeding a short video of pedestrians, frame-by-frame, to the Inference Engine (the trained neural network). The result is image classification output -- text on a screen that displays information about the image, and a video that shows each pedestrian surrounded by a box.

The photo below shows an example frame from the inferred video. The red boxes around the individuals are the result of using the Inference Engine to identify pedestrians. In the original video, the boxes do not exist. The Inference Engine identified objects in the video that it inferred to be pedestrians and drew bounding boxes around them.

<br>

![image of video data before and after inference](https://github.com/hunnel/cvsdk_hello_world/blob/master/images/obj_detect_ped_demo_before_and_after.png "pedestrian detection success")

<br>

### Overview of an end-to-end computer vision application workflow

The figure below shows an example of an end-to-end computer vision application workflow. Some of the components shown are not part of the Intel® CV SDK, but are included in the diagram to help illustrate a complete end-to-end computer vision process.

![Diagram of an end-to-end computer vision workflow](https://github.com/hunnel/cvsdk_hello_world/blob/master/images/e2e_cv_diagram.png "End-to-end computer vision workflow")

**In the figure:**
- dark blue boxes highlight the focus of this tutorial
- white boxes are not directly addressed by this tutorial
- light blue text highlights other Intel® software tools available to use in various steps of an end-to-end computer vision workflow

<br>

### Why this tutorial is important for you

The Hello World tutorial serves two purposes:
<ol>
	<li>to ensure complete and accurate installation and setup of the Intel CV SDK and</li>
	<li>to introduce baseline capabilities needed to optimize end-to-end computer vision and deep-learning on Intel hardware</li>
</ol>

<br>

***

<br>   
<br>

## Optimize a deep-learning model using the Model Optimizer (MO)

#### 1. Navigate to the cv-sdk directory

	cd /opt/intel/computer_vision_sdk_2018.0.182/deployment_tools/model_optimizer

#### 2. Run the Model Optimizer on the pretrained Caffe* model. This step generates one .xml file and one .bin file and place both files in the tutorial samples directory (located here: /opt/intel/tutorials/cvsdk/cvsdk_hello_world/samples/artifacts)

	sudo apt install python3-pip
	pip3 install -r requirements_caffe.txt 
	python3 mo_caffe.py --input_model /opt/intel/tutorials/cvsdk/cvsdk_hello_world/samples/SSD_GoogleNetV2.caffemodel -o /opt/intel/tutorials/cvsdk/cvsdk_hello_world/samples/

The Model Optimizer converts a pretrained Caffe model to be compatible with the Intel Inference Engine and optimizes it for Intel architecture. These are the files you would include with your C++ application to apply inference to visual data.
	
> **Note:** if you continue to train or make changes to the Caffe model, you would then need to re-run the Model Optimizer on the updated model.

#### 3. Navigate to the tutorial sample directory

	cd /opt/intel/tutorials/cvsdk_hello_world

#### 4. Verify creation of the optimized model files (the IR files)

	ls

You should see the following two files listed in this directory: **SSD_GoogleNetV2.xml** and **SSD_GoogleNetV2.bin**

<br>
<br>

## Use the optimized models and Inference Engine in a pedestrian detection application


#### 1. Open the sample app source code to view the lines that call the Inference Engine.
<ul><ul>
	<li> Line 39 &#8212; adds the Inference Engine plugin to your application</li>
	<li> Line 107 &#8212; sets the confidence threshold for object detection</li>
	<li> Line 391 &#8212; loads the Inference Engine plugin for use within the application</li>
	<li> Line 417 &#8212; initializes the network object</li>
	<li> Line 637 &#8212; runs inference using the optimized model
</ul></ul>

#### 2. Close the source file

#### 3. Build the sample application

 	make

#### 4. Run the pedestrian detection sample application to use the Inference Engine on a video
The below command runs the application using the following parameters: 
<ul><ul>
		<li> number of frames to process (-fr)
		<li> location of the optimized deep-learning model (-m)
		<li> target device (CPU or GPU) to be used for inference (-d)
		<li> data labels list location (-l)
	</ul></ul>

	 source /opt/intel/computer_vision_sdk_2018.0.187/bin/setupvars.sh
	./IEobjectdetection -i opt/intel/tutorials/cvsdk_hello_world/hello_world_1.avi -fr 200 -m SSD_GoogleNetV2.xml -d CPU -l pascal_voc_classes.txt -t SSD


You should see a video play with people walking across and red bounding boxes around them. You should also see the output in the console showing the objects found and the confidence level. The higher the confidence level, the more likely the model is correctly identifying and drawing bounding boxes around pedestrians in the video. (for example: 0.83 is more confident than 0.23)

> **Note:** If you get an error related to "undefined reference to 'google::FlagRegisterer...", try uninstalling libgflags-dev: sudo apt-get remove libgflags-dev

<br>

### \(Optional) Explore using different parameters to see how they affect the results

#### 1. Adjust the number of video frames to run through the Inference Engine
The sample video file contains approximately 790 frames; however, in the previous example we opted to ran inference on a subset of those frames. Update the -fr parameter flag to adjust the amount of frames being processed through the Inference Engine.
	
	./IEobjectdetection -i opt/intel/tutorials/cvsdk_hello_world/hello_world_1.avi -fr 790 -m SSD_GoogleNetV2.xml -d CPU -l pascal_voc_classes.txt -t SSD

#### 2. Modify the object detection confidence level threshold
You can also try overriding the confidence level threshold. By setting the new threshold to 0.1, you will see a lot more bounding boxes around the people, but will have more instances of 'false' boxes appearing around non-pedestrian objects.
	
	./IEobjectdetection -i opt/intel/tutorials/cvsdk_hello_world/hello_world_1.avi -fr 200 -m SSD_GoogleNetV2.xml -d CPU -l pascal_voc_classes.txt -t SSD -thresh 0.1
	
<br>
<br>
<br>
   
## Recap

**Key takeaways from this tutorial:**
- How to run the Model Optimizer against a trained Caffe model to create the optimized .IR files.
- How to call the .IR files and Inference Engine from an application.
- How to run a CV application using the optimized model and Inference Engine to analyze video data and apply application logic, in this case, identifying pedestrians.
- Explore and modify application parameters to achieve various results.

<br>

## Next Steps

[Hello World | Part 2: Re-purpose a Pedestrian Detection application to identify cars (coming soon)](https://)

<br>
   
## Additional resources

- [CV SDK home page (IDZ)](https://software.intel.com/en-us/computer-vision-sdk?cid=sem43700020075377675&intel_term=computer+vision+sdk&gclid=CjwKCAiA9f7QBRBpEiwApLGUit1KXgtbu46anzhcsxJVBltKW-JOxPzucCmBxVDZwI_1H4FYgQZ-3RoC96sQAvD_BwE&gclsrc=aw.ds)
- [Deep Learning Inference Engine Developer Guide](https://software.intel.com/en-us/inference-engine-devguide)
- [API Reference for the Deep Learning Inference Engine](https://software.intel.com/en-us/cvsdk-inference-engine-apiref)
- [Intel® CV SDK Code Samples](https://software.intel.com/en-us/computer-vision-sdk-support/code-samples)
- [Using Custom Layers Tutorial with the Deep-Learning Deployment Toolkit](https://software.intel.com/en-us/cvsdk-custom-layers-support-in-inference-engine-tutorial)

<br>
<br>

***

###### OpenCL is a trademark of Apple Inc. used by permission by Khronos   
###### Intel is a registered trademark of Intel Corporation 
###### &ast;Other brands and names may be the property of others
