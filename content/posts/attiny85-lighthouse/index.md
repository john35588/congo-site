---
title: 'Attiny85 Lighthouse'
date: '2026-05-31'
#lastmod: '2026-05-31'
draft: false
ongoing: false
description: "A simple soldering project using an ATtiny85 microprocessor"
summary: "I made a simple lighthouse with wire, LEDs, a potentiometer, and an ATtiny85."
slug: "attiny85-lighthouse"
tags: ["soldering", "woodwork", "programming", "DIY", "3D printing"]
showDateUpdated: true
---

{{< figure
    src="lighthouse-complete.webp"
    alt="A copper wireframe lighthouse on a wood block with a dial for adjusting brightness"
    caption="My copper wireframe lighthouse"
>}}

I've seen a few wireframe soldering projects out there recently, and decided to try my hand at one. I decided a lighthouse was a pretty simple design that even I couldn't mess up, and I already had all the parts and tools I'd need.

I started with stripping and straightening the wire. I used some old 18 gauge wire I had laying around, and spent what seemed like forever stripping about 8-10 feet of it. Next, I drew on some graph paper the patterns I wanted, and carefully bent the wire to match my drawings. 

Soldering was harder that I had thought it would be. The copper is very conductive, so I burned myself a few times while holding it in place long enough to solder. I didn't do a perfect job at it, but i honestly came out looking better than I had hoped! 

Though I didn't have any one "plan" at the beginning, I didn't even think to use any sea glass until it was mostly soldered. I was thinking about how bright it would be and how I'd want something to soften the light when I remembered a pile of sea glass I had collected on my last trip to the beach! I carefully wrapped the edges of each piece in copper tape, then soldered them into place. They add color to the final design, and definitely soften the harsh light.

The wood block is a section of 2x4 from the shed that I hollowed out with a router then routed and sanded the top and sides of. I wanted a more rugged look, so I didn't do anything to finish it.

{{< figure
    src="circuit-prototype.webp"
    alt="The circuit design process on a breadboard"
    caption="Dialing in the circuit design and programming"
>}}

I'm no electrician, and I've never taken a class on circuitry, but I know how to get what I want from the internet if I'm determined enough. I had bought a several ATtiny85 microprocessors months before because I thought the idea of a tiny 8-pin chip that can be programmed to do whatever you can imagine was amazing! When I received them, however, I didn't know how to use them, and didn't have a project in mind to use them for, so they remained unused in my drawer until this project!

I had a few things in mind I wanted this circuit to do:
1. Use a potentiometer to dim the lighthouse's LED
2. Activate at the touch of a button
3. Run for a designated amount of time
4. Be powered through USB

