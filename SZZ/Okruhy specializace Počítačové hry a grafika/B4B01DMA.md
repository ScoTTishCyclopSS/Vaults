## Obsah
1. Vlastnosti celých čı́sel
2. Euklidův algoritmus
3. Binárnı́ relace
4. Matematická indukce
5. Rekurzivnı́ vztahy

## 1. Vlastnosti celých čı́sel

### Dělitelnost: definice, základní vlastnosti (tranzitivita a podobně), gcd(a,b)

- celá čísla Z se skládají z přirozených čísel, nuly a záporných celých čísel
- množina je uzavřena na operaci sčítání, odčítání a násobení

Řekneme, že **a** dělí **b**, značeno $a|b$, jestliže existuje $k ∈ Z$, že $b = ak$
- **a** je faktor **b**
- **b** je násobek **a**
- **b** je dělitelné číslem **a** (pokud toto není pravda, tak píšeme $a ⫮ b$)

#### GCD (greatest common divisor)

**Společný dělitel (common divisor)** čísel $a, b$ je $d ∈ N$ jestliže $d|a$ a $d|b$.

**Největší společný dělitel (greatest common divisor)** $gcd(a, b)$ je největší prvek množiny jejich společných dělitelů, pokud je alespoň jedno z $a, b$ nenulové.
- $gcd(0, 0) = 0$
- $gcd(a, 0) = a$
- $gcd(a, b) = 1$ ? -> $a, b$ jsou **nesoudělná**

EX: (pomocí prvočíselného rozkladu)
- $540 = 54 × 10 = 2 × 27 × 2 × 5 = 2^2 × 3^3 × 5$
- $336 = 4 × 84 = 4 × 4 × 21 = 2^4 × 3 × 7$
- $gcd(540, 336) = 2^2 × 3 = 12$

#### LCM (least common multiplier)

**Společný násobek (common multiple)** čísel a, b je $d ∈ N$ jestliže $a|d$ a $b|d$.

**Nejmenší společný násobek (least common multiple)** $lcm(a, b)$ je nejmenší prvek množiny jejich společných násobků, pokud jsou obě $a, b$ nenulové.
- $lcm(a, 0) = lcm(0, b) = 0$
- $lcm(a, b) ⋅ gcd(a, b) = ∣a∣⋅∣b∣$

EX: (pomocí prvočíselného rozkladu)
- $540 = 54 × 10 = 2 × 27 × 2 × 5 = 2^2 × 3^3 × 5$
- $336 = 4 × 84 = 4 × 4 × 21 = 2^4 × 3 × 7$
- $lcm(540, 336) = 2^4 × 3^3 × 5 × 7 = 15120$

---

### Celá čísla modulo n

#### Konguence

Řekneme, že čísla $a, b ∈ Z$ jsou **kongruentní modulo n**, značeno $a ≡ b \pmod n$, jestliže $n$ dělí $b − a$ ($n|a-b$).

EX:  
(−2) ≡ 13 (mod 5), protože 13 − (−2) = 15, což je dělitelné pěti.

Poznamka: $a ≡ b$ (mod $n$) -> existuje $k \in Z$ takové, že $a = b + k \cdot n$ -> $(a$ mod $n)$ = $(b$ mod $n)$, tj. jsou si rovny zbytky po dělení číslem $n$.

#### Malá fermatova (ferneotova) věta

Pro každé prvočíslo $p$ a každé celé číslo $a$ platí:
- $a^p \equiv a{\pmod p}$
- $a^{{p-1}}\equiv 1{\pmod p}$

EX:
Spocitat vyraz pomoci male fermatove vety: $(15)^{146} - 21{\pmod {13}}$
1.  p = 13 (je prvočíslo), a = 15 (celé číslo), p - 1 = 12, pak:
2. $(15)^{146} - 21 \equiv (2^{12})^{12} × 4 - 21\pmod{13}$
3. $a^{{p-1}}\equiv 1{\pmod p}$ => $2^{12} \equiv 1\pmod{13}$
4. $21\pmod{13} \equiv 8$
5. $(15)^{146} - 21 \equiv 4 - 8 \equiv -4 \equiv 9\pmod{13}$


#### Inverzní číslo (inverse number)

