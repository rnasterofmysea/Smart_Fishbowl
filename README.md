# Fishbowl-Auto-Management-System

# SYSTEM IDEA

![image](https://user-images.githubusercontent.com/81907470/197466414-c1bfe1f7-9bcd-4f10-b52c-d9b15ba873e8.png)


## 1. Water Temperature Management System


![image](https://user-images.githubusercontent.com/81907470/206219565-e28d9a52-3fb6-4740-8f07-bcbf217cd535.png)

  - Setting defualt temperature cased by fish type
  - Measuring water temperature apart from water heater
  - Monitoring by senior monitor and controling survo motor to handel heater
  
## 2. Feeding System

![image](https://user-images.githubusercontent.com/81907470/206219646-1be88c6e-332e-40e3-ba05-1c2e1bc68f5a.png)


  - Set feeding time
  - Controling servo motor to dorp feed from the box
  
## 3. Filtering System

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



# 3. Fast API Programming



# 4. ReactJs Programming







