# MP2 ZH1 segítség
---
### elég a property szignatúra (prop tab+tab)
```csharp
public string Mezo { get; set; }
```
---
### kívülről nem módosítható: protected set;
```csharp
public int Mezo { get { ... } protected set { ... } }
```
---
### osztályon kívülről nem módosítható: private set;
```csharp
public int Mezo { get { ... } private set { ... } }

// klasszikus property
public string GetMezo() { ... }
public void SetMezo(string value) { ... }

// csak olvasható property / számított property
public string Nev { get { ... } }

// string nem lehet null vagy üres
if (string.IsNullOrEmpty(value))
    throw new Exception("A property értéke nem megfelelő!");

// intervallum ellenőrzés [10; 70)
if (value < 10 || value >= 70)
    throw new Exception("A property értéke nem megfelelő!");

// oszthatóság ellenőrzés pl. 5-tel osztható kell legyen
if (value % 5 != 0)
    throw new Exception("A property értéke nem megfelelő!");
```

### regex
- `[]` között tudjuk felsorolni a lehetséges karaktereket pl. `[a-z]`, 1-1 külön karaktert is fel lehet sorolni pl. `[a-z/]`
- `@"^[a-z]"` <-- első karakter
- `@"^[a-z][0-9/]*"` <-- első karakter + bármennyi karakter utána
- `@"^[a-z][0-9/]*[A-Z]$"` <-- első karakter + bármennyi karakter utána + utolsó karakter

első karakter számokat tartalmazhat, az egész számok és betűk az utolós pedig csak betűk lehetnek pl.
```csharp 
if (!Regex.IsMatch(value, @"^[0-9][aA-zZ0-9]*[aA-zZ]$"))
    throw new Exception("A property értéke nem megfelelő!");
```
---
### értéke csak egyszer adható meg
```csharp
private string mezo;
private bool beallitva = false;
public string Mezo
{
    get { return mezo; }
    set
    {
        if (beallitva)
            throw new Exception("A property értéke már be lett állítva!");
        beallitva = true;
        mezo = value;
    }
}
```
---
### konstruktorok
```csharp
public OsztalyNeve(string mezo, string mezo2) 
{ 
    Mezo = mezo;
     Mezo2 = mezo2; 
}
```
Előző konstruktor meghívása
```csharp
public OsztalyNeve(string mezo) : this(mezo, "statikus ertek")
{ }
```
Ős konstruktor meghívása
```csharp
public GyerekOsztalyNeve(string mezo, string mezo2, int mezo3) : base(mezo, mezo2) 
{ 
    Mezo3 = mezo3; 
}
```
---
### `ToString()` metódus felülírása
```csharp
public override string ToString() { return $"{adatok felsorolása}"; }
```
---
### `Equals()` metódus felülírása egysoros megoldás
```csharp
public override bool Equals(object obj)
{
    return obj is not null && obj is OsztalyNeve masik && this.PropertyNeve == masik.PropertyNeve;
}
```
### `Equals()` metódus felülírása több soros megoldás
```csharp
public override bool Equals(object obj)
{
    if (obj is null) return false;
    if (obj is not OsztalyNeve) return false; // vagy !(obj is Osztalyneve)
    OsztalyNeve masik = obj as OsztalyNeve; // vagy (OsztalyNeve)obj;
    if (this.PropertyNeve == masik.PropertyNeve) return true;
    return false;
}
```
---
### Késői kötés alkalmazása
```csharp
public virtual int Valami() { ... }
```
### felülírás
```csharp
public override int Valami() { ... }
```
---
### ha már szerepel a listában
```csharp
if (lista.Contains(ujElem)) { throw new Exception("Ez az elem már szerepel a listában!"); }
```
---
### indexer (indexer tab+tab)
```csharp
public Osztalyneve this[string keresettMezo]
{
    get
    {
        foreach (OsztalyNeve peldany in lista)
        {
            if (peldany.Mezo == keresettMezo) return peldany;
        }
        throw new Exception("Nincs ilyen elem a listában!");
    }
}
```

### indexer használata
```csharp
kontenerOsztalyPeldany[keresettMezo];
```
---

### Megjegyzések
- switch case automatikus kitöltése enum esetekkel -> switch -> tab+tab -> property név beírása -> lefelé nyíl
- billentyűzet shorcutok:
  - ctrl + d -> sor duplikálás
  - alt + up/down -> sor mozgatás fel/le
  - ctrl + x -> sor kivágás
- c# shorcutok:
  - propfull, prop, indexer, ctor
  - generate constructor