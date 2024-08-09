Chess Clock

Chess Clock is a custom-built timer designed to enhance your chess-playing experience by providing precise and user-friendly time management. The clock allows players to easily set, start, and switch between timers for each player, ensuring a fair and competitive game. One of the main challenges was implementing the timing logic and ensuring seamless transitions between the players' timers. The biggest takeaway from this project was learning how to integrate various hardware components, such as 4-digit displays, buttons, and a rotary encoder, to create a functional and intuitive device. The triumph came when the final product worked flawlessly, making chess games more enjoyable and professional.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Himank Y | Inglemoor High School | Computer Science | Incoming Junior

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# First Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/wO3R-TBcILA?si=5Ybzv92w3VzoDyQ5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My project is a dual timer system designed to function as a chess clock using an Arduino Uno. The system includes two 4-digit 7-segment displays for showing the countdown for each player, a rotary encoder for setting the timers, two push buttons for controlling the timers, and a buzzer to signal when a timer runs out. The two 4-digit 7-segment displays will show the remaining time for each player, controlled using the TM1637Display library. The rotary encoder will be used to set the timer values, allowing the user to adjust minutes and seconds. Two push buttons will be used to start and stop the timers for each player. A buzzer will sound when a player's time runs out, providing an audible alert. While building this the challenges that occured to me were understanding on how the code works as well as building functions. I had problems with setting up my buttons and wires at the right parts in my breadboard but I overcame all these challenges by debugging and finding the right locations. 



# Second Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/MwwKPFRAE7w?si=GW7Uv67nV-3V-pZB" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Since the first milestone, I've integrated the rotary encoder for precise timer adjustments and configured dual 4-digit displays to show countdowns for two players. The `setNum` function now accurately updates timer values based on encoder input, while `displayBlink` enhances user interaction during timer setup.Challenges overcome involved fine-tuning timer accuracy and debugging display output issues. Next, I need to add other visual elements such as led lights for whoever's turn it is when the timer is going off.
# Final Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/3euiwd_tP4U?si=xuXW1swdLQO4Fplp" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In this project, I successfully integrated multiple hardware components and software functions to create a fully functional dual-timer system. The timers are controlled individually by buttons, each capable of incrementing its corresponding timer. This system was designed to enhance user interaction through a clear display and intuitive controls. One of the primary challenges was synchronizing the displays and ensuring responsive interactions with the buttons. Overcoming these challenges was crucial for my understanding of both hardware and Arduino programming, which deepened my skills in microcontroller environments and real-time system responses. This not only improved my technical capabilities but also enhanced my problem-solving skills, setting a strong foundation for future projects in embedded systems design.


# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c
#include <TM1637Display.h>

// Encoder
#define outputA 3
#define outputB 4
#define enSw 5

// 4-digit display pins (Digital Pins)
#define CLKA 13
#define DIOA 12
#define CLKB 8
#define DIOB 6
TM1637Display displayA(CLKA, DIOA);
TM1637Display displayB(CLKB, DIOB);

// Button, LED, buzzer
#define btA 11
#define btB 10
#define buzz 2
#define ledA 9
#define ledB 7

#define speakerPin 0 

uint8_t segOff[] = {0, 0, 0, 0};

int timerSet = 0;
int aState;
int aLastState;
int modeState = 0;
int modeStateLast = 0;

unsigned long minSetA = 5;
unsigned long secSetA = 0;
unsigned long minSetB = 5;
unsigned long secSetB = 0;
unsigned long timeO = 0;
unsigned long timeA = 0;
unsigned long timeB = 0;

const unsigned long increment = 5000; // 5 seconds increment in milliseconds

int enSwStatus = 1;
int enSwStatusLast = 1;
int btAStatus = 1;
int btAStatusLast = 1;
int btBStatus = 1;
int btBStatusLast = 1;

void setup() {
  Serial.begin(9600);

  displayA.setBrightness(4);
  displayA.clear();
  displayB.setBrightness(4);
  displayB.clear();
  delay(1000);

  pinMode(btA, INPUT_PULLUP);
  pinMode(btB, INPUT_PULLUP);
  pinMode(buzz, OUTPUT);

  pinMode(ledA, OUTPUT);
  pinMode(ledB, OUTPUT);
  digitalWrite(ledA, LOW);
  digitalWrite(ledB, LOW);

  pinMode(outputA, INPUT);
  pinMode(outputB, INPUT);
  pinMode(enSw, INPUT_PULLUP);
  aLastState = digitalRead(outputA);
  digitalWrite(buzz, LOW);
}

