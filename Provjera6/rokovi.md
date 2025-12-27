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

```SQL
SIGNAL SQLSTATE '45000'
    SET MESSAGE_TEXT = "Simulacija aktiviranja greske"
```

4. Napisati SQL naredbe kojima se korisniku huso:
- oduzima sve ranije date dozvole za relaciju stud
- daje dozvola da pregleda samo podatke o imenima i prezimenima studenata
iz relacije stud
- omogucava pregled i brisanje samo onih studenata iz relacije stud koji 
su rodjeni u mjestu sa postanskim brojem 10000.

```SQL
-- -----------------------------
REVOKE ALL PRIVILEGES ON db.stud FROM 'huso'@'%';

-- -----------------------------
GRANT SELECT (imeStud, prezStud) 
ON db.stud 
TO 'huso'@'%';

-- -----------------------------
CREATE VIEW stud_10000 AS 
SELECT * FROM stud 
WHERE pbrRod = 10000;

GRANT SELECT, DELETE 
ON db.stud_10000 
TO 'huso'@'%';
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

```SQL
BEGIN 
    DECLARE EXIT HANDLER FOR 1142, 1143, 1162, 1163, 1167 BEGIN END;
END;
```

2. Napisati deklaraciju handler-a za obradu svih gresaka u nekoj 
pohranjenoj proceduri. Naredba treba da pohranjuje informacije o MySQL 
kodu greske i opisu aktivirane greske u lokalne varijable kod i poruka.
Nakon obrade greske procedure treba da nastavi sa radom iza nadedbe koja 
je izazvala gresku.

```SQL
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

```SQL
SIGNAL SQLSTATE '45000'
    SET MESSAGE_TEXT = 'Korisnicki izazvana greska.'
```

4. Napisati SQL naredbe kojima se korisniku huso:
- oduzima ranije dodijeljena dozvola za pregled podataka u relaciji pred
- daje dozvola da pregleda samo podatke o kraticama i nazivima predmeta 
iz relacije pred.
- omoguciti pregled i brisanje samo onih predmeta iz relacije pred koji 
pripadaju organizacijskog jedini sa sifrom 10001.

```SQL

REVOKE SELECT 
ON db.pred 
FROM 'huso'@'%';
-- -----------------------------

GRANT SELECT(kratPred, nazPred) 
ON db.pred 
TO 'huso'@'%';
-- -----------------------------

CREATE VIEW pred_10001 AS 
SELECT * FROM pred 
WHERE sifOrgjed = 10001;

GRANT SELECT, DELETE 
ON db.pred_10001 
TO 'huso'@'%';

```

## ROK 13a

1. Kreirati okidac bris_stud koji ce prije brisanja zapisa u relaciji stud 
pokrenuti izvrsavanje postojece procedure kontrola(). Procedura nema 
argumenata.

```SQL

DELIMITER //
CREATE TRIGGER bris_stud 
BEFORE DELETE ON stud 
FOR EACH ROW 
BEGIN 
    CALL kontrola();
END//
DELIMITER ;

```

2. Kreirati okidac(e) kako bi se osiguralo izvodjenje procedure kontrola1()
prije azuriranja atributa datRodStud, a procedure kontrola2() prije 
azuriranja atributa jmbgStud u relaciji stud. Obje procedure nemaju 
argumenata.

```SQL
DELIMITER //
CREATE TRIGGER upd_datRodStud_kontrola1
BEFORE UPDATE ON stud
FOR EACH ROW 
BEGIN 
    IF NEW.datRodStud <> OLD.datRodStud THEN 
        call kontrola1();
    END IF;
END// 
DELIMITER ;

DELIMITER //
CREATE TRIGGER upd_jmbgStud_kontrola2
BEFORE UPDATE ON stud 
FOR EACH ROW 
BEGIN 
    IF NEW.jmbgStud <> OLD.jmbgStud THEN 
        CALL kontrola2();
    END IF;
END// 
DELIMITER ;
```

