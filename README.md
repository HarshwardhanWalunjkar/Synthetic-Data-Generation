
The Project deals with using the Blainder addon in Script form in blender to Sythetically generate datasets in any desired format. 
The folder mars_dataset includes an example of the dataset that might be generated on running the bpy script.
Note: The blainder addon implementation here was done in its most stable version on Blender 3.3 LTS, preferable no problems should arise
till blender 3.9 but may cause unexpected problems.



We work with a rough example of mars type terrain as usually such kind of data is scarce while building rovers that need point cloud data 
to train on.




![image](https://github.com/user-attachments/assets/26088419-bced-4a64-9533-947e2b138a7b)



This is the bpy script of blender that is being run:



```ruby
import bpy
import math
import random
import range_scanner

def random_camera_pos():
    camera_obj = bpy.context.scene.objects["Camera"]
    camera_obj.location[0] = random.uniform(-1,-1)*2
    camera_obj.location[1] = random.uniform(-1,1)*4
    camera_obj.rotation_euler[0] = random.uniform(-1,1)*(math.pi/180)*16
    camera_obj.rotation_euler[1] = random.uniform(-1,1)*(math.pi/180)*16
    
def generate_point_cloud(output_path, file_name):
    
    
    range_scanner.ui.user_interface.scan_static(
    
    bpy.context, 

    scannerObject=bpy.context.scene.objects["Camera"],

    resolutionX=100, fovX=60, resolutionY=100, fovY=60, resolutionPercentage=100,

    reflectivityLower=0.0, distanceLower=0.0, reflectivityUpper=0.0, distanceUpper=99999.9, maxReflectionDepth=10,
    
    enableAnimation=False, frameStart=1, frameEnd=1, frameStep=1, frameRate=1,

    addNoise=False, noiseType='gaussian', mu=0.0, sigma=0.01, noiseAbsoluteOffset=0.0, noiseRelativeOffset=0.0,

    simulateRain=False, rainfallRate=0.0, 

    addMesh=True,

    exportLAS=True, exportHDF=False, exportCSV=False, exportSingleFrames=False, exportPLY=False,
    exportRenderedImage=True, exportSegmentedImage=False, exportPascalVoc=False, exportDepthmap=False, depthMinDistance=0.0, depthMaxDistance=100.0, 
    dataFilePath=output_path, dataFileName=file_name,
    
    debugLines=False, debugOutput=False, outputProgress=True, measureTime=False, singleRay=False, destinationObject=None, targetObject=None

)  
    
random_camera_pos()
obj_renders_per_split = [('training', 10), ('validation', 5), ('testing', 3)]
output_path = "C:/Users/Harshwardhan/Desktop/CGS/Synthetic Data Generation/mars_dataset"

for split_name, split_count in obj_renders_per_split:
    folder_path = str(output_path+"/"+split_name)
    print(f"exporting the split {split_name}")
    for i in range(split_count):
        random_camera_pos()
        file_name = str(split_name+"_"+str(i))
        generate_point_cloud(folder_path,file_name)
```



Here the random_camera_pos() function randomly positions the camera in the scene appropriately so that the scanner stays within the range.



In this example we are generating out point clouds, so we set the exportLAS parameter to True within the lidar scanner's static scan function and call it within
the generate_point_cloud() function.



Note: To get any other type of data out of the scanner from the addon we simply alter the parameters within the code.



Here is an example of the generated point cloud withing the training set folder which we can visualize using something like Cloud Compare:



![image](https://github.com/user-attachments/assets/437dcf7d-2b3f-4a06-9ded-e005b9eb9498)
