import pygame
import random

# Initialize Pygame
pygame.init()

# Game window dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Shooting Game")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Player settings
player_width, player_height = 50, 40
player_x = WIDTH // 2 - player_width // 2
player_y = HEIGHT - player_height - 10
player_speed = 5

# Bullet settings
bullet_width, bullet_height = 5, 10
bullet_speed = 7
bullets = []

# Enemy settings
enemy_width, enemy_height = 50, 40
enemy_speed = 3
enemies = []

# Game clock
clock = pygame.time.Clock()

# Font
font = pygame.font.SysFont("Arial", 32)

# Function to draw the player
def draw_player(x, y):
    pygame.draw.rect(screen, BLUE, (x, y, player_width, player_height))

# Function to draw bullets
def draw_bullets():
    for bullet in bullets:
        pygame.draw.rect(screen, RED, bullet)

# Function to draw enemies
def draw_enemies():
    for enemy in enemies:
        pygame.draw.rect(screen, (0, 255, 0), enemy)

# Function to move bullets
def move_bullets():
    for bullet in bullets:
        bullet.y -= bullet_speed
    # Remove bullets that go off screen
    global bullets
    bullets = [bullet for bullet in bullets if bullet.y > 0]

# Function to move enemies
def move_enemies():
    for enemy in enemies:
        enemy.y += enemy_speed

    # Remove enemies that go off screen
    global enemies
    enemies = [enemy for enemy in enemies if enemy.y < HEIGHT]

# Function to check for collisions
def check_collisions():
    global enemies, bullets
    new_enemies = []
    new_bullets = []
    for enemy in enemies:
        enemy_rect = pygame.Rect(enemy)
        enemy_hit = False
        for bullet in bullets:
            bullet_rect = pygame.Rect(bullet)
            if enemy_rect.colliderect(bullet_rect):
                new_enemies.append(None)  # Mark enemy for removal
                new_bullets.append(None)  # Mark bullet for removal
                enemy_hit = True
                break
        if not enemy_hit:
            new_enemies.append(enemy)
    enemies = [e for e in new_enemies if e is not None]
    bullets = [b for b in new_bullets if b is not None]

# Function to add enemies
def add_enemy():
    enemy_x = random.randint(0, WIDTH - enemy_width)
    enemy_y = -enemy_height
    enemies.append(pygame.Rect(enemy_x, enemy_y, enemy_width, enemy_height))

# Function to add a bullet
def add_bullet():
    bullet_x = player_x + player_width // 2 - bullet_width // 2
    bullet_y = player_y
    bullets.append(pygame.Rect(bullet_x, bullet_y, bullet_width, bullet_height))

# Main game loop
def game_loop():
    global player_x, player_y
    running = True
    score = 0
    while running:
        screen.fill(BLACK)

        # Event handling
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    add_bullet()

        # Movement controls
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and player_x > 0:
            player_x -= player_speed
        if keys[pygame.K_RIGHT] and player_x < WIDTH - player_width:
            player_x += player_speed

        # Update game objects
        move_bullets()
        move_enemies()
        check_collisions()

        # Draw game objects
        draw_player(player_x, player_y)
        draw_bullets()
        draw_enemies()

        # Add new enemies
        if random.random() < 0.02:
            add_enemy()

        # Draw score
        score_text = font.render(f"Score: {score}", True, WHITE)
        screen.blit(score_text, (10, 10))

        # Update the display
        pygame.display.update()

        # Cap the frame rate
        clock.tick(60)

    pygame.quit()

# Start the game
game_loop()
