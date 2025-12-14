Napisati po jednu SQL naredbu za svaki sljedeci zadatak.
Upiti moraju biti neovisni o datumu izvodjenja.
Nece se priznavati rjesenja u kojima se nepotrebno koriste podupiti.

---
> 18.12.2018 C

```
trgovina:                     proizvod:
*sifTrgovina                  *sifProizvod 
 nazivTrgovina NOT NULL        nazivProizvod
 adresa                        

proizvodUTrgovini       dnevnik:
*sifTrgovina            *sifTrgovina
*sifProizvod            *sifProizvod
 datum NOT NULL         *datum
 cijena NOT NULL        cijena NOT NULL

```

a) Ispisati rang listu proizvoda u trgovinama prema broju izmjene cijene 
u toku posljednih 45 dana. Ispisivati naziv proizvoda i naziv trgovine,
te broj izmjena cijene. Upit mora biti neovistan o datumu kada se upit 
izvodi. Blize vrhu liste se nalaze proizvodi sa vise izmjena cijene, a 
proizvode sa eventualnim istim brojem izmjena poredati abecedno po nazivu
proizvoda i nazivu trgovine. (2b)

```SQL
SELECT 
    p.nazivProizvod,
    t.nazivTrgovina,
    COUNT(*) AS broj_izmjena
FROM dnevnik d
JOIN proizvod p      ON d.sifProizvod  = p.sifProizvod
JOIN trgovina t      ON d.sifTrgovina  = t.sifTrgovina
WHERE d.datum >= CURDATE() - INTERVAL 45 DAY
GROUP BY 
    p.sifProizvod,
    t.sifTrgovina,
    p.nazivProizvod,
    t.nazivTrgovina
ORDER BY 
    broj_izmjena DESC,
    p.nazivProizvod ASC,
    t.nazivTrgovina ASC;
```

b) Proizvodima u trgovinama cija cijena nije mijenjana u proteklih mjesec
dana smanjiti cijenu za 10% (2b)

```SQL
UPDATE proizvodUTrgovini put
JOIN dnevnik d 
  ON  d.sifTrgovina = put.sifTrgovina
  AND d.sifProizvod = put.sifProizvod
GROUP BY 
    put.sifTrgovina,
    put.sifProizvod
HAVING MAX(d.datum) < CURDATE() - INTERVAL 1 MONTH
   OR MAX(d.datum) IS NULL
SET put.cijena = put.cijena * 0.9;
```
Drugi nacin:
```SQL
UPDATE proizvodUTrgovini
SET cijena = cijena * 0.9
WHERE (sifTrgovina, sifProizvod) IN (
    SELECT sifTrgovina, sifProizvod
    FROM dnevnik
    WHERE datum >= CURDATE() - INTERVAL 45 DAY
    GROUP BY sifTrgovina, sifProizvod
    HAVING COUNT(*) > 3
);
```

c) Postojecu relaciju IZMJENE (sifTrgovina, sifProizvod, mjesec, godina,
brIzmjena) napuniti podacima o svim proizvodima u trgovinama i pripadnim
mjesecima i godinama u kojima je vise od pet puta tokom doticnog mjeseca 
mijenjana cijena proizvoda u toj trgovini.

```SQL
INSERT INTO IZMJENE (sifTrgovina, sifProizvod, mjesec, godina, brIzmjena)
SELECT
    d.sifTrgovina,
    d.sifProizvod,
    MONTH(d.datum)  AS mjesec,
    YEAR(d.datum)   AS godina,
    COUNT(*)        AS brIzmjena
FROM dnevnik d
GROUP BY
    d.sifTrgovina,
    d.sifProizvod,
    YEAR(d.datum),
    MONTH(d.datum)
HAVING COUNT(*) > 5;

```
---

> 18.12.2018 C

```
PLOVIDBA                                            LUKA        BROD
*regBrBrod    - registarski broj broda              *sifLuka    *regBrBrod    - registarski broj broda
*datPolaska   - datum poslaska broda                 nazLuka     imeBrod      - ime broda
 sifLukaOd    - sifra luke iz koje broj polazi                   nosivost     - najveca ukupna tezina koju brod moze ukrcati (u t)
 sifLukaDo    - sifra luke u koju brod dolazi                    sifMaticLuka - sifra maticne luke broda (luka u kojoj je registrovan)
 datDolaska   - datum dolaska broda u luku u koju plovi
 ukUkrcTezina - ukupna ukrcana tezina na brod u datoj plovidbi

```

