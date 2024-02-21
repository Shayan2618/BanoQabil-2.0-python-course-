from curses import init_color
import pygame
import json
import random
import sys


width , height = 800, 600
FPS =30


win=pygame.display.set_mode((width,height))
pygame.display.set_caption("Typing Test")
pygame.init()


background = pygame.image.load("BKG.jpg")
background=pygame.transform.scale(background,(width,height))

Title_colour=(249,231,159)
prompt_colour=(88,214,141)
Rect_colour=(133,193,233)
input_colour=(93,173,226)
result_colour=(236,112,99)

class user:
    def __init__(self):
        self.prompt= self.get_sentence()
        self.input=""
        self.start_time=0
        self.time_taken=0
        self.timer_started= False
        self.end=False
        self.acurcacy=0
        self.wpm=0
        self.result=""


def get_sentence( self):
    data=json.load (open("words.json"))
    dat=random.choice(data,k=10)
    return "".join(dat)
        



def draw_text(text,message_color,font_size,y_cord, surface):
    font = pygame.font.Font("./FiraCode-VariableFont_wght.ttf",font_size)
    text = font.render(text,1,message_color)
    text_rect = text.get_rect( center=(width/2,y_cord))
    surface.blit(text,text_rect)



    
def draw_surface(surface):
    surface.fill((0,0,0))
    surface.blit(background,(0,0))
    draw_text("Typing Test",40,100)
    if user.prompt :
        if len(user.prompt) >= 60:
            lis=user.prompt.split( )
            w1=" ".join(lis[:len(lis)//2])
            w2=" ".join(lis[len(lis)//2:])
            draw_surface(surface,w1,200,20,prompt_colour)
            draw_surface(surface,w2,240,20,prompt_colour)
    surface.fill((0,0,0)(50,300,700)) 
    pygame.draw.rect(surface,Rect_colour,(50,300,700,80),2) 

    if user.input:
        if len(user.input) >= 45:
            lis=user.input.split( )
            w1=" ".join(lis[:len(lis)//2])
            w2=" ".join(lis[len(lis)//2:])
            draw_surface(surface,w1,325,17,input_colour)
            draw_surface(surface,w2,355,17,input_colour)
        else:
            draw_text(surface,user.input,325,17,input_colour)  
    if user.end:
        draw_text(surface,user.result,500,22,result_colour)
        pygame.display.update()   


running = True

clock=pygame.time.Clock()
from typing import User

user = User()


def reset():
    user.start_time,user.time_taken=0,0
    user.timer_started,user.end=False,False
    user.prompt,user.input=get_sentence(),""
    user.accuracy,user.wpm=0,0
    user.result=""

import time

def show_results():
    if user.timer_started and not user.end:
        user.time_taken=time.time()-user.start_time

        count=0
        for i ,c in enumerate(user.prompt):
            try:
                if c==user.input[i]:
                    count+=1
            except IndexError:
                pass

    user.accuracy=count/len(user.prompt) * 100  
    user.wpm=len(user.input)/5/user.time_taken*60
    user.end=True
    user.result=f"Time:{round(user.time_taken)}||Accuracy:{round(user.accuracy,2)}%||WPM:{round(user.wpm,2)}"
    print(user.result)

    
while running:
    clock.tick(FPS)
    draw_surface(win)
    for event in pygame.event.get():
        if event.type==pygame.QUIT:
            running=False
            sys.exit()

        elif event.type==pygame.MOUSEBUTTONUP:
            if not user.timer_started:
                user.start_time=time.time()
                user.timer_started=True
            x,y=pygame.mouse.get_pos()
            if user.end:
                # reset_game() 
                pass
        elif event.type ==pygame.KEYDOWN:
            if user.timer_started and not user.end:
                if event.key==pygame.K_RETURN:
                    show_results()
            elif event.key  ==pygame.K_BACKSPACE:
                user.input=user.input[:-1]
     
            else:
                try:
                    user.input+=event.unicode
                except:
                    pass
            if not user.timer_started and not user.end:
                user.start_time=time.time()
                user.timer_started=True
                try: 
                    user.input+=event.unicode
                except:
                    pass
        pygame.display.update()

