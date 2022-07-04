# Smarcle Makers Day Arduino Project
### 휠체어 리프트 기어드 모터 소스코드                 
```ino
// 기어드 모터 코드
 #include <SoftwareSerial.h>         //Software Serial Port
   #define RxD 9 
   #define TxD 8
   
   SoftwareSerial blueToothSerial(TxD,RxD);   //the software serial port 
   
  int in1 = 2;
  int in2 = 4;
  int enA = 3;
  int in3 = 5;
  int in4 = 7;
  int enB = 6;
  int i=0; // 정지 시간 조절할 정수형 변수 i
  int j=0;
  int state; // 상황 번호를 저장할 정수형 변수 state


void setup() {
  
   Serial.begin(9600);                   //Serial port for debugging
   blueToothSerial.begin(9600);            //BT module baud rate
   pinMode(enA,OUTPUT);
   pinMode(in1,OUTPUT);
   pinMode(in2,OUTPUT);
   pinMode(enB,OUTPUT);
   pinMode(in3,OUTPUT);
   pinMode(in4,OUTPUT); 
}
 
void loop() {

    if(blueToothSerial.available() > 0){     
      state = blueToothSerial.read();   
      Serial.print(char(state));
      Serial.println("번 선택");
    }

 
    
   
  //Change speed if state is equal from 0 to 4. Values must be from 0 to 255 (PWM)
    i =0; // i를 0으로 초기화
    while (state == '1') // 상승 (state가 1과 같으면)
    {
      digitalWrite(in1, HIGH);
      digitalWrite(in2, LOW);
      digitalWrite(in3, HIGH);
      digitalWrite(in4, LOW);
      digitalWrite(enA, 10);
      digitalWrite(enB, 10);
      delay(1000); 
      i = i+1; // i값을 1씩 증가
      Serial.print(i); 
      Serial.println("초 경과");
      while (i>=20) //i가 20보다 크거나 같으면
      {
         // 정지 명령
         digitalWrite(in1, LOW);
         digitalWrite(in2, LOW);
         digitalWrite(in3, LOW);
         digitalWrite(in4, LOW);
         digitalWrite(enA, 0);
         digitalWrite(enB, 0); 
         i++; //i를 1씩 증가
         if(i==21){ //i가 21과 같으면
          state = 7; //state 값을 7로 저장(state==1인 상황을 벗어나야 하므로)
           break; 
         }
    }
    }
    
      i = 0; // i 값을 0으로 초기화
    
     while (state == '2') // 하강 (state가 2과 같으면)
    {
     digitalWrite(in1, LOW);
     digitalWrite(in2, HIGH);
     digitalWrite(in3, LOW);
     digitalWrite(in4, HIGH);
     digitalWrite(enA, 50);
     digitalWrite(enB, 50);
     delay(1000);
     i = i+1; // i값을 1씩 증가
     Serial.print(i); 
     Serial.println("초 경과");
     while (i>=20) //i가 20보다 크거나 같으면
      {
         // 정지 명령
         digitalWrite(in1, LOW);
         digitalWrite(in2, LOW);
         digitalWrite(in3, LOW);
         digitalWrite(in4, LOW);
         digitalWrite(enA, 0);
         digitalWrite(enB, 0);
         i++; //i를 1씩 증가
         if(i==21){ //i가 21과 같으면
          state = 7; //state 값을 7로 저장(state==2인 상황을 벗어나야 하므로)
           break;
         }
    }
     }
}


// i 값을 조절하면 정지 시간을 조절할 수 있습니다.
```

### 휠체어 리프트 고정대&차단바 소스코드 
```ino
// 고정대&차단바 코드
#include<Servo.h>
#include <SoftwareSerial.h>

Servo servoA;
Servo servoB;
Servo servoC;

SoftwareSerial btSerial(8,9); // TxD, RxD 

int state; // 상황 번호를 저장할 정수형 변수 state

void setup(){
 
 

   servoA.attach(2); // 서보모터A 2번에 연결
   servoB.attach(3); // 서보모터B 3번에 연결
   servoC.attach(4); // 서보모터C 4번에 연결

   Serial.begin(9600); // 시리얼 통신 속도
   btSerial.begin(9600); //btSerial 통신 속도

}

void loop(){


  if(btSerial.available() > 0){     // 블루투스 모듈로 들어온 데이터가 0보다 크다면
      state = btSerial.read();   // 블루투스 모듈로 들어온 번호를 state에 저장
      Serial.println(char(state));
      Serial.write(btSerial.read());
    }


 
    
  if(state == '3') // 차단바_하강(state가 3과 같으면)
  {
       servoA.write(90); // 서보모터A 90도 회전
       delay(1000);  // 1초 대기
    }

    else if(state == '4') // 차단바_상승(state가 4와 같으면)
    {
      servoA.write(-90); // 서보모터A 반대로 90도 회전
      delay(1000);   // 1초 대기
    }

    else if(state == '5') // 고정대_하강(state가 5와 같으면)
    {
      servoB.write(90); // 서보모터B 90도 회전
      servoC.write(90); // 서보모터C 90도 회전
     
      delay(1000); 
    }

    else if(state == '6') // 고정대_상승
    {
      servoB.write(-90); // 서보모터B 반대로 90도 회전
      servoC.write(-90); // 서보모터C 반대로 90도 회전
     
      delay(1000); 
    }

      
  
 
}
```