a) Ispisati rang listu brodova prema ukupnoj tezini tereta koju su prevozili 
sa svim svojim plovidbama. Ispisati registarski broj broda, ime broda i ukupnu
i prosjecnu tezinu tereta. Blize pocetku liste nalaze se brodovi s vecom 
ukupnom tezinom tereta. Brodove koji eventualno imaju jednaku ukupnu tezinu
tereta poredati abecedno po imenu broda (2b)

```SQL
SELECT 
    b.regBrBrod,
    b.imeBrod,
    SUM(p.ukUkrcTezina) AS ukupna_tezina,
    AVG(p.ukUkrcTezina) AS prosjecna_tezina
FROM PLOVIDBA p
JOIN BROD b ON b.regBrBrod = p.regBrBrod
GROUP BY 
    b.regBrBrod,
    b.imeBrod
ORDER BY 
    ukupna_tezina DESC,
    b.imeBrod ASC;
```

b) Nosivost brodova koji su obavili vise od 1000 plovidbi smanjiti za 10% (2b)

```SQL
UPDATE BROD b
JOIN (
    SELECT regBrBrod, COUNT(*) AS br_plovidbi
    FROM PLOVIDBA
    GROUP BY regBrBrod
    HAVING COUNT(*) > 1000
) p ON p.regBrBrod = b.regBrBrod
SET b.nosivost = b.nosivost * 0.9;
```
Drugi nacin:
```SQL
UPDATE BROD
SET nosivost = nosivost * 0.9
WHERE regBrBrod IN (
  SELECT regBrBrod
  FROM PLOVIDBA
  GROUP BY regBrBrod
  HAVING COUNT(*) > 1000
);
```

c) Obrisati podatke o svim lukama iz kojih nisu isplovaljavali brodovi nosivosti
vece od 100t (2b)

```SQL
DELETE FROM LUKA
WHERE sifLuka NOT IN (
    SELECT DISTINCT p.sifLukaOd
    FROM PLOVIDBA p
    JOIN BROD b ON b.regBrBrod = p.regBrBrod
    WHERE b.nosivost > 100
);
```
---

> 14.12.2017 B

```
BIBLIOTEKA:

KNJIGA              STUDENT             PREDMET
*sifKnj NOT NULL    *JMBG NOT NULL      *sifPred NOT NULL
 naslov NOT NULL     ime NOT NULL        nazPred NOT NULL 
 cijena              prezime NOT NULL

LITERATURA
*sifPred NOT NULL       - sifra predmeta
*sifKnj NOT NULL        - sifra knjige
 rbr NOT NULL           - redni broj vaznosti literature za predmete
                          (za svaki predmet krece od 1)
 obavezna NOT NULL      - D-obavezna literatura, N-neobavezna literatura

POSUDBA
*JMBG     NOT NULL    - JMBG studenta
*sifKnj   NOT NULL    - sifra knjige
*datumPos NOT NULL    - datum posudbe
datumDo   NOT NULL    - rok vracanja knjige (datum do kojeg student mora 
                        vratiti knjigu)
datumVra              - datum vracanja knjige

```

a) Ispisati rang listu knjiga prema popularnosti posudjivanja njihovih 
primjeraka u prosloj godini. Ispisati sifru, naslov i ukupan broj posdubi
knjige. Blize vrhu liste se nalaze knjige koje imaju najvise posudbi.
Knjige sa jednakim brojem posudbi poredati abecedno po naslovu (2b)

```SQL
SELECT 
    k.sifKnj,
    k.naslov,
    COUNT(*) AS broj_posudbi
FROM POSUDBA p
JOIN KNJIGA k ON k.sifKnj = p.sifKnj
WHERE YEAR(p.datumPos) = YEAR(CURDATE()) - 1
GROUP BY 
    k.sifKnj,
    k.naslov
ORDER BY 
    broj_posudbi DESC,
    k.naslov ASC;
```

b) Ispisati JMBG, ime, prezime studenta i ukupnu vrijednost knjiga cije 
je primjerke student posudjivao, za one studente za koje je ta ukupna 
vrijednost najveca (2b)

