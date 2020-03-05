

/* The ESP32 has four SPi buses, however as of right now only two of
 * them are available to use, HSPI and VSPI. Simply using the SPI API 
 * as illustrated in Arduino examples will use VSPI, leaving HSPI unused.
 * 
 * However if we simply intialise two instance of the SPI class for both
 * of these buses both can be used. However when just using these the Arduino
 * way only will actually be outputting at a time.
 * 
 * SPI TESTBENCH FOR TI DAC8551
 * 
 */
#include <SPI.h>

#define LED_BUILTIN 2

static const int spiClk = 1000000; // 1 MHz
byte Byte1 = 0;
byte Byte2 = 0;
byte Byte3 = 0;
int dly = 1000;
int low = 0;
int high = 65535;
int delta = 1;
int steps = 65535;
String raw;
String rawData;
String tempRaw;
word valueDAC = 0;

//uninitalised pointers to SPI objects
SPIClass * vspi = NULL;
SPIClass * hspi = NULL;

void setup() {
  // initialize digital pin LED_BUILTIN as an output.
  pinMode(LED_BUILTIN, OUTPUT);
  
  Serial.begin(9600); // opens serial port, sets data rate to 9600 bps
  
  //initialise two instances of the SPIClass attached to VSPI and HSPI respectively
  vspi = new SPIClass(VSPI);
  hspi = new SPIClass(HSPI);
  
  //clock miso mosi ss

  //initialise vspi with default pins
  //SCLK = 18, MISO = 19, MOSI = 23, SS = 5
  vspi->begin();
  //alternatively route through GPIO pins of your choice
  //hspi->begin(0, 2, 4, 33); //SCLK, MISO, MOSI, SS
  
  //initialise hspi with default pins
  //SCLK = 14, MISO = 12, MOSI = 13, SS = 15
  hspi->begin(); 
  //alternatively route through GPIO pins
  //hspi->begin(25, 26, 27, 32); //SCLK, MISO, MOSI, SS

  //set up slave select pins as outputs as the Arduino API
  //doesn't handle automatically pulling SS low
  pinMode(5, OUTPUT); //VSPI SS
  pinMode(15, OUTPUT); //HSPI SS

}

// the loop function runs over and over again until power down or reset
void loop() {
  
  if (Serial.available() > 0) {
    // read the incoming bytes as string
    raw = Serial.readString();
    tempRaw = raw;
    if (tempRaw.startsWith("$DAC,")){
      Serial.println("Set DAC value");
    } else if (tempRaw.startsWith("$HV,")){
      Serial.println("Set HV value");
    } else if (tempRaw.startsWith("$DACSW")){
      Serial.println("DAC sweep MODE");
    } else if (tempRaw.startsWith("$DLY,")){
      Serial.println("Set delay step");
    } else if (tempRaw.startsWith("$HELP")){
      Serial.println("DAC HELP");
      Serial.println("$DAC,<int value>");
      Serial.println("$HV,<int value>");
      Serial.println("$DACSW \t <int low> <int high> <int step>");
      Serial.println("$DLY,<int value>");
      Serial.println("$LOW,<int value>");
      Serial.println("$HIGH,<int value>");
      Serial.println("$STEP,<int value>");
    }
  }

  if (tempRaw.startsWith("$DACSW")){
      sweepDAC();
    }

  if (tempRaw.startsWith("$DLY,")){
    //Serial.println(tempRaw);

    // Extract numbers after ','
    //Serial.println(tempRaw.indexOf(","));
    rawData = tempRaw.substring(tempRaw.indexOf(",") + 1);
    Serial.println(rawData);

    //if (isDigit(raw)) {
      // convert the incoming byte to a char and add it to the string:
      //rawData += (char)raw;
      
    // int value of string of numbers
    // valueDAC type WORD 16 BITS
    dly = rawData.toInt();
    tempRaw = "";
      
    }

  if (tempRaw.startsWith("$LOW,")){
    //Serial.println(tempRaw);

    // Extract numbers after ','
    //Serial.println(tempRaw.indexOf(","));
    rawData = tempRaw.substring(tempRaw.indexOf(",") + 1);
    Serial.println(rawData);

    //if (isDigit(raw)) {
      // convert the incoming byte to a char and add it to the string:
      //rawData += (char)raw;
      
    // int value of string of numbers
    // valueDAC type WORD 16 BITS
    low = rawData.toInt();
    tempRaw = "";  
    }

  if (tempRaw.startsWith("$HIGH,")){
    //Serial.println(tempRaw);

    // Extract numbers after ','
    //Serial.println(tempRaw.indexOf(","));
    rawData = tempRaw.substring(tempRaw.indexOf(",") + 1);
    Serial.println(rawData);

    //if (isDigit(raw)) {
      // convert the incoming byte to a char and add it to the string:
      //rawData += (char)raw;
      
    // int value of string of numbers
    // valueDAC type WORD 16 BITS
    high = rawData.toInt();
    tempRaw = "";
    }

  if (tempRaw.startsWith("$STEP,")){
    //Serial.println(tempRaw);

    // Extract numbers after ','
    //Serial.println(tempRaw.indexOf(","));
    rawData = tempRaw.substring(tempRaw.indexOf(",") + 1);
    Serial.println(rawData);

    //if (isDigit(raw)) {
      // convert the incoming byte to a char and add it to the string:
      //rawData += (char)raw;
      
    // int value of string of numbers
    // valueDAC type WORD 16 BITS
    steps = rawData.toInt();
    tempRaw = "";
    }
  
  if (tempRaw.startsWith("$DAC,")){
    //Serial.println(tempRaw);

    // Extract numbers after ','
    //Serial.println(tempRaw.indexOf(","));
    rawData = tempRaw.substring(tempRaw.indexOf(",") + 1);
    Serial.println(rawData);

    //if (isDigit(raw)) {
      // convert the incoming byte to a char and add it to the string:
      //rawData += (char)raw;
      
    // int value of string of numbers
    // valueDAC type WORD 16 BITS
    valueDAC = rawData.toInt();
    //Serial.println(valueDAC);
    Byte2 = highByte(valueDAC);
    Byte3 = lowByte(valueDAC);

    Serial.print(Byte1, BIN);
    Serial.print("\t");
    Serial.print(Byte2, BIN);
    Serial.print("\t");
    Serial.println(Byte3, BIN);
    
    //use the SPI buses
    delay(1000);
    vspiCommand();
    tempRaw = ""; 
  }

  if (tempRaw.startsWith("$HV,")){
    //Serial.println(tempRaw);

    // Extract numbers after ','
    //Serial.println(tempRaw.indexOf(","));
    rawData = tempRaw.substring(tempRaw.indexOf(",") + 1);
    Serial.println(rawData);

    //if (isDigit(raw)) {
      // convert the incoming byte to a char and add it to the string:
      //rawData += (char)raw;
      
    // int value of string of numbers
    // valueDAC type WORD 16 BITS
    valueDAC = rawData.toInt();
    //Serial.println(valueDAC);
    Byte2 = highByte(valueDAC);
    Byte3 = lowByte(valueDAC);

    Serial.print(Byte2, BIN);
    Serial.print("\t");
    Serial.println(Byte3, BIN);
    
    //use the SPI buses
    delay(dly);
    hspiCommand();
    tempRaw = "";  
  }
}

