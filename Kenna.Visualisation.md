# Scenario Editor Overview #
The purpose of the scenario editor window is to assist users in creating their scenario from scratch. The scenario can be seen as wizard, guiding the user from the setting of the scene to the YAML configuration. The window is created through the EditorWindow class, using the UnityEditor library.
## General Feel of Editor ##

![image.png](/.attachments/image-61e4b703-00a6-4810-95c0-c0a618bac371.png)
The window is found in the Kenna section in the burger menu. The window is well descriptive in that the user will know exactly what to do in each page of the editor.
The user will need to progress from the Area of Interest page until they have completed the Conversion section.

### Agents Tab ###
This tab allows for the user to spawn their prefabs into the scene as Agents. Certain requirements are involved regarding if the prefab has a brain or not. From there, the user is able to customise their agent/s.

### Areas of Interests Tab ###
This tab allows for the user to spawn items of interest (as prefabs) into their playing plane. Given that the checkbox is ticked, the item spawned is placed into the agent's list of objects that it is able to see.

### Tensorflow Integration Tab ### 
This tab allows for the user to configure hyper-parameters for ML-Agent training. 

### Conversion Tab ###
This tab allows for the user to customise and set up their observation parameters for the conversion utility graphs.

## General Methods for the Editor ##
The creation of the editor makes use of the following general methods:

### OnEnable() ###
The method, OnEnable() is called when the objects/script instances are created. The method does the following for the editor:

•	Sets the skin for the editor (how the editor looks like)
•	Sets the animation for the editor 
•	Creates instances 

### OnGUI() ###
The method, OnGUI(), is called for rendering and handling GUI-based events. It is the function that is called several times per frame (which means that it is in a constant loop of being called). In this case, the editor makes use of the function to draw out all the controls of the window.

### Init() ###
The method, Init(), is a custom method that instantiates the editor window, places it underneath an option in the burger menu and allows you to set certain preferences on it (such as size and where it should pop up on the screen).

