# Harjoitustehtävän edistymisen kuvaus

## Alku
En mainitse tehtävänantoa tässä suoraan, ettei se vuoda :).

Aloitan tutustumalla materiaaliin. Ensiksi kävin katsomassa että kyseinen juna tosiaan lähtee ensi keskiviikkona ja näyttää olevan.
Arvioitu saapumisaika VR:n sivujen mukaan 15:58 (paljon paremmat nämä uudet sivut! :).
Tutkitaan API-rajapinnan kuvausta yksittäisten junien hausta. Paljon turhia kenttiä suhteessa tämän tehtävän toteutukseen, filtteröin mielessä oleellisimmat kentät
jotka hetken pohdittuani tuntuvat olevan trainNumber sekä departureDate. Junan timeTableRow listauksesta pitää ottaa huomioon stationShortCode, type (arrival vai departure), trainStopping, cancelled, actualTime sekä differenceInMinutes. Huomaan myös, että timestampit jotka tulevat rajapinnasta näyttävät olevan UTC aikaa.

Tästä filtteröinnistä päästään luomaan tauluja luomaani postgres kantaan:

![alt text](https://github.com/Konnila/harkkis/blob/main/creata_table_train.PNG "Create train")
Huom tässä tehtävässä tulen lisäämään vain "27" eli IC27 junia.

![alt text](https://github.com/Konnila/harkkis/blob/main/create_table_stop.PNG "Create train stop")
Oleellisin kenttä on tietysti station_short_code joka ymmärrykseni mukaan on uniikki kenttä.

![alt text](https://github.com/Konnila/harkkis/blob/main/create_table_train_train_stop.PNG "Create connecting table")
Pieni huomautus tähän väliin että muutin myöhemmin scheduled_time kentän actual_time kentäksi koska oikeastaan emme ole kiinnostuneita arviosta, vaan varsinaisesta ajankohdasta.
Hieman myöhemmin muistin myös lisätä difference_in_time kentän joka antaa suoremman vastauksen kysymykseen "Paljonko juna on myöhässä". Tämän hoksattuani en oikeastaan edes tarvi juuri muuta.

## Kun tehtävän taulut on luotu

Päätän, että otan otoskooksi kymmenen matkaa. Ei mieletön määrä missään nimessä, mutta saadaan suhteellisen hyvä käsitys siitä kuinka aikataulussa tämä kyseinen IC27 juna on. Sitä paitsi jos haluaisin enemmän harjoittaisin hieman datahaun automaatiota, mikä ei aikarajoituksen takia liene viisasta juuri nyt. Painotan päivävalinnoissani edellisiä keskiviikkoja, koska tavoitepäiväkin on keskiviikko.
Nyt pääsen tietysti lisäämään dataa. 

Lisään ensin junia.
![alt text](https://github.com/Konnila/harkkis/blob/main/insert_train.PNG "Insert trains")

**HUOM:** Jälkikäteen lisäsin train-tauluun myös tiedon minkä päivän juna on kyseessä. Tajusin että tein huolimattomuusvirheen, tässähän voi olla nyt vain yksi juna per kaikki päivät.Siksi lisäsin train-tauluun siis myös kolumnin "departure_date" joka on date-tyyppiä. Nyt yksi rivi tässä taulussa tarkoittaa päivänä X tietyn junan lähtöä.

Seuraavaksi lisään pysäkkejä. Oikeasti en tässä tehtävässä ole kiinnostunut lopulta kuin "TPE" pysäkistä, mutta lisäsin alussa varmuuden varalta kaikki pysäkit joissa pysähdytään matkan varrella.

![alt text](https://github.com/Konnila/harkkis/blob/main/insert_stops.PNG "Insert stops")

Ja lopuksi lisätään tehtävän kannalta mielenkiintoisimmat pysäkit tampereella train_train_stop -tauluun.
Alla esimerkki-insert. Näitä tuli yhteensä kymmenen, eli kymmenen saapumishetkeä Tampereelle. 

![alt text](https://github.com/Konnila/harkkis/blob/main/insert_train_train_stops.PNG "Insert stops")

# Tietokantarakenne ja sisältö

Havainnollistan vielä tietokantakaavion sekä sisällön tässä vaiheessa. Osassa kuvia oli vielä jotain puutteita siinä vaiheessa kun ne tein, joten tässä lopullinen versio tietokannasta.

![alt text](https://github.com/Konnila/harkkis/blob/main/diagram_db.PNG "Db Diagram")

Ja sisällöt alla:

![alt text](https://github.com/Konnila/harkkis/blob/main/analysis_table_train.PNG "")
![alt text](https://github.com/Konnila/harkkis/blob/main/analysis_table_train_stop.PNG "")
![alt text](https://github.com/Konnila/harkkis/blob/main/analysis_train_train_stop.PNG "")

# Analyysi

Nyt voin tehdä yksinkertaista analyysiä. Päätän tutkia kymmenen matkan keskimääräistä myöhästymistä minuuteissa (negatiivinen on etuajassa, positiivinen myöhässä). Sekä otokseni isoimman myöhästymisen että suurimman etuajan (jos sellaisia on).

Tutkin asiaa seuraavalla kyselyllä

![alt text](https://github.com/Konnila/harkkis/blob/main/SQL_analysis3.PNG "")

Tällä näkisimme kaikkien tietokannan junien keskimääräinen myöhästyneisyys, isoimman myöhästymisen että suurimman etuajan. Meillä on kannassa kuitenkin vain IC27 joten näemme vain sen tiedot kuvan vasemmassa alalaidassa. round-kenttä (*olisin voinut nimetä palautetun taulun kolumnit paremmin AS statementillä*) tässä tapauksessa kertoo meille että juna on keskimäärin myöhässä hieman yli puoli minuuttia. Otoksessani suurin myöhästyminen oli viisi minuuttia ja juna oli parhaimmillaan etuajassa kolme minuuttia.

VR:n sivujen mukaan arvioitu saapumisaika kyseisellä junalla kyseisenä päivänä on 15:58. Keskimäärin juna on myöhässä alle minuutin, joten on hyvin todennäköistä että Jaana ehtii iltamenoonsa. Olisi mielestäni myös perusteltua ottaa aikaisempi juna, sillä otoksessa huomataan että juna on kerran ollut viisi minuuttia myöhässä. Itseasiassa kahdella matkalla kymmenestä (otokseni) Jaana ei olisi ehtinyt ajoissa. Suosittelisin tämän takia Jaanan ottavan aikaisemman junan, jos ajoissa oleminen on erityisen oleellista :).
Huomautuksena kuitenkin että otoskoko on hyvin pieni.

# Post-Analyysi

Lisäsin tämän kohdan kun jäin hieman miettimään ongelmaa lopuksi uudestaan. Oikeastaan tehtävässä ei pitäisi olla kiinnostunut ollenkaan tapauksista joissa ollaan etuajassa,
vaan kiinnittää huomio pelkästään tilanteisiin joissa ollaan myöhässä. Tällöin myöhästymisistä saataisiin keskihajonta, ja tieto siitä yhdistettynä myöhästymistodennäköisyyteen saisimme älykkäämmän analyysin siitä mitä Jaanan kannattaisi tehdä :)

# Junarajapinta

Osoitteesta https://still-lowlands-49832.herokuapp.com/api/DATE/TRAIN_NUMBER voi käydä kysymässä junaa samalla tavalla kuin digitrafficista. Piti tehdä herokuun tili, ja katsoa miten clojurea deployataan herokuun joten en ehtinyt saada rajapintaa täysin vastaamaan tehtävänantoa, joten nyt tämä replikoi toiminnallisuutta joka löytyy digitrafficista. Palvelimeltani HTTP clientti käy siis hakemassa noita date ja trainnumber parametreja vastaan samat tiedot kuin digitraffic sivustolta. Ensi keskiviikko olisi täten https://still-lowlands-49832.herokuapp.com/api/2020-11-11/27