Nechť $a ∈ Z$. pak $b ∈ Z$ je **inverzní číslo** k $a \pmod n$, jestliže $a · b ≡ 1\pmod n$.

==$ax ≡ 1 (mod n)$ <=> $gcd(a, n) = 1$!==

[[#Hledání Inverzního čísla ve světě modulo]]

---

### Bezoutova věta/rovnost/identita (Bezout’s identity)

Poskytne gcd jako lineární kombinaci čísel a, b!
Definice: Nechť $a, b ∈ Z$. Pak existují $A, B ∈ Z$ takové, že $gcd(a, b) = Aa + Bb$.

EX:
(Protože se téměř všechny příklady opírají o euklidovský algoritmus, uveďme si příklad z [[#Jak funguje, teoreticky (přechod ke zbytku po dělení) i prakticky, jak se pozná kdy končí|něj]])

Zjistili jsme, že gcd(408, 108) = 12. Jak vyjádříme 12 jako lineární kombinaci 408 a 108? ==Přečteme si běh Euklidova algoritmu od konce!==

1. poslední rozklad je 84 = 3 · 24 + 12, 
    tedy zbytek po dělení 12 = 84 − 3 · 24
2. předtím 108 = 84 + 24, 
    tedy zbytek po dělení 24 = 108 − 84, 
    a proto 12 = 84 − 3 · (108 − 84) = (−3) · 108 + 4 · 84
3. první řádek je 408 = 3 · 108 + 84, 
    tedy 84 = 408 − 3 · 108, 
    a proto 12 = (−3) · 108 + 4 · (408 − 3 · 108) = 4 · 408 + (−15) · 108
4. nakonec je gcd(408, 108) = 4 · 408 + (−15) · 108, kde A = 4, B = -15!

#### Diofanticke rovnice

V mnoha aplikacích pracujeme zcela přirozeně ve světě celých čísel, což je ale problém, protože naše obvyklé metody řešení rovnic pracují v množině reálných čísel. 

Pojmem **lineární diofantická rovnice** označujeme libovolnou rovnici typu $ax + by = c$ s neznámými $x, y,$ kde $a, b, c ∈ Z$ a vyžadujeme také řešení $x, y ∈ Z$

- Jestliže c není násobkem gcd(a, b), tak řešení neexistuje
- Jestliže c je násobkem gcd(a, b), tak například rozšířeným Euklidovým algoritmem najděte A, B ∈ Z takové, že gcd(a, b) = Aa + Bb
- Když tuto rovnici vynásobíme (celým) číslem c gcd(a, b) , tak hned vidíme, že čísla x = A c gcd(a, b) , y = B c gcd(a, b) jsou řešením dané rovnice.

EX1:
Lze vyplatit 1250 korun pomocí mincí o hodnotách 6 a 15? 
Zajímá nás tedy řešení rovnice $15x + 6y = 1250$

1. $gcd(15, 6) = 3$
2. $1250 / 3 = 416.66 \notin Z$ => úloha neni řešitelná!

EX2:
Lze odměřit 1200 litrů pomocí nádob s objemem 6 a 15 litrů?
Zajímá nás tedy řešení rovnice $15x + 6y = 1200$

1. $gcd(15, 6) = 3$
2. $1200 / 3 = 400 \in Z$ => úloha je řešitelná!
3. pro  $gcd(15, 6) = 3$ (podle rozsireneho Euklidovskeho algoritmu) plati A = 1, B = -2
4. euklid. identita je $3 = 1 · 15 + (-2) · 6$
5. vynásobíme 400 -> $1200 = 400 · 15 + (-800) · 6$
6. x = 400, y = -800

#### Hledání Inverzního čísla ve světě modulo

Hledání [[#Inverzní číslo (inverse number)|inverzního prvku]] k cislu $a$ v $Z_n$:
1. Pomocí [[#Jak funguje jeho rozšířená verze, která umí poskytnout Bezoutovu identitu (prakticky)|rozšířeného Euklidova algoritmu]] najděte $gcd(a, n) = Aa + Bn$. 
   Jestliže $gcd(a, n) > 1$, pak inverzní prvek k $a$ v $Z_n$ neexistuje
2. Jestliže $gcd(a, n) = 1$, pak Bezoutova identita dává $1 = a ·A+B ·n$. To znamená, že $a ·A ≡ 1 (\mod n)$ a $x = A$ je inverzní číslo k $a$ modulo $n$. 

EX:

Inv. cislo k 25 modulo 42: $25x ≡ 1 (\mod 42)$
Bezout = $42A + 25B = 1$

gcd(42, 25) = ?

q| gcd(-,-) | A | B | Krok
:-: | :-: | :-: | :-: | :--
\- | **A** = 42 | 1 | 0 | 42 = 1×42 + 0×25
==1== | **B** = 25 | 0 | 1 | 25 = 0×42 + 1×25
==1== | 17 | 1 | -1 | **A':** 42 = 1×42 + 0×25<br>**B':** 25 = 0×42 + 1×25 \|==×1==<br>**A'-B':** 17 = 1×42 + (-1)×25
==2== | 8 | -1 | 2 | **A':** 25 = 0×42 + 1×25<br>**B':** 17 = 1×42 + (-1)×25 \|==×1==<br>**A'-B':** 8 = (-1)×42 + 2×25
==1== | 1 | 3 | -5 | **A':** 17 = 1×42 + (-1)×25<br>**B':** 8 = (-1)×42 + 2×25 \|==×2==<br>**A'-B':** 1 = 3×42 + -5×25
\- | 0 | - | - | -

=> gcd(42, 25) = 1 = 3×42 + -5×25
=> inverzní číslo k 25 (modulo 42) je -5

---

## 2. Euklidův algoritmus

Lze jím vypočítat **největšího společného dělitele (gcd)** dvou přirozených čísel!

### Jak funguje, teoreticky (přechod ke zbytku po dělení) i prakticky, jak se pozná kdy končí

Nechť $a,b \in N$, nechť $q,r \in N_0$ splňují $a = q \cdot b + r$ a $0 \leq r < b$. Pak platí následující: $d \in N$ je společný dělitel $a,b$ právě tehdy, když je to společný dělitel $b,r$.

- $gcd(a, b) = gcd(b, r)$
- $a = q \cdot b + r$, kde $q$ je maximální multiplikátor a $r$ je zbytek po dělení
- opakovaně hledáme $gcd$ pro dvojici $b, r$ místo $a,b$
- pokud zbytek $r$ roven nule, pak algoritmus končí, protože to znamená, že $b$ je dělitelem $a$ a tím pádem je gcd nalezen!

EX:
gcd(408, 108) = ?
1. 408 = 3 · 108 + 84, pak:
    gcd(408, 108) = gcd(108, 84)
2. 108 = 1 · 84 + 24, pak:
    gcd(408, 108) = gcd(108, 84) = gcd(84, 24)
3. 84 = 3 · 24 + 12, pak:
    gcd(408, 108) = gcd(108, 84) = gcd(84, 24) = gcd(24, 12)
4. 24 = 2 · 12 + 0, pak:
    gcd(408, 108) = gcd(108, 84) = gcd(84, 24) = gcd(24, 12) = gcd(12, 0) = 12

---

### Jak funguje jeho rozšířená verze, která umí poskytnout Bezoutovu identitu (prakticky)

Rozšířená verze Euklidova algoritmu vypočítá nejen největší společný dělitel (gcd) dvou čísel, ale také [[#Bezoutova věta/rovnost/identita (Bezout’s identity)|Bezoutovy koeficienty]], které umožňují vyjádřit tento gcd jako lineární kombinaci těchto dvou čísel.

EX:
gcd (408, 108) = ?

q| gcd(-,-) | A | B | Bezout.
:-: | :-: | :-: | :-: | :--
\- | 408 | 1 | 0 | 408 = 1×408 + 0×108
==3== | 108 | 0 | 1 | 108 = 0×408 + 1×108
==1== | 84 | 1 | -3 | **x:** 408 = 1×408 + 0×108<br>**y:** 108 = 0×408 + 1×108 \|==×3==<br>**x-y:** 84 = 1×408 + (-3)×108
==3== | 24 | -1 | 4 | **x:** 108 = 0×408 + 1×108<br>**y:** 84 = 1×408 + (-3)×108 \|==×1==<br>**x-y:** 24 = (-1)×408 + 4×108
==2== | 12 | 4 | -15 | **x:** 84 = 1×408 + (-3)×108<br>**y:** 24 = (-1)×408 + 4×108 \|==×3==<br>**x-y:** 12 = 4×380 + (-15)×108
\- | 0 | - | - | - | -

=> gcd (408, 108) = 12 = 4×380 + (-15)×108

---

## 3. Binárnı́ relace

### Co znamená prakticky a jak se definuje matematicky

Binární relace říká, jestliže a jak jsou dva prvky z různých množin vzájemně spojeny nebo uspořádány. Například může říkat, zda je jeden prvek větší než druhý, zda jsou si rovny, zda jeden prvek patří do jiné množiny atd.

Binární relace mezi dvěma množinami může být graficky znázorněna pomocí šipek, linií nebo jiných vizuálních prvků. To umožňuje lepší pochopení, jak prvky mezi sebou souvisí.

Nechť $A,B$ jsou množiny. Libovolná podmnožina $R \subseteq A \times B$ se nazývá **relace** z $A$ do $B$, jestliže  $(a, b) \in R$, pak to značíme $aRb$ a řekneme, že a je v relaci s b vzhledem k R. Jestliže $(a, b) \notin R$, pak řekneme, že a není v relaci s b vzhledem k R.

---

### Čtyři základní vlastnosti

#### Reflexivita

R je **reflexivní**, jestliže pro všechna $a ∈ A$ platí **aRa**. např. "je stejný".

Popis | Obrazek
--- | :-:
Aby byla relace reflexivní, musí být každý prvek množiny A v relaci sám se sebou ("reflexe" je odraz, třeba v zrcadle)<br>$M = \{(1, 1), (2, 2), (3, 3), (4, 4)\}$ | ![[Pasted image 20230826163119.png\|150]]

#### Symetrie

Popis | Obrazek
--- | :-:
Symetrie znamená podívat se na všechny možné dvojice prvků z A a pro každou z nich ověřit platnost jisté vlastnosti, která má formu implikace (aRb => bRa). Jinymy slovy: pokud je (a, b) v relaci, pak musí být i (b, a)<br>Plati pro stejnou mnozinu <br>$M = \{(1, 1), (2, 2), (3, 3), (4, 4)\}$ | ![[Pasted image 20230826163119.png\|150]] 

#### Antisimetrie

Popis | Obrazek
--- | :-:
R je **antisymetrická**, jestliže pro všechna $a, b ∈ A$ platí $(aRb ∧ bRa) ⇒ a = b$. Jinymy slovy: pokud (a,b) je v relaci a zároveň (b,a) je v relaci, pak a = b.<br>Mnozina:$M = \{(1, 2), (3, 2), (4, 2), (4, 3), (4, 4)\}$ **(4,4)** | ![[Pasted image 20230826164807.png\|150]] 

#### Tranzitivita

Popis | Obrazek
--- | :-:
R je **tranzitivní**, jestliže pro všechna $a, b, c ∈ A$ platí $(aRb ∧ bRc) ⇒ aRc$. <br>Jinymy slovy: pokud jsou (a, b) a (b, c) v relaci, pak musí být i (a, c). <br>např. "A je vyšší než B, B je vyšší než C ⇒ A je vyšší než C"<br>Mnozina:$M = \{(1, 2), (3, 2), (4, 2), (4, 3), (4, 4)\}$ **(4, 3), (3, 2) -> (4, 2)** | ![[Pasted image 20230826164807.png\|150]]

---

## 4. Indukce

==Slabý a silný princip matematické indukce jsou ekvivalentní!==

### Slabá indukce

Nechť $n_0 ∈ Z$, nechť $V(n)$ je vlastnost celých čísel, která má smysl pro $n ≥ n_0$. Předpokládejme, že jsou splněny následující předpoklady: 
- (0) -> $V(n_0)$ platí. 
- (1) -> Pro každé $n ∈ Z, n ≥ n_0$ je pravdivá následující implikace: $V(n) ⇒ V(n + 1)$
    Jestliže platí $V(n)$, pak platí i $V(n + 1)$
    Potom $V(n)$ platí pro všechna $n ∈ Z, n ≥ n_0$


(0) říká, že umíme vylézt na první příčku žebříku
(1) říká, že když už někde jsme, tak umíme vylézt o příčku výš
Selský rozum říká, že pak už se dostaneme na žebříku všude.

==Pokud tedy chceme dokázat univerzální platnost nějaké vlastnosti V, stačí dokázat pravdivost tvrzení (0) - **základní krok** a (1) - **indukční krok**.==

EX:
Pro $n ∈ N$ je $V(n)$ tvrzení, že $2 + 4 + · · · + 2n = n(n + 1)$ (**indukční předpoklad**)

0. Dokážeme základní krok!
    - $n = 1$, $V(1)$ zní $2 = 2$, což je pravda
1. Dokážeme indukční krok!
    - vezmeme ==libovolné== $n ≥ 1 ∈ N$ (obecné, ne nějaké konkrétní) a dokážeme, že pro něj platí implikace $V(n) ⇒ V(n + 1)$
    -  pouzijme indukční předpoklad: 
        $2 + 4 + · · · + 2n = n(n + 1)$ 
        $2 + 4 + · · · + 2n + 2(n + 1) = (n + 1)(n + 2)$ 
        $[2 + 4 + · · · + 2n] + (2n + 2) = (n + 1)(n + 2)$
        $n(n + 1) + (2n + 2) = (n + 1)(n + 2)$
    - gg!

---

### Silná indukce

Nechť $n_0 ∈ Z$, nechť $V(n)$ je vlastnost celých čísel, která má smysl pro $n ≥ n0$. Předpokládejme, že následující předpoklady jsou splněny:
- (0) -> $V(n_0)$ platí
- (1) -> Pro každé $n ∈ Z, n ≥ n0$ je pravdivá následující implikace: 
  $V(k) ⇒ V(n + 1)$
    Jestliže platí $V(k)$ pro všechna $k = n_0, n_0 + 1, . . . , n$, pak platí i $V(n + 1)$
    Potom $V(n)$ platí pro všechna $n ∈ Z, n ≥ n0$

(0) říká, že umíme vylézt na první příčku žebříku
(1) říká, že v případě, že umíme vylézt na prvních n příček, tak umíme vylézt i o jednu výše.
Princip pak tvrdí, že umíme vylézt na celý žebřík.

EX:
Pro $n ∈ N$ je $V(n)$ tvrzení, že $2 + 4 + · · · + 2n = n(n + 1)$ (**indukční předpoklad**)

0. Dokážeme základní krok!
    - $n = 1$, $V(1)$ zní $2 = 2$, což je pravda
1. Dokážeme indukční krok!
    - vezmeme $k = n_0, n_0 + 1, . . . , n$ a dokážeme, že pro něj platí implikace $V(k) ⇒ V(n + 1)$
    -  pouzijme indukční předpoklad: 
        $2 + 4 + · · · + 2n = n(n + 1)$ 
        $2 + 4 + · · · + 2n + 2(n + 1) = (n + 1)((n + 1) + 1)$ 
        $[2 + 4 + · · · + 2n] + (2n + 2) = (n + 1)(n + 2)$
        $n(n + 1) + (2n + 2) = (n + 1)(n + 2)$
        $n^2 + 3n + 2 = n^2 + 3n + 2$
    - gg!

---

### Rozdil

Rozdíl spočívá ve způsobu, jakým se indukční krok aplikuje a jaké předpoklady jsou z toho odvozeny.

**Slabá indukce:**

- **Základní krok:** Dokážeme tvrzení pro počáteční hodnotu (např. n=1).
- **Indukční krok:** Předpokládáme platnost tvrzení pro k a dokážeme, že to implikuje platnost pro k+1.

**Silná indukce:**

- **Základní krok:** Dokážeme tvrzení pro počáteční hodnotu (např. n=1).
- **Indukční krok:** Předpokládáme platnost tvrzení pro všechna čísla od 1 do k a dokážeme, že to implikuje platnost pro k+1.

Rozdíl v matematickém zápisu mezi slabou a silnou indukcí může být opravdu malý, a obě formy důkazu mohou vypadat téměř identicky. Klíčový rozdíl spočívá v tom, kolik hodnot se používá v indukčním předpokladu. Slabá indukce používá pouze jednu předchozí hodnotu, zatímco silná indukce používá všechny hodnoty od 1 do k.

---

## 5. Rekurzivnı́ vztahy

### Lineární rekurentní rovnice

**Lineární rekurentní rovnice** řádu $k ∈ N_0$ je libovolná rovnice ve tvaru $a_{n + k} + c_{k − 1}(n)a_{n + k} − 1 + · · · + c_2(n)a_n + 2 + c_1(n)a_{n + 1} + c_0(n)a_n = b_n$ pro všechna $n ≥ n_0$, kde $n_0 ∈ Z$, ci(n) pro $i = {0, . . . , k − 1}$ (tzv. koeficienty rovnice) jsou nějaké funkce $Z → R$, přičemž $c_0(n)$ není identicky nulová funkce, a $\{b_n\}^∞_{n = n_0}$ (tzv. pravá strana rovnice) je pevně zvolená posloupnost reálných čísel. Jestliže $b_n = 0$ pro všechna $n ≥ n_0$, pak se příslušná rovnice nazývá homogenní.

==Tyto rovnice popisují posloupnosti, kde každý člen posloupnosti je lineární kombinací předchozích členů s nějakými koeficienty.==

K získání charakteristických čísel potřebujeme vyřešit rovnici $$p(\lambda) = \lambda^k + c_{k-1}\lambda^{k-1} + ... + c_1\lambda + c_0 = 0$$ které se také říká charakteristická rovnice.

EX:

Vyřešte homogenní lineární rekurentní rovnici $$a_{n + 1} = 4a_{n} - 3a_{n - 1}  + 6 - \frac{1}{2}n \cdot 2^{n}, n \geq 0 | a_1 = 2, a_2 = 11$$

1. Pro řešení je nutné danou rovnost alespoň zjednodušit
	$$a_{n + 1} = 4a_{n} - 3a_{n - 1}  + 6 - \frac{1}{2}n \cdot 2^{n}$$
	$$a_{n + 1} + 3a_{n - 1} - 4a_{n} = 6 - \frac{1}{2}n \cdot 2^{n}$$
	$$min = n - 1 → +1, n ≥ 1$$
	$$a_{n + 2} + 3a_{n} - 4a_{n + 1} = 6 - \frac{1}{2}(n + 1) \cdot 2^{n + 1}$$
	$$a_{n + 2} - 4a_{n + 1} + 3a_{n} = 6 - (n + 1) \cdot 2^{n}$$

2. Nejprve vyřešme homogenní část naší rovnosti (položíme rovno nule):
	$$a_{n + 2} - 4a_{n + 1} + 3a_{n} = 0$$
	Získejme charakteristický polynom:
	$$\lambda^{2} - 4\lambda + 3 = 0 \rightarrow \lambda_{1} = 3, \lambda_{2} = 1$$
	Tím pádem homogenním řešením je (kde $u$ a $v$ jsou vektory):
	$$a_{h,n} = u \cdot 3^n + v, n \geq 1$$

3. Partikulární řešení zjistíme tak, že odhadneme pravou stranu přes polynom
	$$6 - (n + 1) \cdot 2^{n} → A - (n + B)\cdot2^n$$
	Polynom dosadíme do levé strany za $a_{n}$!
	(levou stranu raději rozdělím na několik dílčích částí, označím je $L_i$)
	$$L_1 = [A - (n + 2 + B)2^{n+2}] = [A - (4n + 8 + 4B)2^n]$$
	$$L_2 = [A - (n + 1 + B)2^{n+1}] = [A - (2n + 4 + 2B)2^n]$$
	$$L_3 = [A - (n + B)2^{n}]$$
	$$L = L_1 - 4\cdot L_2 + 3\cdot L_3$$
	$$a_{p,n} = (-3n - 1)\cdot 2^n$$

4. Zkombinováním $a_{n} = a_{p,n} + a_{h, n}$ získáme obecné řešení:
	$$a_{n} = (-3n - 1)\cdot 2^n + 3^nu + v$$

5. Ziskame vektory $u$ a $v$ pomoci kroku $a_1$ a $a_2$
	$$a_1: 2 = (-3 - 1)\cdot 2 + 3u + v = 3u + v - 8$$
	$$a_1: 10 = 3u + v$$
	$$v = 10 - 3u$$
	$$a_2: 11 = (-6 - 1)\cdot 4 + 9u + v = 9u + v - 28$$
	$$a_2: 39 = 9u + v$$
	$$39 = 9u + 10 - 3u$$
	$$29 = 6u$$
	$$u = ..., v = ...$$

---
