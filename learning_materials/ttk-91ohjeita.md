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

Käsky tallettaa rekisteriin R1 kokonaisluvun 2. Huomaat että ennen lukua 2 on = -merkki. Se ilmaisee että sitä seuraava arvo on _välitön operandi_. _Operandi_ on tarkoittaa operaation kohdetta. Operaationa tässä on LOAD, ensimmäisenä operandina on R1:n arvo ja toisena operandina luku 2. KAIKKI TTK-91 -kielen operaatiot ovat binäärioperaatioita eli operaatioita joissa on kaksi (siksi binääri) operaation kohdetta eli operandia. Tällainen termistö voi tuntua vieraalta, mutta olet käyttänyt operaatioita ja operandeja alakoulusta lähtien. Esimerkiksi aivan tavallinen laskutoimitus 4 + 7, missä 4 ja 7 ovat operandeja ja + on operaatio.

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

```
IN R1, =KBD
```

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
A DC 0     ; Kuten Java-kielellä: int A = 0;
B DC 42    ; Kuten Java-kielellä: int B = 42;
```

Konekäskyn yhteydessä osoiteosaan mahtuu vain välillä -32786 ... 32767 olevia lukuja. Pseudokäskyn DC avulla voi määritellä muuttujan arvoksi tämän välin ulkopuolisia lukuja, joita voi sitten käyttää ohjelmassa.

```
MILTSI DC 1000000       ; int MILTSI = 1000000; 
PIENIN DC -2147483648   ; Pienin TTK-91:n hyväksymä kokonaisluku eli - 2^31.
SUURIN DC 2147483647    ; Suurin TTK-91:n hyväksymä kokonaisluku eli 2^31 - 1.
```

Muuttujaan alustettuja isoja lukuja voi käyttää näin:

```
LOAD R1, MILTSI         ; R1:n arvoksi tulee muuttujan MILTSI arvo 1000000.  
                        ; Tämä ei toimisi: LOAD R1, =1000000
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
|`DIV`|Jakolasku | `DIV R1, X` jakaa rekisterin R1 arvon muistiosoitteessa X olevalla arvolla. Huom! Esim. kun R1:n arvo on 9 ja X:n arvo 2, eli suoritetaan laskutoimitus 9/2, tuloksena on 4. Luku siis pyöristyy kokonaisluvuksi alaspäin. |
|`MOD`|Modulo (jakojäännös) | `MOD R1, =3` muuttaa rekisterin R1 arvoksi jakojäännöksen. Esim. R1:n arvo on 5, niin jakojäännös 3:lla jaettaessa on 2.|


### Laskutoimitusesimerkkejä


##### Luvuilla laskeminen

```
LOAD R1, =40  ; Ladataan rekisteriin R1 luku 40.
ADD R1, =2    ; Lisätään rekisterissä R1 olevaan arvoon luku 2.
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


### IF-THEN-ELSE ja LUUPIT

Konekielessä ehtolauseet ja niiden mukaan toimiminen tehdään hyppäämällä ehtojen toteutumisen mukaisiin muistiosoitteisiin.

##### Esimerkki: LUUPPI JA IF-THEN

> Esitellään uusia käskyjä vertailukäsky `COMP` ja ehdollinen hyppykäsky `JLES` (Jump if Less). 
Lisäksi: voit laittaa konekäskyn eteen sanan ja koodissa hypätä siihen.

Tämä ohjelma tulostaa luvut 0...9:
```
          LOAD R1, =0   ; Ladataan rekisteriin R1 luku 0.
UUSIKSI   OUT R1, =CRT  ; Tulostetaan R1:ssä oleva luku.
          ADD R1, =1    ; Kasvatetaan R1:n arvoa yhdellä.
          COMP R1, =10  ; Verrataan R1:n arvoa lukuun 10.
          JLES UUSIKSI  ; (Jump if Less) Jos vertailukäskyn COMP suorituksen jälkeen
                        ;   tilarekisterin bitti L (less) on 1 ja E (equals) on 0,
                        ;   eli R1:n arvo on alle 10, hypätään osoitteeseen UUSIKSI. 
          SVC SP, =HALT ; Tämä lopettaa ohjelman.

