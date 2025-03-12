import pygame
import random
import time

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 720, 1000
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Chellenge - Catch the Falling Apples")

# Colors
WHITE = (255, 255, 255)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
BLACK = (0, 0, 0)
GRAY = (200, 200, 200)
DARK_GRAY = (100, 100, 100)
YELLOW = (255, 255, 0)

# Load Fonts
font = pygame.font.Font(None, 50)

# LOADING SCREEN FUNCTION
def show_loading_screen():
    screen.fill(BLACK)  # Changed background to black
    loading_text = font.render("Loading...", True, WHITE)  # Changed text to white
    text_rect = loading_text.get_rect(center=(WIDTH // 2, HEIGHT // 2))
    screen.blit(loading_text, text_rect)
    pygame.display.flip()
    time.sleep(2)  # Simulate loading time

# Show loading screen before the game starts
show_loading_screen()

# Basket properties
basket_width, basket_height = 120, 25
basket_x = WIDTH // 2 - basket_width // 2
basket_y = HEIGHT - 150
basket_speed = 7

# Apple properties
apple_size = 25
apple_x = random.randint(apple_size, WIDTH - apple_size)
apple_y = 70  # Starting height
apple_speed = 6
apple_acceleration = 0.2

# Score
score = 0

# Restart button
restart_button_rect = pygame.Rect(WIDTH // 2 - 60, HEIGHT // 2 - 25, 120, 50)

# Control buttons (for touch users)
button_width = WIDTH // 2
button_height = 80  # Increased button size for visibility
left_button_rect = pygame.Rect(0, HEIGHT - button_height, button_width, button_height)
right_button_rect = pygame.Rect(button_width, HEIGHT - button_height, button_width, button_height)

# Game state
game_over = False
moving_left = False
moving_right = False

# Game loop
running = True
clock = pygame.time.Clock()

while running:
    screen.fill(BLACK)  # Changed background to black

    # Draw Yellow Border
    pygame.draw.rect(screen, YELLOW, (0, 0, WIDTH, HEIGHT), 5)  

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if game_over and event.type == pygame.MOUSEBUTTONDOWN:
            if restart_button_rect.collidepoint(event.pos):
                # Reset game
                score = 0
                apple_speed = 6
                apple_x = random.randint(apple_size, WIDTH - apple_size)
                apple_y = 70
                basket_x = WIDTH // 2 - basket_width // 2
                game_over = False

        if event.type == pygame.MOUSEBUTTONDOWN:
            if left_button_rect.collidepoint(event.pos):
                moving_left = True
            elif right_button_rect.collidepoint(event.pos):
                moving_right = True
        elif event.type == pygame.MOUSEBUTTONUP:
            moving_left = moving_right = False

    if not game_over:
        apple_y += apple_speed
        if moving_left:
            basket_x -= basket_speed
        if moving_right:
            basket_x += basket_speed
        basket_x = max(0, min(WIDTH - basket_width, basket_x))

        # Apple catching logic
        if apple_y + apple_size >= basket_y and basket_x <= apple_x <= basket_x + basket_width:
            score += 1
            apple_speed += apple_acceleration
            apple_x = random.randint(apple_size, WIDTH - apple_size)
            apple_y = 70  

        if apple_y > HEIGHT:
            game_over = True  

    # Draw basket (Changed to white)
    pygame.draw.rect(screen, WHITE, (basket_x, basket_y, basket_width, basket_height))

    # Draw apple
    pygame.draw.circle(screen, RED, (apple_x, apple_y), apple_size)

    # Display score
    score_text = font.render(f"Score: {score}", True, WHITE)  # Changed text to white
    screen.blit(score_text, (10, 10))

    # Draw buttons
    pygame.draw.rect(screen, DARK_GRAY, left_button_rect)
    pygame.draw.rect(screen, DARK_GRAY, right_button_rect)

    button_font = pygame.font.Font(None, 50)
    left_text = button_font.render("<", True, WHITE)
    right_text = button_font.render(">", True, WHITE)
    
    # Center button text
    screen.blit(left_text, (button_width // 2 - 15, HEIGHT - button_height + 25))
    screen.blit(right_text, (WIDTH - button_width // 2 - 15, HEIGHT - button_height + 25))

    if game_over:
        # Draw game over overlay
        overlay = pygame.Surface((WIDTH, HEIGHT))
        overlay.set_alpha(180)
        overlay.fill(BLACK)  # Changed overlay to black
        screen.blit(overlay, (0, 0))

        # Game over text
        game_over_text = font.render("Game Over", True, WHITE)  # Changed text to white
        text_rect = game_over_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 - 60))
        screen.blit(game_over_text, text_rect)

        # Restart button
        pygame.draw.rect(screen, DARK_GRAY, restart_button_rect, border_radius=10)
        restart_text = font.render("Restart", True, WHITE)
        restart_rect = restart_text.get_rect(center=restart_button_rect.center)
        screen.blit(restart_text, restart_rect)

    pygame.display.flip()
    clock.tick(30)

pygame.quit()
