# Fishbowl-Auto-Management-System

# SYSTEM IDEA

![image](https://user-images.githubusercontent.com/81907470/197466414-c1bfe1f7-9bcd-4f10-b52c-d9b15ba873e8.png)


## 1. Water Temperature Management System

### Requirements
  
  1. If user click a button on web/app, operate Arduino by serial moniter
    1.1 setting default temperature
    1.2 setting temperature
  
  2. If Arduino get a setting temperature, compare it to current temperature.
  
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
????????? react
???   ????????? ButtonItems
???   ???   ????????? FeedingItems
???   ???       ????????? FeedingItems.js
???   ???   ????????? FilteringItems
???   ???       ????????? FileringItems.js
???   ???   ????????? TempItems
???   ???       ????????? ItemTemplate.css
???   ???       ????????? TempItems.js
???   ???   ????????? FeedingTemplate.js
???   ???   ????????? FilteringTemplate.js
???   ???   ????????? ItemTemplate.css
???   ???   ????????? TempTemplate.js
???   ????????? App.css
???   ????????? App.js
???   ????????? ButtonTemplate.js
???   ????????? Template.js
????????? flaskapi
???   ????????? main.py
???   ????????? default_temperature.py
???   ????????? default_feeding.py
???   ????????? filtering_management.py
???   ????????? feeding_management.py
???   ????????? readme
???   ????????? _pycache_
???
????????? arduino
???   ????????? arduino
???       ????????? code
???           ????????? main
???               ????????? main.ino
????????? run.sh
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

## Sensor setting

![image](https://user-images.githubusercontent.com/81907470/206339486-b780deb8-1c67-44ba-bbb7-114ba2ea5243.png)


# 2. Aruino Programming

```
#include <OneWire.h> //???????????? ???????????????
#include <DallasTemperature.h> //???????????? ???????????????
#include <Stepper.h> //????????????
#include <Servo.h> //????????????

//???????????? ????????? ???
#define ONE_WIRE_BUS 2

//???????????? ????????? ???
#define TAKDO A1

// ???????????? ?????? => ??????
#define WATERPUMP_1 6
#define WATERPUMP_2 7

#define SERVO 12

//Setup a oneWire instance to communicate with any OneWire device
OneWire oneWire(ONE_WIRE_BUS);

//Pass oneWire referance to DallasTemperature library
DallasTemperature sensors(&oneWire);

// 2048:?????????(360???), 1024:?????????(180???)...
const int stepsPerRevolution = 2048;

// ?????? ??????????????? ????????? ??? IN4, IN2, IN3, IN1
Stepper myStepper(stepsPerRevolution,11,9,10,8);

int rotation = 0;
int post_temp = 30;

int max_temp = 0;
int min_temp = 0;

// Servo motor Configuration
Servo servo;

//?????? ?????? ??????
void default_temperature(int default_temp){

  min_temp = 20;
  max_temp = 34;
  //1?????? ???????????? 0.6
  // open issue: ????????? ?????????????????? ????????? ??????
  rotation = stepsPerRevolution / (max_temp - min_temp);
  // - => ??????
  // + => ??????
  myStepper.step(rotation * (default_temp - min_temp + 1.5));
  delay(3000);
  Serial.println("end_point");
}

//?????? ?????????
void filtering_management(){

  int takdo_data = 0;
  int i = 0;
  while(i == 0){
  //?????? ?????????
    takdo_data = analogRead(TAKDO);
    takdo_data = takdo_data * 2;
  
  //????????? ????????? ???????????? ??????
    if(takdo_data < 1400){
      digitalWrite(WATERPUMP_1,HIGH);
      digitalWrite(WATERPUMP_2,LOW);
      Serial.println((String) "filtering_management/now_value/" + takdo_data);
    }else{
      digitalWrite(WATERPUMP_1,LOW);
      digitalWrite(WATERPUMP_2,LOW);

      Serial.println((String) "filtering_management/now_value/" + takdo_data);
      Serial.println("end_point");
      i = 1;
    }
  }
  // filtering_management/now_value/{data}
  
}

//?????? ?????? ?????????
void temperature_management(){
  // int voltage = analogRead(TEMP_PIN);0
  // float temp = voltage * 5.0 * 100 / 1024;

  // Send the command to get temperatures
  sensors.requestTemperatures(); 
  float temp = sensors.getTempCByIndex(0);
  Serial.print("\xe2\x84\x83");
  
  // Serial.println(temp);

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
  
  // versionV1
  // Serial.println((String) "temperature_management/now_value/" + temp);
  // delay(3000);
  // Serial.println((String) "temperature_management/diff_value/" + compare_temp);
  // delay(3000);
  // Serial.println((String) "temperature_management/rotation_value/" + rotation * compare_temp);

  ///versionV2
  Serial.println((String) "temperature_management/" + temp + "/" + compare_temp+"/"+rotation * compare_temp);
  delay(3000);
  Serial.println("end_point");
}


//???????????? ??????
void feeding_management(){

  servo.write(180);
  delay(1000);
  servo.write(0);

  Serial.println("end_point");
}

void setup(){

  // ?????? ?????? ??????
  sensors.begin(); //Start up the libratry

  //????????? ?????????
  Serial.begin(9600);

  //step motor ?????? ??????
  myStepper.setSpeed(2);

  // ???????????? 0??? ?????????
  servo.attach(SERVO);
  servo.write(0);
  
  pinMode(TAKDO,INPUT); //???????????? A1??? ??????
  pinMode(WATERPUMP_1,OUTPUT); //????????????1 OUTPUT
  pinMode(WATERPUMP_2,OUTPUT); //????????????2 OUTPUT

}

void loop(){

  //filtering_management();
  
  // serial ????????? ????????? ???????????? ?????? ??????
  if(Serial.available() > 0){
    String inputStr = Serial.readString(); //??? ??????
    inputStr.trim();
     if(inputStr.indexOf(".") >= 0){
        inputStr = inputStr.substring(1, inputStr.length());
        default_temperature(inputStr.toInt());

     } else if(inputStr.equals("temperature")){
        temperature_management();

     } else if(inputStr.equals("filtering")){
        filtering_management();

     } else if(inputStr.equals("feeding")){
        feeding_management();
     }
    }
  }
```
# 3. Fast API Programming

## Main.py

```
from fastapi.middleware.cors import CORSMiddleware
from fastapi import FastAPI
from default_temperature import default_temperature
#from default_feeding import default_feeding
from temperature_management import temperature_management 
from filtering_management import filtering_management
from feeding_management import feeding_management

import serial
import time
from pydantic import BaseModel

app = FastAPI()

port_info = '/dev/ttyACM0'
baudrate_info = 9600
arduino = serial.Serial(port=port_info, baudrate = baudrate_info)


origins = ['*']
origins = ["http://localhost:3000"]
app.add_middleware(
    CORSMiddleware,
    allow_origins = origins,
    allow_credentials = True,
    allow_methods=['*'],
    allow_headers=['*']
        )

#@app.get("/")
#    return "hello world"

@app.get("/default_temperature/{default_value}")
def main(default_value: int):
    default_value = default_value
    return default_temperature(arduino, default_value)

@app.get("/temperature_management")
def main():
    return temperature_management(arduino)


@app.get("/filtering_management")
def main():
    return filtering_management(arduino)


#@app.get("/default_feeding/{default_value}")
#def main(default_value: int):
    #default_value = default_value
    #return default_feeding(default_value)

@app.get("/feeding_management")
def main():
    return feeding_management(arduino)
```

# 4. ReactJs Programming

```

import './App.css';
import axios from 'axios';
import React, {useEffect} from 'react';
import { createGlobalStyle } from 'styled-components';
import Template from './Component/Template';
import ButtonTemplate from './Component/ButtonTemplate';
import TempTemplate from '../src/Component/ButtonItems/TempTemplate'
import FilteringTemplate from '../src/Component/ButtonItems/FilteringTemplate'
import FeedingTemplate from './Component/ButtonItems/FeedingTemplate'

import { useState
 } from 'react';
const GlobalStyle = createGlobalStyle`
  body {
    background: #e9ecef;
  }
`;

const App =() => {
  
  const [tempToggle, setTempToggle] = useState(false);
  const [filteringToggle, setFilteringToggle] = useState(false);
  const [feedingToggle, setFeedingToggle] = useState(false);

  const requestAPI = (route) => {
    
    if(route === "temperature_management"){
      setTempToggle(prev => !prev);
    }
    if(route === "filtering_management"){
      setFilteringToggle(prev => !prev);
    }
    if(route === "feeding_management"){
      setFeedingToggle(prev => !prev);
    }

    // e.preventDefault();
    console.log("?????? ?????? ?????????");
  }

  // const shutdownItem = () => {
  //   setTempToggle(false);
  //   setFilteringToggle(false);
  //   setFeedingToggle(false);
  // }
  return (
    <>
      <GlobalStyle />
      <Template>
        {tempToggle && (<TempTemplate/>)}
        {filteringToggle && (<FilteringTemplate />)}
        {feedingToggle && (<FeedingTemplate />)}

        <div onClick={()=>requestAPI("temperature_management")}><ButtonTemplate >??????</ButtonTemplate></div>
        <div onClick={()=>requestAPI("filtering_management")}><ButtonTemplate>??????</ButtonTemplate> </div>
        <div onClick={()=>requestAPI("feeding_management")}><ButtonTemplate>??????</ButtonTemplate> </div>
        </Template>
    </>
  );
}

export default App;

```

# 5. Test Code (Arduino)





