# space
#lemme try this huehuehue

import pygame
import random
import math

# Initialized pygame
pygame.init()
# Create the screen
game_screen = pygame.display.set_mode((800, 600)) #(width x[left to right], height y[top to bottom])

# Game Title and Icon
pygame.display.set_caption("Space Wars")
icon = pygame.image.load('spaceship.png')
pygame.display.set_icon(icon)

# INITIALIZED TIME
clock = pygame.time.Clock()

# LOAD IMAGES
# Game Background Image
game_background = pygame.image.load('space.png')
# Player Image
user_Img = pygame.image.load('spaceship.png')
# Bullet Image
bullet_Img = pygame.image.load('bullet_1.png')
# Special Item Image
specialItem_Img = pygame.image.load('special_item.png')
# Enemy bot Image
enemy_Img = pygame.image.load('enemy.png')

# Initialized bullet state and Player score
bullet_state = "ready"

#(109, 247, 45) active green
#(52, 235, 119) inactive bright green

def text_objects(text, font):
    text_Surface = font.render(text, True, (255, 255, 255))
    return text_Surface, text_Surface.get_rect()

def game_quit():
    pygame.quit()
    quit()

def game_button(message, x, y, wdth, hght, inactive_msg, active_msg, action = None):
    mouse = pygame.mouse.get_pos()
    click = pygame.mouse.get_pressed()

    if x + wdth > mouse[0] > x and y + hght > mouse[1] > y:
        pygame.draw.rect(game_screen, active_msg, (x, y, wdth, hght))
        if click[0] == 1 and action != None:
            action()
    else:
        pygame.draw.rect(game_screen, inactive_msg, (x, y, wdth, hght))

    green_Text = pygame.font.SysFont("comicsansms", 20)
    Text_Surf, Text_Rect = text_objects(message, green_Text)
    Text_Rect.center = ((x + (wdth / 2)), (y + (hght / 2)))
    game_screen.blit(Text_Surf, Text_Rect)

def game_intro():
    intro = True
    while intro:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
        game_screen.fill((0, 0, 0))
        game_screen.blit(game_background, (0, 0))
        title_text = pygame.font.SysFont("comicsansms", 36)
        Text_Surf, Text_Rect = text_objects("Welcome to Space War", title_text)
        Text_Rect.center = (400, 250)
        game_screen.blit(Text_Surf, Text_Rect)

        # MENU BUTTONS
        game_button("PLAY!", 250, 300, 80, 30, (52, 235, 119), (109, 247, 45), game_loop)
        game_button("QUIT", 475, 300, 80, 30, (235, 55, 52), (252, 0, 0), game_quit)

        pygame.display.update()

def game_paused():
    pause = True
    while pause:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
        game_screen.fill((0, 0, 0))
        game_screen.blit(game_background, (0, 0))
        title_text = pygame.font.SysFont("comicsansms", 36)
        Text_Surf, Text_Rect = text_objects("PAUSED", title_text)
        Text_Rect.center = (400, 250)
        game_screen.blit(Text_Surf, Text_Rect)
        game_button("NEW GAME", 180, 300, 120, 30, (52, 235, 119), (186, 255, 74), game_loop)
        game_button("QUIT", 500, 300, 80, 30, (235, 55, 52), (255, 189, 82), game_quit)
        game_button("RESUME", 345, 300, 100, 30, (4, 72, 219), (61, 233, 242), None)
        pygame.display.update()

def special_Item(x, y):
    game_screen.blit(specialItem_Img, (x, y))

def player(x, y):
    game_screen.blit(user_Img, (x, y))

def fire_bullet(x, y):
    global bullet_state
    bullet_state = "fire"
    game_screen.blit(bullet_Img, (x + 8, y + 10))

def enemy(x, y):
    game_screen.blit(enemy_Img, (x, y))

def isCollision(enemyx, enemyy, bulletx, bullety):
    distance = math.sqrt(((enemyx - bulletx)**2) + ((enemyy - bullety)**2))
    if distance < 32:
        return True
def player_score(score_p):
    score = str(score_p)
    title_text = pygame.font.SysFont("comicsansms", 36)
    Text_Surf, Text_Rect = text_objects("Score: " + score, title_text)
    Text_Rect.center = (100, 36)
    game_screen.blit(Text_Surf, Text_Rect)

# Game Loop
def game_loop():
    # Player initialization
    playerX = 370
    playerY = 545
    playerX_change = 0
    playerY_change = 0

    # Bullet initialization
    bulletX = 370
    bulletY = 545
    bulletX_change = 0
    bulletY_change = 5

    # Special Item initialization
    strt_spLitem_X = random.randrange(20, 780)
    strt_spLitem_Y = -700
    spLitem_speed = 2

    # Enemy Initialization
    enemyX = random.randint(100, 768)
    enemyY = random.randint(0, 0)
    enemyX_change = 1
    enemyY_change = 20

    score = 0
    run = True
    while run:
        # Screen background
        game_screen.fill((0, 0, 0))
        game_screen.blit(game_background, (0, 0))

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                run = False

            # KEYSTROKE
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    playerX_change = -3
                if event.key == pygame.K_RIGHT:
                    playerX_change = 3
                if event.key == pygame.K_UP:
                    playerY_change = -3
                if event.key == pygame.K_DOWN:
                    playerY_change = 3
                if event.key == pygame.K_SPACE:
                    bulletX = playerX
                    bulletY = playerY
                    fire_bullet(playerX, bulletY)
                if event.key == pygame.K_ESCAPE:
                    game_paused()
            if event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT or event.key == pygame.K_UP or event.key == pygame.K_DOWN:
                    playerX_change = 0
                    playerY_change = 0

        # Player/Spaceship Movement
        playerX += playerX_change
        playerY += playerY_change
        player(playerX, playerY)

        # Boundary of the spaceship
        if playerX <= 0:
            playerX = 0
        elif playerX >= 768:
            playerX = 768
        if playerY <= 0:
            playerY = 0
        elif playerY >= 568:
            playerY = 568

        # Enemy Motion
        enemyX += enemyX_change
        if enemyX <= 0:
            enemyX_change = 1
            enemyY += enemyY_change
        elif enemyX >= 740:
            enemyX_change = -1
            enemyY += enemyY_change
        enemy(enemyX, enemyY)

        # Bullet motion
        if bullet_state is "fire":
            fire_bullet(bulletX, bulletY)
            bulletY -= bulletY_change

        # SPECIAL ITEM MOTION
        strt_spLitem_Y += spLitem_speed
        if strt_spLitem_Y > 5000:
            strt_spLitem_Y = 0 - 700
            strt_spLitem_X = random.randrange(30, 770)
        special_Item(strt_spLitem_X, strt_spLitem_Y)

        # COLLISION
        collision = isCollision(enemyX, enemyY, bulletX, bulletY)
        if collision:
            score += 1
            print(score)
            enemyX = random.randint(32, 768)
            enemyY = random.randint(0, 0)
        player_score(score)

        pygame.display.update()

game_intro()
