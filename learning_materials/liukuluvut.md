# IEEE 32 bitin Big Endian liukuluku

Big Endian -tavujärjestys on tuttu ja turvallinen esitystapa jossa merkitsevin tavu on vasemmalla ja vähiten merkitsevä oikealla. Aivan kuten kymmenjärjestelmän luvuissa, joissa esimerkiksi luvussa 197 merkitsevin osa on sataset, toiseksi merkitsevin osa kymmenet ja vähiten merkitseviä on ykköset. Tässä ei käsitellä enempää tavujärjestystä vaan oletetaan se tunnetuksi.

Oletetaan myös että lukija on tutustunut sen jonkin verran binäärilukuihin sekä siihen mikä yleisesti ottaen on IEEE 32-bittinen liukuluku. Tässä esitetään algoritmi desimaaliluvun muuntamisesta IEEE-liukuluvuksi. Erikoistapausta (eksponentti vakiolisäysmuodossa 0 ja piilobitti poikkeuksellisesti 0) ei käsitellä eikä myöskään alle yhden suuruisia desimaalilukuja.


## Desimaaliluvun muuntaminen IEEE-liukuluvuksi

Esitetään desimaaliluvun muuntaminen IEEE-liukuluvuksi esimerkin avulla.


## Esimerkki

Muunnetaan desimaaliluku **-50,8125** liukuluvuksi.

### 1. Merkki (+ vai -)

IEEE-liukuluvussa ensimmäinen bitti vasemmalta ilmaisee merkin. Positiista lukua ilmaisee luku 0, sillä (-1)<sup>0</sup> = 1. Negatiivista lukua ilmaisee luku 1, sillä (-1)<sup>1</sup> = -1.

#### **Positiivisen (+) merkki on 0**
#### **Negatiivisen (-) merkki on 1**

Luku -50,8125 on negatiivinen, joten etumerkiksi tulee 1. Ensimmäinen osa liukulukuesitystä on selvitetty :nerd_face:.


### 2. Desimaaliluvun kokonaislukuosan muunto binääriluvuksi

Muunnetaan desimaaliluvun 50,8125 kokonaislukuosa **50** binääriluvuksi.

| Jaettava  | | Jakaja | | Tulos | | Jakojäännös | Monesko bitti |
| ------------- | - | ------------- | - | ------------- | - | ------------- | ------------- |
| 50 | : | 2 | = | **25** |  | `0` | Viimeinen bitti (oikeanpuoleisin bitti) |
| **25** | : | 2 | = | **12** |  | `1` | Toiseksi viimeisin bitti |
| **12** | : | 2 | = | **6** |  | `0` | Kolmanneksi viimeisin bitti |
| **6** | : | 2 | = | **3** |  | `0` | Neljänneksi viimeisin bitti |
| **3** | : | 2 | = | **1** |  | `1` | Viidenneksi viimeisin bitti |
| **1** | : | 2 | = | 0 |  | `1` | Kuudenneksi viimeisin bitti (vasemmanpuoleisin bitti) |

Saimme lopulta jakolaskun tulokseksi **0**. Nyt haluttu binääriluku saadaan valitsemalla jakojäännökset ylhäältä alas luettuna.

Siten **50 = 110010<sub>2</sub>**.

Alaviitteellä 2 voi osoittaa, että kyse on 2-kantaisesta luvusta eli binääriluvusta. Sitä ei ole välttämätöntä kirjoittaa, mutta sen käyttäminen voi helpottaa näkemään että on kyse binääriluvusta.

### 3. Desimaaliluvun desimaaliosan muunto binääriluvun binääriosaksi

Muunnetaan desimaaliluvun 50,8125 desimaaliosa (eli pilkun oikealla puolella oleva osa) binääriosaksi.

| Kerrottava  | | Kertoja | | Kokonaisosa = bitti | | Desimaaliosan esitys | 
| ------------- | - | ------------- | - | ------------- | - | ------------- | 
| 0,8125 | * | 2 | = | `1` | + | **0,625** |
| **0,625** | * | 2 | = | `1` | + | **0,250** |
| **0,250** | * | 2 | = | `0` | + | **0,500** |
| **0,500** | * | 2 | = | `1` | + | 0,000 |

Saimme lopulta kertolaskun tuloksen desimaaliosan esitykseksi **0**, joten binääriosan laskeminen on valmis. Nyt haluttu binääriosa on kokonaisosat ylhäältä alas luettuna.

Siten desimaaliosa **,8125 on 1101<sub>2</sub>**.

Tässä tapauksessa saimme äärellisen pituisen binääriosan. Jos desimaaliosa olisi esimerkiksi luvusta 1,20, niin binääriosa olisi päättymätön. Tällöin binääriosaa lasketaan vain siihen saakka mitä on mahdollista saada mahtumaan liukulukuun.


