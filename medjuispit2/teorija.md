# NULL vrijednosti

Ukratko:
Gdje god da se pojavi *NULL* vrijednost u aritmetickim izrazima
rezultat tog aritmetickog izraza je *NULL*.

Gdje god da se pojavi *NULL* vrijednost u poredjenju, rezultat
tog poredjenja je *UNKNOWN*.

Kada povezujemo vise tih poredjenja sa logickim operatorima 
*AND*, *OR*, *NOT*, imamo trovalentnu logiku (tabele).

Kada koristimo osnovne operacije aritmeticke algebre i projekciju
mi zapravo trazimo duplikate (iste n-torke) i primjenjujemo 
definiciju kopije.

Kada radimo selekciju spajanja, onda ispitujemo uslove, npr da 
li je nesto jednako necemu (izrazi poredjenja).
Ako se pojave *NULL* vrijednosti u takvim uslovima, rezultat ce 
biti *UNKNOWN*.
Da bi se pojavila kao rezultat selekcije odn spajanja n-torka 
onda se uslov selekcije odn spajanja mora evaluirati kao true.

# INTEGRITET


# PITANJA SA ROKOVA

> 18.12.2018 C

> [!NOTE]
> ***Koje su strategije odrzavanja referencijskog integriteta za kriticne 
> operacije? Za koju od strategija postoji ogranicenje u primjeni i zasto? (3b)***

Kritične operacije su: 

- **brisanje** i
- **ažuriranje** 

n-torke u roditeljskoj (pozivanoj) relaciji čiji se primarni ključ referencira 
kao strani ključ u drugim relacijama.

Glavne strategije su:

- ***Zabrana operacije (NO ACTION / RESTRICT)***

    - Brisanje ili izmjena ciljne n-torke se odbija ako postoje zavisne (pozivajuce)
    n-torke; korisnik dobije gresku i nista se ne mijenja.

- ***Kaskadna promjena (CASCADE)***

    - `ON DETELE CASCADE`: pri brisanju ciljne n-torke automatski se brisu sve 
    pozivajuce n-torke koje se na nju pozivaju.

    - `ON UPDATE CASCADE`: pri promjeni primarnog kljuca roditelja, automatski se 
    azuriraju vrijednosti stranih kljuceva u svim pozivajucim n-torkama.

- ***Postavljanje na NULL (SET NULL)***

    - `ON DELETE / ON UPDATE SET NULL`: strani ključ u svim pozivajućim n-torkama 
    se postavlja na *NULL*, pod uslovom da je strani ključ dozvoljeno da bude NULL 
    (atribut je nullable).

- ***Postavljanje na podrazumijevanu vrijednost (SET DEFAULT)***

    - `ON DELETE/ON UPDATE SET DEFAULT`: strani ključ se postavlja na definisanu 
    podrazumijevanu vrijednost, koja mora biti ili *NULL* ili primarni ključ neke 
    postojeće n-torke roditeljske relacije.

Stategija koja ima ogranicenje i razlog:

- ***Stategija SET NULL i SET DEFAULT imaju ogranicenje primjene:***

    - *SET NULL* se ne smije koristiti kada strani kljuc ne smije biti *NULL*,
    tj. kada je strani kljuc dio primarnog kljuca pozivajuce relacije 
    (entitetski integritet + ogranicenje na *NULL*).

    - *SET DEFAULT* se ne smije koristiti ako podrazumijevana vrijednost nije 
    dozvoljena (nema definisan *DEFAULT* ili ne zadovoljava referencijski integritet),
    tj. ne postoji odgovarajuci primarni kljuc u roditeljskoj relaciji).

---

> 05.12.2023 A

> [!NOTE]
> - ***Koje su strategije odrzavanja referencijskog integriteta za kriticne 
operacije? Za koju od strategija postoji ogranicenje u primjeni i zasto? (3b)***

> 05.12.2023 A

> [!NOTE]
> ***Nabrojite i jednom recenicom objasnite osnovna integritetska ogranicenja? (3b)***

Osnovna integritetska ograničenja su:

- ***Entitetski integritet***

    - Primarni ključ relacije ne smije imati vrijednost *NULL* i mora biti jedinstven,
    jer preko njega identifikujemo svaku n-torku posebno.

- ***Integritet ključa (key integrity)***

    - Za svaki kandidatni ključ relacije važi da se njegove vrijednosti ne smiju 
    ponavljati; nema dvije n-torke s istom vrijednosti ključnog atributa ili 
    skupa atributa.

