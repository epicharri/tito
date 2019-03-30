# Lukujärjestelmistä

## Tito -minikokeen 19.11.2018 [1] ratkaisut selityksineen

### a) Mitkä ovat kokonaislukujen +20 ja -20 Little-Endian 32-bittiset etumerkilliset esitysmuodot?
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
* Muutetaan ensin positiivinen luku +20 binääriluvuksi:
  * 20 = 16\*__1__ + 8\*__0__ + 4\*__1__ + 2\*__0__ + 1\*__0__
* Saadaan binääriluku `10100`.
* Seuraavaksi muutetaan se 32-bittiseksi binääriluvuksi:
* Saadaan `0000 0000  0000 0000  0000 0000  0001 0100`, joka on nyt luku +20 luontaisessa Big-Endian -esitysmuodossa, jossa eniten merkitsevä tavu on ensimmäisenä (8 ensimmäistä bittiä vasemmalta luettuna) ja vähiten merkitsevä tavu viimeisenä (8 viimeistä bittiä vasemmalta luettuna).
* Big Endian etumerkillisessä esitysmuodossa ensimmäinen bitti vasemmalta ilmaisee merkin. + on 0 ja - on 1. Vaihdetaan nyt merkki lukua -20 varten:
* Saadaan `1000 0000  0000 0000  0000 0000  0001 0100`, joka on nyt luku -20 luontaisessa Big Endian -esitysmuodossa.
* Seuraavaksi vaihdetaan tavujärjestys käänteiseen Little Endian -muotoon luvulle +20:
* Saadaan `0001 0100  0000 0000  0000 0000  0000 0000` tai heksadesimaalimuodossa `0x14000000`.
* Ja vastaavasti vaihdetaan tavujärjestys käänteiseen Little Endian -muotoon luvulle -20:
* Saadaan `0001 0100  0000 0000  0000 0000  1000 0000` tai heksadesimaalimuodossa `14000080`.


</details>


### b) Mitkä ovat kokonaislukujen +20 ja -20 Big-Endian 32-bittiset kahden komplementin esitysmuodot?
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
Tehtävässä a on jo kokonaisluku +20 muutettu Big-Endian 32-bittiseksi kokonaisluvuksi `0000 0000  0000 0000  0000 0000  0001 0100`.

Big Endian kahden komplementin esitysmuodossa oleva positiivinen luku on valmiiksi oikeassa muodossaan. Määritellään siis enää luvun +20 vastaluku -20:

* Otetaan luvusta `0000 0000  0000 0000  0000 0000  0001 0100` ensin komplementti kääntämällä bitit (eli 0 -> 1 ja 1 -> 0).
* Saadaan `1111 1111  1111 1111  1111 1111 1110 1011`.
* Lisätään luku `1`.
* Saadaan `1111 1111  1111 1111  1111 1111 1110 1100`, joka on luku -20 Big Endian kahden komplementin esitysmuodossa.

</details>



### c) Mitkä ovat kokonaislukujen +20 ja -20 8-bittiset vakiolisäys 127 -esitysmuodot?
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
* Luku -20: Lisätään lukuun -20 vakiolisäys 127 ja saadaan 107, joka on binäärilukuna `0110 1011`.
* Luku +20: Luku 20 + vakiolisäys 127 = 147 muutettuna binäärilukumuotoon eli `1001 0011`.

* Vakiolisäys -esitystavassakin merkitsevimmän tavun (tässä vain yksi tavu) ensimmäisestä bitistä näkee minkä merkkinen luku on, mutta toisin kuin muissa esitystavoissa, 0 on negatiiviset luvut ja 1 positiiviset luvut.

</details>


### d) Mitkä kaksi etua saadaan, kun 8-bittisessä vakiolisäys-esitysmuodossa vakiolisäys on nimenomaan 127?
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
* Saadaan ilmaistua yksikäsitteisesti luvut välillä -127 ... +128, eli puolet ei-positiivisia ja puolet positiivisia lukuja
* Lukujen järjestys vakiolisäys-esitysmuodossa on sama kuin lukujen todellinen järjestys.
  * Esimerkki: Luku -127 + vakiolisäys 127 = 0 ja luku -126 + vakiolisäys 127 = 1.
  * Esimerkki: Luku -1 + vakiolisäys 127 = 126 ja luku 0 + vakiolisäys 127 = 127.

</details>


### e) Mikä on IEEE liukulukustandardin mukainen 32-bittinen normalisoitu Big-Endian esitysmuoto liukuluvulle -8.25?
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
Oikea vastaus on `1 1000 0010 0000 1000 0000 0000 0000 000`.

