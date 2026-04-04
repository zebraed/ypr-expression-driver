# ypr-expression-driver

A MEL-based Yaw / Pitch / Roll driver setup for Autodesk Maya using quaternion rotation decomposition and exponential map reconstruction.
The entire setup is contained in a single MEL file for quick integration into existing rigs and scripts.

## Description

A MEL-based Yaw / Pitch / Roll driver for Autodesk Maya.

The entire setup is contained in a single MEL file, so it can be sourced and applied immediately without plugin compilation or additional dependencies.
This tool derives stable yaw / pitch / roll style driver values from quaternion-based bend-twist decomposition for practical rigging workflows.

## Why not use raw Euler channels?

Using raw Euler rotation channels directly as driver values can be unreliable.

Euler values are affected by rotate order, axis coupling, and discontinuities near singularities ... so `rotateX`, `rotateY`, and `rotateZ` do not always behave as stable semantic driver channels.

This can make corrective shapes, helper joints, and set-driven-key setups difficult to tune and maintain.

## Internal behavior

* The driver reads `rotateX`, `rotateY`, `rotateZ`, and `rotateOrder` from the target node.
* Internally, roll is extracted as twist around the specified twist axis.
* Yaw and pitch are derived from the bend component in exponential-map space.
* The yaw reference vector does not need to be perfectly orthogonal to the twist axis, because it is orthogonalized internally.
* This tool assumes a meaningful twist axis and bend reference direction are provided.

## Usage

### Load the script

```mel
source "yprExpressionDriver.mel";
```

### Quick start

Install the driver using the default axis setup:

```mel
yprCreateExpressionDriverDefault("joint1");
```

This creates and drives the following output attributes on the target node:

* `yaw`
* `pitch`
* `roll`

The default setup assumes:

* roll / twist axis = `X`
* yaw reference vector = `Y`

### Custom axis setup

You can explicitly define the twist axis and yaw reference vector:

```mel
yprCreateExpressionDriver(
    "joint1",
    1.0, 0.0, 0.0,   // roll / twist axis
    0.0, 1.0, 0.0,   // yaw reference vector
    "yaw", "pitch", "roll"
);
```

Arguments:

* `target`
  Target DAG node

* `rollAxisX`, `rollAxisY`, `rollAxisZ`
  Twist axis used to extract roll

* `yawVectorX`, `yawVectorY`, `yawVectorZ`
  Reference vector used to construct the bend plane

* `yawOutAttr`, `pitchOutAttr`, `rollOutAttr`
  Output attribute names created on the target node if they do not already exist

### Example

```mel
source "yprExpressionDriver.mel";

yprCreateExpressionDriver(
    "neck_jnt",
    1.0, 0.0, 0.0,
    0.0, 1.0, 0.0,
    "yaw", "pitch", "roll"
);
```

After installation, the target node will output three driven values:

* `neck_jnt.yaw`
* `neck_jnt.pitch`
* `neck_jnt.roll`

These values can be used for:

* corrective shapes
* helper joints
* set driven key inputs
* internal rig logic

## References
https://www.3dgep.com/understanding-quaternions/#Multiplying_a_Quaternion_by_a_Scalar

https://www.chadvernon.com/blog/swing-twist/

https://www.youtube.com/watch?v=d4EgbgTm0Bg

https://www.youtube.com/watch?v=zjMuIxRvygQ

Thank you my friend.<br>
https://github.com/perseusrigging/perseus
