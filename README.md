# Fast Odometry and Scene Flow from RGB-D Cameras based on Geometric Clustering

This code contains an algorithm to estimate visual odometry and scene flow with RGB-D cameras.
It has been tested on Windows 10 (Visual Studio 2013 and 2017) and Ubuntu 16.04.

If you use it in your research, please cite the following paper:  
* M. Jaimez, C. Kerl, J. Gonzalez-Jimenez and D. Cremers, **Fast Odometry and Scene Flow from RGB-D Cameras based on Geometric Clustering**, ICRA 2017.  

More info and video in:
http://mapir.isa.uma.es/work/Joint-VO-SF

## Note from Zhaoyang:

To work with Ubuntu 16.04 system built MRPT library, there are quite a few changes I have made to the original code. If you want to use the original author distribution for reproducing the results in the original paper, I do not suggest you use this one. I always use SINTEL images to verify its correctness. Here are the changes I have made:

- An executable file to work with SINTEL dataset, including file parsing and pre-processing.
- Changes w.r.t. MRPT 1.9 interfact, to ensure it can correctly compile and be consistent with MRPT data type and API.


## 1. Configuration and Generation

A CMakeLists.txt file is included to detect external dependencies and generate the project automatically. OpenCV, MRPT, OpenNI2 and Intel TBB are required, you can get them here:
- OpenCV: http://opencv.org/
- MRPT: http://www.mrpt.org/
- OpenNI2: https://structure.io/openni
- Intel TBB: https://www.threadingbuildingblocks.org/
 
The project builds a library embedding the main algorithm as well as other classes to read data from files/datasets or directly from an RGB-D camera. Moreover, it includes 4 different applications to test it. 



## 2. Usage

You can compile four different executables. They all incorporate a 3D visualization that can be used to interact with them. Please read the instructions at the beginning of each main file.

**1) VO-SF-Camera:** It allows to test the algorithm online with an RGB-D camera. Only Asus and PrimeSense cameras are supported in the current version, but you can easily modify it to work with other camera models. Just edit the class "RGBD_Camera" in the files "camera.h" and "camera.cpp". 

**2) VO-SF-Datasets:** To test the algorithm with the TUM RGB-D datasets. However, it is prepared to load these datasets as rawlog files (MRPT format), and therefore you should download them from here:  
http://www.mrpt.org/Collection_of_Kinect_RGBD_datasets_with_ground_truth_CVPR_TUM_2011

You can save the estimated trajectory and evaluate it directly here:  
http://vision.in.tum.de/data/datasets/rgbd-dataset/online_evaluation

**3) VO-SF-ImagePair:** This is a simple application to test the algorithm for a single pair of RGB-D images (mostly useful to see the accuracy of the estimated scene flow). Set the folder where the images are contained in the main file. The image files are expected to have the following names: "depth0.png", "color0.png", "depth1.png" and "color1.png".

**4) VO-SF-ImageSeq:** To test the algorithm with pre-recorded image sequences. Set the folder where the images are contained in the main file. The image files are expected to have the following names:     
Depth sequence - "d0.png", "d1.png", "d2.png"...   
Color sequence - "i0.png", "i1.png", "i2.png"...   

You can set the first image you want to start with in the main file (initial value in "im_count").
You can also set a decimation factor with the variable "decimation".   
    
     
    

In all cases, the original image resolution must be VGA. There is a variable called "res_factor" at the top of the main files which can be set to 1 or 2. 
- If res_factor == 1, the image pyramid is built starting from the full-resolution images (although the solver will only solve until the resolution set by the class variables "rows" and "cols", by default 240 x 320).
- If res_factor == 2, the image pyramid is built starting from QVGA resolution, saving some time but providing "less smooth" images.

Images are expected to be saved with the following format:

- color images - 8 bit in PNG. Resolution: VGA   
               Clue: Use cv::Mat image_name(height, width, CV_8U) and
                         cv::imwrite(filename, image_name) to store them.
					
- depth images - 16 bit monochrome in PNG, scaled by 5000. Resolution: VGA   
               Clue: Use cv::Mat image_name(height, width, CV_16U) and
                     cv::imwrite(filename, image_name) to store them.
                     Multiply the real depth by 5000.

       
The executables do not take any command line argument. If you want to run them from scripts modify them at your convenience.
      
      
The provided code is published under the General Public License Version 3 (GPL v3). More information can be found in the "GPL LICENSE.txt" also included in the repository.


 ## 3. Warnings!!

The method "flipHorizontal()" used for the visualizations is only available from MRPT 1.5.0 on. If you use a lower version please comment it before compiling (find it in "visualization.cpp"). When commented, the scene will show mirrored images in the 2D viewports.   

The 2D viewports embedded in the 3D visualization might fail for some specific configurations. If that is your case, try to go to the corresponding method in "visualization.cpp" and change their initial size to something larger than the image to be plot, for example:
vp_labels->setViewportPosition(..., ..., 321, 241);  (Images displayed are by default 320 x 240)

 
