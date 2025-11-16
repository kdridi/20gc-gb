# 20gc-gb — 20 Games Challenge sur Game Boy

> Projet : réaliser 20 jeux Game Boy en ASM avec **rgbds**, en suivant une approche **TDD** pilotée par un émulateur (**libgambatte**) et en s’appuyant sur le livre de **Maximilien Dagois**.

Ce repo a deux objectifs principaux :

1. **Apprendre et maîtriser le hardware Game Boy** en écrivant des jeux complets.
2. **Mettre en place un workflow TDD** autour de ROMs Game Boy grâce à un émulateur intégré côté C++.

Ce projet est aussi pensé pour être utilisé avec un LLM (Codex / Claude Code / ChatGPT) comme copilote.

---

## 🎯 Objectifs globaux

- Créer **20 jeux jouables** sur Game Boy (ROM `.gb`), en ASM (rgbds).
- Avoir une **arborescence claire** pour :
  - le code des jeux,
  - le code partagé (macros, code commun),
  - les outils / scripts,
  - les tests C++.
- Mettre en place un **pipeline TDD** :
  - compilation des ROM via rgbds,
  - exécution automatisée des ROM dans **libgambatte**,
  - tests C++ qui vérifient l’état de la RAM / des registres après X frames.
- Utiliser le livre de **Maximilien Dagois** comme référence principale pour le hardware Game Boy.

Aucune mention explicite de LLVM dans ce repo : focus total sur **20 jeux + TDD + hardware**.

---

## 🕹️ Liste des 20 jeux (version Game Boy)

Cette liste est adaptée au contexte Game Boy, avec une progression technique (sprites, background, scrolling, son, SRAM, etc.).

1. **Input Toy**  
   - Déplacer un sprite avec le D-pad (pas un vrai jeu, mais premier test HW).
2. **Pong**  
   - Deux raquettes, une balle, score simple.
3. **Breakout minimal**  
   - Palette + quelques briques, pas de power-ups.
4. **Snake**  
   - Serpent, pommes, collisions mur/corps.
5. **Tetris-like simplifié**  
   - 2–3 types de pièces, lignes complètes, pas besoin de toutes les formes.
6. **Space Invaders / Fixed Shooter**  
   - Vaisseau en bas, ennemis qui descendent, tirs.
7. **Flappy-GB**  
   - “Flappy Bird” minimal avec obstacles et gravité.
8. **Sokoban**  
   - Pousser des caisses dans un petit nombre de niveaux.
9. **Pac-like**  
   - Labyrinthe simple, pastilles, ennemis au comportement basique.
10. **Platformer 1 écran**  
    - Personnage qui saute sur quelques plateformes, collecte d’objets.
11. **Reaction Game**  
    - Appuyer au bon moment (test de réflexe, gestion de timer).
12. **Vertical Shooter**  
    - Style 1942 simplifié avec scrolling vertical.
13. **Endless Runner**  
    - Personnage qui court avec obstacles à éviter.
14. **Puzzle simple (type Lights Out / Match-3 réduit)**  
    - Petites grilles, logique de flip / swap.
15. **Jeu musical / toy sonore**  
    - Boutons qui déclenchent des sons / séquences.
16. **Hi-score game avec SRAM**  
    - Sauvegarde du meilleur score en mémoire non volatile.
17. **Boss Fight Only**  
    - Un seul boss avec quelques phases / patterns.
18. **2-players hot seat**  
    - Tour par tour, score comparé (ex : puzzle / score attack).
19. **Mini-collection de 3 micro-jeux**  
    - Style WarioWare simplifié.
20. **“Super Game” remix**  
    - Réutilise des mécaniques de plusieurs jeux précédents.

Chaque jeu aura :

- un **répertoire dédié**,
- au minimum :
  - du code ASM rgbds,
  - un script de build,
  - une ou plusieurs ROM `.gb`,
  - une ou plusieurs **suites de tests C++**.

---

## 🗂️ Arborescence du projet

Organisation prévue (simple et lisible) :

