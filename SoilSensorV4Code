//BME280 configuration
#include <SparkFunBME280.h>
BME280 airSensor;

//Thingspeak client configuration
#include <ThingSpeak.h>
TCPClient client;
unsigned int myChannelNumber = YOUR_CHANNEL_ID_HERE; // replace with your ChannelID
const char * myWriteAPIKey = "YOUR_API_KEY_HERE"; // replace with your WriteAPIKey

//FreedomPop Configuration -- if you use Particle SIM, you do not need this
STARTUP(cellular_credentials_set("fp.com.attz", "", "", NULL));

//This allows us to wait to connect to cellular until we want to
SYSTEM_MODE(SEMI_AUTOMATIC);

//Configure DS18B20 waterproof temp sensor
#include <DS18B20.h>
#include <math.h>
const int      MAXRETRY          = 4;
DS18B20  ds18b20(D2, true); //Sets Pin D2 for Water Temp Sensor and 
char     szInfo[64];

#define TMP36Sig A5 //Pin that TMP36 analog output is connected to
#define PowerPin A3 //Pin that drives NPN transistor to power sensors
#define soilSenseCapPin A4 //Pin that Capacitance sensor is connected to

PMIC pmic; //Charge Controller
FuelGauge fuel; //Battery Monitor


float soilSenseCap;
float airTemp;
float airPressure;
float airHumidity;
float groundTemp;
float enclosureTemp;
float voltage;
float fuelLevel;
float chargeStatus;

void setup() {
    //Turn off RGB Led to conserve power
    RGB.control(true);
    RGB.color(0,0,0);
    
    //Initialize Serial Communication
    Serial.begin(57600);

    //switch NPN transistor to power sensors
    pinMode(PowerPin, OUTPUT);
    digitalWrite(PowerPin, HIGH);

    //Configure Charge Controller for Solar Panel
    pmic.begin();
    pmic.setChargeCurrent(0,0,1,0,0,0);
    pmic.setInputVoltageLimit(4840);
    chargeStatus = pmic.getSystemStatus();
    
            Serial.print("Charge Status: ");
            Serial.println(chargeStatus);

    //Configure BME280
  	airSensor.settings.commInterface = I2C_MODE;
	airSensor.settings.I2CAddress = 0x76; //may be 0x77 depending on your sensor
	airSensor.settings.runMode = 3; //Normal mode
	airSensor.settings.tStandby = 0;
	airSensor.settings.filter = 4;
	airSensor.settings.tempOverSample = 5;
    airSensor.settings.pressOverSample = 5;
	airSensor.settings.humidOverSample = 1;
	delay(10); // BME280 takes <5ms to power up
	airSensor.begin();
  
    //Initialize ThingSpeak client
    ThingSpeak.begin(client);
}

void loop() {

    if(fuel.getSoC() > 20){
        Serial.println("");
        getAirTemp();
        getGroundTemp();
        getEnclosureTemp();
        getSoilData();
        getBatteryLevel();
        publishData();
    }
    
    System.sleep(SLEEP_MODE_DEEP, 600);
}

void getAirTemp(){
    float airTempC = airSensor.readTempC();
    airTemp = airSensor.readTempF();
    airPressure = airSensor.readFloatPressure();
    airHumidity = airSensor.readFloatPressure();
    
	        Serial.print("Temperature: ");
	        Serial.print(airTemp, 2);
        	Serial.println(" degrees F");

        	Serial.print("Pressure: ");
        	Serial.print(airPressure, 2);
        	Serial.println(" Pa");

        	Serial.print("%RH: ");
        	Serial.print(airHumidity, 2);
        	Serial.println(" %");
}

void getGroundTemp(){
    float _temp;
    float celsius;
    int   i = 0;

    do {
        _temp = ds18b20.getTemperature();
    } while (!ds18b20.crcCheck() && MAXRETRY > i++);

    if (i < MAXRETRY) {
        celsius = _temp;
        groundTemp = ds18b20.convertToFahrenheit(_temp);
            
            Serial.print("Gnd Temp (F): ");
            Serial.println(groundTemp);
    }
    else {
        celsius = groundTemp = NAN;
            
            Serial.println("Invalid reading");
  }
}

void getEnclosureTemp() {
    float enclosureTempC = (analogRead(TMP36Sig)*3300/4096-500);
    float enclosureTempF = enclosureTempC*9/5+320;
    enclosureTemp = enclosureTempF/10;
    
            Serial.print("Enc Temp (F): ");
            Serial.println(enclosureTemp);
}

void getSoilData(){
    soilSenseCap = analogRead(soilSenseCapPin);
    
            Serial.print("Soil Sense: ");
            Serial.println(soilSenseCap);
}

void getBatteryLevel() {
    fuelLevel = fuel.getSoC()*100/80;
    voltage = fuel.getVCell();
    
            Serial.print("Battery (V): ");
            Serial.println(voltage);
            Serial.print("Battery (%): ");
            Serial.println(fuelLevel);
}

void publishData(){
            Serial.println("Publishing now.");
    
    Cellular.on();
    Cellular.connect();
    Particle.connect();
    
        ThingSpeak.setField(1, airTemp);
        ThingSpeak.setField(2, airPressure);
        ThingSpeak.setField(3, groundTemp);
        ThingSpeak.setField(4, enclosureTemp);
        ThingSpeak.setField(5, soilSenseCap);
        ThingSpeak.setField(6, chargeStatus);
        ThingSpeak.setField(7, voltage);
        ThingSpeak.setField(8, fuelLevel);
        ThingSpeak.writeFields(myChannelNumber, myWriteAPIKey);
        
    Particle.process();
    delay(5000);
    Particle.disconnect();
    Cellular.off();
}