```SQL
SELECT 
    s.JMBG,
    s.ime,
    s.prezime,
    SUM(k.cijena) AS ukupna_vrijednost
FROM POSUDBA p
JOIN STUDENT s ON s.JMBG = p.JMBG
JOIN KNJIGA k ON k.sifKnj = p.sifKnj
GROUP BY 
    s.JMBG,
    s.ime,
    s.prezime
HAVING SUM(k.cijena) = (
    SELECT MAX(ukupno)
    FROM (
        SELECT SUM(k2.cijena) AS ukupno
        FROM POSUDBA p2
        JOIN KNJIGA k2 ON k2.sifKnj = p2.sifKnj
        GROUP BY p2.JMBG
    ) x
);

```
Drugi nacin:

```SQL
SELECT s.JMBG, s.ime, s.prezime, SUM(k.cijena) AS ukupna_vrijednost
FROM POSUDBA p
JOIN STUDENT s ON s.JMBG = p.JMBG
JOIN KNJIGA k ON k.sifKnj = p.sifKnj
GROUP BY s.JMBG, s.ime, s.prezime
HAVING SUM(k.cijena) >= ALL (
    SELECT SUM(k2.cijena)
    FROM POSUDBA p2
    JOIN KNJIGA k2 ON k2.sifKnj = p2.sifKnj
    GROUP BY p2.JMBG
);
```

c) Knjigama za koje nije navedena cijena, a posudjene su vise od 30 puta 
u jednoj godini, postaviti cijenu na 50KM (2b)

```SQL
UPDATE KNJIGA k
JOIN (
    SELECT 
        sifKnj,
        YEAR(datumPos) AS godina,
        COUNT(*) AS br_posudbi
    FROM POSUDBA
    GROUP BY 
        sifKnj,
        YEAR(datumPos)
    HAVING COUNT(*) > 30
) p ON p.sifKnj = k.sifKnj
SET k.cijena = 50
WHERE k.cijena IS NULL;
```
Drugi nacin:
```SQL
UPDATE KNJIGA
SET cijena = 50
WHERE cijena IS NULL
  AND sifKnj IN (
        SELECT sifKnj
        FROM POSUDBA
        GROUP BY sifKnj, YEAR(datumPos)
        HAVING COUNT(*) > 30
  );
```

d) Postojecu relaciju kasnjenje koja ima istu shemu kao relacija knjiga 
napuniti podacima o knjigama koje su trenutno posudjene, a koje nikada 
nisu vracene na vrijeme. (2b)

```SQL
INSERT INTO KASNJENJE (sifKnj, naslov, cijena)
SELECT DISTINCT 
    k.sifKnj,
    k.naslov,
    k.cijena
FROM KNJIGA k
JOIN POSUDBA p ON p.sifKnj = k.sifKnj
WHERE p.datumVra IS NULL
  AND EXISTS (
        SELECT 1
        FROM POSUDBA p2
        WHERE p2.sifKnj = k.sifKnj
          AND p2.datumVra IS NOT NULL
          AND p2.datumVra > p2.datumDo
  );
```
---

> 14.12.2017 A

```
PREVOZ

VOZAC                                       TERET
*sif_vozac    - sif vozaca                  *sif_teret  - sifra tereta
prez_vozac    - prezime vozaca               naz_teret  - naziv tereta
ime_vozac     - ime vozaca                   tezina     - tezina tereta u tonama
      
VOZNJA                                      VOZILO
*sif_voznja   - sifra voznje                *regbr    - registarski broj vozila
sif_vozac     - sifra voazca                 tip      - tip vozila
sif_teret     - sifra tereta                 nosivost - nosivost vozila (u tonama)
datum         - datum obavljanja prevoza
regbr         - registarski broj vozila                   LOKACIJA:
broj_km       - broj predjenih (prevezenih) kilometara    *sif_lok  - sifra lokacije
sif_lok_od    - sifra lokacije od koje se prevozi teret    naz_lok  - naziv lokacije
sif_lok_do    - sifra lokacije do koje se prevozi teret    adresa   - adresa u kojoj se lokacija nalazi
                                                           grad     - grad u kojem se lokacija nalazi
```

a) Ispisati rang listu vozaca prema ukupnom broju kilometara koje je vozaca
prevezao. U ispisu se moraju vidjeti svi podaci o vozacu i ukupan broj km 
koje je vozac prevezao. Vozaci s vise predjenih kilometara trebaju se nalaziti
blizke pocetku liste. Vozace koji su prevezli isti broj kilometara poredati po 
prezimenu i imenu. (2b)

