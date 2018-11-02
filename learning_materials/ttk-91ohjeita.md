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


## Muuttujat, vakiot, tietueet, taulukot ja laskutoimitukset

### Muuttujat

Muuttujat alustetaan _pseudokäskyllä_ DC (Data Constant) näin:

```
A DC 0
B DC 42
```
_Pseudokäsky_ ei ole "oikea" konekäsky. Se huomioidaan vain käännösvaiheessa. Esimerkiksi `A DC 0` pseudokäsky varaa käännösvaiheessa muuttujalle A muistiosoitteen ja asettaa sen arvoksi 0. Kääntäjä asettaa _symbolitauluun_ tiedon muuttujan nimestä ja osoitteesta.

> Muuttujan määrittely ja alustaminen `A DC 0`. Samantapaisesti siis kuten Javassa `int a = 0;`
> Muuttujan arvoa voidaan muuttaa, vaikka tuo DC käsky on lyhenne sanoista data constant. Yleensä constant on vakio jota ei voi muuttaa.

### Vakiot

Vakiot määritellään pseudokäskyllä EQU näin:

```
VASTAUS EQU 42
```
> Vakiota ei voi muuttaa. Sen arvo on aina se mihin se ohjelmassa määritellään. Tätä vastaa Javascriptin uusimmissa versiossa käytettävä `const vastaus = 42`.
Kuten DC:n, myös EQU:n avulla määritellyn vakion nimi ja arvo talletetaan symbolitauluun käännösvaiheessa.

#### Tietueet ja taulukot

Tietueelle ja taulukolle varataan tilaa pseudokäskyllä DS (Data Segment) näin:

```
PERSON DS 4 
```
Tämä pseudokäsky alustaa taulukon PERSON nelialkioiseksi. Taulukolle varataan siis tilaa samantyyppisesti kuin Javassa `int person = new int[4]`. 


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
