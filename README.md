# fhss
fhss with arduino
#include<SPI.h>
#include "nRF24L01.h"
#include "RF24.h"
#include "printf.h"

RF24 radio(7, 8);

#define ledPin 4
const uint64_t add1 = 0x0a0c0c0aLL;
uint32_t inTime = 0;
byte  idx = 0, frame_cntr = 0;
byte fhop[] = {11,13,15,17,19,21,23,25,27,29,31,33,35,37,39,41,43,45,47,49,51,53,55,57};
byte fhopgam = 0;
byte text[20] = {0xaa, 0xaa, 0xaa, 0xaa, 0xFA, 0x70, 0x10, 0x11, 0x12, 0x13, 0x14, 0x15, 0x16, 0x17, 0x18, 0x19, 0x1A, 0x1B, 0x00, 0x00};



void setup()
{
  //pinMode(ledPin, OUTPUT);
  radio.begin();
  Serial.begin(115200);
 // printf_begin();
  radio.setPALevel(RF24_PA_HIGH);
  radio.setDataRate(RF24_250KBPS);
  radio.setChannel(fhop[fhopgam]);
  radio.openWritingPipe(add1);
  radio.stopListening();
  //radio.setAutoAck (false); 
  //  TCCR1A = 0;
  //  TCCR1B = 0;
  //  TCNT1  = 0;
  //  OCR1A = 25;
  //  TCCR1B |= (1 << WGM12);
  //  TCCR1B |= (1 << CS11);
  //  TCCR1B |= (1 << CS10);
  //  TIFR1 |= _BV (OCF1A);
  //  TIMSK1 = _BV (OCIE1A);
  //radio.printDetails();
}

void loop()
{
  // put your main code here, to run rep atedly:
  if ((micros() - inTime) > 60000)
  {
    //digitalWrite(ledPin, HIGH);
    inTime = micros();//get time
    frame_cntr++;
    text[6] = fhop[fhopgam];
    text[7] = frame_cntr;
    //chk_sum();
    radio.write(&text, sizeof(text), 0);
    while (1) //wait for 1mSec
    {
      if ((micros() - inTime) > 1000)
        break;
    }
    fhopgam = (fhopgam + 1) % 24;
    radio.setChannel(fhop[fhopgam]);
    Serial.println(fhop[fhopgam]);
  }
}


//void chk_sum(void)
//{
//  uint16_t cs = 0;
//  for (int8_t i = 6; i < (sizeof(text)-2) ; i++)
//    cs += text[i];
//  text[18] = cs;
//  text[19] = cs >> 8;//void chk_sum(void)
//}