3. Kreirati okidac unos_pred koji ce za svaki uneseni zapis u relaciji pred 
unijeti novi zapis u relaciju orgjed sa sifrom organizacione jedinice koja je 
unesena prilikom unosa zapisa u relaciju pred, nazivom "Nova organizacijska
jedinica" i sifrom nadredjene organizacijske jedinice NULL.

```SQL
DELIMITER // 
CREATE TRIGGER unos_pred 
AFTER INSERT ON pred 
FOR EACH ROW 
BEGIN 
    INSERT INTO orgjed (sifOrgjed, nazOrgjed, nadOrgjed)
    VALUES (NEW.sifOrgjed, 'Nova organizacijska jedinica', NULL);
END//
DELIMITER ;
```

4. Kreirati okidac izoc_ispit koji ce pri svakoj izmjeni ocjene u relaciji 
ispit usporediti novu i staru vrijednost ocjene te, ako je nova vrijednost 
ocjena veca od stare, pokrenuti izvrsavanje postojece procedure kontrola().
Procedura nema argumenata.

```SQL
DELIMITER //
CREATE TRIGGER izoc_ispit 
BEFORE UPDATE ON ispit 
FOR EACH ROW 
BEGIN 
    IF NEW.ocjena <> OLD.ocjena AND NEW.ocjena > OLD.ocjena THEN
        CALL kontrola();
    END IF;
END//
DELIMITER ;
```

---

## ROK 13b

1. Kreirati okidac unos_stud koji ce nakon unosa zapisa u relaciji stud 
pokrenuti izvrsavanje postojece procedure kontrola(). Procedura nema 
argumenata.

```SQL
DELIMITER //
CREATE TRIGGER unos_stud 
AFTER INSERT ON stud 
FOR EACH ROW 
BEGIN 
    CALL kontrola();
END//
DELIMITER ;
```

2. Kreirati okidac(e) kako bi se osiguralo izvodjenje procedure kontrole1()
prije azuriranja atributa upisanoStud, a procedure kontrola2() prije 
azuziranja atributa brojSatiTjedno u relaciji pred.
Obje procedure nemaju argumenata.

```SQL
DELIMITER // 
CREATE TRIGGER upd_upisanoStud_kontrola1
BEFORE UPDATE ON stud
FOR EACH ROW
BEGIN
    IF NEW.upisanoStud <> OLD.upisanoStud THEN 
        CALL kontrola1();
    END IF;
END//
DELIMITER ;

DELIMITER //
CREATE TRIGGER upd_brojSatiTjedno_kontrola2
BEFORE UPDATE ON pred
FOR EACH ROW
BEGIN
    IF NEW.brojSatiTjedno <> OLD.brojSatiTjedno THEN
        CALL kontrola2();
    END IF:
END//
DELIMITER ;

```

3. Kreirati okidac bris_orgjed koji ce za svaki obrisani zapis u relaciji 
orgjed izbrisati sve nastavnike iz relacije nastavnik sa sifrom organizacijske
jedinice koja je upravo obrisana.

```SQL
DELIMITER //
CREATE TRIGGER bris_orgjed 
AFTER DELETE ON orgjed 
FOR EACH ROW 
BEGIN 
    DELETE FROM nastavnik
    WHERE sifOrgjed = OLD.sifOrgjed;
END// 
DELIMITER ;
```

4. Kreirati okidac izkap_dvorana koji ce pri svakoj izmjeni kapaciteta 
u relaciji dvorana uporediti novu i staru vrijednost kapaciteta te, ako 
je nova vrijednost bar duplo manja od stare, pokrenuti izvrsavanje 
postojece procedure kontrola(). Procedura nema argumenata.

```SQL
DELIMITER //
CREATE TRIGGER izkap_dvorana 
BEFORE UPDATE ON dvorana 
FOR EACH ROW
BEGIN
    IF NEW.kapacitet <> OLD.kapacitet AND // nije obavezan ovaj dio
       NEW.kapacitet <= OLD.kapacitet / 2 THEN
        CALL kontrola();
    END IF:
END//
DELIMITER :
```
