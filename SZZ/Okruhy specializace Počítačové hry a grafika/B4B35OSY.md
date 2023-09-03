## Obsah:
1. [[#1. Systémová volání|Systémová volání]]
	- Jak je implementována ochrana paměti jádra.
	- Jak se předávají parametry a data ze systémových volání.
	- Rozdíl mezi mikro jádrem a monolitickým jádrem.
2. [[#2. Vlákna, procesy|Vlákna, procesy]]
	- Jak se vytvoří proces.
	- Jak lze předat data mezi procesy.
	- Jaký je rozdíl mezi vlákny a procesy, která data sdílejí různá vlákna jednoho procesu.
	  (registry, zásobník, lokální proměnné, globální proměnné, dynamicky alokované proměnné)
3. [[#3. Synchronizace vláken|Synchronizace vláken]]
	- Jaké jsou problémy při paralelním přístupu ke sdíleným datům.
	- Jaké existují synchronizační prostředky.
	- Co je to deadlock, kdy může nastat a lze se deadlocku vyhnout?
4. [[#4. Správa virtuální a fyzické paměti|Správa virtuální a fyzické paměti]]
	- Co je a jak vypadá stránkovací tabulka.
	- Jaké jsou zásadní nevýhody stránkování? 
	- TLB (translation-lookaside-buffer).
	- Více úrovňové stránkování v 32-bit a 64-bit systému.
	- Odkládání stránek na disk.
	- Algoritmy výběru oběti.
	- Metoda copy-on-write.
5. [[#5. Souborové systémy|Souborové systémy]]
	- Jaké typy souborových systémů znáte, který je vhodný pro sekvenční čtený a který pro náhodné čtení souborů? 
	- Vysvětlete základní souborové systémy: FAT, systémy založené na inodech a systémy založené na extendech.
	- Žurnálování - základní princip, kdy mohou vzniknout v souborovém systému chyby, jaké jsou úrovně žurnálování a jeho nevýhody.
6. [[#6. Bezpečnost|Bezpečnost]]
	- Co je Trusted Computing Base.
	- Základní metody řízení přístupu, jak se provádí útok na přetečení zásobníku, jak se lze takovémuto útoku bránit.
7. [[#7. Virtualizace|Virtualizace]]
	- Softwarová virtualizace.
	- Metoda trap-and-emulate.
	- Virtualizace systémového volání.
	- Virtualizace stránkovacích tabulek.
	- Hardwarově asistovaná virtualizace.

## 1. Systémová volání

### Ochrana paměti jádra

**Jádro** 
- Poskytuje ochranu/izolaci
	- Aplikačních programů mezi sebou
	- Hardwaru před škodlivými aplikacemi
	- Dat (souborů) před neoprávněnou manipulací
- Řídí přidělování zdrojů aplikacím
	- Paměť, procesorový čas, přístup k HW, síti, …
- Poskytuje aplikacím služby (tohle nas zajima)

**Přerušení** je základní mechanismus, který umožňuje externím zařízením, programům nebo událostem "přerušit" běžící program nebo proces a získat pozornost procesoru. 
Když dojde k události, která vyžaduje okamžitou reakci, jako například stisknutí klávesy na klávesnici, dokončení operace vstupu/výstupu nebo chybová podmínka, generuje se přerušení.

==Uživatel má do jádra OS přístup **pouze** přes obsluhu přerušení, nebo podobný mechanismus!==

![[Pasted image 20230831143727.png|center|300]]

---

### Jak se předávají parametry a data ze systémových volání?

Při volání operačního systému (systémové volání) z uživatelského programu je potřeba předat parametry a data z uživatelského prostoru do jádra operačního systému a zpět.

Služby jádra (systémové volání) jsou číslovány! 

1. Registr ***eax*** obsahuje číslo požadované služby
   (e.g. v Linuxu: *Otevření souboru* -> kód 5,* Alokace paměti* -> kód 200)
	- Poznamka: V Windows kódy napsány ve formatu HEX (e.g. Kód 0x003E je *Otevření souboru*)
2. Ostatní registry obsahují parametry, nebo odkazy na parametry 
3. Problém je přenos dat mezi pamětí jádra a uživatelským prostorem! 
	- malá data lze přenést v registrech (e.g. návratová hodnota funkce) 
	- ==pro velká data uživatel musí připravit prostor, jádro z/do něj nakopíruje data a předává se pouze adresa (ukazatel)!==

EX:
- `int $0x80` spustí systémové volání
- kód registru `a` je nastaven na hodnotu 4 pro výstup na obrazovku
- kód registru `b` je nastaven na 1 pro standardní výstup
- registr `c` obsahuje ukazatel na řetězec `"Hello World\n"`
- registr `d` má délku textu 12
- přítomnost `"memory"` v seznamu operandů naznačuje, že inline assembly může ovlivnit paměť, a proto by se neměly ukládat do mezipaměti žádné hodnoty
```
int main()
{
    asm volatile (
        "int $0x80"
        :
        : "a" (4), "b" (1), "c" ("Hello World\n"), "d" (12)
        : "memory"
    );
    return 0;
}
```

---

### Mikro jádro a monolitické jádro

#### Monolitické jádro

**Monolitické jádro** je tradičním přístupem, kde všechny hlavní funkce operačního systému jsou integrovány do jednoho velkého programu (jádra). Monolitické jádro je kompaktní a rychlé, protože není nutné volat přes mezivrstvy při komunikaci mezi komponentami jádra. Na druhou stranu může být monolitické jádro náchylné na chyby, protože jedna chyba může ohrozit stabilitu celého systému. Příkladem monolitického jádra je jádro Linux.

- Procesy jsou jen uživatelské a systémové programy
- Jádro OS je prováděno jako velmi složitý program v privilegovaném režimu
- Služba jádra OS je typicky implementována jako kód v jádře, běžící jako přerušení využívající paměťový prostor volajícího programu

![[Pasted image 20230831152139.png|center|300]]

#### Mikro jádro (microkernel)

**Mikrojádro** je modernějším přístupem, kde jádro obsahuje pouze nejzákladnější funkce, jako jsou plánování procesů, správa paměti a komunikace mezi procesy. Většina služeb a ovladačů je umístěna mimo jádro, v uživatelském prostoru. Mikrojádro se snaží minimalizovat množství kódu běžícího v privilegovaném režimu a umožňuje jednotlivým službám běžet v izolovaných a chráněných prostředích. Příkladem mikrojádra je projekt MINIX.

- OS je soustavou systémových procesů
- Funkcí jádra je tyto procesy separovat, ale umožnit přitom jejich kooperaci

![[Pasted image 20230831152238.png|center|400]]

#### Zaver

Základní rozdíl mezi monolitickým jádrem a mikrojádrem spočívá v rozsahu funkcí, které jádro obsahuje. Monolitické jádro má všechny funkce integrovány v jádru, zatímco mikrojádro má jádro zredukováno na minimální sadu základních služeb a ostatní funkce jsou implementovány mimo jádro.

---

## 2. Vlákna, procesy

### Jak se vytvoří proces

**Proces** = spuštěný program!
1. Obsahy registrů procesoru (čítač instrukcí, ukazatel zásobníku, příznaky FLAGS, uživatelské registry, FPU registry)

Rodič vytváří nový proces (potomka) voláním služby **fork** - vznikne identická kopie rodičovského procesu až na: 
- návratovou hodnotu systémového volání 
  (určuje, kdo je potomek a kdo rodič: 0 – jsem potomek, PID – jsem rodič a získávám PID potomka)
- hodnotu PID, PPID – číslo rodičovského procesu

Proces se za dobu své existence prochází více stavy a nachází se vždy v jednom z následujících stavů:
- **Nový (new)** – proces je právě vytvářen, ještě není připraven k běhu
- **Připravený (ready)** – proces čeká na přidělení procesoru
- **Běžící (running)** – instrukce procesu jsou právě vykonávány procesorem
- **Čekající (waiting, blocked)** – proces čeká na událost
- **Ukončený (terminated)** – proces ukončil svoji činnost

![[Pasted image 20230831153126.png|center|400]]

---

### Jak lze předat data mezi procesy

Přepnutí od jednoho procesu k jinému nastává v důsledku nějakého přerušení!
Proces krok-za-krokem:
1. Přechod od procesu $P_1$ k $P_2$ zahrnuje tzv. přepnutí **kontextu**
2. Proces $P_1$ přejde do jádra, který provede přepnutí kontextu → spustí se proces $P_2$
	- OS uschová stav původně běžícího procesu $P_1$
	- OS rozhodne, který proces poběží dál ($P_2$)
	- Obnoví se stav procesu $P_2$
	- Když vlákno neběží ($P_1$), je kontext vlákna uložený v TCB (Thread Control Block)
3. Přepnutí kontextu (doba přepnutí závisí na  HW podpoře v CPU)!

---

### Jaký je rozdíl mezi vlákny a procesy, která data sdílejí různá vlákna jednoho procesu.

[[B4B36PDV#Procesy a vlákna]]

#### Vlákno

**Vlákno** - 

Vlastnosti:
- Vlákna jsou vytvářeny v rámci procesu a viditelný uvnitř procesu
- Vlákna se nachází ve [[#Jak se vytvoří proces|stavech]]
- Když vlákno neběží, je kontext vlákna uložený v TCB (Thread Control Block)
- Vlákno může přistupovat k globálním proměnným a k ostatním zdrojům svého procesu, data jsou sdílena všemi vlákny stejného procesu:
	- Změnu obsahu globálních proměnných procesu vidí všechna ostatní vlákna téhož procesu
	- Soubor otevřený jedním vláknem je viditelný pro všechna ostatní vlákna téhož procesu
- Vlákna se vytvoří i ukončí rychleji než proces
- Přepínání mezi vlákny je rychlejší než mezi procesy (stejný kontext)
- Pokud jedno vlákno na něco čeká, ostatní vlákna nemohou běžet, protože jádro OS označí jako čekající celý proces

![[Pasted image 20230831155430.png | center |400]]

Realizace: - knihovna [[B4B36PDV#Pthreads (POSIX Threads)|PThread]], Java - třída Thread

#### Rozdíl

Vlákno a proces jsou v mnohém podobné. Oba mají identifikátor, množinu registrů které využívají, oba jsou v nějakém stavu plánování, mají nějakou prioritu, mohou měnit obsahy svých proměnných či alokovat nové zdroje atd.

==Hlavním rozdílem, mezi procesem a vláknem je sdílení paměti. Zatímco proces je robustní a samostatný celek, který má všechnu paměť sám pro sebe, vlákno sdílí svoji paměť s dalšími vlákny.==

#### Sděleni dat (komu co patří)

Proces | Vlákno
:-: | :-:
kód programu<br>globální proměnné<br>otevřené soubory<br>správa paměti<br>uživatelská práva|zásobník<br>lokální proměnné<br>čítač instrukcí<br>registry CPU<br>plánovací stav

---


### Plánování procesů

#### FCFS (First-Come = First-Served) 

FCFS je primitivní nepreemptivní plánovací postup. Je to prostá fronta FIFO (first in - first out).

![[Pasted image 20230831170921.png|center|400]]

#### SPN (Shortest Process Next) 

Název hlasí: nejkratší proces jako příští. Vybírá se připravený proces s nejkratší příští dávkou CPU. Je-li kritériem kvality plánování průměrná doba čekání, je SPN optimálním algoritmem!

![[Pasted image 20230831171100.png|center|420]]

#### SRT (Shortest Remaining Time)

Nejkratší zbývající čas - CPU dostane proces, který potřebuje nejméně času do svého ukončení!
Může existovat více procesů se stejným zbývajícím časem, a pak je nutno použít "arbitrážní pravidlo", např. vybrat první z fronty.

![[Pasted image 20230831173813.png|center|400]]

#### Round-Robin

Každý proces dostává CPU periodicky na malý časový úsek, tzv. časové kvantum (desítky ms).
Po vyčerpání kvanta je běžícímu procesu odňato CPU a běžící proces se zařazuje na konec fronty (Žádný proces nedostane 2 kvanta za sebou).

![[Pasted image 20230831174016.png|center|400]]

#### Feedback

Neznáme předem časy, které budou procesy potřebovat na CPU, takze resenim je Penalizace procesů, které běžely dlouho (Implementace pomocí víceúrovňových front)!

---

## 3. Synchronizace vláken

### Synchronizační prostředky

#### Mutex

[[B4B36PDV#Mutex]]

#### Semafor

**Semafor** je typ zámku založený na **čítači**. Semafor umožňuje dvě operace:

- **wait (acquire, down)**: Tato operace se volá před vstupem do KS. Při zavolání operace se vyhodnocuje hodnota čítače:
	- pokud je nenulová, vlákno dekrementuje čítač (typicky o 1) a pokračuje dál ve vykonávání kritické sekce
	- pokud je nulová, vlákno se zablokuje až do doby, než je čítač opět nenulový
- **post (release, up)**: Tato operace se volá ihned po vystoupení z KS. Při zavolání operace se inkrementuje čítač (typicky o 1). Inkrementování čítače o $n$ způsobí probuzení $n$-čekajících vláken (viz operace wait).

![[Pasted image 20230831163744.png|center|400]]

Semafor lze inicializovat na libovolné číslo - toto číslo vyjadřuje, kolik vláken současně může být v kritické sekci. ==Mutex lze simulovat semaforem s počáteční hodnotou čítače 1.==

#### Spin-lock

Spinlock zajišťuje synchronizaci procesů pomocí "busy waiting". Vlákna, která se pokoušejí přistupovat do KS, se proto pouze zacyklí. Všimněte si, že vlákno během smyčky nevykonává skutečnou práci!

![[Pasted image 20230831163822.png|center|400]]

![[Pasted image 20230831164236.png|center|400]]

#### Monitor

Monitor je typ zámku, který spojuje koncepty vlastníka a množiny čekajících vláken, kde vlákna čekají až do splnění určité podmínky. Vlákna mezi sebou mohou komunikovat a sdělovat si vzájemně, že byla podmínka splněna a mohou čekání přerušit. Vlastník se také může svého vlastnictví vzdát a připojit se tak k ostatním čekajícím vláknům. Platí, že kritickou sekci může spustit pouze aktuální vlastník monitoru.

---

### Problemy

#### Deadlock
[[B4B36PDV#Uváznutí (deadlock)]]

#### Soubeh (race condition)
[[B4B36PDV#Souběh (race condition)]]

---

## 4. Správa virtuální a fyzické paměti

**FAP (Fyzický Adresní Prostor)**:
- skutečná paměť počítače – RAM
- velikost závisí na možnostech základní desky a na osazených paměťových modulech

**LAP (Logický Adresní Prostor)**:
- někdy také virtuální paměť
- velikost záleží na architektuře CPU

Výhody systému bez správy paměti: 
- rychlost přístupu do paměti 
- jednoduchost implementace 
- lze používat i bez operačního systému (*robustnost*)

Nevýhody systému bez správy paměti 
- Nelze kontrolovat přístup do paměti (kdokoli může cokoli v paměti přepsat) 
- Omezení paměti vlastnostmi HW

### Segmentace

Program je kolekce segmentů - každý má svůj logický význam (hlavní program, procedura, funkce, objekt a jeho metoda, proměnné, pole, ..)

![[Pasted image 20230831174841.png | center | 400]]

Výhody segmentace:
- Segment má délku uzpůsobenou skutečné potřebě 
- Lze detekovat přístup mimo segment, který způsobí chybu segmentace ("segmentation fault") 
- Lze nastavovat práva k přístupu do segmentu 
- Lze pohybovat s daty i programem v fyzické paměti - posun počátku segmentu je pro aplikační proces neviditelný a nedetekovatelný 

Nevýhody segmentace:
- Alokace segmentů v paměti je netriviální úloha
- Segmenty mají různé délky
- Při běhu více procesů se segmenty ruší a vznikají nové

### Stránkování

Stránkování paměti je metoda správy virtuální paměti, kdy strojové instrukce procesu pracují s logickými adresami, které jednotka **MMU** (typicky součást procesoru) převádí na fyzické adresy (skutečné umístění v paměti RAM).

FAP se dělí na úseky zvané **rámce (frame)** - Pevná délka, zpravidla v celistvých mocninách 2
LAP se dělí na úseky zvané **stránky (page)** - Pevná délka, shodná s délkou rámců

Překlad "logická adresa → fyzická adresa" se provádí pomocí tabulky stránek (**PT = Page Table**)!

#### TLB (translation-lookaside-buffer)

Je speciální rychlá cache paměť pro čísla rámců a čísel stránek pro zrychlení překladu virtuálních adres na fyzické adresy během provádění paměťových operací.

Princip:
- MMU se zeptá TLB: "znáš hodnotu rámce pro číslo stránky p?"
- Odpověď buď ano je to r (TLB hit), nebo neznám (TLB miss, ==je jednou z nevyhod stránkování!==)

Krok za krokem:
1. Při každém pokusu o přístup k paměti se virtuální adresa (generovaná procesorem) nejprve porovná s obsahem TLB
	- Pokud TLB HINT, znamená to, že TLB obsahuje překlad pro tuto virtuální adresu
	- Pokud TLB MISS, procesor se musí obrátit na tabulku stránek (page table)
2. Po získání fyzické adresy je tato adresa uložena do TLB spolu s odpovídající virtuální adresou, takže příště bude rychlejší přístup ke stejnému překladu

![[Pasted image 20230831175437.png]]

==TLB má omezenou kapacitu, a proto může dojít k situaci, kdy je TLB plný a musí být některá položka nahrazena novou!==

#### Principy stránkování (Kdy stránku zavádět do FAP?)

##### Kopírovat při zápisu (copy-on-write)

Při tvorbě nového procesu není nutné kopírovat žádné stránky, ani kódové ani datové. U datových stránek se zruší povolení pro zápis. Při modifikaci datové stránky nastane chyba, která vytvoří kopii stránky a umožní modifikace.

1. Na začátku jsou dvě nebo více entit (e.g. vlákna) sdílením referencující stejná data.
2. Když jedna z entit potřebuje provést změny v těchto sdílených datech (např. zápis), místo vytváření nové kopie dat se vytvoří kopie, pouze pokud je to nezbytně nutné. Jinak se použije sdílená verze dat.
3. Místo okamžitého kopírování se zaznamená, že daná entita provedla změny na určitých místech dat.
4. Když je entita připravena provést zápis do těchto dat, provede se kopírování (pomocí alokace nové paměti) pouze těch konkrétních částí, které se mají změnit. Ostatní části dat zůstávají sdílené.

##### Stránkování při spuštění

Program je celý vložen do paměti při spuštění, ale je tp velmi nákladné a zbytečné, předem nejsou známy nároky na paměť ("dříve se nevyužívalo, dnes je využívána").

##### Stránkování/segmentace na žádost (Demand Paging/Segmentation)

Líná metoda, která nedělá nic dopředu. Řeší problémy s dynamickou alokací proměnných.

##### Předstránkování (Prepaging)

Nahrává stránku, která bude pravděpodobně brzy použita.

##### Čištění (Pre-cleaning)

Změněné rámce jsou ukládány na disk v době, kdy systém není vytížen.

#### Odkládání stránek na disk

Odkládání stránek na disk je součástí virtuálního paměťového systému a slouží k uvolnění paměti tím, že některé stránky, které nejsou aktivně používány, jsou přesunuty z fyzické paměti do paměti na disku. Tím se uvolní místo pro aktuálně používané stránky a umožní se efektivnější využívání omezené paměti.

Odkládání stránek na disk se provádí v několika situacích:
1. Pokud je stránka v paměti, ale nebyla dlouhou dobu aktivní
2. Když dojde k nedostatku fyzické paměti (tzv. "page fault")

##### Politika nahrazování (Algoritmy výběru oběti)

**LRU (Least recently used)** - z TLB vybírá položku, která byla naposledy použita nejméně. 
**NRU (Not Recently Used)** - Každá položka v mezipaměti nebo TLB je označena jako "nedávno použita" nebo "dávno nepoužita". Při rozhodování o tom, která položka bude nahrazena, jsou preferovány položky, které byly dávno nepoužity.
**Second Chance** - Každá položka v mezipaměti nebo TLB je označena "s šancí" nebo "bez šance". Pokud je položka označena s šancí, znamená to, že byla nedávno použita, ale zároveň je stále v rozmezí, kdy by mohla být nahrazena. Pokud je položka označena bez šance, znamená to, že byla nedávno použita a neměla by být nahrazena.

#### Fragmentace jako nevýhoda stránkování

**Externí (vnější) fragmentace** - jsou stránky částečně naplněny.

**Interní (vnitřní) fragmentace** - jsou stránky roztroušeny po paměti a nemohou být spojeny do souvislého bloku, zbytek je tak malý, že ho nelze využít.

#### Více úrovňové stránkování v 32-bit a 64-bit systému.

#todo

---

## 5. Souborové systémy

Co je **souborový systém**? Je způsob organizace dat na pevném disku:
- data uložená v pojmenovaných souborech
- soubory v adresářích (složkách)
- hierarchická struktura adresářů

### Typy souborových systémů

Jaké typy souborových systémů znáte, který je vhodný pro sekvenční čtený a který pro náhodné čtení souborů? 

#### FAT (File Allocation Table)

Souborový systém **FAT (File Allocation Table)** je starší a jednoduchý souborový systém, který byl původně vyvinut firmou Microsoft pro použití na disketách a pevných discích.

- Soubory jsou rozděleny do bloků nebo clusterů. Každý cluster je určitým počtem sektorů na disku. 
- Tabulka alokace souborů (FAT) obsahuje záznamy o každém clusteru, které udávají, zda je volný nebo obsazený, a pokud je obsazený, kam vede další cluster souboru.
- Adresace v souborovém systému FAT je založena na číslech clusterů.
- Data souborů jsou uložena v jednotlivých clusterech. Pokud je soubor velký a potřebuje více clusterů, jsou tyto clustery spojeny pomocí záznamů v tabulce FAT. Každý záznam v tabulce FAT ukazuje na následující cluster v řadě.

![[Pasted image 20230831213657.png|center|400]]

#### Systémy založené na inodech (Ext2-3)

**Inode** je datová struktura v souborovém systému, která obsahuje informace o souboru nebo adresáři (metadata), kromě jeho jména a aktuálního obsahu.
- Každý soubor nebo adresář má svůj vlastní inode!

V souborových systémech založených na inodech, jako je ext2, ext3 je tabulka inodů předem alokována na disku, což umožňuje rychlý přístup k metadatům souboru. Adresáře v těchto systémech obsahují pouze odkazy na inody a jména souborů, což umožňuje efektivní přejmenování a přesun souborů.

![[Pasted image 20230902173636.png]]

#### Systémy založené na extendech(Ext-4)

**Extent** je souvislý blok diskového prostoru, který je alokován pro ukládání dat souboru.
Místo toho, aby sledoval každý jednotlivý blok, který tvoří soubor, souborový systém založený na extenzích sleduje pouze začátek a délku každého extentu.

Souborové systémy založené na extenzích, jako je ext4 nebo XFS, používají extenty k zefektivnění alokace diskového prostoru, zejména pro velké soubory.
Použitím extenzí může souborový systém snížit množství metadat potřebných k sledování umístění souboru na disku.
Extenty také pomáhají snižovat fragmentaci souborů, protože se snaží alokovat souvislé bloky pro soubory, kdykoli je to možné.

![[Pasted image 20230902174249.png]]

---

### Žurnálování (Logs)

Základní princip, kdy mohou vzniknout v souborovém systému chyby, jaké jsou úrovně žurnálování a jeho nevýhody.

---

## 6. Bezpečnost

## 7. Virtualizace