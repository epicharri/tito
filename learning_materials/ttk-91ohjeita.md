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

> Muistutus: ensimmäinen operandi on aina jossakin rekisterissä!

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

### Konekäskyn yhteydessä on käytettävissä 16 bittiä luvun ilmaisuun

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


#### Vakioiden käyttäminen

Esimerkki vakioiden käyttämisestä ja vertailua muuttujan käyttämiseen.


```

MILJARDI DC 1000000000
suurinVakio EQU 32767
pieninVakio EQU -32768

LOAD R1, =suurinVakio     ; Käännöksen jälkeen: LOAD R1, =32767
                          ;   Käännösvaiheessa suurinVakio on korvattu vakion ARVOLLA!
OUT R1, =CRT

LOAD R1, =pieninVakio     ; Käännöksen jälkeen: LOAD R1, =-32768
OUT R1, =CRT

LOAD R1, MILJARDI         ; Käännöksen jälkeen sanan MILJARDI tilalla on muuttujan osoite, eli käsky on muodossa LOAD R1, 10 
                          ;   (jos muuttujan MILJARDI osoite on 10)
OUT R1, =CRT              ;   Rekisterin R1 arvoksi asettiin osoitteessa 10 (eli osoitteessa MILJARDI) oleva arvo 1000000000.

SVC SP, =HALT

```

Tästä kannattaa huomata erityisesti, että pseudokäskyllä EQU voi asettaa vakiolle arvoja vain välillä -32768..32767. Sallitut arvot ovat siis samat kuin mitä lukuja osoite/vakio-osaan voi muutenkin kirjoittaa. Tämän välin ulkopuolisia lukuja voi tallettaa muuttujien arvoiksi ja käyttää niitä, kuten tässä esimerkissä tehtiin muuttujalle MILJARDI. 


### Tietueet ja taulukot

Tietueelle ja taulukolle varataan tilaa pseudokäskyllä DS (Data Segment) näin:


```
PERSON DS 4 
```

Tämä pseudokäsky alustaa taulukon PERSON nelialkioiseksi. Taulukolle varataan siis tilaa samantyyppisesti kuin Javassa `int person = new int[4]`. 

#### Rekisterin käyttö indeksinä

Taulukon läpikäyminen on kätevintä tehdä _indeksirekisterin_ avulla. Rekisteriä, jonka sisältämää arvoa käytetään taulukkoa läpikäydessä indeksinä, kutsutaan indeksirekisteriksi.

Esimerkki:

```
PERSON DS 4             ; Varataan taulukolle muistialue, kooltaan 4. Kääntäjä asettaa taulukon alkuosoitteeksi
                        ;   jonkin muistiosoitteen.

LOAD R2, =0             ; Käytetään rekisteriä R2 indeksirekisterinä. Indeksiksi asetetaan aluksi 0.
LOAD R1, =3             ; Ladataan rekisteriin R1 jokin arvo, tässä tapauksessa luku 3.
STORE R1, PERSON(R2)    ; Tämä käsky tallettaa (STORE) rekisterin R1 sisällön 
                        ;   muistiosoitteeseen, joka saadaan laskemalla yhteen
                        ;   indeksirekisterin R2 arvo ja PERSON -taulukon osoite.
                        ;   Kun R2=3, tallennus tapahtui PERSON-taulukon indeksiin 3.
                      

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


#### Luvuilla laskeminen

```
LOAD R1, =40  ; Ladataan rekisteriin R1 luku 40.
ADD R1, =2    ; Lisätään rekisterissä R1 olevaan arvoon luku 2.
```

#### Muuttujan arvoilla laskeminen ja tuloksen tallettaminen

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

#### Esimerkki: LUUPPI JA IF-THEN

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

#### Koodirivin muistiosoitteen käyttäminen hyppykäskyä varten

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

#### Esimerkki IF-THEN-ELSE

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


### Ehdolliset hypyt positiivisuuden ja negatiivisuuden perusteella

Näitä käskyjä käytetään rekisterin kanssa ilman mitään muuta vertailukäskyä.

| Operaatio | Merkitys | Esimerkki | Selite |
| :--- | :--- | :--- | :--- |
| JNEG | Jump if NEGative. | `JNEG R1, tuliNegaa` | Jos R1<0, hyppää osoitteeseen `tuliNegaa`. |
| JZER | Jump if ZERo | `JZER R3, onNolla` | Jos R3==0, hyppää osoitteeseen `onNolla`.|
| JPOS | Jump if POSitive | `JPOS R2, onPosii` | Jos R2>0, hyppää osoitteeseen `onPosii`. |
| JNNEG |	Jump if Not NEGative | `JNNEG R5, eiNega` |  Jos R5 >= 0, hyppää osoitteseen `eiNega` |
| JNZER |	Jump if Not ZERo) | `JNZER R4, eiNolla` | Jos R4 erisuuri kuin 0, hyppää osoitteeseen `eiNolla` |
| JNPOS |	Jump if Not POSitive | `JNPOS R1, eiPosii` | Jos R1 <= 0, hyppää `eiPosii` |


#### Yksinkertainen luuppi

Tulostetaan luvut 9...0 luupin avulla.
```
          LOAD R1, =9
