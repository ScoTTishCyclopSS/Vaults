## Obsah
1. [[#1. Vnímání barev|Vnímání barev]]
2. [[#2. Barevné modely|Barevné modely]]
3. [[#3. Rastrová grafika|Rastrová grafika]]
4. [[#4. Vektorová grafika a reprezentace 2D objektů|Vektorová grafika a reprezentace 2D objektů]]
5. [[#5. Reprezentace 3D objektů a principy 3D modelování|Reprezentace 3D objektů a principy 3D modelování]]
6. [[#6. Principy mapování textur|Principy mapování textur]]


## 1. Vnímání barev

### Tvorba barevného vjemu v lidském oku

Tenká světlocitlivá vrstva pokrývající přibližně 2/3 vnitřního povrchu oka. 

Popis | Graficky
--- | :-:
Obsahuje světlocitlivé buňky:<ul><li>čípky: přibližně 8 milionů, 3 typy citlivé na různé délky vln. Kombinace signálů ze 3 typů čípků (RGB) umožňuje lidské vnímání barev.</li><li>tyčinky: přibližně 120 milionů, 10x citlivější než čípky. Tyčinky jsou citlivé pouze na jas. Jsou zodpovědné za noční vidění - proto nevidíme barvy při slabém osvětlení. </li></ul> | ![[Pasted image 20230823131820.png\|300]]

Popis | Graficky
--- | :-:
<ul><li>**Žlutá zóna** - část sítnice s maximální koncentrace čípků. Umožňuje ostré vidění v malém úhlu (makulární vidění). Mimo úhel vidíme slaběji rozlišením (periferní vidění).</li><li>**Slepá zóna** - spojení optického nervu se sítnicí, neobsahuje žádné světlocitlivé buňky. Náš mozek chybějící informace doplňuje, takže slepou zónu "nevidíme".</li></ul> | ![[Pasted image 20230823132413.png\|300]]

---

### Kolorimetricky experiment

Referenční pole: Monochromatické světlo
Testovací pole: Tři monochromatická světla definovaných vlnových délek (obvykle RGB)

Účastník experimentu kontroluje intenzitu světel. Snaží se nastavit takovou intenzitu světel, aby v testovacím poli vnímal stejnou barvu jako v referenčním poli (ne vždy je to možné).

<span class="maths">R(r1) + G(g1) + B(b1) =  λ</span>

Výsledkem pokusu jsou funkce shody barev. Říká, jakou intenzitu testovacích světel (RGB) musíme nastavit, abychom viděli stejnou barvu jako u monochromatického světla o vlnové délce.

---

### Srovnávací funkce (CIE RGB)

#todo info

---

### Barevné prostory CIE XYZ a CIE xyY

#### Barevný prostor CIE XYZ

Popis | Prevod
--- | ---
<ul><li>prostor nezávislý na zařízení</li><li>umožňuje přesné měření a výpočet barev</li><li>založen na souboru funkcí pro porovnávání barev, které popisují, jak tři typy čípků v lidském oku reagují na různé vlnové délky</li></ul> | Převod z CIE XYZ na CIE xyY:<ol><li>x = X / (X + Y + Z)</li><li>y = Y / (X + Y + Z)</li><li>Y=Y</li></ol>

#### Barevný prostor CIE xyY

Popis | Prevod
--- | ---
<ul><li>reprezentuje barvy v termínech chromatičnosti HUE (x, y) a jasu SATURATION (Y).</li><li>HUE se týká odstínu a sytosti barvy</li><li>SATURATION představuje vnímaný jas barvy</li></ul> | Převod z CIE xyY na CIE XYZ:<ol><li>X = (Y / y) x</li><li>Y=Y</li><li>Z = (Y / y) (1 - x - y)</li></ol>

---

### Chromatický diagram

Popis | Obrazek
--- | :-:
Grafické znázornění barev ve dvourozměrném prostoru. Široce používaným znázorněním je diagram chromatičnosti CIE 1931, který zobrazuje souřadnice chromatičnosti HUE (x, y) barev. <br>Poskytuje vizuální znázornění celé škály viditelných barev a umožňuje porovnávání a analýzu barevných vztahů. | ![[Pasted image 20230823163809.png\|200]] 

---

## 2. Barevné modely

Barevné modely jsou systémy používané k reprezentaci a popisu barev. Definují základní barvy, pravidla míchání barev a parametry pro přesné určení barev.

### Aditivní a subtraktivní skládání barev. <br>  Barevné modely založené na primárních barvách RGB a CMY(K)

![[Pasted image 20230824164251.png]]

#### Aditivní barevný model (RGB)

Popis | Obrazek
--- | :-:
<ul><li>používají se pro displeje (treba počítačové nebo projektory)</li><li>základní barvy jsou R, G, B</li><li>změnou intenzity základních barev lze vytvořit jiné barvy (sčítání různých vlnových délek světla)</li><li>smícháním plné intenzity všech tří barev vzniká bílé světlo (nepřítomnost všech tří barev vede k černé)</li></ul> | ![[Pasted image 20230824164124.png\|200]]

#### Subtraktivní barevný model (CMYK)

Popis | Obrazek
--- | :-:
<ul><li>používají se v tisku a při fyzickém míchání barev, kdy se barvy odečítají od bílého světla</li><li>základní barvy jsou Cyan (C), Magenta (M) a Yellow (Y)</li><li>‘K’ je zkratka pro Key → klíčová barva označuje černou barvu, která se používá pro zlepšení bohatosti vytištěných obrázků</li><li>kombinací stejných intenzit všech tří barev získáme černou barvu (zatímco nepřítomností základních barev získáme bílou barvu)</li></ul> | ![[Pasted image 20230824164315.png\|200]]

---

### Abstraktní barevné modely HSV a HSL

**HSV** a **HSL** (Hue, Saturation, Value/Lightness) poskytují alternativní způsoby reprezentace barev. Tyto modely rozdělují barevné informace do tří složek:

- **Hue** představuje pozici barvy na barevném kole (vyjádřeno ve stupních od 0° do 360°, kde 0° představuje červenou, 120° zelenou a 240° modrou)
- **Saturation** představuje intenzitu nebo čistotu barvy (od 0% nenasycený do 100% plně nasycený)
- **Value/Lightness** představuje jas barvy
	- **Value** představuje jas (relativní množství světla) v barvě, pohybuje se od 0% do 100%
	- **Lightness** vyjadřuje jas barvy ve vztahu k neutrální šedé barvy stejné světlosti, pohybuje se od 0% (nejtmavší nebo černá) do 100% (nejsvětlejší nebo bílá)

==Na rozdíl od Value v HSV může úprava Lightness v HSL změnit jak jas, tak sytost barvy!==

---

## 3. Rastrová grafika

**Rastrová (bitmapová) grafika** je typ digitální reprezentace obrazu, který k ukládání a zobrazování používá mřížku pixelů. V rastrovém obrázku obsahuje každý pixel informaci o barvě a jasu.

### Obraz jako signál, vzorkování, alias a antialiasing

V rastrové grafice je obrázek považován za **dvourozměrný signál**, kde každý pixel představuje vzorek tohoto signálu. Kombinace hodnot pixelů vytváří celkový vzhled obrázku. 
Signály představují informace, které se mění v čase nebo prostoru. Podobně v případě rastrové grafiky představuje obraz vizuální informaci, která se mění ve dvourozměrném prostoru.

#### Vzorkování (Sampling) 

Popis | Obrazek
--- | :-:
Je proces převodu obrazu reálného světa na mřížku pixelů. Hustota pixelů určuje úroveň detailů a rozlišení výsledného rastrového obrazu. Vyšší vzorkovací frekvence zachycují více detailů, ale také vyžadují více paměti a výpočetního výkonu:<ul><li>**PPI** - pixels per inch</li><li>**DPI** - dots per inch, počet bodů, které je zařízení schopno zobrazit v řádku dlouhém 1 palec)</li></ul> | ![[Pasted image 20230823170258.png\|300]]

#### Aliasing

K aliasingu dochází, když jsou vysokofrekvenční detaily v obraze nedostatečně vzorkovány, což vede ke zkreslení nebo zubatým okrajům.

##### Time aliasing (Alias in time domain)

Popis | Obrazek
--- | :-:
Nízká vzorkovací frekvence videa (e.g. Rotating wheel effect, Clock time interval 10 vs 50 min.) | ![[Pasted image 20230824170929.png]]

##### Spatial aliasing (Alias in space domain)

Popis | Obrazek
--- | :-:
Vykreslování signálu o vyšší frekvenci, než je rozlišení obrazu (e.g. brick wall)| ![[Pasted image 20230824171245.png]]

##### Aliasing of lines

Popis | Obrazek
--- | :-:
Ideální čára je aproximována řadou pixelů, které musí ležet na mřížce pixelů | ![[Pasted image 20230824183243.png]]

#### Antialiasing

##### Coverage 

Popis | Obrazek
--- | :-:
Fragmenty jsou zbarveny podle jejich plochy pokryté primitivem | ![[Pasted image 20230824184535.png\|150]]

##### Supersampling (SSAA 2x, 4x)

Popis | Obrazek
--- | :-:
Scéna vykreslená ve vyšším rozlišení a převzorkována (downsampled) | ![[Pasted image 20230824184721.png\|150]]

##### Multisampling (MSAA 2x, 4x)

Popis | Obrazek
--- | :-:
Scéna je vykreslena ve více vzorcích na pixel na okrajích polygonů | ![[Pasted image 20230824184939.png\|150]]

##### Accumulation

Popis | Obrazek
--- | :-:
scéna je vykreslena několikrát a mírně posunutá! | none :(

---

### Kvantizace barev, polotonovani a dithering

#### Color Quantization (Kvantizace barev)

Je proces snižování počtu barev v obraze při zachování vizuální věrnosti. Často se tak děje kvůli zmenšení velikosti souboru nebo kvůli specifickým omezením zobrazení. Kvantizační algoritmy spojují podobné barvy dohromady a mapují je do omezené palety barev: ==pocet barev = 2<sup>bits</sup>!==

![[Pasted image 20230823170800.png|center]]

#### Halftone (Polotónování)

Je technika používaná k simulaci souvislých tónů obrazu pomocí omezeného počtu barev nebo tónů. Dosahuje se toho tak, že se mění velikost nebo vzor teček, čar nebo jiných tvarů, aby se vytvořila iluze souvislých gradientů.

![[Pasted image 20230823170954.png|center|450]]

#### Dithering

Je podobný Polotónování proces, který přidává šum nebo vzory pro efektivnější rozložení barev nebo tónů. Cílem je dosáhnout podobného efektu jako při polotónování ale bez zvýšení rozlišení.

#todo more info?

![[Pasted image 20230823171126.png|center|450]]

---

### Přímá reprezentace barev (direct color) a indexovaná reprezentace barev

#### Přímá reprezentace barev

Popis | Obrazek
--- | :-:
<ul><li>každý pixel přímo ukládá barevnou informaci pomocí hodnot RGB nebo CMYK</li><li>umožňuje širokou škálu barev a plynulé přechody</li><li>vyžaduje více paměti pro uložení barevných informací pro každý pixel</li></ul> | #todo obrazek

#### Indexovaná reprezentace barev

Popis | Obrazek
--- | :-:
<ul><li>používá se vyhledávací tabulka barev (**CLUT** - Color look up table) nebo paleta barev</li><li>každé hodnotě pixelu odpovídá index v  CLUT, který je přiřazen konkrétní hodnotě barvy</li><li>metoda snižuje paměťové nároky, ale omezuje celkový počet barev</li></ul> | ![[Pasted image 20230823180335.png]]

---

### Transformace rastrového obrazu

Uvažujme transformační matici T. Pro transformaci rastrového obrazu musíme vynásobit matici celočíselné souřadnice každého pixelu obrázku s maticí transformační maticí T.
Problém spočívá v tom, že souřadnice pixelů po výpočtu matice transformaci nebudou celá čísla!

#### Forward mapping

- Souřadnice pixelů ve vstupním obraze vynásobíme T, abychom získali souřadnice ve výstupním obraze.
- Ve výstupním obraze jsou díry mezi pixely v bodech vstupního obrázku (při zvětšení měřítka).
- Několik pixelů vstupního obrazu může být zapsáno do stejného místa pixelů výstupního obrazu (když zmenšíme měřítko).

![[Pasted image 20230825090649.png | center | 500]]

#### Inverse mapping

- Souřadnice pixelů ve výstupním obraze vynásobíme inverzní hodnotou T, abychom získali souřadnice ve vstupním obraze.
- Mnohem lepší než Forward mapping (žádné díry nebo více vstupních pixelů zapsaných do stejného výstupního pixelu), ale stále existují problémy

![[Pasted image 20230825090943.png| center | 500]]

#### Image function reconstruction

Výsledky můžeme zlepšit pomocí rekonstrukcí spojité obrazové funkce! Pomocí interpolace získáme aproximaci obrazové funkce:
- Interpolace nejbližšího souseda (hodnota funkce v bodě je hodnotou nejbližšího vzorku).
- Bilineární interpolace (Hodnota je rekonstruována ze 4 nejbližších vzorků pomocí 3 lineárních interpolací)
  
  ![[Pasted image 20230825091410.png | center |500]]
  ![[Pasted image 20230825091451.png| center |500]]
  
- Kubická interpolace

---

### Komprese rastrového obrazu. 

Algoritmy komprese rastrových obrázků zmenšují velikost souboru rastrových obrázků bez výrazné ztráty kvality.

#### Bezztrátová (lossless) komprese

Neztrácí se žádné informace, z komprimovaného obrazu můžeme rekonstruovat původní obraz!

##### RLE (Run Length Encoding)

Dobře funguje u dat, která obsahují dlouhé řady opakujících se hodnot. Při kompresi RLE jsou po sobě jdoucí identické hodnoty nahrazeny počtem výskytů dané hodnoty. Namísto ukládání každé opakující se hodnoty zvlášť reprezentuje RLE data jako dvojice ==hodnota-počet==.  

EX: *WWWBWWBBBWB → 3W1B2W3B1W1B*

##### Huffman encoding

Přiřazuje kratší kódy častěji se vyskytujícím hodnotám a delší kódy méně častým hodnotám, což vede k celkovému snížení množství dat potřebných k reprezentaci informací.<br>Vytváří binární strom na základě četnosti výskytu jednotlivých symbolů (například barevných hodnot v obrázku)!  

![[Pasted image 20230823181156.png|center|300]]

##### LZW (Lempel-Ziv-Welch) compression

Při zpracování dat vytváří slovník často se vyskytujících vzorů. Opakující se vzory nahrazuje kratšími kódy, čímž účinně snižuje množství dat potřebných k reprezentaci informací. Komprese LZW se běžně používá v souborových formátech, jako jsou GIF a TIFF.

![[Pasted image 20230824185622.png]]

#### Ztrátová (lossy) komprese

Dochází ke ztrátě informací, z komprimovaného obrazu nelze rekonstruovat původní obraz!

##### DCT (Compression with discrete cosine transformation in JPEG)
 
==Je ztrátový kvůli kvantizačnímu kroku!==

1. vstupní obraz je rozdělen na malé, nepřekrývající se bloky (obvykle 8x8 pixelů, každý blok je považován za samostatnou jednotku)
2. převod barevného prostoru: RGB → YCbCr. Tento barevný prostor odděluje jasovou (Y) a chrominanční (Cb a Cr) složku obrazu (lidský vizuální systém je citlivější na změny jasu než chrominance, takže oddělení těchto složek umožňuje účinnější kompresi)
3. DCT transformuje prostorové hodnoty pixelů na frekvenční koeficienty, které představují frekvenční obsah obrazu (prostorova oblast → frekvenční oblast)
4. výsledné frekvenční koeficienty jsou kvantovány. Kvantizace snižuje přesnost koeficientů jejich dělením předem stanovenou kvantizační maticí nebo tabulkou (vyšší hodnoty kvantizace vedou k větší kompresi, ale také k větší ztrátě informací)
5. kvantováné koeficienty jsou poté kompresovány pomocí technik entropického kódování (e.g. [[#Huffman encoding|Huffman]])

![[Pasted image 20230825091728.png]]

---

### Základní formáty GIF, PNG a JPEG a jejich vlastnosti

#todo malo info?

#### GIF (Graphics Interchange Format)

Je široce používán pro jednoduchou grafiku, loga a animace. Vhodný pro obrázky s velkými plochami jednotných barev.

- indexovaná reprezentaci barev
- lossless kompresi 
- podporuje průhlednost 

#### PNG (Portable Network Graphics)

Je ideální pro obrázky, které vyžadují zachování vysoké kvality, jako jsou diagramy, ilustrace nebo obrázky s průhledností.

- indexovaná i přímá reprezentace barev
- lossless kompresi 
- podporuje průhlednost 

#### JPEG (Joint Photographic Experts Group)

Je široce používán pro fotografie a složité obrázky.

- přímá reprezentace barev
- lossy kompresi 

---

## 4. Vektorová grafika a reprezentace 2D objektů

**Vektorová grafika** je typ počítačové grafiky, která reprezentuje obrázky a objekty pomocí matematických rovnic, nikoli pomocí mřížky pixelů. Díky tomu je vektorová grafika nekonečně škálovatelná bez ztráty kvality obrazu.

### Reprezentace 2D objektů pomocí parametrických polynomiálních křivek

V polygonální reprezentaci modelujeme zakřivené objekty pomocí čar:
- Čím více čar použijeme, tím přesnější bude model
- Problémem je, že nevíme, v jakém detailu bychom měli takové objekty modelovat
- Když zvýšíme rozlišení (obrazovka vs. tiskárna) nebo když objekt přiblížíme, můžeme vidět, že je tvořen čarami

Křivky:
- Umožňuje vykreslit zakřivené objekty jako hladké při libovolném rozlišení
- K vymodelování objektu potřebujeme výrazně méně křivek než čar
- To usnadňuje úpravy tvaru objektu

[[B0B39PGR#Interpolační (Ferguson, Catmull-Rom) a aproximační křivky (Bézier, B-spline, NURBS).]]

---

### Bezierové křivky a B-spline křivky, formát SVG

#### [[B0B39PGR#Bézier]]

#### [[B0B39PGR#B-spline]]

#### SVG (Scalable Vector Graphics) format

Format je založen na XML a poskytuje způsob popisu 2D objektů, tvarů a textu pomocí matematických rovnic a geometrických vlastností. SVG podporuje různé prvky, jako jsou čáry, obdélníky, kružnice, elipsy a cesty (které mohou definovat take Bézierovy nebo B-spline křivky). Zadáním matematických rovnic a řídicích bodů mohou soubory SVG přesně reprezentovat složité 2D objekty s hladkými křivkami.

EX: (kreslení elipsy s modrou výplní)

~~~
<?xml version="1.0" encoding="UTF-8">  
<svg xmlns="http://www.w3.org/2000/svg" width="200" height="200">  
  <ellipse cx="100" cy="100" rx="80" ry="50" fill="blue" />  
</svg>
~~~

---

## 5. Reprezentace 3D objektů a principy 3D modelování

### Polygonální reprezentace 3D objektů a datové struktury pro jejich reprezentaci, formát OBJ

Popis | Obrazek
--- | :-:
**Polygon** je plocha roviny obkreslená uzavřenou polylinií. <ul><li>vrcholy - body, které definují polygon</li><li>hrany - Čáry, které spojují sousední vrcholy</li></ul>Polygon můžeme představit jako uspořádanou množinu vrcholů. Potřebujeme alespoň 3 vrcholy pro vytvoření polygonu. | ![[Pasted image 20230824103657.png]]

Polygon nazýváme na základě počtu vrcholů: 
- 3 -> triangle
- 4 -> quad
- 5 -> pentagon (nikdo tak nenazyva ffs..)
- N-gon :3

Orientace polygonu (pomáhají určit orientaci normal):
- **Counterclockwise (CCW)** - (standard) normálový vektor směřuje obvykle ven z back face. To znamená, že pokud se díváte na front face, normálový vektor směřuje k vám.
- **Clockwise (CW)** - normálový vektor směřuje obvykle ven z front face. To znamená, že pokud se díváte na front face, normálový vektor směřuje od vás.

Úhly mezi sousedními hranami měřené uvnitř se nazývají vnitřní úhly polygonu, na jejich základě defenujeme následující typy:
- **Konvexní polygon** - všechny vnitřní úhly jsou menší než 180°. Jakékoli dva body uvnitř mnohoúhelníku můžeme spojit přímkou, která je neprotíná žádnou hranu polygonu.
- **Nekonvexní polygon** - alespoň jeden z vnitřních úhlů je větší než 180°.

#### Polygon soup (polygonální polévka)

- sada nezávislých polygonů
- žádna informace o topologii (které polygony spolu sousedí)
- je obtížné opravit chyby (e.g. "překřížení" polygonů, chybějící polygony atd.), protože chybí informace o topologii

#### 3D mesh (Polygonová síť)

- je souhrn vrcholů, hran a ploch (plochy se obvykle skládají z polygonů různých druhů, které jsme uvedli výš), které definují tvar mnohostěnného objektu ve 3D
- máme informace o topologii

#### Datové struktury

##### List vrcholů a stěn

#todo vic data?

- máme seznam vrcholů, kde každý vrchol je reprezentován pouze jednou
- nemáme informaci o hranách
- máme seznam stěn (faců), kde každý face je reprezentován jako uspořádaná množina indexů k seznamu vrcholů

V průměru využijeme polovinu (nebo méně) paměti potřebné pro polygonální polévku. Informace o výskytu faců s jejich vrcholy jsou snadno dostupné. Je možné získat informace o incidenci vrcholů s jejich faces.

##### Incidence lists

- máme seznam vrcholů, hran a stěn
- pro každý vrchol, hranu a stěnu máme seznamy incidentních vrcholů, hran a stěn
- máme informace o topologii

Efektivní vyhledávání incidentních vrcholů, hran a ploch. Paměťově náročné. Každý seznam může mít různé délku.

##### Okřídlená hrana (winged edge)

- máme seznam vrcholů, hran a stěn
- vzhledem k odkazu na hranový záznam lze v konstantním čase zodpovědět dotazy na sousední hrany, vrcholy a stěny 

Tento druh informací je užitečný pro algoritmy, jako je například subdivision surface.

#### Manifold (n-manifold)

Popis | Obrazek
--- | :-:
Je objekt, který je "lokálně" podobný n-rozměrnému euklidovskému prostoru. Okolí každého bodu na povrchu objektu je topologicky ekvivalentní s euklidovským prostorem R<sup>n</sup>:<ul><li>kruh je 1-manifold</li><li>koule je 2-manifold atd.</li></ul> | ![[Pasted image 20230824132207.png\|200]]

#### Uzavřené plochy (Closed surfaces) 

Popis | Priklad
--- | :-:
Jsou 3D objekty, které nemají na povrchu žádné díry. Mohou (ale nemusí) být mnohostranné. Modelování pro výrobu. | ![[Screenshot 2023-08-24 at 13.47.02.png\|150]]

#### Otevřené plochy (Open surfaces)

Popis | Priklad
--- | :-:
Jsou nemanifoldní 3D objekty, které mají ve svém povrchu díry. Povrch objektu má nulovou šířku. Modelování pro filmy a hry. Pokud 3D síť obsahuje hraniční smyčku, pak se jedná o otevřený povrch. | ![[Screenshot 2023-08-24 at 13.49.07.png]]

#### Orientovatelný povrch (Orientable surface)

Povrch je **orientovatelný**, pokud můžeme rozlišit mezi jeho vnitřní/vnější, horní/dolní casti. 

![[Pasted image 20230824142855.png]]


Polygonální plocha je **orientovatelná**, pokud každá hrana, která dopadá na 2 stěny, má v každé z nich jinou orientaci.

![[Pasted image 20230824142913.png | center | 200]]

#### OBJ

**OBJ** je jednoduchý datový formát, který reprezentuje pouze 3D geometrii - konkrétně pozici každého vrcholu, UV pozici každého vrcholu s texturní souřadnicí, normály vrcholů a facy, které tvoří každý polygon definovaný jako seznam vrcholů a vrcholů textury. 

Vrcholy jsou ve výchozím nastavení uloženy v CCW, takže explicitní deklarace normál tváří není nutná. Souřadnice OBJ nemají žádné měřítko, ale soubory OBJ mohou obsahovat informace o měřítku v lidsky čitelném řádku komentáře.

EX:
~~~
# Cube.obj
# A simple cube

# Vertices
v -0.5 -0.5 0.5
v 0.5 -0.5 0.5
v -0.5 0.5 0.5
v 0.5 0.5 0.5
v -0.5 -0.5 -0.5
v 0.5 -0.5 -0.5
v -0.5 0.5 -0.5
v 0.5 0.5 -0.5

# Texture Coordinates 
vt 0.0 0.0 
vt 1.0 0.0 
vt 0.0 1.0 
vt 1.0 1.0

# Faces (Řádky "f" nyní obsahují indexy vrcholů i indexy souřadnic textur oddělené lomítkem ("/"))
f 1/1 2/2 4/4 3/3 
f 5/1 6/2 8/4 7/3 
f 1/1 2/2 6/4 5/3 
f 3/1 4/2 8/4 7/3 
f 1/1 3/2 7/4 5/3 
f 2/1 4/2 8/4 6/3

~~~

---

### Bezierovy a B-Spline plochy

#### Bezierovy plochy

Princip: ("two for loops instead of one")

![[Pasted image 20230901180024.png]]

1. Vytvoření mřížky kontrolních bodů: Uspořádejte kontrolní body ze všech křivek do mřížky. Tato mřížka bude definovat řídicí body pro Bézierovu plochu. Například pokud máte čtyři Bézierovy křivky jako řádky a čtyři jako sloupce, budete mít mřížku řídicích bodů 4x4.
2. Pro křivky ve směru parametru U použijeme na každou z křivek [[B0B39PGR#Adaptivní vykreslování Bézierovy křivky (algoritmus de Casteljau).|deCasteljau algoritmus]] s U = 0,5.
3. Získáme 4 body - KONTROLNÍ body křivky ve směru parametru V.
4. Na novou křivku použijeme deCasteljau algoritmus s U = 0,6.

![[Pasted image 20230901174525.png ]]

![[Pasted image 20230901174759.png | center | 300]]

---

### Základní modelovací operace s použitím polygonální a polynomiální reprezentace 3D objektů: blokování, bridge, extrude, loft, rotačníplochy, volné modelování

#### Blocking

Popis | Obrazek
--- | :-:
<ul><li>3D objekty nemají fyzické vlastnosti skutečných objektů.</li><li>Při blokování 3D objektů využíváme tzv. primitivní objekty a jejich transformace</li><li>Schopnost 3D objektů protínat se</li></ul> | ![[Pasted image 20230825094613.png]]

#### Bridge

Popis | Obrazek
--- | :-:
Connects two polygons by faces:<ul><li>Není nutné, aby profily měly stejný počet vrcholů, ale doporučuje se to</li><li>Polygony mohou mít různou topologii</li><li>Oba polygony musí být součástí stejného 3d objektu</li></ul> | ![[Pasted image 20230825095218.png\|200]]

Algoritmus
1. Nalezení optimálních prvních hran polygu
2. Spojte tyto hrany čtyřúhelníkem
3. Najděte hrany za základními hranami v každém mnohoúhelníku podle orientace mnohoúhelníku
4. Nejsou již žádné další hrany? - ukončete nebo přejděte ke kroku 2

Problem | Obrazek
--- | :-:
Špatná orientace polygonů (musíme změnit orientaci jednoho profilu) | ![[Pasted image 20230825100209.png\|100]]
Špatná první hrana jednoho profilu (potřebujeme otočit jeden z profilů) | ![[Pasted image 20230825100143.png\|100]] 

#### Extrude

Problem | Obrazek
--- | :-:
Vytváří plochy přetažením profilu (vertex, edge, face) podél čáry | ![[Pasted image 20230825100431.png\|150]]

Algoritmus:
1. Duplikovat profil
2. Zmenit pozice duplikát profilu
3. Bridge dvou profilů

#### Lofting

Popis | Obrazek
--- | :-:
Automatické Bridge několika profilů<ul><li>Pořadí profilů je určeno z jejich umístění</li><li>Bridge profilů se provadi v určeném pořadí</li><li>Všechny profily musí být součástí jednoho 3D objektu</li></ul> | ![[Pasted image 20230825100928.png\|200]] 

#### Surfaces of revolution (Rotační plochy)

Popis | Obrazek
--- | :-:
Otáčení profilu kolem osy souřadnicového systému.  Můžeme ovládat:<ul><li>limitní úhel pro otáčení</li><li>počet kroků rotace k limitnímu úhlu</li><li>Počátek rotace (3D kurzor)</li><li>osu otáčení</li></ul> | ![[Pasted image 20230825103905.png\|300]]

Algoritmus:
1. Duplikujte profil
2. Otočte duplikát kolem osy otáčení o úhel
3. Přemostěte profil
4. Opakujte, dokud není dosaženo limitního úhlu

#### Volné modelování

Modelování nejpříšernějším možným způsobem: ruční posouvání profilů pro dosažení požadovaného výsledku. Často se používá například k opravě stínování. 

- Můžeme upravovat vertexy, hrany, facy, smyčky facu
- Transformace fungují podobně jako transformace 3D objektů
- Pokud je jako střed transformace zvolen medián, pak se počítá z pozic vrcholů (místo počátků lokálních souřadnicových systémů)
- Aktivní vrchol je poslední vybraný vrchol

---

### Dělené plochy (subdivision surfaces), jejich reprezentace a výhody při modelování oproti B-spline plochám

Dílčí plochy se chovají jako plochy [[#B0B39PGR B-spline|B-spline]]-[[B0B39PGR#Parametric continuity C<sup>n</sup>|C2]], ale jsou definovány pomocí řídicí mřížky s libovolnou topologií!

- chceme, aby plochy řídicí mřížky byly quady
- pouze v místech **pole (ang.)** (řídicích bodů, které nemají 4 dopadající hrany) se dělicí plocha nebude chovat jako B-spline plocha
- dělící plochy umožňují modelovat libovolný 3D objekt pouze pomocí jedné plochy
- dílčí plochy umožňují řídit místní úroveň detailů
- převod dělící plochy na 3D síť je snadný - stačí X-krát použít pravidla dělení
- adaptivní dělení podle zakřivení povrchu není možné
- povrch je  spojitý ve všech bodech s výjimkou mimořádných vrcholů - pólů (nemají 4 dopadající hrany)
- [[#Okřídlená hrana (winged edge)]]

Pravidla dělení:

Pravidlo | Formula
--- | :-:
Face (n je pocet vertexu pro face) | ![[Pasted image 20230825111635.png]]
Edge | ![[Pasted image 20230825111655.png]]
Vertex (n je pocet incidentnich hran s vertexem) | ![[Pasted image 20230825111804.png]]

Je možné ovládat zakřivení/ostrost povrchu:
- Pomocí control points
- Pomocí sharp edges
- Pomocí support loops

![[Pasted image 20230825110805.png]]

#### Catmull–Clark subdivision surface

**Step 1: Initialize Data Structures**

- Create an empty list to store the new vertices, edges, and faces of the subdivided mesh.

**Step 2: Subdivide Faces**

- For each quad face in the input mesh:
    1. Calculate the face center (average of the four vertex positions).
    2. Append the face center to the list of new vertices.
    3. Split each edge of the face in half (add these edge midpoints to the list of new vertices).
    4. Create four new faces:
        - Each new face connects one of the face's original vertices, the midpoint of an adjacent edge, the face center, and the midpoint of the next adjacent edge.
    5. Append the four new faces to the list of new faces.

**Step 3: Update Original Vertices**

- For each original vertex in the input mesh:
    1. Calculate the weighted average of the vertex's neighboring face centers and edge midpoints. The weights depend on the valence (number of edges connected to the vertex).
    2. Update the position of the original vertex to this weighted average.

**Step 4: Update New Vertices**

- For each newly created vertex in the list of new vertices:
    1. Calculate the weighted average of the adjacent vertices and the neighboring face centers.
    2. Update the position of the new vertex to this weighted average.

**Step 5: Create New Edges**

- For each edge in the input mesh, if it has not been split in Step 2:
    1. Calculate the midpoint of the edge.
    2. Append the midpoint to the list of new vertices.
    3. Create four new faces:
        - Each new face connects one of the edge's original endpoints, the midpoint of the edge, the adjacent face center, and the midpoint of the adjacent edge.
    4. Append the four new faces to the list of new faces.

**Step 6: Assemble Subdivided Mesh**

- Combine the list of new vertices, edges, and faces to form the subdivided mesh.

**Step 7: Normalize Vertex Positions (Optional)**

- If needed, normalize the positions of all vertices in the subdivided mesh to ensure that they lie on the unit sphere (useful for certain applications).

---

### Implicitní plochy a jejich reprezentace a využití při modelování

Vyjádřeno pomocí implicitní rovnice: $F(x,y,z) = 0$
- $F(x,y,z) < 0$ ? -> bod v souřadnicích x,y,z je uvnitř
- $F(x,y,z) > 0$ ? -> bod v souřadnicích x,y,z je vně

Vlastnosti:
- V každém bodě víme, zda se nacházíme uvnitř povrchu, na povrchu nebo vně rurálního povrchu
- Snadné množinové operace (sjednocení, průnik atd.) 
- Snadno lze vypočítat vzdálenost od povrchu

V modelování nazýváme implicitní plochy **bloby** nebo **metabally**!

#### Blobs

Princip modelovani:
1. Uvažujme N implicitních ploch
2. Pro každou implicitní plochu máme funkci $D(r) = 1/r^2$ definující pole vzdáleností, kde $r$ je vzdálenost od implicitní plochy!
3. Globální pole vzdáleností je pak součtem polí vzdáleností všech implicitních ploch
4. Povrch je reprezentován izoplochou (isosurface) globálního pole vzdáleností pro danou hodnotu 
￼
![[Pasted image 20230901165121.png | center | 300]]

#### Metaballs

Zlepšení blobu! Funguji na základě Gaussovy funkce (Blinn) a její aproximace (Nishimura).

- Každý metaball má svůj poloměr
- V oblasti, kde mají vliv dvě nebo více metaballů, je vliv metaballů následující: přidán (kladný) / odečten (záporný) k/od globálního pole vzdálenosti!
- Mimo oblast vlivu je vliv metaball nulový (to urychluje výpočet izoplochy)

![[Pasted image 20230901165627.png|center|250]]

---

### Sochání (sculpting)

- Uživatel maluje na 3D objekty
- Barva je interpretována jako rychlost pohybu vrcholů
	- **Growing** (rust podle normaloveho vektoru)
	- **Constant** (rust podle konstantniho vektoru) 
	- **Curvature** (rust podle normalovwho vektooru v zalezitodti na curvature na posize vertexu)
- Uživatel maluje na objekty, dokud nedosáhne požadovaného tvaru

![[Pasted image 20230825112643.png]]

Po vymodelování pro hry (pro optimalizaci) se provede repotologie. **Retopologie** je proces zjednodušení topologie sítě, aby byla čistší a lépe se s ní pracovalo.

![[Pasted image 20230825112820.png]]

---

## 6. Principy mapování textur

**Mapování textur (UV Mapping)** je proces, při kterém se na povrch 3D objektu aplikuje 2D obrázek známý jako textura, aby se vylepšil jeho vizuální vzhled. 

Cílem je simulovat detaily povrchu a vytvořit realističtější reprezentaci objektu. Souřadnice U a V odpovídají osám 2D textury. Písmena U a V → souřadnice v osách 2D textury, protože X, Y a Z se používají v prostoru 3D modelu.

### Zobrazení textury na 3D objektů s použitím UV mapování

Proces mapování textur zahrnuje několik kroků:

Krok | Obrazek
--- | :-:
1. **UV Unwrapping** je proces vyrovnání povrchu 3D objektu do 2D roviny, tento krok určuje, jak bude geometrie objektu reprezentována v UV mapě (v závislosti na tvaru objektu a požadovaném výsledku lze použít různé [[#Různé způsoby vytvoření UV mapování a k čemu jsou vhodné\|techniky unwrappingu]]) | ![[Pasted image 20230824154830.png]]
2. **Texture Coordinate Assignment**: po vytvoření UV mapy je třeba každému vertexu přiřadit souřadnice textury. Tyto souřadnice určují, jak bude textura aplikována na povrch objektu. | ![[Pasted image 20230824155431.png\|250]]
3. **Texture Mapping**: Textura se poté aplikuje na 3D objekt pomocí UV mapy a souřadnic textury. [[B0B39PGR#Texture mapping]] | ![[Pasted image 20230824155452.png\|250]]


---

### Různé způsoby vytvoření UV mapování a k čemu jsou vhodné

![[Pasted image 20230824163711.png]]

#### (Tri)Planar mapping

Popis | Obrazek
--- | :-:
Planar promítá UV na mesh prostřednictvím roviny. Tato projekce je nejlepší pro objekty, které jsou relativně ploché nebo jsou alespoň zcela viditelné z jednoho úhlu kamery. | ![[Pasted image 20230824161806.png]]
Triplanar je metoda mapování textury bez nutnosti použití UV souřadnic. Funguje tak, že se textura promítne na objekt ze tří různých os (X, Y, Z) a výsledky se promíchají dohromady. Díky tomu lze texturu na objekt aplikovat bez viditelných švů, bez ohledu na jeho tvar nebo orientaci. | ![[Pasted image 20230824162356.png]]

#### Cylindrical Mapping

Popis | Obrazek
--- | :-:
Obalí texturu kolem válcového tvaru. Dobře funguje pro objekty, jako jsou láhve, válce nebo sloupy. Může však vést k viditelným švům v horní a dolní části objektu. | ![[Pasted image 20230824161716.png]]

#### Spherical Mapping (e.g. env. mapping)

Popis | Obrazek
--- | :-:
Promítá texturu na kouli. Je vhodná pro objekty, jako jsou zeměkoule nebo planety. Sférické mapování poskytuje bezešvou aplikaci textury, ale může způsobit zkreslení v blízkosti pólů koule. | ![[Pasted image 20230824161550.png]] 

#### Box Mapping (e.g. skybox)

Popis | Obrazek
--- | :-:
Mapuje šest různých textur na stěny krychle. Je užitečná pro objekty s krabicovými tvary, jako jsou místnosti nebo budovy. Mapování boxů umožňuje přesné umístění textur, ale může vyžadovat dodatečné úpravy textur pro spojení švů. |

---