```SQL
SELECT 
    v.sif_vozac,
    v.prez_vozac,
    v.ime_vozac,
    SUM(vo.broj_km) AS ukupno_km
FROM VOZAC v
JOIN VOZNJA vo ON vo.sif_vozac = v.sif_vozac
GROUP BY 
    v.sif_vozac,
    v.prez_vozac,
    v.ime_vozac
ORDER BY 
    ukupno_km DESC,
    v.prez_vozac ASC,
    v.ime_vozac ASC;
```

b) Ispisati sve podatke o vozilima kojima je proteklih 3 godine obavljeno 
vise od 100 voznji i koji su presli vise od 10000km (2b)

```SQL
SELECT 
    vozi.*
FROM VOZILO vozi
JOIN (
    SELECT 
        regbr,
        COUNT(*)     AS br_voznji,
        SUM(broj_km) AS ukupno_km
    FROM VOZNJA
    WHERE datum >= CURDATE() - INTERVAL 3 YEAR
    GROUP BY regbr
    HAVING COUNT(*) > 100
       AND SUM(broj_km) > 10000
) x ON x.regbr = vozi.regbr;
```
Drugi nacin:

```SQL
SELECT *
FROM VOZILO v
WHERE v.regbr IN (
    SELECT regbr
    FROM VOZNJA
    WHERE datum >= CURDATE() - INTERVAL 3 YEAR
    GROUP BY regbr
    HAVING COUNT(*) > 100
       AND SUM(broj_km) > 10000
);
```

c) Svim vozilima koja su barem jednom prevozila teret cija tezina prelazi 10t,
smanjiti podatak o nosivosti tako da im se nosivost umanji za 10% prosjecne 
tezine tereta prevezene na svim njihovim voznjama (2b)

```SQL
UPDATE VOZILO v
JOIN (
    SELECT 
        vo.regbr,
        AVG(t.tezina) AS prosj_tezina
    FROM VOZNJA vo
    JOIN TERET t ON t.sif_teret = vo.sif_teret
    GROUP BY vo.regbr
    HAVING MAX(t.tezina) > 10
) x ON x.regbr = v.regbr
SET v.nosivost = v.nosivost - 0.1 * x.prosj_tezina;
```
Drugi nacin:
```SQL
UPDATE VOZILO
SET nosivost = nosivost - 0.1 * (
    SELECT AVG(t.tezina)
    FROM VOZNJA v
    JOIN TERET t ON t.sif_teret = v.sif_teret
    WHERE v.regbr = VOZILO.regbr
)
WHERE regbr IN (
    SELECT v2.regbr
    FROM VOZNJA v2
    JOIN TERET t2 ON t2.sif_teret = v2.sif_teret
    GROUP BY v2.regbr
    HAVING MAX(t2.tezina) > 10
);
```

d) Obrisati sve podatke iz relacije vozac, za one vozace koji nikada nisu 
vozili na relaciji "Luka Ploce" - "Solana Tuzla" (2b)

```SQL
DELETE FROM VOZAC v
WHERE NOT EXISTS (
    SELECT 1
    FROM VOZNJA vo
    JOIN LOKACIJA l1 ON l1.sif_lok = vo.sif_lok_od
    JOIN LOKACIJA l2 ON l2.sif_lok = vo.sif_lok_do
    WHERE vo.sif_vozac = v.sif_vozac
      AND l1.naz_lok = 'Luka Ploce'
      AND l2.naz_lok = 'Solana Tuzla'
);
```
---

> 12.12.2014 A

```
trgovina:                     proizvod:
*sifTrgovina                  *sifProizvod 
 nazivTrgovina NOT NULL        nazivProizvod
 adresa                        

proizvodUTrgovini       dnevnik:
*sifTrgovina            *sifTrgovina
*sifProizvod            *sifProizvod
 datum NOT NULL         *datum
 cijena NOT NULL        cijena NOT NULL
```

a) Svim proizvodima u trgovinama kojima je u toku posljednih 45 dana cijena 
mijenjana vise od 3 puta umanjiti cijenu za 5% (2b)

```SQL
UPDATE proizvodUTrgovini put
JOIN (
    SELECT 
        sifTrgovina,
        sifProizvod,
        COUNT(*) AS br_izmjena
    FROM dnevnik
    WHERE datum >= CURDATE() - INTERVAL 45 DAY
    GROUP BY sifTrgovina, sifProizvod
    HAVING COUNT(*) > 3
) d ON d.sifTrgovina = put.sifTrgovina
   AND d.sifProizvod = put.sifProizvod
SET put.cijena = put.cijena * 0.95;
```
Drugi nacin:

