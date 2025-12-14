Napisati po jednu SQL naredbu za svaki sljedeci zadatak.
Upiti moraju biti neovisni o datumu izvodjenja.
Nece se priznavati rjesenja u kojima se nepotrebno koriste podupiti.

---

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

