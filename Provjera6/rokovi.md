## 12a

1. Napisati deklaraciju handler-a za obradu svih gresaka u nekoj pohranjenoj
proceduri. Naredba treba da pohranjuje informacije o MySQL kodu greske i 
opisu aktivirane greske u lokalne varijable kod i poruka. Nakon obrade 
greske treba da nastavi sa radom iza naredbe koja ja izazvala gresku.

Da bi gore deklarisani handler, kao jedini handler za obradu gresaka u nekoj
pohranjenoj proceduri, odradio sve greske koje se u njoj mogu pojaviti ona 
se mora unijeti u kod procedure:

a - na kraju procedure, ispred naredbe END//
b - na pocetku procedure, ispred prve izvrsene naredbe,
c - u zasebnom bloku (BEGIN...END)

```MySQL
-- unutar tijela procedure --

DECLARE kod CHAR(5);
DECLARE poruka TEXT;

DECLARE CONTINUE HANDLER FOR SQLEXECPTION
BEGIN 
    GET DIAGNOSTICS CONDITION 1 
    kod = RETURNED_SQLSTATE,
    poruka = MESSAGE_TEXT;
END;

-- unutar tijela procedure --
```
2. Napisati opsti oblik deklaracije handelra koji ce u nekoj pohranjenoj
proceduri obradjivati greske broj: 1142, 1143, 1162, 1163 i 1167.
Nakon obrade greske izvrsavanje programa treba da nastavi izvan bloka 
u kojem je deklarisan handler.

```SQL
DECLARE EXIT HANDLER FOR 1142, 1143, 1162, 1163, 1167 BEGIN END;
```

3. Napisati SQL naredbu kojom ce simulirati pojavu greske ciji je SQLSTATE
kod 45000 i opis "Simulacija aktiviranja greske".

```mysel
SIGNAL SQLSTATE '45000'
    SET MESSAGE_TEXT = "Simulacija aktiviranja greske"
```

4. Napisati SQL naredbe kojima se korisniku huso:
- oduzima sve ranije date dozvole za relaciju stud
- daje dozvola da pregleda samo podatke o imenima i prezimenima studenata
iz relacije stud
- omogucava pregled i brisanje samo onih studenata iz relacije stud koji 
su rodjeni u mjestu sa postanskim brojem 10000.

```mysel
-- -----------------------------
REVOKE ALL PRIVILEGES ON db.stud FROM 'huso'@'%';

-- -----------------------------
GRANT SELECT (imeStud, prezStud) 
ON db.stud TO 'huso'@'%';

-- -----------------------------
CREATE VIEW stud_10000 AS 
SELECT * FROM stud 
WHERE pbrRod = 10000;

GRANT SELECT, DELETE ON db.stud_10000 TO 'huso'@'%';
```
---

## 12b

1. Napisati opsti oblik deklaracije handler-a koji ce u nekoj pohranjenoj
proceduri obradjivati greske broj: 1142, 1143, 1162, 1163, 1167. Nakon
obrade greske izvrsavanje programa terba da se nastavi izvan bloka 
u kojem je deklarisan handler.

Da bi gore deklarisani handler u nekoj pohranjenoj proceduri, obradio 
greske koje se mogu pojaviti unutar eksplicitno definisanog bloka 
naredbi ona se mora unijeti u kod procedure:

a - neposredno iznad BEGIN naredbe kojom pocinje eksplicitni blok naredbi

b - neposredno iza END naredbe kojom zavrsava eksplicitni blok naredbi

c - neposredno iza BEGIN naredbi kojom pocinje eksplicitni blok

```mysel
BEGIN 
    DECLARE EXIT HANDLER FOR 1142, 1143, 1162, 1163, 1167 BEGIN END;
END;
```

2. Napisati deklaraciju handler-a za obradu svih gresaka u nekoj 
pohranjenoj proceduri. Naredba treba da pohranjuje informacije o MySQL 
kodu greske i opisu aktivirane greske u lokalne varijable kod i poruka.
Nakon obrade greske procedure treba da nastavi sa radom iza nadedbe koja 
je izazvala gresku.

```mysel
DECLARE kod CHAR(5);
DECLARE poruka TEXT;

DECLARE CONTINUE HANDLER FOR SQLEXECPTION
BEGIN
    GET DIAGNOSTICS CONDITION 1 
        kod = RETURNED_SQLSTATE,
        poruka = MESSAGE_TEXT;
END;
```

3. Napisati SQL naredbu kojom ce simulirati pojavu greske ciji je SQLSTATE
kod 45000 i opis "Korisnicki izazvana greska".

```mysel
SIGNAL SQLSTATE '45000'
    SET MESSAGE_TEXT = 'Korisnicki izazvana greska.'
```

4. Napisati SQL naredbe kojima se korisniku huso:
- oduzima ranije dodijeljena dozvola za pregled podataka u relaciji pred
- daje dozvola da pregleda samo podatke o kraticama i nazivima predmeta 
iz relacije pred.
- omoguciti pregled i brisanje samo onih predmeta iz relacije pred koji 
pripadaju organizacijskog jedini sa sifrom 10001.

```mysel

```

## ROK 13a

1. Kreirati okidac bris_stud koji ce prije brisanja zapisa u relaciji stud 
pokrenuti izvrsavanje postojece procedure kontrola(). Procedura nema 
argumenata.

```SQL

```

2. Kreirati okidac(e) kako bi se osiguralo izvodjenje procedure kontrola1()
prije azuriranja atributa datRodStud, a procedure kontrola2() prije 
azuriranja atributa jmbgStud u relaciji stud. Obje procedure nemaju 
argumenata.

```SQL

```

3. Kreirati okidac unos_pred koji ce za svaki uneseni zapis u relaciji pred 
unijeti novi zapis u relaciju orgjed sa sifrom organizacione jedinice koja je 
unesena prilikom unosa zapisa u relaciju pred, nazivom "Nova organizacijska
jedinica" i sifrom nadredjene organizacijske jedinice NULL.

```SQL

```

4. Kreirati okidac izoc_ispit koji ce pri svakoj izmjeni ocjene u relaciji 
ispit usporediti novu i staru vrijednost ocjene te, ako je nova vrijednost 
ocjena veca od stare, pokrenuti izvrsavanje postojece procedure kontrola().
Procedura nema argumenata.


---

## ROK 13b

1. Kreirati okidac unos_stud koji ce nakon unosa zapisa u relaciji stud 
pokrenuti izvrsavanje postojece procedure kontrola(). Procedura nema 
argumenata.

```SQL

```

2. Kreirati okidac(e) kako bi se osiguralo izvodjenje procedure kontrole1()
prije azuriranja atributa upisanoStud, a procedure kontrola2() prije 
azuziranja atributa brojSatiTjedno u relaciji pred.
Obje procedure nemaju argumenata.

```SQL

```

3. Kreirati okidac bris_orgjed koji ce za svaki obrisani zapis u relaciji 
orgjed izbrisati sve nastavnike iz relacije nastavnik sa sifrom organizacijske
jedinice koja je upravo obrisana.

```SQL

```

4. Kreirati okidac izkap_dvorana koji ce pri svakoj izmjeni kapaciteta 
u relaciji dvorana uporediti novu i staru vrijednost kapaciteta te, ako 
je nova vrijednost bar duplo manja od stare, pokrenuti izvrsavanje 
postojece procedure kontrola(). Procedura nema argumenata.

```SQL

```

