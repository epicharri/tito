# Opas TTK-91 ohjelmointiin

Tämä on lyhyt ohjeistus TTK-91 -kielen alkeisiin ja tarkoitettu lähinnä alkuun pääsemiseen. Lisätietoa kannattaa lukea kurssin materiaaleista.

## Hello world

TTK-91:ssä voit käyttää vain kokonaislukuja. Siinä ei ole tukea merkkijonojen ilmaisemiselle eikä liukuluvuille. Tulostetaan siis _Hello Worldin_ sijaan luku 2.

> Aina, kun lasket jotain tai käytät I/O:ta (tulostat näytölle tai luet luvun näppäimistöltä), on käytettävä jotakin rekisteriä!

Rekistereitä joita voit käyttää vapaasti ovat R1 - R5. Myös rekisteriä R0 voi käyttää, mutta se voi käyttäytyä välillä oudosti. Vältä sitä toistaiseksi.

### Luvun lataaminen rekisteriin

Ladataan nyt luku 2 rekisteriin LOAD -käskyllä:

```LOAD R1, =2```

> =2 tarkoittaa lukua 2. Jos käytät konekäskyä `LOAD R1, 2`, se lataa rekisteriin R1 _muistiosoitteessa_ 2 olevan arvon!

Pienet ja isot kirjaimet eivät ole merkityksellisiä. Voit siis kirjoittaa koodia vaikka näin `load r1, =2` tai `LoAd R1, =2`.

Käsky tallettaa rekisteriin R1 kokonaisluvun 2. Huomaat että ennen lukua 2 on = -merkki. Se ilmaisee että sitä seuraava arvo on _välitön operandi_. _Operandi_ on tarkoittaa operaation kohdetta. Operaationa tässä on LOAD, ensimmäisenä operandina on R1 ja toisena operandina luku 2. KAIKKI TTK-91 -kielen operaatiot ovat binäärioperaatioita eli operaatioita joissa on kaksi (siksi binääri) operaation kohdetta eli operandia. Tällainen termistö voi tuntua vieraalta, mutta olet käyttänyt operaatioita ja operandeja alakoulusta lähtien. Esimerkiksi aivan tavallinen laskutoimitus 4 + 7, missä 4 ja 7 ovat operandeja ja + on operaatio.

> Terminologiaa: Käsky on sama kuin operaatio, esimerkiksi LOAD. Konekäsky on koko rimpsu operandeineen ja rekistereineen, tässä tapauksessa LOAD R1, =2.

Tarkalleen ottaen `LOAD R1, =2` on konekäsky `LOAD R1, =2(R0)`. Konekäskyssä on aina operaatio, ensimmäinen rekisteri, osoitusmoodi (yllä siis _välitön_; muut ovat suora ja epäsuora, joihin palataan kohta), osoiteosa (välitön operandi tai muistiosoite) ja indeksirekisteri. Indeksirekisteri on R0 jos sitä ei ole merkitty. Jos indeksirekisterinä käytetään R0:aa, sen arvona on silloin 0, vaikka rekisterissä R0 olisi jokin muu luku. Siispä LOAD R1, =2(R0) lataa rekisteriin R1 arvon, joka saadaan laskemalla yhteen 2 + 0 eli 2. Sen sijaan kun käytät rekistereitä R1 - R5 indeksirekisterinä, esimerkiksi näin: `LOAD R1, =2(R2)`, ja R2:n sisältö on esimerkiksi 5, ladataan R1:een arvo 2 + 5.

### Tulostaminen

Seuraavaksi tulostetaan rekisteriin R1 talletettu arvo näytölle OUT -käskyllä:

```OUT R1, =CRT```

Tämä konekäsky lähettää CRT -laitteelle eli näytölle rekisterin R1 sisällön tulostettavaksi. Siis tuon siihen aiemmin ladatun luvun 2.

> Muistutus: ensimmäinen operandi on aina jokin rekisteri!

### Ohjelman lopetus

Ohjelma pitää aina lopettaa käyttöjärjestelmän kutsumiskäskyllä SVC antamalla ensimmäiseksi operandiksi _pino-osoitin (Stack Pointer, SP)_ ja toiseksi operandiksi ohjelman lopetusrutiinin osoite HALT näin:

```
SVC SP, =HALT
```

Tämä konekäsky lopettaa ohjelman.


## Luvun lukeminen näppäimistöltä

