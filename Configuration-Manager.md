Kenna Provides a Configuration tool with which Hyperparameters for the ML-agents reinforcement learning algorithms can be set. Also, with this tool the existing agent scripts along with the rewards scripts can be seen. 

# Applying Machine Learning to Kenna.
It is a requirement/prerequisite to have the ML-agents package installed. [here](https://github.com/Unity-Technologies/ml-agents)

Kenna uses a machine learning engine called Unity ML-Agents to train the reinforcement learning models. More specifically the package has a python API and command line interface with which everything from training to control can be initiated. 

All documentation for ML-agents can be found [here](https://github.com/Unity-Technologies/ml-agents/tree/master/docs). With a brief overview available [here](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/ML-Agents-Overview.md#learning-from-vector-observations)

# Hyperparameters
Each Agent you train has a set of specific ‘Hyperparameters’ which defines how the reinforcement algorithms will perform. [here](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Training-Configuration-File.md)
The goal of the algorithm is to prefect a ‘policy’ which defines how the actor of the trained neural network would behave, the most preferred case would be to use the proximal policy optimization technique which is more forgiving to a broader set of scenarios. 

The hyperparameters for any ML-agents object needs to be contained in a YAML object file (separate). The actual YAML object contains the collection of Hyperparameters applicable to a specific scenario. 

![image.png](/.attachments/image-c876c0e7-c1a5-4a8f-890b-b1e9c319771f.png)

# Behaviour Parameters
It is important to understand the behaviour parameters module within the ML-agents package. This module briefly is responsible for configuring set operation specifications for the ML-agents engine to enforce during training. 

This is where the agent’s hyperparameter object needs to be selected, The Vector space the scenario will operate in and a bunch of other configurations. 
The default agent of the Kenna system automatically configures the Behaviour parameters module, but for more specific changes configuration with custom agents or direct changes on the component inspector is required. 

![image.png](/.attachments/image-c3c4015e-8211-414c-ba94-e688f1c0872c.png)


# Discrete action spaces (vector actions) 
The discrete action space describes an environment in which an artificially intelligent actor would act based on an interval-based or ‘discrete’ action space. This does not only describe the finite actions available to the actor, but also the amount of time in which these actions can be performed. 

Kenna operates in the discrete action space as the most suitable action to take is determined by its utility graphing system. [here](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Learning-Environment-Design-Agents.md#discrete-action-space)

# Rewards
Mostly the agent is controlled by a certain policy (PPO). This policy can be adjusted via the reward system the agent utilizes.

Rewards may determine future decisions and can adjust utility graphs. There are three types of rewards namely, extrinsic rewards which are based on the agent's environment interaction, curiosity rewards which are based on the intuitive nature of the agent within a defined action space, and lastly General adversarial imitation learning (GAIL) rewards which are based on the observation of how the agent's behaviour differs from its training input data. 

To add a reward simply access the agent end point in the Rewards script using the AddReward() or SetReward() [here](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Learning-Environment-Design-Agents.md#rewards)

# Creating an Agent.
There can be two ways to create and use an agent script. 
1.	Manually remove default existing agent script and add a custom agent script. 
2.	Use the default agent that is imported by default onto the agent game object when constructing the scene with the ‘Kenna.Visualization’ tool. 

# Custom Agents / Editing Default Agents 
To create a custom Agent script, it is important to use the Kenna.AI endpoint in the correct manner. The default agent already has a set few configuration and already has the endpoint set up.

Access the Kenna.AI endpoint by referencing the train brain component.

![image.png](/.attachments/image-a1faaa8b-d14f-4b7f-9978-457a53f6d608.png)

An Example would be setting the active task as follows:

![image.png](/.attachments/image-3ef8bb01-242c-4937-937e-b6fa015f2490.png)