```SQL
UPDATE proizvodUTrgovini put
SET put.cijena = put.cijena * 0.95
WHERE (put.sifTrgovina, put.sifProizvod) IN (
    SELECT d.sifTrgovina, d.sifProizvod
    FROM dnevnik d
    WHERE d.datum >= CURDATE() - INTERVAL 45 DAY
    GROUP BY d.sifTrgovina, d.sifProizvod
    HAVING COUNT(*) > 3
);
```

b) Postojecu relaciju IZMJENE (sifTrgovina, sifProizvod, mjesec, godina) 
napuniti podacima o proizvodima u trgovinama i pripadnim mjesecima i godinama
u kojima je vise od pet puta tokom doticnog mjeseca mijenjana cijena proizvoda
u toj trgovini (2b)

```SQL
INSERT INTO IZMJENE (sifTrgovina, sifProizvod, mjesec, godina)
SELECT
    sifTrgovina,
    sifProizvod,
    MONTH(datum) AS mjesec,
    YEAR(datum)  AS godina
FROM dnevnik
GROUP BY
    sifTrgovina,
    sifProizvod,
    YEAR(datum),
    MONTH(datum)
HAVING COUNT(*) > 5;
```
---

>? 22.12.2021 A ?

```
PRIJAVE

STUD:             PROVJERA:
*mbrstud          *datProvjera    - datum odrzavanja provjere
 imeStud          *sifPred        - sifra predmeta iz kojeg se odrzava provjera
 prezStud          sifVrProvj     - sifra vrste provjere koja se odrzava
                   vrijemeProvj   - vrijeme pocetka provjere
NASTAVNIK:         trajanje       - trajanje provjere u minutama
*sifNast           sifNast        - sifra nastavnika koji obavlja provjeru
 imeNast
 prezNast         VRSTA PROVJ
                  *sifraVrProvj   - sifra vrste provjere
                   nazVrProvj     - naziv vrste provjere (zavrsni ispit, medjuispit..)

PRED:             PRIJAVA:        
*sifPred          *datProvjera    - datum odrzavanja provjere 
nazPred           *sifPred         - sifra predmeta iz kojeg se odrzava provjera
                  *mbrStud        - maticni broj studenta koji obavlja prijavu
PREDAJE:           datPrijava     - datum kada je obavljena prijava
*sifPred           vrijemePrijava - vrijeme kada je obavljena prijava
*sifNast
 nosilac BOOL   - da li je nosilac predmeta
```

a) Ispisati rang listu predmeta prema broju obavljenih prijava na bilo koju
vrstu provjere u toku tekuce godine. Ispisivati naziv predmeta i broj 
obavljenih prijava, a blize vrhu liste treba da se pojave predmeti koji su 
imali vise prijava. Predmete sa istim brojem prijava poredati po nazivu.
(2b)

```SQL
SELECT 
    pr.nazPred,
    COUNT(*) AS broj_prijava
FROM PRIJAVA p
JOIN PRED pr ON pr.sifPred = p.sifPred
WHERE YEAR(p.datPrijava) = YEAR(CURDATE())
GROUP BY 
    pr.sifPred,
    pr.nazPred
ORDER BY 
    broj_prijava DESC,
    pr.nazPred ASC;
```

b) Svim provjerama koje ne odrzavaju nosioci na predmetu produziti trajanje 
za 20% (2b)

```SQL
UPDATE PROVJERA pv
JOIN PREDAJE pr 
  ON pr.sifPred = pv.sifPred
 AND pr.sifNast = pv.sifNast
SET pv.trajanje = pv.trajanje * 1.2
WHERE pr.nosilac = 0;
```
Drugi nacin:

```SQL
UPDATE PROVJERA
SET trajanje = trajanje * 1.2
WHERE (sifPred, sifNast) IN (
    SELECT sifPred, sifNast
    FROM PREDAJE
    WHERE nosilac = 0   -- ili 'N' ako je tako zapisano
);

```
c) Obrisati sve podatke o provjerama na koje je prijavljeno manje od 5 
studenata (2b)

```SQL
DELETE FROM PROVJERA
WHERE (datProvjera, sifPred) IN (
    SELECT datProvjera, sifPred
    FROM PRIJAVA
    GROUP BY datProvjera, sifPred
    HAVING COUNT(*) < 5
);
```
---