Tulosta   OUT R1, =CRT
          SUB R1, =1
          JNNEG R1, Tulosta  ; Jos R1>=0, hyppää kohtaan TULOSTA
          SVC SP, =HALT
```



### Ehdolliset hypyt suuruusvertailukäskyn `COMP` jälkeen.

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


### Lukujen lisääminen taulukkoon ja taulukon lukeminen

Tässä esimerkissä käyttäjältä pyydetään 10 positiivista lukua jotka talletetaan taulukkoon. Jos käyttäjä syöttää ei-positiivisen luvun, tai kun kaikki luvut on saatu, ohjelma tulostaa annetut luvut viimeisenä annetusta alkaen ja ohjelma lopetetaan. Jos yhtään positiivista lukua ei ole annettu, mitään ei tulosteta ja ohjelma lopetetaan.

```
          LUVUT DS 10           ; Varataan tilaa taulukolle LUVUT
                                ;   Javassa: int[] LUVUT = new int[10]

          LOAD R1, =0           ; R1 = 0. Tätä käytetään indeksirekisterinä.

Anna      IN R2, =KBD           ; R2 = näppäimistöltä luettu luku.
          JNPOS R2, Elsa        ; Jos R2<=0, hyppää kohtaan Elsa.
          STORE R2, LUVUT(R1)   ; Tallenna R2:n arvo taulukkoon kohtaan R1 eli
                                ;   muistiosoitteeseen R1:n arvo + taulukon
                                ;   LUVUT osoite. Vertaa: LUVUT[R1] = R2;

          ADD R1, =1            ; Lisätään indeksirekisterin arvoa yhdellä.
          COMP R1, =10          ; Verrataan sitä 10:een.
          JLES Anna             ; Jos R1<10, hyppää kohtaan Anna

Elsa      JZER R1, LetItGo      ; Jos R1 == 0 (yhtään lukua ei ole annettu),
                                ;   hypätään kohtaan LetItGo


          ; Tulostetaan luvut viimeksi annetusta alkaen:

Snowgies  SUB R1, =1            ; Vähennetään indeksirekisteristä 1
          LOAD R2, LUVUT(R1)    ; R2 = LUVUT[R1]
          OUT R2, =CRT          ; Tulostetaan luku
          JPOS R1, Snowgies     ; Jos R1 > 0, hyppää kohtaan Snowgies

LetItGo   SVC SP, =HALT         ; Ohjelman lopetus.


