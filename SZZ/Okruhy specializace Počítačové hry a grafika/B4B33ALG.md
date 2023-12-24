## Obsáh:
1. Řád růstu funkcí, asymptotická složitost algoritmu.
2. Rekurze, Stromy, binární stromy, prohledávání s návratem.
3. Fronta, zásobník, průchod stromem/grafem do šířky/hloubky.
4. Binární vyhledávací stromy, AVL a B- stromy
5. Algoritmy řazení: Insert Sort, Selection Sort, Bubble Sort, QuickSort Merge Sort, Halda, Heap Sort, Radix sort, Counting Sort.
6. Dynamické programování, struktura optimálního řešení, odstranění rekurze. Nejdelší společná podposloupnost, optimální násobení matic, problém batohu.
7. Rozptylovací tabulky (Hashing), hashovací funkce, řešení kolizí, otevřené a zřetězené tabulky, double hashing, srůstající tabulky, univerzální hashování.

## 1. Řád růstu funkcí, asymptotická složitost algoritmu.

![[Pasted image 20230903090240.png]]

## 2. Rekurze, Stromy, binární stromy, prohledávání s návratem.


### Rekurze

**Rekurze** je chování funkce, při kterém funkce volá sama sebe. Takové funkce se nazývají rekurzivní. Na rozdíl od cyklu se jen několikrát neopakují, ale pracují "uvnitř" sebe navzájem.

Jedná se o metodu řešení problémů podobnou matematické indukci: aby se funkce vykonala, musíte nejprve získat její výsledek zavoláním s jinou hodnotou.

EX:

Nejznámějším příkladem je Fibanacciho číslo, kde každé následující číslo je součtem předchozích dvou.