> 05.12.2023 B

Medju ucenicima koji zavrsavaju srednju skolu i zele se upisati na fakultet 
provedena je anketa. U bazi podataka nalaze se relacije:

```
MJESTO                        SKOLA                                       
*pbr      - postanski broj    *sifSkola   - sifra skole
nazMjesto - naziv mjesta       nazSkola   - naziv skole
                               pbrSkola   - postanski broj mjesta u kojem se nalazi skola

                                FAKULTET
                                *sifFakultet    - sifra fakulteta
UCENIK                           nazFakultet    - naziv fakulteta
*jmbg       - JMBG ucenika       pbrFakultet    - postanski broj mjesta u kojem se nalazi
prezUcenik  - prezime ucenika    adresaFakultet - adresa 
imeUcenik   - ime ucenika
sifSkola    - sifra srednje skole koje ucenik pohadja
sifFakultet - sifra fakulteta na kojeg se ucenik zeli upisati
opciUspjeh  - opci uspjeh (ocjena 2-5)
datAnketa   - datum anketiranja ucenika
```

a) Ispisati ukupan broj i prosjecan opci uspjeh ucenika koji pohadjaju 
srednju skolu u mjesecima ciji naziv zapoicnje slovima od S do Z i anketirani
su bilo kojeg radnog dana u posljednjem tromjesjecu prosle godine (3b)

```SQL
SELECT 
    COUNT(*)        AS broj_ucenika,
    AVG(opciUspjeh) AS prosjecan_uspjeh
FROM UCENIK u
JOIN SKOLA s ON s.sifSkola = u.sifSkola
JOIN MJESTO m ON m.pbr = s.pbrSkola
WHERE m.nazMjesto  >= 'S'
  AND m.nazMjesto  <  'Ž'          -- ili 'Z' ovisno o kolaciji
  AND u.datAnketa BETWEEN 
        MAKEDATE(YEAR(CURDATE()) - 1, 1) + INTERVAL 9 MONTH   -- 1.10. prošle godine
    AND MAKEDATE(YEAR(CURDATE()) - 1, 1) + INTERVAL 11 MONTH + INTERVAL 30 DAY  -- 31.12.
  AND DAYOFWEEK(u.datAnketa) BETWEEN 2 AND 6;  -- pon–pet
```

b) Ispisati jmbg, ime i prezime ucenika, naziv mjesta srednje skole i mjesta 
fakulteta za ucenike koji ne pohadjaju srednju skolu u istom mjestu u kojem 
se nalazi fakultet na kojeg se zele upisati (3b)

```SQL
SELECT 
    u.jmbg,
    u.imeUcenik,
    u.prezUcenik,
    mS.nazMjesto  AS mjesto_skole,
    mF.nazMjesto  AS mjesto_fakulteta
FROM UCENIK u
JOIN SKOLA   s  ON s.sifSkola     = u.sifSkola
JOIN MJESTO  mS ON mS.pbr         = s.pbrSkola
JOIN FAKULTET f ON f.sifFakultet  = u.sifFakultet
JOIN MJESTO  mF ON mF.pbr         = f.pbrFakultet
WHERE mS.pbr <> mF.pbr;
```

c) Za svako mjesto u kojem pohadja srednju skolu neki od anktetiranih ucenika
ispisati postanski broj, naziv mjesta, te ukupan broj razlicitih fakulteta na 
koje se zele upisati anketirani ucenici iz tog mjesta. Rezultate poredati tako
da se mjesta s vecim brojem fakulteta na koje se zele upisati anketirani 
ucenici nalaze blice pocetku liste. Mjesta u kojima ima jednak broj razlicitih
fakulteta poredati po abecedi. U ispis ukljuciti samo mjesta u kojima je broj
razlicitih fakulteta koje zele upisati ucenici veci od prosjecnog opceg uspjeha
svih anketiranih ucenika koji ne pohadjaju skolu iz tog mjesta (3b)