```

Esimerkissä toteutettiin 

:snowflake: lukujen lisääminen taulukkoon

:snowflake: lukujen lukeminen taulukosta

:snowflake: ehdollinen hyppy suuruusjärjestysvertailukäskyn `COMP` avulla

:snowflake: ehdollisia hyppyjä rekisterin sisällön merkin (ei-positiivinen, nolla, positiivinen) perusteella

:snowflake: näytettiin kaksi suuntaa miten käydä läpi luuppia (0...9 ja 9...0).



### Osoitusmuodot ja osoitinmuuttujat

Konekäskyn yhteydessä voi käyttää osoitusmuotoina _välitöntä_, _suoraa_ ja _epäsuoraa_ osoitusta. Konekäskyn kääntämisen jälkeen nämä ilmaistaan kahtena bittinä konekäskyn binääriesityksessä kuten esitettiin aiemmin [täällä.](#konekäskyn-binääriesitys)


#### Esimerkki osoitusmuotojen käytöstä ja osoitinmuuttujista


```
; Alustetaan ensin muuttujat.

X DC 42       ; int x = 42; 
PX DC 0       ; X:n osoitinmuuttuja
PPX DC 0      ; PX:n osoitinmuuttuja 

; Määritellään osoitinmuuttujien arvot

LOAD R1, =X   ; Ladataan R1:een X:n OSOITE.
STORE R1, PX  ; Talletetaan X:n osoite PX:n arvoksi.
              ;   Nyt PX osoittaa X:n arvoon.

LOAD R1, =PX  ; Ladataan R1:een PX:n OSOITE
STORE R1, PPX ; Talletetaan PX:n osoite PPX:n arvoksi.

; Käytetään noita

LOAD R1, X    ; R1 = haeArvoOsoitteesta(X)

LOAD R1, @PX  ; Ladataan R1:een PX:n osoittama arvo
              ;   R1 = haeArvoOsoitteesta(haeArvoOsoitteesta(PX))
              ;   R1:ssä on nyt X:n arvo.

LOAD R1, @PPX ; Ladataan R1:een PPX:N osoittama arvo
              ;   R1 = haeArvoOsoitteesta(haeArvoOsoitteesta(PPX))
              ;   R1:ssä on nyt PX:n arvo (jonka arvo on X:n osoite)

