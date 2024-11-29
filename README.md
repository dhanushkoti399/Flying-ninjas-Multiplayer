# Flying-ninjas-Multiplayer
import pygame
import sys
import random

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 500, 720
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Flying Ninjas Multiplayer")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED=(255,0,0)
# Game constants
GRAVITY = 0.5
BIRD_JUMP = -10
PIPE_SPEED = -3
PIPE_GAP = 150

# Load images
background_img = pygame.image.load('C:/Users/user/Downloads/Python CA Project/.vscode/download.jpeg')
ninja1_img = pygame.image.load('C:/Users/user/Downloads/Python CA Project/.vscode/WhatsApp Image 2024-11-27 at 20.57.11_c5a78c8e.jpg')
ninja2_img = pygame.image.load('C:/Users/user/Downloads/Python CA Project/.vscode/WhatsApp Image 2024-11-27 at 20.48.02_3239f1f9.jpg')

# Scale images
background_img = pygame.transform.scale(background_img, (WIDTH, HEIGHT))
ninja1_img = pygame.transform.scale(ninja1_img, (30, 30))
ninja2_img = pygame.transform.scale(ninja2_img, (30, 30))

# Clock for controlling the frame rate
clock = pygame.time.Clock()

def create_pipe():
    """Creates a new pipe."""
    height = random.randint(100, 400)
    top_pipe = pygame.Rect(WIDTH, 0, 50, height)
    bottom_pipe = pygame.Rect(WIDTH, height + PIPE_GAP, 50, HEIGHT - height - PIPE_GAP)
    return top_pipe, bottom_pipe

# Game variables
bird1 = pygame.Rect(100, HEIGHT // 2, 30, 30)
bird2 = pygame.Rect(50, HEIGHT // 2, 30, 30)
bird1_velocity = 0
bird2_velocity = 0

pipes = [create_pipe()]
score1 = 0
score2 = 0
game_over1 = False
game_over2 = False

def reset_game():
    """Resets the game state."""
    global bird1, bird2, bird1_velocity, bird2_velocity, pipes, score1, score2, game_over1, game_over2
    bird1 = pygame.Rect(100, HEIGHT // 2, 30, 30)
    bird2 = pygame.Rect(50, HEIGHT // 2, 30, 30)
    bird1_velocity = 0
    bird2_velocity = 0
    pipes = [create_pipe()]
    score1 = 0
    score2 = 0
    game_over1 = False
    game_over2 = False

while True:
    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if not game_over1 and not game_over2:
                if event.key == pygame.K_w:  # Player 1 controls
                    bird1_velocity = BIRD_JUMP
                if event.key == pygame.K_UP:  # Player 2 controls
                    bird2_velocity = BIRD_JUMP
            if game_over1 or game_over2:
                if event.key == pygame.K_r:  # Restart game
                    reset_game()

    # Game logic
    if not (game_over1 and game_over2):
        # Bird movements
        bird1_velocity += GRAVITY
        bird2_velocity += GRAVITY
        bird1.y += bird1_velocity
        bird2.y += bird2_velocity

        # Move pipes
        for pipe_pair in pipes:
            pipe_pair[0].x += PIPE_SPEED
            pipe_pair[1].x += PIPE_SPEED

        # Add new pipes
        if pipes[-1][0].x < WIDTH - 200:
            pipes.append(create_pipe())

        # Remove pipes that go off-screen
        if pipes[0][0].x < -50:
            pipes.pop(0)

        # Check collisions
        for pipe_pair in pipes:
            if bird1.colliderect(pipe_pair[0]) or bird1.colliderect(pipe_pair[1]) or bird1.top < 0 or bird1.bottom > HEIGHT:
                game_over1 = True
            if bird2.colliderect(pipe_pair[0]) or bird2.colliderect(pipe_pair[1]) or bird2.top < 0 or bird2.bottom > HEIGHT:
                game_over2 = True

        # Update scores
        for pipe_pair in pipes:
            if pipe_pair[0].x == bird1.x:
                score1 += 1
            if pipe_pair[0].x == bird2.x:
                score2 += 1

    # Drawing
    screen.blit(background_img, (0, 0))  # Draw the background
    # Draw birds
    screen.blit(ninja1_img, (bird1.x, bird1.y))
    screen.blit(ninja2_img, (bird2.x, bird2.y))
    # Draw pipes
    for pipe_pair in pipes:
        pygame.draw.rect(screen, BLACK, pipe_pair[0])
        pygame.draw.rect(screen, BLACK, pipe_pair[1])
    # Display scores
    font = pygame.font.Font(None, 40)
    score_text1 = font.render(f"Player 1: {score1}", True, RED)
    score_text2 = font.render(f"Player 2: {score2}", True, RED)
    screen.blit(score_text1, (10, 10))
    screen.blit(score_text2, (10, 50))
    # Determine and display the highest scorer
    if score1 > score2:
        leader_text = font.render("Player 1 is leading!", True, WHITE)
    elif score2 > score1:
        leader_text = font.render("Player 2 is leading!", True, WHITE)
    else:
        leader_text = font.render("It's a tie!", True, WHITE)
    screen.blit(leader_text, (WIDTH // 2 - leader_text.get_width() // 2, 100))  # Center the text
    # Game over text
    if game_over1 or game_over2:
        game_over_text = font.render("Game Over! Press R to Restart", True, WHITE)
        screen.blit(game_over_text, (50, HEIGHT // 2 - 20))
    pygame.display.flip()
    clock.tick(25)
