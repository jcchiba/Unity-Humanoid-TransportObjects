# Purpose
With these scripts building on Final IK, you can make your character walk to an object, pick it up with both hands, transport it to a specified destination and place it there. The character will:
* grab the object in a specified manner, and hold it at a specified point and in a specified angle in front of it
* smoothly avoid obstacles while walking to the object and to its destination

![alt tag](https://github.com/mariusrubo/Unity-Humanoid-TransportObjects/blob/master/transport.jpg)

# Installation
## Prepare your character
* Download and import Final IK from Unity's AssetStore (90$)
* Attach the script "WalkToGoal.cs" from my repository "Unity-Humanoid-Walk-To-Goal" to your character. Make sure the character has an animator and the Locomotion controller.
* Attach the scripts "Full Body Biped IK" and "Interaction System" to your character.
* Create two empty transforms, name them "HoldPoint" and "HoldPointOriginal", make them children of your character and place them in front of the character where it should hold objects (e.g. at (0, 1.0, 0.4)).
* Attach the script "PickUp.cs" to your character. Drag your character to the field "Interaction System" and the two HoldPoints to their according places.
* Find both hands from your character in the hierarchy, and attach "Hand Poser.cs" to both of them. You do not need to fill in a transform for "PoseRoot".

## Prepare objects
* Make sure your object has a Rigidbody component and a box collider.
* Attach the script "Interaction Object" to your object. Add a weight curve for "Position Weight" that starts at 0, reaches 1 at 0.6sec and returns to 0 at 1.2sec. This means that the character will need 0.6sec to move its hands to the correct positions on the objects. You can change these values, if this is too fast or slow for you.
* Still on your object's "Interaction Object", also add 3 multipliers, all computing from Position Weight: One for "Rotation Weight" (value here: 1), one for "Poser Weight" (value: 1), one for "Reach" (value: 0.2). You do not need to insert anything in "Other Look At Target", "Other Targets" and "Position Offset".
* Still on your object's "Interaction Object", also set an Event "Pause" (not "Pickup"!), and set its time to half of the weight curve length you specified above (0.6sec in the example). See the image for how "Interaction Object" should look like.

![alt tag](https://github.com/mariusrubo/Unity-Humanoid-TransportObjects/blob/master/interactionObject.jpg)

* Copy both of your character's hands, make these copies children of your object (they will become invisible). Remove "Hand Poser" from the copies, instead attach "Interaction Target" (you will now see their bones again) and select the corresponding effector type. Move these hands' copies in the right position, and manipulate the bones' angles until they roughly resemble the posture in which your character should hold the object.
* Copy your object and name it something like "Object1Destination". This serves to visually indicate where your character should place the object. On this copy, delete everything you previously added to your object and has now been copied, too. Switch off the Mesh Renderer to make the copy invisible, but keep its box collider.  
* Don't specify your object or the transform it is placed on as obstacles in the Navigation Mesh, because this will keep the character from walking close enough to the object to grab it.

## Control transportation behavior from one center script
* Place the script "TransportInterface.cs" to any object in your scene. Insert your character, the objects and their destinations accordingly.
* Also define position and rotation for the HoldPoint individually for each object. This allows you to hold each object in a certain way (e.g. a guitar may be held in a different angle than a book). For now, you can just insert (0, 1, 0.4) for all positions and each object's rotation for all HoldPoint rotations.
* Press Play, click on the buttons in Game View.

## Fine-tune the character's hand posture and the HoldPoint
* You may have noticed that defining the hand posture with high precision is difficult, because when clicking on the hand copies attached to the object, you can only see their bones.
* To fine-tune the posture, first open TransportInterface.cs, find the line "if (cascade == 3)" and replace 3 with a larger number (e.g. 30). This will keep the character from walking towards the goal after it has grabbed the object.
* Play the scene and make the character grab the object by clicking on the according button. Then find the grabbed object in the hierarchy (it is now a child of the character!), click on its hand copies, and manipulate their bones. Your character will now move its actual hand accordingly, making it easy to see if the gesture looks natural. Also move the HoldPointOriginal, until you character holds the object at the right place.
* When you are satisfied with the posture, copy the hand copy while still in play mode, and note down the position and rotation of the hand copy.
* Stop play mode and replace the object's hand copy with the one you just copied. Set the position and rotation of he hand copy to the values you just noted down. 
* While your character is holding the object in Play Mode, you can also move and rotate the character's HoldPoint. When you are satisfied with the posture, note down the HoldPoints's position and rotation, stop Play Mode, and manually insert the values in "TransformInterface.cs".
* Don't forget to reset the line "if (cascade == 3)" in TransportInterface.cs.
* If you wish to use your object in another scene, it is best to make the object including the attached hands a prefab: to do this, just drag the object from the inspector to the project view. You can then insert the prefab in a new scene, with the attached hands in the correct place.

# Limitations
* While this approach of procedural grabbing is more flexible than the use of animations, your character will of course still be much less flexible compared to real humans. Most of all, your character will always grab the object in the specified way, and if the object is not placed the right way round, your character will bring itself in an absurd posture to grab it. For cube- or ball-shaped objects, Final IK offers solutions to correctly grab from all angles. For other objects, this is not easily realizable.
* In this example, the character does not actually grab the objects (i.e. open and close its hands at the right time). Instead, it simply brings its fingers into the right positions while moving its hands at the right place. If more realistic grabbing is important for you, you may want to play around with the Weight Curves in your object's "Interaction Object". 
* The picking-up procedure looks best if the object is positioned a little below the HoldPoint (e.g. on top of a table). If your character has to lift objects from the ground, some adjustments in Final IK's components may be needed to make this look natural. If your character has to get objects from a heightened position, adjust the Weight Curves on your object's "Interaction Object" to not make the object move through the table or shelf it is positioned on.
* When placing the object to its destination, it is simply moved along a line from its HoldPoint in front of the character to its destination. Therefore, putting objects onto a heightened place may cause problems (e.g. if your character puts it on shelf above its head, the object will have to move through the shelf.)
* The fingers do not move in the right positions if the scripts from my repository "Unity-Humanoid-Gestures" are being used in parallel. It is therefore best to switch these off.

# License
"TransportInterface.cs" runs under the GPLv3 license. "PickUp.cs" was adapted from Final IK's "PickUp2Handed.cs" and published here with friendly permission from Root Motion.
