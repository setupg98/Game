import pygame
import random

# Initialize pygame
pygame.init()

# Define constants
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
CAR_WIDTH = 50
CAR_HEIGHT = 100
ROAD_WIDTH = 400
ROAD_LINE_WIDTH = 10
FPS = 60

# Colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
GRAY = (169, 169, 169)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

# Set up the game window
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Car Racing Game")

# Load car image
car_img = pygame.image.load('car.png')
car_img = pygame.transform.scale(car_img, (CAR_WIDTH, CAR_HEIGHT))

# Define car class
class Car:
    def __init__(self):
        self.x = SCREEN_WIDTH // 2 - CAR_WIDTH // 2
        self.y = SCREEN_HEIGHT - CAR_HEIGHT - 10
        self.speed = 5

    def draw(self):
        screen.blit(car_img, (self.x, self.y))

    def move_left(self):
        self.x -= self.speed
        if self.x < (SCREEN_WIDTH - ROAD_WIDTH) // 2:
            self.x = (SCREEN_WIDTH - ROAD_WIDTH) // 2

    def move_right(self):
        self.x += self.speed
        if self.x > (SCREEN_WIDTH + ROAD_WIDTH) // 2 - CAR_WIDTH:
            self.x = (SCREEN_WIDTH + ROAD_WIDTH) // 2 - CAR_WIDTH

# Define obstacle class
class Obstacle:
    def __init__(self):
        self.width = random.randint(50, 100)
        self.height = random.randint(50, 100)
        self.x = random.randint((SCREEN_WIDTH - ROAD_WIDTH) // 2, (SCREEN_WIDTH + ROAD_WIDTH) // 2 - self.width)
        self.y = -self.height
        self.speed = 7

    def draw(self):
        pygame.draw.rect(screen, RED, [self.x, self.y, self.width, self.height])

    def move(self):
        self.y += self.speed

# Main game loop
def game_loop():
    clock = pygame.time.Clock()
    car = Car()
    obstacles = []
    score = 0
    game_over = False

    while not game_over:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True

        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            car.move_left()
        if keys[pygame.K_RIGHT]:
            car.move_right()

        # Clear the screen
        screen.fill(GRAY)

        # Draw road
        pygame.draw.rect(screen, BLACK, [(SCREEN_WIDTH - ROAD_WIDTH) // 2, 0, ROAD_WIDTH, SCREEN_HEIGHT])
        for i in range(0, SCREEN_HEIGHT, 40):
            pygame.draw.rect(screen, WHITE, [SCREEN_WIDTH // 2 - ROAD_LINE_WIDTH // 2, i, ROAD_LINE_WIDTH, 20])

        # Update obstacles
        if random.randint(1, 60) == 1:
            obstacles.append(Obstacle())
        
        for obstacle in obstacles:
            obstacle.move()
            obstacle.draw()

            # Check for collision
            if car.y < obstacle.y + obstacle.height and car.y + CAR_HEIGHT > obstacle.y:
                if car.x < obstacle.x + obstacle.width and car.x + CAR_WIDTH > obstacle.x:
                    game_over = True

            # Remove obstacles that are out of the screen
            if obstacle.y > SCREEN_HEIGHT:
                obstacles.remove(obstacle)
                score += 1

        # Draw car
        car.draw()

        # Draw score
        font = pygame.font.Font(None, 36)
        score_text = font.render("Score: " + str(score), True, WHITE)
        screen.blit(score_text, (10, 10))

        # Update the display
        pygame.display.flip()
        clock.tick(FPS)

    # Game over screen
    screen.fill(BLACK)
    font = pygame.font.Font(None, 72)
    game_over_text = font.render("Game Over", True, RED)
    screen.blit(game_over_text, (SCREEN_WIDTH // 2 - game_over_text.get_width() // 2, SCREEN_HEIGHT // 2 - game_over_text.get_height() // 2))
    pygame.display.flip()
    pygame.time.wait(0.1)

# Start the game
game_loop()

# Quit pygame
pygame.quit()
