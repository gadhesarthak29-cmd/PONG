# PONG
Here’s a polished Pong game in Python that will run smoothly in VS Code. I’ve added attractive colors, a glowing ball effect, and some fancy features like score display and paddle speed control.
import pygame
import random
import sys

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Fancy Pong Game")

# Colors
WHITE = (255, 255, 255)
NEON_BLUE = (0, 255, 255)
NEON_PINK = (255, 0, 150)
NEON_GREEN = (0, 255, 100)
BLACK = (0, 0, 0)

# Fonts
font = pygame.font.SysFont("comicsansms", 40)

# Paddle settings
PADDLE_WIDTH, PADDLE_HEIGHT = 15, 100
paddle_speed = 7

# Ball settings
BALL_SIZE = 20
ball_speed_x = 5 * random.choice((1, -1))
ball_speed_y = 5 * random.choice((1, -1))

# Player paddles
player1 = pygame.Rect(50, HEIGHT//2 - PADDLE_HEIGHT//2, PADDLE_WIDTH, PADDLE_HEIGHT)
player2 = pygame.Rect(WIDTH-50-PADDLE_WIDTH, HEIGHT//2 - PADDLE_HEIGHT//2, PADDLE_WIDTH, PADDLE_HEIGHT)

# Ball
ball = pygame.Rect(WIDTH//2 - BALL_SIZE//2, HEIGHT//2 - BALL_SIZE//2, BALL_SIZE, BALL_SIZE)

# Scores
score1, score2 = 0, 0

# Draw glowing ball
def draw_glowing_ball(ball_rect):
    pygame.draw.ellipse(screen, NEON_GREEN, ball_rect)
    glow_radius = 30
    glow_surface = pygame.Surface((glow_radius*2, glow_radius*2), pygame.SRCALPHA)
    pygame.draw.circle(glow_surface, (0, 255, 100, 100), (glow_radius, glow_radius), glow_radius)
    screen.blit(glow_surface, (ball_rect.centerx - glow_radius, ball_rect.centery - glow_radius))

# Game loop
clock = pygame.time.Clock()
running = True

while running:
    screen.fill(BLACK)

    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    # Controls
    keys = pygame.key.get_pressed()
    if keys[pygame.K_w] and player1.top > 0:
        player1.y -= paddle_speed
    if keys[pygame.K_s] and player1.bottom < HEIGHT:
        player1.y += paddle_speed
    if keys[pygame.K_UP] and player2.top > 0:
        player2.y -= paddle_speed
    if keys[pygame.K_DOWN] and player2.bottom < HEIGHT:
        player2.y += paddle_speed

    # Ball movement
    ball.x += ball_speed_x
    ball.y += ball_speed_y

    # Collision with top/bottom
    if ball.top <= 0 or ball.bottom >= HEIGHT:
        ball_speed_y *= -1

    # Collision with paddles
    if ball.colliderect(player1) or ball.colliderect(player2):
        ball_speed_x *= -1

    # Scoring
    if ball.left <= 0:
        score2 += 1
        ball.center = (WIDTH//2, HEIGHT//2)
        ball_speed_x *= random.choice((1, -1))
        ball_speed_y *= random.choice((1, -1))

    if ball.right >= WIDTH:
        score1 += 1
        ball.center = (WIDTH//2, HEIGHT//2)
        ball_speed_x *= random.choice((1, -1))
        ball_speed_y *= random.choice((1, -1))

    # Draw paddles
    pygame.draw.rect(screen, NEON_BLUE, player1)
    pygame.draw.rect(screen, NEON_PINK, player2)

    # Draw ball with glow
    draw_glowing_ball(ball)

    # Draw scores
    score_text = font.render(f"{score1}   |   {score2}", True, WHITE)
    screen.blit(score_text, (WIDTH//2 - score_text.get_width()//2, 20))

    # Update display
    pygame.display.flip()
    clock.tick(60)
