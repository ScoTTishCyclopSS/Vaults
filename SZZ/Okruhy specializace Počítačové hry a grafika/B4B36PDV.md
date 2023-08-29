## Obsah:
1. [[#1. Paralelní systémy/výpočty|Paralelní systémy/výpočty]]
- Hardwarová podpora pro paralelní výpočty: (super)skalární architektury, pipelining, spekulativní vyhodnocování, vektorové instrukce, vlákna, procesy, GPGPU. Hierarchie cache pamětí.
- Komplikace v paralelním programování: souběh (race condition), uváznutí (deadlock), iluze sdílení (false sharing).
- Podpora paralelního programování v C a C++: pthreads, thread, jthread, atomic, mutex, lock_guard.
- Podpora paralelního programování v OpenMP: sériově-paralelní model uspořádání vláken (fork-join), paralelizovatelná úloha (task region), různé implementace specifikace. Direktivy parallel, for, section, task, barrier, critical, atomic.
- Techniky dekompozice programu: statické a paralelní rozdělení práce. Threadpool a fronta úkolů. Balancování a závislosti (dependencies).
- Techniky dekompozice programu na příkladech z řazení: quick sort, merge sort.
- Techniky dekompozice programu na příkladech z numerické lineární algebry a strojového učení: násobení matice vektorem, násobení dvou matic, řešení systému lineárních rovnic.
2. [[#2. Distribuované výpočty/systémy|Distribuované výpočty/systémy]]
- Úvod do distribuovaných systémů (DS). Charakteristiky DS. Čas a typy selhání v DS. 
- Detekce selhání v DS. Detektory selhání a jejich vlastnosti. 
- Čas a kauzalita v DS. Uspořádání událostí v DS. Fyzické hodiny a jejich synchronizace. Logické hodiny a jejich synchronizace. 
- Globální stav v DS a jeho výpočet. Řez distribuovaného výpočtu. Algoritmus pro distribuovaný globální snapshot. Stabilní vlastnosti DS. 
- Vzájemné vyloučení procesů v DS. Algoritmy pro vyloučení procesů a jejich vlastnosti. 
- Volba lídra v DS. Algoritmy pro volbu lídra a jejich vlastnosti. 
- Konsensus v DS. FLP teorém. Algoritmy pro distribuovaný konsensus.

## 1. Paralelní systémy/výpočty

### Hardwarová podpora pro paralelní výpočty

#### (Super)Skalární architektury

Umožňují provádět více instrukcí najednou. Procesor rozděluje instrukce na menší části a provádí je paralelně, což zvyšuje výkon. Architektura zahrnuje více vykonávacích jednotek (ALU), které mohou pracovat nezávisle na sobě.

EX:
Uvažme cyklus:

~~~
for (i = 0; i < 1000; i++) 
	z[i] = x[i] + y[i];
~~~
jedna jednotka může počítat z\[0], druhá z\[1] atd.

#### Pipelining (model paralelismu)

Umožňuje **Paralelizace na úrovni instrukcí (ILP)**. ILP rozděluje vykonávání instrukcí do několika fází. Každá fáze zpracovává jinou operaci, což umožňuje, aby se instrukce prováděly sekvenčně, ale zároveň paralelně. 

==Každé vlákno provede nějakou práci nad daty a pošle výsledek dalšímu.==

EX:
Chceme sečíst 2 vektory reálných čísel (float \[1000])
1 součet – 7 operací:
- Načtení (fetch)
- Porovnání exponentů
- Posun
- Součet
- Normalizace
- Zaokrouhlení
- Uložení výsledku

Bez ILP -> 7x1000x (čas 1 operace = 1ns) -> 7000 ns
S ILP (7 jednotek) -> 1000 ns

#### Spekulativní vyhodnocování

#todo

#### Vektorové instrukce

Tyto instrukce umožňují provádět stejnou operaci na více datech současně, což zvyšuje výkon a zpracovávává více úkolů za jedno kolo instrukcí. Vektorové instrukce jsou zvláště užitečné pro aplikace, které pracují s velkými množstvími dat najednou (e.g. zpracování obrazu).



EX:
Vektorové instrukce jsou součástí architektury procesoru, ale my, programátoři, k nim máme přístup prostřednictvím knihoven, jako je níže uvedený kód.  

~~~
#include <immintrin.h>

void vectorizedSum(float *a, float *b, float *result, int size) {
    for (int i = 0; i < size; i += 8) {
        __m256 va = _mm256_load_ps(&a[i]);
        __m256 vb = _mm256_load_ps(&b[i]);
        __m256 vresult = _mm256_add_ps(va, vb);
        _mm256_store_ps(&result[i], vresult);
    }
}
~~~

Tento kód provádí součet dvou vektorů **a** a **b** do vektoru **result** pomocí AVX vektorových instrukcí. Tyto instrukce provádějí součet osmi čísel současně, což zvyšuje rychlost výpočtu.

EX2:
Někdy však stačí do překladače gzc přidat flag (e.g. *-ftree-vectorizer-verbose=1*), který překladači řekne, že má sám paralelizovat cykly pomocí vektorových instrukcí. Například kód, který již známe:

~~~
for (i = 0; i < 1000; i++) 
	z[i] = x[i] + y[i];
~~~

Poznámka: ==ne všechny cykly mohou být automaticky paralelizovány (např. v případě chyby ve velikosti počátečních a konečných vektorových dat)!!!==

#### Procesy a vlákna

Běh (běhové instance) programu se nazývá **proces**. Procesy jsou nezávislé a mohou být prováděny paralelně. Proces obsahuje jedno nebo více **vláken**.

![[Pasted image 20230827174042.png|center|300]]

Procesy žijí v navzájem oddělených adresních prostorech! Vlákna jednoho procesu naopak paměť sdílejí.

![[Pasted image 20230827174237.png|center|400]]

#### GPGPU (General Purpose Graphics Processing Unit)

GPU jsou navrženy pro zpracování grafických výpočtů, ale lze je také použít k provádění dalších operací! **GPGPU** maximalizuje efektivitu zpracování odložením některých operací z CPU na GPU. 
Když nezpracováváte grafiku, je GPU neustále k dispozici pro provádění dalších úkolů! Protože GPU jsou optimalizovány pro zpracování vektor výpočty, mohou dokonce zpracovat některé instrukce rychleji než CPU.

==V závislosti na tom, na jaké GPGPU se chcete zaměřit, může být nutné přepnout flag využití GPU!==

EX:

Pro NVIDIA hardwar NVIDIA CUDA Compiler Driver (NVCC): 
*nvcc -mp=gpu -gpu=cc80* (Pro A100 clusteru RCI)

#### Hierarchie cache pamětí

Hierarchie cache paměti v procesoru je systém, ktery slouží k ukládání často používaných dat. Tyto paměti jsou umístěny přímo na čipu procesoru a slouží k urychlení přístupu k datům, která jsou často používána programy.

![[Pasted image 20230827182211.png|center|350]]

==Před použitím cache použijeme paměťové registry, které máme k dispozici...==

##### L1 Cache

- je první úroveň cache paměti a je umístěna přímo na jádře procesoru
- má malou kapacitu, ale je extrémně rychlá
- má podkategorií: 
	- **instrukční cache (L1i)** ukládá instrukce (kódy) programů, které běží na procesoru
	- **datová cache (L1d)** ukládá data, která jsou právě používána programem 

##### L2 Cache

- je druhá úroveň cache paměti
- má větší kapacitu než L1 cache
- jejím cílem je snížit zátěž na L1 cache a umožnit procesoru ukládat více dat, která nebyla nalezena v L1 cache
- může být sdílena mezi více jádry procesoru v multijádrových procesorech (nebo pokud je to maximální úroveň cache v hierarchii procesoru)

##### L3 cache (Shared LLC)

- je třetí úrovní cache paměti
- sdílena mezi všemi jádry procesoru
- má ještě větší kapacitu než L2 cache a slouží k ukládání dat a instrukcí, které nebyly nalezeny v L1 a L2 cache

##### Proč je tu sakra tolik úrovní?

Cílem je minimalizovat nutnost přístupu do hlavní paměti, která je pomalejší!


---


---

### Podpora paralelního programování v C a C++ 

#### Pthreads (POSIX Threads)

#todo 

#### Thread (Vlákna v C++)

- vlákna jsou exportována hlavičkou `<thread>`
- konstruktor vlákna získá prvním argumentem funkci, kterou bude vykonávat, ostatní argumenty předá volané funkci
- dříve, než je zavolán destruktor std::thread, musíme zavolat metodu `join` – ==spouštějící vlákno čeká, než spuštěné vlákno doběhne!==

EX:

```
#include <iostream>
#include <thread>

using namespace std; // yes, fuck off

void function(int id, int n)
{
	for (int i = 0; i < n; i++)
	{
		cout << "Vlakno " << id << " rika ahoj!\n";
	}
}

int main()
{
	std::thread t1(function, 1, 2);
	std::thread t2(function, 2, 1)
	t1.join();
	t2.join();
}
```


#### Jthread (Joining threads)

`<jthread>` je novým přídavkem v jazyce C++ od standardu C++20. Poskytuje vylepšenou verzi knihovny `<thread>`: automaticky spravuje ukončení vlákna při jeho zničení (==auto join==)! 

#### Mutex

**Mutex** je struktura pro synchronizaci vláken!
- vlákna žádají o vlastnictví (uzamčení) mutexu
- mutex může být v danou chvíli uzamčen pouze jedním vláknem

Zámky jsou RAII třídy, které obsluhují uzamykání a odemykání mutexů. Mutexy a zámky jsou exportovány hlavičkou `<mutex>`.

Problemy? [[#Uváznutí (deadlock)]]

EX:

Poznamka: ==`std::unique_lock` volá unlock na mutexu v jeho destruktoru!==

```
... 
int counter = 0; 
std::mutex mutex; 

auto thread_func = [&counter, &mutex]() 
{ 
	for (int i = 0; i < 1'000'000; ++i) 
	{ 
		std::unique_lock lock(mutex); 
		counter++; 
		counter--; 
	} // destruktor lock odemkne mutex
}; 
...
```

#### Lock_guard

**std::lock_guard** je třída, která poskytuje automatickou správu mutexů. Zajišťuje, že mutex je správně uzamknut a odemknut, i když dojde k vyhození výjimky. Použití `std::lock_guard` je snadný způsob, jak nezapomenout na ruční odemykání mutexů a minimalizovat riziko zaseknutí.

EX:

```
volatile int g_i = 0;
std::mutex g_i_mutex;  // protects g_i
 
void safe_increment(int iterations)
{
    const std::lock_guard<std::mutex> lock(g_i_mutex);
    while (iterations-- > 0)
        g_i = g_i + 1;
    // g_i_mutex is automatically released when lock goes out of scope
}
 
void unsafe_increment(int iterations)
{
    while (iterations-- > 0)
        g_i = g_i + 1;
}
 
int main()
{
    auto test = [](auto func)
    {
        g_i = 0;
        std::jthread t1(1'000'000);
	    std::jthread t2(1'000'000);
	    std::cout << "final g_i: " << g_i << endl;
    };
    
    test(safe_increment);
    test(unsafe_increment);
}
```

#### Atomic

Od verze C++11 existuje vynikající podpora atomických proměnných v hlavičce `<atomic>`. 

**Atomické proměnné** jsou alternativa k mutexu nad jednou proměnnou.
- zamknutí mutexu je poměrně nákladná operace..
- atomické operace nejsou zdarma, ale jsou levnější 
- pracuje se s nimi podobně jako s neatomickými proměnnými, ale operace nad nimi jsou nedělitelné!

==Pozor, pro synchronizaci nad více proměnnými je stále potřeba zámek!==

1. **Vytvoření atomické proměnné**: Začněte tím, že vytvoříte instanci atomické proměnné požadovaného datového typu. Například `std::atomic<int> counter;`.
2. **Čtení hodnoty**: Pro čtení hodnoty atomické proměnné použijte metodu `load()`. Například `int value = counter.load();`.
3. **Zápis hodnoty**: Pro zápis hodnoty do atomické proměnné použijte metodu `store()`. Například `counter.store(10);`.
4. **Inkrementace hodnoty**: Pro inkrementaci hodnoty atomické proměnné můžete použít metodu `fetch_add()` nebo operátor `++`. Například `counter.fetch_add(1);` nebo `counter++;`.
5. **Dekrementace hodnoty**: Pro dekrementaci hodnoty atomické proměnné můžete použít metodu `fetch_sub()` nebo operátor `--`. Například `counter.fetch_sub(1);` nebo `counter--;`.

EX:

```
#include <iostream>
#include <thread>
#include <atomic>

std::atomic<int> counter(0);

void incrementCounter(int numIncrements) {
    for (int i = 0; i < numIncrements; ++i) {
        counter.fetch_add(1);  // Inkrementace hodnoty atomicky
    }
}

int main() {
    std::thread t1(incrementCounter, 1000000);
    std::thread t2(incrementCounter, 1000000);

    t1.join();
    t2.join();

    std::cout << "Counter value: " << counter.load() << std::endl;

    return 0;
}

```

---

### Komplikace v paralelním programování

#### Souběh (race condition)

**Race Condition** nastává, když dva nebo více vláken přistupuje k sdílenému prostředku (e.g. proměnná) zároveň a alespoň jedno z těchto vláken provádí zápis.

![[Pasted image 20230827205153.png | center | 400]]

#### Uváznutí (deadlock)

**Deadlock** je stav ve kterém program nemůže pokračovat, protože se různá vlákna navzájem blokují. K deadlocku obvykle dojde, pokud se více vláken pokusí zamknout stejné mutexy v různém pořadí!

![[Pasted image 20230827200441.png| center | 300]]

![[Pasted image 20230827200922.png|center]]

#### Iluze sdílení (false sharing)

**False sharing** nastává, když nekolik vláken procesů přistupuje k různým proměnným, které jsou umístěny na stejném cache řádku. I když se tyto proměnné nijak nesdílí, sdílení cache řádku způsobuje neefektivní komunikaci mezi vlákny a snižuje výkon.

![[Pasted image 20230827205420.png | center | 400]]

---

### Podpora paralelního programování v OpenMP 

#### Sériově-paralelní model uspořádání vláken (fork-join)

Model **fork-join**: program se nejprve vykonává sekvenčně (jedno vlákno), a pokud se narazí na direktivu pro paralelizaci, vlákna se "rozdělí" (fork) a vykonávají přidělené části kódu paralelně. Po dokončení paralelní části se vlákna "sloučí" (join) a program pokračuje sekvenčně.

![[Pasted image 20230828095503.png | center | 400]]

#### Paralelizovatelná úloha (task region)

Umožňuje vytvoření paralelního regionu (pomoci [[#Parallel | #pragma omp parallel]]), kde se úlohy mohou vykonávat paralelně. V rámci tohoto regionu lze použít direktivu [[#Task|#pragma omp task]] pro vytváření samostatných úloh (tasků), které mohou být vykonávány nezávisle na sobě vlákny v rámci paralelního regionu.

#### Různé implementace specifikace

#todo

#### Direktivy 

##### Parallel

`#pragma omp parallel` označuje začátek paralelního regionu a vytváří **[[#Threadpool a fronta úkolů|thread pool]]** (nebo "user-level threads"), které budou provádět paralelní úlohy. Každé vlákno v pool vykonává kopii kódu v rámci paralelního regionu.
- na zacatek je počet vláken nastaven na základě dostupného hardwaru vláken, ale lze jej ovlivnit proměnnými prostředí `OMP_NUM_THREADS`, modifikátorem `num_threads(2)` nebo funkce `omp_set_num_threads()`

##### For

`#pragma omp for` se používá k paralelnímu rozdělení iterací smyčky mezi vlákna v rámci paralelního regionu. Každé vlákno dostane přidělenou část iterací a vykoná je nezávisle na sobě.

EX:

```
...
    #pragma omp parallel for
    for (int i = 0; i < vectorSize; ++i) {
        resultVector[i] = vectorA[i] + vectorB[i];
    }

    // Výsledek
    std::cout << "Result vector: ";
    for (int i = 0; i < vectorSize; ++i) {
        std::cout << resultVector[i] << " ";
    }
    return 0;
}
```

##### Section

Pomocí `#pragma omp section` v rámci paralelního regionu můžeme určit, který úsek kódu bude vykonáván paralelně.

EX:

- `#pragma omp parallel sections` je k vytvoření paralelního regionu, ve kterém chceme zpracovat sekce kódu
- každá sekce je označena `#pragma omp section`

```
int main() {
    #pragma omp parallel sections
    {
        #pragma omp section
        {
            std::cout << "Section A executed by thread: "; 
            std::cout << omp_get_thread_num() << std::endl;
        }

        #pragma omp section
        {
            std::cout << "Section B executed by thread: ";
            std::cout << omp_get_thread_num() << std::endl;
        }
    }
    return 0;
}

```

##### Task

`#pragma omp task` se používá k vytvoření samostatné úlohy (tasku) v rámci paralelního regionu. Úlohy mohou být v rámci paralelní oblasti prováděny vlákny nezávisle.

EX:

- `#pragma omp parallel` pro vytvoření paralelního regionu
- `#pragma omp single` zajistí, že úkoly budou vytvořeny pouze jednou, nezávisle na počtu vláken
- `#pragma omp task` k označení úloh
- `#pragma omp taskwait` zajistí, že se všechny úlohy dokončí před pokračováním

```
int main() {
    #pragma omp parallel
    {
        #pragma omp single
        {
            #pragma omp task
            taskA();

            #pragma omp task
            taskB();
            
            #pragma omp taskwait
        }
    }
    return 0;
}
```

##### Barrier

`#pragma omp barrier` slouží k synchronizaci vláken v rámci paralelního regionu. Před pokračováním dále se vlákna zastaví na bariéře, dokud všechna vlákna nepřijdou na tento bod.

EX:

##### Critical

`#pragma omp critical` zajišťuje, že pouze jedno vlákno může současně provádět kritickou sekci. Tím se minimalizuje [[#Souběh (race condition)]] při přístupu ke sdíleným datům.

==Jaky je rozil od pouziti atomic promen==?
- `#pragma omp critical`: každé vlákno bude postupně získávat a uvolňovat zámek, což zajišťuje, že inkrementace bude probíhat atomicky (výhradně jedno vlákno najednou). To může způsobit zpomalení, protože vlákna musí čekat na získání zámku.
- `std::atomic`: atomické operace jsou navrženy tak, aby byly vykonávány atomicky bez nutnosti zámků nebo kritických sekcí.

EX:

```
int main() {
    int sharedValue = 0;

    #pragma omp parallel
    {
        // Každé vlákno přidá hodnotu do sdílené proměnné
        #pragma omp critical
        sharedValue += 1;

        // Synchronizace všech vláken na bariéře
        #pragma omp barrier

        // Výpis sdílené hodnoty
        std::cout << "Thread " << omp_get_thread_num();
        std::cout << " sharedValue: " << sharedValue << std::endl;
    }
    return 0;
}
```

##### Atomic

`#pragma omp atomic` se používá k [[#Atomic|atomickému]] provádění operací nad sdílenými proměnnými. Zajišťuje, že operace nad proměnnou se provedou nedělitelně a minimalizuje [[#Souběh (race condition)|race condition]].

==Jaky je rozdil atomic promen od omp atomic?==
- `#pragma omp atomic` je specifická direktiva OpenMP, která funguje v rámci kódu paralelizovaného pomocí OpenMP
- `std::atomic` je součástí C++ standardní knihovny a funguje v celém kódu, bez ohledu na to, zda je paralelizován pomocí OpenMP nebo jiných technologií

EX:

```
int main() {
    int counter = 0;

    #pragma omp parallel for
    for (int i = 0; i < 1000000; ++i) {
        #pragma omp atomic
        counter++;
    }
    
    std::cout << "Counter value: " << counter << std::endl;
    return 0;
}
```

---

### Techniky dekompozice programu

#### Rozdělení práce

##### Statické rozdělení

Při **statickém rozdělení práce** se úlohy předem rozdělí mezi vlákna. Každé vlákno dostává pevnou část úloh a pracuje na nich nezávisle na ostatních.

- ne nutně v době kompilace
- jednou přidělíme vláknům úkoly a toto přidělení je neměnné
- kdy nám statické rozdělení pomůže?
	- všechny úkoly trvají (přibližně) stejně dlouho
	- každý úkol může trvat různě dlouho, ale víme předem očekávanou dobu trvání!

![[Pasted image 20230828112251.png|center|400]]

##### Dynamické rozdělení

Při **dynamickém rozdělení práce** se úlohy rozdělují dynamicky v průběhu vykonávání programu. Úlohy jsou přiřazeny volným vláknům nebo procesům podle jejich dostupnosti.

Klíčové cíle:
- [[#Balancování a závislosti (dependencies)|Vybalancování]] – aby každé vlákno vykonávalo (přibližně) stejnou práci
- Minimalizace komunikace – aby vlákna na sebe nemusely čekat
- Minimalizace duplicitní/zbytečné práce – aby vlákna nepočítali něco, co by se nepočítalo bez paralelizace
- Program přiděluje úkoly dynamicky na základě aktuálního vytížení jednotlivých vláken -> [[#Threadpool a fronta úkolů]]

![[Pasted image 20230828112608.png|center|400]]

###### Problemy

Existence " kritické sekce" v systému: všechna vlákna používají stejnou frontu!

![[Pasted image 20230828113015.png|center|400]]

Co kdyby mělo každé vlákno vlastní frontu?
- Můžeme vytvořit vlastní frontu pro každé vlákno
- Vlákno vkládá a vybírá úkoly z vlastní fronty

![[Pasted image 20230828113122.png|center|400]]

Jak zabezpečíme [[#Balancování a závislosti (dependencies)|vybalancování]]?
- Když má vlákno prázdnou frontu, může "ukradnout" úkoly z jiné fronty!

![[Pasted image 20230828113222.png|center|400]]

#### Threadpool a fronta úkolů

#todo

#### Balancování a závislosti (dependencies)

Ideálně chceme, aby všechna vlákna/jádra pracovaly a skončily současně.  Pokud jsou některá vlákna nebo procesy rychlejší než ostatní, mohou dostávat více úloh a vykonávat je, zatímco pomalejší vlákna nebo procesy čekají na další úlohy.

![[Pasted image 20230828120152.png|center|500]]

`#pragma omp task` v kombinaci s `depend` a `out` umožňuje řídit **závislosti** mezi úlohami v OpenMP. Klauzule `depend` umožňuje specifikovat vstupní a výstupní závislosti mezi úlohami, zatímco klauzule `out` určuje, které hodnoty budou závislé.

EX:

V tomto příkladu máme dvě úlohy v rámci jednoho paralelního bloku:
- První úloha vypočítá dvojnásobek proměnné `data` a uloží jej do proměnné `result`. 
  Tato úloha má `out` závislost na proměnné `result`, což znamená, že další úloha, která bude závislá na `result`, nemůže začít, dokud první úloha neskončí.
- Druhá úloha vypočítá dvojnásobek hodnoty v proměnné `result` a vypíše jej. 
  Tato úloha má `in` závislost na proměnné `result`, což znamená, že nemůže začít, dokud první úloha neukončí svou činnost a nedoručí hodnotu `result`.

```
int main() {
    int result = 0;
    int data = 5;

    #pragma omp parallel
    {
        #pragma omp single
        {
            #pragma omp task depend(out:result)  // Výstupní závislost: result
            result = data * 2;

            #pragma omp task depend(in:result)   // Vstupní závislost: result
            {
                int doubledResult = result * 2;
                std::cout << "Doubled result: " << doubledResult << std::endl;
            }
        }
    }

    return 0;
}
```

#### Příklady z řazení

##### Quick sort

**Quicksort** je algoritmus řazení, který využívá techniky rozděl a panuj. Pro dekompozici Quicksortu se používá následující postup:

- Vybere se prvek ze seznamu, který se nazývá pivot.
- Seznam se rozdělí na dvě části: jednu s prvky menšími než pivot a druhou s prvky většími.
- Obě části se řadí rekurzivně pomocí stejného postupu (rozdělení a řazení).
- Výsledné části se sloučí dohromady.

Tento proces je aplikován rekurzivně na každou získanou část, dokud nejsou všechny části setříděny. Jedná se o příklad **statického rozdělení práce**, protože každé rekurzivní volání pracuje s konkrétní částí seznamu!

EX1:

V tomto příkladu je paralelizace úspěšná díky tomu, že pokaždé, když rozdělíme část pole pomocí pivotu, ponecháme první polovinu pro hlavní vlákno a druhou polovinu pro další vlákno.

- Klauzuli `shared(vector_to_sort)` ke každé úloze, abychom sdíleli pole `vector_to_sort` mezi všemi úlohami.
- Klauzuli `firstprivate(from, part2)` ke každé úloze. To znamená, že každá úloha má svou vlastní kopii `from` a `part2`. Tím se zajišťuje, že úlohy nebudou sdílet stejné promene.

![[Pasted image 20230828122642.png|center]]

EX2:

Nezapomínáme však na způsob použití [[#Task]]. Pokusíme se pomocí nich rozdělit úlohu na jednotlivé podúkoly!

```
void quickSort(std::vector<int>& arr, int low, int high) {
    if (low < high) {
	    // virtualni metoda, ktera vrati odpovidajici cast pole
        int pi = partition(arr, low, high); 

        #pragma omp task
        quickSort(arr, low, pi - 1);
        
        #pragma omp task
        quickSort(arr, pi + 1, high);
    }
}

int main() {
    std::vector<int> data = {12, 11, 13, 5, 6, 7};

    #pragma omp parallel
    {
        #pragma omp single
        quickSort(data, 0, data.size() - 1);
    }
	
	// print array bla-bla
	...
    return 0;
}
```


##### Merge sort

**Mergesort** je také algoritmus řazení, který používá techniku rozděl a panuj. Při dekompozici Mergesortu se používá následující postup:

- Seznam se rozdělí na poloviny, dokud nezbudou jednotlivé prvky nebo podseznamy o velikosti 1.
- Jednotlivé prvky nebo podseznamy se následně slévají dohromady pomocí procesu merge
- Merge spojuje dvě setříděné části seznamu do jednoho setříděného seznamu
  
Postup se opakuje, dokud se seznamy nedoslévají dohromady, a výsledkem je setříděný seznam. Při Mergesortu se v každém kroku provádí paralelní slévání dvou setříděných částí seznamu. Tímto způsobem lze využít **paralelní rozdělení práce** pro různé části seznamu.

EX:

![[Pasted image 20230828125319.png]]

#### Příklady z numerické lineární algebry a strojového učení

##### Násobení matice vektorem

Chceme vypočítat vektor $y = Ax$. Při dekompozici násobení matice vektorem se často používá **paralelní rozdělení práce**, kde jednotlivé části vektoru $x$ jsou přiřazeny k různým vláknům. Každé vlákno poté vypočítá část výsledného vektoru $y$, která odpovídá přidělené části vektoru $x$. Nakonec se výsledné části sloučí dohromady.

EX:

Představme si následující matici. Jak je zřejmé, paralelizace je možná buď podle řádků, nebo podle sloupců. 

![[Pasted image 20230828160051.png|center|400]]

Podívejme se na první případ. V závislosti na řádcích budou naše vlákna provádět počítání pro každou buňku finalniho vektoru.  Jaké operace by měla vlákna v tomto případě provádět? 
1. Pro každý řádek proveďte následující operace (for)
2. Násobení každého prvku řádku každým prvkem vektoru (for)
3. Sčítání všech součinů do výsledné hodnoty políčka (for)

Závěr: n-dotazů na řádek + n-dotazů na vektor + součet
Při tomto přístupu bude obtížné algoritmus vektorizovat.
==Pro úspěšnou vektorizaci je důležité, aby se při provádění operace na různých prvcích matice a vektoru nemusela provádět žádná složitá podmíněná větvení.==

![[Pasted image 20230828160434.png|center|400]]

Co kdybychom to zkusili polde sloupcu? Vzpomeňme si na pravidlo násobení matice vektorem a přeskočme popis všech operací a přejděme rovnou k závěru: n dotazů na sloupec, pouze 1 dotaz na vektor a sčítání ve vnější smyčce! To už je jiná věc!

![[Pasted image 20230828160145.png|center|500]]

##### Násobení dvou matic

1. **Rozdělení bloků**: Matice A a B jsou rozděleny na menší bloky. Tyto bloky mohou mít například velikost 4x4 nebo 8x8 prvků. Tím se zajišťuje, že budoucnosti vektorové instrukce budou moci pracovat s větším počtem prvků najednou.
2. **Paralelní výpočet**: Pro každý blok matice C (výsledkové matice) se provádí vektorizovaný výpočet, který násobí odpovídající bloky matice A a B. Tento krok umožňuje vektorovým instrukcím provádět násobení na více prvcích současně.
3. **Sčítání výsledků**: Výsledky násobení jednotlivých bloků jsou následně sečítány do výsledkové matice C.

![[Pasted image 20230828163914.png]]

##### řešení systému lineárních rovnic (GEM)

Dalším typickým úkolem je řešení soustavy lineárních rovnic, kde lze využít Gaussovu eliminaci!

Závislosti: Po výběru pivota se změní všechny hodnoty pro řádky a sloupce větší než pozice pivota!

#todo

---

## 2. Distribuované výpočty/systémy

