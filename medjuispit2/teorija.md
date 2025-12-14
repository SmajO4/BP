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

- ***Koje su strategije odrzavanja referencijskog integriteta za kriticne 
operacije? Za koju od strategija postoji ogranicenje u primjeni i zasto?*** (3b)

- ***Zadane su relacije:***

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

***Napisite rezultate obavljanaj sljedecih operacija (SQL upite nije potrebno pisati):***

<!-- a) π_D (r |><| s) ∩ π_D (t) -->
a) $a)\ \pi_D (r ⋈ s) \cap \pi_D (t)$ (1b)

b) `SELECT SUM(D), COUNT(DISTINCT A) FROM s WHERE C <> 'ac'` (1b)

---

> 05.12.2023 A

- ***Koje su strategije odrzavanja referencijskog integriteta za kriticne 
operacije? Za koju od strategija postoji ogranicenje u primjeni i zasto? (3b)***

> 05.12.2023 A

- ***Nabrojite i jednom recenicom objasnite osnovna integritetska ogranicenja? (3b)***

---

> 14.12.2017 A

- ***Sta podrazumijevamo pod pojmom horizontalne normalizacije relacija? 
Ilustrovati na vlastitom primjeru. (3b)*** 

- ***Zadane su relacije:***

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
***Napisite rezultate obavljanaj sljedecih operacija (SQL upite nije potrebno pisati):***

<!-- a) π_(A,B) (s |><| t) ∩ r -->
a) $\pi_{A,B} (s ⋈ t) \cap r$

b) `SELECT SUM(DISTINCT C), COUNT(D) FROM s WHERE B <> 'n'`















