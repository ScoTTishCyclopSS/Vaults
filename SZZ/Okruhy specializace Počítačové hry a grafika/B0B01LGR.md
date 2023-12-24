## Obsah:
- [[#1. Výroková logika|Výroková logika]]
- [[#2. Predikátová logika|Predikátová logika]]
- [[#3. Základní pojmy a definice teorie grafů|Grafy a vtipné pojmy, které si nepamatuji]]

---

## 1. Výroková logika

### Syntax a sémantika výrokové logiky

Označení | Popis
--- | ---
A, B .. | množina atomických výroků 
α, β .. ∈ A | výroková formule
¬α |** unární spojka**, protože vytváří novou formuli z jedné existující formule
(α ∧ β), (α ∨ β), (α ⇒ β), <br>(α ⇔ β), (α ⊕ β), (α ↓ β) | **binární spojky**, protože vytvářejí novou formuli ze dvou formulí
a, b, c .. | množina atomických výroků

Spojky označují následující operace:
- Negace (¬, NOT)
- Konjunkce (∧, AND)
- Disjunkce (∨, OR)
- Implikace (⇒, IMPLIES)
- Ekvivalence (⇔, IFF)
- Vylučovací (⊕, XOR)
- Negaci konjunkce (↓, NAND)

**Pravdivostní ohodnocení** je zobrazení u:P(A) → {0, 1}, které splňuje pravidla, které lze rozpoznat z této **pravdivostní tabulky logických spojek**:

α|β|α ∧ β|α ∨ β|α ⇒ β|α ⇔ β|α ⊕ β|α ↓ β
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
0 | 0 | 0 | 0 | 1 | 1 | 0 | 1
0 | 1 | 0 | 1 | 1 | 0 | 1 | 1
1 | 0 | 0 | 1 | 0 | 0 | 1 | 1
1 | 1 | 1 | 1 | 1 | 1 | 0 | 0

%% Separator %%

α|¬α
:-: | :-:
1 | 0
0 | 1

Poznámka:
- (α ⇒ α) = (¬α ∨ β)
- (α ⇔ β) = (α ⇒ β) ∧ (β ⇒ α) = (¬α ∨ β) ∧ (¬β ∨ α)
- (α ⊕ β) = ¬(α ⇔ β) = (α ∧ ¬β) ∨ (¬α ∧ β)
- (α ↓ β) = ¬(α ∧ β)

**Syntaktický strom** formule slouží k vizualizaci způsobu, jak formule vzniká. Je to strom, kde každý vrchol, který není listem, je ohodnocen logickou spojkou. Binární spojky mají dva následníky a unární spojky mají pouze jednoho následníka. ^769f49

EX: (b ⇒ ¬c) ⇔ ((a ∧ c) ⇒ b)

![[Pasted image 20230817215823.png | center | 350]]

**Tautologie**: Formule je pravdivá ve všech ohodnoceních. 

**Splnitelná formule**: Formule je pravdivá alespoň v jednom ohodnocení.

**Kontradikce**: Formule je nepravdivá ve všech ohodnoceních.

**Jak zjistit, že je formule tautologie/kontradikce/splnitelná bez využití tabulky?**

1. u((α ∧ β) ⇒ α) je tautologie?
2. Protipříklad: u((α ∧ β) ⇒ α) = 0 (proto u(α ∧ β) = 1 a u(α) = 0)
3. Ale u(α ∧ β) = 1 jenom pokud u(α) = 1 a u(β) = 1
4. Tedy máme SPOR! 
5. Protože je to jediný způsob, jak dokázat nepravdivost, formule je tautologie!

==Nebo udělat [[#^769f49|syntaktický strom]] se stejným protipříkladem!==

---

### Důkazový systém - přirozená dedukce (I hate it so much)

**Přirozená dedukce** pracuje s formulemi, které obsahují základní sadu spojek kromě ekvivalence (místo <=> budeme dokazovat ⇒ 2-krat).

Poznámka: znak ┴ je sporem (e.g. ¬α ∧ α), \[..] je box

Pravidla dedukce:
1. Introduction rule (i - pravidlo) - zavádí spojku
2. Elimination rule (e - pravidlo) - odstraňuje spojku

Základní odvozovací pravidla:

Spojka | i - pravidlo | e - pravidlo
:-: | :-: | :-:
∧ | $\dfrac{α⠀⠀⠀β}{α⠀∧⠀β}$∧<sub>i</sub> | $\dfrac{α⠀∧⠀β}{α}$∧<sub>e1</sub>⠀⠀$\dfrac{α⠀∧⠀β}{β}$∧<sub>e2</sub>
=> | $\dfrac{[{α \atop β}]}{α⠀=>⠀β}$=><sub>i</sub> | $\dfrac{α⠀⠀α⠀=>⠀β}{β}$=><sub>e</sub>
∨ | $\dfrac{α}{α⠀∨⠀β}$∨<sub>i1</sub>⠀⠀$\dfrac{β}{α⠀∨⠀β}$∨<sub>i2</sub> | $\dfrac{α⠀⠀α⠀=>⠀β}{β}$=><sub>e</sub>
¬ | $\dfrac{[{α \atop ┴}]}{¬α}$¬<sub>i</sub> | $\dfrac{α⠀⠀¬α}{┴}$¬<sub>e</sub>⠀⠀$\dfrac{¬¬α}{α}$¬¬<sub>e</sub>
┴ | none | $\dfrac{┴}{α}$┴<sub>e</sub> (==za spor lze odvodit cokoliv==)

Formule α je **logickým důsledkem** formule β (**β ⊢ α, S ⊢ α**), pokud lze z formule β odvodit formuli α pomocí pravidel logického odvozování.

EX:
Dukazat: ¬α ∨ β ⊢ α => β

Kroky | Graficky
--- | :-:
<ol><li>globální pp</li><li>pomocní pp</li><li>pomocní pp</li><li>¬e (spor) má 2. a 3.</li><li>┴e má 4.</li><li>=><sub>i</sub> má box 3-5</li><li>pomocní pp</li><li>pomocní pp</li><li>kopie 7., proto platí</li><li>=><sub>i</sub> má box 8-9, ∨<sub>e</sub> má vse vyse</li><li>done</li></ol> | ![[Pasted image 20230822181415.png\|200]]

---

### Významová (sémantická) ekvivalence formulí výrokové logiky

Formule **α** a **β** jsou **tautologicky (sémanticky)** ekvivalentní, jestliže pro každé ohodnocení u platí **u(α) = u(β)**.

Pro každé formule α, β a γ platí:
-  α = α
- ¬¬α = α
- **de Morganova pravidla** (negace všech spojek a formulí):
	- ¬(α ∧ β) = (¬α ∨ ¬β)
	- ¬(α ∨ β) = (¬α ∧ ¬β)
- **distributivní zákony** (správné otevírání závorek v konjunkci / disjunkci):
	- α ∧ (β ∨ γ) = (α ∧ β) ∨ (α ∧ γ)
	- α ∨ (β ∧ γ) = (α ∨ β) ∧ (α ∨ γ)
- (α ⇒ β) = (¬α ∨ β)

---

### Normální formy formulí

**Literál** je logická proměnná nebo negace logické proměnné.

**Minterm** je literál nebo konjunkce konečně mnoha literálů.
- (α ∧ ¬β ∧ γ) / (α ∧ ¬β) / α / tt

**Maxterm (klausule)** je literál nebo disjunkce konečně mnoha literálů.
- (α ∨ ¬β ∨ γ) / (α ∨ ¬β) / α / ff

#### DNF (Disjunktivní Normální Tvar)

Formule je v DNF, jestliže je složena z mintermů nebo disjunkcí konečně mnoha mintermů.

- α = (a ∧ ¬b ∧ c)  ∨ (a ∧ b)
- β = (a) ∨ (b)

##### Nalezení DNF je přes 1

1. Máme: (a ⇒ b)  = (¬a ∨ b)
2. Vytvořme si tabulku:

a | b | a ⇒ b
--- | --- | ---
0 | 0 | ==1==
0 | 1 | ==1==
1 | 0 | 0
1 | 1 | ==1==

3. Vytvořme původní formule z mintermů (odvozené z tabulky):
   α = (¬a ∧ ¬b) ∨ (¬a ∧ b) ∨ (a ∧ b)

#### CNF (Konjunktivní normální tvar)

Formule je v CNF, jestliže je složena z maxtermů nebo konjunkcí konečně mnoha maxtermů.

- α = (a ∨ ¬b ∨ c)  ∧ (a)
- β = (a ∨ b)

##### Nalezení CNF je přes 0

1. Máme: (a ⇒ b)  = (¬a ∨ b)
2. Vytvořme si tabulku:

a | b | a ⇒ b
--- | --- | ---
0 | 0 | 1
0 | 1 | 1
1 | 0 | ==0==
1 | 1 | 1

3. Vytvořme původní formule z maxtermů (odvozené z tabulky):
   α = (a) ∧ (¬b)

#### Převod formule do CNF

Často se vyskytuje úloha, kdy je třeba vzorec převést do CNF bez účasti tabulky (která samozřejmě velmi pomáhá), v takovém případě je třeba vzorec postupně rozšířit do podoby konjunkcí konečně mnoha maxtermů.

EX: Formule není v CNF, takže se neskládá z maxtermů!
1. α = ((¬a ∨ b) ∧ c)  ==∨== (a ∧ d) - Nejsnazší je začít s odhadem disjunkcí podle distributivního zákonu.
2. α = ((¬a ∨ b) ==∨== (a ∧ d))  ∧ (c ==∨== (a ∧ d) - Znovu distributivní zákony!
3. α = ((¬a ∨ b ∨ a) ∧ (¬a ∨ b ∨ d)) ∧ ((c ∨ a) ∧ (c ∨ d)) - Nakonec lze otevřít vnější závorky *(poznámka: (¬a ∨ b ∨ a) → (tt ∨ b) → tt)*
4. α = (¬a ∨ b ∨ d) ∧ (c ∨ a) ∧ (c ∨ d) - Formule je v CNF!

#### Rezoluční metoda

**Klausalni tvář kl(S)** - je množina všech klausuli vznyklych aplikaci převedení každé formuli z S do CNF.

==S má jenom tautologie? -> kl(S) = ∅==

Rezoluční metoda testuje, zda je množina klausuli splnitelná (S |= γ <=> S ∪ {¬γ} je nesplnitelná)!
1. vyrábí resolventy z dvojic klausuli (sym. důsledky dvojic)
2. pokud vzniká ff resolventa => kl(S) je nesplnitelná

Ale co to je **resolventa**? Resolventa α, β je klausule, která obsahuje všechny literály z α, β (pouze jednou) kromě dvojic x a ¬x (značíme **res<sub>x</sub>(α, β)**)

EX1:
res<sub>x</sub>(x ∨ y ∨ z, ¬x ∨ y ∨ w) = (y ∨ z ∨ w)

Klausule v RM značíme v tabulce, a řádek za řádkem řešíme resolventy!

EX2:

*Máme:* S = { a => b, b => c },  γ = (a ∨ b) => c
*Chceme:* S |= γ <=> S ∪ {¬γ} je nesplnitelná

1. do CNF
	- S1: a => b |=| ¬a ∨ b, CNF (1 kl.)
	- S2: b => c |=| ¬b ∨ c, CNF (1 kl.)
	- γ: (a ∨ b) => c |=| ¬(a ∨ b) ∨ c
	  ¬γ = (a ∨ b) ∧ ¬c, CNF (2 kl.)
2. tabulka

. | ¬a ∨ b | ¬b ∨ c | a ∨ b | ¬c | | | res
:-- | :-:  | :-:    | :-: | :-: | :-: | :-: | :-:
a   |  0   |        |  1  |     |**b**|     | res<sub>a</sub>(¬a ∨ b, a ∨ b) = **b**
b   | ▒▒▒▒ |  0     | ▒▒▒ |     |  1  |**c**| res<sub>b</sub>(¬b ∨ c, b) = **c**
c   | ▒▒▒▒ | ▒▒▒▒   | ▒▒▒ |  0  | ▒ |  1  | res<sub>c</sub>(¬c, c) = **ff**<br>formule je NEsplnitelná!

EX3:
Co když je splnitelná?

.   | ¬a ∨ b | ¬a ∨ f | ¬a ∨ ¬b ∨ c | ¬c ∨ f  | a     |     |     |      |     |    
:-- | :-:    | :-:    | :-:         | :-:     |:-:    | :-: | :-: | :-:  | :-: | :-:
a   |   0    |  0     |  0          |         |   1   |**b**|**f**|**¬b ∨ c**| 
b   |  ▒▒▒▒  |  ▒▒▒▒  |   ▒▒▒▒▒▒    |         |   ▒   |  1  |     |  0       |**c**|
c   |  ▒▒▒▒  |  ▒▒▒▒  |   ▒▒▒▒▒▒    |    0    |   ▒   |  ▒  |     |   ▒▒▒▒   |  1  |**f**
f   |  ▒▒▒▒  |  ▒▒▒▒  |   ▒▒▒▒▒▒    |   ▒▒▒▒  |   ▒   |  ▒  |  1  |   ▒▒▒▒   |  ▒  |  1

- res<sub>a<sub>1</sub></sub> = (¬a ∨ b, a) = b
- res<sub>a<sub>2</sub></sub> = (¬a ∨ f, a) = f
- res<sub>a<sub>3</sub></sub> = (¬a ∨ ¬b ∨ c, a) = (¬b ∨ c)
- res<sub>b</sub> = (b, ¬b ∨ c) = c
- res<sub>c</sub> = (¬c ∨ f, c) = f
- res<sub>f</sub> = (f, f) = f

Nedostali jsme ff => S je splnitelná!

---

###  Důsledek ve výrokové logice

Formule γ je **sémantickým důsledkem (konsekventem)** 
mn. S (**S ⊨ γ**), pokud v každém ohodnocení jsou pravdivé vsechni formule z S, je také pravdivá γ.

EX1:
Je mn. {a; a ⇒ b} sym. dus. b? ({a; a ⇒ b} ⊨ b?)
Ano, protože při všech splňujících ohodnoceních S je splněno γ

a | a ⇒ b | b | 
--- | --- | --- | ---
1 | 1 | 1 | ok

EX2:
Je mn. {b; a ⇒ b} sym. dus. a? ({b; a ⇒ b} ⊨ a?)
Nope, protože při všech splňujících ohodnoceních S není splněno γ

b | a ⇒ b | a | 
--- | --- | --- | ---
1 | 1 | 1 | ok
1 | 1 | 0 | oops

**Splnitelná množina** - ex. alespoň jedno ohodnocení **u**, ve kterém jsou pravdivé <br>všechny formulí **u(formule) = 1**.

EX:

Máme: 
- α = a ⇒ (b ∧ c) 
- β = b ⇒ (¬a ∨ ¬c)

Potřebujeme: u(α) = u(β) = 1

Řešení: množina je splnitelná (==nebo můžeme nakreslit syntaktický strom==)!

a | b | c | α | β
-- | -- | -- | -- | --
0 | 0 | 1 | 1 | 1 

**Nesplnitelná množina** - neex. žádné ohodnocení **u**, ve kterém jsou pravdivé všechny<br>formulí.

Množina formulí S je **pravdivá** v ohodnocení u, pokud každá formule z S je pravdivá v u (ϕ ∈ S platí u(ϕ) = 1).

Množina formulí S je **nepravdivá** v ohodnocení u, pokud existuje alespoň jedna formule, která je nepravdivá v u ( = 0).

---

### Úplné systémy logických spojek (USS(R?!))

Množina logických spojek ∆ tvoří **úplný systém logických spojek**, jestliže pro každou formuli **α** existuje formule **β**, která je s ní tautologicky ekvivalentní a používá pouze spojky z množiny ∆.

(==pomocí spojek z množiny ∆ je možné vyjádřit libovolnou logickou formuli α tak, aby tato nová formule β byla s α tautologicky ekvivalentní==)

EX:
- 100% USS: {¬, ∧} / {¬, ∨} / {¬, ⇒} 
- Je {¬, ⇒} USS? - Ano, vždyť pomocí **⇒** a **¬** můžeme ekvivalentně zapsat **∨**!
  (¬α ⇒ β) = (¬(¬α) ∨ β) = (α ∨ β)
- Je {¬, ⇔} USS? - Nope, vždyť pomocí **⇔** a **¬** nemůžeme ekvivalentně zapsat **∨** (nebo **∧**, nebo **⇒**)!

#todo Boolevsky kalkul?..

---

## 2. Predikátová logika

### Syntax predikátové logiky

**Logické symboly**:
- **proměnné**: x, y, z...
- **logické spojky**: ¬, ∧, ∨, ⇒, ⇔
- **kvantifikátory**: ∃ - existuje, ∀ - pro vše, = - rovnost

**Speciálně symboly**:
- **predikátové** - Množina predikátových symbolů (není prázdná) P(x), Q(x, y), R(z)...
- **funkční** - Množina funkčních symbolů (může být prázdná) f(x), g(y), h(z)...
- **konstantní** - Množina konstantních symbolů (může být prázdná) a, b, c...

**Arita** je přirozené číslo, které udává, kolika objektů se daný predikát nebo funkční symbol týká, nebo kolik proměnných má funkční symbol.

**Term** - může být buď proměnná nebo je zkonstruován z jiných termů použitím funkčních symbolů (popisují objekty, včetně toho jak vznikly).
1. Každá proměnná a každý konstantní symbol je term
2. Pokud je f funkčním symbolem s aritou n a t1, t2, ..., tn jsou termy, pak f(t1, t2, ..., tn) je také term
3. Nic, co nevzniklo konečným použitím pravidel 1 a 2, není term

**Atomické formule** je řetězec P(t1, t2, ..., tn), kde P je predikátový symbol s kladnou aritou n a t1, t2, ..., tn jsou n-tice termů.

EX:
"Každé liché je menší než nějaké sudé číslo."
- Termy: x → liché, y → sudé
- Atom: L(x) , S(y), "je menší než" → P(L(x), S(y))

"Každé číslo je menší než jeho následník."
- Termy: x → číslo, f(x) → následník
- Atom: "je menší než" → P(x, f(x))

**Syntaktický strom formule** je podobný syntaktickému stromu výrokových formulí. Rozdíl je v tom, že kvantifikátory jsou považovány za unární a také vytváříme ho pro termy. Listy syntaktického stromu jsou vždy ohodnoceny proměnnou, konstantním symbolem, predikátovým symbolem arity 0 nebo F.

**Vázaný výskyt** - v syntax. stromů při postupu od listu ohodnoceného tímto X ve směru ke kořeni narazíme na kvantifikátor s touto proměnou.

**Volný výskyt** - výskyt proměnné ve formuli, který není vázán žádným kvantifikátorem.

EX: ∀xP(x, f(x)) ∨ S(x)

![[Pasted image 20230817214817.png | center | 350]]

Legální **přejmenování** vázaných proměn v podformuli ∀x / ∃x je pokud platí:  
- přejmenovány všechny výskyty x (x → y)  
- žádná volná proměna nestala vázanou!

==Dvě formule jsou považovány za stejné, jestliže se liší pouze legálním přejmenováním vázaných proměnných==

EX: 
- α = ∀xP(x, f(z)) ∨ S(x) - všechny proměnné mají volný výskyt
- α = ∀yP(y, f(z)) ∨ S(x) -  (x → y): všechny proměnné mají volný výskyt
- α = ∀zP(z, ==f(z)==) ∨ S(x) - proměnná z má vázaný výskyt!

**Uzavřená formule (Sentence)** - formule, která má pouze vázané výskyty proměnných.

**Otevřená formule** - formule, která má pouze volné výskyty proměnných.

---

### Sémantika predikátové logiky

**Universum interpretace** - mn. U, je „svět”, jehož prvky popisují termy 
a o vlastnostech těchto prvků mluví formule (==může být prázdná pouze pokud 
interpretace neobsahuje konstanty!==).

**Přiřazení \[[−]]** - je funkce, která provádí přiřazení interpretací predikátovým a funkčním symbolům.

- Každému predikátu P s aritou n přiřazuje \[[P]]
  \[[P]] obsahuje všechny n-tice objektů, které mají "vlastnost" P
- Každému funkčnímu symbolu f s aritou n přiřazuje \[[f]]
   \[[f]] přijímá n-tici objektů a vytváří z nich další objekt (\[[f]]: U<sup>n</sup> → U)

**Kontext proměnných** - zobrazení **ρ**, které každé proměnné **x ∈ Var** přiřazuje prvek **ρ(x) ∈ U**, kde U je univerzum.

**Aktualizace (update) kontextu** - pro daný kontext proměnných ρ, proměnnou x ∈ Var a prvek d ∈ U označuje **ρ\[x := d]** kontext proměnných, který má stejné hodnoty jako ρ, s výjimkou proměnné x, která je přiřazena hodnotou d.

EX1: 
1. Term:  f(t1, . . . , tn)
2. Kontext:  \[[ f(t1, . . . , tn) ]]ρ = \[[f]]( \[[t1]]ρ, . . . , \[[tn]]ρ )
3. hodnota termu f(t1, . . . , tn) se získala aplikací funkce \[[f]] na n-tici prvků!

EX2:
1. \[[ ϕ  o  ψ ]] = \[[o]] ( \[[ ϕ ]]ρ, \[[ ψ ]]ρ )
2. \[[o]] je jakákoliv funkce arity 2
3. Kontext: \[[ ϕ ]]ρ = 2, \[[ ψ ]]ρ = 3, \[[o]] je násobení
4. \[[o]] ( \[[ ϕ ]]ρ, \[[ ψ ]]ρ ) => 2 * 3

**Model sentence** ϕ - Interpretace {U, \[[−]]}, ve které je sentence ϕ pravdivá!
- Sentence ϕ se nazývá **tautologie**, jestliže je pravdivá v každé interpretaci.
- Sentence ϕ se nazývá **kontradikce**, jestliže je nepravdivá v každé interpretaci.
- Sentence ϕ se nazývá **splnitelná**, jestliže je pravdivá v alespoň jedné interpretaci.

Je také možné vytvořit množinu sentence, stejné vlastnosti fungují i pro ni:
- **Model množiny sentencí** - Množina sentencí M je **splnitelná**, jestliže existuje alespoň  jedna interpretace  {U, \[[−]]}, ve které jsou všechny sentencí z M pravdivé (==prázdná množina sentencí je splnitelná==).
- Množina sentencí M je **nesplnitelná**, jestliže pro každou interpretaci {U, \[[−]]} existuje formule z M, která je v {U, \[[−]]} nepravdivá, tj. nemá model.

---

### Významová (sémantická) ekvivalence formulí predikátové logiky

**Tautologická ekvivalence** - dvě sentence ϕ a ψ jsou tautologicky ekvivalentní, jestliže mají stejné modely, tj. jsou pravdivé ve stejných interpretacích.

EX:
ϕ |=| ψ právě tehdy, když ϕ ⇔ ψ je tautologie, např. ¬(∀x P(x)) |=| (∃x ¬P(x)) 

---

### Normální formy formulí

#todo ???

---

### Důsledek v predikátové logice

**Sémantický důsledek (Konsekvent)** - Jestliže každý model množiny sentencí S je také modelem sentence ϕ (**S |= ϕ**).
(==sentence ϕ není konsekventem množiny sentencí S, jestliže existuje model množiny S, který není modelem sentence ϕ==)

==S |= ϕ právě tehdy, když S ∪ {¬ϕ} je nesplnitelná množina.==

EX1:
∀x∃yM(x, y) |= ∃y∀xM(x, y)
1. platí že pro každé číslo existuje číslo větší (Sentence 1: ∀x∃yM(x, y))
2. neplatí, že existuje nějaké číslo větší než všechny ostatní (Sentence2: ∃y∀xM(x, y))
3. => existuje model množiny, ve kterém první sentence platí, ale druhá sentence neplatí, proto není sémantickým důsledkem!

---

## 3. Základní pojmy a definice teorie grafů

### Základní pojmy

Před analýzou typů grafů je třeba zavést základní pojmy: **G = (V, E, ε)**
- **G** - graf
- **V** - neprázdná konečná množina vrcholů (nebo uzlů)
- **E** - konečná množina hran (**orientované** / **neorientované**)
- **ε** - **vztah incidence** je přiřazení, které každé hraně přiřazuje množinu {u, v} (počáteční vrchol - koncový vrchol)
	- u = v? => hrana je **orientovaná smyčka**
	- ε(e1) = ε(e2) ? => hrany jsou **paralelní**

Na základě typů hran můžeme odhadnout, že graf má 2 typy: **orientovaný** a **neorientovaný**.
- **Orientovaný graf** se liší tím, že graficky vyznačuje směr hrany z jednoho vrcholu do jiného vrcholu
- Také hrany v **orientovaném grafu** mohou být vyhodnoceny 

#### Stupeň d(v)
Popis | Graficky
--- | :-:
**Stupeň d(v)** je počet hran, které do daného vrcholu zasahují! | ![[Pasted image 20230820152934.png\|120]]

- u orientovaného grafu rozdělujeme podle orientace hrany - **d<sub>in</sub>** a **d<sub>out</sub>**
- žádná hrana nevstupuje do zdrojového vrcholu: d<sub>in</sub>(v) = 0
- žádná hrana nevystupuje z výlevkového vrcholu: d<sub>out</sub>(v) = 0

#### Podgraf
Popis | Graficky
--- | :-:
Graf GI = (VI, EI) je **podgrafem** grafu G = (V, E), pokud VI ⊆ V a EI ⊆ E. | ![[Pasted image 20230820153227.png]]
**Faktor** grafu G je podgraf grafu G, který obsahuje všechny vrcholy grafu G, ale ne nutně všechny jeho hrany. | ![[Pasted image 20230820153655.png\|250]]

#### Sled
Popis | Graficky
--- | :-:
**Sled** délky *k* je posloupnost vrcholů a hran *v<sub>0</sub>, e<sub>1</sub>, v<sub>1</sub>, e<sub>2</sub> ... v<sub>k-1</sub>, v<sub>k</sub>, v<sub>0</sub>* taková, že hrana e<sub>i</sub> je incidentní s vrcholy v<sub>i-1</sub> a v<sub>i</sub>. | ![[Pasted image 20230820222857.png\|250]]
**Triviální sled** - Jediný vrchol a žádná hrana. | ![[Pasted image 20230820195051.png\|250]]
**Uzavřený sled** - Sled, kde *v<sub>0</sub>=v<sub>k</sub>*. | ![[Pasted image 20230820223300.png\|250]]

Poznámka: Vyberme si nějaký bod a vydáme se na výlet po hranách, postupně si zapisujme navštívené vrcholy a hrany, výsledná posloupnost je sled. ==Opakování vrcholů nebo hran nás nezajímá!==

#### Tah
Popis | Graficky
--- | :-:
**Tah** je sled, kde se neopakují hrany. | ![[Pasted image 20230820223554.png\|250]]
**Uzavřený tah** je uzavřený sled, ve kterém se neopakují hrany. | ![[Pasted image 20230820223846.png\|250]]
**Eulerovský tah** v neorientovaném grafu G je tah, který obsahuje všechny hrany a všechny vrcholy grafu G. | ![[Pasted image 20230820211158.png\|250]]
**Uzavřený eulerovský tah** v neorientovaném grafu G je uzavřený tah, který obsahuje všechny hrany a všechny vrcholy grafu G. | ![[Pasted image 20230820224149.png\|250]]


Poznámka: ==Pokud v grafu existuje uzavřený eulerovský tah, nazýváme ho **eulerovský graf**!==

#### Cesta

Popis | Graficky
--- | :-:
**Cesta** je tah, ve kterém se neopakují vrcholy (s výjimkou *v<sub>0</sub>=v<sub>k</sub>*). | ![[Pasted image 20230820221408.png]] 
**Kružnice** nazýváme uzavřenou orientovanou cestu. Tedy cestu, která začíná a končí ve stejném vrcholu. | ![[Pasted image 20230820221418.png]] 

#### Prostý graf

Popis | Graficky
--- | :-:
Graf (orientovaný nebo ne), který neobsahuje žádné paralelní hrany (avšak může obsahovat smyčky)! | ![[Pasted image 20230820142757.png]]

Poznámka: ∑d(v) = 2|E| => ==Každý graf má sudý počet vrcholů lichého stupně!==

#### Úplný graf (Complete graph)
Popis | Graficky
--- | :-:
Graf, kde je každý vrchol s každým jiným propojen hranou! | ![[Pasted image 20230820142711.png]]

Poznámka: K<sub>n</sub> = n(n-1)/2 => ==V uplnem grafu K<sub>n</sub> je počet hran (n je počet vrcholů)!==

#### Diskrétní graf (Null graph)
Popis | Graficky
--- | :-:
Speciální typ grafu, který se skládá z konečné množiny vrcholů a žádných hran mezi nimi! | ![[Pasted image 20230820142455.png\|100]]

#### Bipartitní graf (Bipartite graph)
Popis | Graficky
--- | :-:
Graf, ve kterém můžeme rozdělit množinu vrcholů na 2 podmnožiny, kde žádné vrcholy jedné množiny nejsou spojeny hranou. | ![[Pasted image 20230820143311.png\|120]]

##### Úplný bipartitní graf (Complete bipartite graph)
Popis | Graficky
--- | :-:
Bipartitní graf, který obsahuje všechny možné hrany. | ![[Pasted image 20230820143523.png\|130]]

Poznámka: K<sub>m,n</sub> = m × n => ==V uplnem bipartitním grafu K<sub>m,n</sub> je počet hran (m, n jsou počty vrcholů v podmnožinach)!==

#### Regulární graf (Regular graph)
Popis | Graficky
--- | :-:
Graf, kde každý vrchol má stejný **stupeň**! | ![[Pasted image 20230820145039.png]]

#### Acyklický graf (Directed acyclic graph, DAG)
Popis | Graficky
--- | :-:
Graf, ve kterém neexistuje žádný orientovaný **cyklus** (nedá se projít z vrcholu zpět do něj samého po orientovaných hranách)! | ![[Pasted image 20230820145619.png\|200]] 

#### Isomorfní graf (Graph isomorphism)
Popis | Graficky
--- | :-:
Grafy jsou izomorfní, pokud mají stejný počet vrcholů a „stejná spojení“. | ![[Pasted image 20230820151833.png\|250]]

Poznámka: ==když mají dva grafy jiné **skóre** jsou nutně neizomorfní (tedy jiné), když mají stejné skóre můžou i nemusí být isomorfní!==

#### Souvislý graf (Connected graph)
Popis | Graficky
--- | :-:
Graf, kde mezi jakoukoliv kombinací dvou jeho vrcholů existuje **cesta**! | ![[Pasted image 20230820152231.png]] 

##### Silně souvislý graf
Popis | Graficky
--- | :-:
Graf je **silně souvislý**, jestliže mezi každou dvojicí vrcholů u a v existuje orientovaná cesta z *u* do *v* a orientovaná cesta z *v* do *u*. | ![[Pasted image 20230821132304.png]] 

##### Komponenta souvislosti
Popis | Graficky
--- | :-:
**Komponenta souvislosti** je maximální množina vrcholů M, pro kterou platí, že indukovaný podgraf určený M je souvislý! | ![[Pasted image 20230821133401.png]]

##### Silně souvislé komponenty souvislosti
Popis | Graficky
--- | :-:
**Silně souvislá komponenta souvislosti** je maximální množina vrcholů A, pro kterou platí, že z každého vrcholu v množině A je možné dosáhnout každého jiného vrcholu v této množině pomocí orientované cesty. | ![[Pasted image 20230821133131.png\|200]] 

---

### Stromy a jejich vlastnosti
Popis | Graficky
--- | :-:
**Strom** je souvislý graf neobsahující žádnou kružnici. <ul><li>má vždy alespoň 2 vrcholy</li><li>má vždy alespoň 2 vrcholy stupně 1</li><li>má vždy n-1 hran, kde n je počet vrcholů</li></ul> | ![[Pasted image 20230821133735.png]]

**Kořen stromu** je vrchol grafu G, ze kterého vede orientovaná cesta do všech ostatních vrcholů grafu G.

- Pokud existuje hrana (u, v) v grafu G, říkáme, že vrchol u je **předchůdce** vrcholu v a vrchol v je **následník** vrcholu u.
- Vrchol, který nemá následníka, se nazývá **listem**

#### Kostra grafu
Popis | Graficky
--- | :-:
Faktor grafu G, který je stromem, je **kostra**. <br>(==kostra neobsahuje žádné kružnice!==) | ![[Pasted image 20230821135019.png]]

Poznámka: ==Může existovat více než jedna kostra==, jak ukazuje obrázek výše. Hlavní je, že získaný podgraf musí splňovat vlastnosti stromu (ke každému vrcholu lze najít cestu!).

---

### Minimální kostry a algoritmy na jejich hledání
Popis | Graficky
--- | :-:
**Minimální kostra** ohodnoceného grafu G je taková kostra, která má nejmenší součet cen hran ze všech možných koster grafu G. | ![[Pasted image 20230821135357.png]]

#### Kruskalův algoritmus

Je algoritmus pro hledání minimální kostry v ohodnoceném grafu!

1. Začněte s prázdnou množinou, která představuje minimální kostru.
2. Seřaďte hrany grafu podle jejich ohodnocení (vzestupně nebo sestupně)
3. Pokud přidání hrany z množiny do minimální kostry nevytváří kružnice, přidejte ji do kostry, jinak skip
4. Pokračujte, dokud nejsou projety všechny hrany

EX:

Krok | Graficky
--- | :-:
Prázdná množina: **{ }** <br> Hrany grafu: **1, 1, 2, 3, 4** | ![[Pasted image 20230821135810.png\|150]]
Minimální cena v množině je 1, přidáme ji<br>Množina: **{ 1 }** | ![[Pasted image 20230821135901.png\|150]]
Opět stejná situace, určitě žádná kružnice<br>Množina: **{ 1, 1 }** | ![[Pasted image 20230821140134.png\|150]]
Nakonec přidáme 2. Výsledný podgraf splňuje vlastnosti stromu a je již minimální kostrou, ale zkontrolujme i ostatní hrany.<br><ul><li>Přidáním 3. vznikne kružnice (trojúhelník)</li><li>Přidáním 4. vznikne také kružnice o 4 hranách</li></ul>Množina: **{ 1, 1, 2 }** | ![[Pasted image 20230821140256.png\|170]] 

#### Jarníkův-Primův algoritmus

Tento algoritmus používá iterační metodu tvorby kostry a funguje následovně:

1. Vybereme libovolný vrchol v. Položíme K=0, S={v}
2. Vybereme nejlevnější hranu e, která spojuje některý vrchol x z množiny S s vrcholem y, který v S neleží. 
3. Vrchol y přidáme do množiny S a hranu e přidáme do K.
4. Opakujeme kroky 2-3 dokud nejsou všechny vrcholy v množině S.

EX:

Krok | Graficky
--- | :-:
S = **{ D }**, Sousedé: **A, B, E, F**<br>Levný soused: **A(5)** -> K = **5** | ![[Pasted image 20230821142216.png\|200]]
S = **{ D, A }**, Sousedé: **B, E, F**<br>Levný soused: **F(6)** -> K = **5, 6** | ![[Pasted image 20230821142505.png\|200]]
S = **{ D, A, F }**, Sousedé: **B, E, G**<br>Levný soused: **B(7)** -> K = **5, 6, 7**| ![[Pasted image 20230821142559.png\|200]]
S = **{ D, A, F, B }**, Sousedé: **C, E, G**<br>Levný soused: **E(7)** -> K = **5, 6, 7, 7**| ![[Pasted image 20230821142757.png\|200]]
S = **{ D, A, F, B, E }**, Sousedé: **C, G**<br>Levný soused: **C(5)** -> K = **5, 6, 7, 7, 5**| ![[Pasted image 20230821142811.png\|200]]
S = **{ D, A, F, B, E, C }**, Sousedé: **G**<br>Levný soused: **G(9)** -> K = **5, 6, 7, 7, 5, 9**| ![[Pasted image 20230821143008.png\|200]]

---

### Komponenty silné souvislosti a algoritmus na jejich hledání

Co je [[#Silně souvislé komponenty souvislosti]], jsme již uvedli výše!
Podívejme se nyní na algoritmus, který je pomáhá odhalit v grafu!

#### Kosarajův algoritmus (Kosaraju's algorithm)

Tento algoritmus funguje na principu DFS. Dále musíme navštívit všechny vrcholy a zapamatovat si čas jejich otevření. Při ukončení DFS postupně uzavírat a zapsat do záznamu, ze kterého pak vytvoříme komponenty souvislosti.

EX:

Krok | Graficky
--- | :-:
Začněme s vrcholem **F** a otevřeme jej v *0*. Následný DFS nás dovede k **H**, který otevřeme v *1* a ihned uzavřeme v *2*, protože odtud nic nevede. <br>S **F** však ještě nejsme hotovi, takže přejdeme k otevření (a okamžitému uzavření) **G** a nakonec i **F**. <br> S vrcholem **I** je to snadné, takže ho hned navštívíme.<br>Výsledná posloupnost: **{ H, G, F, I }**| ![[Pasted image 20230821185016.png\|300]] 
Nyní začneme s C, zde bude cesta vedena ze všech zbývajících vrcholů vrcholu. Zapíšeme si časy jejich návštěv a podíváme se na výslednou posloupnost: **{ H, G, F, I, A, E, D, B, C }** | ![[Pasted image 20230821184951.png\|300]]
Nyní použijeme naši posloupnost v opačném pořadí, abychom získali komponenty z tohoto opačného grafu. | ![[Pasted image 20230821185520.png\|300]] 
Začněme s **C**, vytvořme naši první komponentu (deláme tah) a poté ze posloupnosti odstraňme vše, co jsme v této komponentě použili: **{ H, G, F, I, ~~A, E, D, B, C~~ }** | ![[Pasted image 20230821185754.png\|300]] 
Totéž děláme pro **I** (**{ H, G, F, ~~I~~, ~~A, E, D, B, C~~ }**) <br>Pak pro **F** (**{ H, ~~G, F~~, ~~I~~, ~~A, E, D, B, C~~ }**) <br>A nakonec **H** (**{ ~~H~~, ~~G, F~~, ~~I~~, ~~A, E, D, B, C~~ }**), hotovo. | ![[Pasted image 20230821185952.png\|300]]

#### Tarjanův algoritmus (Tarjan's algorithm) 
Grafový algoritmus sloužící k vyhledávání silných komponent orientovaného grafu. Tarjanův algoritmus vychází z prohledávání do hloubky. Vrcholy se při prohledávání indexují dle pořadí svého nalezení. Při návratu z rekurze se každému vrcholu přiřadí uzel s nejnižším indexem na jaký lze dosáhnout. Podrobně (možná až příliš) si můžete přečíst [zde](https://kix.fsv.cvut.cz/~demel/grafy/err72.pdf)!

EX:

Krok | Graficky
--- | :-:
zjišťujeme, že vrchol **1** není v žádné komponentě (sam o sobe je 1. komponenta silné souvislosti, přidělena pořadová čísla **1/1**), hledání do hloubky proto zahájíme ve vrcholu **1** | ![[Pasted image 20230821151706.png\|230]] 
postup do hloubky ve směru vrcholu **5**:  -> přidělena pořadová čísla **6/5, 4/5, 5/5** (2. komponenta silné souvislosti) | ![[Pasted image 20230821152059.png\|230]] 
hledání do hloubky proto zahájíme ve vrcholu **2** (nemá smysl dělat BFS k 1. vrcholu, už jsme ho považovali za samostatnou komponentu) | ![[Pasted image 20230821152622.png\|230]]  
postup do hloubky ve směru vrcholu **7** -> přidělena pořadová čísla **7/2, 2/2** (3. komponenta silné souvislosti) | ![[Pasted image 20230821152328.png\|230]]

---

### Schopnost modelování praktických problémů s využitím grafů

#### Hledání uzavřeného orientovaného (nebo ne) eulerovského tahu

Co je [[#Tah|eulerovský tah]], jsme již uvedli výše!

**Eulerovský graf** je graf ve kterém existuje uzavřený Eulerovský tah takový graf má buď všechny vrcholy sudého stupně -> potom má uzavřený eulerovský tah, nebo má právě dva liché vrcholy *u,v* -> potom má eulerovský tah, kde tah začíná ve vrcholu *u* a končí ve vrcholu *v*.

1. Vstup: Souvislý graf G, vrcholy sudého stupně
   ( nebo souvislý orientovaný graf G = (V, E), kde pro vrcholy platí din(v) = dout(v) )
2. Vybereme libovolný vrchol, z toho vrcholu náhodný (orientovaný) tah T pomocí dosud nepoužitých hran, dokud to jde
3. T obsahuje všechny hrany?
	- Ano -> skončíme
	- Ne -> v T ex. vrchol v němž začíná ještě nepoužitá hrana <br> (krok 2 z tohoto vrcholu)
4. Vystup: T je (orientovaný) uzavřený eulerovský tah!

EX:

Typ | Graficky
--- | :-:
Princip je velmi podobný jako u BFS, začneme ve vrcholu **A** a jdeme, řekněme, směrem k **B**.<br>Tah bude: **{ A - B - E - D - B - C - A }**<br>Jiný tah bude: **{ B -> E -> D -> B -> C -> A -> B }**  | ![[Pasted image 20230821180313.png\|250]]
V neorientovaném grafu je to jednodušší, protože nejsme omezeni na pohyb, takže tentokrát začneme s **E**.<br>Konečný uzavřený tah je: **{ E - D - B - A - C - B - E }**| ![[Pasted image 20230821180555.png\|250]]

#### Topologické očíslování

**Topologické očíslování** je vytvoření posloupnosti vrcholů grafu v topologicky správném pořadí. K tomu potřebujeme seznam vrcholů a počet hran vstupujících do každého vrcholu.

1. Vytvořte výše uvedený seznam vrcholů a indexů d<sub>in</sub>
2. Vyberte vrchol s minimálním indexem d<sub>in</sub>
3. Indexy vrcholů, ke kterým vede dříve vybraný vrchol, snížit o 1
4. Označme vrchol jako topologicky navštívený vrchol
5. Opakujte kroky 2-4, dokud nebudou všechny vrcholy v konečné řadě

EX:

Popis | Graficky
--- | :-: 
Máme daný orientovaný graf, dokonce acyklický. Vytvoříme pro něj posloupnost vrcholů od 0 do 8 a pro každý z nich označíme počet vstupních hran. | ![[Pasted image 20230822124036.png\|250]]

%% Separator %%

V | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | Očíslování
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
**d<sub>in</sub>(V)** | | \| \| | \| \| | \| \| | \| \| | \| |  | \| | \| | { }


Krok | Graficky
--- | :-:
1 - Postup z videa zde zopakujeme, a přestože bychom mohli klidně začít ve vrcholu **0**, zvolíme **6**. Poté provedeme opravy v tabulce pro vrcholy **3**, **7**, **8**. | ![[Pasted image 20230822120931.png\|250]]

%% Separator %%

V | 0 | 1 | 2 | 3 | 4 | 5 | ==*6*== | 7 | 8 | Očíslování
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
**d<sub>in</sub>(V)** | | \| \| | \| \| | \| | \| \| | \| | ✓ | | | { 6 }

%% Separator %%

Krok | Graficky
--- | :-:
2 - Skončeme s touto částí grafu, a proto vybereme prázdné vrcholy **7** a **8**, pak je také označíme v očíslování. | ![[Pasted image 20230822121107.png\|250]]

%% Separator %%

V | 0 | 1 | 2 | 3 | 4 | 5 | 6 | ==*7*== | ==*8*== | Očíslování
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: 
**d<sub>in</sub>(V)** |  | \| \| | \| \| | \| | \| \| | \| | ✓ | ✓ | ✓ | { 6, 7, 8 }

%% Separator %%

Krok | Graficky
--- | :-:
3 - Podle tabulky je dalším vrcholem **0**, kde již opravíme údaje pro další 4 vrcholy: **1**, **2**, **3**, **4**. | ![[Pasted image 20230822121620.png\|250]]

%% Separator %%

V | ==*0*== | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | Očíslování
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: 
**d<sub>in</sub>(V)** | ✓ | \| | \| |  | \| | \| | ✓ | ✓ | ✓ | { 6, 7, 8, 0 }

%% Separator %%

Krok | Graficky
--- | :-:
4 - Další volný vrchol je **3**, ale musíme opravit i spojení s **2**. | ![[Pasted image 20230822122112.png\|250]]

%% Separator %%

V | 0 | 1 | 2 | ==*3*== | 4 | 5 | 6 | 7 | 8 | Očíslování
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
**d<sub>in</sub>(V)** | ✓ | \| | | ✓ | \| | \| | ✓ | ✓ | ✓ | { 6, 7, 8, 0, 3 }

%% Separator %%

Krok | Graficky
--- | :-:
5 - V předchozím kroku jsme uvolnili vrchol **2**, proto jej označíme jako další, který uvolní **1** a **5** najednou. | ![[Pasted image 20230822122432.png\|250]]

%% Separator %%

V | 0 | 1 | ==*2*== | 3 | 4 | 5 | 6 | 7 | 8 | Očíslování
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
**d<sub>in</sub>(V)** | ✓ | | ✓ | ✓ | \| | | ✓ | ✓ | ✓ | { 6, 7, 8, 0, 3, 2 }

%% Separator %%

Krok | Graficky
--- | :-:
6 - Nakonec odstraníme všechny hrany - přejdeme na **1** a uvolníme **4**. | ![[Pasted image 20230822122644.png\|250]]

%% Separator %%

V | 0 | ==*1*== | 2 | 3 | 4 | 5 | 6 | 7 | 8 | Očíslování
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
**d<sub>in</sub>(V)** | ✓ | ✓ | ✓ | ✓ |  |  | ✓ | ✓ | ✓ | { 6, 7, 8, 0, 3, 2, 1 }

%% Separator %%

Krok | Graficky
--- | :-:
7 - Přidáme **5** na konec jako poslední volný vrchol. | ![[Pasted image 20230822122728.png\|250]]

%% Separator %%

V | 0 | 1 | 2 | 3 | ==*4*== | ==*5*== | 6 | 7 | 8 | Očíslování
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
**d<sub>in</sub>(V)** | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | { 6, 7, 8, 0, 3, 2, 1, 4, 5}


#### Chceš jádro?

**Jádro** orientovaného grafu G = (V, E) je množina J ⊆ V jeho vrcholů taková, že mezi libovolnými dvěma nevede hrana a z každého vrcholu mimo J vede aspoň jedna hrana do J.

Tento úkol lze samozřejmě řešit očima, stejně jako všechno ostatní. My však použijeme již vyřešený Topologického očíslování! ==Náš graf je acyklický => má jedno jádro!==

![[Pasted image 20230822124036.png\|300]]

Topologického očíslování: V = { 6, 7, 8, 0, 3, 2, 1, 4, 5}

EX:
1. Začněme opět od konce: můžeme přidat k jádru **5**? Ano, protože je na něm hrana, ale nic z něj nevede
   J = { 5 }
2. Kdo je spojen s **5**? Vrchol **2**, který vede k **5**, a proto nebude v jádře.
   J = { 5 }
3. Vraťme se k původnímu pořadí a podívejme se na vrchol **4**, ke kterému vedou dvě hrany, ale nic z něj nevychází. K současnému jádru také nemá žádnou vazbu, takže ji přidáme:
   J = { 5, 4 }
4. Podle výše uvedené logiky kontrolujeme **0** a **1**, obě vedou do jádra, takže je nepřidáváme.
   J = { 5, 4 }
5. Pokud jde o vrchol **3** - vše splňuje podmínky, přidáme jej do jádra. V souladu s tím už **6** v jádře rozhodně nebude.
   J = { 5, 4, 3 }
6. Konečně **7** a **8** jsou volné, takže je přidáme do jádra.
   J = { 5, 4, 3, 7, 8 }

#### Obarvení grafu

**(O)Barvení grafu** je jednou z disciplín teorie grafů, která se zabývá přiřazováním barev vrcholům v grafu. Algoritmus spočívá v postupném obarvování vrcholů za podmínky, že každé dva sousedé nemají stejnou barvu.

1. Vyberte vrchol, obarvěte jej povolenou barvou (barvy jsou označeny velkými písmeny)
2. Najděte všechny sousedy vrcholu, zakažte jim stejnou barvu
3. Přejděte na další vrchol a obarvěte jej první povolenou barvou
4. Opakujte kroky 1-3, dokud nejsou všechny vrcholy vybarveny

EX:

V | Barva (V) | Zakázané barvy (V)
:-: | :-: | :-:
0 | R | 
1 | G | R
2 | R | G
3 | G | R
4 | G | R
5 | B | R, G
6 | R | G

Graf je 3-barevný:

![[Pasted image 20230822161045.png|300]]