void loop() {
  switch (modeState) {
    case 0:
      digitalWrite(buzz, LOW);
      digitalWrite(ledA, LOW);
      digitalWrite(ledB, LOW);
      displayA.showNumberDecEx(minSetA * 100 + secSetA, 0b01000000, true, 4, 0);
      displayB.showNumberDecEx(minSetB * 100 + secSetB, 0b01000000, true, 4, 0);
      enSwStatus = digitalRead(enSw);
      btAStatus = digitalRead(btA);
      btBStatus = digitalRead(btB);
      if (enSwStatus == 0 && enSwStatusLast == 1) {
        modeState = 1;
      }
      if (btAStatus == 0 && btAStatusLast == 1) {
        modeState = 11;
        timeO = millis();
        timeA = 0;
        timeB = 0;
      }
      if (btBStatus == 0 && btBStatusLast == 1) {
        modeState = 12;
        timeO = millis();
        timeA = 0;
        timeB = 0;
        playSound(); // Play sound when button B is clicked
      }
      btAStatusLast = btAStatus;
      btBStatusLast = btBStatus;
      enSwStatusLast = enSwStatus;
      break;

    case 1:
      enSwStatus = digitalRead(enSw);
      minSetA = setNum(minSetA);
      displayBlink("A", 0);
      if (enSwStatus == 0 && enSwStatusLast == 1) {
        modeState = 2;
      }
      enSwStatusLast = enSwStatus;
      break;

    case 2:
      enSwStatus = digitalRead(enSw);
      secSetA = setNum(secSetA);
      displayBlink("A", 2);
      if (enSwStatus == 0 && enSwStatusLast == 1) {
        modeState = 3;
      }
      enSwStatusLast = enSwStatus;
      break;

    case 3:
      enSwStatus = digitalRead(enSw);
      minSetB = setNum(minSetB);
      displayBlink("B", 0);
      if (enSwStatus == 0 && enSwStatusLast == 1) {
        modeState = 4;
      }
      enSwStatusLast = enSwStatus;
      break;

    case 4:
      enSwStatus = digitalRead(enSw);
      secSetB = setNum(secSetB);
      displayBlink("B", 2);
      if (enSwStatus == 0 && enSwStatusLast == 1) {
        modeState = 0;
      }
      enSwStatusLast = enSwStatus;
      break;

    case 11:
      timeA = millis() - timeO;
      if (timeOut() == 1) {
        modeState = 13;
      }
      displayA.showNumberDecEx(timeDisplay(timeA, "A"), 0b01000000, true, 4, 0);
      displayB.showNumberDecEx(timeDisplay(timeB, "B"), 0b01000000, true, 4, 0);
      
      // Blink LED A
      if (millis() % 500 < 250) {
        digitalWrite(ledA, HIGH);
      } else {
        digitalWrite(ledA, LOW);
      }

      btAStatus = digitalRead(btA);
      if (btAStatus == 0 && btAStatusLast == 1) {
        timeO = millis() - timeA;
        minSetA += 5 / 60;
        secSetA += 5 % 60;
        if (secSetA >= 60) {
          secSetA -= 60;
          minSetA += 1;
        }
        modeState = 12;
      }
      btAStatusLast = btAStatus;
      enSwStatus = digitalRead(enSw);
      if (enSwStatus == 0 && enSwStatusLast == 1) {
        modeState = 0;
      }
      enSwStatusLast = enSwStatus;
      break;

    case 12:
      timeB = millis() - timeO;
      if (timeOut() == 1) {
        modeState = 13;
      }
      displayA.showNumberDecEx(timeDisplay(timeA, "A"), 0b01000000, true, 4, 0);
      displayB.showNumberDecEx(timeDisplay(timeB, "B"), 0b01000000, true, 4, 0);
      
      // Blink LED B
      if (millis() % 500 < 250) {
        digitalWrite(ledB, HIGH);
      } else {
        digitalWrite(ledB, LOW);
      }

      btBStatus = digitalRead(btB);
      if (btBStatus == 0 && btBStatusLast == 1) {
        timeO = millis() - timeB;
        minSetB += 5 / 60;
        secSetB += 5 % 60;
        if (secSetB >= 60) {
          secSetB -= 60;
          minSetB += 1;
        }
        modeState = 11;
        playSound(); // Play sound when button B is clicked again
      }
      btBStatusLast = btBStatus;
      enSwStatus = digitalRead(enSw);
      if (enSwStatus == 0 && enSwStatusLast == 1) {
        modeState = 0;
      }
      enSwStatusLast = enSwStatus;
      break;

    case 13:
      analogWrite(buzz, 255);
      digitalWrite(ledA, LOW);
      digitalWrite(ledB, LOW);
      delay(100);
      analogWrite(buzz, 0);
      delay(100);
      enSwStatus = digitalRead(enSw);
      if (enSwStatus == 0 && enSwStatusLast == 1) {
        modeState = 0;
      }
      enSwStatusLast = enSwStatus;
      break;

    default:
      break;
  }
}