```

##### Koodirivin muistiosoitteen käyttäminen hyppykäskyä varten

Rivin alussa oleva sanaa, joka ei ole mikään kielen käskykannan operaatio, voi käyttää ohjelmoidessa rivin muistiosoitteena. Käännösvaiheessa kääntäjä huomaa nämä sanat ja asettaa koodissa jokaiseen kohtaan, missä sana on konekäskyn osana (kuten yllä olevassa koodissa kohdassa `JUMP UUSIKSI`), kyseisen sanan tilalle sen rivin muistiosoitteen minkä alussa sana on. 

Näitä sanoja voi käyttää kuten muuttujia. Voit esimerkiksi saada ladattua rekisteriin R1 muistipaikassa UUSIKSI olevan konekäskyn (`LOAD R1, UUSIKSI`) ja muistipaikan UUSIKSI osoitteen (`LOAD R1, =UUSIKSI`).

Osoitteen ilmaisimena ei saa käyttää operaatioiden nimiä (ei saa olla esim. LOAD, ADD, OUT, ...).

> Huom! Jos rivin alussa määritetään sana jonka perusteella siihen voi hypätä, on sillä rivillä oltava konekäsky. Siinä voi käyttää halutessaan konekäskyä `NOP` eli No Operation, joka tosin sitten kuluttaa yhden kierroksen käskyjen nouto- ja suoritussyklissä.

Näin ei siis voi tehdä:
```
          LOAD R1, =0  
UUSIKSI                 ; **Ei näin! Tällä rivillä on oltava myös konekäsky.**
          OUT R1, =CRT
```

##### Esimerkki IF-THEN-ELSE

Tässä esimerkkiohjelmassa pyydetään näppäimistöltä lukua. Jos luku on 0, sitä ei hyväksytä vaan pyydetään uudelleen lukua. Sen jälkeen se talletetaan muuttujan A arvoksi ja verrataan muuttujien A ja B arvoa keskenään. Huomatkaa että vertailussa vähintään toisen luvuista pitää olla rekisterissä! Jos vertailun `COMP R1, B` tulos on että R1 > B, `JGRE suurempi` -konekäskyn avulla siirrytään osoitteeseen `suurempi`.

```
          A DC 0             ; Alustetaan muuttuja A, A=0.
          B DC 100           ; Alustetaan muuttuja B, B=100.

kysyLuku  IN R1, =KBD        ; Pyydetään luku näppäimistöltä.
          JZER R1, kysyLuku  ; Jump if ZERo. Jos R1==0, hyppää kysyLuku.

          STORE R1, A        ; Talletetaan kysytty luku muuttujan A arvoksi.
          COMP R1, B         ; Verrataan kysyttyä lukua muuttujan B arvoon.
          JGRE suurempi      ; Jump if GREater. Jos R1:n arvo on suurempi
                             ;   B:n arvo, hypätään kohtaan suurempi.

          LOAD R1, B         ; Ei ollut suurempi, tehdään tässä jotain muuta.
          ADD R1, =A
          STORE R1, A
          OUT R1, =CRT
          JUMP lopetus       ; Hypätään ehdottomalla JUMP -käskyllä kohtaan lopetus.
                             ;   Ilman tätä ehdotonta hyppyä siirryttäisiin seuraavalle riville.

suurempi  LOAD R1, =1000     ; Tehdään tässä asioita jos vertailun tulos oli suurempi.
          DIV R1, A
          OUT R1, =CRT

lopetus   SVC SP, =HALT      ; Lopetus pitää merkitä jos sinne halutaan hypätä.

```


### Ehdoton hyppy
| | | | |
| :--- | :--- | :--- | :--- |
| JUMP | Ehdoton hyppy | `JUMP 20` | Hypätään osoitteeseen 20. |
| | | `JUMP lopetus` | Hypätään suorittamaan konekäskyä jonka eteen on kirjoitettu `lopetus`. |


### Ehdolliset hypyt

Muut hypyt ovat ehdollisia hyppyjä. Jos ehto toteutuu, hypätään käskyn yhteydessä annettavaan osoitteeseen. Muutoin jatketaan seuraavan käskyn suoritukseen, eli lähdekoodissa siis seuraavalla rivillä olevan käskyn suorittamiseen.


#### Ehdolliset hypyt positiivisuuden ja negatiivisuuden perusteella

Näitä käskyjä käytetään rekisterin kanssa ilman mitään muuta vertailukäskyä.

| Operaatio | Merkitys | Esimerkki | Selite |
| :--- | :--- | :--- | :--- |
| JNEG | Jump if NEGative. | `JNEG R1, tuliNegaa` | Jos R1<0, hyppää osoitteeseen `tuliNegaa`. |
| JZER | Jump if ZERo | `JZER R3, onNolla` | Jos R3==0, hyppää osoitteeseen `onNolla`.|
| JPOS | Jump if POSitive | `JPOS R2, anna` | Jos R2>0, hyppää osoitteeseen `anna`. |
| JNNEG |	Jump if Not NEGative | `JNNEG R5, eiNega` |  Jos R5 >= 0, hyppää osoitteseen `eiNega` |
| JNZER |	Jump if Not ZERo) | `JNZER R4, eiPaha` | Jos R4 erisuuri kuin 0, hyppää osoitteeseen `eiPaha` |
| JNPOS |	Jump if Not POSitive | `JNPOS R1, mitasNyt` | Jos R1 <= 0, hyppää `mitasNyt` |


##### Esimerkki

Tulostetaan luvut 9...0 luupin avulla.
```
          LOAD R1, =9
