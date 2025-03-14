import pygame
import random

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 600, 400
win = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Snake Game - Works on Replit & Locally!")

# Colors
WHITE = (255, 255, 255)
GREEN = (0, 255, 0)
RED = (213, 50, 80)
BLACK = (0, 0, 0)

# Snake settings
BLOCK_SIZE = 10
SPEED = 10  # Initial speed

# Font settings
font = pygame.font.SysFont("bahnschrift", 25)

# Function to display the score
def show_score(score):
    text = font.render(f"Score: {score}", True, WHITE)
    win.blit(text, [10, 10])

# Function to draw the snake
def draw_snake(snake_list):
    for block in snake_list:
        pygame.draw.rect(win, GREEN, [block[0], block[1], BLOCK_SIZE, BLOCK_SIZE])

# Function to show game over message
def show_message(msg, color, x, y):
    text = font.render(msg, True, color)
    win.blit(text, [x, y])

# Function to generate food in a valid position
def generate_food(snake_list):
    all_positions = [(x, y) for x in range(0, WIDTH, BLOCK_SIZE) for y in range(0, HEIGHT, BLOCK_SIZE)]
    available_positions = list(set(all_positions) - set(tuple(pos) for pos in snake_list))

    if available_positions:
        return random.choice(available_positions)  # Pick a random valid position
    else:
        return None  # No available space left!

# Game loop function
def game_loop():
    global SPEED  # Ensure speed persists after restarting
    game_over = False
    game_close = False

    x, y = WIDTH // 2, HEIGHT // 2  # Start position
    dx, dy = 0, 0  # Direction

    snake_list = []
    snake_length = 1
    score = 0

    # Generate the first food
    food_pos = generate_food(snake_list)

    clock = pygame.time.Clock()  # Game clock

    while not game_over:
        while game_close:
            win.fill(BLACK)
            show_message("Game Over! Press Q-Quit or C-Play Again", RED, WIDTH // 6, HEIGHT // 3)
            show_score(score)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    pygame.quit()
                    quit()
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        pygame.quit()
                        quit()
                    if event.key == pygame.K_c:
                        game_loop()  # Restart game correctly

        # Event Handling
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT and dx == 0:
                    dx, dy = -BLOCK_SIZE, 0
                elif event.key == pygame.K_RIGHT and dx == 0:
                    dx, dy = BLOCK_SIZE, 0
                elif event.key == pygame.K_UP and dy == 0:
                    dx, dy = 0, -BLOCK_SIZE
                elif event.key == pygame.K_DOWN and dy == 0:
                    dx, dy = 0, BLOCK_SIZE

        # Move snake
        x += dx
        y += dy

        # Collision detection (Wall)
        if x >= WIDTH or x < 0 or y >= HEIGHT or y < 0:
            game_close = True

        win.fill(BLACK)

        # Draw food
        if food_pos:
            pygame.draw.rect(win, RED, [food_pos[0], food_pos[1], BLOCK_SIZE, BLOCK_SIZE])
        else:
            show_message("YOU WIN! NO SPACE LEFT!", BLUE, WIDTH // 6, HEIGHT // 3)
            pygame.display.update()
            pygame.time.delay(3000)  # Show win screen for 3 seconds
            pygame.quit()
            quit()

        # Update snake body
        snake_head = [x, y]
        snake_list.append(snake_head)

        if len(snake_list) > snake_length:
            del snake_list[0]

        # Collision detection (Self)
        for block in snake_list[:-1]:
            if block == snake_head:
                game_close = True

        draw_snake(snake_list)
        show_score(score)
        pygame.display.update()

        # Eating food
        if food_pos and x == food_pos[0] and y == food_pos[1]:
            food_pos = generate_food(snake_list)  # Generate new food position
            snake_length += 1
            score += 10  # Increase score
            SPEED += 0.5  # Increase speed slightly

        clock.tick(SPEED)  # Control speed

# Run the game
game_loop()
