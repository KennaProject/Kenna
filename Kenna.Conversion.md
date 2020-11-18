# Kenna.Conversion
## Introduction
Kenna conversion is part of the Kenna package. The responsibility of the Kenna Conversion project is to facilitate the conversion process from a TensorFlow AI model to utility graphs.
## Conversion Overview
The main conversion consists of distinct sections.
1. Input - The input to the conversion process is a TensorFlow model.pb file.
2. Conversion - The data from the model.pb file together with a configurable template is used during the conversion process.
3. Output - The output contains the utility graphs inferred from the trained AI.
### TensorFlow
TensorFlow is used in different stages during the Kenna process. The first stage is to train an AI model. The saved AI model is contained in the model.pb file after training has completed. Using the file as input to the Kenna Conversion, TensorFlow.Net is then used to load the model into memory. Loading the complete TensorFlow program for the trained AI model gives us the ability to be able to run inference against the model to defer our utility graphs based on the configurable training parameters for the utility graphs.
## Conversion Input
Alongside the model.pb file provided, a list of Observation model's needs to be supplied to describe the AI observations found in the model.pb file.
``` C# 
public class ObservationModel
{
    /// <summary>
    /// The observation number correlating to the position of the
    /// observation in the saved AI model.
    /// </summary>
    public int ObservationIndex { get; set; }

    /// <summary>
    /// Describes the observation purpose. This is a helper
    /// property to assist during testing.
    /// </summary>
    public string Description { get; set; }

    /// <summary>
    /// Minimum value of the observation plain
    /// </summary>
    public decimal Minimum { get; set; }

    /// <summary>
    /// Maximum value of the observation plain
    /// </summary>
    public decimal Maximum { get; set; }

    /// <summary>
    /// The resolution into where the minimum and maximum
    /// observation plain ranges are dealt in. This will
    /// ultimately be the number of nodes in the 2D plain for the observation
    /// </summary>
    /// <remarks>
    /// If the Minimum is 0 and the Maximum is 100 and the Resolution is 50. The
    /// observation plain will be in multiples of 2. Eg: 2,4,5...48,50.
    /// </remarks>
    public int Resolution { get; set; }

    /// <summary>
    /// The normalizer value is used to calibrate the input value of the observation
    /// according to the trained model.
    /// </summary>
    public decimal NormalizerValue { get; set; }

    /// <summary>
    /// The equalizer value used as input when running inference graphs of other observations.
    /// </summary>
    public float EqualizerValue { get; set; }

    /// <summary>
    /// The input steps based of the (Maximum - Minimum) / Resolution;
    /// </summary>
    public decimal Step => (Maximum - Minimum) / Resolution;

    public ObservationModel()
    {
        //Set defaults
        EqualizerValue = 1;
        NormalizerValue = 1;
    }
}
```
Additionally, a list of action models needs to be supplied. The ActionModel represents the action output that is the possible outcomes of the AI model.
``` C#
/// <summary>
/// Represents the action number and the weight applied
/// </summary>
public class ActionModel
{
    /// <summary>
    /// The action number correlating to the position of the output
    /// in the saved AI model.
    /// </summary>
    public int Index { get; set; }

    /// <summary>
    /// A configurable weight an action should carry when running inference.
    /// </summary>
    /// <remarks>1 is 100%</remarks>
    public float Weight { get; set; }
}
```
## Conversion process
### Example
* 3 Observations
    1. Health (0-10), Resolution: 5
    2. Distance(1-5), Resolution: 5
    3. State(0-1), Resolution: 1
* 2 Actions
    1. Run away
    2. Run towards
1. Running inference requires to be able to get the avg impact all other observations has on a observation for each action independently. The inference graph will look as follow for the health observation.
### The steps for approximation are as follows.
1.	Calculate the average on x_n as x_avg
2.	Calculate standard deviation for x_n as x_stdev
3.	For each point that is further than p from x_stdev, remove that point and repeat steps 1-3.
4.	Stop when all points are within acceptable parameters or only 80% of points are left.
5.	Calculate final x_avg and x_stdev.
6.	Set x_n as x_avg. 

#### Health (Subset)
| Health | Distance | State | Run away | Run towards |  
|--------|----------|-------|----------|-------------|
| 0      | 1        | 0     | 0.56     | 0.66        |  
| 0      | 2        | 0     | 0.70     | 0.50        |  
| 0      | 3        | 0     | 0.80     | 0.40        |  
| 0      | 4        | 0     | 0.90     | 0.30        |  
| 0      | 5        | 0     | 0.98     | 0.20        |  
| 0      | 1        | 0     | 0.56     | 0.66        |  
| 0      | 1        | 1     | 0.90     | 0.40        |  
|        |          |  Avg  | 77,14    | 44.57       |  
| 1      | 1        | 0     | 0.66     | 0.56        |  
| 1      | 2        | 0     | 0.80     | 0.40        |  
| 1      | 3        | 0     | 0.90     | 0.30        |  
| 1      | 4        | 0     | 0.98     | 0.20        |  
| 1      | 5        | 0     | 0.80     | 0.33        |  
| 1      | 1        | 0     | 0.46     | 0.76        |  
| 1      | 1        | 1     | 0.80     | 0.50        |  
|        |          | Avg   | 77.14    | 43.57       |  

## Output
The output of the conversion process results in a dictionary of actions for each observation model a utility graph each.
| Action | Observation | Utility Graph | 
|--------|-------------|---------------|  
| 0      | Health      | 6 nodes       |  
| 1      | Health      | 6 nodes       |  
| 0      | Distance    | 6 nodes       |  
| 1      | Distance    | 6 nodes       |  
| 0      | State       | 2 nodes       |  
| 1      | Health      | 2 nodes       |  
Inferred utility graph node
``` C#
public class Inferred
{
    /// <summary>
    /// Reference to the output action
    /// </summary>
    public int ActionRef { get; set; }

    /// <summary>
    /// X-value
    /// </summary>
    public float InputValue { get; set; }

    /// <summary>
    /// Calculated Y -value
    /// </summary>
    public float AverageProbability => AverageProbabilityRef
                                        .Average(x => x.Value);

    /// <summary>
    /// AverageProbability per referred observation
    /// </summary>
    public ICollection<KeyValuePair<int, float>> AverageProbabilityRef 
    { get; set; }

    public Confidence Confidence => new Confidence
    {
        PointsRemoved = (int)ConfidenceRef.Average(x=>x.Value.PointsRemoved),
        StandardDeviation =
        ConfidenceRef.Average(x=>x.Value.StandardDeviation)
    };

    public ICollection<KeyValuePair<int, Confidence>> ConfidenceRef 
    { get; set; }
}
```
## Dependencies
* SciSharp.TensorFlow.Redist - https://github.com/SciSharp/TensorFlow.NET/tree/master/src/SciSharp.TensorFlow.Redist
* TensorFlow.NET - https://github.com/SciSharp/TensorFlow.NET