```text
20gc-gb/
  games/
    pong/
      asm/        # ASM rgbds spécifique à Pong
      tests/      # tests C++ spécifiques à Pong
      assets/     # données, tiles, etc. (si besoin)
      Makefile    # build de la ROM Pong

    snake/
      asm/
      tests/
      assets/
      Makefile

    ...  # etc. pour chaque jeu

  engine/
    asm/          # code ASM partagé, macros, runtime commun
    include/      # équivalents de "headers" ASM (macros, constantes)
    docs/         # notes sur conventions, mapping RAM commun

  external/
    libgambatte/  # sources de libgambatte (non commités ou en submodule)

  tests/
    common/       # tests C++ génériques, helpers, utilitaires de test

  tools/
    build_rom.sh      # script générique pour builder une ROM
    run_tests.sh      # script pour exécuter tous les tests C++
    generate_assets/  # outils potentiels pour convertir des assets

  docs/
    GAMEBOY_CODING_ADVENTURE.md  # notes et résumé du livre de M. Dagois
    # Le PDF du livre ne sera pas commité.
    # Exemple de chemin local : docs/GameBoyCodingAdventure.pdf (ignored)

  .gitignore
  CMakeLists.txt       # projet C++ pour les tests libgambatte
  README.md
````

---

## 🧰 Outils et dépendances

### rgbds

Utilisé pour assembler et linker les ROM Game Boy :

* `rgbasm`, `rgblink`, `rgbfix`
* Un script (Makefile ou `tools/build_rom.sh`) sera fourni pour chaque jeu, par ex :

```sh
rgbasm -o pong.o asm/pong.asm
rgblink -o pong.gb pong.o
rgbfix -v -p 0 pong.gb
```

### libgambatte

Utilisée pour exécuter les ROM dans un **environnement de test C++**.

* Le code source sera placé dans `external/libgambatte/`.
* Un projet C++ (CMake) sera configuré pour :

  * compiler libgambatte en library,
  * compiler les tests C++ qui l’utilisent.

> **Note pour Codex :**
> Suppose que `external/libgambatte` contient le core de l’émulateur.
> Fournir des wrappers C++ simples si nécessaire (par ex. `GbEmu` avec `loadRom()`, `runFrames()`, `getRam()`).

### C++ et framework de tests

Les tests seront écrits en C++ (C++17 ou plus) avec **doctest** (header-only).

* `tests/common/doctest.h`
* Chaque jeu peut avoir ses propres fichiers de tests, par ex. `games/pong/tests/test_pong.cpp`.

---

## 🧪 TDD : stratégie de tests

L’approche TDD est double :

1. **Tests d’intégration ROM-level avec libgambatte**

   * On compile une ROM (`pong.gb`),
   * on la lance dans libgambatte depuis un test C++,
   * on simule des entrées,
   * on laisse tourner X frames,
   * on lit la RAM, les registres, etc.,
   * on vérifie que l’état correspond aux attentes.

2. **(Plus tard) Tests unitaires de logique pure (optionnel)**

   * On pourra écrire une version C/C++ de la logique de certains jeux (`GameState` + `step()`),
   * la tester en C++ pur sans émulateur,
   * puis porter / synchroniser cette logique en ASM.

### Exemple de test C++ intégration (pseudo-API libgambatte)

Fichier : `games/pong/tests/test_pong.cpp`

```cpp
#define DOCTEST_CONFIG_IMPLEMENT_WITH_MAIN
#include "doctest.h"

#include "gambatte.h"   // API à adapter à la vraie libgambatte
#include <fstream>
#include <vector>

std::vector<uint8_t> loadRom(const char* path) {
    std::ifstream ifs(path, std::ios::binary);
    REQUIRE(ifs && "ROM not found");
    return std::vector<uint8_t>((std::istreambuf_iterator<char>(ifs)),
                                std::istreambuf_iterator<char>());
}