Tulosta   OUT R1, =CRT
          SUB R1, =1
          JNNEG R1, Tulosta  ; Jos R1>=0, hyppää kohtaan TULOSTA
          SVC SP, =HALT
```



#### Ehdolliset hypyt suuruusvertailukäskyn `COMP` jälkeen.

Vertailukäskyn (esim. `COMP R1, =10`) jälkeen tilarekisterin (SR) bittien G (greater), L (less) ja E (equals) arvot muuttuvat sen mukaisiksi mikä vertailun tulos on ollut.

Esimerkki:
```

COMP R1, =10    ; Verrataan COMP -käskyllä R1:n arvoa lukuun 10. Vertailun tulos
                ;   merkitään tilarekisteriin.
JLES lopetus    ; Jump if LESs, hypätään osoitteeseen lopetus, jos vertailun tulos
                ;   oli se, että R1<10. Muutoin jatketaan seuraavan rivin suorittamiseen.
                ;   Tämä hyppykäsky tarkastaa vertailun tuloksen tilarekisteristä (SR).

COMP R1, A      ; Verrataan R1:n arvoa muuttujan A arvoon. Vertailun tulos 
                ;   merkitään tilarekisteriin.
JGRE suurempi   ; Jump if Greater. Jos vertailun tulos oli se, että R1:n arvo on 
                ;   suurempi kuin A:n arvo, hypätään osoitteeseen suurempi.