int setNum(int num) {
  aState = digitalRead(outputA);
  if (aState != aLastState) {
    if (digitalRead(outputB) != aState) {
      num++;
    } else {
      if (num > 0) {
        num--;
      }
    }
  }
  aLastState = aState;
  return num;
}

void displayBlink(String player, int pos) {
  if (player == "A") {
    if ((millis() * 5 / 1000) % 2 == 0) {
      displayA.setSegments(segOff, 4, pos);
    } else {
      displayA.showNumberDecEx(minSetA * 100 + secSetA, 0b01000000, true, 4, 0);
    }
  } else if (player == "B") {
    if ((millis() * 5 / 1000) % 2 == 0) {
      displayB.setSegments(segOff, 4, pos);
    } else {
      displayB.showNumberDecEx(minSetB * 100 + secSetB, 0b01000000, true, 4, 0);
    }
  }
}

long timeDisplay(long timeRun, String player) {
  long min_t = 0;
  long sec_t = 0;
  if (player == "A") {
    min_t = (minSetA * 60 + secSetA - timeRun / 1000) / 60;
    sec_t = (minSetA * 60 + secSetA - timeRun / 1000) % 60;
  } else if (player == "B") {
    min_t = (minSetB * 60 + secSetB - timeRun / 1000) / 60;
    sec_t = (minSetB * 60 + secSetB - timeRun / 1000) % 60;
  }
  return min_t * 100 + sec_t;
}

int timeOut() {
  if (timeA / 1000 >= (minSetA * 60 + secSetA) || timeB / 1000 >= (minSetB * 60 + secSetB)) {
    return 1;
  } else {
    return 0;
  }
}

// Function to play a sound on the speaker
void playSound() {
  tone(speakerPin, 1000, 500); // Play a 1000 Hz tone for 500 ms
}

