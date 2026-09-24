//此代码为基于ESP32S3二自由度的舵机机械臂，主要存在于臂的主体，缺少手部代码，可以自行添加
//其中包含未成形的残缺动态扫描的数码管代码，可以无视



#include<ESP32Servo.h>
#define adc_PIN 1

float adc_voltage=0;
int  last_adc_value=0;
Servo my_Servo_01;
Servo my_Servo_02;
int GUAN_01=47;
int GUAN_02=48;
volatile name Premise=OFF;
// #define segpins[8]={18,4,5,6,7,15,16,17};
// #define digitalPin[2]={41,42};
// #define segCode[10]={
//   0b00111111, // 0
//   0b00000110, // 1
//   0b01011011, // 2
//   0b01001111, // 3
//   0b01100110, // 4
//   0b01101101, // 5
//   0b01111101, // 6
//   0b00000111, // 7
//   0b01111111, // 8
//   0b01101111, // 9
// };

  int last_Switch_01_State=HIGH;
  int last_Switch_02_State=HIGH;
  int Switch_01_State;
  int Switch_02_State;
int var;
#define LED_01_PIN 9
#define LED_02_PIN 46
#define switch_01_PIN 10
#define switch_02_PIN 11
#define my_Servo_01_PIN 13
#define my_Servo_02_PIN 12

int pose=0;
int pose_02=0;
unsigned long time_Delay=50;
unsigned long the_First_Time=0;

void setup() {
  pinMode(LED_01_PIN,OUTPUT);
  pinMode(LED_02_PIN,OUTPUT);
  pinMode(switch_01_PIN,INPUT_PULLUP);
  pinMode(switch_02_PIN,INPUT_PULLUP);
  my_Servo_01.attach(my_Servo_01_PIN);
  my_Servo_02.attach(my_Servo_02_PIN);
  Serial.begin(115200);
 // pinMode(digitalPin[0], OUTPUT);
 // pinMode(digitalPin[1], OUTPUT); 
  analogReadResolution(12);
  // put your setup code here, to run once:
  my_Servo_02.write(pose_02);
  my_Servo_01.write(pose);
  Serial.println("开始首次运行");
}

void loop() {
 
  if(millis()-the_First_Time>=time_Delay){
    int adc_value=analogRead(adc_PIN);
   //按键防抖

    adc_value= map(adc_value,0,4095,0,180);
    if(abs(last_adc_value-adc_value)>=5){
      my_Servo_02.write(adc_value);
      Serial.print("二号舵机：");
      Serial.println(adc_value);
      last_adc_value=adc_value;
    }
    //通过旋转电位器实现舵机的转动
   Switch_01_State=digitalRead(switch_01_PIN);
   Switch_02_State=digitalRead(switch_02_PIN);
   the_First_Time=millis();
//双条件判断可以做到防止长按引发的异常抖动
  if(last_Switch_01_State==HIGH&&Switch_01_State==LOW){
    var=1;
  }
  if(last_Switch_02_State==HIGH&&Switch_02_State==LOW){
    var=2;
  }
  
  switch(var){
    case 1:
    Servo_01_Motion_go();
      break;
      
    case 2:
    Servo_01_Motion_back();
      break;
      
  }

  last_Switch_01_State=Switch_01_State;
  digitalWrite(LED_01_PIN,LOW);
  last_Switch_02_State=Switch_02_State;
  digitalWrite(LED_02_PIN,LOW);

  var=0;


  // count_Pose_01(pose_02);
 // count_Pose_02(pose_02);


}
}
// void showNumber(int num){
//   byte code=segCode[num];
//   for(int i=0;i<=7;i++){
//     int state=bitRead(code,i);
//     digitalWrite(segpins[i],state);
    
//   }
//   }
// void count_Pose_01(int poses_01){
//   int ten_1=poses_01/10;
 
//    digitalWrite(digitalPin[0],LOW);
//    digitalWrite(digitalPin[1],HIGH);
//    showNumber(ten_1);
//   //digitalWrite(digitalPin[0],HIGH);
//  // digitalWrite(digitalPin[1],LOW);
// }
// void count_Pose_02(int poses_02){
//    int ten_2=poses_02/10;
//   int ones=poses_02-ten_2*10;

//   showNumber(ones);
//   digitalWrite(digitalPin[0],LOW);
//   digitalWrite(digitalPin[1],HIGH); 

// }
void Servo_01_Motion_go(){
    pose+=5;
    if(pose>=185){
      pose=180;
      }
      my_Servo_01.write(pose);
      digitalWrite(LED_01_PIN,HIGH);
      Serial.print("一号舵机:");
      Serial.print(pose);
//每次按下会加5°运行
      Serial.println(" ");
}
void Servo_01_Motion_back(){
        pose-=5;
    if(pose<=-5){
      pose=0;
      }
      my_Servo_01.write(pose);
      digitalWrite(LED_02_PIN,HIGH);
      Serial.print("一号舵机:");
      Serial.print(pose);
  
      Serial.println(" ");
}


