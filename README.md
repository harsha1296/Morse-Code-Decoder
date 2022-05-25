# Morse-Code-Decoder
Morse code is a method of transmitting text information as a series of on-off tones, light or clicks that can be directly understood by a skilled listener of observer without special equipment. It is named for Samuel F.B. Morse, an inventor of telegraph. Each Morse code symbol represents either a text character (letter or numeral) or a prosing and is represented by unique sequence of dots and dashes. The duration of dash is three times the duration of dot. Each dot or dash is followed by a short silence, equal to the dot duration. The letters of a word are separated by a space equal to three dots( one dash) and the word are separated by space equal to seven dots. The dots duration is the basic unit of time measurement in code transmission. To increase the speed of the communication the code was designed so that the length of each character in Morse varies approximately inversely to its frequency of occurrence in English. Thus the most common letter in English, the letter “E” has the shortest code, a single dot. Compared to voice, Morse code is less sensitive to poor signal conditions yet still comprehensible to humans without a decoding device. Morse is, therefore, a useful alternative to synthesized speech for sending automated data to skilled listeners on voice channels.
 
DESCRIPTION AND IMPLEMENTATION
INPUT: The input to the system is given by a push button. Input was given in the following format:-
1.	If the push button is pressed for less than 3 seconds the microcontroller reads it as a dot.
2.	If the push button is pressed for 3 or more than 3 seconds the microcontroller reads it as dash.
3.	If the push button is released for less than 3 seconds the microcontroller reads it as a spacing between dot and dash.
4.	If the push button is released for more than 3 but less than 7 seconds microcontroller reads it as spacing between two letters.
5.	If the push button is released for more than 10 seconds microcontroller understands that the transmission of morse code is over and displays the message “ Completed” on the LCD display.
 
OUTPUT: The output will be shown on a 16x2 LCD display on the receiver.

TRANSMISSION: The transmission of morse code is done using RF module. It works at a frequency of 434 Khz. It is the the simplest form of transmitting device. It transmits only a high signal ‘1/Vcc’ or a low signal ‘0/ground’. Encoder IC HT12E encodes the transmittted data and Decoder HT12D decodes the received data.

MORSE CODE DECODING: The decoding of the morse code sent to the receiver is done form the morse tree. We use level traversing method for tree traversal.
 
METHOD IMPLEMENTED: To measure the time for which the push button was pressed or released we use the concept of timers and counters. Atmega32 has the following timers:
1.	Timer 0,8 bit
2.	Timer 1,16 bit consisting of two 8 bit parts 1,A and B
3.	Timer 2, 8 bit
Now there are three two clocks: 
1.	System clock (fs): This is the clock frequency at which Atmega is running. By default it is 1 MHz which can be changed by using setting fuse bits.
2.	Timer clock (ft): This is the clock frequency at which timer module is running. Each timer module has different clocks.
Now ft can be in ratios of fs. That is, ft=fs,fs/8,fs/64 …
For example, if we keep fs = 8 MHz then available options for ft are: 8 MHz, 1 MHz, 125 KHz .
There are total 4 settings for Timers, 
1. Clock Source: Source for timer clock, keep it as system 
clock. You can also provide external clock. Read datasheet 
for more information about external clock source. 

2. Clock value: This is value of ft. Drop down for available 
options of ft. Chose whichever is required 

3. Mode: There are many modes of timers. We will be 
discussing following 2 modes, 

a. Fast PWM top = FFh 
b. CTC top=OCRx (x=0, 1A, 2) 

4. Output: Depending upon the mode we have chosen there 
are options for output pulse. We will look in detail later. 
Basically each Timer has a counter unit with size 8 bit for Timer 0, 2 and 16 bit for Timer 1. I will be talking about Timer 0 and same will follow for other Timers. 
Each counter has a register which increments by one on every rising edge of timer clock. After counting to its full capacity, 255 for 8 bit, it again starts from 0. By using this register we can have different modes. 

1. Timer/Counter (TCNT0) and Output Compare Register (OCR0) are 8-bit registers. 

2. TOP: The counter reaches the TOP when it becomes equal to the highest value in the count sequence. The TOP value can be assigned to be the fixed value 0xFF (MAX) or the value stored in the OCR0 Register. The assignment is dependent on the mode of operation. 

FAST PWM MODE:
PWM = Pulse Width Modulation. 
This mode is used to generate pulse with 
1.	Fixed Frequency (F) 
2.	Variable Duty Cycle (D) 
F = Ft / 256 
D = OCR0 / 255 (non inverted) 
D = (255-OCR0) / 255 (inverted)
By changing OCR0 value we can change the duty cycle of the output pulse. 
As OCR0 is an 8bit register it can vary from 0 o 255. 
0 ≤ OCR0 ≤ 255
Pins for Output pulse, 
Timer 0 : OC0, pin 4 
Timer 1A : OC1A, pin 19 
Timer 1B : OC1B, pin 18 
Timer 2 : OC2, pin 21
 
CTC MODE:
CTC = Clear Timer on Compare Match. 
This mode is to generate pulse with, 
1.	Fixed Duty Cycle (D = 0.5) 

2.	 Variable Frequency (F) 

F = 𝐟𝐭 𝟐/ (𝑶𝑪𝑹𝟎+𝟏) 
D = 0.5
By changing value of OCR0 we can change the value of output pulse frequency. As OCR0 is an 8bit register it can vary from 0 to 255. 
0 ≤ OCR0 ≤ 255
 
To detect that that a data has been received the concept of interrupt is used.
There are 3 external interrupts in Atmega 16. They are 
• INT0 : PD2, Pin 16 
• INT1 : PD3, Pin 17 
• INT2 : PB2, Pin 3 

There are 3 modes of external Interrupts, 
1. Low Level: In this mode interrupt occurs whenever it 
detects a ‘0’ logic at INT pin. To use this, you should put 
an external pull up resistance to avoid interrupt every 
time. 

2. Falling Edge: In this mode interrupt occurs whenever it detects a falling edge that is ‘1’ to ‘0’ logic change at INT pin. 

3. Rising Edge: In this mode interrupt occurs whenever it detects a falling edge that is ‘0’ to ‘1’ logic change at INT pin. 

After generating the code, you can see the following function :- 
```
// External Interrupt 0 service routine
interrupt [EXT_INT0] void ext_int0_isr(void)
{
//Place your code here
}
```
You can place your code in the function, and the code will be executed whenever interrupt occurs.
 
CONCLUSION: 
The basic project has been developed using Atmega32 and RF module for designing a morse code decoder. Message in morse code can be sent from sender to receiver who would receive the morse code in decoded form i.e. in the form of letters and numbers which will get displaced on the LCD display.
