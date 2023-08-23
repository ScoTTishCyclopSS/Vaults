## Obsáh:
- [[#1. Techniky pro efektivní implementaci uživatelského rozhraní|Techniky pro efektivní implementaci uživatelského rozhraní]]
- [[#2. Příprava uživatelského rozhraní pro testování s/bez uživatele|Příprava uživatelského rozhraní pro testování s/bez uživatele]]

## 1. Techniky pro efektivní implementaci uživatelského rozhraní

### Implementace MVVM modelu pomocí návrhových vzorů (např. observer, event-delegate, publish-subscribe). Řešení vztahu View-ViewModel pomocí návrhového vzoru “Data Binding”.

**MVVM (Model-View-ViewModel)** reprezentuje stav a chování prezentace nezávisle na ovládacích prvcích grafického uživatelského rozhraní.

![[Pasted image 20230814140334.png]]

- **View** je kolekce viditelných prvků, která také přijímá vstupy od uživatele. Patří sem uživatelské rozhraní (UI), animace a text. ==Prezentuje stav modelu a data, změny komponent uživatelského rozhraní.==
- **ViewModel** se nachází mezi vrstvami View a Model. Zde jsou umístěny ovládací prvky pro interakci s View, zatímco vazba se používá k propojení prvků uživatelského rozhraní ve View s ovládacími prvky ve ViewModelu. ==Obsahuje data ve formátu vhodné pro zobrazení.==
- **Model** reprezentuje data aplikace a obsahuje logiku pro manipulaci s daty, kterou ViewModel načte po obdržení vstupu od uživatele prostřednictvím View. ==Upozorní View, když je stav dojde ke změně v modelu!==

#### Observer pattern

Principem je vytváření komponent typu **Observer** na základě vztahu **one-to-many**. Tímto způsobem se Observery přihlásí (subscribe) ke změnám vybraného objektu (často VIewModel) pro následnou reakci na událost: ==subject changes -> observers are notified!==

![[Pasted image 20230814143002.png | center | 400]]

#### Event-delegate mechanism

Mechanismus určený k implementaci vzoru Observer v jazyce C# založený na událostech a delegátech.

1. Nejčastěji se ViewModel používá jako Subject, takže to uděláme. Při vytvoření přidáme událost <span class="term">PropertyChangedEventHandler</span> (seznam pozorovatelů jako delegátů). Pro hlášení budoucích změn našim pozorovatelům pomocí delegáta použijeme metodu <span class="term">PropertyChanged</span>!
```
public class TaskViewModel : INotifyPropertyChanged
{
	private string _taskTitle;
	public string TaskTitle
    {
        get { return _taskTitle; }
        set 
        { 
	        _taskTitle = value;
	        OnPropertyChanged("TaskModels");
	    }
    }
        
	public event PropertyChangedEventHandler PropertyChanged;
	protected void OnPropertyChanged(string property)
	{
	    PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(property));
	}
}
```
2. Napišme si příklad našeho Observeru, bude to jednoduchá třída s jednou metodou <span class="term">HandlePropertyChanged</span>, která se v důsledku přihlásí k události.
```
public class ObserverViewModel : INotifyPropertyChanged
{
	public void HandlePropertyChanged(object sender, PropertyChangedEventArgs e)
	{
	    Console.WriteLine($"Property '{e.PropertyName}' changed");
	}
}
```
3. Nyní zbývá jen zaregistrovat našeho Observera pomocí delegáta (podepsat ho na <span class="term">PropertyChanged</span>).
```
public class MainWindow : Window
{
	private TaskViewModel taskViewModel;
	private ObserverViewModel observerViewModel;
	
    public MainWindow()
    {
        InitializeComponent();
        
        taskViewModel = new TaskViewModel();
        DataContext = taskViewModel;
        
        observerViewModel = new ObserverViewModel();
        taskViewModel.PropertyChanged += ObserverViewModel.HandlePropertyChanged;
    }
}
```

#### Publish-subscribe pattern

Pattern Observer definuje závislost one-to-many mezi objekty, takže když jeden objekt (subjekt) změní stav, všechny jeho závislé objekty (pozorovatelé) jsou automaticky informovány a aktualizovány.

V patternu **Publish-subscribe** odesílatelé zprávy (**Publishers**), neprogramují zprávy tak, aby byly zasílány přímo konkrétním příjemcům (**Subscribers**). Pattern odděluje Publishers od Subscribers zavedením prostředníka známého jako "Message-broker". Publishers publikují zprávy Message-brokerovi a Subscribers přijímají zprávy od Message-brokeru.

![[Pasted image 20230814161713.png | center | 300]]

#### Data Binding

Obecná technika pro propojení dvou zdrojů dat/informací. Oddělení View a ViewModel v architektuře MVVM.

1. Binding v XAML (za slovem Binding následuje název vlastnosti v příslušném ViewModelu)
```
<Label Content="{Binding TaskTitle}">
```

2. Implementace <span class="term">INotifyPropertyChanged</span> 
- Prvky UI reagují na změny pomocí <span class="term">INotifyPropertyChanged.PropertyChanged</span> udalosti
- Obvykle implementuje abstraktní třídu (e.g. BindableBase)
  BindableBase již bude obsahovat událost <br><span class="term">event  PropertyChangedEventHandler</span>  a její zpracování <br><span class="term">void OnPropertyChanged</span>
```
public abstract class BindableBase : INotifyPropertyChanged 
{ 
	public event PropertyChangedEventHandler PropertyChanged; 
	protected void OnPropertyChanged(string propName) 
	{ 
		PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propName));
	}
	...
}
```
3. Implementace Properties v ViewModel (se při přístupu k nim chovají jako políčka)
```
public class TaskViewModel : BindableBase
{
	private string _taskTitle;
	public string TaskTitle
    {
        get { return _taskTitle; }
        set 
        { 
	        _taskTitle = value;
	        OnPropertyChanged("TaskModels");
	    }
    }
    ...
}
```
4. Commands (objekt, který slouží ke sjednocení chování akcí v UI)
-  založeny na rozhraní <span class="term">ICommand</span>
- definuje metody <span class="term">Execute()</span> a <span class="term">CanExecute()</span>

```
public class RelayCommand : ICommand
{
	readonly Action<object> _execute;
    readonly Predicate<object> _canExecute;
    
    public RelayCommand(Action<object> execute, Predicate<object> canExecute)
    {
        _execute = execute;
        _canExecute = canExecute;
    }
    
	public bool CanExecute(object parameter)
    {
        return _canExecute == null ? true : _canExecute(parameter);
    }
    
    public void Execute(object parameter)
    {
        _execute(parameter);
    }
}
```

- při vytváření nového příkazu je proto třeba ve ViewModelu nastavit několik nových metod: tu, která vrátí kontrolu možnosti provedení, a tu, která bude (nebo nebude) provedena jako výsledek

```
public class TaskViewModel : BindableBase
{
    ...
    private RelayCommand _taskCommand;
    
    public ICommand AddUserCommand
    {
        get
        {
            return _taskCommand ?? 
            (_taskCommand = new RelayCommand(WriteHello, CanWriteHello));
        }
    }
    
    private bool CanWriteHello()
    {
	    return true;
    }
	
	private void WriteHello()
	{
		Console.WriteLine("Hello!");
	}
}
```

---

### Přizpůsobení (customization) UI komponent pomocí šablon (DataTemplate, ControlTemplate), stylů a triggerů.  Vytváření tzv. User Control a Custom Control.

Šablona (**Template**) popisuje celkový vzhled a vizuální podobu prvku UI. Ke každému ovládacímu prvku je přiřazena výchozí šablona, která určuje vzhled daného prvku.

#### Data Template

**Data Template** definuje a specifikuje vzhled a strukturu kolekce dat.
Pro řízení vykreslování obsahu (ContentControl) - je nutné použít vlastnost <span class="term">ContentTemplate</span>
```
// Vytvořená šablona mění způsob zobrazení textu v TextBlock 
<DataTemplate x:Key="myTemplate">
  <TextBlock FontWeight="Bold" TextWrapping="Wrap"/>
</DataTemplate>

// Použití šablony v ContentControl
<ContentControl ContentTemplate="{StaticResource myTemplate}"/>
```
Vlastnost <span class="term">ItemTemplate</span> slouží k regulaci všech položek v rámci objektu (ItemsControl).
```
// Vytvořená šablona mění způsob zobrazení textu v TextBlock 
<DataTemplate x:Key="myItemTemplate">
  <TextBlock FontWeight="Italic"/>
</DataTemplate>

// Použití šablony v ItemTamplate
<ListBox ItemTemplate="{StaticResource myItemTemplate}"/>
```

#### Control Template
**ControlTemplate** definuje vizuální vzhled ovládacího prvku. Všechny prvky uživatelského rozhraní mají nějaký vzhled i chování, např. tlačítko má vzhled a chování.

```
<ControlTemplate x:Key="buttonTemplate" TargetType="{x:Type Button}">
	<Grid>
		<Ellipse Width="100“ Height="100">
			<Ellipse.Fill>
				<LinearGradientBrush StartPoint="0,0" EndPoint="0,1">
					<GradientStop Offset="0" Color="Blue"/>
					<GradientStop Offset="1" Color="Red"/>
				</LinearGradientBrush>
			</Ellipse.Fill>
		</Ellipse>
		...
</ControlTemplate>

// Použití šablony na tlačítko
<Button Template="{StaticResource buttonTemplate}" Content="OK" />
```

**Jaký je v tom rozdíl?**

==ControlTemplate obsahuje výrazy TemplateBinding, které se vážou zpět na vlastnosti samotného ovládacího prvku, zatímco DataTemplate obsahuje standardní výrazy Binding, které se vážou na vlastnosti jejího DataContextu.==

#### Styles

Upravování stylu probíhá podobným způsobem, zde se však používají tagy <span class="term">Setter</span>, za nimiž následuje název vlastnosti, která se má změnit
```
// Změna způsobu zobrazení textu v elementu "Button"
<Style x:Key="buttonStyleBold" >
	<Setter Property="Button.FontWeight" Value="Bold"/>
</Style>

// Použití stylu na tlačítko
<Button Style="{StaticResource buttonStyleBold}">I want to die...</Button>
```

#### Triggers

Poslední skupinou přizpůsobení jsou Triggery. Chcete-li je změnit, musíte se obrátit na příslušnou vlastnost <span class="term">Style.Triggers</span>

##### Property triggers
Jsou vyvolány, když se hodnota závisle property změní

```
<Style x:Key="RedButtonStyle" TargetType="Button">
    <Style.Triggers>
        <Trigger Property="IsMouseOver" Value="True">
            <Setter Property="Background" Value="Red" />
        </Trigger>
    </Style.Triggers>
</Style>

// Použití stylu na tlačítko
<Button Style="{StaticResource RedButtonStyle}">Kill me...</Button>
```

##### Data triggers
Používají se pro vlastnosti, které nemusí být nutně závislými vlastnostmi. Fungují tak, že vytvoří vazbu na běžnou vlastnost, u které se pak sledují změny

```
<Style x:Key="RedButtonStyle" TargetType="Button">
    <Style.Triggers>
        <DataTrigger Binding="{Binding ElementName=elName, Path=IsChecked}"       
 Value="True">
            <Setter Property="Background" Value="Red" />
        </DataTrigger>
    </Style.Triggers>
</Style>

// Použití stylu na tlačítko
<Button Style="{StaticResource RedButtonStyle}">Kill me...</Button>
```

##### Event triggers
Jsou vyvolány, když je vyvolána událost

```
<Style x:Key="RedButtonStyle" TargetType="Button">
    <EventTrigger RoutedEvent="MouseEnter">
        <Setter Property="Background" Value="Red" />
    </EventTrigger>
</Style>

// Použití stylu na tlačítko
<Button Style="{StaticResource RedButtonStyle}">Kill me...</Button>
```

#### User control

**UserControl** prvky umožňují sbírat a kombinovat různé integrované ovládací prvky dohromady a zabalit je do opakovaně použitelného XAML.

- Složí více existujících ovládacích prvků do opakovaně použitelné "skupiny".
- XAML + kód za ním
- Nelze stylovat/templovat
- Dědí z UserControl

```
// WPF kod
<UserControl x:Class="MyUserControl" ...>  
    <Grid>  
	  <uc:MyUserControl Title="Enter title:" MaxLength="30" />
    </Grid>  
</UserControl>

// .cs kod
public partial class MyUserControl : UserControl
{
	public string Title { get; set; }
	
    public LimitedInputUserControl()
    {
        InitializeComponent();
        this.DataContext = this;
    }
}
```

#### Custom control

**CustomControl** je třída, která nabízí vlastní styl a šablonu, které jsou obvykle definovány v souboru generic.xaml.

- Rozšiřuje existující  control o další funkce
- Skládá se ze souboru s kódem a výchozího stylu v souboru Themes/Generic.xaml
- Může být stylizován/šablonován
- Nejlepší přístup k vytvoření knihovny ovládacích prvků

---

### Validace uživatelského vstupu pomocí validačních pravidel a interfaců (Data source exception, data error interface), případně vlastní validační třídou. Prezentace chyb pomocí šablon a triggerů.

![[Pasted image 20230815194922.png]]

#### Validační pravidla

##### Validation by exception from data source
Ověřování na základě výjimky ze zdroje dat vyvoláno při aktualizaci zdroje dat (může být také konvertorem hodnot).

<span class="term">Binding Path=... - Binding.ValidationRules - ExceptionValidationRule</span>

```
<TextBox>
	<TextBox.Text>
		<Binding Path=“AlarmName“>
			<Binding.ValidationRules>
				<ExceptionValidationRule />
			</Binding.ValidationRules>
		</Binding>
	</TextBox.Text>
</TextBox>
```

##### Validation by data error interface
Zdroj dat dědí třídu <span class="term">IDataErrorInfo</span> (kombinace techniky <span class="term">IDataErrorInfo</span> a vyvolání události změny validační vlastnosti jako interface <span class="term">INotifyPropertyChanged</span>)

==Umožňuje asynchronní ověřování ve vlákně na pozadí!==

```
// XAML kod
<TextBox>
	<TextBox.Text>
		<Binding Path=“AlarmName“>
			<Binding.ValidationRules>
				<DataErrorValidationRule />
			</Binding.ValidationRules>
		</Binding>
	</TextBox.Text>
</TextBox>

// .cs kod
public event EventHandler<DataErrorsChangedEventArgs> ErrorsChanged;
public IEnumerable GetErrors(string propertyName)
public bool HasErrors
{
	get { return … ; }
}
```

##### Validation by custom validation class
To představuje vytvoření třídy v dědičnosti  <span class="term">ValidationRule</span>, jejíž metody budou ověřovat data a vracet výsledek ověření (často "ok" nebo "not ok"), v XAMLu <span class="term">ValidatesOnDataErrors=“true“</span> a přidat pravidlo!

```
// XAML kod
<TextBox>
	<TextBox.Text>
		<Binding Path=“AlarmName“ ValidatesOnDataErrors=“true“>
			<Binding.ValidationRules>
				<local:JpgValidationRule/>
			</Binding.ValidationRules>
		</Binding>
	</TextBox.Text>
</TextBox>

// .cs code
public class JpgValidationRule : ValidationRule 
{
	public override ValidationResult Validate(object value, CultureInfo cultureInfo)
	{
		return new ValidationResult(true, null);
	}
}
```


##### Validation by custom code (using delegate)  #todo 

#### Prezentace chyb

Prezentace chyb uživateli umožňuje lepší zpětnou vazbu mezi uživatelem a aplikací pro potřebnou validaci dat. Existují následující typy prezentace chyb:

- Vytvoření samostatné šablony [[#Control Template]], která by zobrazovala například textový blok s chybou (nebo vytvořit Binding na výsledek pravidla ValidationRule nebo na předem vytvořenou chybovou zprávu.)
- Použití triggerů s následnou zprávou nad kurzorem
  ```
  <Trigger Property="Validation.HasError" Value="true">
  ```
	![[Pasted image 20230815200104.png | 200]]
---

## 2. Příprava uživatelského rozhraní pro testování s/bez uživatele

![[Pasted image 20230815215357.png | center | 500]]

### Postupy pro důsledné oddělení jednotlivých částí (decomposition) software podle vzoru MVC/MVP/MVVM. Vytáření UI test skriptu (testovací kód, nahrávání interakce, GUI ripping).

#### MVC (Model-View-Controller)

Součásti | Schéma
--- | ---
<ul><li>**Model** reprezentuje data a business logiku</li><li>**View** prezentuje model uživateli</li><li>**Controller** zpracovává uživatelský vstup</li></ul> | ![[Pasted image 20230816122023.png \| center \| 250]]

##### Model

- Reprezentuje data aplikace a obsahuje logiku pro manipulaci s daty
- Seskupuje související data a operace pro poskytování konkrétní služby
- Poskytuje služby, ke kterým Controller přistupuje pro změny v modelu
- Upozorňuje View, když dojde ke změně stavu modelu
- ==No “direct connection” from model to view or controller==

##### View

- Prezentuje stav modelu a data
- Prezentuje změny UI komponent
- Předává uživatelské vstupy do Controlleru
- Zobrazení může mít logiku (code behind)

![[Pasted image 20230816122408.png | 300]]

==Zde vzniká rozpor ohledně fungování funkce View, protože jak View, tak Contoller zpracovávají uživatelský vstup.==

##### Controller

- Zpracovává požadavky od uživatelů
- Převádí vstupy od uživatelů na akce, které má model provést
- Vybírá další View na základě vstupu uživatele a výsledku operací modelu

##### Reálná implementace

Součásti | Schéma
--- | ---
<ul><li>žádná čistá separace View-Controller</li><li>view má "Model logiku"</li><li>uživatel komunikuje s oběma View a Controller</li></ul> | ![[Pasted image 20230816122642.png\|250]]

#### MVP (Model-View-Presenter)

Součásti | Schéma
--- | ---
<ul><li>Uživatelský vstup a výstup pomocí **View**</li><li>Propojení **View** a **Presenter**</li><li>Kontrolní řídicí jednotka (Presenter)</li></ul> | ![[Pasted image 20230816123439.png \| center \| 250]]
**Passive View typ**: <ul><li>View je jednodušší</li><li>Žádná vazba mezi Model a View</li></ul> | ![[Pasted image 20230816123611.png\| center \| 250]]

##### Presenter

- Přijímá vstupy z View
- Interakce s Modelem za účelem získání nebo aktualizace dat
- Formátuje data z Modelu do prezentovatelného formátu pro View
- Aktualizuje stav a obsah View na základě změn v Modelu a interakcí s uživatelem

##### **Passive View vs. Standard MVP**

Ve standardním MVP má Presenter aktivnější úlohu při aktualizaci View. Přímo aktualizuje prvky, vlastnosti a ovládací prvky View tak, aby odrážely změny v Modelu. ==View může předat některé úlohy vykreslování a formátování Presenterovi, ale za většinu aktualizací UI je zodpovědný Presenter.== Standardní MVP má tendenci mít těsnější vazbu mezi Presenterem a View, protože Presenter má větší kontrolu nad vzhledem Zobrazení!

V Passive View MVP  je View odpovědné za vykreslování a aktualizaci na základě instrukcí od Presentera. Presenter se zaměřuje především na obsluhu interakcí s uživatelem, obchodní logiku a manipulaci s daty. ==Upozorňuje View na změny v Modelu, ale View se samostatně rozhoduje, jak aktualizovat své vlastní UI prvky.==

#### MVVM (Model-View-ViewModel)

Tento typ modelu je podrobně popsán v kapitole [[#Implementace MVVM modelu pomocí návrhových vzorů (např. observer, event-delegate, publish-subscribe). Řešení vztahu View-ViewModel pomocí návrhového vzoru “Data Binding”.]]

#### UI Testing

Sedm **principů testování**:
1. testování ukazuje pouze existenci defektu
2. komplexní testování není možné
3. včasné testování (early testing)
4. seskupování defektů
5. paradox pesticidů
6. testování závisí na kontextu
7. chybný závěr o neexistenci chyb (absence-of-errors fallacy)

Testovací přístupy:

Název | Popis
--- | ---
**Black Box testing** | <ul><li>na základě funkcionality</li><li>neznámá vnitřní struktura</li></ul>
**White Box testing** | <ul><li>založený na programovacích patternech</li><li>známá vnitřní struktura</li></ul>
**Grey Box testing** | <ul><li>Black Box + White Box</li></ul>

Testování uživatelského rozhraní má **následující cíle**:
- ověření funkčnosti, dostupnosti komponent uživatelského rozhraní.
- walk-through (dokončení úkolů s určitým úsilím)
- čitelnost, vyhledatelnost
- chybové situace
- použitelnost

Metody testování:

Název | Popis
--- | ---
**Manual Based Testing** | <ul><li>ruční kontrola lidskými testery v souladu s požadavky uvedenými ve specifikaci UI</li></ul>
**Record and Replay** | <ul><li>testovací kroky jsou zaznamenávány (nastrojem, not Fraps hehe)</li><li>zaznamenané testovací kroky jsou prováděny v aplikaci</li></ul>
**Model Based Testing** | <ul><li>model chování systému</li><li>pomáhá při vytváření efektivních testovacích situací</li></ul>

##### UI Test Script

Psaní testovacího skriptu simuluje akci uživatele použitím různých metod a funkcí v připravené aplikaci (jednoduchým příkladem je Selenium: klikni sem, počkej, napiš tohle...).

![[Pasted image 20230816114308.png | center | 600]]

Nejčastěji se úspěch určuje podle toho, zda byla během testování vyvolána výjimka (Exception)

##### Interaction recording

Testování touto metodou se provádí pomocí nějakého automatizovaného nástroje (tj. nejedná se o Fraps, ale o nástroj), který provede kroky v aplikaci (např. Espresso Test Recorder).

![[Pasted image 20230816114530.png | center | 600]]

Po "nahrání" kroků v aplikaci nástroj vygeneruje kód, který v budoucnu provede stejné kroky spuštěním kódu, takže možnost upravit test zůstává!

##### GUI ripping

Tato metoda spočívá v generování hierarchického modelu UI se souborem operátorů. 
Zahrnuje programovou interakci s prvky uživatelského rozhraní (návrhářem testů), zachycení jejich obsahu a extrakci příslušných dat pro další zpracování nebo analýzu.
Nástroj vygeneruje sadu testů pro scénáře!

![[Pasted image 20230816115533.png | center | 600]]

Během provádění skriptu se často provádí snímky obrazovky pro pozdější analýzu, např. pro porovnání po změně, pokud se něco najednou chová jinak.

![[Pasted image 20230816120025.png | center | 600]]

##### Simulace lidského chování

Nejčastěji simulace lidského chování umožňuje odhalit náhodné chyby, protože takový test se provádí bez zásady "pracovat s aplikací as intendent" nebo Stress-test.

Běžně se používají následující testovací techniky:

Název | Popis
--- | ---
**Android monkey testing** | <ul><li>běží na emulátoru nebo zařízení</li><li>generuje pseudonáhodné uživatelské události</li><li>generuje řadu událostí na úrovni systému</li></ul>
**Keystroke Level Mode (KLM)** | <ul><li>zahrnuje lidské faktory do testování (e.g. reakční doba)</li><li>definuje kritérium lidského výkonu</li><li>určuje minimální dobu trvání UI walk-through of ideálního walk-through.</li></ul>

---

### Příprava software pro uživatelské testování - data mocking, podpora metody Wizard-of-Oz, sběr dat z chování software a interakce s uživatelem.

#### Testovánı́ s uživatem

##### Data mocking

Je technika testování softwaru, při níž se k simulaci chování skutečných komponent nebo závislostí v systému používají makety objektů. **Mock objekty** jsou simulované verze skutečných objektů nebo komponent, se kterými testovaná jednotka interaguje. Tyto makety objektů napodobují chování skutečných komponent, aniž by ve skutečnosti prováděly jejich plnou funkčnost.

==Reálná data potřebná pro validní test!==

![[Pasted image 20230815215627.png | center | 500]]


##### Wizard-of-Oz
Metoda testování neexistujícího systému ("logika operace"). Odpovídá definici fungování algoritmů ("Co když - pak").

- Systém simulovaný reálnou osobou
- Uživatel o tom může, ale nemusí vědět

![[Pasted image 20230815212909.png | center | 500]]

EX:
1. Mějte v pozadí "wizard" (člověka), který bude vystupovat jako virtuální asistent
2. Účastníci se věří, že komunikují se reálným softwarem, ale jejich příkazy ve skutečnosti zpracovává "wizard"
3. Účastník se zeptá: "Jaké je dnes počasí?"
4. "Wizard" přečte zadání a ručně zadá odpověď, například "Dnes je slunečné počasí s teplotou 75 °C".
5. Sbírejte zpětnou vazbu o zkušenostech uživatelů, použitelnosti a případných problémech, které se vyskytly

#### Testovánı́ bez uživatele

- Velmi rychlá metoda
- Žádné monitorování reálných uživatelů
- Může zjistit pouze očividné problémy
- Vysoká pravděpodobnost neobjektivity
- Může být provedena na systému s nízkou úrovní vývoje

##### Heuristic Evaulation (Heuristics by J. Nielsen)

- Identifikuje problémy použitelnosti v návrhu uživatelského rozhraní.
- Low-fi, hi-fi prototypy a reálné artefakty
- Široké využití heuristiky návrhu
- Uživatelské rozhraní hodnocené více experty

![[Pasted image 20230815211859.png | center | 600]]

##### Cognitive Walkthrough
- Simulace kognitivních aktivit uživatele
- Simulace řešení prováděného uživatelem v každé fázi dialogu mezi člověkem a počítačem
- Abychom zjistili, zda můžeme předpokládat, že uživatel vybere správnou akci

![[Pasted image 20230815212735.png| center | 400]]

==Kognitivní procházení vyžaduje větší přípravu a dokumentaci, zatímco heuristické hodnocení je pružnější a subjektivnější!!!==

---