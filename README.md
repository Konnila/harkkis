# Harjoitustehtävän edistymisen kuvaus

## Alku

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

Päätän, että otan otoskooksi kymmenen matkaa. Ei mieletön määrä missään nimessä, mutta saadaan suhteellisen hyvä käsitys siitä kuinka aikataulussa tämä kyseinen IC27 juna on. Sitä paitsi jos haluaisin enemmän harjoittaisin hieman datahaun automaatiota, mikä ei aikarajoituksen takia liene viisasta juuri nyt.
Nyt pääsen tietysti lisäämään dataa. 

Lisään ensin junia.
![alt text](https://github.com/Konnila/harkkis/blob/main/insert_train.PNG "Insert trains")

**HUOM:** Jälkikäteen lisäsin train-tauluun myös tiedon minkä päivän juna on kyseessä. Tajusin että tein huolimattomuusvirheen, tässähän voi olla nyt vain yksi juna per kaikki päivät.Siksi lisäsin train-tauluun siis myös kolumnin "departure_date" joka on date-tyyppiä. Nyt yksi rivi tässä taulussa tarkoittaa päivänä X tietyn junan lähtöä.

Seuraavaksi lisään pysäkkejä. Oikeasti en tässä tehtävässä ole kiinnostunut lopulta kuin "TPE" pysäkistä, mutta lisäsin alussa varmuuden varalta kaikki pysäkit joissa pysähdytään matkan varrella.

![alt text](https://github.com/Konnila/harkkis/blob/main/insert_stops.PNG "Insert stops")