```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs.1 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Elegoo Starter Kit | The starter kid is needed as its the motherboard to function the whole clock | $44.99 | <a href="https://www.amazon.com/ELEGOO-Project-Tutorial-Controller-Projects/dp/B01D8KOZF4/ref=sr_1_3_pp?crid=2CDRMQVSYOD4H&dib=eyJ2IjoiMSJ9.AcWZy-Yg4mDTnhzEHozxzPZdVC5-KUL2tW-OQewDKpA7ZhEVnlvYJFELmn1cEN5uvrZVxp4St_nlIhbtJibvxUj7s5mZJHZ5gTUoGHyjSCEJnV1m-a2PWxrXyWYqZrufz70WGPo3NV3-f7iFEXccDbUvJu8BRvjxPCjgkJ_uJnDxpPk3Gjw_yw7XMWWb8ll4GsOLKWrxeEr1uOS5BeD0EU2Y1MvZXAOcITMnykL7K69Fr4P6SnWB2EpkaJ37raPdYGD096Z_rSPyEsRfxIx6Rv9N65w4nRID980vI94aLYQ.PuIIln1now7ULLLgzCb0zPl5SBpDXd-IT05ToFwhflM&dib_tag=se&keywords=arduino+starter+kit&qid=1720652047&s=industrial&sprefix=arduino+starter+ki%2Cindustrial%2C72&sr=1-3/"> Link </a> |
| 9V Batteries| This is used to power the clock to function it | $12.36 | <a href="https://www.amazon.com/Amazon-Basics-Performance-All-Purpose-Batteries/dp/B00MH4QM1S/ref=sr_1_5_pp?crid=3TQ7ANPH958JM&dib=eyJ2IjoiMSJ9.bmcV2Upj_vpB6G9CFlPPxYAryat512da7ekZjc52HecXSTmtx7PbJ50EgQFPCMqlAxjOUq-tL4vQTpozlHvH89bMwx-HJoyGcdz6EY8HrMxahTiqOXkoP7ewkDcgHoMhmHamdlQfW6FBHO0Gm-DYZZnnMuvEU3qOpemA8PGEvRhEx4-lGaBZhrvls039G1-9SizAW-YRGXZ2fFrdVDlREyyOhAuxXZaE5QqUxWesRQgP9UfGOYaInRWTTPwhDbXFa-RPzGbU1C_u4wq-NMqKBtWEQqR9-cA8O3FYOx3icEY.dtKJmI2T-iCmMM_bYnbiHUWzhKpJDRxS-bBmZIwYFKM&dib_tag=se&keywords=9v+batteries&qid=1720651326&rdc=1&s=electronics&sprefix=9v+batteries%2Celectronics%2C105&sr=1-5"> Link </a> |
| 7 Segment Display | This item is used to display the time | $7.99 | <a href="https://www.amazon.com/WWZMDiB-Module%EF%BC%8CLED-Brightness-Adjustable-Accessories/dp/B0BFQNFX6D/ref=sr_1_1_sspa?crid=4FDIRL8C4Z3U&dib=eyJ2IjoiMSJ9.QgE2LsaDM6XMNWEKtfowv6-PbOSX2xhfnjc8ga6aSoZXfs-idNkA9Ry6gW5xXwdB3MQphqLpgoIsDnzgtIYvLkoepUfuY2uLPqicJqKDkKnboM0jQQrhz9AZOv5zmOdrUFJHY1qCBLICzeGzJsft3HwTDgR4Ii-2yY662dfn_flQ9naG88nPMjZhyYt8CB8IOtxmvV9G52EVmHU0NpiE8bhfPcV3h2EQ6aMis0izEmY.iv725XikNlgecuj747NmLUhRIykpvN1TY3xTLJ2YXbk&dib_tag=se&keywords=7+segment+display+4+digit&qid=1720652429&sprefix=7+segment+display%2Caps%2C94&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1"> Link </a> |
| Rotary Encoder` | This allows to easily set, start, and switch between timers for each player| $8.99 | <a href="https://www.amazon.com/SongHe-KY-040-Encoder-Development-Arduino/dp/B087ZQLLWQ/ref=sr_1_1_sspa?crid=31JIP47IPMIK8&dib=eyJ2IjoiMSJ9.561HXmjsvo5F8O9BqiyjaSi-8obSRX9raeAnvJo4oFw5OnkbeV1QRHHfVEvZIIJLpVAHEld3c3HBMBSr11X2zkFY26Q7hLI6ypqkbnw62s_tKxp_6V7hLdomEZTkGvssu6FbdS07oVElHEGBm6cr64bpEDsxVK--rsN238OB8Ze3hJgJ6H7n0Ghirxb4ApragEHxYvKmC3vD7RNFPLPw6rMIPYUELi-Mr8N375lKCQ6N3LwbhQWs1wxW_Z3ZDZq2F_8-O71LqIw9qhxzVTC-yNQ4zZdoWF9itE-o3bjbb1k.hZzW7hz1jPqwJJMdFx4wTPmlBjlYlx5wvw79WBSj7qU&dib_tag=se&keywords=rotary+encoder&qid=1720652395&s=industrial&sprefix=rotary+encoder%2Cindustrial%2C84&sr=1-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1"> Link </a> |
| Fielect 2Pcs 0.5W 8 Ohm DIY Magnetic Speaker Arduino Round Internal Magnet Speaker Loudspeaker Replacement | This item is used for sound whenever the player clicks on a button a sound plays off| $6.99 | <a href="https://www.amazon.com/Fielect-Magnet-Speaker-Internal-Diameter/dp/B0826YLV6C/ref=sr_1_1?crid=1S95IIG65GFFJ&dib=eyJ2IjoiMSJ9.7rVScTfTCbTp4d5573lwZ0PxeRLW61PQzlHBdpSWsVNiHE-9osFsZUmXRI4nItGthZ3RfzLZv7PPY5_Mv7mJPw9Wh0Bh4AraGMapXRenytos8jJZ2w1smRv3m1V-p7jugi0w9c3dr1suay99OpbtnIA8398UEU6v27Vb3_U_XvYsQSTnyaMCq5_ikhQfKUee8fa9_7vlSrLF4c38yuY0BXZXildrWjLWIw-DQtzx6BI.C7V45MVq5rdnFFxV8aYkjhdEPCOpiZrJ5LYplLPCbwg&dib_tag=se&keywords=fielect+2pcs+0.5w+8+ohm+diy+magnetic+speaker&qid=1722014226&sprefix=fielect+2pcs+0.5w+8+ohm+dit+magnetic+speaker%2Caps%2C55&sr=8-1"> Link </a> |


# Other Resources/Examples
Here were the tutorials that were used!
- [Tutorial 1]([https://projecthub.arduino.cc/herolivechannel/chess-alarm-clock-using-arduino-rotary-encoder-7-segment-1511f2])


To watch the BSE tutorial on how to create a portfolio, click here.