- ***Referencijski integritet***

    Svaka vrijednost stranog ključa u pozivajućoj relaciji mora ili odgovarati 
    postojećem primarnom ključu u pozivanoj relaciji, ili biti *NULL*
    (ako je to dozvoljeno).
    ​
- ***Domenski integritet***

    Vrijednosti atributa moraju pripadati svojoj domeni, tj. tipu i dozvoljenom 
    rasponu/skupu vrijednosti (npr. cjelobrojni raspon, skup kodova, dozvoljeni 
    opseg ocjena itd.).

- ***Ograničenja NULL vrijednosti***

    Za određene atribute može se dodatno propisati da ne smiju imati *NULL* 
    vrijednosti (npr. `NOT NULL` za ime, prezime, obavezne atribute).

- ***Opća (generalna) korisnička ograničenja***

    Dodatna pravila koja definišu odnose između atributa 
    (npr. `starost ≥ staž + 16 i starost ≤ staž + 65`), obično se implementiraju 
    pomoću `CHECK` ili složenijom logikom u aplikaciji/okidačima.

---

> 14.12.2017 A

> [!NOTE]
> ***Sta podrazumijevamo pod pojmom integritet baze podataka i koja 
pravila integriteta postoje?) (3b)***

- ***Integritet baze podataka***

    - Integritet označava ispravnost, konzistentnost i istinitost podataka u bazi, 
    tako da baza odražava realno stanje i da nema kontradiktornih ili „nemogućih” 
    podataka.

    - Integritet se narušava greškama korisnika, aplikacije ili namjernom sabotažom,
    pa se uvode integritetska ograničenja da to spriječe pri svakoj operaciji 
    izmjene podataka.

- ***Pravila integriteta (grupe ograničenja)***

    - Osnovna integritetska ogranicenja:
        - Entitetski integritet, 
        - Integritet ključa, 
        - Referencijski integritet, 

    - Korisnicka integritetska ogranicenja:
        - Domenski integritet, 
        - Ograničenja na NULL i 
        - Opća korisnička integritetska ograničenja.


> [!NOTE]
> ***Sta podrazumijevamo pod pojmom horizontalne normalizacije relacija? 
Ilustrovati na vlastitom primjeru. (3b)*** 

- ***Pojam:***

    - Horizontalna normalizacija znači „razbijanje” jedne relacije na više relacija 
    tako što se n-torke dijele po vrijednosti nekog atributa (npr. tip člana), 
    a zatim se za svaki podskup n-torki zadržavaju samo atributi koji za tu grupu 
    imaju smisla (projekcija).

- ***Primjer:***

    - Početna relacija: 
    `CLAN(clanBr, prezime, ime, postBr, adresa, vr, matBrSt, nastZv)`, gdje atribut 
    `matBrSt` ima smisla samo za studente, a `nastZv` samo za nastavnike.

    - Horizontalnom normalizacijom naprave se dvije relacije:

    `STUDENT(clanBr, prezime, ime, postBr, adresa, vr, matBrSt)` – sadrži n-torke 
    gdje je `vr = 'S'` i samo atribut `matBrSt` od spornih.

    `NASTAVNIK(clanBr, prezime, ime, postBr, adresa, vr, nastZv)` – sadrži n-torke 
    gdje je `vr = 'N'` i samo `nastZv`, čime se izbjegavaju besmislene *NULL* 
    vrijednosti

---

> 14.12.2017 B

> [!NOTE]
>***Navedite pravila za rukovanje NULL vrijednostima u osnovnim logickim
operacijama, relacijskim i aritmetickim izrazima? (3b)***

- ***Aritmetički izrazi***

    - Ako je jedan ili oba operanda *NULL*, rezultat aritmetičkog izraza je *NULL*
    `(npr. 10 + NULL → NULL, NULL * 1 → NULL, NULL – NULL → NULL)`.

    - Isto važi i za unarne `+` i `−`: `+NULL i −NULL` daju rezultat *NULL*.

- ***Relacijski izrazi (poređenja)***

    - Operatori: `>`, `<`, `>=`, `<=`, `=`, `<>` rade u trovalentnoj logici 
    (*true*, *false*, *unknown*).

    - Ako je bar jedan operand NULL, rezultat poređenja je UNKNOWN 
    (npr. NULL ≥ 15.5 → unknown, NULL = NULL → unknown, NULL <> NULL → unknown).

