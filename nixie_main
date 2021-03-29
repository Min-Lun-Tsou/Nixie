#include "DHT.h"
#define DHTpin A0
#define Ra 5 
#define Rb 3
#define Rc 2
#define Rd 4    
#define La 9
#define Lb 7
#define Lc 6
#define Ld 8  // if changed don't forget the pin no' in the pinMode() section
#define AnL 10 //if changed don't forget the pin no' in the interrupt vetctor also
#define AnR 11
uint8_t temp,moist,left,right,q,w,o,p,lastime;
bool R  = 0;    //if the muiltiplexer is at the right set or not
bool change = 1;//if it is the time to change muitiplexer cursor
DHT dht(DHTpin,DHT11);

// the needed digital signal on the drivers' pins to the corresponding number, 11 switches tube off
bool driverL[11][4] = {  //for left two(tube 1&2)
    {0,0,0,1},{0,1,0,1},{1,0,0,0},{0,1,1,0},{0,1,1,1},
    {0,0,1,1},{0,0,1,0},{0,1,0,0},{1,0,0,1},{0,0,0,0},
    {1,1,1,1}
  };                    
bool driverR[11][4] = {  //for right two
    {0,1,0,0},{0,1,1,0},{0,0,0,0},{0,0,1,1},{0,0,1,0},
    {1,0,0,0},{1,0,0,1},{0,1,1,1},{0,0,0,1},{0,1,0,1},
    {1,1,1,1}
  };

void setup() {
  noInterrupts();
  TCCR2A = 0b00000000;  //use timer2 to control muitiplexing
  TCCR2B = 0b00000110;
  OCR2A  = 0xce;
  OCR2B  = 0xeb;
  TIMSK2 = 0b00000111;
  for(uint8_t i=2;i<=11;i++)
    pinMode(i,OUTPUT);
  dht.begin();
  temp = dht.readTemperature();
  moist = dht.readHumidity();
  lastime = millis();
  interrupts();
}

void loop() {
  //section dataupdate (gets data from sensor, clock etc...to variables temp and moist)
  if (millis()-lastime > 5000){
    temp = dht.readTemperature();
    moist = dht.readHumidity();
    lastime = millis();
  }
  
  // section scene *haven't programmed(puts numbers from variables to four digits "qwop" according to the animation, might use timer1)
  q = 10;
  w = temp/10;
  o = temp%10;
  p = 10;
    
  // section hardware output ()
  if(change&&R){  
    driverWrite(3,o);
    driverWrite(4,p);
    change = 0;
  }
  else if(change){
    driverWrite(1,q);
    driverWrite(2,w);
    change = 0;
  }
}


ISR(TIMER2_COMPA_vect){
  PORTB&=0b11110011;
}

ISR(TIMER2_COMPB_vect){
  change = 1;
}

ISR(TIMER2_OVF_vect){
  if(R){
    PORTB|=0b00001000; 
    R = 0;
  }
  else{
    PORTB|=0b00000100;
    R = 1;
  }
}

void driverWrite(uint8_t tube, uint8_t x){ //turn the left driver or right driver to the digit "x"
  switch(tube){
    case(1):
      digitalWrite(La,driverL[x][3]);digitalWrite(Lb,driverL[x][2]);
      digitalWrite(Lc,driverL[x][1]);digitalWrite(Ld,driverL[x][0]);break;
    case(2):
      digitalWrite(Ra,driverL[x][3]);digitalWrite(Rb,driverL[x][2]);
      digitalWrite(Rc,driverL[x][1]);digitalWrite(Rd,driverL[x][0]);break;
    case(3):
      digitalWrite(La,driverR[x][3]);digitalWrite(Lb,driverR[x][2]);
      digitalWrite(Lc,driverR[x][1]);digitalWrite(Ld,driverR[x][0]);break;
    case(4):
      digitalWrite(Ra,driverR[x][3]);digitalWrite(Rb,driverR[x][2]);
      digitalWrite(Rc,driverR[x][1]);digitalWrite(Rd,driverR[x][0]);break;
  }
}
