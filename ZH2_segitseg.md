# MP2 ZH2 segítség
---
### Interface
Interfacek esetén minden public és abstract, tehát nem kell kiírni. Mivel minden abstract, ezért nem fejtünk ki egy törzset sem.
```csharp
public interface IInterfeszNeve
{
    int Property1 { get; set; }
    void Eljaras(int param1, string param2);
}
```
Interface implementálása (akár több is lehet vesszőkkel felsorolva, de csak egy ősosztály lehet, amit mindig előre kell írni)
```csharp
public class OsztalyNeve : IInterfeszNeve
{
    public int Property1 { get; set; } // auto implementált prop
    public void Eljaras(int param1, string param2)
    {
        // implementáció
    }
}
```
---
### Abstract osztály
Abstract metódusnak nincs a törzse kifejtve. Ha egy osztályban van legalább 1 abstract metódus akkor az osztályt abstractnak kell jelölni, így már példányosítani nem lehet. Az abstract osztályokban lehetnek nem abstract metódusok is.
```csharp
public abstract class OsztalyNeve
{
    public int Fuggveny() 
    {
        return 0;
    }

    // absztrakt metódus, ami kötelezően felülírandó a gyermek osztályban 
    // (vagy abstract maradhat ha a gyerekosztály is abstract)
    public abstract void Eljaras(int param1, string param2); 

    // protected konstruktor, így csak a gyermek osztályokban érhető el
    protected OsztalyNeve(/*paraméterek*/) 
    {
        // konstruktor
    }
}
```
---
### BEÉPÍTETT INTERFACEK
### `ICloneable`
Segítségével vissza tudunk adni egy példányt, ami ugyanazokkal a tulajdonságokkal rendelkezik mint a példányunk, csak más referencia címre kerül.
```csharp
public class OsztalyNeve : ICloneable
{
    public int Property1 { get; set; }
    public OsztalyNeve(int property1)
    {
        Property1 = property1;
    }

    public object Clone()
    {
        return new OsztalyNeve(this.Property1);
    }
}
```
---
### `IComparable, IComparable<T>`
Segítségével össze tudunk hasonlítani 2 példányt, és meg tudjuk mondani, hogy az egyik nagyobb-e mint a másik.
```csharp
public class OsztalyNeve : IComparable<OsztalyNeve>
{
    public int Property1 { get; set; }
    public OsztalyNeve(int property1)
    {
        Property1 = property1;
    }

    public int CompareTo(OsztalyNeve other)
    {
        if (other == null) return 1;

        if (this.Property1 > other.Property1) return 1; // 1 ha this nagyobb
        else if (this.Property1 < other.Property1) return -1; // -1 ha other nagyobb
        else return 0; // 0 ha egyenlő
    }
}
```
vagy
```csharp
public class OsztalyNeve : IComparable
{
    public int Property1 { get; set; }
    public OsztalyNeve(int property1)
    {
        Property1 = property1;
    }

    public int CompareTo(object obj)
    {
        if (obj == null) return 1;
        if (obj is not OsztalyNeve) throw new Exception("A paraméter nem OsztalyNeve típusú!");

        OsztalyNeve other = (OsztalyNeve)obj;
        if (this.Property1 > other.Property1) return 1;
        else if (this.Property1 < other.Property1) return -1;
        else return 0;
    }
}
```
egysorban
```csharp
public int CompareTo(OsztalyNeve other)
{
    if (other == null) return 1;

    return this.Property1.CompareTo(other.Property1);
}
```
---
### `IEnumerable<T>`
Segítségével betudjuk járni a konténerosztályt egy kivánt módon.
```csharp
public class KontenerOsztalyNeve : IEnumerable<OsztalyNeve>
{
    private List<OsztalyNeve> lista = new List<OsztalyNeve>();
    public IEnumerator<OsztalyNeve> GetEnumerator()
    {
        foreach (OsztalyNeve osztalyNeve in lista)
        {
            yield return osztalyNeve;
            // extra ha klón
            // yield return (OsztalyNeve)osztalyNeve.Clone();
        }
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
}
```
IEnumerable használata főprogramban
```csharp
KontenerOsztalyNeve kontener = new KontenerOsztalyNeve();
foreach (OsztalyNeve osztalyNeve in kontener)
{
    Console.WriteLine(osztalyNeve.Property1);
}
```
---
### LINQ, Lambda
Az összes lekérdezés egymásra kapcsolva is megvalósítható.
pl. `lista.Where(x => x.Property1 > 5).Select(x => x.Property2).ToList();`
```csharp
public class KontenerOsztalyNeve : IEnumerable<OsztalyNeve>
{
    private List<OsztalyNeve> lista = new List<OsztalyNeve>();

    public OsztalyNeve MaxValamiAlapjan()
    {
        return lista.MaxBy(x => x.Property1);
        // min ugyanígy csak MinBy
    }

    public int MaxErtek()
    {
        return lista.Max(x => x.Property1);
        // min ugyanígy csak Min
    }

    public float Atlag()
    {
        return lista.Average(x => x.Property1);
    }

    public List<OsztalyNeve> SzurtLista()
    {
        return lista.Where(x => x.Property1 > 5).ToList();
    }

    public List<OsztalyNeve> ListaVisszaAdvaKlonokkal()
    {
        return lista.Select(x => (OsztalyNeve)x.Clone()).ToList();
    }

    public List<OsztalyNeve> ListaRendezve()
    {
        return lista.OrderBy(x => x.Property1)
                    .ThenByDescending(x => x.Property2)
                    .ToList();
        // OrderBy -> növekvő
        // OrderByDescending -> csökkenő
        // ThenBy -> növekvő
        // ThenByDescending -> csökkenő
        // az első rendezés után a többinél a thenBy...-t kell használni
    }

}
```
---
### Delegate, Func, Predicate
```csharp
public delegate int DelegateNeve(int param1, int param2);

public class OsztalyNeve
{
    public int Muvelet(DelegateNeve muvelet, int a, int b)
    {
        return muvelet(a, b);
    }
}

Func <int, int, int> osszead = (a, b) => a + b;
Func <int, int, int> kivon = (a, b) => a - b;
Func <int, int, int> szoroz = (a, b) => a * b;
Predicate <int> paros = (a) => a % 2 == 0; // egyenlő Func<int, bool> lenne

static int Osszead(int a, int b)
{
    return a + b;
}

static int Kivon(int a, int b)
{
    return a - b;
}

static void Main(string[] args)
{
    OsztalyNeve osztaly = new OsztalyNeve();

    // delegate használata (delegate paraméter átadása)
    int osszeadas = osztaly.Muvelet(Osszead, 5, 3);
    int kivonas = osztaly.Muvelet(Kivon, 5, 3);
    int szorzas = osztaly.Muvelet((a, b) => a * b, 5, 3); // lambda kifejezés

    // Func használata ugyanúgy mintha sima metódus lenne
    int osszeadasFunc = osszead(5, 3);
    int kivonasFunc = kivon(5, 3);
    int szorzasFunc = szoroz(5, 3);
    
    // Predicate használata
    bool parosE = paros(4); // true
}
```
---
### KIVÉTELKEZELÉS
### saját kivétel
első módszer -> saját kivétel osztály létrehozása
```csharp
public class SajatKivetel : Exception
{
    public SajatKivetel() : base() { }
    public SajatKivetel(string uzenet) : base(uzenet) { }
}
```
második módszer -> `throw new SajatKivetel();` -> jobb klikk -> `generate exception in new file`

### try catch
```csharp
try
{
    // kód, ami kivételt dobhat
}
catch (SajatKivetel e) // fentről lefelé a legspecifikusabb kivételt kell kezelni
{
    Console.WriteLine(e.Message);
}
catch (Exception e)
{
    Console.WriteLine(e.Message);
}
finally
{
    Console.WriteLine("Ez a blokk mindig lefut.");
}
```
### Fájl név nem létezik ...
```csharp
// ez a sor dobhatja tehát ezt tesszük a try blokkba
StreamReader sr = new StreamReader("fajl.txt"); 
```

 ```csharp
StreamReader sr = null; // try blokkon kívül deklaráljuk, hogy finallyban tudjuk zárni
try
{
    sr = new StreamReader("fajl.txt");
}
catch (FileNotFoundException e)
{
    Console.WriteLine(e.Message);
}
finally
{
    if (sr != null) sr.Close();
}
```