```


### Välitön osoitus

Välitön osoitustapa on se, missä konekäskyn osoiteosassa eli oikeanpuoleisimpina 16 bittinä on jo valmiiksi eli välittömästi se arvo mitä on tarkoitus käyttää konekäskyn suorituksessa. Ohjelmoidessa välitön osoitus ilmaistaan = -merkillä. Esimerkiksi konekäskyssä `LOAD R1, =100` on kyse välittömästä osoitustavasta. Prosessori havaitsee välittömän osoitustavan konekäskyssä olevista kahdesta bitistä jotka kertovat muistinoutojen määrän. Välittömässä niiden bittien arvo on 0, joten mitään ei tarvitse hakea muistista ja prosessori voi ryhtyä suorittamaan käskyä.

> Välitön osoitus: Esimerkiksi `LOAD R1, =100` lataa rekisteriin R1 luvun 100.


### Suora osoitus

Suorassa osoituksessa konekäskyn osoiteosassa eli oikeanpuoleisimpina 16 bittinä on muistiosoite, josta operaatiota varten tarvittava tieto on haettava. Ohjelmoidessa suora osoitus ilmaistaan ilman mitään erikoismerkkejä. 

Esimerkki: Oletetaan että muuttujan X muistiosoite on 25, ja oletetaan että muuttujan X arvo on 1000. Tällöin konekäsky `LOAD R1, X` on kääntämisen jälkeen on muodossa `LOAD R1, 25`.

Käskyn nouto- ja suoritussyklissä asia tapahtuu näin:

1. Käsky on noudettu `LOAD R1, 25` käskyrekisteriin (IR, _Instruction Register_).
2. Prosessori hoitaa käskyn purkamisen osiin erittäin tehokkaasti. Käskyrekisteristä osoiteosan (oikeanpuoleisimmat 16 bittiä) sisältö (tässä luku 25) siirtyy nopeiden johtimien avulla heti väliaikaisrekisteriin (TR, _Temporary Register_, "temppari").
3. Prosessori tarkistaa osoitustavan osoitustapaa osoittavista biteistä. Koska tässä on kyse suorasta osoituksesta, niiden bittien arvo on 1. Siitä prosessori tietää, että tarvitaan yksi muistinouto lisää.
4. Seuraavaksi haetaan muistiosoitteesta 25 sen sisältö, joka on yllä oletettu olevan 1000, ja se talletetaan väliaikaisrekisteriin.
5. Ja sitten käsky suoritetaan: rekisterin R1 arvoksi tulee väliaikaisrekisterin arvo 1000.

> Suora osoitus: esimerkiksi `LOAD R1, X` lataa rekisteriin R1 _muistiosoitteen_ X sisällön eli toisin sanottuna _muuttujan_ X arvon.


### Epäsuora osoitus

Epäsuora osoitus ilmaistaan ohjelmoidessa @ -merkillä. Esimerkiksi `LOAD R1, @PX` lataa rekisteriin R1 muuttujan PX arvon mukaisen muistipaikan sisällön. Toisella tavalla sanoen se konekäsky lataa rekisteriin R1 _muuttujan PX osoittaman arvon_.

Osoitustapaa ilmaisevien bittien arvo on epäsuorassa osoituksessa 2, joten prosessori tekee 2 muistinoutoa.

### Esimerkki osoitinmuuttujista

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


## Aliohjelmat

### Aliohjelma arvoparametreilla

Esimerkki aliohjelman kutsumisesta ja toteutuksesta. Tässä esimerkissä aliohjelmalle välitetään arvoparametrit aktivaatiotietueen avulla. Aktivaatiotietue on alue muistissa, jolla ratkaistaan aliohjelmien toteutus. Se toimii pinona. Pinoon talletetaan arvoja `PUSH` -konekäskyllä ja sieltä saadaan otettua arvoja `POP` -konekäskyllä.

Esimerkissä on kommentit jotka auttavat ymmärtämään miten aliohjelmien kutsu ja toteutus toimii. Tämä kannattaa kokeilla Titokoneella. On suositeltavaa, että muokkaat sitä haluamallasi tavalla niin opit paremmin. Voit esimerkiksi antaa aliohjelmalle lisää parametreja ja tehdä niillä jotain laskentaa aliohjelmassa.

```
; Tämä on .k91 -tiedosto, joka on käännettävissä Titokoneella.
x dc 314                    ; Määritellään x muuttujaksi ja asetetaan sille alkuarvo 314.
                            ; Huomaa, että tämä ei ole muodossa x dc =314, vaikka muutoin
                            ;   käytetään merkkiä = kun esim. ladataan rekisteriin jokin luku
                            ;   tähän tyyliin: load r1, =314.
y dc 227                    ; Määritellään y muuttujaksi ja asetetaan sille alkuarvo 227
        
push sp, =0                 ; varataan pinoon tilaa paluuarvolle
push sp, x                  ; pushataan muistiosoitteessa x oleva arvo pinoon (arvoparametri)
push sp, y                  ; pushataan muistiosoitteessa y oleva arvo pinoon (arvoparametri)
        
call sp, myLittleSuby       ; kutsutaan aliohjelmaa myLittleSuby
                            ; call -käsky siirtää automaattisesti vanhan PC:n ja FP:n (R7:n) arvon pinoon

pop sp, r1                  ; Aliohjelmasta on palattu. Popataan sp-pinon päällimmäinen 
                            ;   arvo rekisteriin r1.
                            ; Näin siis saatiin aliohjelman paluuarvo pääohjelmaan.
        
out r1, =crt                ; Tulostetaan se

svc sp, =halt               ; Pääohjelman lopetus
        
; Aliohjelman toteutus
; Varataan ensin tilaa muuttujille jotta on helpompi muistaa mitä on tekemässä.
; Muuttujien nimeämisessä on muistettava että ne ovat globaaleja eli niiden tulee olla yksilöllisiä.
; Jos siis käytät aliohjelmassa allaolevan sx:n paikalla x (jota käytetään pääohjelmassa), tulee ongelmia.
            