Titokoneella voidaan käsitellä vain kokonaislukuja. Luku luetaan näppäimistöltä näin:
```IN R1, =KBD```
Tämän konekäskyn suorituksen jälkeen rekisterin R1 arvo on näpppäimistöltä luettu luku.


## Pienin ja suurin TTK-91:n hyväksymä kokonaisluku

### Käytettävissä 32 bittiä luvun ilmaisuun

Kun ohjelma lukee käyttäjän syötteen operaatiolla `IN` tai kun määritellään muuttujan tai vakion arvo, on käytettävissä 32 bittiä luvun ilmaisuun. Koska käytössä on myös negatiiviset luvut, lukualue tällöin seuraava:

* Pienin kokonaisluku on - 2^31 = - 2147483648
* Suurin kokonaisluku on 2^31 - 1 = 2147483647

### Käytettävissä 16 bittiä luvun ilmaisuun

Kun kokonaislukua käytetään konekäskyn yhteydessä, on käytössä 16 bittiä luvun ilmaisuun. Koska käytössä on myös negatiiviset luvut, lukualue on tällöin seuraava:

* Pienin kokonaisluku on - 2^15 = -32768
* Suurin kokonaisluku on 2^15 = 32767

#### Konekäskyn binääriesitys

Tässä on taulukko konekäskyn binääriesityksestä, josta voitte havaita, että konekäskyn yhteydessä on vain 16 bittiä käytössä kokonaisluvun ilmaisua varten.

