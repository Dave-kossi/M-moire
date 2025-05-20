#Projet de fin d'étude
import numpy as np
# valeur prise par les cartes 
def valeur_carte(carte, regle='c'):
    if 2 <= carte <= 10:
        return carte
    if carte == 1:
        return 1
    if regle == 'a':
        return carte  # J=11, Q=12, K=13
    elif regle == 'b':
        return 10
    elif regle == 'c':
        return 5
    else:
        raise ValueError("Règle inconnue")
# Definition du paquet de cartes 
def creer_paquet(regle='c'):
    paquet = []
    for _ in range(4):  # 4 couleurs
        for carte in range(1, 14):
            paquet.append(valeur_carte(carte, regle))
    return np.array(paquet)

def compter_kruskal(paquet, nombre_secret):
    position = 0
    compteur = nombre_secret
    derniere_carte_clef = None
    while position < len(paquet):
        compteur -= 1
        if compteur == 0:
            derniere_carte_clef = position
            compteur = paquet[position]
        position += 1
    return derniere_carte_clef

def simuler_kruskal(nb_simulations=100000, regle='c', strategie_magicien='fixe', valeur_magicien=1):
    paquet = creer_paquet(regle)
    succes = 0
    for _ in range(nb_simulations):
        np.random.shuffle(paquet) 
        nombre_secret_sujet = np.random.randint(1, 11)
        if strategie_magicien == 'fixe':
            nombre_secret_magicien = valeur_magicien
        elif strategie_magicien == 'aleatoire':
            nombre_secret_magicien = np.random.randint(1, 11)
        else:
            raise ValueError("Stratégie magicien inconnue")
        carte_sujet = compter_kruskal(paquet, nombre_secret_sujet)
        carte_magicien = compter_kruskal(paquet, nombre_secret_magicien)
        if carte_sujet == carte_magicien:
            succes += 1
    return succes / nb_simulations

def tester_strategies(nb_simulations=100000, regle='a'):
    print(f"Règle {regle} - Simulation avec {nb_simulations} parties")
    p_succes_1 = simuler_kruskal(nb_simulations, regle, 'fixe', 1)
    print(f"Magicien avec nombre secret fixe = 1 : probabilité de succès ≈ {p_succes_1:.6f}")

    p_succes_aleat = simuler_kruskal(nb_simulations, regle, 'aleatoire')
    print(f"Magicien avec nombre secret aléatoire (1-10) : probabilité de succès ≈ {p_succes_aleat:.6f}")

    print("Magicien avec nombre secret fixe variant de 1 à 10 :")
    for i in range(1, 11):
        p = simuler_kruskal(nb_simulations // 10, regle, 'fixe', i)
        print(f"  Nombre secret = {i} : probabilité de succès ≈ {p:.6f}")
# Appel direct pour lancer la simulation
tester_strategies(nb_simulations=1000000, regle='c')
