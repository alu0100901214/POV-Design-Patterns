# Design Patterns

For this activity I modified the WebGL application where a ship that moves around the stage using the keyboard was implemented. 
I decided to use the "Command" design pattern to modify the "Shared/Controller.razor.cs" file and I needed to make a small change in "Pages/Game.razor.cs".

The idea was to modify the Controller class that contained the game actions to:
- Move forward
- Move backward
- Stop.

The original Controller class performs both the Pawn movement calculations and the detection of the keys to perform the different actions described above.

To implement this design pattern, I have extracted the motion calculations into a separate class called "Action".
The Controller class now contains only the connections to detect keyboard and mouse movement and some Getters and Setters that "Game.razor.cs" needed access to.

```
public partial class Controller : LayoutComponentBase {
        public Action action;

        public MoveForwardCommand forward;
        public MoveBackwardCommand backward;
        public StopCommand stop;

        public Controller(){
            action = new Action();
            forward = new MoveForwardCommand();
            backward = new MoveBackwardCommand();
            stop = new StopCommand();
        }

        private void mouseEvent(MouseEventArgs e){
            double x = e.ClientX;
            double y = e.ClientY;

            x = x/action.WindowWidth;
            y= y/action.WindowHeight;

           action.MouseMovement(x,y);
        }

        private void keydownEvent(KeyboardEventArgs e){
            if(e.Key == "w")
                forward.execute(action);

            if(e.Key == "s")
                backward.execute(action);
        }


        private void keyupEvent(KeyboardEventArgs e){
            stop.execute(action);
        }
        
        // Getters
        ...
        // Setters
        ...
    }
```

But now the implementation of these actions is done by the new class "Command" that by inheritance contains the child classes "MoveFordwardCommand" to move forward, "MoveBackwardsCommand" to move backward and the class "StopCommand" to stop the movement. All of them implement the execute() function which is passed an object of the "Action" class in order to work.

```
    public abstract class Command{
        public abstract void execute(Action act);
    }

    public class MoveForwardCommand : Command{
        public override void execute(Action act) {
            double f = System.Math.PI/180;                        
            double yaw = f*act.GetBoomAngles().Yaw;
            act.MovementInput.x = -(float)System.Math.Sin(yaw);
            act.MovementInput.z = -(float)System.Math.Cos(yaw);
            act.MovementInput.y=0.0f;
            act.MovementInput = 0.1f* act.MovementInput;
        }
    }

    public class MoveBackwardCommand : Command{
        public override void execute(Action act) {
            double f = System.Math.PI/180;                        
            double yaw = f*act.GetBoomAngles().Yaw;
            act.MovementInput.x = (float)System.Math.Sin(yaw);
            act.MovementInput.z = (float)System.Math.Cos(yaw);
            act.MovementInput.y=0.0f;
            act.MovementInput = 0.1f* act.MovementInput;
        }
    }

    public class StopCommand : Command{
        public override void execute(Action act) {
            act.MovementInput.x = 0.0f;
            act.MovementInput.y = 0.0f;
            act.MovementInput.z= 0.0f;
        }
    }
```

About the modification in "Game.razor.cs", there is a "PawnController" object of the "Controller" class. Due to the extraction of the movement calculations, the accesses have been modified thanks to the implemented Getters and Setters.