paluuarvo equ -4    
sx equ -3            
sy equ -2                   ; viimeisenä sp-pinoon laitettu arvo on kohdassa -2(fp) eli siis fp:n arvo - 2  
                            ; fp-1 ja fp-0 eli kaksi ylintä ovat pääohjelman PC:tä ja FP:tä varten

; Aliohjelma:
MYLITTLESUBY pushr sp       ; pushr sp laittaa rekisterit r0-r6 (r6=sp) pinoon jotta
                            ; voit käyttää vapaasti rekistereitä aliohjelmassa
                      
load r1, sx(fp)             ; Lataa r1:een osoitteessa fp-3 oleva arvo
load r2, sy(fp)             ; Lataa r2:een osoitteessa fp-2 oleva arvo
            
mul r1, r2                  ; Tehdään pikku laskutoimitus r1 = r1 * r2 jotta tämä 
                            ;   aliohjelma tekee edes jotain.
            
store r1, paluuarvo(fp)     ; Nyt sp-pinossa on osoitteessa fp-4 aliohjelman paluuarvo
popr sp                     ; Popataan pinosta siihen pushr sp:llä laitetut rekisterit
exit sp, =2                 ; Palataan pääohjelmaan siten että 2 pinon päällimmäistä arvoa 
                            ; poistetaan pinosta (nuo mitkä laitettiin push sp, x ja 
                            ; push sp, y aliohjelmaa kutsuttaessa.
                            ; Pinon päällimmäisenä on nyt ennen aliohjelman kutsua 
                            ; ensiksi laitettu arvo eli kohdassa fp - 4 oleva arvo,
                            ; jota aliohjelma on muuttanut.
          
```


### Aliohjelma viiteparametreilla

Viiteparametreja käytettäessä aliohjelmalle välitetään muuttujan tai taulukon osoite. Muuttujan osoitteen välittämisen etuna on se, että aliohjelma voi lukea ja muuttaa annetun muuttujan arvoja. Taulukon osoitteen välittämisen hyöty on se, että aliohjelma voi lukea ja muuttaa taulukon sisältöä.


```
; Tämä on .k91 -tiedosto, joka on käännettävissä Titokoneella.
x dc 42                     ; Määritellään x muuttujaksi ja asetetaan sille alkuarvo 42.
                            ; Huomaa, että tämä ei ole muodossa x dc =42, vaikka muutoin
                            ;   käytetään merkkiä = kun esim. ladataan rekisteriin jokin luku
                            ;   tähän tyyliin: load r1, =42.
        
push sp, =x                 ; pushataan muuttujan x osoite pinoon (viiteparametri)

y dc 33                     ; Koska aliohjelma on toteutettu siten, että siihen halutaan viiteparametrit,
                            ;   alustetaan nyt muuttuja jota voi käyttää viiteparametrina.
                            ;   Tällainen muuttujan alustus voidaan siis tehdä keskellä koodia.
                            ;   Sitä ei ole pakko tehdä aivan lähdekoodin alussa. 

push sp, =y                 ; pushataan muuttujan y osoite pinoon (viiteparametri)
        
call sp, SWAP               ; kutsutaan aliohjelmaa SWAP, joka vaihtaa x:n ja y:n arvot keskenään
                            ; call -käsky siirtää automaattisesti vanhan PC:n ja FP:n (R7:n) arvon pinoon

; Nyt tässä ei ole mitään erillistä paluuarvoa, sillä aliohjelma muutti suoraan 
; viiteparametreina annettujen muuttujien arvoja. Paluuarvon välittämistä 
; on käsitelty yllä olevassa esimerkissä.

load r1, x                  ; Aliohjelmasta on palattu. Ladataan aliohjelman muuttama x:n arvo rekisteriin r1.
load r2, y                  ; Ja samoin y:n arvo.
        
out r1, =crt                ; Tulostetaan x:n arvo, joka on nyt 33 eli entinen y:n arvo.
out r2, =crt                ; Tulostetaan y:n arvo, joka on nyt 42 eli entinen x:n arvo.

svc sp, =halt               ; Pääohjelman lopetus
        
; Aliohjelman toteutus
; Varataan ensin tilaa muuttujille jotta on helpompi muistaa mitä on tekemässä.
; Muuttujien nimeämisessä on muistettava että ne ovat globaaleja eli niiden tulee olla yksilöllisiä.
; Jos siis käytät aliohjelmassa allaolevan sx:n paikalla x (jota käytetään pääohjelmassa), tulee ongelmia.

; Aliohjelma swap vastaa korkean tason kielen komentoa public static void swap(Luku a, Luku b). 
;   Se siis vaihtaa parametreina annettujen muuttujien arvot vähän samantyyppisesti kuin olioiden 
;   sisäistä tilaa voidaan muuttaa Javassa. Tässä tapauksessa aliohjelmalle on annettava 
;   parametreina muuttujien osoitteet (push sp, =muuttujannimi. Erillistä paluuarvoa ei nyt välitetä.

sx equ -3            
sy equ -2                   ; viimeisenä sp-pinoon laitettu arvo on kohdassa -2(fp) eli siis fp:n arvo - 2  
                            ; fp-1 ja fp-0 eli kaksi ylintä ovat pääohjelman PC:tä ja FP:tä varten

; Aliohjelma:
SWAP pushr sp               ; pushr sp laittaa rekisterit r0-r6 (r6=sp) pinoon jotta
                            ; voit käyttää vapaasti rekistereitä aliohjelmassa
                      
load r1, @sx(fp)            ; Lataa r1:een arvo, johon osoitteessa fp-3 oleva arvo viittaa
load r2, @sy(fp)            ; Lataa r2:een arvo, johon osoitteessa fp-2 oleva arvo viittaa
store r1, @sy(fp)           ; Talletetaan r1:stä arvo osoitteeseen johon sy(fp) eli fp-2 viittaa
store r2, @sx(fp)           ; Talletetaan r2:sta arvo osoitteeseen johon sx(fp) eli fp-3 viittaa

; Nyt voidaan lähteä pois aliohjelmasta. Viiteparametreina annettujen muuttujien arvot on vaihdettu.

popr sp                     ; Poprataan pinosta siihen pushr sp:llä laitetut rekisterit. Tämä käsky
                            ;   asettaa aliohjelmaa kutsuneen rutiinin rekisterien arvot takaisin.
exit sp, =2                 ; Palataan pääohjelmaan. Käsky asettaa FP:n ja PC:n arvon ennalleen ja
                            ;   poistaa pinosta 2 päällimmäistä arvoa, eli nuo mitkä laitettiin
                            ;   push sp, =x ja push sp, =y käskyillä aliohjelmaa kutsuttaessa.
                            ;   Pino-osoitin SP osoittaa nyt siihen mihin kuuluukin. Aliohjelmalle
                            ;   parametreina annetut osoitteet eivät ole enää pinossa.
                            ;   (Tarkalleen ottaen ne ovat edelleen muistissa siellä
                            ;   mihin ne laitettiinkin, mutta niihin ei enää pääse
                            ;   käsiksi POP -käskyllä.)
          
```


## Esimerkki kevään 2018 kokeen aliohjelmatehtävästä

### Tehtävänanto

Funktio F(a, b) palauttaa arvonaan lausekkeen 2a+3b+1 arvon. 
Esimerkiksi lausekkeen x = F(1,1) suorituksen jälkeen muuttujan x arvo on 6. 
Parametri a on arvoparametri ja parametri b on viiteparametri. Muuttuja x on määritelty pääohjelmatasolla.

i. [3 p] Toteuta ttk-91 symbolisella konekielellä (funktiota F kutsumalla) lauseke x = F(x, 3).

ii. [4 p] Toteuta ttk-91 symbolisella konekielellä funktio F.

Noudata suositusten mukaista aliohjelmien (funktioiden) kutsumekanismia.

(Lähde: Tietokoneen toiminta -kurssin koe 7.5.2018., tehtävä 4 d. Kokeen laatinut Teemu Kerola.)


### Malliratkaisu

#### (i)

Muuttuja x on tehtävänannon mukaan määritelty pääohjelmatasolla (eli ei aliohjelmassa). Sitä ei tarvitse siten tässä enää määritellä.
Koska a on arvoparametri, välitetään aliohjelmalle sen arvo.
Koska b on viiteparametri, välitetään aliohjelmalle sen osoite. 


| Ohjelman koodi | Selite |
| -------------- | --- |
|`b dc 3`| Alustetaan muuttuja b ja annetaan sen arvoksi 3.|
|`push sp, =0`| Varataan pinosta tilaa **paluuarvoa** varten puskemalla pinoon joku luku.|
|`push sp, x`| Pusketaan pinoon x:n arvo, eli aliohjelman (funktion) F ensimmäinen parametri.<br>Välitettiin **arvo**, koska ensimmäinen parametri on **arvoparametri.**|
|`push sp, =b`| Pusketaan pinoon muuttujan b **osoite**, eli aliohjelman (funktion) F toisen parametrin osoite.<br>Osoite on **viite muuttujaan**. Käytettiin siis **viiteparametria**.|
|`call sp, F`| Kutsutaan aliohjelmaa F |
|`pop sp, r1`| Popataan pinosta aliohjelman sinne laskema arvo.|
|`store r1, x`| Talletaan se arvo muuttujan x arvoksi eli osoitteeseen x.|

Valmista! Nyt x = F(x,3).


#### (ii)

Toteutetaan funktio F(a,b) = 2a + 3b + 1.
Esimerkki mitä funktion kuuluu palauttaa. Olkoon a=10 ja b=5. Tällöin F(a,b) = F(10,5) = 2\*10 + 3\*5 + 1 = 20+15+1 = 36.

Ohjelman koodi | Selite
-------------- | ---
`paluuarvo equ -4` | Paluuarvon sijainti suhteessa rekisteriin FP on -4.
`arvoA equ -3` | Ensimmäisen parametrin sijainti suhteessa rekisteriin FP on -3.
`viiteB equ -2` | Toisen parametrin sijainti suhteessa rekisteriin FP on -2.
| | Yllä on määritelty vakiot auttamaan viittaamisessa. Alla on varsinainen aliohjelma.
`F pushr sp` | Aliohjelma alkaa. Aliohjelman nimi **F** määritellään tässä.<br> Rivillä on oltava koodia. PUSHR SP Tallettaa rekisterit talteen.
`load r1, arvoA(fp)` | Ladataan r1:een ensimmäisen parametrin arvo.
`load r2, @viiteB(fp)` | Ladataan r2:een toisen parametrin osoittama arvo.
`mul r1, =2` | r1 = 2a
`mul r2, =3` | r2 = 3b
`add r1, r2` | r1 = r1 + r2 = 2a + 3b
`add r1, =1` | r1 = r1 + 1, joten nyt rekisterissä r1 on 2a+3b+1
`store r1,   paluuarvo(fp)` | Talletetaan palautettava arvo pinoon kohtaan paluuarvo + fp.<br>Tätä merkitään koodissa paluuarvo(fp).<br>Vakion paluuarvo arvo on -4, joten tämän voisi kirjoittaa myös -4(fp).
`popr sp` | Palautetaan rekisterien arvot, jotka oli pushrattu aliohjelman alussa.
`exit sp, =2` | Poistetaan 2 ylintä arvoa pinosta, jolloin ylimpänä on paluuarvo, ja poistutaan aliohjelmasta.

Valmista :relaxed: :tada:

>Huomaa, että parametrien sijainnin numerointi alkaa -2:sta, koska kohdissa -1 ja 0 on vanha PC ja vanha FP.
>Huomaa, että nämä equ-vakiot eivät saa olla nimetty samoilla nimillä kuin muualla koodissa!!!

(Lähde: Tietokoneen toiminta -kurssin 7.5.2018., tehtävän 4 d malliratkaisu. Malliratkaisun laatinut Harri Kähkönen.)

                        