```SQL
SELECT 
    m.pbr,
    m.nazMjesto,
    COUNT(DISTINCT u.sifFakultet) AS br_razl_fakulteta
FROM UCENIK u
JOIN SKOLA  s ON s.sifSkola = u.sifSkola
JOIN MJESTO m ON m.pbr      = s.pbrSkola
GROUP BY m.pbr, m.nazMjesto
HAVING COUNT(DISTINCT u.sifFakultet) >
       (
         SELECT AVG(opciUspjeh)
         FROM UCENIK u2
         JOIN SKOLA  s2 ON s2.sifSkola = u2.sifSkola
         JOIN MJESTO m2 ON m2.pbr      = s2.pbrSkola
         WHERE m2.pbr <> m.pbr
       )
ORDER BY 
    br_razl_fakulteta DESC,
    m.nazMjesto ASC;
```

d) Ipisati naziv fakulteta i naziv mjesta u kojem se fakultet nalazi za sve 
fakultete koje ne zeli upisati nijedan ucenik anketiran u tekucoj godini. (3b)

```SQL
SELECT 
    f.nazFakultet,
    m.nazMjesto
FROM FAKULTET f
JOIN MJESTO m ON m.pbr = f.pbrFakultet
WHERE f.sifFakultet NOT IN (
    SELECT DISTINCT u.sifFakultet
    FROM UCENIK u
    WHERE YEAR(u.datAnketa) = YEAR(CURDATE())
);
```

e) Svi ucenici koji su pohadjali srednju skolu Prva opsta gimnazija prebaceni
su u skolu koja ima najmanje anktetiranih ucenika. Ovu promjenu potrebno je 
evidentirati u bazi podataka (3b)

```SQL
UPDATE UCENIK u
SET u.sifSkola = (
    SELECT sMin.sifSkola
    FROM (
        SELECT s2.sifSkola, COUNT(*) AS br_ucenika
        FROM UCENIK u2
        JOIN SKOLA s2 ON s2.sifSkola = u2.sifSkola
        GROUP BY s2.sifSkola
        ORDER BY br_ucenika ASC
        LIMIT 1
    ) AS sMin
)
WHERE u.sifSkola = (
    SELECT s1.sifSkola
    FROM SKOLA s1
    WHERE s1.nazSkola = 'Prva opsta gimnazija'
    LIMIT 1
);
```
Drugi nacin:

```SQL
UPDATE UCENIK
SET sifSkola = (
    SELECT sMin.sifSkola
    FROM (
        SELECT u2.sifSkola, COUNT(*) AS br_ucenika
        FROM UCENIK u2
        GROUP BY u2.sifSkola
        ORDER BY br_ucenika ASC
        LIMIT 1
    ) AS sMin
)
WHERE sifSkola = (
    SELECT s1.sifSkola
    FROM SKOLA s1
    WHERE s1.nazSkola = 'Prva opsta gimnazija'
    LIMIT 1
);
```
---

> 05.12.2023 A 

Data je baza podataka MEHANICARSKA_RAD u kojoj se nalaze relacije:

```
AUTOMOBIL                                     MODEL
*tvBr        - tvornicki broj automobila      *sifProizv  - sifra proizvodjaca automobila
 godProizv   - godina proizvodnje             *nazModel   - naziv modela
 sifProizvod - sifra proizvodjaca automobila   godProizv  - godina kada je model prvi puta proizveden
 nazModel    - naziv modela

MEHANICAR                                   VRSTA_POSLA
*jmbg                                       *sifVrPosla   - sifra vrste posla
 prezMeh                                     opVrPosla    - opis vrste posla
 imeMeh                                      normativ     - vrijeme koje mehanicar potrosi za
                                                            obavljanje date vrste posla (u min)
 sifVrPosla - sifra vrste posla za koju je  RADNI_NALOG
              mehanicar specijaliziran      *redBt  - redni broj naloga
 koef       - koeficjent za platu            datDol - datum dolaska automobila u radionicu
                                             tvBr   - tvornici broj automobila

OBAVIO
*redBr      - redni broj naloga
*datVol     - datum dolaska automobila u radionicu
*sifVrPosla - sifra vrste posla koja se obavlja na radnom nalogu
jmbg        - jmbg mehanicara koji obavlja dati posao
trajanje    - vrijeme koje je mehanicar utrosio za obavljanje datog posla (min)
```
a) Ispisati nazive proizvodjaca i modela svih automobila koji su u proteklih
10 dana primljeni u radionicu.
Svaka kombinacija u potpisu treba da se pojavi samo jednom (3b)

```SQL
SELECT DISTINCT
    a.sifProizvod,
    a.nazModel
FROM RADNI_NALOG rn
JOIN AUTOMOBIL a ON a.tvBr = rn.tvBr
WHERE rn.datDol >= CURDATE() - INTERVAL 10 DAY;
```

