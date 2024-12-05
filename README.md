# Landis7_setup
This document is working on how to successfully compile on great lakes.

## Download the example .sif file
(I suggest to complete this step on Great Lakes, and then transferring the sandbox folder back to my personal computer to add the extension. Since I used to try to do this step on
my personal computer, but it failed to turn the .sif file back to the sandbox folder. Therefore, I suggest to complete this step on Great Lakes. However, you are feel free on try this step on your
personal computer.)

I will first download the example .sif file that the developer provided to us, the link is https://github.com/Klemet/LANDIS-II-Apptainer/releases/tag/Core_v7_v1. Then transferring the example file to great lakes. (you can try this step on your personal computer).

If you are on the great lakes, please run the following command to load the singularity
```cmd
module load singularity
```

Then, the next step is to turn the example .sif file back to the sandbox environment
```cmd
singularity build --sandbox Lsandbox ubuntuLANDIS_Corev7_v1.sif
```
***Note***: 'Lsandbox' is the sandbox folder name after you turn the .sif file back to the sandbox folder. 'ubuntuLANDIS_Corev7_v1.sif' is the example .sif file name.

If you complete the step above on Great Lakes, please run the following command to transfer your sandbox folder back to your personal computer
```cmd
rsync -avz uniquename@greatlakes.arc-ts.umich.edu:/path/to/remote/file /path/to/local/destination
```


## Install Apptainer on your personal computer
If you didn't install the apptainer on your personal computer, please follow the instructions [here](https://apptainer.org/docs/user/latest/quick_start.html#installation) to install apptainer on your personal computer.

## Install the Extension
Please note that the example file had installed a lot of extension.
![image](https://github.com/user-attachments/assets/89ffa4e7-616d-431d-af6c-2b4ddf2d8608)

Our first step is to get into the sandbox environment, please run the following command
```cmd
apptainer shell --fakeroot --writable ubuntuLANDIS/
```

***Note***: 'ubuntuLANDIS' here is the sandbox folder name.

Then, go to the LANDIS_Linux folder inside the sandbox environment
```cmd
cd ubuntuLANDIS/bin/LANDIS_Linux/
```
Please go to [here](https://www.landis-ii.org/extensions) to check the extension that you may need to install. I will use biomass_succession as example to show how to install the extension.
```cmd
git clone https://github.com/LANDIS-II-Foundation/Extension-Biomass-Succession
```
The next step is to go into the extension folder that you just clones and modify the .csproj file(it is usually located in the src file)

```cmd
cd Extension-Biomass-Succession/src
```
Then please use 'nano' to modify the .csproj file, in this biomass_succession example
```cmd
nano biomass-succession.csproj
```
1. Add the following line inside the <PropertyGroup> ... </PropertyGroup> tags : <AppendTargetFrameworkToOutputPath>false</AppendTargetFrameworkToOutputPath>

2. Add the following lines inside the <Project> ... </Projects> tags, that will tell dotnet where to place the compiled .dll :
```cmd
  <PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Release|AnyCPU'">
   <OutputPath>..\..\build\extensions</OutputPath>
  </PropertyGroup>
```
3. In the <HintPath> tags of the <ItemGroup>, replace any path to the different dll with
```cmd
..\..\build\extensions\{Name of the dll}.dll.
```
This will tell dotnet where to find the support dll we downloaded earlier, and which are needed for the compilation.

![image](https://github.com/user-attachments/assets/a41cbf85-399a-44e3-8b4f-15a6005be664)

The place that I use red pen point out should be complete ***same*** as the command on the graph.

Once you've done this, use dotnet build from inside the folder where the .csproj file is (e.g. do cd /bin/LANDIS_Linux/Extension-Biomass-Succession/src and then dotnetbuild). This test the compiling to see if there are any errors. Then, use dotnet build -c Release to finish the compiling. End up by checking if the .dll of the extension is correctly inside /bin/LANDIS_Linux/build/extensions

***Note***: The step above may fail a lot of times, since you have to try the different versions to find the version that fit the Landis-v7 model. This step is frustrating, but it is unavoidable. You have to check the git log and use the git checkout to go back to the previous version to check if this version works or not. Every time you go to the different version extension, you have to re-edit the .csproj file again.

The next step is to edit the extension.xml file, which is located in the path /root/ubuntuLANDIS/bin/LANDIS_Linux/build/extensions. You have to add the information for your new extension here. The information that you need to add here is on Extension/deploy/installer folder. For example, in the biomass_succession extension, you can find the information that you need to add to extension.xml on 'bin/LANDIS_Linux/Extension-Biomass-Succession/deploy/installer.' ***Note***: that information is on .txt file, not .iss file.


I highly recommend that you test if the extensions function properly right now, as you are still inside the sandbox environment. You better see if there are errors right now, as you're still able to correct them. Every extension folder cloned from Github often contains a set of test files; I recommend you run the test scenario that goes with them. To run it, go in the folder with the test scenario, and then use dotnet /bin/LANDIS_Linux/build/Release/Landis.Console.dll {nameOftheScenarioMainFile}.txt. LANDIS-II should launch the simulation with your test scenario properly at this point. If something doesn't work, be certain that the path to Landis.Console.dll is correct in your command.

If everything installed successfully, please exit the sandbox environment with command 'exit.'


## Build .sif File
Run the following command to build the .sif file
```cmd
apptainer build ubuntuLANDIS.sif ubuntuLANDIS
```
***Note***: 'ubuntuLANDIS.sif' is the name of your new .sif file, you can change its name. 'ubuntuLANDIS' is the sandbox environment folder.

## Compile on Great Lakes
Then please transfer your .sif file to the Great Lakes. Please run the following command to make the .sif file work

```cmd
 singularity exec ../example1.sif /bin/.dotnet/dotnet /bin/LANDIS_Linux/build/Release/Landis.Console.dll scenario.txt
```

***Note***:

1. ../example1.sif is the location of my .sif file's location, you are feel free to change it to your location.

2. scenario.txt is the location of the text file that you want to process, you are also feel free to change it

3. Anything else please don't change it.

## Reference
Feel free to check the [instruction](https://github.com/Klemet/LANDIS-II-Apptainer?tab=readme-ov-file#%EF%B8%8F-creating-your-own-apptainer-file) that the Landis developer write







