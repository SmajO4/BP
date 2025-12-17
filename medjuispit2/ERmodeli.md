Svaki zadatak trazi da se uradi sljedece:

a) Nacrtati ER mode, opisati entitete i veze (njihove atribute i kljuceve).
Sve sheme moraju zadovoljiti 3NF.

b) Dobijeni ER model transformirati u relacijski te napisati naredbu za 
kreiranje relacije koja ima najvise stranih kljuceva sa ugradjenim osnovnim
pravilima integriteta.

---

> 18.12.2018

1.)
U bazi podataka pohranjuju se podaci o jednoj transportnoj kompaniji.
Kompanija posjeduje vozila razlicitih kategorija kojima se mogu obavljati 
prevozi razlicitih tereta. Za vozila se evidentira registarski broj vozila,
nosivost, tip vozila i kategorija kojoj pripada. Kategorije i tipovi vozila 
su opisani svojim siframa i nazivima.
Evidentira se koji teret se moze prevoziti kojom kategorijom vozila.
Vozila jedne kategorije mogu prevoziti vise razlicitih tereta, ali isto tako
jedan teret se moze prevoziti razlicitim kategorijama vozila.

Jednim Vozilom se u toku jednog dana moze obaviti samo jedna voznja, a 
evidentira se lokacija od koje se obavlja voznja, lokacije do koje se obavlja
voznja, ukupna tezina svih tereta koji se preovze, broj predjenih kilometara
te vozac koji obavlja voznju. Lokacije su opisane sifrom, nazivom, adresom i 
mjestom gdje se nalaze. Za mejsta se evidentira postanski broj i naziv mjesta.
Jednom voznjom se moze prevesti vise razlicitih tereta, a za svaki teret se 
evidentira koji radnik je izvrsio njegov utovar. Jedan radnik moze obaviti
utovar vise razlicitih tereta za jednu voznju, ali utovar jednog tereta za 
jednu voznju obavlja samo jedan radnik. Za racnike i vozace se evidentira jmbg
koji ih jedinstveno identifiricra, ime, prezime, datum zaposljenja i mjesto
stanovanja. Za radnike se dodatno evidentira strucna sprema, a za vozace broj
i datum izdavanja vozacke dozvole.


2.) 
U bazi podataka pohranjuju se podaci o receptima jedne online kuharice.
Za jela se evidentiraju sifra koja ga jednoznacno odredjuje, naziv jela, 
kratak opis jela i nacionalna kuhinja kojoj pripada. 
Nacionalne kuhinje su opisane oznakom i nazivom. Svako jelo moze imati vise 
recepata po kojem se priprema. Za svaki recept se evidentira njegov redni
broj za dato jelo, procijenjeno vrijeme za izradu jela, opste instrukcije 
za pripremu, oznaku tezine pripreme (cijeli broj od 1 do 5) i autor recepta.
Autori imaju svoju sifru, ime, prezime i kontakt mail adresu.

Svako jelo pripada jednoj vrsti (supa, pita, pecenje, jelo s rostilja, ...)
koje imaju svoje oznake i nazive. Za pripremu svakog jela po odredjenom 
receptu se evidentiraju namirnice i njihova kolicina u receptu. Namirnice 
imaju svoje sifre, nazive, jedinicu mjere i vrstu kojoj pripadaju (zacini,
meso, zitarice, ...). Vrste namirnice su opisane oznakom i nazivom.


---

> 5.12.2023 B

U bazi podataka se pohranjuju podaci o umjetnickim djelima i izlozbama jedne 
umjetnicke galerije. Svako umjetnicko djelo ima svoju sifru (koja ga jednoznacno
odredjuje), naziv djela i autora djela. Svako djelo pripada jednom umjetnickom
pravcu (barok, renesansa,...).
Pravci, vrste i tehnike djela imaju svoju oznaku koja ih jednoznacno identificira 
i opis. Autori djela su opisani sifrom, imenom, prezimenom i datumom rodjenja.
Umjetnicka galerija ima svoje lokacije na kojima se izlazu umjetnicka djela.
Djela se mogu izlagati na razlicitim lokacijama, a evidentira se svako 
premjestanje djela u galeriji. Isto djelo na nekoj lokaciji moze biti izlozeno
vise puta, ali ne istog dana. Lokacije su opisane sifrom, nazivom i dodatnim
opisom lokacije.
Galerija organizuje izlozbe za koje se evidentira sifra, naziv i period trajanja
izlozbe (od kojeg do kojeg datuma traje izlozba). Na izlozbama se izlaze vise 
umjetnickih djela, a jedno djelo moze biti izlagano na vise razlicitih izlozbi.