b) Ispisati podatke o mehanicarima (jmbg, prezime i ime), opis vrste posla
za koju su specijalizirani i opis vrste posla koju su obavili na automobilu
i njegov tvornicki broj za one mehanicare koji su bar jednom u toku prosle 
godine obavili vrstu posla za koju nisu specijalizirani. (3b)

```SQL
SELECT DISTINCT
    m.jmbg,
    m.prezMeh,
    m.imeMeh,
    vp_spec.opVrPosla AS opis_specijalizacije,
    vp_rad.opVrPosla  AS opis_posla,
    rn.tvBr
FROM OBAVIO o
JOIN MEHANICAR m     ON m.jmbg       = o.jmbg
JOIN VRSTA_POSLA vp_rad  ON vp_rad.sifVrPosla = o.sifVrPosla
JOIN RADNI_NALOG rn  ON rn.redBr     = o.redBr
                    AND rn.datDol    = o.datVol
JOIN VRSTA_POSLA vp_spec ON vp_spec.sifVrPosla = m.sifVrPosla
WHERE YEAR(o.datVol) = YEAR(CURDATE()) - 1
  AND o.sifVrPosla <> m.sifVrPosla;
```
c) Ispisati rang listu mehanicara prem aukupnom broju obavljenih poslova.
Ispisati jmbg, prezime, ime i ukupan broj poslova. Blize pocetku liste nalaze 
se mehanicari koji su obavili veci dio poslova.
Mehanicare koji su eventualno obavili jednak broj poslova poredati abecedno 
po prezimenu. U ispis ukljiciti samo mehanicare ciji je broj obavljenih 
poslova veci od broja razlicitih vrsta poslova obavljenih na svim automobilima
(3b)

```SQL
SELECT 
    m.jmbg,
    m.prezMeh,
    m.imeMeh,
    COUNT(*) AS br_poslova
FROM MEHANICAR m
JOIN OBAVIO o ON o.jmbg = m.jmbg
GROUP BY 
    m.jmbg,
    m.prezMeh,
    m.imeMeh
HAVING COUNT(*) >
       (
         SELECT COUNT(DISTINCT o2.sifVrPosla)
         FROM OBAVIO o2
       )
ORDER BY 
    br_poslova DESC,
    m.prezMeh ASC;
```
d) Ispisati nazive proizvodjaca i nazive modela na cijim automobilima nikada
nije obavljena vrsta posla "Zamjena vodene pumpe" (3b)

```SQL
SELECT DISTINCT
    a.sifProizvod,
    a.nazModel
FROM AUTOMOBIL a
WHERE NOT EXISTS (
    SELECT 1
    FROM RADNI_NALOG rn
    JOIN OBAVIO o       ON o.redBr      = rn.redBr
                       AND o.datVol     = rn.datDol
    JOIN VRSTA_POSLA vp ON vp.sifVrPosla = o.sifVrPosla
    WHERE rn.tvBr = a.tvBr
      AND vp.opVrPosla = 'Zamjena vodene pumpe'
);
```
e) Svim mehanicarima koji su obavili vise od 500 poslova na bilo kojem 
automobilu postaviti sifru vrste posla za koji je specijaliziran na sifru 
vrste posla koju je taj mehanicar posljednju obavio (prepotsavlja se da je taj
dan obavljao samo tu vrstu posla) (3b)

```SQL
UPDATE MEHANICAR m
SET m.sifVrPosla = (
    SELECT o1.sifVrPosla
    FROM OBAVIO o1
    WHERE o1.jmbg = m.jmbg
    ORDER BY o1.datVol DESC
    LIMIT 1
)
WHERE m.jmbg IN (
    SELECT jmbg
    FROM OBAVIO
    GROUP BY jmbg
    HAVING COUNT(*) > 500
);
```
Drugi nacin:

```SQL
UPDATE MEHANICAR
SET sifVrPosla = (
    SELECT o1.sifVrPosla
    FROM OBAVIO o1
    WHERE o1.jmbg = MEHANICAR.jmbg
    ORDER BY o1.datVol DESC
    LIMIT 1
)
WHERE jmbg IN (
    SELECT j2.jmbg
    FROM OBAVIO j2
    GROUP BY j2.jmbg
    HAVING COUNT(*) > 500
);
```
