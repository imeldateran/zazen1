# zazen1

import shiffman.box2d.*;
import org.jbox2d.collision.shapes.*;
import org.jbox2d.common.*;
import org.jbox2d.dynamics.*;


Box2DProcessing box2d;

Head head; 
Mover[] movers = new Mover[25];


Attractor a;

void setup() {
  head = new Head("meditador.png", 0, height/30);
  size(800,800);
  smooth();

  box2d = new Box2DProcessing(this);
  box2d.createWorld();
  
  box2d.setGravity(0,0);

  for (int i = 0; i < movers.length; i++) {
    movers[i] = new Mover(random(8,16),random(width),random(height));
  }
  a = new Attractor(32,width/2,height/2);
}

void draw() {
  background(255);
  
   background(255);
  head.display();
   head.move();
   
 
  box2d.step();

  a.display();

  for (int i = 0; i < movers.length; i++) {
   
    Vec2 force = a.attract(movers[i]);
    movers[i].applyForce(force);
    movers[i].display();
  }
}


class Mover {

  
  Body body;

  PFont f;
  String message = "sielncio cine mar ";



float r = 100;

  Mover(float r_, float x, float y) {
    r = r_;
    f = createFont("Arial",40,true);
  textFont(f);
 
  textAlign(CENTER);
 
    BodyDef bd = new BodyDef();
    bd.type = BodyType.DYNAMIC;

   
    bd.position = box2d.coordPixelsToWorld(x,y);
    body = box2d.world.createBody(bd);

  
    CircleShape cs = new CircleShape();
    cs.m_radius = box2d.scalarPixelsToWorld(r);
    
    
    FixtureDef fd = new FixtureDef();
    fd.shape = cs;
   
    fd.density = 1;
    fd.friction = 0.3;
    fd.restitution = 0.5;

    body.createFixture(fd);

    body.setLinearVelocity(new Vec2(random(-5,5),random(-5,-5)));
    body.setAngularVelocity(random(-1,1));
  }

  void applyForce(Vec2 v) {
    body.applyForce(v, body.getWorldCenter());
  }


  void display() {
   
    Vec2 pos = box2d.getBodyPixelCoord(body);
   
    float a = body.getAngle();
    pushMatrix();
    translate(pos.x,pos.y);
    rotate(a);
    fill(150);
    
     textFont(f);         
  int x = 10;
  for (int i = 0; i < message.length(); i++) {
    textSize(random(12,36));
    text(message.charAt(i),x,height/2);
    // textWidth() spaces the characters out properly.
    x += textWidth(message.charAt(i)); 
  }
 
    popMatrix();
  }
}


class Head  {   
  PImage head; 
  float x,y;   
  float rot;   
  Head(String filename, float x_, float y_) { 
    
    head = loadImage(filename);
    x = x_;
    y = y_;
    rot = 0.0;
    head.resize(300,400);
   image(head,800,200);

  }   

  void display()  {   
   
    image(head,230, 260); 

  }   

  void move()  {   
    
    x += 1.0;
   rot += 0.01;
    if (x > width/2+head.width/2) {
     x = -head.width/2;
    }

  }   
}    

class Attractor {
  
 
  Body body;
  float r;

  Attractor(float r_, float x, float y) {
    r = r_;
   
    BodyDef bd = new BodyDef();
    bd.type = BodyType.STATIC;
   
    bd.position = box2d.coordPixelsToWorld(x,y);
    body = box2d.world.createBody(bd);

   
    CircleShape cs = new CircleShape();
    cs.m_radius = box2d.scalarPixelsToWorld(r);
    
    body.createFixture(cs,1);

  }


 
  Vec2 attract(Mover m) {
    float G = 100; 
    Vec2 pos = body.getWorldCenter();    
    Vec2 moverPos = m.body.getWorldCenter();
   
    Vec2 force = pos.sub(moverPos);
    float distance = force.length();
    
    distance = constrain(distance,2,1);
    force.normalize();
    
    float strength = (G * 1 * m.body.m_mass) / (distance * distance); 
    force.mulLocal(strength);        
    return force;
  }

  void display() {
   
    Vec2 pos = box2d.getBodyPixelCoord(body);
    
    float a = body.getAngle();
    pushMatrix();
    translate(pos.x,pos.y);
    rotate(a);
    fill(255);
    stroke(255);
    
    ellipse(200,200,60,90);
    popMatrix();
  }
}
