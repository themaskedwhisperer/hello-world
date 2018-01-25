# hello-world
Let's just follow the tutorial for now

I took a creative computing class my last semester of college and never finished my final project. It was a 2D side scroller. I'd like to somehow make it work. To make myself feel better about it. Plus it was a pretty nifty cool idea.

I'll let this portfolio live here. :https://itp.nyu.edu/classes/creativecomputing-spring2015/author/sss639/

project 1

void setup() {
  size(600, 300);
}

void draw(){
  PImage MarioBack = loadImage("http://hugoware.net/resource/images/misc/game-background.jpg");
  image(MarioBack,0,0,700,300);
  PImage Link = loadImage("http://fc03.deviantart.net/fs70/i/2012/051/a/d/link_chibi_by_shadowshyna-d4qfnhf.png");
  image(Link,width-mouseX,0,300,150);
  PImage Seto = loadImage("https://41.media.tumblr.com/47927d6354f9b6188fec5f6712e28a5c/tumblr_n5die4PNOp1s50yqxo1_400.png");
  image(Seto,90,height-mouseY,150,150); 
  
}

project 2

//tracking the average pixel makes it shake less
import processing.video.Capture;
Capture camera;// regular processing libary
int aveX, aveY; //this is what we are trying to find
int threshold = 20; //you can adjust with- = see key pressed
//you should click on the color you really want, see mousePressed below
int colorToChase = color (120, 70, 70); //just some redish color
PImage springTrap = loadImage("http://t07.deviantart.net/Rw5yNxB9obkcg8j7Je4BnOjeR2s=/300x200/filters:fixed_height(100,100):origin()/pre04/6c39/th/pre/f/2015/066/3/f/springtrap_jumpscare_from_the_right_by_cosmicmoonshine-d8kv3h2.png");
boolean showPixelsWithinThreshold = true;  //show pixels for debugging

PImage goldenFreddy = loadImage("http://img2.wikia.nocookie.net/__cb20141114202129/freddy-fazbears-pizza/images/4/40/Transparent_golden_freddy_decal_by_punchox3-d84pzyg.png");

//goldenFreddy goldenFreddy1 ;
// goldenFreddy goldenFreddy2 ;
// goldenFreddy goldenFreddy3 ;

goldenFreddy[] allgoldenFreddys = new goldenFreddy[100];

void setup() {
  size(640, 480);
  println(Capture.list());//show list of cameraeras available
  camera = new Capture(this, 640, 480);
  camera.start();
  for (int i = 0; i < allgoldenFreddys.length; i++) {
    allgoldenFreddys[i] = new goldenFreddy();
  }
}
void draw() {
  trackColor();
  image(springTrap, aveX, aveY, 40, 40);

  for (int i = 0; i < allgoldenFreddys.length; i++) {
    allgoldenFreddys[i].checkEdges();
    allgoldenFreddys[i].moveIt();
    allgoldenFreddys[i].checkForIntersection();
    allgoldenFreddys[i].display();
  }
}

class goldenFreddy {
  float xpos = random(640);
  float ypos =  random(480) ;
  int xDirection= 1;
  int yDirection= 1;

  void checkEdges() {
    if (xpos > width || xpos < 0) {
      xDirection = -xDirection;
    }
    if (ypos > height || ypos < 0) {
      yDirection = -yDirection;
    }
  }

  void moveIt() {
    xpos = xpos + xDirection;
    ypos = ypos + yDirection;
  }
  void checkForIntersection() {
    if (dist(xpos, ypos, aveX, aveY) < 20) {
      yDirection = 0; //;
      xDirection =  0; //;
    }
  }

  void display() {
    image(goldenFreddy, xpos, ypos, 50, 50);
  }
}