TEST_CASE("Pong - la balle est recentrée après un point") {
    // 1) Charger la ROM Pong
    auto romData = loadRom("build/roms/pong.gb");

    gambatte::GB gb;
    gb.load(romData.data(), romData.size());  // API indicative

    // 2) Réinitialiser
    gb.reset();

    // 3) Laisser tourner quelques frames (passer l’écran titre, etc.)
    for (int i = 0; i < 120; ++i) {
        gb.runFor(70224);  // ~1 frame de cycles GB (valeur à ajuster)
    }

    // 4) Récupérer un pointeur sur la RAM (WRAM à partir de 0xC000)
    uint8_t* ram = gb.getRam();  // API indicative

    // Simuler un état ou vérifier que la logique du jeu a déjà mis la balle au centre
    uint8_t ballX = ram[0xC100 - 0xC000];
    uint8_t ballY = ram[0xC101 - 0xC000];

    CHECK(ballX == 80);  // valeur logique de centre X (exemple)
    CHECK(ballY == 72);  // valeur logique de centre Y (exemple)
}
```

> **Note pour Codex :**
> Si l’API de libgambatte diffère, adapter :
>
> * les noms (`GB`, `load`, `runFor`, `getRam`)
> * les types utilisés pour les cycles / frames
> * la manière d’accéder à la RAM.

---

## 📖 Livre de Maximilien Dagois

Le livre de référence pour ce projet est :

> *Game Boy Coding Adventure — Maximilien Dagois* (No Starch Press)

Utilisation prévue :

* Prendre des notes dans `docs/GAMEBOY_CODING_ADVENTURE.md`.
* S’inspirer des patterns proposés pour :

  * l’initialisation de la console,
  * la gestion des interruptions,
  * l’affichage (BG, sprites, scroll),
  * la gestion des entrées,
  * le son,
  * la SRAM, etc.
* Ne pas commiter le PDF du livre.
  On peut simplement supposer un chemin local, par ex. `docs/GameBoyCodingAdventure.pdf` ignoré par git.

---

## 🤖 Travail avec Codex / Claude / ChatGPT

Cette section explique comment le LLM doit se comporter pour aider sur ce repo.

### Rôle attendu du LLM

* Aider à :

  * écrire du code ASM rgbds lisible, modulaire, commenté,
  * proposer des macros et conventions réutilisables,
  * créer des tests C++ contre libgambatte,
  * écrire des scripts de build simples (Makefile, CMake, scripts shell),
  * analyser et refactorer le code au fur et à mesure du challenge.
* Ne PAS :

  * introduire d’autres toolchains ou moteurs exotiques,
  * parler d’autres architectures obscures,
  * complexifier inutilement le pipeline.

### Prompt de contexte (à donner au LLM dans ce repo)

> Tu travailles sur le projet `20gc-gb`.
> Ce projet contient 20 petits jeux Game Boy écrits en ASM avec rgbds.
> On utilise libgambatte comme core d’émulation pour tester les ROMs via des tests C++ (doctest).
> Ton but est d’aider à :
>
> * écrire et structurer l’ASM rgbds,
> * factoriser le code partagé dans `engine/`,
> * écrire des tests C++ qui chargent les ROMs, simulent quelques frames dans libgambatte, lisent la RAM et vérifient l’état du jeu,
> * générer ou améliorer des scripts de build (Makefile, CMake).
>   Ne propose pas d’autres architectures ou toolchains : on reste focalisé sur rgbds, libgambatte, et le 20 Games Challenge.

### Exemples de demandes à faire au LLM

* **ASM Pong (logique)**

  > “Lis `games/pong/asm/pong.asm`. Propose une refactorisation des routines de mise à jour de la balle pour qu’elles soient plus faciles à tester (séparer calcul de la nouvelle position de l’écriture en VRAM).”

* **Test C++ pour un état précis**

  > “Écris un test doctest qui charge `build/roms/pong.gb` dans libgambatte, simule 200 frames, puis vérifie que la variable score joueur 1 stockée à l’adresse 0xC110 vaut 3.”

* **Script de build ROM**

  > “Génère un Makefile minimal pour `games/pong` qui :
  >
  > * assemble `asm/pong.asm` en `pong.o`
  > * link `pong.o` en `pong.gb`
  > * applique `rgbfix` pour rendre la ROM valide.
  >   Le répertoire de sortie des ROMs est `build/roms/`.”

---

## ✅ Roadmap (focus 20GC + TDD)

1. **Initialisation projet**

   * Créer la structure de base (`games/`, `engine/`, `external/`, `tests/`, `tools/`, `docs/`).
   * Ajouter un `CMakeLists.txt` minimal pour compiler un test C++ trivial.
   * Ajouter `doctest.h` dans `tests/common/`.

2. **Intégration libgambatte**

   * Télécharger les sources de libgambatte dans `external/libgambatte/`.
   * Configurer CMake pour compiler libgambatte en library.
   * Écrire un petit wrapper C++ (`GbEmu` par exemple) avec :

     * `bool loadRom(const std::string&)`
     * `void reset()`
     * `void runFrames(int n)`
     * `uint8_t* getRam()`

3. **Premier jeu : Input Toy**

   * Écrire le code ASM minimal pour afficher un sprite et le déplacer au D-pad.
   * Écrire un test C++ qui :

     * charge la ROM,
     * simule quelques frames,
     * vérifie que RAM correspond à la position attendue après une séquence d’inputs.

4. **Jeu 02 : Pong**

   * Spécifier les adresses RAM pour la balle, les raquettes et les scores.
   * Écrire l’ASM Pong.
   * Écrire plusieurs tests C++ pour :

     * vérification de la position de la balle après rebond murale,
     * vérification du reset de balle après un point,
     * vérification du score.

5. **Étendre progressivement aux autres jeux**

   * Pour chaque jeu :

     * définir les données importantes en RAM,
     * écrire les tests C++ d’intégration,
     * refactoriser l’ASM pour rester lisible et testable.

---