```



| Operaatio | Merkitys | Esimerkki | Selite | SR:n bitit G,L,E |
| :--- | :--- | :--- | :--- | :--- |
| JLES |	Jump if LESs | `JLES pienempi` | Jos vertailun tulos oli pienempi. | L asetettu, ei E |
| JEQU |	Jump if EQUal | `JEQU tasan` | Jos vertailun tulos oli tasan | E, ei muut |
| JGRE |	Jump if GREater | `JGRE suurempi` | Jos vertailun tulos oli suurempi | G, ei muut |
| JNLES |	Jump if Not LESs | `JNLES eiAlle` | Jos vertailun tulos oli suurempi tai yhtäsuuri | E tai G |
| JNEQU	| Jump if Not EQUal | `JNEQU eiSama` | Jos vertailun tulos oli erisuuri | L tai G |
| JNGRE |	Jump if Not GREater | `JNGRE eiSuurempi`  | Jos vertailun tulos oli pienempi tai yhtäsuuri | L tai E |

> Muista, että näitä suuruusvertailuhyppykäskyjä käytettäessä on aina ensin tehtävä vertailu `COMP` -käskyllä. Esimerkkejä: `COMP R1, X` tai `COMP R1, =10`.


### Osoitusmuodot

Konekäskyn yhteydessä voi käyttää osoitusmuotoina _välitöntä_, _suoraa_ ja _epäsuoraa_ osoitusta. Konekäskyn kääntämisen jälkeen nämä ilmaistaan kahtena bittinä konekäskyn binääriesityksessä kuten esitettiin aiemmin [täällä.](#konekäskyn-binääriesitys)


#### Välitön osoitus

Välitön osoitustapa on se, missä konekäskyn osoiteosassa eli oikeanpuoleisimpina 16 bittinä on jo valmiiksi eli välittömästi se arvo mitä on tarkoitus käyttää konekäskyn suorituksessa. Ohjelmoidessa välitön osoitus ilmaistaan = -merkillä. Esimerkiksi konekäskyssä `LOAD R1, =100` on kyse välittömästä osoitustavasta. Prosessori havaitsee välittömän osoitustavan konekäskyssä olevista kahdesta bitistä jotka kertovat muistinoutojen määrän. Välittömässä niiden bittien arvo on 0, joten mitään ei tarvitse hakea muistista ja prosessori voi ryhtyä suorittamaan käskyä.

> Välitön osoitus: Esimerkiksi `LOAD R1, =100` lataa rekisteriin R1 luvun 100.


#### Suora osoitus

Suorassa osoituksessa konekäskyn osoiteosassa eli oikeanpuoleisimpina 16 bittinä on muistiosoite, josta operaatiota varten tarvittava tieto on haettava. Ohjelmoidessa suora osoitus ilmaistaan ilman mitään erikoismerkkejä. 

Esimerkki: Oletetaan että muuttujan X muistiosoite on 25, ja oletetaan että muuttujan X arvo on 1000. Tällöin konekäsky `LOAD R1, X` on kääntämisen jälkeen on muodossa `LOAD R1, 25`.

Käskyn nouto- ja suoritussyklissä asia tapahtuu näin:

1. Käsky on noudettu `LOAD R1, 25` käskyrekisteriin (IR, _Instruction Register_).
2. Prosessori hoitaa käskyn purkamisen osiin erittäin tehokkaasti. Käskyrekisteristä osoiteosan (oikeanpuoleisimmat 16 bittiä) sisältö (tässä luku 25) siirtyy nopeiden johtimien avulla heti väliaikaisrekisteriin (TR, _Temporary Register_, "temppari").
3. Prosessori tarkistaa osoitustavan osoitustapaa osoittavista biteistä. Koska tässä on kyse suorasta osoituksesta, niiden bittien arvo on 1. Siitä prosessori tietää, että tarvitaan yksi muistinouto lisää.
4. Seuraavaksi haetaan muistiosoitteesta 25 sen sisältö, joka on yllä oletettu olevan 1000, ja se talletetaan väliaikaisrekisteriin.
5. Ja sitten käsky suoritetaan: rekisterin R1 arvoksi tulee väliaikaisrekisterin arvo 1000.

> Suora osoitus: esimerkiksi `LOAD R1, X` lataa rekisteriin R1 _muistiosoitteen_ X sisällön eli toisin sanottuna _muuttujan_ X arvon.


#### Epäsuora osoitus

Epäsuora osoitus ilmaistaan ohjelmoidessa @ -merkillä. Esimerkiksi `LOAD R1, @PX` lataa rekisteriin R1 muuttujan PX arvon mukaisen muistipaikan sisällön. Toisella tavalla sanoen se konekäsky lataa rekisteriin R1 _muuttujan PX osoittaman arvon_.

Osoitustapaa ilmaisevien bittien arvo on epäsuorassa osoituksessa 2, joten prosessori tekee 2 muistinoutoa.

##### Esimerkki

Oletetaan seuraavat 4 asiaa
* Muuttujan X arvo eli muistiosoitteen X sisältö on 1000.
* Muuttujan X muistiosoite on 25.
* Muuttujan PX muistiosoite on 30.
* Muuttujan PX arvo, eli muistiosoitteen PX sisältö on X:n osoite eli 25.

> Jos muuttujan PX arvo on muuttujan X osoite, sanotaan että muuttuja PX osoittaa muuttujan X arvoon. Tällöin PX on muuttujan X _osoitinmuuttuja_ (pointer = osoitin).

Esimerkiksi `LOAD R1, @PX` käskyä suorittaessa tapahtuu käskyjen nouto- ja suoritussyklissä seuraavaa:

1. Käsky `LOAD R1, @30` on noudettu käskyrekisteriin.
  * @30 eikä PX, sillä kääntämisen jälkeen koodissa ei ole enää muuttujia vaan pelkästään niiden muistiosoitteet. Yllä on oletettu että PX:n osoite on 30.
2. Käsky on purettu. Nyt luku 30 eli muuttujan PX osoite on väliaikaisrekisterissä (TR, _Temporary Register_).
3. Osoitustapa näkyy käskyn binääriosoituksen biteistä. Epäsuorassa sen arvo on 2. Prosessori tietää, että on siksi tehtävä 2 muistinoutoa.
4. Noudetaan muistista muistipaikan 30 sisältö eli muuttujan PX arvo (joka on nyt tässä muuttujan X osoite 25) ja talletaan se TR:ään.
5. Tehdään toinen muistinouto. TR:ssä on luku 25, joten haetaan sen mukaisen muistiosoitteen sisältö ja talletetaan se TR:ään. Nyt TR:ssä on muistipaikan 25 sisältö (muuttujan X arvo), siis luku 1000.
6. Ja sitten käsky suoritetaan: Rekisteriin R1 siirtyy TR:ssä oleva luku 1000.


