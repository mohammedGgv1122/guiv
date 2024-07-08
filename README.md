import pygame
import sys
import random

# تهيئة Pygame
pygame.init()

# إعداد أبعاد النافذة
WIDTH, HEIGHT = 800, 600
win = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("لعبة أكثر متعة")

# إعداد الألوان
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)

# إعداد شخصية اللاعب
player_size = 50
player_color = (0, 0, 255)
player_x = WIDTH // 2
player_y = HEIGHT // 2
player_speed = 5

# إعداد العقبات
obstacle_size = 50
obstacle_color = RED
obstacle_speed = 5
obstacles = []

# إعداد النقاط
point_size = 20
point_color = GREEN
points = []
score = 0

# إعداد الخط لعرض النقاط
font = pygame.font.SysFont(None, 55)

def draw_text(text, font, color, surface, x, y):
    textobj = font.render(text, True, color)
    textrect = textobj.get_rect()
    textrect.topleft = (x, y)
    surface.blit(textobj, textrect)

# الدالة الرئيسية للعبة
def main():
    global player_x, player_y, score

    clock = pygame.time.Clock()
    running = True

    # إزالة جزء الموسيقى مؤقتًا
    # pygame.mixer.music.load("background_music.mp3")
    # pygame.mixer.music.play(-1)  # التشغيل المتكرر للموسيقى

    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        # حركة اللاعب
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and player_x - player_speed > 0:
            player_x -= player_speed
        if keys[pygame.K_RIGHT] and player_x + player_speed < WIDTH - player_size:
            player_x += player_speed
        if keys[pygame.K_UP] and player_y - player_speed > 0:
            player_y -= player_speed
        if keys[pygame.K_DOWN] and player_y + player_speed < HEIGHT - player_size:
            player_y += player_speed

        # إضافة العقبات بشكل عشوائي
        if random.randint(1, 20) == 1:
            obstacle_x = random.randint(0, WIDTH - obstacle_size)
            obstacles.append([obstacle_x, 0])

        # تحريك العقبات
        for obstacle in obstacles:
            obstacle[1] += obstacle_speed
            if obstacle[1] > HEIGHT:
                obstacles.remove(obstacle)

        # إضافة النقاط بشكل عشوائي
        if random.randint(1, 50) == 1:
            point_x = random.randint(0, WIDTH - point_size)
            points.append([point_x, 0])

        # تحريك النقاط
        for point in points:
            point[1] += obstacle_speed
            if point[1] > HEIGHT:
                points.remove(point)

        # التحقق من التصادم مع العقبات
        for obstacle in obstacles:
            if player_x < obstacle[0] < player_x + player_size or player_x < obstacle[0] + obstacle_size < player_x + player_size:
                if player_y < obstacle[1] < player_y + player_size or player_y < obstacle[1] + obstacle_size < player_y + player_size:
                    running = False  # انتهاء اللعبة عند التصادم

        # التحقق من جمع النقاط
        for point in points:
            if player_x < point[0] < player_x + player_size or player_x < point[0] + point_size < player_x + player_size:
                if player_y < point[1] < player_y + player_size or player_y < point[1] + point_size < player_y + player_size:
                    points.remove(point)
                    score += 1
                    # إضافة تأثير صوتي عند جمع النقاط
                    # تأكد من وجود ملف الصوت "point_sound.wav"
                    # pygame.mixer.Sound("point_sound.wav").play()

        # رسم العناصر
        win.fill(WHITE)
        pygame.draw.rect(win, player_color, (player_x, player_y, player_size, player_size))
        for obstacle in obstacles:
            pygame.draw.rect(win, obstacle_color, (obstacle[0], obstacle[1], obstacle_size, obstacle_size))
        for point in points:
            pygame.draw.rect(win, point_color, (point[0], point[1], point_size, point_size))

        # عرض النقاط
        draw_text(f"Score: {score}", font, BLACK, win, 10, 10)

        # تحديث الشاشة
        pygame.display.flip()
        clock.tick(60)

    pygame.quit()
    sys.exit()

if __name__ == "__main__":
    main()