Konekäskyn rakenteesta on lisätietoa [täällä.](https://www.cs.helsinki.fi/group/titokone/ttk91_ref_fi.html). Alla oleva taulukko on suoraan kyseisestä lähteestä.

```
      8 bittiä       3b    2b    3b              16 bittiä
 +----------------+------+----+------+--------------------------------+
 | Operaatiokoodi |  Rj  | M  |  Ri  |   osoite / välitön operandi    |
 +----------------+------+----+------+--------------------------------+
  31            24 23              16 15                             0

  Lähde: Tietokoneen toiminta -kurssin kotisivu, Helsingin yliopisto
  https://www.cs.helsinki.fi/group/titokone/ttk91_ref_fi.html
```

Konekäskyn binääriesityksessä vasemmalta lukien ensimmäiset 8 bittiä ovat operaatiokoodi, seuraavat 3 bittiä ensimmäinen rekisteri, sitten 2 bittiä osoitustapa, sitten 3 bittiä toinen rekisteri ja lopuksi 16 bittiä muistiosoitetta tai välitöntä operandia varten. Luvun ilmaisu konekäskyn yhteydessä on siis mahdollista käyttämällä 16 bittiä. Koska TTK-91:ssä on sekä negatiiviset että positiiviset kokonaisluvut, pienin luku mitä konekäskyn yhteydessä voidaan käyttää, on - 2^15 = - 32768 ja suurin luku on 2^15 - 1 = 32767.

Seuraavassa kappaleessa näytetään miten voidaan käyttää pienempiä lukuja kuin -32768 ja suurempia lukuja kuin 32767.


## Muuttujat, vakiot, tietueet, taulukot ja laskutoimitukset

### Muuttujat

Muuttujat alustetaan _pseudokäskyllä_ DC (Data Constant) näin:

```
A DC 0
B DC 42
PIENIN DC -2147483648   ; Pienin TTK-91:n hyväksymä kokonaisluku eli - 2^31.
SUURIN DC 2147483647    ; Suurin TTK-91:n hyväksymä kokonaisluku eli 2^31 - 1.
```

> Konekäskyn yhteydessä pienin mahdollinen luku on -32768 ja suurin 32767.

> Lukua operaatiolla `IN` luettaessa ja muuttujan tai vakion määrittelyssä pienin mahdollinen luku on -2147483648 ja suurin 2147483647.

_Pseudokäsky_ ei ole "oikea" konekäsky. Se huomioidaan vain käännösvaiheessa. Esimerkiksi `A DC 0` pseudokäsky varaa käännösvaiheessa muuttujalle A muistiosoitteen ja asettaa sen arvoksi 0. Kääntäjä asettaa _symbolitauluun_ tiedon muuttujan nimestä ja osoitteesta.

> Muuttujan määrittely ja alustaminen `A DC 0`. Samantapaisesti siis kuten Javassa `int a = 0;`
> Muuttujan arvoa voidaan muuttaa, vaikka tuo DC käsky on lyhenne sanoista data constant. Yleensä constant on vakio jota ei voi muuttaa.

### Vakiot

Vakiot määritellään pseudokäskyllä EQU näin:

```
VASTAUS EQU 42
```
> Vakiota ei voi muuttaa. Sen arvo on aina se mihin se ohjelmassa määritellään. Tätä vastaa Javascriptin uudemmissa versioissa käytettävä `const vastaus = 42`.
Kuten DC:n, myös EQU:n avulla määritellyn vakion nimi ja arvo talletetaan symbolitauluun käännösvaiheessa.

### Tietueet ja taulukot

Tietueelle ja taulukolle varataan tilaa pseudokäskyllä DS (Data Segment) näin:


```
PERSON DS 4 
```

Tämä pseudokäsky alustaa taulukon PERSON nelialkioiseksi. Taulukolle varataan siis tilaa samantyyppisesti kuin Javassa `int person = new int[4]`. 

##### Rekisterin käyttö indeksinä

Taulukon läpikäyminen on kätevintä tehdä _indeksirekisterin_ avulla. Rekisteriä, jonka sisältämää arvoa käytetään taulukkoa läpikäydessä indeksinä, kutsutaan indeksirekisteriksi.

Esimerkki:

```
PERSON DS 4             ; Varataan taulukolle muistialue, kooltaan 4. Kääntäjä asettaa taulukon alkuosoitteeksi
                        ;   jonkin muistiosoitteen.

LOAD R2, =0             ; Käytetään rekisteriä R2 indeksirekisterinä. Indeksiksi asetetaan aluksi 0.
LOAD R1, =3             ; Ladataan rekisteriin R1 jokin arvo, tässä tapauksessa luku 3.
STORE R1, PERSON(R2)    ; Tämä käsky tallettaa (STORE) rekisterin R1 sisällön muistiosoitteeseen, joka saadaan
                        ;    laskemalla yhteen indeksirekisterin R2 arvo ja PERSON -taulukon osoite.


```


### Laskutoimitukset

TTK-91:ssä on käytössä seuraavat laskutoimitukset.

| | | |
|:---|:---|:---|
|`ADD`|Yhteenlasku | `ADD R1, =5` lisää rekisterin R1 arvoon luvun 5.|
|`SUB`|Vähennyslasku | `SUB R1, 2` vähentää rekisterin R1 arvosta MUISTIOSOITTEEN 2 sisältämän arvon.|
|`MUL`|Kertolasku | `MUL R1, X` kertoo rekisterin R1 arvon muistiosoitteessa X olevalla arvolla (muuttujan X arvolla).|
|`DIV`|Jakolasku | `DIV R1, X` jakaa rekisterin R1 arvon muistiosoitteessa X olevalla arvolla. Huom! Esim. kun R1:n arvo on 5 ja X:n arvo 2, eli suoritetaan laskutoimitus 9/2, tuloksena on 4. Luku siis pyöristyy kokonaisluvuksi alaspäin. |
|`MOD`|Modulo (jakojäännös) | `MOD R1, 3` muuttaa rekisterin R1 arvoksi jakojäännöksen. Esim. R1:n arvo on 5, niin jakojäännös 3:lla jaettaessa on 2.|


### Laskutoimitusesimerkkejä


##### Luvuilla laskeminen

```
LOAD R1, =40    ; Ladataan rekisteriin R1 arvo 40.
ADD R1, =2      ; Lisätään reksiterissä olevaan arvoon 2.
```

##### Muuttujan arvoilla laskeminen ja tuloksen tallettaminen

```
A DC 2      ; int a = 2
B DC 42     ; int b = 42

LOAD R1, A  ; Ladataan rekisteriin R1 muuttujan A arvo. Toisin sanoen ladataan R1:een muistiosoitteen A sisältö.
MUL R1, B   ; Kerrotaan rekisterin R1 sisältämä luku muuttujan B arvolla.
STORE R1, A ; Talletetaan rekisterin R1 arvo muuttujan A arvoksi
```

Mikä ihmeen `STORE`? No, sillä talletetaan rekisterin arvo muuttujan arvoksi. Täsmällisesti ilmaistuna konekäskyllä `STORE R1, A` talletetaan rekisterin R1 arvo muistipaikkaan A.