void trackColor() {
  if (camera.available()) {
    background(255);
    camera.read();
    camera.loadPixels();
    int totalFoundPixels= 0;  //we are going to find the average location of change pixes so
    int sumX = 0;  //we will need the sum of all the x find, the sum of all the y find and the total finds
    int sumY = 0;
    //enter into the classic nested for statements of computer vision
    for (int row = 0; row < camera.height; row++) {
      for (int col = 0; col < camera.width; col++) {
        //the pixels file into the room long line you use this simple formula to find what row and column the sit in 

        int offset = row * camera.width + col;
        //pull out the same pixel from the current frame 
        int thisColor = camera.pixels[offset];

        //pull out the individual colors for this pixel
        float r = red(thisColor);
        float g = green(thisColor);
        float b = blue(thisColor);

        //pull out the individual colors for the pixel color you are chasing
        float targetR = red(colorToChase);
        float targetG  = green(colorToChase);
        float targetB = blue(colorToChase);

        //in a color "space" you find the distance between color the same whay you would in a cartesian space, phythag or dist in processing
        float diff = dist(r, g, b, targetR, targetG, targetB);

        if (diff < threshold) {  //if it is close enough in size, add it to the average
          sumX = sumX + col;
          sumY= sumY + row;
          totalFoundPixels++;
          if (showPixelsWithinThreshold) {
            //turn qualifying pixels pink for debugging
            camera.pixels[offset] = color(255, 200, 200);
          }
        }
      }
    }
    if (showPixelsWithinThreshold) { //when debuggging press x to toggle
      camera.updatePixels();
      image(camera, 0, 0);// draw the camera
      text("Click on Color to Track and then Press 'x' to clear video", mouseX +10, mouseY +10);
    }
    if (totalFoundPixels > 0) {
      aveX = sumX/totalFoundPixels;
      aveY = sumY/totalFoundPixels;
      aveX = width -aveX;  //this mirrors the location left and right
    }
  }
}


void mousePressed() {
  //if they click, use that picture for the new thing to follow
  int offset = mouseY * camera.width + mouseX;
  //pull out the same pixel from the current frame 
  int thisColor = camera.pixels[offset];
  colorToChase = thisColor;
  println("Chasing new color  " + red(colorToChase) + " " + green(colorToChase) + " " + blue(colorToChase));
}

void keyPressed() {
  //for adjusting things on the fly
  if (key == '-') {
    threshold--;
    println("Threshold " + threshold);
  } else if (key == '=') {
    threshold++;
    println("Threshold " + threshold);
  } else if (key == 'x') {
    //toggle showing pixels for debugging
    showPixelsWithinThreshold = !showPixelsWithinThreshold;
  }
}

project 3

String[] words;

IntDict concordance;

int index = 0;
void setup() {
  size(600,400);
  String[] lines = loadStrings("http://www.google.com/?gws_rd=ssl");
  String allthetxt = join(lines, " " );
  words = splitTokens(allthetxt, " ,.:;!");
  concordance = new IntDict();
  
  
  for (int i = 0; i < words.length; i++) {
   concordance.increment(words[i]);
 }
 
 println(concordance);
  

  
}

void draw() {
  background(0);
  textSize(64);
  textAlign(CENTER);
  text(words[index], width/2, height/2);
  index++;
}

project 4

int numFrames = 6;  // Number of frames in the animation
int currentFrame = 0;
PImage[] images = new PImage[numFrames];
    
void setup() {
  size(840, 600);
  frameRate(24);
  
  images[0]  = loadImage("clock1.jpg");
  images[1]  = loadImage("clock2.jpg"); 
  images[2]  = loadImage("clock3.jpg");
  images[3]  = loadImage("clock4.jpg"); 
  images[4]  = loadImage("clock5.jpg");
  images[5]  = loadImage("clock6.jpg"); 
  
 
  //for (int i = 0; i < numFrames; i++) {
  //  String imageName = "clock" + nf(i, 4) + ".jpg";
  //  images[i] = loadImage(imageName);
  //}
} 


 
void draw() { 
  background(0);
  currentFrame = (currentFrame+1) % numFrames;  // Use % to cycle through frames
  int offset = 0;
  for (int x = -100; x < width; x += images[0].width) { 
    image(images[(currentFrame+offset) % numFrames], x, -20);
    offset+=2;
    image(images[(currentFrame+offset) % numFrames], x, height/2);
    offset+=2;
  }
}

