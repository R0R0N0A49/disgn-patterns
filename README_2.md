# Design Patterns – Journal d’apprentissage

Ce dépôt est un **journal personnel d’apprentissage des design patterns**.  
Il présente les patterns que j’ai étudiés ou utilisés, avec des exemples et des explications simples.

**Légende :**  
- ✅ : Design pattern déjà utilisé en projet  
- ❌ : Design pattern jamais utilisé personnellement  
- 🧪 : Design pattern étudié mais pas encore appliqué

---

## 📊 Vue d’ensemble des patterns

| Catégorie | Pattern | Statut |
|-----------|---------|--------|
| Création (Creational) | Factory / Factory Method | ✅ |
| Création (Creational) | Builder | ✅ |
| Création (Creational) | Prototype | ✅ |
| Création (Creational) | Singleton | ✅ |
| Structurel (Structural) | Decorator | ❌ |
| Structurel (Structural) | Adapter | ❌ |
| Comportemental (Behavioral) | Observer | ❌ |
| Comportemental (Behavioral) | Event Bus | ❌ |
| Comportemental (Behavioral) | Strategy | ❌ |
| Comportemental (Behavioral) | Command | ❌ |

---

## 📌 Descriptions détaillées des patterns

### 1️⃣ Patterns de création (Creational)

#### 🔹 Factory / Factory Method ✅
Le pattern **Factory Method** délègue la création d’objets à une méthode spécifique, sans exposer la classe concrète.  
Le code client manipule uniquement une interface ou une classe abstraite, réduisant le couplage et facilitant l’évolution du code.

**Quand l’utiliser :**  
- Création complexe d’objets  
- Éviter les `new` partout dans le code  
- Type d’objet dépend d’un paramètre ou contexte

---

#### 🔹 Builder ✅
Le pattern **Builder** construit un objet complexe étape par étape, séparant la construction de sa représentation finale.  
Il permet de créer différentes variantes d’un même objet tout en gardant le code lisible.

**Quand l’utiliser :**  
- Constructeurs avec beaucoup de paramètres  
- Objets créés de plusieurs façons  
- Lecture et contrôle du processus de création

---

#### 🔹 Prototype ✅
Le pattern **Prototype** crée de nouveaux objets en clonant une instance existante plutôt qu’en instanciant une nouvelle classe avec `new`.  
Utile lorsque la création d’un objet est coûteuse ou que le type exact n’est pas connu à l’avance.

**Quand l’utiliser :**  
- Création d’objets lourds ou coûteuse  
- Éviter une hiérarchie complexe de factories  
- Objets très similaires

---

#### 🔹 Singleton ✅
Le pattern **Singleton** garantit qu’une classe n’a qu’une seule instance et fournit un point d’accès global à celle-ci.  
Souvent utilisé pour gérer des ressources partagées comme des logs, configurations ou connexions.

**Quand l’utiliser :**  
- Ressources uniques et partagées  
- Accès global nécessaire  

⚠️ À utiliser avec précaution car il introduit un état global.

---

### 2️⃣ Patterns structurels (Structural)

#### 🔹 Decorator ❌
Ajoute dynamiquement des fonctionnalités à un objet existant sans modifier sa structure.

#### 🔹 Adapter ❌
Permet de faire communiquer deux interfaces incompatibles.

---

### 3️⃣ Patterns comportementaux (Behavioral)

#### 🔹 Observer ❌
Définit une relation un-à-plusieurs où les observateurs sont notifiés automatiquement lorsqu’un sujet change d’état.

**Quand l’utiliser :**  
- UI, MVC  
- Synchronisation automatique entre objets

---

#### 🔹 Event Bus ❌
Permet à différents composants de communiquer via des événements sans dépendances directes.  
Les producteurs publient des événements et les consommateurs s’y abonnent, favorisant un découplage fort.

**Quand l’utiliser :**  
- Applications complexes  
- Systèmes modulaires  
- Communication asynchrone ou événementielle

---

#### 🔹 Strategy ❌
Permet de définir une famille d’algorithmes et de les rendre interchangeables à l’exécution.

#### 🔹 Command ❌
Encapsule une requête en tant qu’objet, permettant de paramétrer les clients avec des actions à exécuter ultérieurement.

---

## 📝 Notes

- Les patterns marqués ❌ **n’ont pas encore été utilisés** dans mes projets, mais sont étudiés pour la compréhension.  
- Les exemples présents dans le dépôt sont cohérents avec les patterns marqués ✅.  
- Ce dépôt est un **outil d’apprentissage**, pas un catalogue exhaustif pour la production.

---

## 💡 Conseils

- Commence par comprendre et utiliser les patterns ✅  
- Étudie les patterns ❌ pour savoir quand les appliquer  
- Ajoute des exemples pratiques au fur et à mesure pour renforcer la mémoire
