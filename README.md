

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

The photo below shows an example frame from the inferred video. The red boxes around the individuals are the result of using the Inference Engine to identify pedestrians. In the original video, the boxes do not exist. The Inference Engine identified the objects in the video that it inferred to be pedestrians and drew the identifying boxes around them.

<br>

![image of video data before and after inference](https://github.com/hunnel/cvsdk_hello_world/blob/master/images/obj_detect_ped_demo_before_and_after.png "pedestrian detection success")

<br>

### Overview of an end-to-end computer vision application workflow

The figure below shows you the full end-to-end computer vision application process. Some of the components shown are not part of the Intel® CV SDK, but are included in the diagram to help illustrate a complete E2E CV process.  

For example, the Intel® CV SDK requires a trained model. The first two columns of the figure cover acquiring a deep-learning model and refining it for your application. If you need to retrain the model after optimization using in the Intel® CV SDK, you will need to return to this part of the process, retrain your model, then re-run the Model Optimizer to generate new .IR files. For this tutorial, a trained model is provided.

After your trained model is completed, you are ready to use the Intel® CV SDK in a developer environment to write an application and optimize its performance on Intel® hardware. During this phase of development you use the Model Optimizer on the pre-trained model. In addition to optimizing your model, you also need to integrate the components within your application by calling the .IR files and the Inference Engine. Depending on your specific needs, you may also utilize other Intel® tools, like the Intel® Media SDK, in your application.

Once you have used the Model Optimizer and integrated the applicable components, you are ready to deploy your application at the edge. This piece of the process uses the Inference component of the Intel® CV SDK. In this tutorial, deployment is represented by using the video provided with this tutorial instead of using data directly from a video camera or on a separate piece of hardware.

![Diagram of an end-to-end computer vision workflow](https://github.com/hunnel/cvsdk_hello_world/blob/master/images/e2e_cv_diagram.png "End-to-end computer vision workflow")

**In the figure:**
- The blue boxes highlight the focus of this tutorial.
- The dark gray boxes are column headings to show where the work happens.
- The light gray boxes shows the overall part of the process accomplished by each column.
- The white boxes are not directly addressed by this tutorial.
- The blue text shows which piece of Intel® software performs the action in the box, whether or not they are addressed by this tutorial.

<br>

### Why this tutorial is important for you

Deep learning is a complex process. To be successful at optimizing and deploying your own models, it is necessary to understand the process flow of moving from a trained model to an executable application. The illustration above gives you an overview of the process flow, and the steps in the tutorial reinforce selected steps in this process.

This video in this tutorial prepares you for more difficult deep learning scenarios. For example, this tutorial provides the basis of code for security functions like:

- Triggering an action if Inference detects someone entering an environment. For example, a homeowner might want to be alerted if a person walks onto their property, but not an animal.
- Comparing the number of people leaving an environment to the number of people who entered the environment. For example, a store might want to be alerted at closing time that someone might still be on the premises.

<br>

***

<br>   
<br>

## Optimize a deep-learning model using the Model Optimizer (MO)

#### 1. Navigate to the cv-sdk directory

	cd /opt/intel/computer_vision_sdk_2018.0.182/deployment_tools/model_optimizer

#### 2. Run the Model Optimizer on the pretrained Caffe* model. This step generates one .xml file and one .bin file, both in the tutorial samples directory /opt/intel/tutorials/cvsdk/cvsdk_hello_world/samples/artifacts

	sudo apt install python3-pip
	pip3 install -r requirements_caffe.txt 
	python3 mo_caffe.py --input_model /opt/intel/tutorials/cvsdk/cvsdk_hello_world/samples/SSD_GoogleNetV2.caffemodel -o /opt/intel/tutorials/cvsdk/cvsdk_hello_world/samples/

The Model Optimizer converts a pretrained Caffe model to be compatible with the Intel Inference Engine and optimizes it for Intel architecture. These are the files you would include with your C++ application to apply inference to visual data.
	
> **Note:** if you continue to train or make changes to the Caffe model, you would then need to re-run the Model Optimizer on the updated model.

#### 3. Verify creation of the optimized model files (the IR files)

	ls

You should see the following two files listed in this directory: **SSD_GoogleNetV2.xml** and **SSD_GoogleNetV2.bin**

<br>
<br>

## Use the optimized models and Inference Engine in a pedestrian detection application

#### 1. Navigate to the tutorial sample directory

	cd /opt/intel/tutorials/cvsdk_hello_world

#### 2. Open the sample app source code to view the lines that call the Inference Engine.
<ul><ul>
	<li> Line 39 &#8212; adds the Inference Engine plugin to your application
	<li> Line 107 &#8212; sets the confidence threshold for object detection
	<li> Lines 382-404 &#8212; loads the Inference Engine plugin for use within the application
	<li> Lines 506-515 &#8212; loads the model file into the Inference Engine plug-in
</ul></ul>

#### 3. Close the source file

#### 4. Build the sample application

 	make

#### 5. Run the pedestrian detection sample application to use the Inference Engine on a video
The below command runs the application using the following parameters: 
<ul><ul>
		<li> number of frames to process (-fr)
		<li> location of the optimized deep-learning model (-m)
		<li> target device (CPU or GPU) to be used for inference (-d)
		<li> data labels list location (-l)
	</ul></ul>

	./IEobjectdetection -i opt/intel/tutorials/cvsdk/videos/vtest.avi -fr 200 -m artifacts/VGG_VOC0712_SSD_300x300_deploy/VGG_VOC0712_SSD_300x300_deploy.xml -d CPU -l pascal_voc_classes.txt


You should see a video play with people walking across and red bounding boxes around them. You should also see the output in the console showing the objects found and the confidence level. The higher the confidence level, the more likely the model is correctly identifying and drawing bounding boxes around pedestrians in the video. (for example: 0.83 is more confident than 0.23)

> **Note:** If you get an error related to "undefined reference to 'google::FlagRegisterer...", try uninstalling libgflags-dev: sudo apt-get remove libgflags-dev

<br>

### \(Optional) Explore using different parameters to see how they affect the results

#### 1. *define some suggested activities here and describe what should happen/what it means*

<br>
<br>

***

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

- [CV SDK landing page (IDZ)](https://software.intel.com/en-us/computer-vision-sdk?cid=sem43700020075377675&intel_term=computer+vision+sdk&gclid=CjwKCAiA9f7QBRBpEiwApLGUit1KXgtbu46anzhcsxJVBltKW-JOxPzucCmBxVDZwI_1H4FYgQZ-3RoC96sQAvD_BwE&gclsrc=aw.ds)
- [Developer guide](https://software.intel.com/en-us/cvsdk-inference-engine-apiref)
- [API references](https://software.intel.com/en-us/inference-engine-devguide)

<br>
<br>

***

<br>


###### OpenCL is a trademark of Apple Inc. used by permission by Khronos   
###### Intel is a registered trademark of Intel Corporation 
###### &ast;Other brands and names may be the property of others