> 5.12.2023 

U bazi podataka pohranjuju se podaci o odrzavanju eksterne mature za srednju 
skolu. Potrebno je evidentirati kandidate (jmbg, prezime, ime, datum rodjenja),
srednju skolu koju su zavrsili te mjesto rodjenja i stanovanja.
Prepostavlja se da je kandidat zavrsio samo jednu srednju skolu. Za svaku 
srednju skolu evidentira se sifra koja ih jednoznacno odredjuje, naziv, adresa i 
mjesto gdje se skola nalazi. Za mjesta se evidentira postanski broj, naziv 
mjesta i kanton u kojem se mjesto nalazi. Kantoni imaju svoju sifru i naziv.
Za svaki predmet se sacinjava test koji se sastoji od odredjenog broja 
zadataka. Svaki zadatak ima sifru, tekst, broj ponudjenih odgovora i oznaku 
tacnog odgovora. Za test se evidentira datum polaganja, ukupan broj bodova 
koji kandidat moze osvojiti i minimalan broj bodova za prolaz.
Za svaki zadatak se evidentira redni broj na testu i koliko bodova nosi, a 
jedan zadatak se moze pojaviti na vise razlicitih testova.
Predmetu su opisani sifrom i nazivom.
Za svakog kandidata evidentiraju se odgovori (ozn odgovora) na svaki zadatak 
koji  dao na testu.


---

> 14.12.2017

1.) U nekom preduzecu evidentiraju se uredjaji. Za svaki uredjaj evidentira se 
inventarni broj i godina proizvodnje. Uredjaj je u vlasnistvu jednog odjela
preduzeca, a za njegovo odrzavanje je takodje zaduzen jedan odjel (ne nuzno
odjel u cijem je vlasnistvu).
Odjeli imaju sifru, naziv i odjel kojem pripadaju.
Uredjaji su smjesteni na razlicitim lokacijama, a evidentira se svako 
premjestanje uredjaja. Isti uredjaj na neku lokaciju moze biti smjesten vise 
puta, ali ne istog dana. Lokacije su opisane sifrom, nazivom i dodatnim 
opisom lokacije.
Ukoliko se neki uredjaj pokvari, evidentira se prijava kvara. Prijava kvara 
sadrzi informacije o uredjaju, datumu nastanka kvara, vrsti kavara (kratica,
naziv), napomeni uz kvar na doticnom uredjaju, te radniku (sifra radnika,
prezime i ime) koji je taj kvar prijavio. U jednom danu istu vrstu kvara na 
nekom uredjaju moze prijaciti vise radnika. Na istom uredjaju isti radnik ne 
moze istog dana prijaviti kvar iste vrste, ali moze prijaviti razlicite vrste
kvarova na istom urejdjaju. Moze se desiti da se neki uredjaji nikada nisu 
kvarili.



2.)U bazi podataka pohranjuju se podaci o odrzavanju predstava u nekom pozoristu.
Predstava ima svoju sifru (koja je jednoznacno odredjuje) i naziv predstave.
Svaka predstava pripada nekoj od kategorija (drama, balet, opera,...).
Kategorija ima svoju oznaku i opis (D - drama, B - balet, ...).
Jedno odrzavanje predstave naziva se izvedbom. Svaka predstava moze imati 
vise izvedbi. Za izvedbu se evidentira datum i tacno vrijeme pocetka (sat i 
minuta) te broj prodanih karata. Jedna predstava ne moze imati vise izvedbi 
koje pocinju istog dana u isto vrijeme.
Izvedbe se ocjenjuju od strane kriticara. Ocjena moze poprimiti vrijednost od 
1 do 10. Jednu izvedbu moze ocijeniti vise kriticara, a jedan kriticar moze 
ocijeniti vise izvedbi. Za kriticara se evidentira sifra (koja ga jednoznacno
odredjuje), ime i prezime.
Za svaku predstavu evidentirani su zadaci zaposlenika pozorista (glumac, 
reziser, scenograf,...). Svaki zaposlenik moze na jednoj predstavi obavljati
samo jedan zadatak. Isti zadatak na jednoj predstavi moze obavljati nekoliko
zaposlenika (predstava moze imati vsie glumaca, scenografa,...). Za svaki 
zadatak evidentira se sifra (koja ga jednoznacno odredjuje) i naziv.
Za svakog zaposlenika evidentira se JMBG, ime i prezime.


