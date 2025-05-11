import pygame
import random

# Initialisation de Pygame
pygame.init()

# Paramètres de la fenêtre
largeur_fenetre = 800
hauteur_fenetre = 600
screen = pygame.display.set_mode((largeur_fenetre, hauteur_fenetre))
pygame.display.set_caption("123Cubes v2")

# Couleurs
BLANC = (255, 255, 255)
NOIR = (0, 0, 0)
ROUGE = (255, 0, 0)
BLEU = (0, 0, 255)

# Variables
niveau = 1
score = 0
joueur_size = 50
boss_size = 100
cube_size = 40
vitesse_joueur = 5
vitesse_boss = 3

# Classes
class Joueur(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((joueur_size, joueur_size))
        self.image.fill(BLEU)
        self.rect = self.image.get_rect()
        self.rect.center = (largeur_fenetre // 2, hauteur_fenetre - 50)

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            self.rect.x -= vitesse_joueur
        if keys[pygame.K_RIGHT]:
            self.rect.x += vitesse_joueur
        if keys[pygame.K_UP]:
            self.rect.y -= vitesse_joueur
        if keys[pygame.K_DOWN]:
            self.rect.y += vitesse_joueur

class Cube(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((cube_size, cube_size))
        self.image.fill(NOIR)
        self.rect = self.image.get_rect()
        self.rect.x = random.randint(0, largeur_fenetre - cube_size)
        self.rect.y = random.randint(0, hauteur_fenetre - cube_size)

class Boss(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((boss_size, boss_size))
        self.image.fill(ROUGE)
        self.rect = self.image.get_rect()
        self.rect.center = (largeur_fenetre // 2, 100)

    def update(self):
        self.rect.x += random.choice([-vitesse_boss, vitesse_boss])
        if self.rect.left <= 0 or self.rect.right >= largeur_fenetre:
            self.rect.x -= random.choice([-vitesse_boss, vitesse_boss])

# Initialisation des objets
joueur = Joueur()
cubes = pygame.sprite.Group()
boss = Boss()

# Groupes de sprites
all_sprites = pygame.sprite.Group()
all_sprites.add(joueur)

for i in range(10):
    cube = Cube()
    all_sprites.add(cube)
    cubes.add(cube)

# Boucle de jeu
running = True
while running:
    screen.fill(BLANC)
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    
    # Mise à jour des objets
    all_sprites.update()

    # Vérification des collisions avec les cubes
    cubes_collisions = pygame.sprite.spritecollide(joueur, cubes, True)
    for cube in cubes_collisions:
        score += 10  # Augmenter le score lorsqu'un cube est touché
    
    # Boss activation à chaque niveau
    if score >= 100 * niveau and boss not in all_sprites:
        all_sprites.add(boss)

    # Vérification des collisions avec le boss
    if pygame.sprite.collide_rect(joueur, boss):
        score += 50  # Bonus en cas de victoire sur le boss
        boss.kill()  # Boss est détruit après la victoire
    
    # Dessiner tous les objets
    all_sprites.draw(screen)
    
    # Affichage du score et du niveau
    font = pygame.font.SysFont("Arial", 24)
    score_text = font.render(f"Score: {score}", True, NOIR)
    niveau_text = font.render(f"Niveau: {niveau}", True, NOIR)
    screen.blit(score_text, (10, 10))
    screen.blit(niveau_text, (largeur_fenetre - 200, 10))

    # Mise à jour de l'écran
    pygame.display.flip()

    # Contrôle de la vitesse du jeu
    pygame.time.Clock().tick(60)

pygame.quit()