### 4. Eksponentin valinta


#### Yhdistetään kokonaisosa ja binääriosa

Yhdistetään kohdassa 2 muunnettu kokonaisosa **50 = 110010<sub>2</sub>** ja binääriosa **,8125 on 1101<sub>2</sub>**.

Saadaan **110010.1101<sub>2</sub>**.


#### Lasketaan eksponentti

Eksponentti saadaan laskemalla kuinka paljon binääripistettä tulee siirtää jotta binääriesityksen ensimmäisen 1-bitin oikealle puolelle tulee piste. Koska meillä on luku 110010.1101, on siirrettävä pistettä 5 bittiä vasemmalle. Binääripisteen vasemmalle siirtäminen vastaa kahdella kertomista siirtojen määrän verran. Siis:

110010.1101 = 1.100101101 * 2<sup>5</sup>.

Pistettä siirrettiin 5 pistettä vasemmalle jolloin saimme luvun muotoon 1.100101101 * 2<sup>5</sup>. Luku 5 on haluamamme eksponentti.

Samalla saadaan määriteltyä **mantissa** luvusta 1.100101101. Palataan siihen kuitenkin alempana.


#### Muunnetaan eksponentti vakiolisäys 127 -muotoon

Tämä tapahtuu helposti. Eksponenttiin 5 lisätään luku 127, jolloin saadaan luku 132.


#### Muunnetaan vakiolisäys 127 -muodossa oleva eksponentti binääriluvuksi

Muunnetaan saatu luku 132 binääriluvuksi:

| Jaettava  | | Jakaja | | Tulos | | Jakojäännös | Monesko bitti |
| ------------- | - | ------------- | - | ------------- | - | ------------- | ------------- |
| 132 | : | 2 | = | **66** |  | `0` | Viimeinen bitti (oikeanpuoleisin bitti) |
| **66** | : | 2 | = | **33** |  | `0` | Toiseksi viimeisin bitti |
| **33** | : | 2 | = | **16** |  | `1` | Kolmanneksi viimeisin bitti |
| **16** | : | 2 | = | **8** |  | `0` | Neljänneksi viimeisin bitti |
| **8** | : | 2 | = | **4** |  | `0` | Viidenneksi viimeisin bitti |
| **4** | : | 2 | = | **2** |  | `0` | Kuudenneksi viimeisin bitti |
| **2** | : | 2 | = | **1** |  | `0` | Seitsemänneksi viimeisin bitti |
| **1** | : | 2 | = | 0 |  | `1` | Kahdeksanneksi viimeisin bitti (vasemmanpuoleisin bitti) |


Koska saimme jakolaskun tulokseksi 0, olemme valmiit. Haluttu binääriluku on jakojäännökset alhaalta ylös lukien eli **10000100<sub>2</sub>**.

Olemme saaneet toisen osan eli eksponentin liukulukuesitystä varten :sunglasses:.



### 5. Mantissa

Tämä on myös nyt helppoa. Yllä eksponenttia selvittäessä on jo muunnettu binääriesitys 110010.1101 muotoon 1.100101101 * 2<sup>5</sup>.

Koska IEEE-liukulukustandardissa käytetään _piilobittiä_, eli mantissan ensimmäinen osa oletetaan luvuksi 1, sitä ei tarvitse merkitä mantissaan. Binääriesitys on muodossa 1.100101101. Nyt pisteen vasemmalla puolella on bitti, jota ei laiteta mantissaan.
> Piilobitti on IEEE-liukuluvussa binääripisteen vasemmalla puolella oleva bitti 1, jonka oletetaan olevan 1, joten sitä ei tarvitse merkitä. Piilobitin ansiosta voidaan ilmaista luku tarkemmin.

Mantissan pituus on 23 bittiä. Se alkaa tuosta binääripisteen oikealta puolen ja loppuosa täytetään nollilla. Mantissa on siten tässä tapauksessa `10010110100000000000000`.


### YHDISTETÄÄN KAIKKI

Yllä on jo määritetty kaikki liukuluvun osat ja tässä ne nyt yhdistetään :revolving_hearts:.

| + vai - | Eksponentti 8 bittiä | Mantissa 23 bittiä |
| --- | --- | --- |
| `1`| `10000100` | `10010110100000000000000` |

Joten luku -50,8125 on IEEE 32-bittisenä liukulukuna `1100 0010 0100 1011 0100 0000 0000 0000` 

Kannattaa harjoitella näitä muutoksia eri luvuilla. Muunna esimerkiksi nämä luvut 2,5 ja 4,125 IEEE 32-bittiseksi liukuluvuksi. On myös mielenkiintoista muuntaa liukuluvuksi luku jonka desimaaliosa ei ole täsmälleen kahden potenssien summa, esimerkiksi luku 5,1.


