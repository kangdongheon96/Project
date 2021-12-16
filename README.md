# Project
Robotvision

import cv2
import imutils
import numpy as np
import pytesseract
from PIL import Image


img = cv2.imread('Capture.png',cv2.IMREAD_COLOR)
cv2.imshow('Car',img)
cv2.waitKey()

img = imutils.resize(img, width=300)

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
cv2.imshow('Gray',gray)
cv2.waitKey()

gray = cv2.bilateralFilter(gray, 11, 17, 17) 
cv2.imshow('Gaussian Blur',gray)
cv2.waitKey()

edged = cv2.Canny(gray, 30, 500) 
cv2.imshow('Canny',edged)
cv2.waitKey()

cnts,new = cv2.findContours(edged.copy(), cv2.RETR_LIST, cv2.CHAIN_APPROX_SIMPLE)
img1=img.copy()
cv2.drawContours(img1,cnts,-1,(0,255,0),3)
cv2.imshow("img1",img1)
cv2.waitKey(0)


cnts = sorted(cnts, key = cv2.contourArea, reverse = True)[:30]
screenCnt = None 
img2 = img.copy()
cv2.drawContours(img2,cnts,-1,(0,255,0),3) 
cv2.imshow("img2",img2) 
cv2.waitKey(0)
count=0
idx=7

for c in cnts:
      
  
        peri = cv2.arcLength(c, True)
        approx = cv2.approxPolyDP(c, 0.018 * peri, True)
        if len(approx) == 4:
                screenCnt = approx
                x,y,w,h = cv2.boundingRect(c)
                new_img=img[y:y+h,x:x+w]
                cv2.imwrite('./'+str(idx)+'.png',new_img) 
                idx+=1
                break
              

cv2.drawContours(img, [screenCnt], -1, (0, 255, 0), 3)
cv2.imshow("Final image with plate detected",img)
cv2.waitKey(0)
Cropped_loc='./7.png'
cv2.imshow("cropped",cv2.imread(Cropped_loc)) 
text=pytesseract.image_to_string(Cropped_loc,lang='kor',config='--psm 7 --oem 0') 
print("Number is : " ,text)
cv2.waitKey(0)
cv2.destroyAllWindows() 
          