- ***Logičke operacije (AND, OR, NOT – trovalentna logika)***

    - AND:

        - true AND true → true; true AND unknown → unknown; true AND false → false.
        - unknown AND unknown → unknown; unknown AND false → false; false AND bilo 
        šta → false.

    - OR:
        - true OR bilo šta → true; unknown OR unknown → unknown; unknown OR 
        false → unknown; false OR false → false.

    - NOT:
        - NOT true → false; NOT false → true; NOT unknown → unknown.

| ***OR*** | *true* | *unknown* | *false* |
|----------|--------|-----------|---------|
| *true*   | true   |  true     |  true   |
| *uknown* | true   | uknown    | uknown  |
| *false*  | true   | uknown    | false   |

| ***AND*** | *true* | *unknown* | *false* |
|----------|--------|-----------|---------|
| *true*   | true   |  uknown   |  false  |
| *uknown* | uknwon |  uknown   |  false  |
| *false*  | false  |  false    |  false  |

| ***NOTE*** |       |
|------------|-------|
|  *true*    | false |
| *uknown*   | uknown|
| *false*    | true  |
        ​
WHERE uslov selektirа samo n-torke gdje je izraz true; n-torke za koje je izraz 
false ili unknown se odbacuju.

> [!NOTE] 
> ***Navedite moguce strategije odrzavanja referensijskog integriteta pri 
brisanju n-torke. Koje od navedenih strategija se ne smiju koristiti ukoliko
se brise n-torka ciji je primarni kljuc strani kljuc neke druge relacije i 
ujedno dio njezinog primarnog kljuca? (3b)***

Moguće strategije pri brisanju n-torke u pozivanoj relaciji (roditelj):

- ***NO ACTION / RESTRICT***

    - Brisanje roditelja je zabranjeno ako postoje djeca koja ga referenciraju;
    pozivajuće n-torke ostaju netaknute.

- ***CASCADE (ON DELETE CASCADE)***

    - Pri brisanju roditeljske n-torke brišu se i sve pozivajuće n-torke 
    (kaskadno brisanje).

- ***SET NULL (ON DELETE SET NULL)***

    - Strani ključ u djeci se postavlja na NULL (dozvoljeno samo ako taj atribut 
    smije biti NULL).

- ***SET DEFAULT (ON DELETE SET DEFAULT)***

    - Strani ključ se postavlja na podrazumijevanu vrijednost (mora biti dozvoljena 
    i konzistentna sa referencijskim integritetom).

Kada se briše n-torka čiji je primarni ključ ujedno strani ključ neke druge relacije 
i dio njenog primarnog ključa, strategije *SET NULL* i *SET DEFAULT* se ne smiju 
koristiti, jer ključna polja primarnog ključa u pozivajućoj relaciji ne smiju biti 
*NULL* niti smiju dobiti vrijednost koja ne čini validan primarni ključ 
(*entitetski integritet*).

---

> 12.12.2014 A

> [!NOTE]
> ***Koja su to korisnicka pravila integriteta i na koji nacin se definisu 
u SQLU-u? Navedite vlasitit primjer. (3b)***

- ***Korisnička pravila integriteta i definisanje u SQL-u***

    - Korisnička integritetska ograničenja su opća, dodatna pravila koja modeluju 
    specifičnu poslovnu logiku, najčešće odnose između vrijednosti više atributa 
    u istoj n-torci.

    - U SQL-u se obično definišu pomoću `CHECK` ograničenja na nivou kolone ili 
    tabele, a složenija pravila mogu ići u okidače (TRIGGER) ili aplikacijski kod.

- ***Primjer:***

    - Relacija `ZAPOSLENIK(sifra, prezime, starost, staz)` sa pravilom: 
    `starost ≥ staz + 16 i starost ≤ staz + 65`.

U SQL-u:

```SQL
CREATE TABLE zaposlenik (
  sifra   INT PRIMARY KEY,
  prezime CHAR(20),
  starost INT,
  staz    INT,
  CHECK (starost >= staz + 16 AND starost <= staz + 65)
);
```
Ovo CHECK ograničenje je tipično korisničko pravilo integriteta.


> [!NOTE]
> - ***Sta su to regularni i slabi entiteti i na koji nacin se prikazuju u 
ER modelu? (3b)***

- ***Regularni (jaki) entitet:***

    - Regularni entitet je entitet koji može postojati „sam za sebe“, 
    bez postojanja nekog drugog entiteta od kojeg bi zavisio egzistencijalno.

    - Opisuje se vlastitim atributima i ima svoj primarni ključ formiran isključivo 
    od svojih atributa (npr. STUDENT s ključem matBrSt, ZAPOSLENIK s ključem matBrZap).

    - U ER modelu se prikazuje običnim (jednostrukim) pravougaonikom, a ključni 
    atribut (ili atributi) se podvlače u skici entiteta ili navode u šemi entiteta 
    (npr. STUDENT = matBrSt, prezime, ime, jmbg; PK = {matBrSt}).