I started by designing the circuit on a breadboard, then I wrote the program in the Arduino IDE. Here's the final code:
```c++
const int mainLed = 0;    // Main LED pin with brightness adjustment
const int led1 = 3;       // First indicator LED pin
const int led2 = 4;       // Second indicator LED pin
const int potPin = A1;    // Potentiometer pin, changes main LED brightness
const int buttonPin = 1;  // Button pin, sets Main LED runtime

const unsigned long DELAY_1 = 600000UL;   // 10 minutes
const unsigned long DELAY_2 = 1200000UL;  // 20 minutes
const unsigned long DELAY_3 = 1800000UL;  // 30 minutes

unsigned long remaining;   // Time remaining on timer
unsigned long timeNeeded;  // Time needed for LED to be turned off

bool ledEnabled = false;      // Start with main LED off
bool lastButtonState = HIGH;  // Start with button unpressed

void setup() { // Set pin modes
  pinMode(mainLed, OUTPUT);
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);

  analogWrite(mainLed, 0);
  digitalWrite(led1, LOW);
  digitalWrite(led2, LOW);

  remaining = 0;
  timeNeeded = 0;
}

void loop() { // Main program loop
  int potValue = analogRead(potPin); // Get potentiometer value
  
  // Make sure one button press isn't repeatedly registered
  bool buttonState = digitalRead(buttonPin);
  bool buttonPressed = (lastButtonState == HIGH && buttonState == LOW);
  lastButtonState = buttonState;
  
  if (millis() < timeNeeded) { // If the timer is still going
    remaining = timeNeeded - millis(); // Determine how many ms left to run
  } else {
    remaining = 0;
  }

  // Timer Set Logic
  if (buttonPressed && remaining > DELAY_2) {         // In the middle of DELAY_3, shutoff
    timeNeeded = millis();
  } else if (buttonPressed && remaining > DELAY_1) {  // In the middle of DELAY_2, set timeNeeded to DELAY_3
    timeNeeded = millis() + DELAY_3;
  } else if (buttonPressed && remaining > 0) {        // In the middle of DELAY_1, set timeNeeded to DELAY_2
    timeNeeded = millis() + DELAY_2;
  } else if (buttonPressed) {                         // Main LED off, set timeNeeded to DELAY_1
    timeNeeded = millis() + DELAY_1;
  }

  // Set Status LEDs
  if (remaining > DELAY_2) {         // Between DELAY_2 and DELAY_3 time left
    digitalWrite(led1, HIGH);
    digitalWrite(led2, HIGH);
  } else if (remaining > DELAY_1) {  // Between DELAY_1 and DELAY_2 time left
    digitalWrite(led1, LOW);
    digitalWrite(led2, HIGH);
  } else if (remaining > 0) {        // Between 1 and DELAY_1 time left
    digitalWrite(led1, HIGH);
    digitalWrite(led2, LOW);
  } else {                           // No time left, Main LED is off
    digitalWrite(led1, LOW);
    digitalWrite(led2, LOW);
  }

  // LED output
  if (remaining > 0) {
    // Convert 0-1023 to 6-255 PWM (too dim and the LED turns off)
    int brightness = map(potValue, 0, 1023, 6, 255);
    analogWrite(mainLed, brightness);
  } else {
    analogWrite(mainLed, 0);
  }
}
```

I'm not super familiar with C++, so I'm sure there are many things in my code that, to anyone who's used C++ before, looks questionable at best, but it works pretty well. 

In pseudocode, it basically has four states: off, on for 10 minutes, on for 20 minutes, and on for 30 minutes. Pressing the button cycles through the four states in that order, while a timer runs in the background that changes the states in the reverse order, stopping at off. There are two indicator LEDs that show what mode it is in:

|LED 1|LED 2|State|
|---|---|---|
|off|off|off|
|on|off|on for 10 minutes|
|off|on|on for 20 minutes|
|on|on|on for 30 minutes|

While in any of the "on" states, it takes the position of the potentiometer, and uses PWM to change the brightness of the main LED.

My original plan was to build the circuit inside the lighthouse so you could see the circuit, but luckily I didn't overestimate myself that much, and I hid it all inside the base. It looks admittedly messy, but I assure you, it is pretty sturdy. I used small screws to anchor bits of wire into place, and after taking this picture, I used hot glue to cover many of the wires so they cannot be accidentally shorted out. 

I had used a momentary pushbutton on the breadboard, but as I was putting everything together, I couldn't think of a way to get the pushbutton fit in with the rest of the project. I eventually decided to make my own "button" by having two pieces of copper wire connect when one is pushed. The tension of the wire itself pushes it back up. This actually worked out better because in the breadboard design, a single button press occasionally registered as two because the button would bounce when pressed.

The potentiometer has a dial made of wood-fill filament that I printed after failing several times to make one out of a dial. It's pretty difficult to create a centered, straight hole in a dowel without a drill press.

{{< figure
    src="circuit-final.webp"
    alt="The final circuit installed inside the wood block"
    caption="A bit messy, but working great!"
>}}

The final product looked and worked better than I thought it would! For the main LED, I used a flexible filament LED similar to the ones used in replica Edison bulbs. The entire lighthouse acts as a ground, and the single pole going up the middle to the LED carries the PWM signal from the ATtiny85.

I really enjoyed the diversity of this project, there was soldering, programming, a bit of rudimentary woodworking, circuit design, and 3D printing! I already have a few ideas for other designs to try, but it will probably be a while before I get to it again.

{{< figure
    src="lighthouse-top.webp"
    alt="Top view of the lighthouse showing the flexible filament LED"
    caption="The flexible LED used in the top of the lighthouse"
>}}