---

> 12.12.2014. A

1.) U bazi podataka evidentiraju se podaci o nastavi i prisustovanju studenata
nastavi.
Na pocetku semestra evidentiraju se nastavne jedinice koje cine plan izvodjenja
nastave. Nastavna jedinica je identificirana vrstom nastave (lab vjezbe, 
predavanja,...), predmetom i rednim brojem nastavne sedmice. Za nastavnu 
jedinicu se evidentira jos i opis teme koja se odredjuje. Np.

| sifPred | nazPred | oznVrNast | opisVrNast | rbrSedmica | opisTema |
|---------|---------|-----------|------------|------------|----------|
| 101  | BP | P | Predavanje | 1 | Uvod u baze |
| 101  | BP | A | Auditorne | 1 | Uvod u baze |
| 101  | BP | A | Lab vjezbe | 1 | Uvod u baze |
| 102 | OS | P | Predavanje | 1 | Uvod u OS |

Pretpostavka je da se u istoj sedmici ista vrsta nastave iz predmeta odvija
samo jednom.
Predmet ima svoju sfiru i naziv. Vrsta nastave ima oznaku i opis vrste nastave
(npr L-lab vjezbe, A-auditorne vjezbe, P-predavanje).
Evidentira se je li student prisustvovao nastavnoj jedinici. Osim oznake 
prisustvovanja nastavi (D/N) cuva se i broj bodova koje je pri tome osovjio 
(npr u 2 sedmici: lab vjezbi iz BP student s matricnim brojem 0036381073 je 
osvojio 3b). Za studenta se cuva matricni broj, ime i prezime.
Za nastavnike se evidentira sifra, ime i prezime i organizacijska jedinica u 
kojoj su zaposleni. Svaka organizacijska jedinica ima sifru i naziv, ima 
najvise jednu nadredjenu organizacijsku jedinicu, a moze imati vise poredjenih
organizacijskih jedinica.

---

> 2021

U bazi podataka pohranjuju se podaci o odrzavanju i rezultatima utrke formule1.
Evidentiraju se podaci o timovima i pilotima koji nastupaju za timove.
Za timove se evidentira sifra koja ga jednoznacno identificira, naziv tim,
drzava kojoj tim pripada i direktor tima (prvi vozac, drugi vozac, testni
vozac...)
Uloge imaju svoje sifre i nazive, a u svakom timu jedan pilot moze obavljati
samo jednu ulogu te jednu ulogu moze obavljati samo jedan pilot.
Piloti i direktori timova su osobe za koje se evidentira sifra koja ih 
jednoznacno identificira, ime, prezime i drzava iz koje dolaze.
Pored toga, za direktora se evidentira datum od kada je direktor, a za 
pilote se evidentira ukupan broj pobjeda i godina od koje se takmici.
Drzave su opisane oznakom drzave i nazivom.
Utrke se odrzavaju na stazama za koje se evidentira sifra, naziv i rekord 
staze te drzave u kojoj se staza nalazi. Jednog dana se moze odrzavati samo
jedna utrka formule 1, a osim datuma evidentira se i naziv utrke te staza na 
kojoj se odrzava. Na svakoj utrci se evidentira koji pilot je osvojio 
koju poziciju. Svaka pozicija nosi tacan broj bodova koji se ne mijenja 
(pozicija 1 -> 12b, pozicija 2 -> 10b...)







