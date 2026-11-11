# Séance 2 — Loi binomiale (TG‑21) : représenter, simuler, encadrer

> **Terminale — Spécialité Mathématiques | Durée : 1h**
>
> **Prérequis :** définition de la loi binomiale, formule de $P(X=k)$, passage « au moins / au plus » → $P(X\leq k)$, fonctions `loi` et `cumul` (fiche TG‑20).
>
> **Objectif :** savoir décrire l'allure de l'histogramme d'une loi binomiale, simuler une loi binomiale en Python, et déterminer un **intervalle** $[a;b]$ qui contient $X$ avec une probabilité d'au moins $0,95$.

---

## 1. Déroulement (1 heure)

| Temps | Activité |
|---|---|
| 0–5 min | Diagnostic rapide |
| 5–20 min | Cours : allure, symétries, simulation, seuils et intervalle |
| 20–30 min | 4 exercices faciles |
| 30–40 min | 3 exercices moyens |
| 40–50 min | 3 exercices difficiles |
| 50–55 min | 1 exercice très difficile (au choix, l'autre en travail personnel) |
| 55–60 min | Bilan oral + travail personnel |


---

## 2. Diagnostic — 5 min

### Question 1
Rappeler la formule de $P(X=k)$ pour $X\sim\mathcal B(n;p)$.

### Question 2
Exprimer $P(X\geq5)$ à l'aide d'un évènement de la forme $P(X\leq k)$.

### Question 3
Si $X\sim\mathcal B(8;0,3)$, quelle loi suit $Y=8-X$ ?

### Question 4
Que renvoie en Python la fonction `random()` du module `random` ?

<details>
<summary>👉 Correction</summary>

**1.** $$P(X=k)=\binom{n}{k}\,p^{k}\,(1-p)^{n-k}$$

**2.** $$P(X\geq5)=1-P(X\leq4)$$

**3.** $X$ compte les succès (probabilité $0,3$), donc $8-X$ compte les échecs (probabilité $0,7$) : $Y\sim\mathcal B(8;0,7)$.

**4.** Un nombre décimal « au hasard » dans l'intervalle $[0;1[$.

</details>

---

## 3. Résumé de cours

### 3.1 Rappel : les deux fonctions Python de la séance précédente

```python
from math import comb

def loi(n, p, k):        # renvoie P(X = k)
    return comb(n, k) * p**k * (1 - p)**(n - k)

def cumul(n, p, k):      # renvoie P(X <= k)
    s = 0
    for i in range(k + 1):
        s = s + loi(n, p, i)
    return s
```

---

### 3.2 L'allure de l'histogramme d'une loi binomiale

Pour $X\sim\mathcal B(n;p)$, on représente chaque entier $k$ par une barre de hauteur $P(X=k)$.

- les barres **montent**, puis **descendent** ;
- le sommet (la valeur la plus probable) est proche de $np$ ;
- exemple : pour $X\sim\mathcal B(10;0,25)$, le sommet est en $k=2$, pas loin de $10\times\frac14=2,5$.

**Pourquoi ?** On compare deux barres consécutives avec le quotient :

$$\frac{P(X=k+1)}{P(X=k)}=\frac{(n-k)\,p}{(k+1)(1-p)}$$

Ce quotient **décroît** avec $k$, donc les barres montent tant qu'il reste supérieur à 1, puis descendent. Il est supérieur à 1 si et seulement si :

$$k<(n+1)p-1$$

*(Il vient de $\dfrac{\binom{n}{k+1}}{\binom{n}{k}}=\dfrac{n-k}{k+1}$.)*

---

### 3.3 Deux symétries à connaître

Si $X\sim\mathcal B(n;p)$ :

- $n-X$, qui compte les échecs, suit $\mathcal B(n;1-p)$ ;
- si $p=\dfrac12$, l'histogramme est **symétrique** : pour tout $k$,

$$P(X=k)=P(X=n-k)$$

car $\dbinom{n}{k}=\dbinom{n}{n-k}$.

---

### 3.4 Simuler une loi binomiale

On fait « passer » le schéma de Bernoulli par l'ordinateur :

```python
from random import random

def simul(n, p):         # renvoie une valeur de X
    s = 0
    for i in range(n):
        if random() < p:
            s = s + 1
    return s

N = 10000
c = 0
for j in range(N):
    if simul(10, 0.25) >= 5:
        c = c + 1
print(c / N)
```

Ce programme renvoie une **fréquence** qui fluctue autour de $P(X\geq5)\approx0,078$ (on peut obtenir $0,0791$, puis $0,0768$, mais presque jamais $0,078$ pile).

> **À retenir :** une fréquence fluctue autour de la probabilité ; plus $N$ est grand, plus la fluctuation est petite.

---

### 3.5 Chercher un seuil, un intervalle

Soit $X\sim\mathcal B(n;p)$ et un risque $\alpha$ (souvent $\alpha=0,05$).

- $b$ est le **plus petit** entier tel que $P(X\leq b)\geq1-\dfrac{\alpha}{2}$ ;
- $a$ est le **plus petit** entier tel que $P(X\leq a)>\dfrac{\alpha}{2}$.

Alors $P(X<a)\leq\dfrac{\alpha}{2}$ et $P(X>b)\leq\dfrac{\alpha}{2}$, donc :

$$\boxed{P(a\leq X\leq b)\geq1-\alpha}$$

On justifie chaque seuil par **deux probabilités consécutives**.

**Exemple.** $X\sim\mathcal B(50;0,3)$, $\alpha=0,05$.

$$P(X\leq8)\approx0,018\leq0,025\qquad P(X\leq9)\approx0,040>0,025\qquad\text{donc }a=9$$

$$P(X\leq21)\approx0,9749<0,975\qquad P(X\leq22)\approx0,9877\geq0,975\qquad\text{donc }b=22$$

Ainsi $P(9\leq X\leq22)\approx0,969$.

---

### 3.6 Les deux seuils en Python

```python
a = 0
while cumul(50, 0.3, a) <= 0.025:
    a = a + 1

b = 0
while cumul(50, 0.3, b) < 0.975:
    b = b + 1

print(a, b)              # affiche 9 22
```

> **Réflexe :**
>
> $$\boxed{\text{Allure}\rightarrow\text{Simulation}\rightarrow\text{Seuils}\ a\text{ et }b\rightarrow\text{Intervalle}\ [a;b]}$$

---

## 4. Exercices faciles

### Exercice 1 — Lire le quotient
Soit $X\sim\mathcal B(10;0,25)$.

a. Calculer $\dfrac{P(X=2)}{P(X=1)}$ et $\dfrac{P(X=3)}{P(X=2)}$.

b. En déduire la valeur la plus probable de $X$.

<details>
<summary>👉 Correction</summary>

**a.** Avec $\dfrac{P(X=k+1)}{P(X=k)}=\dfrac{(n-k)p}{(k+1)(1-p)}$ :

$$\frac{P(X=2)}{P(X=1)}=\frac{9\times0,25}{2\times0,75}=\frac{2,25}{1,5}=1,5$$

$$\frac{P(X=3)}{P(X=2)}=\frac{8\times0,25}{3\times0,75}=\frac{2}{2,25}=\frac{8}{9}\approx0,89$$

**b.** Le premier quotient est supérieur à 1 : $P(X=2)>P(X=1)$. Le second est inférieur à 1 : $P(X=3)<P(X=2)$. Comme le quotient décroît avec $k$, les barres montent puis descendent.

$$\boxed{\text{La valeur la plus probable est }k=2}$$

avec $P(X=2)\approx0,2816$.

</details>

---

### Exercice 2 — Symétrie « succès / échecs »
Soit $X\sim\mathcal B(8;0,3)$ et $Y=8-X$.

a. Quelle est la loi de $Y$ ?

b. Justifier que $P(X=3)=P(Y=5)$, puis calculer cette probabilité à $10^{-4}$ près.

<details>
<summary>👉 Correction</summary>

**a.** $Y$ compte les échecs :

$$\boxed{Y\sim\mathcal B(8;0,7)}$$

**b.** $Y=5\Longleftrightarrow8-X=5\Longleftrightarrow X=3$, donc $P(X=3)=P(Y=5)$.

$$P(X=3)=\binom83\times0,3^{3}\times0,7^{5}=56\times0,027\times0,16807$$

$$\boxed{P(X=3)\approx0,2541}$$

</details>

---

### Exercice 3 — Cas $p=\frac12$
Soit $X\sim\mathcal B(10;0,5)$. Montrer que $P(X=3)=P(X=7)$ sans les calculer, puis donner leur valeur.

<details>
<summary>👉 Correction</summary>

$$P(X=3)=\binom{10}{3}\,0,5^{10}\qquad P(X=7)=\binom{10}{7}\,0,5^{10}$$

et $\dbinom{10}{3}=\dbinom{10}{7}=120$, donc les deux probabilités sont égales.

$$\boxed{P(X=3)=P(X=7)=\frac{120}{1024}\approx0,1172}$$

</details>

---

### Exercice 4 — Fréquence et probabilité
Avec le programme du cours (3.4), un élève obtient les fréquences $0,0791$ puis $0,0768$ pour l'évènement $X\geq5$ avec $X\sim\mathcal B(10;0,25)$.

a. Calculer la valeur exacte de $P(X\geq5)$ sachant que $P(X\leq4)\approx0,9219$.

b. Les résultats de l'élève sont-ils cohérents ? Pourquoi ne trouve-t-il pas exactement la même valeur à chaque exécution ?

<details>
<summary>👉 Correction</summary>

**a.**

$$P(X\geq5)=1-P(X\leq4)\approx1-0,9219$$

$$\boxed{P(X\geq5)\approx0,078}$$

**b.** Oui : $0,0791$ et $0,0768$ sont proches de $0,078$. Une fréquence obtenue par simulation **fluctue** autour de la probabilité : chaque exécution tire de nouveaux nombres au hasard, donc le résultat change légèrement d'une fois à l'autre.

</details>

---

## 5. Exercices moyens

### Exercice 5 — La graine la plus probable
Une graine germe avec probabilité $0,85$, indépendamment des autres. On sème 20 graines et $X$ compte celles qui germent.

a. Montrer que $P(X=k+1)>P(X=k)\Longleftrightarrow k<16,85$.

b. En déduire la valeur la plus probable de $X$.

<details>
<summary>👉 Correction</summary>

$X\sim\mathcal B(20;0,85)$.

**a.** D'après le cours, $P(X=k+1)>P(X=k)\Longleftrightarrow k<(n+1)p-1$, soit :

$$k<21\times0,85-1=16,85$$

**b.** Les barres montent tant que $k\leq16$ : $P(X=17)>P(X=16)$. Pour $k=17$, on n'a plus $k<16,85$ : $P(X=18)<P(X=17)$.

$$\boxed{\text{La valeur la plus probable est }17}$$

avec $P(X=17)\approx0,2428$.

</details>

---

### Exercice 6 — Trois histogrammes
On dispose des lois $\mathcal B(8;0,2)$, $\mathcal B(8;0,5)$ et $\mathcal B(8;0,8)$.

a. Pour chacune, déterminer la valeur la plus probable avec la condition $k<(n+1)p-1$.

b. Expliquer pourquoi les histogrammes de $\mathcal B(8;0,2)$ et $\mathcal B(8;0,8)$ sont symétriques l'un de l'autre.

<details>
<summary>👉 Correction</summary>

**a.**

- $\mathcal B(8;0,2)$ : $(n+1)p-1=9\times0,2-1=0,8$. Les barres montent pour $k=0$ seulement, donc le sommet est en $\boxed{1}$.
- $\mathcal B(8;0,5)$ : $9\times0,5-1=3,5$. Les barres montent jusqu'à $k=3$, donc le sommet est en $\boxed{4}$.
- $\mathcal B(8;0,8)$ : $9\times0,8-1=6,2$. Les barres montent jusqu'à $k=6$, donc le sommet est en $\boxed{7}$.

**b.** Si $X\sim\mathcal B(8;0,2)$, alors $8-X\sim\mathcal B(8;0,8)$. Ainsi, pour tout $k$ :

$$P(X=k)=P(8-X=8-k)$$

L'histogramme de $\mathcal B(8;0,8)$ est donc le miroir de celui de $\mathcal B(8;0,2)$ (sommets en $1$ et $7=8-1$).

</details>

---

### Exercice 7 — Trouver l'intervalle
Soit $X\sim\mathcal B(30;0,4)$. On donne :

| $k$ | 6 | 7 | 15 | 16 | 17 |
|---|---|---|---|---|---|
| $P(X\leq k)$ | $0,0172$ | $0,0435$ | $0,9029$ | $0,9519$ | $0,9788$ |

a. Déterminer le plus petit entier $a$ tel que $P(X\leq a)>0,025$.

b. Déterminer le plus petit entier $b$ tel que $P(X\leq b)\geq0,975$.

c. Donner $P(a\leq X\leq b)$ et conclure.

<details>
<summary>👉 Correction</summary>

**a.** $P(X\leq6)\approx0,0172\leq0,025$ et $P(X\leq7)\approx0,0435>0,025$ :

$$\boxed{a=7}$$

**b.** $P(X\leq16)\approx0,9519<0,975$ et $P(X\leq17)\approx0,9788\geq0,975$ :

$$\boxed{b=17}$$

**c.**

$$P(7\leq X\leq17)=P(X\leq17)-P(X\leq6)\approx0,9788-0,0172=0,9616$$

$$\boxed{P(7\leq X\leq17)\approx0,962\geq0,95}$$

Donc $X$ appartient à l'intervalle $[7;17]$ avec une probabilité d'au moins $0,95$.

</details>

---

## 6. Exercices difficiles

### Exercice 8 — Combien de lignes ?
Une entreprise compte 100 salariés. À un instant donné, chacun téléphone avec probabilité $0,1$, indépendamment des autres. Le standard possède $c$ lignes ; il sature si **plus de $c$** salariés appellent en même temps. $X$ est le nombre de salariés qui appellent.

a. Donner la loi de $X$ et calculer la probabilité de saturation pour $c=10$.

b. Écrire une boucle Python qui donne le plus petit $c$ tel que la probabilité de saturation soit au plus $0,05$. Donner ce nombre.

<details>
<summary>👉 Correction</summary>

**a.** $X\sim\mathcal B(100;0,1)$ (100 appels indépendants de même probabilité). Pour $c=10$ :

$$P(X>10)=1-P(X\leq10)$$

$$\boxed{P(X>10)\approx0,417}$$

**b.**

```python
c = 0
while 1 - cumul(100, 0.1, c) > 0.05:
    c = c + 1
print(c)
```

Le programme affiche $\boxed{15}$ : en effet $P(X>14)\approx0,0726>0,05$ et $P(X>15)\approx0,0399\leq0,05$ (le seuil est donc atteint pour $c=15$).

Avec 15 lignes, le standard sature dans moins de $5\,\%$ des cas.

</details>

---

### Exercice 9 — Cent lancers de pièce
On lance 100 fois une pièce équilibrée. $X$ compte les « pile ».

a. Déterminer $a$ et $b$ tels que $P(a\leq X\leq b)\geq0,95$, avec la méthode du cours. On donne $P(X\leq39)\approx0,0176$, $P(X\leq40)\approx0,0284$, $P(X\leq59)\approx0,9716$ et $P(X\leq60)\approx0,9824$.

b. Vérifier que l'intervalle est symétrique autour de $50$ et expliquer pourquoi.

c. Une pièce donne 62 « pile » sur 100 lancers. Qu'en penser ?

<details>
<summary>👉 Correction</summary>

**a.** $X\sim\mathcal B(100;0,5)$ et $\alpha=0,05$.

- $P(X\leq39)\approx0,0176\leq0,025$ et $P(X\leq40)\approx0,0284>0,025$ : $a=40$.
- $P(X\leq59)\approx0,9716<0,975$ et $P(X\leq60)\approx0,9824\geq0,975$ : $b=60$.

$$P(40\leq X\leq60)\approx0,9824-0,0176=0,9648$$

$$\boxed{[a;b]=[40;60]}$$

**b.** $40=50-10$ et $60=50+10$. L'intervalle est centré sur $50$ car, pour $p=\dfrac12$, l'histogramme est symétrique : $P(X=k)=P(X=100-k)$.

**c.** $62>60$ : la valeur observée sort de l'intervalle. Pour une pièce équilibrée, $P(X\geq62)\approx0,0105$, soit environ une chance sur cent. Il est raisonnable de **douter** de l'équilibre de la pièce, sans pour autant en être certain.

</details>

---

### Exercice 10 — Gare aux arrondis
Soit $X\sim\mathcal B(30;0,5)$. Un élève lit $P(X\leq18)\approx0,900$ à la calculatrice et répond que $18$ est le plus petit entier $b$ tel que $P(X\leq b)\geq0,9$.

a. A‑t‑il raison ? On donne la valeur plus précise $P(X\leq18)\approx0,89976$ et $P(X\leq19)\approx0,95063$.

b. Quel est le plus petit entier $b$ tel que $P(X\leq b)\geq0,95$ ?

<details>
<summary>👉 Correction</summary>

**a.** Non. La valeur arrondie $0,900$ cache le fait que $P(X\leq18)\approx0,89976<0,9$. Le plus petit entier $b$ tel que $P(X\leq b)\geq0,9$ est donc :

$$\boxed{b=19}$$

**b.** $P(X\leq18)\approx0,8998<0,95$ et $P(X\leq19)\approx0,9506\geq0,95$ :

$$\boxed{b=19}$$

> **Leçon :** pour un seuil, on compare **toujours** avec des valeurs non arrondies (calculatrice, Python), jamais avec des valeurs déjà arrondies à $10^{-3}$.

</details>

---

## 7. Exercices très difficiles / réflexion

*Faire l'un des deux en séance, laisser l'autre en travail personnel.*

### Exercice 11 — Ce dé est‑il truqué ?
Parcoursupe obtient 17 fois « 6 » sur 60 lancers d'un dé. On suppose le dé équilibré ; $X$ est le nombre de « 6 » en 60 lancers.

a. Donner la loi de $X$ et calculer $P(X\geq17)$.

b. Déterminer le plus petit entier $a$ tel que $P(X\geq a)\leq0,05$. On donne $P(X\geq15)\approx0,0648$ et $P(X\geq16)\approx0,0338$.

c. Que peut-on conclure ? Est‑ce certain ?

<details>
<summary>👉 Correction</summary>

**a.**

$$\boxed{X\sim\mathcal B\!\left(60;\frac16\right)}$$

$$P(X\geq17)=1-P(X\leq16)\approx\boxed{0,0164}$$

**b.** $P(X\geq15)\approx0,0648>0,05$ et $P(X\geq16)\approx0,0338\leq0,05$ :

$$\boxed{a=16}$$

En Python :

```python
a = 0
while 1 - cumul(60, 1/6, a - 1) > 0.05:
    a = a + 1
print(a)                 # affiche 16
```

**c.** $17\geq16$ : si le dé était équilibré, obtenir autant de « 6 » aurait une probabilité d'environ $1,6\,\%$ seulement. On peut donc **soupçonner** que le dé est truqué.

Ce n'est **pas certain** : il reste un risque (ici inférieur à $5\,\%$) de se tromper en rejetant un dé en réalité équilibré.

</details>

---

### Exercice 12 — Simuler un intervalle
On reprend $X\sim\mathcal B(50;0,3)$ et l'intervalle $[9;22]$ du cours (probabilité $\approx0,969$).

a. Écrire une fonction `freq_intervalle(n, p, a, b, N)` qui simule $N$ fois la loi et renvoie la fréquence des cas où $a\leq X\leq b$. On pourra utiliser la fonction `simul` du cours.

b. Avec $N=10000$, quelle valeur attend‑on ? Pourquoi trouve‑t‑on des résultats légèrement différents à chaque exécution ?

<details>
<summary>👉 Correction</summary>

**a.**

```python
def freq_intervalle(n, p, a, b, N):
    c = 0
    for j in range(N):
        x = simul(n, p)
        if a <= x <= b:
            c = c + 1
    return c / N

print(freq_intervalle(50, 0.3, 9, 22, 10000))
```

**b.** On attend une valeur proche de :

$$\boxed{P(9\leq X\leq22)\approx0,969}$$

À chaque exécution, les nombres aléatoires sont différents : la fréquence **fluctue** de quelques millièmes autour de la probabilité. Cette fluctuation diminue quand $N$ augmente.

</details>

---

## 8. Bilan de fin de séance (5 min)

### Les 4 réflexes à retenir

#### Réflexe 1 — Allure
**L'histogramme monte puis descend ; le sommet est proche de $np$.**

#### Réflexe 2 — Symétries
**Si $p=\dfrac12$, l'histogramme est symétrique ; $n-X$ suit $\mathcal B(n;1-p)$.**

#### Réflexe 3 — Simulation
**Une fréquence simulée fluctue autour de la probabilité.**

#### Réflexe 4 — Seuils
$$\boxed{a:\ P(X\leq a)>0,025\qquad b:\ P(X\leq b)\geq0,975\qquad P(a\leq X\leq b)\geq0,95}$$

et on justifie chaque seuil par deux probabilités consécutives, sans valeur arrondie.

---

## 9. Travail personnel

- refaire les exercices 1, 5 et 6 (allure et quotient) ;
- refaire les exercices 7, 9 et 10 (seuils et intervalles) ;
- relire et expliquer ligne par ligne les programmes `simul` et « les deux seuils » ;
- l'exercice très difficile non traité en séance (11 ou 12).

### Pour le professeur

Noter à la fin de la séance :

- lecture correcte du quotient $\dfrac{P(X=k+1)}{P(X=k)}$ ;
- compréhension de la fluctuation d'une fréquence ;
- justification d'un seuil par deux probabilités consécutives ;
- vigilance sur les valeurs arrondies ;
- aisance avec les boucles `while` en Python.

> **Lien avec la séance suivante (TG‑22) :** on a vu où se situe le « centre » de la loi (autour de $np$) et sa largeur (l'intervalle $[a;b]$). La séance suivante donne ces deux informations sous forme de **formules** : espérance $E(X)=np$ et écart-type $\sigma(X)=\sqrt{np(1-p)}$.
