## Obsáh:
- [[#1. Rastrový zobrazovací řetězec, jeho fixní a programovatelné bloky|Rastrový zobrazovací řetězec, jeho fixní a programovatelné bloky]] 
- [[#2. Programování pomocí shaderů|Programování pomocí shaderů]]
- [[#3. Typické transformace a jejich reprezentace|Typické transformace a jejich reprezentace]]
- [[#4. Osvětlovací model|Osvětlovacı́ model]]
- [[#5. Základní parametrické křivky|Základnı́ parametrické křivky]]

---

## 1. Rastrový zobrazovací řetězec, <br>jeho fixní a programovatelné bloky
### Logické bloky v zobrazovacím řetězci a jejich funkci.
**Vertex Shader (nebo VS)** - program v GLSL používaný ke zpracování každého vrcholu
- VS přijímá souřadnice a atributy jednoho vrcholu + uniformy
- počítá:  ^cb27dd
	- transformace vrcholů (pozice), 
	- transformace normál a normalizace, 
	- transformace souřadnic textury (uv), 
	- per-vertex osvětlení, 
	- upravuje atributy spojené s každým vrcholem

**Fragment Shader (FS)** - program v GLSL používaný ke zpracování každého fragmentu
- vypočítá barvu a hloubku **fragmentu** (vzorek z oblasti jednoho pixelu rasterizovaného primitiva - interpolovaná barva, hloubka, pozice okna atd.) ^413d86
- odesílá vypočítané hodnoty do framebuffer (info o jeden snímek)
- počítá:  ^bc67ec
	- přístup k texturám a aplikace, 
	- mlha (fog), 
	- součet barev (jako v phongu), 
	- per-pixel osvětlení, 
	- operace s interpolovanými hodnotami

**Programmable Pipeline** - Každá aplikace musí definovat dvě fáze shaderu

![[Pasted image 20230806162503.png]]

**OpenGL Pipeline** - programovatelné bloky jsou součástí celkového grafického pipelinu

![[Pasted image 20230806162801.png]]

**Podrobnosti o jednotlivých krocích** (bloky ==této barvy== nejsou na diagramu):
- *Vertex buffer* → OpenGL kopíruje datový blok (blok vrcholů - VAO) do GPU
- *VS (spuštěn pro každý vertex)*
	- → přijímá souřadnice a atributy jednoho vrcholu + jednotné proměnné (sdílené přes primitivy)
	- → počítá pozici vrcholu ve 3D ořezové souřadnice a další proměnné ([[#^cb27dd]])
- ==*Fáze tessellation*== → transformace **concave polygons** do **convex polygons** 

   ![center |400](https://lh5.googleusercontent.com/kQ7gv89c0W8sJAW7k0tM7ZRQLULkObbiinsz5AbtQlvTxHTMiIybpqWz6U-gnHIzcMEpQzeilmi9Q94ZRDo9LSj9Dk7bK7eYG0VBY0sazx9QLPEKwMOGo8Nmzltf_B1hYgxyKL_mY2IX7h6qATFWmCg)
   
- *Primitive Assembly* → čeká, dokud neobdrží tři vrcholů a pak sestrojí jeden trojúhelník, pak předává trojúhelníky (trojice vrcholů) do rasterizéru
- *Rasterization* → generuje fragmenty, které pokrývají trojúhelník a leží ve 2D okně (jednoduše řečeno, je to dělí 𝑤 a odřízne okrajem okna), vypočítá 2D polohu každého fragmentu a interpoluje proměnné (měnící se, varying) z hodnot přiřazených vrcholům trojúhelníku
- *FS (spuštěn pro každý fragment)*
	- → vypočítá barvu a hloubku fragmentu
	- → odešle vypočtené hodnoty do framebuffer (ukládá informace o jednom snímku, [[#^bc67ec]])
- *Frame buffer* → fragment se stane pixelem na obrazovce pouze tehdy, pokud projde všemi testy fragmentů (e.g. scissor, stencil, depth)

**Závěr:** Drawing Loop (glutDisplayFunc):
1. vyčistit okno
	```
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)
	```
2. bind shader program
	```
	glUseProgram(program)
	```
3. bind vstupy VS
	```
	glBindVertexArray(VAO)
	```
4. nastavit jednotné proměnné (materiál, transformace,…)
	```
	glUniform(...)
	```
5. začít kreslit
	```
	glDrawElements(typ, kolik, data typ, 0) 
	```
6. unbind VAO
	```
	glBindVertexArray(0); 
	```
7. unbind shader program
	```
	glUseProgram(0)
	```

---
### Perspektivně správná interpolace.
FS ve výchozím nastavení přijímá interpolované hodnoty výstupů VS!

Qualifier | Význam | Demo
----- | ----- | -----
<span class="term">flat</span> | bez interpolace, hodnota z prvního nebo posledního vrcholu (zalezi na glProvokingVertex) | ![[Pasted image 20230809182706.png]]
<span class="term">noperspective (affine)</span> | lineární interpolace | ![[Pasted image 20230809182728.png]]
<span class="term">smooth</span> | perspektivně správná interpolace (by default) | ![[Pasted image 20230809182739.png]]

- <span class="term">flat</span> → výstup shaderu pro každý vrchol primitivu zůstává konstantní na celém povrchu primitivu. Tento typ interpolace se často používá pro hodnoty, které jsou konstantní pro každý primitiv, jako jsou barvy nebo vlastnosti materiálu.
- <span class="term">noperspective</span> → typ interpolace, který považuje varying atributy za lineárně interpolované v prostoru obrazovky, nikoli za perspektivně opravené. Tento typ interpolace se používá pro atributy, které by neměly být ovlivněny perspektivním zkreslením, jako jsou screen-aligned textury nebo screen-space effects.
- <span class="term">smooth</span> → plynule interpoluje hodnoty přes primitivní povrch, přičemž bere v úvahu perspektivní zkreslení způsobené 3D projekcí. Tento typ interpolace se běžně používá pro změnu atributů, jako jsou souřadnice textur nebo normály vrcholů, kde jsou požadovány hladké gradienty.

EX: 
```
#version 330 core

in vec3 vertexPosition;
in vec3 vertexColor;
in vec2 vertexTexCoord;

flat out vec3 interpolatedColorFlat;
smooth out vec2 interpolatedTexCoordSmooth;
noperspective out float interpolatedCustomAttributeNoPerspective;

// Example transformation matrices
uniform mat4 modelMatrix;
uniform mat4 viewMatrix;
uniform mat4 projectionMatrix;

void main() {
    gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(vertexPosition, 1.0);

    // Flat interpolation for color (remains constant across the primitive)
    interpolatedColorFlat = vertexColor;

    // Smooth interpolation for texture coordinates
    interpolatedTexCoordSmooth = vertexTexCoord;

    // No interpolation (NoPerspective) for a custom attribute
    interpolatedCustomAttributeNoPerspective = vertexPosition.y;
}
```
---
### Vrstvy obrazové paměti a jejich účel (použití v bloku operací a testů).
#todo 

---
### Textury - zobrazování (způsoby kombinace s osvětlením), filtrování, mip-mapping, mapa prostředí.

#### Zobrazování textur
**Textura** - je vlastnost povrchu, která popisuje jemné detaily povrchu objektu a je důležitá pro vnímání struktury, barvy a kvality povrchu.
- textura se používá ke zvýšení vizuální kvality vykreslovaného objektu (jednoduchý objekt vypadá složitěji)
- nebo k nahrazení některých jemných detailů, které jsou obvykle příliš složité pro geometrické modelování
#todo_způsoby_kombinacer_s_osvětlením

#### Mip-mapping
Problém **minifikace** vzniká v důsledku rozdílu rozlišení mezi původní texturou a velikostí, ve které je vykreslena na obrazovce. Když je textura zobrazena v menší velikosti, než je její původní rozlišení, musí GPU vzorkovat a mapovat textury (prvky textury) z původní textury na menší pixely na obrazovce.

**Mipmap** - "pyramida" textur se snižujícím se rozlišením. Používá rozlišení, které odpovídá otisku (footprint) na obrazovce. Tím se zabrání třepení (shimmering) textury při "přeskakování" z texelu na texel v textuře.

**Mip-mapping** pomáhá snižovat aliasing a zlepšuje výkon tím, že snižuje počet texturovacích operací pro vzdálené objekty. OpenGL → <span class="term">glGenerateMipmap(GLenum target)</span>


**Ok, ale jak správně vybrat mipmapu?**

Úroveň mipmap <span class="maths">d</span> je zvolena podle velikosti pixelu promítnutého do prostoru textury (pixel je promítnut do úrovně 0 pokrývající <span class="maths">pu</span> texely ve směru *u* a <span class="maths">pv</span> texely ve směru *v*.
Formula: <span class="maths">d ~ log2(max(pu, pv))</span>
==*Na nejvyšší úrovni je textura 1 pixel!*==

![[Pasted image 20230806181538.png | center | 300]]

#### Filtrovani

Typy filtrování | Popis | Demo
----- | ----- | ----- 
<span class="term">GL_NEAREST</span> | prvek textury, který je nejblíže zadaným souřadnicím textury (rychlý, aliasing) | ![[Pasted image 20230809184418.png]]
<span class="term">GL_LINEAR</span> | vážený průměr textury 2x2 prvky, které jsou nejblíže k zadané souřadnice textury ( pomalejší, ale plynulé) | ![[Pasted image 20230809184428.png]]

Kombinace

Metody filtrování | Popis 
----- | ----- 
<span class="term">GL_NEAREST_MIPMAP_NEAREST</span> | používá GL_NEAREST v nejbližší mipmapě 
<span class="term">GL_NEAREST_MIPMAP_LINEAR</span>  | používá GL_NEAREST ve dvou nejbližších mipmapách (výsledek je průměrem těchto dvou hodnot) 
<span class="term">GL_LINEAR_MIPMAP_NEAREST</span> | používá GL_LINEAR v nejbližší mipmapě 
<span class="term">GL_LINEAR_MIPMAP_LINEAR</span> | používá GL_LINEAR ve dvou nejbližších mipmapách (výsledek je průměrem těchto dvou hodnot) 

![[Pasted image 20230806180105.png| center |500]]


---
#### Mapa prostředí
Než si rozebereme použití mapování prostředí, musíme si rozebrat metody mapování textur.

##### Texture mapping

- <span class="term">forward (direct)</span> → mapování obrazových bodů na polygon, může vytvářet díry a překrývání v obrázcích textur! Textura se vypočítá na základě polohy povrchu nebo souřadnic.
   Princip: <span class="maths">(uv [surface parametrization] → object space [projection] → screen space)</span>
- <span class="term">inverse</span> → vypočítáme souřadnice textury pro daný bod na povrchu objektu vypočítané ze souřadnic obrazovky! Textura se vypočítá spíše na základě směru nebo orientace povrchu.
  Princip: <span class="maths">(uv ← object space [pixel screen coord → point in world coords → texure parametrix coords] ← screen space)</span>

**Environment mapping** je příkladem mapování textur, které spadá do kategorie <span class="term">inverzního</span> mapování textur. Prostředí je vykresleno na objekt prostředí (typicky koule, krabice) a uloženo v texturách, které jsou s ním spojeny.  Render objekt je umístěn uvnitř objektu prostředí. Odražený paprsek se protne s tímto obklopujícím tvarem (enclosing shape).

![[Pasted image 20230806185030.png|center|300]]

Při sférickém mapování se k určení odpovídajícího směru na mapě prostředí používá normála povrchu každého bodu na objektu. Normála povrchu funguje jako 3D vektor směřující ven z povrchu objektu.

Směry při mapování:
- Spherical Mapping: <span class="maths">Dir = normalize(N)</span>, kde N je surface normal
- Cube Mapping: <span class="maths">Dir = V - 2 × dot(V, N) × N</span>, kde N je surface normal, V je view dir.

Mapa prostředí se pak vzorkuje ve směru určeném normálou povrchu, aby se získala informace o barvě pro daný bod na povrchu objektu.

==*Existuje několik dalších různých tvarů, které lze použít k počítání. Například válec. Nakonec použijte co nejpodobnější tvar, aby minimalizovat zkreslení!*==

![[Pasted image 20230806191619.png|500]]

---
## 2. Programování pomocí shaderů
### Základní datové typy v GLSL (skalár, vektor, matice, sampler).

1. <span class="term">Scalar</span> představuje jednu hodnotu. Může to být buď **int**, nebo **float**. Skaláry se obvykle používají pro ukládání jednoduchých číselných dat, jako jsou barevné složky nebo vzdálenosti.
2. <span class="term">Vektor</span> je uspořádaná množina skalárů. Může to být 2D, 3D nebo 4D vektor, označovaný jako **vec2**, **vec3** a **vec4**. Vektory se běžně používají k ukládání geometrických atributů, jako jsou positions, directions nebo hodnoty barev. Ke každé složce vektoru lze přistupovat samostatně( .x, .y, .z).
3. <span class="term">Matice</span> je pole skalárů. Může mít rozměry 2x2, 3x3 nebo 4x4, označované jako **mat2**, **mat3** a **mat4**. Matice se používaji k reprezentaci transformací a souřadnicových systémů. Mohou uchovávat operace otáčení, translace, škálování a promítání.
4. *[Sampler](https://docs.google.com/document/d/1d5adPThJSL9dYJYZN4d704F9GSUS0-dS4QG-gfAVh8E/edit#bookmark=id.73bz3aq0xxzn)* je datový typ používaný pro přístup k texturám. Představuje odkaz na objekt textury a poskytuje metody pro vzorkování (sampling) textur (pixelů textury). Existují různé typy samplerů, například **sampler1D**, **sampler2D**, **sampler3D** a **samplerCube**, které odpovídají 1D, 2D, 3D a krychlové mapě textur. Samplery umožňují shaderovým programům získávat informace o barvách z textur a provádět operace, jako je filtrování nebo  mip-mapování.
#### Wtf is n-dimensional sampler?

1. <span class="term">sampler1D</span> - Umožňuje přístup k jedné složce textury na konkrétní 1D souřadnici.
	- 1D textura je lineární textura s jednou řadou texelů. Často se používá k ukládání dat podél 1D osy, například barev podél gradientu nebo hodnot výšky terénu.
2. <span class="term">sampler2D</span> - Umožňuje přístup k více složkám (např. barva a alfa) textury na konkrétní 2D souřadnici.
	- 2D textura je tradiční textura s šířkou i výškou, která se běžně používá k zobrazení obrázků, vzorů nebo detailů povrchu.
3. <span class="term">sampler2DRect</span> - Obdélníkové textury nemusí mít mocniny dvou rozměrů jako tradiční textury.
4. <span class="term">sampler3D</span> - Umožňuje přístup k více složkám textury na konkrétní 3D souřadnici.
	- 3D textura je rozšíření 2D textury o další rozměr hloubky. Používá se k ukládání volumetric dat, jako jsou clouds, smoke.

![[Pasted image 20230806201413.png| center | 500]]
#### Další příklady sampleru

1. <span class="term">samplerCube</span> - Slouží k přístupu k texturám krychlové mapy (šestistranné textury používané pro mapování prostředí, skybox).
2. <span class="term">samplerNDArray, samplerCubeArray</span> - Slouží k přístupu k polím textur, což jsou kolekce vícenásobných ND nebo krychlových map textur. Umožňuje přístup ke složkám textur v poli na základě dodatečného indexu pole.
3. <span class="term">samplerBuffer</span> - Slouží k přístupu k texturám bufferu (textures backed by buffer objects).

---
### Rozdíl mezi proměnnými in a uniform
- <span class="term">in</span> proměnné: předávání data z jedné fáze shaderu do druhé, readonly, používaji se k předávání atributů, jako jsou pozice vrcholů, normály, souřadnice textur nebo interpolované hodnoty mezi VS a FS.
```
// Vertex Shader  
in vec3 position; // vertex position  
void main() {  
  // ...   
}
```

- <span class="term">uniform</span> proměnné: inicializuje je aplikace před spuštěním programu shader, konstantní při všech vyvoláních shader programu, jsou sdíleny mezi všemi zpracovanými vrcholy nebo fragmenty, používají se k ukládání globálních dat, která musí být konzistentní ve všech vyvoláních shaderu, jako jsou transformační matice, vzorkovače textur nebo vlastnosti materiálů.
```
// Fragment Shader
uniform vec4 lightColor;  // light color
void main() {
    // ...
}
```

EX (c++):
```
// Retrieve the uniform variable location  
int lightColorLocation = glGetUniformLocation(shaderProgram, "lightColor");  

// Set the uniform variable value  
glUseProgram(shaderProgram);  
glUniform4f(lightColorLocation, 1.0f, 0.0f, 0.0f, 1.0f);  // Set light color to red
```
---
### Jak se přiřazují hodnoty proměnných z OpenGL do GLSL
**Postup:**
1. Získání umístění uniform 
2. Přiřazení hodnoty uniform (float / vec / matrix)
	- **float** → samotnou hodnotu
	  ```
	  void glUniform1f(	GLint location, GLfloat v0);
      ```
	- **vec** → ukazatel (value_ptr)
	  ```
	  void glUniform3fv(GLint location, GLsizei count, const GLfloat *value);
	  ```
	  
	- **matrix** → taky ukazatel 
	  ```
	  void glUniformMatrix4fv(GLint location, GLsizei count, GLboolean transpose, const GLfloat *value);
      ```
	  

EX (c++):
```
GLint myUniformLocation = glGetUniformLocation(shaderProgram, "myUniform");  
glUseProgram(shaderProgram);  

// float assign  
GLfloat value = 0.5f;  
glUniform1f(myUniformLocation, value); 

// vector assign  
glm::vec3 vectorValue(1.0f, 0.0f, 0.0f);  
glUniform3fv(myUniformLocation, 1, glm::value_ptr(vectorValue));

// matrix assign  
glm::mat3 matrixValue(1.0f);  
glUniformMatrix4fv(myUniformLocation, 1, GL_FALSE, glm::value_ptr(matrixValue));
```
---
## 3. Typické transformace a jejich reprezentace

### Lineární a afinní transformace a jejich maticová reprezentace, homogenní souřadnice

#### Homogenní & Kartézské souřadnice

Materiál, na kterém je tato část založena, si můžete přečíst [zde](https://wordsandbuttons.online/interactive_guide_to_homogeneous_coordinates.html).

V kartézském souřadném systému je bod v prostoru určen trojicí čísel <span class="maths">(x<sub>c</sub>, y<sub>c</sub>, z<sub>c</sub>)</span>. 
V homogenních souřadnicích je bod v prostoru určen 4 čísly <span class="maths">(x<sub>h</sub>, y<sub>h</sub>, z<sub>h</sub>, w)</span>.

- Každý kartézský bod lze získat z homogenní trojice právě takto: 
  <span class="maths">x<sub>c</sub> = x<sub>h</sub> / w</span> (stejne pro y, z). 

- Chcete-li převést bod z kartézských do homogenních souřadnic, můžete jednoduše říci: 
  <span class="maths">x<sub>h</sub> = x<sub>c</sub>, y<sub>h</sub> = y<sub>c</sub>, w = 1</span>.
	- Pro homogenní souřadnice platí jedno pravidlo:
	  (2.08, 1.96, 2) = (0.52, 0.49, 0.5) = (1040, 980, 1000)
	- Čím menší je <font color=#d7992>w</font>, tím dále se bod v kartézských souřadnicích "vzdaluje" od nuly. 
	  ==*Proto <font color=#d7992>w = 0</font> simuluje nekonečno*==.
  

**Závěr**: Kartézské souřadnice jsou obvykle jen první tří homogenní souřadnice dělené čtvrtým. Pokud je tedy čtvrtá souřadnice 1, jsou homogenní souřadnice stejné jako kartézské.

---

#### Lineární transformace

Třídu lineárních transformací lze popsat pomocí matic - ve 3D prostoru matice 3x3. Je to proto, že lineární transformaci lze přesně specifikovat tak, že nám řekne její vliv na bázové vektory.

![center|150](https://lh6.googleusercontent.com/S1MRnGXMOS-ah13rlSatzeRWoPNDeqPZeuBRTCw2v8e0Uv_TmF2SW5ttr7Xr6fwzf0MzbU1K3woAMJ4Pxa-wJgqHsWKJM0lNu93rVBEUlmrsyap2PtJc0LYacjYTMxjoYlhPO3kyhgjPX43PYKEQVRM)

Matice | Znaceni | Pouziti
------ | ----- | -----
<span class="term">Scale</span> | ![center\|200](https://lh6.googleusercontent.com/dnZiJ1A1L-VG3o83iMfFAGYgldtqOV797TWTVvmlWvHk5Wx18lVl2E68atOH8Gy5Zhq1XL0PGd2mhIeybG2kA1ehYa0PB7Ey0i-V3ufiOSfo0DViJmfFSay7UUfgWdDfvs20puTDCNNswic2uB7-UCg) | ![\|200](https://lh3.googleusercontent.com/kqLNtDyWVCVJgxEeeNfCmZd5g_gyF12_78suF5jrarKaRpmGNEN-BDBDgcB_2UXctO-1Bij2vfdxCFduIEhiKyMflJtt2VVua8k873dHbece7HuuMKCewFrl9pMmmWFderqhhLXhxLrGNM4mW8Ew84E) 
<span class="term">Translation</span> | ![center\|200](https://lh6.googleusercontent.com/5BCWThgd6_jy8DAMRKFaEhH1H2xnKSskeUThPHm9pvKHUgKUNYi4-iekOgWvv18vKDt2X95dV4gILJOIyjQBcllsgql0w3OkYBwJAEvNgYljvsyqlwkVnj37EfzxH83KYweIDRpn19JInXCFwMAPczI) | ![\|200](https://lh5.googleusercontent.com/CsQHqTce_z3IjRGfKjNk0PN-8FowukSKixBz5GgRvSkLll5B4SgSHT9GQpcS3X8iWW0eTnu-xkpvM3LhrawrJZ4CuJHv3fJu0RsSRthqd4CRt-ICyq3g2A-sJE65XQd0JXs87cf-sgIG2bXkk7D9wRg) 
<span class="term">Rotation</span> | ![center\|200](https://lh4.googleusercontent.com/KOXamMbef27R6ecIHSY_buChMTKAWl9gft8WTqpMigPUV80iNHzPOy4ZZ1om69AuY9o8Gfb606OpyoFWnlIUxF8AQ5s4VSwS8INhfBhmsS5EO85Vd3H8GiJWdSf2xCDapXGL4UgvAOfNxDpdIVCxBY8) ![center\|200](https://lh6.googleusercontent.com/PRN62z-TvwXcv_WDB3LiboWN2gVMZQ6JDIoAPmK-8_XcjkeaxHnTZ8kIlYrd2e5j8x60GUBbSTgnihI99x4f0WoSECogU-EWpsoOegeGUVlyjOZt7_Kht42jdKW0nd3uxm-4eba-FPOL-QqfRUud2OU) ![center\|200](https://lh3.googleusercontent.com/SkbUO18lZpOtZXN-_5y9vYzkuYnJ2_leWb6AueeRYjQArsCtaUXYF2yz2Kg2eXTPvQzJajT7DGjQ6d0hxrTC5PAUcOco6iq8-QdZpA1wAPdl8aHAzNS0peU8eBzgI5g3ma18iK0QoPkhM_dMFPCZMhc)
==*Změna měřítka může být symetrické (stejná změna ve všech osách) nebo asymetrické (různá změna pro každou osu).*==

Jakékoli rotace ve 3D lze dosáhnout pomocí sekvence otáčení kolem os x, y a z (např. Eulerovy úhly). ==*Rotace ve 3D nejsou komutativní → pořadí rotace je proto důležité*== ([[#<font color= 85DCB>Gimbal lock</font>|Gimbal lock]] risk!).

---

#### Affinní
Libovolnou kombinaci posunu, rotace, změny měřítka/odrazu a smyku lze kombinovat v jediné afinní transformační matici 4x4!

![|150](https://lh6.googleusercontent.com/a_9cRy4IT0vpWKpNc-1si8tzsQ3Le0RTK7iODhLNly7QxdUPN5t-u4TYt8nTcxqNHGSnyBT-HUBputL0zd3ALIc34KedVx84FnL_A6Q3dKxNrNBuScAcseXDzQfCt75tlhdwJo4dQm735VPkGzm44rU)
 
**Proč je poslední řádek (0, 0, 0, 1)???**

Při násobení transformační matice vektorem slouží poslední řádek jako homogenní souřadnice výsledného transformovaného bodu. Jeho <font color="red">nastavením na hodnotu 1 zůstane výsledná homogenní souřadnice nezměněna a zachová se poloha bodu po transformace.</font>

Při práci s objekty jako jsou přímky, se často dostáváme do problémů. Dvě přímky mohou mít jeden průsečík, nebo také žádný, kuželosečky známe trojího druhu (elipsa, parabola, hyperbola) - ale přesto je trochu cítit, že speciální případy se od sebe příliš neliší. Kdybychom uměli pracovat s body v nekonečnu, mohli bychom například tvrdit, že dvě přímky mají vždy právě jeden průsečík. Z tohoto důvodu vznikla projektivní geometrie, která s nekonečnem nějak pracovat umí. Jeden ze způsobů, jak si projektivní plochu, neboli obyčejnou 2D rovinu s body v nekonečnu, představit, jsou právě homogenní souřadnice. <font color="red">Plochu, v které normálně pracujeme, umístíme rovnoběžně s rovinou xy na souřadnici w = 1. (viz obrázek).</font>

![](https://lh5.googleusercontent.com/x7Ph7iCfant9TJG6NIwT0J5pJ9-oDUrlQNA9QqrSzUAhwf4RalDQ5hnUN2H2yDdvw2eSnsEAnBlRe0ao4dDNGOCgciSysv9Q0u9ubvhIpgQAIJZ3fP5-zQj9EU571Ssl5MjCqPqAErR57HPMZk-mlBI)

Bod <font color=#d7992>V</font>, který má v rovině souřadnice <font color=#d7992>[ vx, vy ]</font>, má tedy homogenní protějšek (противополо́жность) v bodu <font color=#d7992>Vh = [ vx, vy, 1 ]</font>. Bod <font color=#d7992>Vh</font> si tedy můžeme představit i jako vektor; a je průsečík přímky v s rovinou <font color=#d7992>w = 1</font>.
	
![[Pasted image 20230808221823.png]]

V tomto modifikovaném případě by měřítkový faktor 2 v homogenní souřadnici <font color=#d7992>w</font> způsobil změnu hloubkové složky <font color=#d7992>Z</font>, čímž by se spolu s ostatními dimenzemi zavedlo nerovnoměrné měřítko ve směru <font color=#d7992>Z</font>. Toto již není afinní transformace, protože poslední řádek není <font color=#d7992>[0 0 0 1]</font>, což znamená, že transformace nezachovává homogenní souřadnice a perspektivní vlastnosti.

---
### Sestavení matice rotace podle jedné souřadné osy, škálování, nastavení kamery (lookAt) a záběru (viewport)

#### LookAt

![[Pasted image 20230905094201.png]]

#### ViewPort

The $s_x$ a $s_y$ parameters for this mapping are the coordinates of the lower, left corner of the viewport.

![[Pasted image 20230905094139.png]]

---
### Posloupnost souřadných systémů, kterými prochází vrchol, než získá souřadnice v rámci okna

![](https://lh4.googleusercontent.com/u-uzr5cil9KhghNpfaqkbFogSkGWiOfBFo3bVM-w3Re_oG28wjmkwiP0Qkhexv7JodIQTm40KtfTuGYatgy5KVdhjFhkfqAHNY512W6bR7UPl08OfA7odWV9LCKn4Ciz_oSlA1hWTeS1onzlHax9cDQ)

#### Pořadí násobení matic: opačné pořadí!

Ačkoli si tedy intuitivně myslíme, že translace by měly být použity jako první, v kontextu násobení matic a transformace vektorů prostřednictvím pipeline se transformace spojují v opačném pořadí. Proto se translace při afinních transformacích obvykle aplikují jako poslední.

Operace aplikované na jednotlivé vrcholy v pořadí výše uvedených souřadnic můžete vidět zde:

![[Pasted image 20230809110252.png|center|500]]

- **TRS (Modeling transformation)**:
	- Scaling Matrix (S): Mění velikost objektu ve světovém prostoru.
	- Rotation Matrix (R): Otáčí objekt kolem zadaného bodu nebo osy ve světovém prostoru.
	- Translation Matrix (T): Přesune objekt z jeho lokálního prostoru do nové pozice ve světovém prostoru.
- **PVM (Camera transformation + Projection transformation)**: 
	- Model Matrix (M): Translates the vertex to its position in the world space.
	- View Matrix (V): Rotates and translates the vertex to the camera space.
	- Projection Matrix (P): Projects the vertex to clip space.

EX:
```
v = (1, 0, 0, 1)

T * R * S * v = T * (R * (S * v))
              = T * (R * (3, 0, 0, 1))
              = T * (3, 0, 0, 1)
              = (5, 0, 0, 1)

P * V * M * v = P * V * (M * v)
              = P * V * (3, 0, 0, 1)
              = P * (3, 0, 0, 1)
              = (3, 0, 0, 1)
```

**Poznamka**: <span class="maths">PVM = MVP<sup>T</sup></span>!!

---
### Transformace vůči zadanému souřadnému systému

#todo 

---
### Matice rovnoběžného a perspektivního promítání

#### Matice rovnoběžného promítání
**(Orthographic projection just scales and shifts)**
![|400](https://lh3.googleusercontent.com/2JauY-_xGJ4oVjm8gdT8DhvbHdNglfzwMr5JlQATWik__Pxq6U1lXRmDgPjJeVaCrQWUnnSO4pz220r-nK0O_GAbAbr-y8-w69_GK-zRNB5oHRHYosQntb8npaUpbqmzPyym2bgF545vnfUH79r0ZPU)

Matice | Operace nad vektorem
----- | -----
![\|300](https://lh3.googleusercontent.com/0Oh922aOa5ffyrGK9cT1IDEG1hpzXzB9mpGasFvPzS5LoKvWv3tH79nY_m2bzleCS7w4Y4xepESXW8kjgKAl28w7ZqiiJLucpjdKnUQQBe9Q02oVKogL70zFen2k-ylMWqCbePiFdGROjiTevBXKa9k) | ![\|200](https://lh6.googleusercontent.com/juBbUMQl_rPj9cNN8xH8eQTmD7ZXh2NxO_YAjYFoBiuGes54g56nSIY6_SUAmLkwphQaHrgDmPXBi9irCfrX6vksgw9XLpABQolt5ZMy_ZjdSwOSo8W5P6rmOOGOHY3YUVftzAnF7ecjnK05ggeE9L0)

**Poznamky**: 
- Pouzivame -2, protoze box lezi v -z!!
- 2/.. pouzivame protoze checeme namapovat coords do \[-1, 1] (2 cisla)
   (kdybychom meli 3, tak range by se zmenil na \[-1.5, 1.5])
- Translace zahova centralizace projekce podle os (o kolik posunut objekt do near plane)

---

#### Matice perspektivního promítání
**(Perspective projection deforms the box to frustum)**
![|400](https://lh5.googleusercontent.com/epFetYVWKXN9DaRmOFdZYeG6yahgDj35arIPw7bF5P8eUwMgv7K2PfVsyrPORXYS3S7nz2SB_lb2SQ3pFe6Aze1xy18lnBhfrL5hNc_geewCkbGgJEvHRrEdXjXcfu4PTKaUckN2qiHMdjDIpouvp7U)

Matice | Operace nad vektorem
----- | -----
![\|300](https://lh5.googleusercontent.com/pswn6l39gOPBOdaFXUn2BZfYOwca3bToNf_y8yGlKSbJHYOZcgnwyYji6CEtQzoEWWsDrUIHSXGRh6XXybrnps9X23BmOdcGOduUEU7WtOcMSb6of_UB0C01O8aflLgyEMSNyCpyHfXnLFvH3KYnPNU) | ![\|250](https://lh3.googleusercontent.com/AnU7oCngtq5AEe6gkndX5vcmG9GGrCTOaya2fuX4Ly41zZCnWrZAVIHnLre09syFWgW0Tfsoj9LrYpuhAbsLjmT6xuWmJ6xDk1K7b-hfN4CCWp8nG3CakgirEPKWWAR7PiHAyzJ1eFtPokK4QUGCFcE)

**Poznamky**: 
- ==*Promítání obecně NENÍ afinní transformace (nema 0 0 0 1)!*==
- Důvodem hodnoty -1 je, že v homogenních souřadnicích je z-ová souřadnice oproti kartézským souřadnicím negována. Tato negace je nutná pro transformaci souřadnic do pravotočivé soustavy souřadnic.
- 2n/.. pouzivame protoze checeme namapovat na near plane

---
### Gimbal lock
**Gimbal lock** - je ztráta jednoho stupně volnosti v trojrozměrném mechanismu, ke které dochází, když jsou osy dvou ze tří poháněny do paralelní konfigurace, čímž se systém "uzamkne" do rotace v dvourozměrném prostoru.

![center|500](https://lh5.googleusercontent.com/2LNY1tHO80qR_uCyMeZv35MXY_3_E9ziS8gA_lH6hTkWS8tqcTPlkVMPJlIWCqxYm1fkwVRqBYnuNGIybK1VvICUPVnSO3sJV_AouVu19s3cWS0swAQZ-1BV4wRLZnjmaiWD44jchQn6WthBY9VfFs0)

#### How to avoid Gimbal lock?
1. Použití jedné rotace kolem libovolné osy místo posloupnosti rotací (Eulerovy úhly).
	e.g. rotace + apply → rotace + apply → rotace + apply → ...
1. Pro reprezentaci rotací použijte kvaterniony!
---
### Interpolace translace a rotace (kwaterniony, lerp a slerp)

#### Interpolation of Translation
Je plynulý přechod objektu z jedné polohy do druhé ve 3D prostoru. V tomto případě se obvykle používá lineární interpolace (lerp).

**LERP** je interpolační metoda, která počítá přechodné hodnoty mezi dvěma koncovými body na základě lineární funkce:  <span class="maths">lerp(P0, P1, t) = (1 - t)P0 + tP1</span>

![[Pasted image 20230808224735.png|center|400]]

---

#### Interpolation of Rotation
**Kvaternion** je matematická konstrukce používaná k vyjádření otáčení a orientace v trojrozměrném prostoru <span class="maths">(q0, q1, q2, q3)</span>, kde  <span class="maths">q1, q2, q3</span>  → vektory standartni bazi (E3, imaginarni cast) představuje osu otáčení <span class="maths">q0</span> →  skalar (= (q0, q), q – vektor), určuje velikost otáčení kolem této osy.

![[Pasted image 20230808224909.png|center|500]]

**Poznamka**: Cisty Quaternion (pure, ryze imaginarni) – <font color=#d7992>(0, q1, q2, q3)</font> oznaci vektor <font color=#d7992>(q1, q2, q3)</font>.
“Vektor je cisty Quaternion, ve kterem realna cast je nulova.”

**LERP a SLERP**: 

![[Pasted image 20230903152940.png]]

![center|](https://lh6.googleusercontent.com/YWWlRBSf8uMBgZ3t39-j_xE9fCu4gc8fqb-G3f0Q01z8YneVCACcRg-bytq1MdectYDjt7tBB7Bdne_1a9USSe1ArWySV0UKNV6zNlzCRUWlWW7k629uXooCRhBfO7Dh8_cTFxO_D4TrAKYMt2QiA8M)

---

## 4. Osvětlovací model
Před přímou analýzou všech složek osvětlení je nutné je označit. Na obrázku níže to jsou: **vrchol**, u kterého se bude počítat osvětlení, jeho **normálový vektor**, **vektor směru ke zdroji světla** od vrcholu a také **vektor odrazu** světla a **směru ke kameře** v prostoru. Všechny tyto údaje pomáhají vypočítat zjasnění pro každý vrchol a následně odpovídajícím způsobem pro celý povrch. 

![center](https://lh5.googleusercontent.com/A8oWMWqEvvfg7P-QRUSgNRsc2LNm95s_oZ82PJ7xro9zH0KJ1Ky_Fb7q_Ih3GoLZHv9o2U_u9Sml9Yv-kubKFufrIuokzTGbKyQgNPX77urc9f1YPyMGBHJ-9bHHuQl2Kr-bAk-KQpklgqQHuu4evdM)

K výpočtu osvětlení se také běžně používají parametry materiálu (např. textury, jako je roughness nebo metalness). Princip je stejný, ale v tomto případě se vlastnosti pro konkrétní vrchol vypočítají s ohledem na souřadnice textury.

Compute color in point (in camera space) separate rgb, separate components (ambient, diffuse, specular) ▪ in vertex • Use vectors 𝑛, Ԧ 𝑙 = (light pos−vertex pos), 𝑟 Ԧ , 𝑣 Ԧ = −vertex pos • Compute color – then interpolate over the triangle ▪ in fragment • Interpolate 𝑛, vertex 𝑝𝑜𝑠 in camera space • normalize(𝑛), • compute Ԧ 𝑙 = (light pos−vertex 𝑝𝑜𝑠), 𝑟 Ԧ , 𝑣 Ԧ = −vertex pos • Compute color in fragment

---

### Normálový vektor a jeho použití při výpočtu osvětlení v bodě.

Hlavním případem použití pro normály jsou výpočty osvětlení, kde musíte určit úhel (prakticky často kosinus) mezi normálou v daném bodě povrchu a směrem ke zdroji světla nebo ke kameře. 

==Podrobné použití normálového vektoru bude popsáno v části [[#Phongův osvětlovací model, vzorce jednotlivých složek.]]==

### Výpočet normály trojúhelníka.

**Proč vůbec počítat normálu trojúhelníku?**
- **Backface Culling** - Normálové vektory pomáhají určit orientaci polygonu vzhledem ke kameře.
- **[[#Flat|Flat Shading (per-face osvětlení)]]**

Při vytváření flat shadingu je často nutné použít přímo normálu pro obličej. Výpočet normály trojúhelníku se provádí následujícím způsobem: <span class="maths">faceN = norm(crossProd(CB, CA))</span>

Graficky | crossProd
----- | -----
![\|300](https://lh6.googleusercontent.com/sBYGlirofdWpJJ1bX-UHTSJX5ZMdthE9tVw5cizeur4En2S3s75JGRGwbwrHvhTEa-KXKl2_edzvkbCfSUXWehV2P0yFiov_AJwpIhfD0PCn2P0e165ShVJ0QZQg5-N-KwCDkvMOagxdfFtf7ox1KUw) | Pokud jste zapomněli, jak se počítá crossProd:<br><br> ![](https://lh6.googleusercontent.com/3r_hvUMhi2hog8JMHb1-4JJq_Z225IP1s3xFZSujU5OHy2mmaweJ1cIsuHelCNDJUU1LbKiDNJBbpDllOC4rhZ9teATx8qqpvE1av8OHHnt4OCOUjD3fAZI6yUqaSRSrW3dUMuv-KyjGPVQeMlwsAcs)

---

### Interpolace normály a ostatních vektorů a proč se používají normalizované vektory.

Jak víme - interpolace vektorů se provádí především během procesu rasterizace, který probíhá po fázi VS a před fází FS. [](https://ogldev.org/www/tutorial09/tutorial09.html)

1. VS zpracovává každý vertex a vypočítává různé proměnné (například barvy, normály, souřadnice textur), které budou použity FS.
2. Rasterizace provede interpolaci mezi třemi vrcholy trojúhelníku (buď po řádcích, nebo jinou technikou) a "navštíví" každý pixel uvnitř trojúhelníku spuštěním FS.
3. FS vrátí barvu pixelu, kterou rasterizér umístí do color bufferu pro zobrazení (po absolvování několika dalších testů, jako je test hloubky atd.).

==Normálový vektor musí být transformovány pomoci normální maticí!==
<span class="maths">N = (M<sup>-1</sup>)<sup>T</sup></span>, kde M - modelová transformační matice ⇒ <span class="maths">n’ = Nn</span>

![](https://lh6.googleusercontent.com/leswKw9E1Z9GrZpbO7hR-LVXtw-2SNn5_RADmXj2YwOB-YIp-wYy2iDt4AlY7SOG_Cm3M-GazNae1I3n2k5tbF-tEIYCqyFId5DqHVApeBV4P9NayK9vDMX7tY0zf-JnxFx5KbVZu2sW8njPtRod2b8)

**Stačí mít jako vstupy VS jednotkové normály?**
- Ano, pokud se používají ve VS (ale délka se může změnit transformací ve VS)
- Ne, pokud jsou použity ve FS, protože interpolací zkrátí jejich délku, proto normalizujte normály před použitím!

**Které transformace nemění délku normál?** 
- Rigidní transformace, tj. takové, které mohou být reprezentovány ortogonální maticí (e.g. rotace a translace)

**Proč musí normálový vektor zůstat normalizovaný?**

Pokud by normálové vektory nebyly normalizovány, pak by výpočty osvětlení mohly být zkreslené nebo nesprávné kvůli různým velikostem normál, které ovlivňují intenzitu osvětlení.
Interpolované normály na povrchu polygonu by nepředstavovaly plynulý přechod stínování, jak bylo zamýšleno (==používají se v dot součinu pro výpočet kosinu úhlu mezi dvěma vektory==).

==*Pokud není normalizovaný, dot product musí se dělit hodnotou součinem délek vektorů pro výpočet kosinu!*==

---


### Proč stačí kanály RGB (metamerism).
**Metamerism (neboli metamerie)** je jev, kdy se barva dvou objektů jeví pod určitým světelným zdrojem stejná, ale ve skutečnosti mají odlišné spektrální rozložení.

![[Pasted image 20230810123052.png|center|500]]

**Proč stačí kanály RGB?**

Kanály RGB jsou základními složkami při reprezentaci barev v digitálních systémech, jako jsou například počítačové monitory, televizní obrazovky nebo digitální fotoaparáty. Každá barva je v těchto systémech vytvořena kombinací intenzit tří základních barev - červené (R), zelené (G) a modré (B).

V případě kanálů RGB se využívá metamerism, protože lidské oko má tři typy čípku citlivé na různé části spektra - červený, zelený a modrý. Tím, že se používají právě tyto tři základní barevné kanály, lze vytvořit širokou škálu barev, které jsou pro většinu lidí vnímány jako různé.

---


### Metody stínování.
**Shading models** - definují, jak se vypočítávají odstíny (shades) barev pro pixely.
#### Flat
Popis | Demo
----- | ----- 
Odstíní (shades) každý face jednotně na základě jeho normáloveho vektoru, dává low-poly modelům faced vzhled (per-face). | ![[Pasted image 20230810140200.png\|150]]

**Princip:**
- osvětlení se vyhodnocuje jednou pro každý polygon (obvykle pro první vrchol polygonu) na základě normály polygonu
- vypočtená barva se použije pro celý polygon, takže rohy vypadají ostře
- obvykle používá se v případech, kdy jsou pokročilejší techniky stínování příliš výpočetně náročné

#### Gouraud
Popis | Demo
----- | ----- 
Osvětlení (barva) počítané na vrchol, interpolace barev mezi vrcholy, rychlá a jednoduchá implementace. | ![[Pasted image 20230810141445.png\|150]]

**Princip:**
- určete normálu v každém vrcholu mnohoúhelníku
- použijte model osvětlení na každý vrchol a vypočítejte intenzitu světla z normály vrcholu
 - interpolujte intenzity vrcholů pomocí bilineární interpolace přes polygon povrchu

**Problemy:**
- protože se osvětlení počítá pouze ve vrcholech, mohou být nepřesnosti (hlavně u zrcadlových světel na velkých trojúhelnících) velmi viditelné
 - T-spoje se sousedními polygony mohou někdy vést k vizuálním anomáliím
 - spekulární světla se při plochém stínování vykreslují špatně


#### Phong
Popis | Demo
----- | ----- 
Známe normálové vektory vrcholů, interpolace normálových vektorů po povrchu, osvětlení vypočtené na fragment. | ![[Pasted image 20230810141827.png\|150]]

Phongovo stínování je podobné Gouraudovu stínování s tím rozdílem, že namísto interpolace intenzity světla se normály interpolují mezi vrcholy a osvětlení se vyhodnocuje na pixel. Spekulární odlesky jsou tedy počítány mnohem přesněji než v Gouraudově stínovacím modelu.

Pro dosažení hladšího vzhledu se normály počítají na vrchol, a ne per-face. Při výpočtu normál na vrchol je nutné vzít v úvahu plochy, které sdílejí daný vrchol. Takže například při použití čtyřúhelníků je každý vrchol (kromě rohových a hraničních vrcholů) sdílen čtyřmi polygony. Normála na vrcholu by se měla vypočítat jako normalizovaný součet všech normál jednotkové délky pro každou plochu, kterou vrchol sdílí. Uvažujme následující obrázek:

![center|250](https://lh6.googleusercontent.com/qnIsH1npkxK_zMH0FG2_u0LH5grtYt71a1tjZUIfHzD8h2iC8WnIxbxZtZB33mh9Pl0gd_K23On5qrxYhRAbCg9knDArhZj5cvOOr4ECOCtBhErOGQEkOiltM_W8HaNL7nvxuB1WJoKKFMMsfrOi1FE)

**Princip:**
- vypočítejte normálu N pro každý vrchol polygonu
- pomocí bilineární interpolace vypočtěte normálu Ni pro každý pixel (normálu je třeba pokaždé znovu [[#Interpolace normály a ostatních vektorů a proč se používají normalizované vektory.|normalizovat]]!)
- použijte model osvětlení na každý pixel a vypočítejte intenzitu světla z Ni.

---

### Phongův osvětlovací model, vzorce jednotlivých složek.
![](https://lh4.googleusercontent.com/rDckSiQkcIsMyspbifm3s5nmUCXKhvcrVX7eDd9AEgxwXL-tkH5Z8DNFidqTQqUBfMiSLRThqZ9xxjv6FY9pRFYhjBxH9p97Mai14OCCg8JdjZSjv8QvIPvHWUAUPEiOYMIZ__9TEbRWMlNEDTlHR2s)

**Phong illumination model** je lokální empirický osvětlovací model (žádné odrazy druhého řádu, žádné stíny, ...). Rozděluje odraz od povrchu do dalších tří částí:
<span class="maths">celkové_osvětlení = ambientní_složka + difúzní_složka + spekulární_složka</span>

#### Ambient
Popis | Demo 
----- | ----- 
**Ambientní osvětlení** je světlo rozptýlené okolním prostředím natolik, že jeho směr nelze určit - zdá se, že přichází ze všech směrů. Když světlo z okolí dopadá na povrch, je rozptýleno rovnoměrně do všech směrů. | ![[Pasted image 20230810160551.png\|300]] 

Graficky | Výpočet
----- | -----
**None :(** | <ul><li><span class="maths">ambient<sub>reflected</sub> = ambient<sub>light</sub> × ambient<sub>material</sub></span></li><li><span class="maths">global_ambient<sub>reflected</sub> = global_ambient<sub>env</sub> × ambient<sub>material</sub></span><br>(kde *global_ambient<sub>env</sub>* je intenzita okolního světla)</li></ul>

#### Diffuse
Popis | Demo 
----- | ----- 
**Difúzní světlo** představuje směrové světlo vysílané světelným zdrojem. Je rozptýleno rovnoměrně do všech směrů. Množství je úměrné úhlu mezi směrem ke světlu a normálou se mění s kosinem úhlu. | ![[Pasted image 20230810161120.png\|300]] 

Graficky | Výpočet
----- | -----
![\|250](https://lh6.googleusercontent.com/EsExtZ23ZbJ-d-2PjBTqqvSQUbwN6syCZO_Acv8SImZtTLsppEecqLDVnMVuz0EqGeXXG5DlAo4117wIIqdBN7-vE--i59ZMjXAA8llTEq1XqgvPHCl86MJffOdMgHZ1wznvQ2AUX58GysrdsgesKCo) | <ul><li><span class="maths">diffuse<sub>reflected</sub> = max (cos a, 0) × diffuse<sub>light</sub> × diffuse<sub>material</sub></span><br>(kde cos a má maximum při dopadu světla kolmo na povrch, cos a = l.n)</li></ul>

#### Specular
Popis | Demo 
----- | ----- 
**Difúzní světlo** představuje směrové světlo vysílané světelným zdrojem. Je rozptýleno rovnoměrně do všech směrů. Množství je úměrné úhlu mezi směrem ke světlu a normálou se mění s kosinem úhlu. | ![[Pasted image 20230810171958.png\|300]] 

Graficky | Výpočet
----- | -----
![[Pasted image 20230810162642.png\|250]] | <ul><li><span class="maths">specular<sub>reflected</sub> = max (cos b, 0)<sup>shininess_material</sup> × specular<sub>light</sub> × specular<sub>material</sub></span><br>(kde cos b = c.r)</li></ul>
![[Pasted image 20230810163237.png\|250]] | <ul><li><span class="maths">specular<sub>reflected</sub> = max (cos b, 0)<sup>shininess_material</sup> × specular<sub>light</sub> × specular<sub>material</sub></span><br>(kde cos b = n.s, s = (c + l)/\|c + l\|)</li></ul>

---

### Základní typy světel a jejich simulace ve Phongovém osvětlovacím modelu.

#### Directional

Popis | Demo
----- | ----- 
Světlo vychází rovnoměrně z daného směru, jako by vycházelo z plochy nekonečné velikosti a vzdálenosti od osvětlovaného objektu (e.g. slunce). | ![[Pasted image 20230810173339.png\|150]]
**Vlastností**:
- má směr
- počítá ambient, diffuse a specular složky
- ve Phongu: <span class="maths">light = ambient<sub>reflect</sub> + diffuse<sub>reflect</sub> + specular<sub>reflect</sub></span>

#### Point 
Popis | Demo
----- | ----- 
Světlo vychází z jednoho bodu a šíří se do všech směrů. | ![[Pasted image 20230810173303.png\|200]]
**Vlastností**:
- má pozicí
- počítá ambient, diffuse a specular složky
- zalezi na vzdalenosti: 
	- <span class="maths">dist = normalize(lightPos − pointPos)</span>
	- <span class="maths">attenuationFactor = 1.0 / (constAtten + linearAtten × dist + quadrAtten × dist<sup>2</sup> )</span>
- ve Phongu: <span class="maths">light = (ambient<sub>reflect</sub> + diffuse<sub>reflect</sub> + specular<sub>reflect</sub>) × attenuationFactor</span>

#### Spot (reflector)
Popis | Demo
----- | ----- 
Světlo vychází z jednoho bodu a šíří se ven v kuželu o variabilním průměru a délce. | ![[Pasted image 20230810172212.png\|200]]
**Vlastností**:
- má směr (<span class="term">GL_SPOT_DIRECTION</span>), pozicí, ořezávání (<span class="term">GL_SPOT_CUTOFF</span>)
- počítá ambient, diffuse a specular složky
- zalezi na vzdalenosti: 
	- <span class="maths">dist = normalize(lightPos − pointPos)</span>
	- <span class="maths">attenuationFactor = 1.0 / (constAtten + linearAtten × dist + quadrAtten × dist<sup>2</sup> )</span>
- má nastavitelný úhel osvětlení: <span class="maths">spotlightEffect = pow(max(cos a, 0), SPOT_EXPONENT)</span>, kde <span class="maths">cos a</span> defenuje uhel mezi <span class="term">SPOT_DIRECTION</span> vektorem a smerovym vektorem k vertexu
- ve Phongu: <span class="maths">light = (ambient<sub>reflect</sub> + diffuse<sub>reflect</sub> + specular<sub>reflect</sub>) × attenuationFactor</span>

---

## 5. Základní parametrické křivky
### Interpolační (Ferguson, Catmull-Rom) a aproximační křivky (Bézier, B-spline, NURBS). 

Vytváření křivek zahrnuje metody, kterými řídicí body mění její vzhled. 

Nazev | Popis | Graficky
--- | --- | ---
**Interpolační** | Křivka projde skrz kontrolně body. | ![[Pasted image 20230813171740.png\|150]]
**Aproximační** | Křivka projde poblíž kontrolních bodů (je jimi přitahována). | ![[Pasted image 20230813171749.png\|150]]

#### Interpolace

Metoda | Popis | Příklad
--- | --- | ---
Polynomiální <br>tvorba | Vytvoření křivky se určí řešením soustavy rovnic. <br>Náročná na výpočet. | <div style="width:320px">Body: p0 = [0, 0], p1 = [1, 2], p2 = [3, 3]</div> Rovnice: <span class="maths">y = f<sup>2</sup>(x) = ax<sup>2</sup> + bx + c</span><br><ul><li>p0: <span class="maths">0 = c</span> → a = -0.5</li><li>p1: <span class="maths">2 = a + b + c</span> → b = 2.5</li><li>p2: <span class="maths">3 = 9a + 3b + c</span> → c = 0</li></ul>
Tvorba z <br>kubických oblouku | Každý oblouk definován pomocí 2 body a první 2 derivace). Jak získáme derivací? | Centrální deference: <br>![[Pasted image 20230813175212.png\|200]]<br>Pomocí (y<sub>i+1</sub>-y<sub>i-1</sub>) odečtením bodů <br>vepředu a vzadu získáme rovnoběžku pro i
Parametrická <br>tvorba | Pohyb bodem v čase t. | 

##### Ferguson (Hermite)

K vytvoření této úsečky potřebujeme 2 body a 2 vektory. Pro každý bod a první derivaci budeme mít rovnici. Celkem máme 4 proměnné, tudíž rovnici křivky můžeme zadat v polymickém tvaru qubické křivky.

![[Pasted image 20230813193929.png]]

##### Catmull-Rom

![[Pasted image 20230905095131.png]]

#### Aproximace
Tento typ má následující vlastnosti:
1. vážený součet pozic bodů × váha (součet všech vah = 1)
2. ==posun bodu ovlivní **segment**==
3. má spojitou derevaci k-teho řadu

##### Bézier

Jsou definovány sadou řídicích bodů, které ovlivňují tvar křivky.  Bézierovy křivky mohou mít různé stupně, ale nejběžnější jsou kubické Bézierovy křivky (stupeň 3). 
Kubická Bézierova křivka je definována čtyřmi řídicími body: dvěma kotevními body a dvěma řídicími body. Křivka plynule prochází kotevními body a zároveň je ovlivňována řídicími body, čímž vzniká široká škála tvarů.

Pouziti | Obrazek
--- | :-:
Vektorová grafika:<ul><li>Obsahuje posloupnost příkazů, které říkají, co a jak se má vykreslit<li>Příkazy přesně určují vykreslované tvary</li><li>Příkazy se vykreslují v rozlišení displeje</li><li>Pokud zvětšíme obrázek jsou příkazy vykresleny znovu</li></ul> | ![[Pasted image 20230824222704.png\|250]]
Fonty:<ul><li>**Type 1 (PostScript)** jeden z prvních formátů digitálních písem vyvinutých Adobe. K definování tvarů znaků používají Bézierovy křivky a jsou založeny na jazyce pro popis stránek PostScript. Currently no support!</li><li>**Type 3** na rozdíl od Type 1 umožňuje pro písma složitější grafiku a efekty (e.g. stínování, gradienty atd.)</li><li>**TrueType** byl vytvořen Apple a Microsoft jako způsob zlepšit vykreslování písem na obrazovkách a tiskárnách. Používají kvadratické Bézierovy křivky. </li></ul> | ![[Pasted image 20230824223801.png\|200]]
Animace: <ul><li>K určení vlastností objektů používáme klíčové snímky (keyframes)</li><li>musíme určit, jak se bude hodnota měnit od jednoho klíčového snímku do druhého</li></ul> | ![[Pasted image 20230824231738.png]]

[[#Adaptivní vykreslování Bézierovy křivky (algoritmus de Casteljau).]]

##### B-spline

B-spliny jsou spojenim segmentu (Bézierovych křivek) a nabízejí větší flexibilitu než Bézierovy křivky, protože umožňují místní kontrolu nad tvarem křivky (pro kazdy segment sve nezavisle bazove funkce). Úpravou polohy řídicích bodů lze křivku upravit, aniž by byla ovlivněna celá křivka. B-spline křivky se běžně používají v aplikacích pro počítačem podporované navrhování (CAD).

[[#Bezier Spline]]

---

### Parametrická reprezentace.  

Existují různé způsoby reprezentace křivek:
1. <span class="term">explicitni</span> <span class="maths">y = f(x)</span> [parabola]
	- y závislé na x
	- nelze použít pro rotace protože nemá pro jeden x více hodnot y
	- nelze využít pro 3d křivku
2. <span class="term">implicitni</span> <span class="maths">F(x, y) = 0</span> [kruznice]
	- x a y jsou nezávislé
	- používáme pro test ==je bod na křivce? (in out test)==
3. <span class="term">parametricka</span> <span class="maths">x=x(t), y=y(t)</span> [kruznice (chodime po ni s casem t)]
	- funkce závislé na parametru (e.g. čas t)
	- n = stupeň
	  k = rad (počet kontrolních bodů pro 1 segment)
	  m = počet všech kontrolních bodů
	  m + k = počet hodnot v knot vektoru

**Parametrizace křivky** je proces definování parametrických funkcí, které umožňují jednoznačně identifikovat libovolný bod na křivce pro danou množinu nezávislých proměnných - parametrů.

Již jsme se dozvěděli, že stupeň křivky je různý, pak můžeme zjistit, co jednotlivé stupně představují:

 Stupeň | Název | Rovnice ve 3D
----- | ----- | ----- 
<span class="term">n = 0 (constant)</span> | Point | <ul><li> Q<sub>x</sub>(t) = a<sub>x<sub>0</sub></sub>t<sup>0</sup></li><li> Q<sub>y</sub>(t) = a<sub>y<sub>0</sub></sub>t<sup>0</sup></li><li> Q<sub>z</sub>(t) = a<sub>z<sub>0</sub></sub>t<sup>0</sup></li></ul>
<span class="term">n = 1 (linear)</span> | Line | <ul><li> Q<sub>x</sub>(t) = a<sub>x<sub>0</sub></sub>t<sup>0</sup>+a<sub>x<sub>1</sub></sub>t<sup>1</sup></li><li> Q<sub>y</sub>(t) = a<sub>y<sub>0</sub></sub>t<sup>0</sup>+a<sub>y<sub>1</sub></sub>t<sup>1</sup></li><li> Q<sub>z</sub>(t) = a<sub>z<sub>0</sub></sub>t<sup>0</sup>+a<sub>z<sub>1</sub></sub>t<sup>1</sup></li></ul>
<span class="term">n = 2 (quadratic)</span> | Parabola | <ul><li> Q<sub>x</sub>(t) = a<sub>x<sub>0</sub></sub>t<sup>0</sup>+a<sub>x<sub>1</sub></sub>t<sup>1</sup>+a<sub>x<sub>2</sub></sub>t<sup>2</sup></li><li> Q<sub>y</sub>(t) = a<sub>y<sub>0</sub></sub>t<sup>0</sup>+a<sub>y<sub>1</sub></sub>t<sup>1</sup>+a<sub>y<sub>2</sub></sub>t<sup>2</sup></li><li> Q<sub>z</sub>(t) = a<sub>z<sub>0</sub></sub>t<sup>0</sup>+a<sub>z<sub>1</sub></sub>t<sup>1</sup>+a<sub>z<sub>2</sub></sub>t<sup>2</sup></li></ul>
<span class="term">n = 3 (cubic)</span> | S-curve | <ul><li> Q<sub>x</sub>(t) = a<sub>x<sub>0</sub></sub>t<sup>0</sup>+a<sub>x<sub>1</sub></sub>t<sup>1</sup>+a<sub>x<sub>2</sub></sub>t<sup>2</sup>+a<sub>x<sub>3</sub></sub>t<sup>3</sup></li><li> Q<sub>y</sub>(t) = a<sub>y<sub>0</sub></sub>t<sup>0</sup>+a<sub>y<sub>1</sub></sub>t<sup>1</sup>+a<sub>y<sub>2</sub></sub>t<sup>2</sup>+a<sub>y<sub>3</sub></sub>t<sup>3</sup></li><li> Q<sub>z</sub>(t) = a<sub>z<sub>0</sub></sub>t<sup>0</sup>+a<sub>z<sub>1</sub></sub>t<sup>1</sup>+a<sub>z<sub>2</sub></sub>t<sup>2</sup>+a<sub>z<sub>3</sub></sub>t<sup>3</sup></li></ul>
<span class="term">n = 4 (quartic)</span> | ??? | <ul><li> Q<sub>x</sub>(t) = a<sub>x<sub>0</sub></sub>t<sup>0</sup>+a<sub>x<sub>1</sub></sub>t<sup>1</sup>+a<sub>x<sub>2</sub></sub>t<sup>2</sup>+a<sub>x<sub>3</sub></sub>t<sup>3</sup>+a<sub>x<sub>4</sub></sub>t<sup>4</sup></li><li> Q<sub>y</sub>(t) = a<sub>y<sub>0</sub></sub>t<sup>0</sup>+a<sub>y<sub>1</sub></sub>t<sup>1</sup>+a<sub>y<sub>2</sub></sub>t<sup>2</sup>+a<sub>y<sub>3</sub></sub>t<sup>3</sup>+a<sub>y<sub>4</sub></sub>t<sup>4</sup></li><li> Q<sub>z</sub>(t) = a<sub>z<sub>0</sub></sub>t<sup>0</sup>+a<sub>z<sub>1</sub></sub>t<sup>1</sup>+a<sub>z<sub>2</sub></sub>t<sup>2</sup>+a<sub>z<sub>3</sub></sub>t<sup>3</sup>+a<sub>z<sub>4</sub></sub>t<sup>4</sup></li></ul>

Než se pustíme do spojitosti při spojování segmentů, je třeba si ujasnit, co představuje  segment křivky!

Křivka se skládá z: | Notace | Graficky
--- | --- | ---
jedné úsečky | <ul><li>Q(t) - křivka<br>(pro dané t dostaneme bod na křivce)</li><li>q'(t) - tečný vektor křivky<br>(1. derivace)</li><li>q''(t) - druhá derivace v bodě</li></ul> | ![[Pasted image 20230813134610.png\|200]]
několika úseků | <ul><li>Q<sub>i</sub>(t) - i-tý úsek křivky</li><li>q<sub>i</sub>'(t) - tečný vektor k 𝑖-té úsečce</li></ul>**Knots (Uzly)**:<ul><li>p<sub>i</sub> = Q<sub>i</sub>(1) = Q<sub>i+1</sub>(0)<br>je uzel (spojovací bod)</li><li>q<sub>i</sub>' = q<sub>i</sub>'(1) = q<sub>i+1</sub>'(0)<br>je tečný vektor (1. derivace) v uzlu i</li><li>q<sub>i</sub>'' = q<sub>i</sub>''(1) = q<sub>i+1</sub>''(0)<br>je 2. derivace v uzlu i</li></ul> | ![[Pasted image 20230813134755.png\|250]]

==Myslím, že už máte představu o tom, co derivace úseček křivky představují, ale podrobnější reprezentaci (a také interaktivní!) si můžete prohlédnout v sekci [[#Adaptivní vykreslování Bézierovy křivky (algoritmus de Casteljau).]]==

---

### Adaptivní vykreslování Bézierovy křivky (algoritmus de Casteljau).
Materiál, ze kterého článek vychází, prezentuje krásná Freya Holmér na  [svém kanálu YouTube](https://www.youtube.com/watch?v=aVwxzDHniEw)!

**De Casteljauův algoritmus** poskytuje intuitivní a efektivní přístup ke generování hladkých a vizuálně přitažlivých křivek, které se hodně používají v počítačem podporovaném navrhování, animaci a grafických aplikacích. De Casteljauův algoritmus umožňuje iterativním dělením řídicích bodů přesně definovat tvar křivky a bez námahy řídit její zakřivení, což vede k vytváření úžasných a složitých návrhů. V této části pochopíme práci tohoto algoritmu na příkladu konstrukce **kubických Beziérových křivek** (==použití 4 bodů k definování úsečky křivky==).

Na úvod se podívejme na princip algoritmu, který spočívá v iteračním dělení úseček mezi body, dokud nezůstane pouze jeden bod, který bude měnit svou polohu v závislosti na časovém parametru <span class="maths">t</span> získané funkce, čímž se vykreslí křivka.

Máme tedy 4 kontrolní body, pak první úroveň rozdělení (body A, B, C) a poslední rozdělení (body D, E). Každý posun podél daných úseček lze formulovat jako lineární interpolaci v závislosti na časovém parametru <span class="maths">t</span> (obrázek níže). Bod, který vytvoří křivku, se tedy nachází na úsečce D-E a my jej rozvineme jako funkci křivky. 

![[Pasted image 20230810184609.png]]

To je skvělé! Naše křivka bude sestrojena jako kombinace 6 interpolačních funkcí! Pojďme je tedy zkombinovat:

Lerp pro každý úsek | Polynomy dohromady | Vliv polynomu na bod
----- | ----- | -----
![[Pasted image 20230810190219.png\|210]] | ![[Pasted image 20230810190252.png\|210]] | ![[Pasted image 20230810190306.png\|210]]
Tento zápis se nazývá **Biersteinův polynomický tvar**! Graf těchto 4 polynomů je vidět na obrázku níže vlevo. Tento zápis se nazývá Biersteinův polynomický tvar. Graf těchto 4 polynomů je vidět na obrázku níže vlevo. Protože na jednu úsečku působí 4 řídicí body, graf zní "závislost hmotnosti <span class="maths">w</span> na čase <span class="maths">t</span>".

Čas a hmotnost jsou v tomto případě normalizovány a jsou v rozmezí 0 až 1. Zkusme zjistit, jak budou vypadat naše bodové vektory, když na ně aplikujeme váhu v časovém bodě rovnou například ~0,4 (pro zjednodušení umístěme počátek (tj. bod (0, 0)) dovnitř kontrolních bodů).

![[Pasted image 20230810192117.png]]

Víme tedy, jak vypadají vektory našich řídicích bodů v určitém časovém okamžiku, skvělé, tak proč je nesečíst? Vždyť vzorec, který jsme dostali dříve, právě tohle dělá! 

![[Pasted image 20230810192557.png]]

A teď jedna zajímavost. Pokud najdeme derivace těchto polynomů, zjistíme, že první derivace představuje rychlost změny (**velocity/tangent vector**), který udává směr křivky v určitém časovém okamžiku! ==Je to další bezierova křivka o stupeň niž (**quadratic**))!==

Můžeme však jít ještě dál a provést druhou derivaci, pak dostaneme vektor zrychlení (**acceleration vector**, jinými slovy - míru změny rychlosti)! ==A znovu jde o další bezierovou křivku o stupeň niž (**linear**)!==

Jedná se o formuláře, které jsme prošli v části [[#Parametrická reprezentace.]]

Nakonec poslední derivace již představuje stupeň změny zrychlení a vypadá obvykle jako bod!

![[Pasted image 20230813002411.png]]

Stejně podrobnou konstrukci křivky si můžete prohlédnout v interaktivní vložce Desmos níže [](https://www.desmos.com/calculator/pkpkjvjszz)!

<iframe src="https://www.desmos.com/calculator/dsd32lx4on" width="100%" height="700" style="border: 1px solid #ccc" frameborder=1></iframe>

---

### Spojitost při spojování segmentů. 

Materiál, ze kterého článek vychází, prezentuje krásná Freya Holmér na  [svém kanálu YouTube](https://www.youtube.com/watch?v=jvPPXbo87ds)!

Teď, když už víme, jak naše křivka vypadá ve 3D a jak je nastavena, můžeme se pustit do spojování jednotlivých segmentů křivky!

Všechny úsečky, které se budou spojovat, se většinou vyrovnávají pomocí tečných vektorů, které jsou následujících typů:

![[Pasted image 20230813164234.png]]

Dále se podíváme na všechny varianty a dozvíme se, jak s nimi souvisí derivace různých úrovní a jak tečny mění vzhled složené křivky.

Představme si, že se naše křivka skládá z několika částí, ale přesto chceme, aby tvořila souvislý matematický tvar. Zavedeme si nový termín - **Bezier Spline**.

#### Bezier Spline

Jak jsme se dozvěděli v předchozích kapitolách - naše křivka je definována **řídicími body (control points)** a bodům, v nichž se úsečky spojují, říkáme **uzly (knots)**, a konečně při sestavování křivky tohoto rozsahu se cestování po ní zvětšuje, takže potřebujeme definovat nový časový interval, po kterém budeme chodit s novým parametrem **u** a který se bude skládat z tzv. **uzlových intervalů (knot intervals)**.

![[Pasted image 20230813151414.png]]

Otázkou však zůstává - proč prostě nevytvořit Beziérovu křivku většího stupně? V tomto případě nebudeme mít nad úsečkou lokální kontolu, bude to prostě velká křivka. Když nyní aplikujeme základní funkce na jednotlivé intervaly, vidíme, že nad úsečkou máme lokální kontrolu!

![[Pasted image 20230813152302.png]]

Nakonec se dostáváme k otázce spojitosti při spojování segmentů. Existují 2 typy spojitosti: **Parametric** continuity a **Geometric** continuity.

#### Parametric continuity C<sup>n</sup>

Stupeň | Demo
--- | --- 
<span class="term">C<sup>0</sup> Positional continuity</span> | ![[Pasted image 20230813154113.png\|center]]
**Сhování** | Při první derivaci tento typ spojení nezachovává délku výsledného tečného vektoru, takže se při interpolaci křivky mohou objevit skoky (na grafu rychlosti je vidět, že všechny úsečky první derivace nejsou nijak spojeny). ==Začátek dalšího segmentu křivky je však stejný jako konec předchozího.==

Stupeň | Demo
--- | --- 
<span class="term">C<sup>1</sup> Velocity continuity</span> | ![[Pasted image 20230813155840.png\|center]]
**Сhování** | Jak lze tedy předchozí chybu upravit? Mirror tangens points - ==Rychlost na konci bodu A se musí rovnat rychlosti na začátku bodu B!== Jinými slovy - 1. derivace na konci bodu A se musí rovnat 1. derivaci začátku bodu B. Proto lze říci, že toto spojení zaručuje tečnu, ale ne hladké vázaní.

Stupeň | Demo
--- | --- 
<span class="term">C<sup>2</sup> Acceleration continuity</span> | ![[Pasted image 20230813161050.png\|center]]
**Сhování** | No, zbývá upravit zrychlení podle stejného principu - ==Zrychlení na konci bodu A se musí rovnat zrychlení na začátku bodu B!== Jinými slovy - 2. derivace na konci bodu A se musí rovnat 2. derivaci začátku bodu B. Toto spojení zaručuje směr, rychlost a zrychlení.

==Nakonec je třeba uvést, že každý další stupeň (C<sup>n+1</sup>) nutně splňuje vlastnosti všech předchozích stupňů!== 

![[Pasted image 20230813161440.png]]

#### Geometric continuity G<sup>n</sup>

Stupeň | Сhování
--- | --- 
<span class="term">G<sup>0</sup> Positional continuity</span> |  Tento typ spojení má tytéž vlastnosti jako C<sup>0</sup>

Stupeň | Demo
--- | --- 
<span class="term">G<sup>1</sup> Tangent continuity</span> | ![[Pasted image 20230813164724.png\|center]]
**Сhování** | Z tohoto spojení vyplývá, že tečné vektory v uzlovém bodě jsou zarovnány (ale nejsou si rovny!).<br>Takže křivky mají stejný směr tečny a směr pohybu, ale rychlost je jiná. ![\|300](https://lh3.googleusercontent.com/R70zzRaOrkfXIlhzP7JpeQbLaBC05X6miN2grwmsQiamL09G7g1srtBM5qGFVkd5Bl_O0db0UYeTsubodICiDZ3FUZITBioRDcOxdg5j0iuNWLSSQpKs90ZMssqPlq1bq8F6HMj90MrmR0PuUGqNQ3Q)

Stupeň | Demo
--- | --- 
<span class="term">G<sup>2</sup> Curvature continuity</span> | ![[Pasted image 20230813165819.png\|center]]
**Сhování** | Zakřivení je samo o sobě spojité.<br> Spojení zachová směr, rychlost a zrychlení!

==Nakonec je třeba uvést, že každý další stupeň (G<sup>n+1</sup>) nutně splňuje vlastnosti všech předchozích stupňů!== 

![[Pasted image 20230813170643.png]]

---