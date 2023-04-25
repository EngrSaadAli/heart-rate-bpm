/*
Heart Rate Measurement in BPM
This code measures heart rate in beats per minute (BPM) using an analog sensor.
References:

https://www.arduino.cc/en/Tutorial/BuiltInExamples/AnalogInOutSerial
https://learn.sparkfun.com/tutorials/pulse-oximeter-and-heart-rate-monitor
*/
int UpperThreshold = 518;           // Upper threshold for detecting a pulse
int LowerThreshold = 490;           // Lower threshold for detecting a pulse
int reading = 0;                    // Current analog sensor reading
float BPM = 0.0;                    // Heart rate in beats per minute
bool IgnoreReading = false;         // Flag to ignore readings when a pulse is detected
bool FirstPulseDetected = false;    // Flag to track if the first pulse has been detected
unsigned long FirstPulseTime = 0;   // Timestamp of the first pulse
unsigned long SecondPulseTime = 0;  // Timestamp of the second pulse
unsigned long PulseInterval = 0;    // Time interval between pulses

void setup() {
  Serial.begin(115200);  // Initialize serial communication
}

void loop() {

  reading = analogRead(A0);  // Read the sensor value

  // If a pulse is detected and we're not ignoring readings
  if (reading > UpperThreshold && IgnoreReading == false) {
    // If this is the first pulse detected
    if (FirstPulseDetected == false) {
      FirstPulseTime = millis();  // Record the time of the first pulse
      FirstPulseDetected = true;
    } else {
      SecondPulseTime = millis();                        // Record the time of the second pulse
      PulseInterval = SecondPulseTime - FirstPulseTime;  // Calculate the time interval between pulses
      FirstPulseTime = SecondPulseTime;                  // Update the first pulse time
    }
    IgnoreReading = true;  // Ignore further readings until pulse is over
  }

  // If the reading is below the lower threshold, reset the IgnoreReading flag
  if (reading < LowerThreshold) {
    IgnoreReading = false;
  }

  // Calculate BPM based on the pulse interval
  BPM = (1.0 / PulseInterval) * 60.0 * 1000;

  // Output the BPM value to the serial monitor
  Serial.print(BPM);
  Serial.println(" BPM");
  Serial.flush();
}
