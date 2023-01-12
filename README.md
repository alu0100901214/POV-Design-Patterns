# Design Patterns

For this activity I modified the WebGL application where a ship that moves around the stage using the keyboard was implemented. 
I decided to use the "Command" design pattern to modify the "Shared/Controller.razor.cs" file and I needed to make a small change in "Pages/Game.razor.cs".

The idea was to modify the Controller class that contained the game actions to:
- Move forward
- Move backward
- Stop.

-> This

```
public partial class Controller : LayoutComponentBase, IController {

        public double WindowWidth=1.0;
        public double WindowHeight=1.0;

        public double MouseEffect=1.0;

        public double BoomRate=1.0;

        public double ScaleMovement {get; set;}

      public void MouseMovement(double x, double y){

            Coordinates2D mC = new Coordinates2D(x,y);
            Coordinates2D delta;
            delta = mC - this.mCoord;
            this.mCoord = mC;

            boomTarget = new Angles2D(boomTarget.Yaw + MouseEffect * delta.X,
                    boomTarget.Pitch + MouseEffect * delta.Y);
            boomAngles = boomAngles + BoomRate*(boomTarget-boomAngles);
            
        }
        public bool GamePlaying=false;
        private Coordinates2D mCoord = new Coordinates2D();

        private Angles2D boomAngles = new Angles2D();
        private Angles2D boomTarget = new Angles2D();

        private Vector3 MovementInput = new Vector3(0.0f,0.0f,0.0f);

        private void mouseEvent(MouseEventArgs e){
             
            double x = e.ClientX;
            double y = e.ClientY;
            x = x/WindowWidth;
            y= y/WindowHeight;

           MouseMovement(x,y);
        }

        private void keydownEvent(KeyboardEventArgs e){
            Console.WriteLine("KeyDown");
            double f = System.Math.PI/180;                        
            double yaw = f*boomAngles.Yaw;
            switch(e.Key){
                case "w" :
                    MovementInput.x = -(float)System.Math.Sin(yaw);
                    MovementInput.z = -(float)System.Math.Cos(yaw);
                    MovementInput.y=0.0f;
                    MovementInput = this.ScaleMovement* MovementInput;
                    break;
                case "s":
                    MovementInput.x = (float)System.Math.Sin(yaw);
                    MovementInput.z = (float)System.Math.Cos(yaw);
                    MovementInput.y=0.0f;
                    MovementInput = this.ScaleMovement* MovementInput;
                    break;
                default:
                    break;
                    

            }
        }

        private void keyupEvent(KeyboardEventArgs e){
            Console.WriteLine("keyUp"); 
            MovementInput.x = 0.0f;
            MovementInput.y = 0.0f;
            MovementInput.z= 0.0f;
        }

        public Coordinates2D GetMCoord(){
            return this.mCoord;
        }

        public Angles2D GetBoomAngles(){
            return this.boomAngles;
        }

        public Vector3 GetMovement(){
            return this.MovementInput;
        }

        protected ElementReference ctrlDiv;
        protected override async Task OnAfterRenderAsync(bool firstRender) {
            if (firstRender)  {
                this.boomAngles = new Angles2D();
                this.ScaleMovement = 0.1;
            }   
            return;         
        }      
    }
```
