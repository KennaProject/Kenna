# Utility AI Overview #
The utility AI engine determines an agent's actions by weighing the utility (importance/usefulness/highest resulting outcome) of all available actions and choosing the one with the highest value.  
## Components ##
### Action Task ###
An action task is a single, or series of actions an agent can perform. This could be as simple as *walking forwards* or as complicated as *get ready for work*. Action tasks interface with virtual controller to direct the agent to perform the physical actions in the environment.
### Utility Graph ###
A utility graph is a normalized (0.0-1.0) function graph (each value in the domain corresponds to only one value in the range) that describes when an action should be chosen. Each graph's values are based on a single, measurable observation's values as the domain and a common factor (often time) as the range.
Each action task can contain one or more utility graphs.
### Sensors ###
Sensors are used to collect the measurable observations for the utility graphs. Sensors can be visual such as a sight sensor, auditory such as audio or disturbance sensors, or meta such as property value sensors (unit statistics) such as health, speed, range, or type of object.
### Blackboard ###
After sensors collect the data within an agent's environment, the data is then stored in the agent's blackboard. This can be seen as an agent's memory or the state of the current environment relative to the agent.  
Utility graphs interface with blackboards to pull the data required for utility calculations.
### Domain ###
The domain contains all the agent's possible actions and has the responsibility of acting as the interface for action functionality and decision making.
### Brain ###
The brain is the top most level of the utility engine. It is the component added to an agent within the Unity editor. The brain contains a domain, blackboard, unit statistics, and all sensors utilized by the agent.
# General Usage #
The utility engine can be used as a standalone AI engine to depict simple or highly complex behaviors. Utility engines are often favored over the likes of behavior trees, or state machines, as they offer a non-binary decision making solution. This is great if the behavior of an agent should be less predictable, less exploitable and more creative while still being easily manageable and believable.
# Getting Started #
Included in the package are the base functionality of all the components mentioned above, including two examples of sensors. The first example is a sight sensor, used to detect other objects in the scene based on predefined configurations and runtime variables. The second is a stats sensor, used to pull the agent's unit statistics into the blackboard. These two examples also include container types for their respective resulting data types i.e. SightTarget and StatResult.
Use these examples to create new sensors for you agent.

Except for the brain, all instantiable items are ScriptableObjects and should be instanced in editor time through the Kenna->AI context menu. Once an instance is created it can be configured and assigned to the appropriate fields of higher components.

1. To start creating you custom behavior, create a class and implement the IOperator interface. This script will control the physical actions of your action (which you will create next) through the use of a virtual controller.
The operator arguments are all the parameters the operator will need to perform the action.

2. Create a class and inherit from ActionTask. Use the previously created operator type for this new action's operator. Implement conditional checks, argument assignments and set the PerformAction method to call the operator's PerformAction method.
Instantiate an instance of the created ActionTask and set the required parameters.

3. Either create and instance of an existing sensor or following one of the existing examples, create a desired sensor. Instantiate an instance of the desired sensor(s) and set the required parameters.

4. Create a class and inherit from ValueNormalizer. Override the GetNormalizedValue method and implement an algorithm for normalizing the sensor's, created in the previous step, data. This object will be used to normalize the sensor's data for the utility graph.

5. Create an instance of the Utility ScriptableObject and assign the value normalize created in the previous step to the appropriate field. Assign the utility instance to the utility field of the ActionTask created in step 2.

6. Create an instance of the Domain and Blackboard ScriptableObjects. Assign the ActionTask created in step 2 to the domain.

7. Add a UtilityBrain component to the agent GameObject and assign the Blackboard,  Domain and sensors to the appropriate fields.

8. Setup the virtual controller and any other components required for the agent as per your game requirements.
# Utility Conversion #
1. To use utility graphs created from ML-Agent trained neural networks, follow the steps as outlined in the Scenario Editor and TensorFlow Integration documentation. Also read through the Kenna.Conversion documentation to better understand how conversion are done and the inherit limitations associated.

2. After you have generated a scenario, configured the required ML-Agent environments, implemented the required agent training, and trained the agent, a resulting neural network model will be generated.

3. Create and instance of the GraphConversion ScriptableObject. Using the scenario editor window assign the appropriate domain and the newly created (or existing) graph conversion. Locate the neural network frozen graph file and select it. Once the conversion is done, the graph conversion instance will contain the resulting utility graphs.

4. Copy the graphs from the graph conversion to the appropriate action's utility instance.

5. Create an agent that will utilize a UtilityBrain and assign the appropriate domain and other fields.
The agent is now ready to be tested.

Each action has some parameters that will influence how the utility calculations are made. Change the parameters and graph nodes until the desired behavior is met.