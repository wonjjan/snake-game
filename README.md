import pstats
import pygame
from pygame.locals import QUIT, KEYDOWN, K_LEFT, K_RIGHT, K_DOWN, K_UP
import sys
from random import randint as ri


# 일반먹이 0 70% 
# 상점200 1 10% 
# 빨라짐 2 10% 
# 스피드초기화 3 5%
# 방향키 4 5%


pygame.init()

W, H = 20, 20
SUR = pygame.display.set_mode((W*40,H*40))
FPS = pygame.time.Clock()


S = [(W//2, H//2)]
F = []
E = []


def add_food():
    while True:
        pos = ri(0,W-1), ri(0,H-1)
        if pos in S or pos in F:
            continue
        F.append(pos)

        per = ri(1,100)
        if per <= 70:
            E.append(0)
        elif per <= 80:
            E.append(1)
        elif per <= 90:
            E.append(2)
        elif per <= 95:
            E.append(3)
        else:
            E.append(4)
        break

def move(idx):
    del F[idx]
    del E[idx]
    add_food()


for i in range(10):
    add_food()


key = K_DOWN
game_over = False

mess = pygame.font.SysFont(None, 80)
rev = False
sp = 3
score = 0

while True:
    SUR.fill((0,0,0))
    
    for i in pygame.event.get():
        if i.type == QUIT:
            pygame.quit()
            sys.exit()
        elif i.type == KEYDOWN:
            key = i.key

    if not game_over:

        if rev:
            if key == K_UP:
                head = S[0][0], S[0][1] + 1
            elif key == K_DOWN:
                head = S[0][0], S[0][1] - 1
            elif key == K_LEFT:
                head = S[0][0] + 1, S[0][1]
            elif key == K_RIGHT:
                head = S[0][0] - 1, S[0][1]
        else:
            if key == K_UP:
                head = S[0][0], S[0][1] - 1
            elif key == K_DOWN:
                head = S[0][0], S[0][1] + 1
            elif key == K_LEFT:
                head = S[0][0] - 1, S[0][1]
            elif key == K_RIGHT:
                head = S[0][0] + 1, S[0][1]
        
        
        if head in S or head[0] < 0 or head[0] > 19 or head[1] < 0 or head[1] > 19:
            game_over = True 
        
        S.insert(0, head)
        if head in F:
            idx = F.index(head)
            if rev:
                rev = False
                if key == K_UP:
                    key = K_DOWN
                elif key == K_DOWN:
                    key = K_UP
                elif key == K_RIGHT:
                    key = K_LEFT
                elif key == K_LEFT:
                    key = K_RIGHT

            if E[idx] == 1:
                score += 100
            elif E[idx] == 2:
                sp += 4
            elif E[idx] == 3:
                sp = 3
            elif E[idx] == 4:
                rev = True
                if key == K_UP:
                    key = K_DOWN
                elif key == K_DOWN:
                    key = K_UP
                elif key == K_RIGHT:
                    key = K_LEFT
                elif key == K_LEFT:
                    key = K_RIGHT
            move(idx)
            sp += 1
            score += 100
        else:
            S.pop()
        
        
        

        # 선그리기
        # pygame.draw.line(SUR, (255,0,0), (0,0), (W,H), 3)
        # pygame.draw.line(SUR, (255,0,0), (0,H), (W,0), 5)

        for i in range(1,H):
            pygame.draw.line(SUR, (255,255,255), (0, i*40), (W*40, i*40))

        for i in range(1,W):
            pygame.draw.line(SUR, (255,255,255), (i*40, 0), (i*40, H*40))
        

        for i in S:
            if rev:
                pygame.draw.rect(SUR, (255,0,0), (i[0]*40,i[1]*40,40,40))
            else:
                pygame.draw.rect(SUR, (0,255,0), (i[0]*40,i[1]*40,40,40))
            # pygame.draw.rect(surf, (색깔), (startx, starty, width, height))

        # F : 먹이위치 (튜플) 들의 리스트
        for num,i in enumerate(F,0):
            if E[num] == 0:
                pygame.draw.ellipse(SUR, (255,255,255), (i[0]*40, i[1]*40, 40, 40))
            elif E[num] == 1:
                pygame.draw.ellipse(SUR, (0,0,255), (i[0]*40, i[1]*40, 40, 40))
            elif E[num] == 2:
                pygame.draw.ellipse(SUR, (100,100,100), (i[0]*40, i[1]*40, 40, 40))
            elif E[num] == 3:
                pygame.draw.ellipse(SUR, (200,200,200), (i[0]*40, i[1]*40, 40, 40))
            elif E[num] == 4:
                pygame.draw.ellipse(SUR, (255,0,0), (i[0]*40, i[1]*40, 40, 40))   
    else:
        m = mess.render(f"YOUR SCORE {score}!", True, (255,255,255))
        mx, my = m.get_width(), m.get_height()
        SUR.blit(m, (W*40//2-mx//2, H*40//2-my//2))


    pygame.display.flip()
    FPS.tick(sp)