- ***Slabi entitet (egzistencijalno i identifikacijski slab)***

    - Slabi entitet ne može postojati bez entiteta „vlasnika“: njegovo postojanje 
    je egzistencijalno zavisno od drugog entiteta 
    (npr. DIJETE koje je uzdržavano od određenog ZAPOSLENIKA).

    - Kod identifikacijski slabog entiteta, vlastiti atributi nisu dovoljni za 
    jedinstvenu identifikaciju; za identifikaciju se koristi kombinacija ključnih 
    atributa entiteta vlasnika i vlastitih atributa (djelimični ključ), npr. 
    ključ DIJETE = (sifZap, imeDj, godRod).
    ​

- ***Grafički prikaz slabog entiteta u ER modelu***

    - Slabi entitet se u ER dijagramu prikazuje dvostruko uokvirenim pravougaonikom, 
    čime se naglašava njegova zavisnost.

    - Identifikujuća veza (veza s entitetom vlasnikom, npr. „Uzdržava“ između 
    ZAPOSLENIK i DIJETE) se često crta dvostrukim rombom ili dvostrukom linijom, 
    a atributi koji su dio djelimičnog ključa slabog entiteta posebno se označavaju 
    (npr. drugačijim podvlačenjem u literaturi).

---

>?
>22.12.2021 A

> [!NOTE] 
>***Objasnite sta su integritetska ogranicenja i sta omogucavaju njihove 
definicije u bazi podataka. (3b)***



# Zadaci sa relacijama i ispisom

> [!NOTE]
> ***Zadane su relacije:***
> ***Napisite rezultate obavljanja sljedecih operacija 
> (SQL upite nije potrebno pisati):***

```text
r(A    B    C)      s(A    B    C    D)     t(D    E)
--------------      -------------------     ---------
  1    X    ab        4    Z    ac  1.2     5.3   null
  2   null null      null  Z   null 0.5     null  4.6
 null  X    ab        3   null  aa  null    4.6    1
  4    Z   null       2   null  bb  0.2     5.3    4
  4    Z    ac       null  X    ab  null
  3    Y    aa        1    X    ab  5.3
  2   null  bb        1    X    ac  null
```
<!-- a) π_D (r |><| s) ∩ π_D (t) -->
a) $a)\ \pi_D (r ⋈ s) \cap \pi_D (t)$ (1b)

Rjesenje: {5.3}


b) `SELECT SUM(D), COUNT(DISTINCT A) FROM s WHERE C <> 'ac'` (1b)

Rezultat upita je jedna torka:
```
text
SUM(D)   COUNT(DISTINCT A)
 5.5              3
``````
[Detaljni koraci: pr1](./relacije/pr1.txt)

---

```text
r(A    B)      s(A    B    C    D)     t(D    E)
---------      -------------------     ---------
  a    m         b    m   null null      3    1
 null  m         a    n   null  5        5   null
  b    n         b    n    4    9        7    1
 null null       a    m    6    3       null null
  c   null       c   null  4    5        5    4
  b    h        
```
<!-- a) π_(A,B) (s |><| t) ∩ r -->
a) $\pi_{A,B} (s ⋈ t) \cap r$

b) `SELECT SUM(DISTINCT C), COUNT(D) FROM s WHERE B <> 'n'`


---

```text
r(A    B)      s(A    B    C    D)     t(D    E)
---------      -------------------     ---------
  a    m         b    m   null null      3    1
 null  m         a    n   null  5        5   null
  b    n         b    n    4    9        7    1
 null null       a    m    6    3       null null
  c   null       c   null  4    5        5    4
  b    m        
```
<!-- a) π_(A,B) (s |><| t) ∩ r -->
a) $\pi_{D} (r ⋈ s) \cap \pi_{t}$

b) `SELECT SUM(DISTINCT E), COUNT(D) FROM t WHERE E < 4`

---

```text
r(A    B   C)      s(A    B    D)     t(D    E)
-------------      --------------     ---------
 null null c        null null  d        d    e
  a   null c         k    b   null     null  g
  n   null c         k    b    d
  m   null f
  k    b   f

```
a) $\sigma_{\text{A <> v OR B <> b}}(r)$

b) $\pi_{A,B}(r) \setminus \pi_{A,B}(s)$






