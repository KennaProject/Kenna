
``` C# 
/*-------------------------------------------------------------------------------------------------
  1. LAYOUT
 -------------------------------------------------------------------------------------------------
 
  1.1)  4 SPACES for indentation (don't use tabs).
  1.2)  Max line length = 130 characters.
  1.3)  Opening & closing parentheses on a new line.
  1.4)  Always prefer a layout that makes the code more readable.
 
 -------------------------------------------------------------------------------------------------
  2. NAMING CONVENTIONS
 -------------------------------------------------------------------------------------------------
 
  2.1) More or less based of https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/naming-guidelines
  2.2) Pascal Case (classes, structs , enums and methods and enum values)
  2.3) Camal Case (Fields + Properties, constants)
 
 -------------------------------------------------------------------------------------------------
  3. ACRONYMS
 -------------------------------------------------------------------------------------------------
   
  3.1 Try to avoid using acronyms/abbreviations where possible
      
 -------------------------------------------------------------------------------------------------
  4. GENERAL GUIDELINES
 -------------------------------------------------------------------------------------------------
  4.1 Public properties are searializable by default and displays on the editor. Except auto-properties
 *----------------------------------------------------------------------------------------------*/

using System.Collections.Generic;
using MLAgents;
using TMPro;
using UnityEngine; // only import namespaces that are used.

namespace Penguin.Scripts
{
    public class PenguinArea : MonoBehaviour
    {
        // General order of class members:
        // -------------------------------
        // properties (exposed in the editor should be the first in the class to be easily identified)
        // fields
        // constructors & finaliser
        // public methods
        // private methods
        
        [Tooltip("The agent inside the Area")] //Try to always use tooltips to explain the property to the user
        public PenguinAgent penguinAgent;

        [Tooltip("The baby penguin inside the area")]
        public GameObject penguinBaby;

        [Tooltip("The TextMeshPro text that shows the cumulative reward of the agent")]
        public TextMeshPro cumulativeRewardText;

        [Tooltip("Prefab of a live fish")]
        public Fish fishPrefab;

        private List<GameObject> fishList;

        /// <summary>
        /// The number of fish remaining
        /// </summary>
        public int FishRemaining => fishList.Count;

        /// <summary>
        /// Reset the area, including fish and penguin placement
        /// </summary>
        public void ResetArea()
        {
            RemoveAllFish();
            PlacePenguin();
            PlaceBaby();
            SpawnFish(4, Academy.Instance.FloatProperties.GetPropertyWithDefault("fish_speed", 0.5f));
        }

        /// <summary>
        /// Remove a specific fish from the area when it is eaten
        /// </summary>
        /// <param name="fishObject">The fish to remove</param>
        public void RemoveSpecificFish(GameObject fishObject)
        {
            fishList.Remove(fishObject);
            Destroy(fishObject);
        }

        /// <summary>
        /// Choose a random position on the X-Z plane within a partial donut shape
        /// </summary>
        /// <param name="center">The center of the donut</param>
        /// <param name="minAngle">Minimum angle of the wedge</param>
        /// <param name="maxAngle">Maximum angle of the wedge</param>
        /// <param name="minRadius">Minimum distance from the center</param>
        /// <param name="maxRadius">Maximum distance from the center</param>
        /// <returns>A position falling within the specified region</returns>
        public static Vector3 ChooseRandomPosition(Vector3 center, float minAngle, float maxAngle, float minRadius, float maxRadius)
        {
            float radius = minRadius;
            float angle  = minAngle;

            if (maxRadius > minRadius)
            {
                // Pick a random radiusChooseRandomPosition
                radius = UnityEngine.Random.Range(minRadius, maxRadius);
            }

            if (maxAngle > minAngle)
            {
                // Pick a random angle
                angle = UnityEngine.Random.Range(minAngle, maxAngle);
            }

            // Center position + forward vector rotated around the Y axis by "angle" degrees, multiplies by "radius"
            return center + Quaternion.Euler(0f, angle, 0f) * Vector3.forward * radius;
        }


        /// <summary>
        /// Remove all fish from the area
        /// </summary>
        private void RemoveAllFish()
        {
            if (fishList != null)
            {
                for (int i = 0; i < fishList.Count; i++)
                {
                    if (fishList[i] != null)
                    {
                        Destroy(fishList[i]);
                    }
                }
            }

            fishList = new List<GameObject>();
        }

        /// <summary>
        /// Place the penguin in the area
        /// </summary>
        private void PlacePenguin()
        {
            Rigidbody rigidbody = penguinAgent.GetComponent<Rigidbody>();

            rigidbody.velocity              = Vector3.zero;
            rigidbody.angularVelocity       = Vector3.zero;
            penguinAgent.transform.position = ChooseRandomPosition(transform.position, 0f, 360f, 0f, 9f) + Vector3.up * .5f;
            penguinAgent.transform.rotation = Quaternion.Euler(0f, UnityEngine.Random.Range(0f, 360f), 0f);
        }

        /// <summary>
        /// Place the baby in the area
        /// </summary>
        private void PlaceBaby()
        {
            Rigidbody rigidbody = penguinBaby.GetComponent<Rigidbody>();

            rigidbody.velocity             = Vector3.zero;
            rigidbody.angularVelocity      = Vector3.zero;
            penguinBaby.transform.position = ChooseRandomPosition(transform.position, -45f, 45f, 4f, 9f) + Vector3.up * .5f;
            penguinBaby.transform.rotation = Quaternion.Euler(0f, 180f, 0f);
        }

        /// <summary>
        /// Spawn some number of fish in the area and set their swim speed
        /// </summary>
        /// <param name="count">The number to spawn</param>
        /// <param name="fishSpeed">The swim speed</param>
        private void SpawnFish(int count, float fishSpeed)
        {
            for (int i = 0; i < count; i++)
            {
                // Spawn and place the fish
                GameObject fishObject = Instantiate<GameObject>(fishPrefab.gameObject, transform, true);
                fishObject.transform.position = ChooseRandomPosition(transform.position, 100f, 260f, 2f, 13f) + Vector3.up * .5f;
                fishObject.transform.rotation = Quaternion.Euler(0f, UnityEngine.Random.Range(0f, 360f), 0f);

                // Set the fish's parent to this area's transform

                // Keep track of the fish
                fishList.Add(fishObject);

                // Set the fish speed
                fishObject.GetComponent<Fish>().fishSpeed = fishSpeed;
            }

        }

        /// <summary>
        /// Called when the game starts
        /// </summary>
        private void Start()
        {
            ResetArea();
        }

        /// <summary>
        /// Called every frame
        /// </summary>
        private void Update()
        {
            // Update the cumulative reward text
            cumulativeRewardText.text = penguinAgent.GetCumulativeReward().ToString("0.00");
        }
    }
}


```