IEEE:n 32-bitin liukulukustandardissa liukuluku muodostuu merkistä eli +/- (1 bitti), eksponentista vakiolisäysmuodossa (8 bittiä) ja mantissasta (23 bittiä).

* Merkki on -, joten ensimmäinen bitti on 1.
* Muunnetaan 8,25 binaariluvuksi. Luku 8 on `1000` ja 0,25 eli 1/4 on `.01`. Saadaan `1000.01`.
  * 8,25 = 1\*8 + 0\*4 + 0\*2 + 0\*1 + 0\*0,5 + 1\*0,25
* Muutetaan luku muotoon jossa ensimmäinen numero vasemmalta lukien on 1:
  * `1000.01 = 1.00001 * 2^3`.
  * Pistettä siirrettiin 3 numeroa vasemmalle, joten eksponentti on 3.
  * (Tämä on vastaavaa kuin kymmenkantaisessa järjestelmässä se, että 1729,25 = 1,72925 * 10^3.)
* IEEE-liukulukustandardissa oletetaan, että luku on siinä muodossa, että ensimmäinen bitti on 1, joten sitä ei tarvitse tallettaa! Siispä saamme mantissaksi pisteen oikeanpuoleiset luvut lisättynä riittävällä määrällä nollia, eli `0000 1000 0000 0000 0000 000`.
* Eksponentti on ilmaistava vakiolisäys 127 muodossa. 3 + 127 = 130, joka on 8-bittisenä binäärilukuna `1000 0010`.
* Lopuksi yhdistetään merkki, eksponentti vakiolisäysmuodossa ja mantissa ja saadaan lopullinen vastaus `1 1000 0010 0000 1000 0000 0000 0000 000`.
* Jos vielä halutaan, voidaan ryhmitellä luku puolitavun (4 bitin) osiin, josta se on kätevä muuttaa heksadesimaaliksi: `1100 0001 0000 0100 0000 0000 0000 0000`, josta saadaan `0xC1040000`. Käänteisesti muutos tehdään vastaavasti. Jos olisi annettuna heksadesimaaliluku `0xC1040000`, sen muuntaminen binääriluvuksi on suoraviivaista, onhan `0xC = 0b1100, 0x1 = 0b0001, 0x0 = 0b0000, 0x4 = 0b0100`.


</details>


### f) Kaukokirjoittimen (teleprinter, telex) ASCII-koodissa on 7-bittinen merkkikoodisto. Telexiä käytetään edelleen, mutta nykyään merkit yleensä koodataan 8-bittisiin tavuihin. Tuon ylimääräisen bitin voisi käyttää pariteettibittinä. Mitä etua tästä saataisiin? Mitä kustannuksia siitä tulisi? Kuinka pariteettibitin arvo asetetaan? Kuinka pariteettibittiä käytettäisiin tässä tapauksessa? Kuka sen asettaisi ja kuinka sitä käytettäisiin tiedon eheyden tarkistamiseen? Mitä tapahtuu, jos järjestelmä havaitsee pariteettivirheen? 
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
Ratkaisua ei ole vielä lisätty.

</details>


### g) ECC muisti (Error-correcting code memory) tallettaa datan 128 bitin (16 tavua) lohkoina. Lohkot on suojattu muistissa Hamming-koodilla, joka korjaa kaikki 1 bitin virheet ja havaitsee kaikki 2 bitin virheet.

i. Montako ylimääräistä bittiä tarvitaan? Selitä?
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
Ratkaisua ei ole vielä lisätty.

</details>


ii. Kuka asettaa nämä ylimääriset bitit ja miten? Kuka tarkistaa niiden arvot ja milloin? 
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
Ratkaisua ei ole vielä lisätty.

</details>


iii. Tehdäänkö Hamming-koodin laskenta laitteistolla vai ohjelmistolla? Selitä. 
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
Ratkaisua ei ole vielä lisätty.

</details>


iv. Oletetaan nyt, että satunnainen alkeishiukkanen kääntää (muuttaa) yhden bitin muistiin talletetussa lohkossa. Kuinka tämä virhe korjataan? Kuka sen tekee ja milloin?
<details>
  <summary>Katso vastaus klikkaamalla.</summary>

#### Ratkaisu
Ratkaisua ei ole vielä lisätty.

</details>


Ratkaisut selityksineen laatinut Harri Kähkönen.


Lähteet
1. Kerola, Teemu. Tietokoneen toiminta -kurssin minikoe 2, 19.11.2018. Luettu 30.3.2019 https://www.cs.helsinki.fi/group/nodes/kurssit/tito/kokeet/2018/2018_11_19_mk2.pdf