**Důležité**: Tento problém lze také řešit [[#6. Dynamické programování, struktura optimálního řešení, odstranění rekurze. Nejdelší společná podposloupnost, optimální násobení matic, problém batohu|dynamickým programováním]], a to tak, že vytvoříte pole o n prvcích a uložíte do něj řešení, přičemž samotný kód bude vypadat jako běžný for-cyklus (ano, vyžaduje více paměti, ale volání bude mnohem méně).

#### Mistrova věta (master-theorem)

Umožňuje nalézt asymptotická řešení rekurenčních vztahů, které se mohou objevit při analýze asymptotiky mnoha algoritmů.

$$T(n) = a \cdot T(\frac{n}{b}) + f(n)$$
$$T(n) = a \cdot T(\frac{n}{b}) + (n^d)$$

- $d > log_ba$ ? -> $\theta(n^d)$
- $d < log_ba$ ? -> $\theta(n^{log_ba})$
- $d = log_ba$ ? -> $\theta(n^d\cdot log(n))$

---

### Stromy

![[Pasted image 20230904112757.png]]

![[Pasted image 20230904113847.png]]

---


### Binární stromy

  
**Binární strom** je stromová datová struktura, která se skládá z uzlů, kde každý uzel může mít nejvýše dva potomky: levého a pravého. Binární stromy jsou často používány pro organizaci dat a umožňují efektivní operace jako vyhledávání, vkládání a mazání prvků.

![[Pasted image 20230904120643.png | center | 300]]

#### Pravidelný (regular)

- hloubka je $log(n+1)-1$
- každý uzel má právě dva potomky (krome listy), tedy levého a pravého (BST)

#### Vyvážený (balanced)

- pocet potomku je 0 nebo 2
- ve kterém je rozdíl mezi hloubkou levého a pravého podstromu pro libovolný uzel omezený na konstantní hodnotu (casto 1)

---

### Prohledávání s návratem (Backtracking)

Prohledávání s návratem využívá rekurze k ukládání stavů a postupnému zkoumání možných variant. Jakmile je zjištěno, že daná cesta není platná nebo řešení není možné, algoritmus se vrátí zpět a zkouší jinou cestu. Tímto způsobem postupuje a zkoumá různé možnosti, dokud nenajde řešení nebo prozkoumá všechny možné varianty.

EX:

N-queens problem: umístěte n-králů na šachovnici tak, aby se navzájem neporazili.

1. **Inicializace**: Vytvořte prázdnou $n x n$ šachovnici, kde n je počet dám. Můžete použít dvourozměrné pole (matici), kde každá buňka reprezentuje jedno pole šachovnice.
2. **Rekurzivní funkce**: Vytvořte rekurzivní funkci, která bude umisťovat dámy na šachovnici. Funkce bude přijímat následující argumenty:
     Funkce bude provádět následující kroky:
    - Pokud aktuální řádek je roven n, což znamená, že všechny dámy jsou umístěny, uložte aktuální konfiguraci šachovnice jako řešení.
    - Pro každý sloupec v aktuálním řádku:
        - Zkontrolujte, zda je možné umístit dámu na dané pole, tj. zda není ohrožována žádnou jinou dámou na šachovnici.
        - Pokud je umístění možné, umístěte dámu na pole.
        - Zavolejte rekurzivní funkci pro následující řádek.
        - Pokud rekurzivní volání vrátí True (úspěšné umístění všech dám), vrať True.
        - Jinak odstraňte dámu z aktuálního pole (zpěttrace).
    - Pokud žádné pole v aktuálním řádku nedovolilo úspěšné umístění dámy, vrať False.
3. **Volání rekurzivní funkce**: Zavolejte rekurzivní funkci pro první řádek šachovnice 
4. **Zpracování výsledku**: Pokud rekurzivní funkce vrátí True, pak jste našli řešení, které umožňuje umístit všechny dámy bez ohrožení. Pokud vrátí False, pak řešení neexistuje pro daný vstup.

---

## 3. Fronta, zásobník, průchod stromem/grafem do šířky/hloubky.

### Fronta

Fronta se řídí pravidlem **FIFO (First In First Out)** - položka, která vstoupí jako první, je zároveň položkou, která vyjde jako první!

![[Pasted image 20230904000559.png | center | 600]]

Umožňuje následující operace:
- Enqueue: Přidat prvek na konec fronty
- Dequeue: Odebrat prvek z čela fronty
- IsEmpty: Zkontroluje, zda je fronta prázdná
- IsFull: Zjistí, zda je fronta plná.
- Peek: Zjistí hodnotu přední části fronty, aniž by ji odstranil

**Enqueue:**
1. zkontrolovat, zda je fronta plná
2. pokud je první vkladani prvku - nastavte hodnotu FRONT na 0
3. zvýšit index REAR o 1
4. přidejte nový prvek na pozici, na kterou ukazuje REAR

**Dequeue:**
1. zkontrolujte, zda je fronta prázdná
2. vrátit hodnotu, na kterou ukazuje FRONT
3. zvýšit index FRONT o 1
4. pro poslední prvek resetujte hodnoty FRONT a REAR na -1 (prázdný).

---

### Zásobník

Zásobník se řídí pravidlem **LIFO (Last In First Out)** - položka, která vstoupí jako posledni, je zároveň položkou, která vyjde jako první!

![[Pasted image 20230904002556.png | center | 500]]

Existuje několik základních operací, které nám umožňují provádět různé akce se zásobníkem.
- Push: Přidání prvku na vrchol zásobníku
- Pop: Odstranění prvku z vrcholu zásobníku
- IsEmpty: Zjistí, zda je zásobník prázdný
- IsFull: Zjistí, zda je zásobník plný.
- Peek: Zjistí hodnotu horního prvku bez jeho odstranění

**Push:**
1. zkontrolovat, zda je fronta plná
2. Při vložení prvku zvýšíme hodnotu TOP a umístíme nový prvek na pozici, na kterou ukazuje TOP.

**Pop:**
1. zkontrolujte, zda je fronta prázdná
2. Při vysunutí prvku vrátíme prvek, na který ukazuje TOP, a snížíme jeho hodnotu.

---

### BFS (Breadth first search)

Standardní implementace BFS zařazuje každý vrchol grafu do jedné ze dvou poli:
- visited
- not visited yet

#todo complexity??

Účelem algoritmu je označit každý vrchol jako navštívený a zároveň se vyhnout cyklům!
Algoritmus funguje následovně:
- Začněte tím, že umístíte libovolný z vrcholů grafu na konec fronty
- Vezměte přední prvek fronty a přidejte jej do seznamu navštívených
- Sousední vrcholy, které nejsou v seznamu navštívených, přidejte na konec fronty!
- Opakujte kroky 2 a 3, dokud nebude fronta prázdná.

![[Pasted image 20230904005046.png | center | 500]]

---

### DFS

Standardní implementace DFS zařazuje každý vrchol grafu do jedné ze dvou poli:
- visited
- not visited yet

#todo complexity?

Algoritmus DFS funguje následovně:
- Začněte tím, že umístíte libovolný vrchol grafu na vrchol zásobníku
- Vezměte horní prvek zásobníku a přidejte jej do seznamu navštívených
- Sousední vrcholy tohoto vrcholu, které nejsou v seznamu navštívených, přidejte na vrchol zásobníku
- Opakujte kroky 2 a 3, dokud nebude zásobník prázdný

![[Pasted image 20230904005851.png | center | 500]]

---

![[Pasted image 20230904004908.png | center | 500]]

## 4. Binární vyhledávací stromy, AVL a B- stromy

### BST (Binary Search Tree)

- **Vkládání** hodnoty je podobné vyhledávání
	- levý podstrom je menší než kořen a pravý podstrom je větší než kořen
	- v závislosti na hodnotě postupujeme buď do pravého, nebo do levého podstromu
	- když dosáhneme bodu, kde je levý nebo pravý podstrom nulový, vložíme tam nový uzel
- **Smazání**
	- listový uzel ? → odstraňte uzel ze stromu
	- uzel ma jediný potomek ? -> nahraďte tento uzel jeho potomkem
	- uzel ma oba potomky ? -> získat následníka daného uzlu (==min v pravem podstromu==) a nahrait uzel timto následníkem

. | Vyhledávání | Vkládání | Vymazání
--- | :-: | :-: | :-:
**Nejhorší (Pravidelný)** | $O(n)$ | $O(n)$ | $O(n)$
**Nejlepší (Vyvážený)** | $O(logn)$ | $O(logn)$ | $O(logn)$
**Běžný** | $O(n)$ | $O(n)$ | $O(n)$

![[Pasted image 20230903221037.png]]

---

### AVL

**Vkládání** a **Smazání** proběhne standardně jako v obyčejném BVS:
- rozdíl hloubek je -1, 0, 1 (==kontrola hloubek po kazde operaci!==)
- rozdil je vetsi nez 1 nebo mensi nez -1 ? -> odpovidajici rotace

**Rotace** Když narazíme na rozvážený uzel, do kterého jsme bezprostředně došli...
- dvěma hranami doprava nahoru, provedeme v tomto uzlu **R** rotaci
- dvěma hranami doleva nahoru, provedeme v tomto uzlu **L** rotaci
- hranami doleva a pak doprava nahoru, provedeme v tomto uzlu **LR** rotaci
- hranami doprava a pak doleva nahoru, provedeme v tomto uzlu **RL** rotaci

![[Pasted image 20230904010504.png | center | 300]]

Vyhledávání | Vkládání | Vymazání
:-: | :-: | :-:
$O(logn)$ | $O(logn)$ | $O(logn)$

---

### B-stromy

- **Vyhledávání** je podobné jako u [[#BST (Binary Search Tree)]], pouze pokud hodnota uzlu není větší než pravý klíč a menší než levý klíč, pak leží mezi nimi!
- **Vkládání**
	1. Vyhledejte příslušný uzel pro vložení (сheck povolený počet klíčů v uzlu)
	2. Pokud je uzel plný, postupujte podle následujících kroků:
		- Vkládejte prvky ve vzestupném pořadí
		- Nyní jsou prvky větší než jeho limit. Rozdělte tedy na medián
		- Posuňte mediánový klíč nahoru a vytvořte levé klíče jako levého potomka a pravé klíče jako pravého potomka.
	3. Pokud uzel není plný, postupujte podle níže uvedených kroků
		- Uzel vkládejte v rostoucím pořadí

![[Pasted image 20230903225141.png | center | 500]]

- **Smazání**:
	1. Klic je v listu (Vymazání klíče NEporušuje vlastnost minimálního počtu klíčů)
	   ![[Pasted image 20230903231152.png | 400]]
	2. Klic je v listu (Vymazání klíče porušuje vlastnost minimálního počtu klíčů)
	  Pokud má levý uzel více než minimální počet klíčů, vypůjčte si klíč z tohoto uzlu. 
	  V opačném případě zkontrolujte, zda si chcete vypůjčit klíč z pravého sourozeneckého uzlu.
	  ![[Pasted image 20230903231505.png | 400]]
	3. Pokud již oba sousedne uzly mají minimální počet klíčů, sloučí se uzel buď s levým, nebo s pravým. Toto sloučení se provádí prostřednictvím rodiče uzlu!
	   ![[Pasted image 20230903231725.png | 400]]
	4. Pokud klíč, který má být odstraněn, leží ve vnitřním uzlu
		- Vnitřní uzel, který je smazán, je nahrazen předchůdcem v pořadí, pokud má levý potomek více než minimální počet klíčů.
		- Vnitřní uzel, který je smazán, je nahrazen následníkem v pořadí, pokud má pravý potomek více než minimální počet klíčů.
		![[Pasted image 20230903231935.png | 400]]
		- If either child has exactly a minimum number of keys then, merge the left and the right children.
		![[Pasted image 20230903232158.png | 400]]


Vyhledávání | Vkládání | Vymazání
:-: | :-: | :-:
$O(k\cdot log(kn))$ | $O(k\cdot log(kn))$ | $O(k\cdot log(kn))$

## 5. Algoritmy řazení: Insert Sort, Selection Sort, Bubble Sort, QuickSort Merge Sort, Halda, Heap Sort, Radix sort, Counting Sort.

### Selection sort

Vybere v každé iteraci nejmenší prvek z neuspořádaného seznamu a umístí jej na začátek neuspořádaného seznamu (nebo do uspořádaného seznamu).

- **Stabilnost**: je **nestabilní**, protože procházíte seznam a hledáte minimum, takže pokud máte dvě minima za sebou, vyřadíte to poslední, protože musíte projít celý seznam.
  (e.g. \[3, 2, 4, 1<sub>a</sub>, 1<sub>b</sub>, 6] -> \[1<sub>b</sub>, 3, 2, 4, 1<sub>a</sub>, 6])
- **Složitost**: 
	- Nejhorší -> $O(n^2)$
	- Nejlepší -> $O(n^2)$
	- Běžný -> $O(n^2)$

EX: 
![[Pasted image 20230903090536.png]]

---

### Insertion sort

Porovnejte klíč s prvním prvkem. Pokud je první prvek větší než klíč, pak se klíč umístí před první prvek. (1<9 a 1<5)

- **Stabilnost**: **stabilní**, protože pouze přehazuje čísla na předchozí pozici, dokud není číslo menší nebo rovno (e.g. \[1<sub>a</sub>, 1<sub>b</sub>, 6, 3, 2, 4] -> \[1<sub>a</sub>, 1<sub>b</sub>, 3, 2, 4, 6]).
- **Složitost**: 
	- Nejhorší -> $O(n^2)$
	- Nejlepší -> $O(n)$
	- Běžný -> $O(n^2)$

EX:
![[Pasted image 20230903215102.png]]

---

### Bubble sort

Porovnejte první a druhý prvek: Pokud je první prvek větší než druhý, prohodí se. Nyní porovnejte druhý a třetí prvek. Pokud nejsou v pořadí, prohoďte je. Uvedený postup pokračuje až do posledního prvku.

- **Stabilnost**: je **stabilní**, protože pouze prohodíte dvě čísla, ale neprohodíte je, pokud jsou stejná (e.g. \[1<sub>a</sub>, 1<sub>b</sub>, 6, 3, 2, 4] -> \[1<sub>a</sub>, 1<sub>b</sub>, 3, 2, 4, 6]).
- **Složitost**: 
	- Nejhorší -> $O(n^2)$
	- Nejlepší -> $O(n^2)$
	- Běžný -> $O(n^2)$

**Poznamka**: ==Ve výše uvedeném algoritmu se všechna porovnání provádějí, i když je pole již setříděno, což prodlužuje dobu provádění!== Můžeme zavést další proměnnou "*swapped*". Pokud po iteraci k prohození nedojde, bude hodnota proměnné "*swapped*" false. To znamená, že prvky jsou již setříděny a není třeba provádět další iterace. To zkrátí dobu provádění a pomůže optimalizovat algoritmus.

EX: 
![[Pasted image 20230903091633.png]]

---

### Quick sort (rozděluj a panuj)

1. Nalezení pivotu (buď konec pole, nebo taktiky pro lepší dosažení výsledků).
2. Rozdělení pole na dvě části (méně pivotů vlevo, více pivotů vpravo).
3. Opakování kroků pro obě části, dokud nezbudou pouze 2 prvky pro porovnání
4. Sestavení setříděného pole

- **Stabilnost**: **není stabilní**, protože nevíte, která čísla v seznamu prohodíte, můžete snadno omylem změnit pořadí dvou stejných čísel. 
   (e.g. #todo \[1<sub>a</sub>, 1<sub>b</sub>, 6, 3, 2, 4] -> \[1<sub>a</sub>, 1<sub>b</sub>, 3, 2, 4, 6]).
- **Složitost**: 
	- Nejhorší -> $O(n^2)$
	- Nejlepší -> $O(n×logn)$
	- Běžný -> $O(n×logn)$

**Poznamka**: ==Je zřejmé, že volba posledního prvku jako pivotu není vždy dobrou taktikou, co když je poslední prvek nejmenší?== Proto existují taktiky pro výběr pivotu, například "průměr tří": vezmeme hodnoty prvního a posledního prvku a zjistíme jejich průměrnou hodnotu v poli.

EX:
![[Pasted image 20230903092620.png]]

---

### Merge sort (rozděluj a panuj)

1. Rozdělte pole na dvě části
2. Dělíme tak dlouho, dokud nezůstanou k porovnání pouze 2 (nebo někdy 1) prvky.
3. Při skládání segmentů pole dohromady byste se měli řídit pravidlem: porovnávat prvky podle indexů
	- pro každou část vytvoříme vlastní ukazatel, který se posune až po úspěšném přesunu prvku do setříděného pole:
	  1. \[1, 2, 4] a \[3, 6] -> porovnáme 1 a 3 -> \[1]
	  2. \[~~1~~, 2, 4] a \[3, 6] -> porovnáme 2 a 3 -> \[1, 2]
	  3. \[~~1~~, ~~2~~, 4] a \[3, 6] -> porovnáme 4 a 3 -> \[1, 2, 3]
	  4. \[~~1~~, ~~2~~, 4] a \[~~3~~, 6] -> porovnáme 4 a 6 -> \[1, 2, 3, 4]
	  5. \[~~1~~, ~~2~~, ~~4~~] a \[~~3~~, 6] -> ukládáme 6 -> \[1, 2, 3, 4, 6]
1. Pokračujte ve skládání pole tak dlouho, dokud nezískáme setříděné pole

- **Stabilnost**: je **stabilní**, protože pouze porovnáváte levý a pravý seznam, každopádně pokud jsou dvě čísla stejná, číslo vlevo zůstane vlevo.
   (e.g. \[1<sub>a</sub>, 2, 4] a \[1<sub>b</sub>, 3, 6] -> [1<sub>a</sub>, 1<sub>b</sub>, 2, 3, 4, 6]).
- **Složitost**: 
	- Nejhorší -> $O(n×logn)$
	- Nejlepší -> $O(n×logn)$
	- Běžný -> $O(n×logn)$

EX:
![[Pasted image 20230903093608.png]]

#### Jaký je rozdíl oproti quick sort?

Merge vytvari spoustu doplnujicich poli, ale quick pracuje s puvodnim.

---

### Counting sort

1. Nalezení maxima v poli
    ![[Pasted image 20230903095317.png|450]]
2. Spočítejte všechna čísla (zapište počet jednotlivých prvků)
    ![[Pasted image 20230903095339.png|450]]
3. Vytvořte komutativní součet
    ![[Pasted image 20230903095359.png|450]]
4. Vezměte prvek a získejte index umístění prvku odečtením 1 od komutativního součtu
    (po umístění každého prvku na správnou pozici snižte jeho počet o 1)
    ![[Pasted image 20230903095443.png|500]]

- **Stabilnost**: **stabilní**, ale musíte ==projít původní seznam od konce==, jinak máte zaručenou nestabilitu.
   (e.g. \[4, 1<sub>a</sub>, 1<sub>b</sub>] a soucet \[2, 1] -> pozice pro 1<sub>a</sub> je 2 - 1 = 1, pro 1<sub>b</sub> je 1 - 1 = 0 -> \[1<sub>b</sub>, 1<sub>a</sub>, 2, 4]).
- **Složitost**: 
	- Nejhorší -> $O(n)$
	- Nejlepší -> $O(n)$
	- Běžný -> $O(n)$

---

### Radix sort

1. Najděte největší prvek v poli, tj. $max$. 
2. Nechť $x$ je počet číslic v $max$ ($x$ se počítá, protože musíme projít všechna významná místa všech prvků)
    (e.g. v tomto poli \[121, 23, 1, 45, 100] máme největší číslo 121 a má 3 číslice. Smyčka by tedy měla projít až na sté místo nebo 3-krát)
3. Pro každou pozici položky provedeme [[#Counting sort]] počínaje koncem položky
4. Hlavní pole se bude skládat pouze z čísel aktuální pozice řazení (jedničky, desítky, stovky atd.)
	- Z něj bude také vytvořen komutativní součet

- **Stabilnost**: **stabilní**, protože používá [[#Counting sort]].
- **Složitost**: 
	- Nejhorší -> $O(n)$
	- Nejlepší -> $O(n)$
	- Běžný -> $O(n)$

EX: \[12==1==, 43==2==, 56==4==, 2==3==, ==1==, 4==5==, 78==8==]
![[Pasted image 20230903100458.png|500]]
![[Pasted image 20230903100503.png|400]]
![[Pasted image 20230903100508.png|400]]

---

## 6. Dynamické programování, struktura optimálního řešení, odstranění rekurze. Nejdelší společná podposloupnost, optimální násobení matic, problém batohu.

**Dynamické programování** je metoda pro efektivní řešení určitých optimalizačních úloh. Lze jej použít pro řešení úloh, které lze rozdělit na podúlohy, jejichž optimální řešení lze použít při řešení původní úlohy. Princip dynamického programování spočívá v rekurzivním dělení úlohy na menší části, které se řeší ve vhodném pořadí, jejich výsledky se zaznamenávají a jsou použity pro řešení složitějších podúloh včetně původní úlohy.

### Struktura optimálního řešení

Tato vlastnost se nazývá optimální substruktura. Znamená to, že optimální řešení problému lze sestrojit kombinací optimálních řešení jeho podproblémů. Pokud se například snažíte najít nejkratší cestu mezi dvěma městy a znáte nejkratší cesty mezi všemi mezilehlými městy, můžete tyto kratší cesty zkombinovat a určit tak celkovou nejkratší cestu.

---

### Odstranění rekurze

V rekurzivně formulovaném postupu řešení se opakovaně objevují stejné menší podproblémy. DP umožňuje obejít opakované výpočty, obvykle prostým tabelováním výsledků menších podproblémů, nebo jinými slovy předpočítáním menších výsledků.

---

### Nejdelší společná podposloupnost (Longest Common Subsequence, LCS)

Cílem je najít nejdelší společnou podposloupnost dvou posloupností.
Představme si, že máme dvě posloupnosti A = \[a1, a2, ..., am] a B = \[b1, b2, ..., bn]. Nejdelší společná podposloupnost (LCS) je taková posloupnost C, která je podposloupností jak A, tak B, a zároveň je nejdelší mezi všemi takovými podposloupnostmi.

Postup:
1. Vytvoříme si tabulku o velikosti (m+1) x (n+1), kde každý prvek tabulky bude reprezentovat délku nejdelší společné podposloupnosti pro danou část posloupností A a B
2. Inicializujeme první řádek a první sloupec tabulky na hodnotu 0
3. Pro každý prvek tabulky C\[i]\[j] provedeme následující:
	- Pokud $a_i$ je rovno $b_j$, pak C\[i]\[j] = C\[i-1]\[j-1] + 1 
	- Pokud $a_i$ není rovno $b_j$, pak C\[i]\[j] = max(C\[i-1]\[j], C\[i]\[j-1]) 
5. Nakonec délka LCS je uložena v pravém dolním rohu tabulky, tedy C\[m]\[n]!
6. Po vytvoření tabulky lze rekonstruovat nejdelší společnou podposloupnost tím, že se prochází tabulkou z pravého dolního rohu směrem k levému hornímu rohu!

==Весь подход сводится к поэтапному заполнению матрицы, где строки представляют собой элементы x, а колонки элементы y. При заполнении действуют два правила, вытекающие из уже сделанных наблюдений:==
- Если элемент $x_i$ равен $y_i$ то в ячейке (i, j) записывается значение ячейки (i - 1, j - 1) с добавлением единиц
- Если элемент $x_i$ не равен $y_j$ то в ячейку (i, j) записывается максимум из значений слева (i - 1, j) и сверху (i, j - 1)

**![](https://lh4.googleusercontent.com/3NQMDY96DvCnXxE2y2Ol6GW7yPd9EVGus9hBGulEo_X_VL3I2FvLiqPwE8MwJ68kgWpbevtUFyYjpQ9jE6TP3OhbJTVSN7RGcjnCRmkMG16LO3YbkzRdT2oATOCkK-3jQMvyXPyrFy-kEB9HsdTFTJc)**

**![](https://lh6.googleusercontent.com/g4Ash8zPhf-PSyh51wsQ-F64dYTZMiUC6xhJRtQNdIbmkU0BsoA4pSJJyRuhJ4cw6pnTYFddQ3xbMkEW46J1TVQX8AFbrxaSFN2ZKARG3ov29sxSYM0CUtrJ-28emHfbSR6YJJP2rtZO67utInV2qPw)**

---

### Optimální násobení matic

Uvažujme posloupnost matic $A_1, A_2, A_3 ..., A_n$ kde rozměry matice $A_i$ jsou dány rozměry jejích řádků a sloupců:

![[Pasted image 20230903233524.png | center | 500]]

Pro určení optimálního pořadí násobení matic můžeme definovat tabulku, kde polozka \[i]\[j] představuje minimální počet skalárních násobení potřebných k výpočtu součinu matic $A_i$ a $A_j$ (zpočátku jsou všechny hodnoty v tabulce nastaveny na nekonečno).
Základním případem pro tabulku je situace, kdy i = j, což znamená, že máme jedinou matici, a v tomto případě je \[i]\[j] nastaveno na 0, protože není nutné násobení.

Mat | $A_1$ | $A_2$ | $A_3$ | Vypocty!
:-: | :-: | :-: | :-: | ---
**$A_1$** | 0 | 60 | 70 | \[1]\[2] ($A_1$ × $A_2$) = \[1]\[1] + \[2]\[2] + (3 × 5 × 4) = 0 + 0 + 60 = 60
**$A_2$** | 0 | 0 | 40 | \[2]\[3] ($A_2$ × $A_3$) = \[2]\[2] + \[3]\[3] + (5 × 4 × 2) = 0 + 0 + 40 = 40
**$A_3$** | 0 | 0 | 0 | Coze 70?

Protože konečný výsledek lze získat dvěma různými způsoby, zaznamená se ten nejlepší (min)!
- \[1]\[3] (($A_1$ × $A_2$) × $A_3$) = \[1]\[2] + \[3]\[3] + (3 × 4 × 2) = 60 + 0 + 24 = 84
- \[1]\[3] ($A_1$ × ($A_2$ × $A_3$)) = \[1]\[1] + \[2]\[3] + (3 × 5 × 2) = 0 + 40 + 30 = 70

---

### Problém batohu

![[Pasted image 20230904090802.png]]

![[Pasted image 20230904090844.png]]

![[Screenshot 2023-09-04 at 9.08.57.png]]

---

## 7. Rozptylovací tabulky (Hashing), hashovací funkce, řešení kolizí, otevřené a zřetězené tabulky, double hashing, srůstající tabulky, univerzální hashování.

**Hašovací tabulka** je vyhledávací datová struktura, která asociuje hašovací klíče s odpovídajícími hodnotami. Hodnota klíče je spočtena z obsahu položky pomocí nějaké hašovací funkce.

**Hašovací funkce** je matematická funkce pro převod vstupních dat do (relativně) malého čísla.
- výstupem hašovací funkce se označuje **hash**
- funkce se používají k rychlejšímu prohledávání tabulky a porovnávání dat
- e.g. kryptografické hašovací funkce je používána pro vytváření a ověřování elektronického podpisu

**Kolize** obecně vznikají, protože potenciálních klíčů je typicky víc, než je velikost tabulky. Kolize je situace, kdy se záznamy s různými klíči hašují zvolenou funkcí na stejné místo.

Podle způsobu řešení kolizí se hašovací tabulky dělí na dva základní druhy:
- Řešení kolizí zřetězením
- Otevřená adresace

### Zřetězení záznamů

Každá položka tabulky obsahuje ukazatel na seznam prvků zahašovaných na stejné místo. Při hledání projdeme celý seznam prvků. Výhodou je, že faktor naplnění může být větší než 1.

**![](https://lh5.googleusercontent.com/j6UV-ffYVDpbvAF6e9fBIMWJzVR99N95LKDuDTW6G3TyNM9FUZGue7S64X55wghanzth6PgneUrH603BLtsH_vkBc1HSpV02nkczuWWOyRiK1voDzXDu0i52LX1bJXxOrSxMwDqxulGTSnzFrojIiRI)**

---

### Otevřená adresace

Tento druh tabulek je obvyklá implementace. Tabulka obsahuje všechny prvky ve svých položkách. tj. pokud je při vkládání pozice již obsazena, je nějakým algoritmem určeno náhradní místo, případně opakovaně, dokud se nepodaří vkládaný prvek umístit na volné místo.

(podle metody se tabulky naplňují nejvýše přibližně na 70–90 %, protože pak se doby operací výrazně prodlužují. Při naplnění je nutno přehašovat do nové tabulky)

#### Linear probing

Tato metoda zahrnuje použití speciálně definované hashovací funkce. 
1. Pozivame funkce pro vypocet pozici vkladani
2. Pozice je obsazena ? -> vloz data dal (i++)
	- Krok i je determicky (e.g. = 2) ? -> vloz o 2 pozice dal
3. Také hashovací funkci lze upravit - přidat ke kroku při kolizi koeficient (např. 3 v příkladu níže), pak bude nová pozice +3 od aktuální!

EX:

Hasovaci funkce je $h(k) = (k + i) mod 5$, mod 5 protoze 5 je delka pole
Resenim dat podle modulo ziskame pozici pro ukladani (e.g. 1%5 = 1)

Graficky | Vypocty
:-: | ---
![[Pasted image 20230904092639.png\|350]] | **1**%5 = 1<br>**5**%5 = 0<br>**21**%5 = 1 -> 1 je obsazeno -> vloz dal (i++) -> 2<br>**10**%5 = 0 -> 0, 1, 2 jsou obsazeny -> vloz do 3<br>**7**%5 = 2 -> 2, 3 jsou obsazeny -> vloz do 4 
![[Pasted image 20230904093506.png\|350]] | **1**%5 = 1<br>**5**%5 = 0<br>**21**%5 = 1 -> 1 je obsazeno -> vloz o 3 pos. dal -> 4<br>**10**%5 = 0 -> 0 je osazeno -> vloz o 3 pos. dal -> 3<br>**7**%5 = 2

Z výše uvedených příkladů je patrné, že při použití druhé metody bylo porovnávacích operací mnohem méně!

#### Double hashing

**Double hashing** se používá ve spojení s otevřeným adresováním v hashovacích tabulkách k řešení kolizí hashů, a to tak, že se v případě kolize použije sekundární hash klíče jako offset.

==Tato metoda předpokládá použití hashovací funkce k nalezení pozice a druhé hashovací funkce k vyřešení kolize!==

EX:

Hasovaci (double) funkce je $h(k) = [h_1(k) + i\cdot h_2(k)]mod n$
Defenujeme $h_1(k)$ a $h_2(k)$:
- $h_1(k) = k \% 11$
- $h_2(k) = k\% 10 + 1$

![[Pasted image 20230904094820.png]]

Vypocty:
- **1**%11 = 1, **1**%10 + 1 = 2, i = 0 (neni kolize) -> h(k) = 1
- **25**%11 = 3, **25**%10 + 1 = 6, i = 0 (neni kolize) -> h(k) = 3
- **23**%11 = 1, **23**%10 + 1 = 4, i = 1 (je kolize!) -> h(k) = 5

---

### Srůstající tabulky

V samostatné řetězové hashovací tabulce jsou položky, které se hashují na stejnou adresu, umístěny do seznam na této adrese. Tato technika může vést k velkému plýtvání pamětí, protože samotná tabulka musí být dostatečně velká.

#### LISCH

Princip: Prohledej řetězec začínající na místě i pokud nenajdeš k pridej ho do tabulky na první místo od konce tabulky a připoj ho do řetězce na **poslední** místo

![[Pasted image 20230904111800.png]]

#### EISCH

Princip: Prohledej řetězec začínající na místě i pokud nenajdeš k pridej ho do tabulky na první místo od konce tabulky a připoj ho do řetězce za **první** místo

![[Pasted image 20230904111845.png]]

---