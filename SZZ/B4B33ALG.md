---
updated: 2023-08-11T06:17
date: 2023-08-11T06:16
tags: writing/idea
parent: [[Ideas]]
---
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

## 3. Fronta, zásobník, průchod stromem/grafem do šířky/hloubky.


## 4. Binární vyhledávací stromy, AVL a B- stromy


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

#todo

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


### Heap sort

#todo

## 6. Dynamické programování, struktura optimálního řešení, odstranění rekurze. Nejdelší společná podposloupnost, optimální násobení matic, problém batohu.


## 7. Rozptylovací tabulky (Hashing), hashovací funkce, řešení kolizí, otevřené a zřetězené tabulky, double hashing, srůstající tabulky, univerzální hashování.