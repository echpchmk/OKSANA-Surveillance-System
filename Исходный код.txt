import numpy as np
import cv2
from pynput import keyboard
from pynput import mouse
import datetime
import time
from tkinter import *
import threading

def mishka():
    def on_move(x,y):
        with open('input_log.txt', 'a') as f:
            f.write('mouse is moved at {0}\n'.format(
                datetime.datetime.now()))

        with open('time_log.txt', 'a') as ft:
            ft.write('{0}\n'.format(
                time.time()))
    with mouse.Listener(on_move=on_move) as listener:
        listener.join()

def klava():
    def on_press(key):
        try:
            with open('input_log.txt', 'a') as f:
                f.write('key is pressed at {0}\n'.format(
                datetime.datetime.now()))

            with open('time_log.txt', 'a') as ft:
                ft.write('{0}\n'.format(
                    time.time()))

        except AttributeError:
            with open('input_log.txt', 'a') as f:
                f.write('key is pressed at {0}\n'.format(
                datetime.datetime.now()))

            with open('time_log.txt', 'a') as ft:
                ft.write('{0}\n'.format(
                    time.time()))

    with keyboard.Listener(on_press=on_press) as listener:
        listener.join()

print('Добро пожаловать в OKSANA Surveillance Systems!')
print('Введите допустимое время бездействие сотрудника(в секундах)')
a = float(input())


def camera():
    faceCascade = cv2.CascadeClassifier('haarcascade_frontalface_alt2.xml')

    cap = cv2.VideoCapture(0)
    cap.set(3, 640)  # set Width
    cap.set(4, 480)  # set Height
    while True:
        ret, img = cap.read()
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        faces = faceCascade.detectMultiScale(
            gray,
            scaleFactor=1.2,
            minNeighbors=5,
            minSize=(20, 20)
        )

        for (x, y, w, h) in faces:
            cv2.rectangle(img, (x, y), (x + w, y + h), (255, 0, 0), 2)
            roi_gray = gray[y:y + h, x:x + w]
            roi_color = img[y:y + h, x:x + w]
            with open('input_log.txt', 'a') as f:
                f.write('face is detected at {0}\n'.format(
                    datetime.datetime.now()))

            with open('time_log.txt', 'a') as ft:
                ft.write('{0}\n'.format(
                    time.time()))

        cv2.imshow('video', img)

        k = cv2.waitKey(30) & 0xff
        if k == 27:  # press 'ESC' to quit
            break

    cap.release()
    cv2.destroyAllWindows()

def check():
    while True:
        f_read = open("time_log.txt", "r")
        last_line = f_read.readlines()[-1]
        if time.time() - float(last_line) > (a):
            window = Tk()
            window.title("ALARM")
            window.geometry('287x250')
            lbl = Label(window, text="Вы превысили время бездействия, введите пароль:")
            lbl.grid(column=0, row=0)
            txt = Entry(window, width=20)
            txt.grid(column=0, row=1)
            btn = Button(window, text="Ввод")
            btn.grid(column=0, row=2)
            window.mainloop()



t1 = threading.Thread(target=mishka, args=())
t2 = threading.Thread(target=klava, args=())
t3 = threading.Thread(target=camera, args=())
t4 = threading.Thread(target=check, args=())

t1.start()
t2.start()
t3.start()
t4.start()


t1.join()
t2.join()
t3.join()
t4.join()
