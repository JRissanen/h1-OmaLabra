# h1-OmaLabra
This is a repository for my own pen-testing environment for the Penetration testing course 2023.

Olen tehnyt kaikki harjoitukset omalla tietokoneellani. </br>
Koneen infot:
Edition: Windows 10 Pro
Version: 22H2
OS build: 19045.2728
Processor: Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz   3.19 GHz
Installed RAM: 16,0 GB
System type: 64-bit operating system, x64-based processor

Virtuaalikoneiden infot kerrotaan artikkelissa ja virtuaalikoneet ovat Oracle VM VirtualBoxissa, jonka versio on: Version 6.1.40.

__a) Lataa Kali Linux__ ja __b) Lataa Metasploitable 2__

Aloitin lataamalla Kali Linuxin 64 bittisen virtuaalikoneen valmiin imagen. [Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines) </br>
Sen jälkeen latasin metasploitable 2:sta myös virtuaalikone imagen. [Metasploitable 2](https://sourceforge.net/projects/metasploitable/) </br>
Lisäsin molemmat koneet VirtualBoxiin: Oracle VM VirtualBox Manager -> File -> Import Appliance -> Browse -> Open.

__c) Koneiden virtuaaliverkko__

Koneille piti luoda Host-Only verkko, jossa Kali Linux saa yhteyden Internettiin sekä Metasploitable koneeseen. Kalin Internet-yhteys täytyy myös olla katkaistavissa. </br>
Aloitin menemällä VirtualBoxin Managerissa: File -> Host Network Manager. </br>
Seuraavaksi lisäsin uuden VirtualBox Host-Only Ethernet Adapterin kohdasta: 'Create' ja määritin DHCP Serverin päälle, jotta verkko osaa itse hakea oikeat osoitetiedot. </br>
Host-Only Ethernet Adapter mahdollistaa sen, että Kali-kone ja Metasploitable-kone ovat yhteydessä toisiinsa, mutta kumpikaan ei ole yhteydessä verkkoon, joten portti skannaukset yms tunkeutuminen on turvallista ja laillista. </br>

![Screenshot 2023-04-02 115658](https://user-images.githubusercontent.com/116954333/229343980-d7f5484c-9f43-4b4c-b2b6-8a1dc169ddb7.png)

Jätin Kaliin myös toisen Network Adapterin, jonka laitoin NAT:in taakse, jotta se on irroitettavissa kun Internet-yhteys halutaan katkaista. </br>
Lopullinen kofiguraatio on siis: </br>
Metasploitable 2: Settings -> Network -> Adapter 1 = Host-Only Adapter. Mitään muuta adapteria ei tarvita Metasploitablelle. </br>
Kali Linux: Settings -> Network -> Adapter 1 = NAT ja Adapter 2 = Host-Only Adapter. 

![Screenshot 2023-04-02 110629](https://user-images.githubusercontent.com/116954333/229343997-d1135a28-9e1a-4cea-b6f0-538578194af0.png)

Sitten testasin yhteyttä Kalin ja Metasploitablen välillä.  </br>
Ensin piti varmistaa, että kumpikaan kone ei saa Internet-yhteyttä, tein sen pingaamalla googlea.  </br>

![Screenshot 2023-04-02 110914](https://user-images.githubusercontent.com/116954333/229344327-a04f471c-7816-469f-aef2-14e3ccb472f0.png)

Kali sai aluksi yhteyden, koska en ollut irroittanut NAT:in takana olevaa adapteria, joten tein sen ja yhteys katkesi.  </br>
Adapterin irroittaminen kesken virtuaalikoneen päällä ollessa onnistuu hiiren oikealla napilla VirtualBoxin alapalkissa olevaa kahden päällekkäisen koneen kuvaketta painamalla. Sen jälkeen vain valitaan kumpi adapteri halutaan irroittaa ja minulla oli NAT adapterissa 1, joten irroitin sen.

![Screenshot 2023-04-02 110353](https://user-images.githubusercontent.com/116954333/229344302-fc1fd08d-4528-49cd-8737-1957821f7c00.png)

Nyt kumpikaan kone ei ole enää Internettiin yhteydessä, joten seuraavaksi testataan koneiden välisen yhteyden.  </br>
Se onnistui antamalla Metasploitable koneella ´ifconfig´ komennon ja sen jälkeen ´ping´ komennon Kalista Metasploitablen lokaaliin osoitteeseen. </br>

![Screenshot 2023-04-02 123314](https://user-images.githubusercontent.com/116954333/229344672-a6f0f582-5e6e-484d-89f6-fcd617ec0106.png)

Yhteys kunnossa.

__d) Etsi Metasploitable porttiskannaamalla (db_nmap -sn). Tarkista selaimella, että löysit oikean IP:n - Metasploitablen etusivulla lukee Metasploitable.__

Aloitin antamalla komennon ´msfdb run´, jotta sain metasploit ohjelman käyntiin. </br>

![Screenshot 2023-04-02 124149](https://user-images.githubusercontent.com/116954333/229345145-803bf742-d8dc-4dfe-b135-2fd1772a3ca9.png)

Kun metasploi oli päällä, tein uuden workspacen, nimesin sen msploi2. </br>

![Screenshot 2023-04-02 124510](https://user-images.githubusercontent.com/116954333/229345279-39a124dc-53a4-4a00-8887-79da73011627.png)

Koska lokaali verkkoni on osoiteavaruudessa 192.168.56.X niin skannasin ´db_nmap -sn 192.168.56.1-254´, jotta sain tulokset kaikista 254:stä osoitteesta, jonka jälkeen tarkistin komennolla: ´hosts´, että mitkä osoitteet ovat käytössä.

![Screenshot 2023-04-02 125353](https://user-images.githubusercontent.com/116954333/229345656-b88741ad-0bd5-419e-baf6-2ce00b064436.png)

Sain tuloksen, että osoitteet 192.168.56.1 ja 192.168.56.100-102 ovat käytössä ja niihin kuuluu myös aiemmin pingattu metasploitable koneen lokaali osoite. </br>
Kirjoitin lokaalin osoitteen selaimeen ja se oli Metasploitable 2:n verkkosivu.

![Screenshot 2023-04-02 130955](https://user-images.githubusercontent.com/116954333/229346709-ec216f29-c6ce-4986-8ffd-e005d4911f51.png)

__e) Porttiskannaa Metasploitable huolellisesti (db_nmap -A)__







