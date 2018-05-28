# SmartAttendance
#include <SPI.h>
#include <MFRC522.h>

#define id "AndroidAP"
#define pw "meen7906"
#define addr "rfid-iot490.eu-gb.mybluemix.net"
#define SS_PIN 10
#define RST_PIN 9
MFRC522 mfrc522(SS_PIN, RST_PIN);

const char *ok = "OK";
void setup() {
  // put your setup code here, to run once:
  Serial.begin(115200);
  Serial.println("AT");
  delay(3000);
  analogReference(INTERNAL);
  Serial.println("AT+CWMODE=1");
  delay(2000);
  String conn=String("AT+CWJAP=\"")+id+"\",\""+pw+"\"";
  Serial.println(conn);
  delay(3000);
  SPI.begin();      // Initiate  SPI bus
  mfrc522.PCD_Init();   // Initiate MFRC522
  Serial.println("Approximate your card to the reader...");
  Serial.println();
}

void loop() {
  // put your main code here, to run repeatedly:
  // Look for new cards
  if ( ! mfrc522.PICC_IsNewCardPresent()) 
  {
    return;
  }
  // Select one of the cards
  if ( ! mfrc522.PICC_ReadCardSerial()) 
  {
    return;
  }
  //Show UID on serial monitor
  Serial.print("UID tag :");
  String content= "";
  byte letter;
  for (byte i = 0; i < mfrc522.uid.size; i++) 
  {
     Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
     Serial.print(mfrc522.uid.uidByte[i], HEX);
     content.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
     content.concat(String(mfrc522.uid.uidByte[i], HEX));
  }
  Serial.println();
  Serial.print("Message : ");
  content.toUpperCase();
  if (content.substring(1) == "65 57 E7 D9") //change here the UID of the card/cards that you want to give access
  {
    Serial.println("Can Karaca");
    Serial.println();
    delay(3000);
  }
  else if(content.substring(1) == "B3 B9 26 90") //change here the UID of the card/cards that you want to give access
  {
    Serial.println("Yigit Yalcinkaya");
    Serial.println();
    delay(3000);
  }
 
 else   {
    Serial.println("Guest Student");
    delay(3000);
  }
  yolla(content.substring(1));
}

void yolla(String msg){
  Serial.println(String("AT+CIPSTART=\"TCP\",\"") + addr+ "\",80");
  delay(1000);
  if(Serial.find((char*)"Error")){
    Serial.println("AT+CIPSTART Error");
    return;
  }
  msg.replace(" ","");
  String yollanacakkomut = "GET /data?&field1=";   
  yollanacakkomut += msg;                   
  yollanacakkomut += " HTTP/1.1";
  yollanacakkomut += "\r\n";
  yollanacakkomut += "Host: rfid-iot490.eu-gb.mybluemix.net";
  yollanacakkomut += "\r\n";
  yollanacakkomut += "Accept: */*"; 
  yollanacakkomut +="\r\n\r\n";      
  delay(10000);

  Serial.print("AT+CIPSEND=");
  Serial.println(yollanacakkomut.length()+2);
  delay(3000);


  Serial.print(yollanacakkomut);
  Serial.print("\r\n\r\n");
 
}
