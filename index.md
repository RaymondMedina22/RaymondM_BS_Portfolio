# Raspberry Pi Ball Tracking Robot
Robot uses computer vision to identify the color of the ball alongside using a ultrasonic sensor to determine the distance of the ball

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Raymond M| University of Wisconsin - Madison | Industrial Engineering | Sophomore 

![Headstone Image](https://user-images.githubusercontent.com/86113507/124283036-734ca400-db19-11eb-981c-386359f8b74b.jpeg)
![Headstone Image](https://cdn-shop.adafruit.com/970x728/4296-11.jpg)

# Final Milestone

In my second milestone, I was able to complete my base project in which I did this by using a raspberry camera to set up an open cv for the project. I started by setting up the ultrasonic sensor so the robot could determine its distance from the ball and how far it needs to go. After this, it was to set up a code that could bring everything together. First I needed to code cv to identify the ball based on its shape and color, in this case, a red ball. After coding the movement of the motors so the robot would be able to turn correctly based on where the ball is located. On top of this, I include a led light that lights up when the ball is too close. The end result, after putting everything was a robot that could track a red ball 

[![Second Milestone](https://res.cloudinary.com/marcomontalbano/image/upload/v1625231523/video_to_markdown/images/youtube--hLWgj4PqETg-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://www.youtube.com/watch?v=hLWgj4PqETg&t=1s "Second Milestone"){:target="_blank" rel="noopener"}
# First Milestone 

My first milestone was setting up the four motors used for my robot making sure they were wired and coded correctly through raspberry pi. First I set up the raspberry OS to the raspberry pi to run python. After this, I wired the motor driver to the raspberry making sure the wires were connected to the correct pins. Which is important when coding the movement of the motors. After this, I wired the motor driver to the four motors and four pack battery holder as a power source for the motor driver. After setting up the wiring I used python in pi to coded the motors to be able to go forward all at the same time.

[![First Milestone](https://res.cloudinary.com/marcomontalbano/image/upload/v1624628589/video_to_markdown/images/youtube--I0sU-SC-8pI-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://www.youtube.com/watch?v=I0sU-SC-8pI "First Milestone"){:target="_blank" rel="noopener"}

# Presentation

[![Final Presentation ](https://res.cloudinary.com/marcomontalbano/image/upload/v1625231891/video_to_markdown/images/youtube--iUbbWPSFT7o-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://www.youtube.com/watch?v=iUbbWPSFT7o "Final Presentation "){:target="_blank" rel="noopener"}

# Reflection

After completing this project it showed me that there are many challenges that are faced when trying to do engineering from mechanical problems to having to do code debugging. However, the challenges that are faced when engineering are enjoyable and fun, essential the excitement that is felt when your solving your problem.

# Circuit Diagram of Control System

![Image 6-25-21 at 11 08 AM](https://user-images.githubusercontent.com/86113507/123448241-b1d9e000-d5a8-11eb-912a-e35c41b8e109.jpg)
# Code
    from picamera.array import PiRGBArray  
    from picamera import PiCamera
    import RPi.GPIO as GPIO
    import time
    import cv2
    import cv2 as cv
    import numpy as np

    #hardware work
    GPIO.setmode(GPIO.BOARD)
    GPIO.setwarnings(False)

    GPIO_TRIGGER = 29     # Ultrasonic sensor
    GPIO_ECHO = 31

    MOTOR1B= 13 #Right Motor
    MOTOR1E= 15

    MOTOR2B= 5  #Left Motor
    MOTOR2E= 7

    LED_PIN= 33  

    # Set pins as output and input
    GPIO.setup(GPIO_TRIGGER,GPIO.OUT)  # Trigger
    GPIO.setup(GPIO_ECHO,GPIO.IN)      # Echo
    GPIO.setup(LED_PIN,GPIO.OUT)
    # Set trigger to False
    GPIO.output(GPIO_TRIGGER, False)

      def sonar(GPIO_TRIGGER,GPIO_ECHO):
      start=0
      stop=0
      # Set pins as output and input
      GPIO.setup(GPIO_TRIGGER,GPIO.OUT)  # Trigger
      GPIO.setup(GPIO_ECHO,GPIO.IN)      # Echo
      #Set trigger to False (Low)
      GPIO.output(GPIO_TRIGGER, False)
      
      # Sensor setup
      time.sleep(0.01)
      #while distance > 5:
      #Send 10us pulse to trigger
      GPIO.output(GPIO_TRIGGER, True)
      time.sleep(0.00001)
      GPIO.output(GPIO_TRIGGER, False)
      begin = time.time()
      while GPIO.input(GPIO_ECHO)==0 and time.time()<begin+0.05:
            start = time.time()
      while GPIO.input(GPIO_ECHO)==1 and time.time()<begin+0.1:
            stop = time.time()
      # Calculate pulse length
      elapsed = stop-start
      # Distance pulse travelled in that time is time
      # multiplied by the speed of sound (cm/s)
      distance = elapsed * 34000
     
      # Distance 
      distance = distance / 2
     
      print ("Distance : %.1f" % distance)
      # Reset GPIO settings
      return distance
    #Motor setup 
    GPIO.setup(MOTOR1B, GPIO.OUT)
    GPIO.setup(MOTOR1E, GPIO.OUT)

    GPIO.setup(MOTOR2B, GPIO.OUT)
    GPIO.setup(MOTOR2E, GPIO.OUT)

    def forward():
      GPIO.output(MOTOR1B, GPIO.LOW)
      GPIO.output(MOTOR1E, GPIO.HIGH)
      GPIO.output(MOTOR2B, GPIO.LOW)
      GPIO.output(MOTOR2E, GPIO.HIGH)
     
    def reverse():
      GPIO.output(MOTOR1B, GPIO.HIGH)
      GPIO.output(MOTOR1E, GPIO.LOW)
      GPIO.output(MOTOR2B, GPIO.HIGH)
      GPIO.output(MOTOR2E, GPIO.LOW)
     
    def rightturn():
      GPIO.output(MOTOR1B,GPIO.HIGH)
      GPIO.output(MOTOR1E,GPIO.LOW)
      GPIO.output(MOTOR2B,GPIO.LOW)
      GPIO.output(MOTOR2E,GPIO.HIGH)
     
    def leftturn():
      GPIO.output(MOTOR1B,GPIO.LOW)
      GPIO.output(MOTOR1E,GPIO.HIGH)
      GPIO.output(MOTOR2B,GPIO.HIGH)
      GPIO.output(MOTOR2E,GPIO.LOW)

    def stop():
      GPIO.output(MOTOR1E,GPIO.LOW)
      GPIO.output(MOTOR1B,GPIO.LOW)
      GPIO.output(MOTOR2E,GPIO.LOW)
      GPIO.output(MOTOR2B,GPIO.LOW)
     
    #Image Analysis
    def segment_colour(frame):  
    hsv_roi =  cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    mask_1 = cv2.inRange(hsv_roi, np.array([160, 160,10]), np.array([190,255,255]))
    ycr_roi=cv2.cvtColor(frame,cv2.COLOR_BGR2YCrCb)
    mask_2=cv2.inRange(ycr_roi, np.array((0.,165.,0.)), np.array((255.,255.,255.)))

    mask = mask_1 | mask_2
    kern_dilate = np.ones((8,8),np.uint8)
    kern_erode  = np.ones((3,3),np.uint8)
    mask= cv2.erode(mask,kern_erode)      #Eroding
    mask=cv2.dilate(mask,kern_dilate)     #Dilating
    #cv2.imshow('mask',mask)
    return mask

    def find_blob(blob): 
    largest_contour=0
    cont_index=0
    contours, hierarchy = cv2.findContours(blob, cv2.RETR_CCOMP, cv2.CHAIN_APPROX_SIMPLE)
    for idx, contour in enumerate(contours):
        area=cv2.contourArea(contour)
        if (area >largest_contour) :
            largest_contour=area
           
            cont_index=idx
            #if res>15 and res<18:
            #    cont_index=idx
                              
    r=(0,0,2,2)
    if len(contours) > 0:
        r = cv2.boundingRect(contours[cont_index])
       
    return r,largest_contour
    def target_hist(frame):
    hsv_img=cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
   
    hist=cv2.calcHist([hsv_img],[0],None,[50],[0,255])
    return hist

    #Camera Setup
    #initialize the camera and grab a reference to the raw camera capture
    camera = PiCamera()
    camera.resolution = (160, 128)
    camera.framerate = 16
    rawCapture = PiRGBArray(camera, size=(160, 120))
 
    # Camera start up
    time.sleep(0.001)
 
    # Camera Analysis
    for image in camera.capture_continuous(rawCapture, format="bgr", use_video_port=True):
      #grab the raw NumPy array representing the image, then initialize the timestamp and occupied/unoccupied text
      frame = image.array
      frame = cv2.flip(frame,1)
      global centre_x
      global centre_y
      centre_x=0.
      centre_y=0.
      hsv1 = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
      mask_red=segment_colour(frame)      #masking red the frame
      loct,area=find_blob(mask_red)
      x,y,w,h=loct
     
      #distance coming from front ultrasonic sensor
      distanceC = sonar(GPIO_TRIGGER,GPIO_ECHO)
             
      if (w*h) < 10:
            found=0
      else:
            found=1
            simg2 = cv2.rectangle(frame, (x,y), (x+w,y+h), 255,2)
            centre_x=x+((w)/2)
            centre_y=y+((h)/2)
            cv2.circle(frame,(int(centre_x),int(centre_y)),3,(0,110,255),-1)
            centre_x-=80
            centre_y=6--centre_y
            print (centre_x,centre_y)
      initial=400
      flag=0
      GPIO.output(LED_PIN,GPIO.LOW)   
      #Motor Movement 
      if(found==0):#If the ball is not found
        flag==0
        stop()
        time.sleep(0.05)
               if (distanceC<10:    
                    stop()
                    time.sleep(1)
                    else:
                        forward()
                        flag=0            
      elif(found==1):
            if(area<initial):
                  if(distanceC<10):
                        forward()
                        time.sleep(0.00625)
            elif(area>=initial):
                  initial2=6700
                  if(area<initial2):
                        if(distanceC>10):
                              flag=0
                              forward()
                              time.sleep(0.025)
                              #Center ball to camera's imaginary axis.
                              if(centre_x<=-20):
                                    if(centre_x>=-20 or centre_x>=20):
                                          flag=0
                                          rightturn()
                                          time.sleep(0.025)
                                    elif(centre_x>=-20 or centre_x>=20):
                                          flag=1
                                          leftturn()
                                          time.sleep(0.025)
                              forward()
                              time.sleep(0.00003125)
                              stop()
                              time.sleep(0.00625)
                        else:
                              stop()
                              time.sleep(0.01)

                  else:
                        #if it founds the ball and it is too close it lights up the led.
                        GPIO.output(LED_PIN,GPIO.HIGH)
                        time.sleep(0.1)
                        stop()
                        time.sleep(0.1)
      #cv2.imshow("draw",frame)    
      rawCapture.truncate(0)  # clear the stream in preparation for the next frame
         
      if(cv2.waitKey(1) & 0xff == ord('q')):
            break
            

    GPIO.cleanup() 
