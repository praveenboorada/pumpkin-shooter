# pumpkin-shooter
import pygame
import random
import math

pygame.init()

# creating a  window for game

screen = pygame.display.set_mode((800, 600))

title = "pumpkinShooter"

# icon=pygame.image.load('')
pygame.display.set_caption(title)
# pygame.display.set_icon(icon)
game_on = True
# background image
bg = pygame.image.load('data/backGround.png')
pygame.mixer.music.load('data/bensound-beachparty.mp3')
pygame.mixer.music.play(-1)

bullet_sound = pygame.mixer.Sound('data/Bullet.mp3')
explosion_sound = pygame.mixer.Sound('data/hit.mp3')
# player
player_img = pygame.image.load('data/player.png')
playerX = 400 - 32
playerY = 600 - 64
playerX_change = 0

# enemy
no_of_enemies = 5
enemy_img = []
enemyX = []
enemyY = []
enemyX_change = []
enemyY_change = []

for i in range(no_of_enemies):
    enemy_img.append(pygame.image.load('data/snapchat.png'))
    enemyX.append(random.randint(0, 736))
    enemyY.append(random.randint(20, 120))
    enemyX_change.append(2)
    enemyY_change.append(40)

# bullet
bullet_img = pygame.image.load(('data/bullet.png'))
bulletX = 0
bulletY = 550
bulletY_change = -8
bullet_state = 'ready'
score = 0
score_font = pygame.font.Font('data/TitilliumWeb-Regular.ttf', 32)
scoreX = 10
scoreY = 10

gameover_font = pygame.font.Font('data/TitilliumWeb-Regular.ttf', 32)
gameoverX = 200
gameoverY = 250
restart_font = pygame.font.Font('data/TitilliumWeb-Regular.ttf', 32)
restartX = 180
restartY = 300
gamestatus = 'running'


def show_gameover(x, y):
    global gamestatus
    gameover_img = gameover_font.render('GAME OVER SCORE : ' + str(score), True, (255, 0, 0))
    screen.blit(gameover_img, (x, y))
    pygame.mixer.music.stop()
    gamestatus = 'end'


def show_restart(x, y):
    restart_img = restart_font.render('To restart the game press r: ', True, (0, 255, 0))
    screen.blit(restart_img, (x, y))
    pygame.mixer.music.stop()


def show_score(x, y):
    score_img = score_font.render('SCORE : ' + str(score), True, (255, 255, 255))
    screen.blit(score_img, (x, y))


def is_collision(x1, y1, x2, y2):
    distance = math.sqrt(math.pow((x2 - x1), 2) + math.pow((y2 - y1), 2))
    if distance < 25:
        return True
    else:
        False


def enemy(x, y, i):
    screen.blit(enemy_img[i], (x, y))


def player(x, y):
    screen.blit(player_img, (x, y))


def bullet(x, y):
    screen.blit(bullet_img, (x + 20, y))


while (game_on):
    # background  colour
    screen.fill((45, 51, 71))
    screen.blit(bg, (0, 0))  # blit means drawing on image as of x cordinate,y coordinate
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_on = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                playerX_change = -3
            if event.key == pygame.K_RIGHT:
                playerX_change = 3
            if event.key == pygame.K_SPACE:
                if bullet_state == 'ready':
                    bullet_state = 'fire'
                    bulletX = playerX
                    bullet(bulletX, bulletY)
                    bullet_sound.play()
            if event.key == pygame.K_r:
                score = 0
                playerX = 400 - 32
                pygame.mixer.music.play(-1)
                if gamestatus == 'end':
                    gamestatus = 'running'
                    for i in range(no_of_enemies):
                        enemyX[i] = (random.randint(0, 736))
                        enemyY[i] = (random.randint(20, 120))

        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT:
                playerX_change = 0
            if event.key == pygame.K_RIGHT:
                playerX_change = 0

    # bulletmoments
    if bullet_state == 'fire':
        if bulletY < 10:
            bulletY = 550
            bullet_state = 'ready'

        bulletY += bulletY_change
        bullet(bulletX, bulletY)
    # enemymoments
    for i in range(no_of_enemies):
        # gameover
        if enemyY[i] > 500:
            show_gameover(gameoverX, gameoverY)
            show_restart(restartX, restartY)
            for j in range(no_of_enemies):
                enemyY[j] = 1200

        # end
        enemyX[i] += enemyX_change[i]
        if enemyX[i] <= 0:
            enemyX[i] = 0
            enemyX_change[i] = 2.2
            enemyY[i] += enemyY_change[i]
        elif enemyX[i] >= 800 - 64:
            enemyX[i] = 800 - 64
            enemyX_change[i] = -2.2
            enemyY[i] += enemyY_change[i]
        enemy(enemyX[i], enemyY[i], i)

        # collision
        collision = is_collision(enemyX[i], enemyY[i], bulletX, bulletY)
        if (collision):
            bulletY = 516
            bullet_state = 'ready'
            enemyX[i] = random.randint(0, 736)
            enemyY[i] = random.randint(20, 120)
            score += 1
            explosion_sound.play()
            # print(score)
    show_score(scoreX, scoreY)
    # player moments
    playerX += playerX_change

    if playerX <= 0:
        playerX = 0
    elif playerX >= 800 - 64:
        playerX = 800 - 64
    player(playerX, playerY)

    pygame.display.update()  # this function must be there for keep updating the display
