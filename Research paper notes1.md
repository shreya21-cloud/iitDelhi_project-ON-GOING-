Grasp-CLIP: Pick Up What You Want - Notes
1. Problem Statement

Traditional robot grasping systems can generate grasp poses for objects, but they cannot easily understand commands like:

"Pick up the apple"

Challenges:

Cannot identify specific target objects from text instructions.
Slow inference speed.
High model complexity.
Low grasp precision in cluttered scenes.

Goal:

Enable robots to grasp a specific object using natural language commands.
Improve speed, precision, and generalization.

2. Main Idea of Grasp-CLIP

Grasp-CLIP combines:

GSNet (6-DoF grasp generation)
CLIP (Image + Text understanding)

Input Modalities:

Point Cloud
RGB Image
Text Instruction

Example:

Text:

Pick up the red bell pepper

Output: Only grasp poses belonging to the red bell pepper.


3. What is GSNet?
GSNet is the base grasping network.

Functions:
Takes point cloud as input.
Generates 1024 candidate grasp points.
Predicts:
Approach direction
Grasp rotation
Grasp depth
Gripper width
Grasp score

Limitation:

Does not know which object is the target object.

4. Why CLIP is Used?

CLIP learns relationships between:

Images
Text

Benefits:

Understands object names.
Understands attributes like:
Color
Shape
Size
Helps generalize to unseen objects.

Example:Training:

Green Bell Pepper

Testing:

Yellow Bell Pepper

CLIP can still understand the relationship.

5. Grasp-CLIP Architecture

 
   RGB Image
      ↓
CLIP Image Encoder
      ↓
Image Features

Text Instruction
      ↓
CLIP Text Encoder
      ↓
Text Features

Point Cloud
      ↓
GSNet
      ↓
Point Features

Image + Text Features
      ↓
Dense Fusion Module
      ↓
Selection Module
      ↓
Target Object Grasp Poses


6. Module 1: Image-Text Fusion Module
   Purpose:

Combine image information with text instructions.

Process:

RGB image → CLIP Image Encoder.
Text command → CLIP Text Encoder.
Features fused using element-wise multiplication.

Output:

Text-aware image features.

Example:

Image contains:

Apple
Book
Instruction:

Pick up the apple

Image features become focused on the apple.

7. Module 2: Dense Fusion Module
Purpose:

Combine visual information with point cloud information.

Problem:

Point cloud alone cannot distinguish similar objects.

Solution:

Fuse:
Point cloud features
Image features

Inspired by:

DenseFusion

Output:

Multimodal features.

Benefit:

Better object recognition.

8.Module 3: Selection Module
Purpose:

Select grasp points belonging only to the target object.

Input:

Multimodal features
Text features

Process:

Feature multiplication.
Feature addition.
MLP classification.

Output:

For each grasp point:
For each grasp point:

1 → Target Object
0 → Not Target Object

Repeated:

4 times

Loss Function:

Focal Loss

Reason:

Handles class imbalance effectively.


9. Dataset Creation

Simulation Platform:

NVIDIA Isaac Sim

Dataset Details:

Item	Value
Objects	266
Training Scenes	142
Objects per Scene	10
Images per Scene	255
Total Images	36,210

Additional:
100 unseen objects used for testing.

0. Generalization Capability
1. Tested on:

Unseen objects
New colors
New descriptions

Examples:

Commands:

Pick up the yellow bell pepper
Pick up the red bell pepper
Pick up the pencil case

Result:

Successfully distinguishes objects.

Reason:

CLIP provides strong image-text alignment.

10. Generalization Capability

Tested on:

Unseen objects
New colors
New descriptions

Examples:

Commands:

Pick up the yellow bell pepper
Pick up the red bell pepper
Pick up the pencil case

Result:

Successfully distinguishes objects.

Reason:

CLIP provides strong image-text alignment.

11. Performance Comparison

Compared With:

CLIPort
2D grasping only.
Higher parameters.
Slower.
Grounded SAM + GSNet
Two-stage pipeline.
Uses segmentation first.
More computation.

Grasp-CLIP

Advantages:

End-to-End.
Faster.
Fewer parameters.
Better grasp precision.

Key Results:

Method	Params	Time
CLIPort	0.21B	0.401s
Grounded SAM + GSNet	0.83B	0.625s
Grasp-CLIP	0.18B	0.238s