void sweepDAC() {
  int delta = (high - low)/steps;
  int value;
  Serial.print("LOW: ");
  Serial.println(low);
  Serial.print("HIGH: ");
  Serial.println(high);
  Serial.print("STEPS: ");
  Serial.println(steps);
  Serial.print("DELTA: ");
  Serial.println(delta);
  for (int i = 0; i <= steps ; i++) {
    if (low == 0){
      low = 0;
    }
    if (high == 0){
      high = 65535;
    }
    if (delta == 0){
      delta = 1;
    }
    value = low + i*delta;
    digitalWrite(LED_BUILTIN, HIGH);   // turn the LED on (HIGH is the voltage level)
    delay(dly);                       // wait for a second
    digitalWrite(LED_BUILTIN, LOW);    // turn the LED off by making the voltage LOW
    delay(dly);
    // valueDAC type WORD 16 BITS
    valueDAC = value;
    //Serial.println(valueDAC);
    Byte2 = highByte(valueDAC);
    Byte3 = lowByte(valueDAC);
    delay(dly);
    Serial.print("DAC value: ");
    Serial.println(value);
    Serial.print(Byte2, BIN);
    Serial.print("\t");
    Serial.println(Byte3, BIN);
    vspiCommand();
  }
  
}

void vspiCommand() {
  byte Byte1 = 0b00000011; // junk data to illustrate usage
  //byte Byte2 = 0b00110011; // junk data to illustrate usage
  //byte Byte3 = 0b01010101; // junk data to illustrate usage

  //use it as you would the regular arduino SPI API
  vspi->beginTransaction(SPISettings(spiClk, MSBFIRST, SPI_MODE1));
  digitalWrite(5, LOW); //pull SS slow to prep other end for transfer
  vspi->transfer(Byte1);
  vspi->transfer(Byte2);  
  vspi->transfer(Byte3);    
  digitalWrite(5, HIGH); //pull ss high to signify end of data transfer
  vspi->endTransaction();
}

void hspiCommand() {
  byte Byte1 = 0b00000011;
  //byte Byte2 = 0b00110011;
  //byte Byte3 = 0b11001100;
  
  hspi->beginTransaction(SPISettings(spiClk, MSBFIRST, SPI_MODE1));
  digitalWrite(15, LOW);
  hspi->transfer(Byte1);
  hspi->transfer(Byte2);
  hspi->transfer(Byte3);
  digitalWrite(15, HIGH);
  hspi->endTransaction();
}
