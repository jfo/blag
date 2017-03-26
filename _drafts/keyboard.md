---
title: Keybord
layout: post
---

http://hackaday.com/2012/06/29/turning-an-arduino-into-a-usb-keyboard/

step one:

send a keycode to the computer every 2 seconds.

```arduino
void setup() {
  pinMode(12, INPUT_PULLUP);
  digitalWrite(12, HIGH);

  Keyboard.begin();
  Serial.begin(9600);
}

void loop() {
  Serial.println(digitalRead(12));
  if (digitalRead(12) == 1) {
    Keyboard.print("a");
  }
  delay(2000);
}
```