project 5 - what i would like to improve. the failed final project

public class HeroineGame

  private static final String TAG = HeroineGame.class.getSimpleName();

  private Bitmap bitmap;    // the animation sequence
  private Rect sourceRect;  // the rectangle to be drawn from the animation bitmap
  private int frameNr;    // number of frames in animation
  private int currentFrame;  // the current frame
  private long frameTicker;  // the time of the last frame update
  private int framePeriod;  // milliseconds between each frame (1000/fps)

  private int spriteWidth;  // the width of the sprite to calculate the cut out rectangle
  private int spriteHeight;  // the height of sprite

  private int x;        // the X coordinate of the object (top left of the image)
  private int y;        // the Y coordinate of the object (top left of the image)

}

public void update(long gameTime) {
  if (gameTime > frameTicker + framePeriod) {
    frameTicker = gameTime;
    // increment the frame
    currentFrame++;
    if (currentFrame >= frameNr) {
      currentFrame = 0;
    }
  }
  // define the rectangle to cut out sprite
  this.sourceRect.left = currentFrame * spriteWidth;
  this.sourceRect.right = this.sourceRect.left + spriteWidth;
}

public void update() {
  heroine.update(System.currentTimeMillis());
}
public void draw(Canvas canvas) {
    // where to draw the sprite
    Rect destRect = new Rect(getX(), getY(), getX() + spriteWidth, getY() + spriteHeight);
    canvas.drawBitmap(bitmap, sourceRect, destRect, null);
  }
  
  private HeroineGame heroine;

  public MainGamePanel(Context context) {
   

    // create spirte and load hopefully
    heroine = new HeroineGame(
        BitmapFactory.decodeResource(getResources(), R.drawable.SpriteSheet1.png)
        , 10, 50  // initial position
        , 30, 47  // width and height of sprite
        , 5, 5);  // FPS and number of frames in the animation

    // create the game loop thread
    thread = new MainThread(getHolder(), this);

}

public void draw(Canvas canvas) {
  // where to draw the sprite
  Rect destRect = new Rect(getX(), getY(), getX() + spriteWidth, getY() + spriteHeight);
  canvas.drawBitmap(bitmap, sourceRect, destRect, null);
  canvas.drawBitmap(bitmap, 20, 150, null);
  Paint paint = new Paint();
  paint.setARGB(50, 0, 255, 0);
  canvas.drawRect(20 + (currentFrame * destRect.width()), 150, 20 + (currentFrame * destRect.width()) + destRect.width(), 150 + destRect.height(),  paint);
}

Project 5 my first failed code



size(800,600);
PImage KatoChan = loadImage("http://fc07.deviantart.net/fs71/f/2015/008/0/4/kato__saekano__by_sanoboss-d8d5td9.jpg");
image(KatoChan,0,0,900,500);

PImage Asami = loadImage("http://cdn.themis-media.com/media/global/images/library/deriv/812/812870.jpg");
image(Asami,0,0,150,150);

PImage Onodera = loadImage("http://i.ytimg.com/vi/QxQlFHMIfvE/maxresdefault.jpg");
image(Onodera,0,200,150,150);

PImage Kaneki = loadImage("http://fc03.deviantart.net/fs70/f/2014/268/f/2/kaneki_ken_render_by_monaleinchenx3-d80gh23.png");
image(Kaneki,100,0,150,150);

PImage Free = loadImage("http://40.media.tumblr.com/7cafcb52fa8df9880c1a4664333b2a7c/tumblr_mw4gclQlzW1si30aao1_1280.png");
image (Free,200,0,300,150);

