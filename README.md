## Initialization

Controller is a wrapper to unity's Input system, that allows centralization of keys and actions into a named-controll. Initializing the controller can be done by either throwing the `ControllerInitializer` mono behaviour on a game object, or if you want a more custom solution you can run the following code:

```csharp
                Controller.SetupControllers();
                Controller.SetControllerType(ControllerType.keyboard);
```
The command `SetupControllers` initializes the controllers for the system, and `SetController` defines what controller type is currently active. `SetControllerType` can be called at any time to change the active controller type.

To define what named controls you have, you can add them freely to the enum `Controls` inside Controller.cs, There exists a few example controls already present there.
```csharp
        public enum Controls
        {
            Jump,

            CameraMovementX,
            CameraMovementY,

            MovementX,
            MovementY,
            Run,
        }
```

To define the behaviour of the given controls, you can add them to `SetupKeyboard` `SetupXbox` and `SetupPS`. The following are examples of a setup for a basic character controller on keyboard
```
            SetupButtonDown(KeyCode.Space, Controls.Jump);

            SetupButtonAxis("Mouse X", Controls.CameraMovementX);
            SetupButtonAxis("Mouse Y", Controls.CameraMovementY);

            SetupButtonAxis(KeyCode.W, KeyCode.S, Controls.MovementY);
            SetupButtonAxis(KeyCode.D, KeyCode.A, Controls.MovementX);

            SetupButtonHeld(KeyCode.LeftShift, Controls.Run);
```
At any point in time the controls can be overwritten with new controls by simply rerunning the various setup functions on any of the controller base classes.

Defining jump as the space bar, Camera movement by the Mouse X and Y values, WASD movement, and shift for running. Running the actions for youe gameplay would potentially look something like so:
```csharp
        public override void UpdateJump()
        {
            if (GetKeyDown(Controls.Jump) && CanJump)
            {
                jumpQueued = true;
                timeTillJump = movementData.JumpDelay;
            }

            base.UpdateJump();
        }

        public void UpdatePosition()
        {
            if (isGrounded && !RecentlyJumped && !jumpQueued)
            {
                running = GetKey(Controls.Run);

                float x = -GetAxis(Controls.MovementX);
                float y = GetAxis(Controls.MovementY);
                if (x != 0 || y != 0)
                {
                    direction = Vector2.SignedAngle(Vector2.up, new Vector2(x, y)) + playerCameraController.Angle;

                    Vector2 movement = direction.DegreeToVector2();
                    xMovement = movement.x;
                    yMovement = movement.y;
                }
                else
                {
                    xMovement = 0;
                    yMovement = 0;
                }
            }
        }
```
