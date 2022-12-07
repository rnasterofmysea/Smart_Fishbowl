# Fishbowl-Auto-Management-System

# SYSTEM IDEA

![image](https://user-images.githubusercontent.com/81907470/197466414-c1bfe1f7-9bcd-4f10-b52c-d9b15ba873e8.png)


## 1. Water Temperature Management System

### Requirements
  
  1. If user click a button on web/app, operate Arduino by serial moniter
    1.1 setting default temperature
    1.2 setting temperature
  
  2. If Arduino get a setting temperature, compare it to current temperature.
  3. 
    2.1 Send the current temperature to raspberry pi4
    
  3. After comparsion, operate step motor
    3.1 Send endpoint to raspberry pi4
  
  4. print the result by ReactJs

![image](https://user-images.githubusercontent.com/81907470/206219565-e28d9a52-3fb6-4740-8f07-bcbf217cd535.png)

  - Setting defualt temperature cased by fish type
  - Measuring water temperature apart from water heater
  - Monitoring by senior monitor and controling survo motor to handel heater
  
## 2. Feeding System

  1. If user click a button on web/app, operate Arduino by serial moniter
  2. If Arduino get a starting point, operatre servo motor
  3. Send endpoint to raspberry pi4
  4. print the result by ReactJs
   
![image](https://user-images.githubusercontent.com/81907470/206219646-1be88c6e-332e-40e3-ba05-1c2e1bc68f5a.png)

  - Set feeding time
  - Controling servo motor to dorp feed from the box
  
## 3. Filtering System

  1. If user click a button on web/app, operate Arduino by serial moniter
  
  2. Compare current turbidty with standard
    
    2.1 send current turbidty to raspiberry pi
    2.1 while current turbidty over standard, operating water pump
  
  3. Send endpoint to raspberry pi4
  4. print the result by ReactJs

![image](https://user-images.githubusercontent.com/81907470/206219689-9d90486c-563a-4762-9b18-b923fa66ebf8.png)
    
  - Measuring turbidity & sensor to judge water pollution degree
  - Operating water pump if water pullution degree is over
  - Absorbing water from water tank (which planted waterplant)
  
## +4. [Alpha] Water Level Management System

  - Measuring water level
  - Operating water pump if water level is lower than common level
  - If water level is not filled, alert user to notice it

## +5. [Alpha] PH Level Management System

 - Measuring & monitoring PH level
 - If ph level is caution level, drop water quality medicine


# System Configuration

## Hardware Configuration

![image](https://user-images.githubusercontent.com/81907470/204811618-90469649-6c7b-46e3-becd-3fb59f42ca7a.png)

### Board

- Arduino
- Raspberry Pi4

### Sensor Module

- water temperature sensor
- servo motor
- water pump

### Software Configuration

- Webservice: React
- Webserver: FastAPI
- ProgramLanguage: Python , Arduino.c



## etc Equipment

- water bowl


# System Flow-Diagram

![image](https://user-images.githubusercontent.com/81907470/204813416-28406883-f15b-4c4a-b242-8efc1b20fb94.png)

# System Structure

```bash
├── react
│   ├── ButtonItems
│   │   └── FeedingItems
│   │       └── FeedingItems.js
│   │   └── FilteringItems
│   │       └── FileringItems.js
│   │   └── TempItems
│   │       ├── ItemTemplate.css
│   │       └── TempItems.js
│   │   ├── FeedingTemplate.js
│   │   ├── FilteringTemplate.js
│   │   ├── ItemTemplate.css
│   │   └── TempTemplate.js
│   ├── App.css
│   ├── App.js
│   ├── ButtonTemplate.js
│   └── Template.js
├── flaskapi
│   ├── main.py
├── arduino
│   └── dataset.py
└── run.sh
```

# System Tutorial

## 1. Development Setting

## Install Arudino

```
sudo install arduino
```

## Install Fast API

```
$ pip3 install fastapi
$ pip3 install uviconrn
```

## Install ReactJs

```
sudo curl -fsSL https://deb.nodesource.com/setup_14.x | bash -
apt-get install -y nodejs
```
```
$ node -v
$ npm -v
```

```
$ sudo apt-get install build-essential
```

```
$ npm install -g create-react-app
$ create-react-app --version
$ create-react-app ["project-name"]
$ npm start
```

# 2. Aruino Programming

```
#include <OneWire.h>
#include <DallasTemperature.h>
#include <Stepper.h>

#define ONE_WIRE_BUS 2


//Setup a oneWire instance to communicate with any OneWire device
OneWire oneWire(ONE_WIRE_BUS);

//Pass oneWire referance to DallasTemperature library
DallasTemperature sensors(&oneWire);

// 2048:한바퀴(360도), 1024:반바퀴(180도)...
const int stepsPerRevolution = 2048; 

// 모터 드라이브에 연결된 핀 IN4, IN2, IN3, IN1
Stepper myStepper(stepsPerRevolution,11,9,10,8);

int rotation = 0;
int post_temp = 30;

int max_temp = 0;
int min_temp = 0;


void default_setting(){

  min_temp = 20;
  max_temp = 34;
  int default_temp = 25;
  //1도가 움직일때 0.6
  // open issue: 소수점 몇째짜리까지 되는지 모름
  rotation = stepsPerRevolution / (max_temp - min_temp);
  myStepper.step(rotation * (default_temp - min_temp));
  Serial.print("초기값");
  Serial.println(rotation * (default_temp - min_temp));
  delay(5000);
}

void setup(){

  // 온도 센서 설정
  sensors.begin(); //Start up the libratry

  //시리얼 모니터
  Serial.begin(9600);

  //step motor 속도 설정
  myStepper.setSpeed(14);

  // 서보모터 0도 초기화
  Serial.print("초기화+++++");
  delay(2000);
  default_setting();
  //default 값 설정
}

void loop(){
  // int voltage = analogRead(TEMP_PIN);
  // float temp = voltage * 5.0 * 100 / 1024;

  // Send the command to get temperatures
  sensors.requestTemperatures(); 
  float temp = sensors.getTempCByIndex(0);
  Serial.print("\xe2\x84\x83");
  
  Serial.println(temp);

  float compare_temp = temp - post_temp;
  
  if(temp > max_temp){
    compare_temp = max_temp - temp;
    myStepper.step(rotation * compare_temp);
    post_temp = max_temp;
  } else if (temp <min_temp){ 
      compare_temp = min_temp - temp;
      myStepper.step(rotation * compare_temp);
      post_temp = min_temp;
  } else{ 
    if(compare_temp > 0){
      myStepper.step(rotation * compare_temp);
      post_temp = temp;
    }else{
      myStepper.step(-rotation * compare_temp);
      post_temp = temp;
    }
  }
  
  Serial.print("온도차 ::");
  Serial.print(compare_temp);
  Serial.print(" 움직인 각도:: " );
  Serial.println(rotation * compare_temp);

  delay(3000);

}
```
# 3. Fast API Programming



# 4. ReactJs Programming

# 5. Test Code (Arduino)





