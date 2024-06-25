# OAuth autorizacijski sustav
### Načela rada primjena u mrežnoj sigurnosti i izazovi

**Ivan Močilac, Ivana Mikulić, Amar Ademi, David Škrčinčki, Marko Galavić**

## Sadržaj
1. [Uvod](#uvod)
2. [Ključni koncepti OAuth 1.0](#ključni-koncepti-oauth-10)
3. [Ključni koncepti OAuth 2.0](#ključni-koncepti-oauth-20)
4. [OAuth 1.0](#oauth-10)
5. [Primjer](#primjer)
6. [Kriptografske komponente u OAuthu 1.0](#kriptografske-komponente-u-oauthu-10)
7. [Prednosti i nedostaci OAuth 1.0](#prednosti-i-nedostaci-oauth-10)
8. [OAuth 2.0](#oauth-20)
9. [Tokeni](#tokeni)
10. [Prednosti i nedostaci OAuth 2.0](#prednosti-i-nedostaci-oauth-20)
11. [Kriptografska komponenta OAuth 2.0](#kriptografska-komponenta-oauth-20)
12. [Sigurnost tokena](#sigurnost-tokena)
13. [Kriptografski aspekti toka OAuth 2.0 (OAuth 2.0 Flow)](#kriptografski-aspekti-toka-oauth-20-oauth-20-flow)
14. [Introspekcija i verifikacija tokena](#introspekcija-i-verifikacija-tokena)
15. [Upravljanje kriptografskim ključevima i tajnama](#upravljanje-kriptografskim-kljucevima-i-tajnama)
16. [Najbolje sigurnosne prakse](#najbolje-sigurnosne-prakse)

## Uvod
Za početak potrebno nam je kratko objašnjenje rada autentifikacijskih protokola OAuth 1.0 i OAuth 2.0. OAuth je otvoreni standardni protokol koji se koristi za autorizaciju. Aplikacijama trećih strana omogućuje ograničeni pristup resursima korisnika bez izlaganja njihovih vjerodajnica. Ovo je osobito korisno za odobravanje pristupa web resursima kao što je pristup privatnim podacima pohranjenim na drugom mjestu ili korištenje API-ja.

OAuth protokol relativno je novi protokol te se njegovo korištenje sve više povećava. U tradicionalnoj klijent-server arhitekturi korisnik koristi svoje korisničko ime i lozinku za pristup resursima koji se nalaze na serveru. Razlog zbog kojeg je potrebna nova vrsta protokola je ta što razvojem web i Cloud servisa pojavila se potreba za pristupom treće strane prema korisničkim resursima. OAuth 1.0 i OAuth 2.0 omogućuju siguran pristup više aplikacija prema korisničkim resursima.

## Ključni koncepti OAuth 1.0
1. **Uloge:**
    - Korisnici - Osoba koja posjeduje podatke i može odobriti pristup njima.
    - Klijent (potrošač) - Aplikacija koja traži pristup podacima korisnika.
    - Pružatelj usluga - Poslužitelj koji ugošćuje korisničke podatke i sposoban je prihvatiti i odgovoriti na zahtjeve zaštićenih resursa pomoću pristupnih tokena.
2. **Tokeni:**
    - Token zahtjeva - Privremena vjerodajnica koju koristi klijent za dobivanje autorizacije od vlasnika resursa.
    - Pristupni token - Token koji koristi klijent za pristup podacima vlasnika resursa. Dobiva se nakon što korisnik autorizira token zahtjeva.
3. **Autorizacija u tri koraka:**
    - Dobivanje tokena zahtjeva: Klijent traži token zahtjeva od pružatelja usluge. Zahtjev se potpisuje korištenjem vjerodajnica klijenta.
    - Autorizacija tokena zahtjeva: Korisnik autorizira token zahtjeva obično putem korisničkog sučelja koje pruža davatelj usluge.
    - Razmjena tokena zahtjeva za token pristupa: Nakon što korisnik autorizira token zahtjeva klijent ga mijenja za token pristupa. Ovaj se token zatim može koristiti za izradu provjerenih API zahtjeva u ime korisnika.
4. **Potpisi:**
    - OAuth 1.0 koristi potpise kako bi potvrdio da zahtjev nije neovlašteno mijenjan i kako bi osigurao da zahtjev dolazi iz ovlaštenog izvora. HMAC-SHA1, RSA-SHA1 i PLAINTEXT uobičajene su metode potpisa koje se koriste.
5. **Komunikacija:**
    - OAuth 1.0 zahtijeva više koraka preusmjeravanja između klijenta i pružatelja usluge. Komunikacija obično uključuje preusmjeravanje korisničkog preglednika između davatelja usluge i klijentske aplikacije.

## Ključni koncepti OAuth 2.0
1. **Uloge:**
    - Korisnici - Korisnik koji autorizira aplikaciju za pristup njihovim resursima.
    - Klijent - Aplikacija koja zahtijeva pristup podacima vlasnika resursa.
    - Autorizacijski server - Poslužitelj izdaje pristupne tokene klijentu nakon uspješne provjere autentičnosti vlasnika resursa i dobivanja autorizacije.
    - Poslužitelj resursa (Resource Server) - Poslužitelj na kojem se nalaze zaštićeni resursi sposoban prihvatiti zahtjeve i odgovoriti na njih koristeći pristupne tokene.
2. **Tokeni:**
    - Tokeni pristupa - Token koji klijent koristi za upućivanje provjerenih zahtjeva poslužitelju resursa.
    - Token za osvježivanje (Refresh Token) - Token koji se koristi za dobivanje novog pristupnog tokena bez potrebe da vlasnik resursa ponovno autorizira klijenta.
3. **Vrste odobrenja - OAuth 2.0 definira nekoliko vrsta odobrenja autorizacije za različite slučajeve upotrebe:**
    - Dobivanje autorizacijskog koda - Koristi se u aplikacijama na strani poslužitelja. Klijent dobiva autorizacijski kod koji se zatim mijenja za pristupni token.
    - Implicitna dodjela - Koristi se u aplikacijama na strani klijenta (npr. aplikacije s jednom stranicom). Pristupni token vraća se izravno bez posrednog autorizacijskog koda.
    - Dodjeljivanje vjerodajnice za lozinku vlasnika resursa - Koristi se kada klijent ima odnos povjerenja s vlasnikom resursa kao što su aplikacije prve strane. Klijent prikuplja korisničko ime i lozinku vlasnika resursa kako bi dobio pristupni token.
    - Dodjela vjerodajnica klijenta - Koristi se za komunikaciju machine-to-machine. Klijent koristi vlastite vjerodajnice za dobivanje pristupnog tokena.
    - Dodjela tokena osvježavanja - Koristi se za dobivanje novog pristupnog tokena pomoću tokena za osvježavanje.
4. **Krajnje točke:**
    - Krajnja točka autorizacije - Gdje vlasnik resursa autentificira i odobrava autorizaciju klijentu.
    - Krajnja točka tokena - Gdje klijent razmjenjuje odobrenje autorizacije za token pristupa i token za osvježavanje.
    - Krajnja točka preusmjeravanja - Klijentska krajnja točka na koju autorizacijski poslužitelj šalje korisnika nakon odobravanja ili odbijanja autorizacije.
5. **Opseg:**
    - Opsezi definiraju specifične dozvole koje klijent zahtijeva. Ograničavaju pristup dodijeljen pristupnim tokenom. Na primjer, opseg može navesti pristup samo za čitanje korisničkom profilu.

## OAuth 1.0
Razvoj OAuth protokola započela je grupa web developera za Internet servise koji su željeli riješiti uobičajeni problem delegiranja i pristupanja zaštićenim resursima. Prva stabilna verzija OAuth protokola pojavila se 2007. godine, a 2009. godine bila je revizija te se koristi verzija OAuth 1.0a. OAuth je u tradicionalnu klijent-server arhitekturu dodao novu ulogu: vlasnik resursa, klijent i poslužitelj resursa. Korištenjem tokena za pristup resursima postignut je sigurniji način autentifikacije i autorizacije.

## Primjer
OAuth 1.0 proces može se ilustrirati pomoću jednostavnog primjera:

1. **Klijent** (aplikacija za praćenje fitnessa) želi pristupiti **resursima** (podaci o fitnessu) koje **vlasnik resursa** (korisnik) ima na **poslužitelju resursa** (servis za pohranu podataka o fitnessu).
2. **Klijent** traži od **pružatelja usluge** (servis za pohranu podataka o fitnessu) **token zahtjeva**. Zahtjev uključuje identifikator klijenta i digitalni potpis za provjeru autentičnosti.
3. **Pružatelj usluge** odobrava **token zahtjeva** i vraća ga **klijentu**.
4. **Klijent** preusmjerava **vlasnika resursa** na **pružatelja usluge** gdje se od vlasnika resursa traži da odobri **token zahtjeva**.
5. Nakon što **vlasnik resursa** odobri **token zahtjeva**, **pružatelj usluge** preusmjerava **vlasnika resursa** natrag na **klijenta** s ovjerenim **tokenom zahtjeva**.
6. **Klijent** sada može razmijeniti ovjereni **token zahtjeva** za **pristupni token**.
7. **Pristupni token** omogućuje **klijentu** pristup podacima **vlasnika resursa** na **poslužitelju resursa**.

## Kriptografske komponente u OAuthu 1.0
OAuth 1.0 koristi kriptografske potpise za provjeru autentičnosti i zaštitu integriteta poruka. Najčešće korištene metode potpisa su:

- **HMAC-SHA1**: Koristi se za stvaranje koda za provjeru autentičnosti poruke pomoću tajnog ključa.
- **RSA-SHA1**: Koristi asimetrične ključeve za potpisivanje zahtjeva.
- **PLAINTEXT**: Nema kriptografske zaštite, koristi se za jednostavne zahtjeve kada nije potrebna sigurnost.

## Prednosti i nedostaci OAuth 1.0
### Prednosti:
- **Sigurnost**: Upotreba tokena umjesto lozinki smanjuje rizik od krađe identiteta.
- **Delegacija**: Omogućuje vlasnicima resursa da daju ograničeni pristup svojim podacima bez dijeljenja lozinki.
- **Standardizacija**: Omogućuje interoperabilnost između različitih aplikacija i servisa.

### Nedostaci:
- **Kompleksnost**: Implementacija OAuth 1.0 može biti složena zbog korištenja kriptografskih potpisa i višestrukih koraka preusmjeravanja.
- **Potpisivanje zahtjeva**: Zahtijeva dodatnu računalnu snagu za generiranje i provjeru kriptografskih potpisa.
- **Ograničena podrška za mobilne uređaje**: OAuth 1.0 nije dizajniran s obzirom na mobilne uređaje, što može otežati integraciju u mobilne aplikacije.

## OAuth 2.0
OAuth 2.0 razvijen je kao nasljednik OAuth 1.0 s ciljem pojednostavljenja protokola i povećanja sigurnosti. Predstavljen je 2012. godine i brzo je postao široko prihvaćen standard za autorizaciju.

## Tokeni
OAuth 2.0 koristi različite vrste tokena za autorizaciju i autentifikaciju:

- **Pristupni tokeni**: Koriste se za pristup zaštićenim resursima.
- **Tokeni za osvježivanje**: Koriste se za dobivanje novih pristupnih tokena bez potrebe za ponovnom autentifikacijom vlasnika resursa.

## Prednosti i nedostaci OAuth 2.0
### Prednosti:
- **Jednostavnost**: OAuth 2.0 je jednostavniji za implementaciju u usporedbi s OAuth 1.0.
- **Podrška za mobilne uređaje**: Dizajniran je s obzirom na mobilne uređaje, što olakšava integraciju u mobilne aplikacije.
- **Fleksibilnost**: Podržava različite vrste autorizacijskih tokova za različite slučajeve upotrebe.

### Nedostaci:
- **Sigurnosni rizici**: OAuth 2.0 se oslanja na HTTPS za sigurnost, što može biti problematično ako se HTTPS ne koristi pravilno.
- **Kompatibilnost**: OAuth 2.0 nije unatrag kompatibilan s OAuth 1.0, što može zahtijevati značajne promjene u postojećim aplikacijama.

## Kriptografska komponenta OAuth 2.0
OAuth 2.0 koristi TLS (Transport Layer Security) za zaštitu komunikacije između klijenta i servera. TLS osigurava povjerljivost, integritet i autentičnost podataka koji se razmjenjuju tijekom autorizacijskog procesa.

## Sigurnost tokena
Sigurnost tokena od ključne je važnosti u OAuth 2.0. Tokeni bi trebali biti zaštićeni tijekom prijenosa i pohrane kako bi se spriječila njihova krađa ili neovlaštena uporaba. Najbolje prakse za zaštitu tokena uključuju korištenje sigurnih komunikacijskih kanala (npr. HTTPS), enkripciju tokena i pohranu tokena u sigurnim spremnicima.

## Kriptografski aspekti toka OAuth 2.0 (OAuth 2.0 Flow)
OAuth 2.0 tok uključuje nekoliko kriptografskih aspekata:

1. **Autorizacijski kod**: Kratkotrajni kod koji se razmjenjuje za pristupni token. Kod je obično zaštićen pomoću TLS-a.
2. **Pristupni token**: Token koji se koristi za pristup zaštićenim resursima. Tokeni bi trebali biti dovoljno jaki da se ne mogu lako pogoditi ili krivotvoriti.
3. **Token za osvježivanje**: Token koji se koristi za dobivanje novih pristupnih tokena. Ovaj token treba biti zaštićen na isti način kao i pristupni tokeni.

## Introspekcija i verifikacija tokena
Introspekcija tokena je proces provjere valjanosti tokena i dobivanja dodatnih informacija o njemu. To uključuje provjeru jesu li tokeni istekli, opozvani ili nevažeći. Verifikacija tokena može se provesti korištenjem kriptografskih potpisa ili upitima autorizacijskom serveru.

## Upravljanje kriptografskim ključevima i tajnama
Upravljanje kriptografskim ključevima i tajnama ključno je za sigurnost OAuth 2.0 protokola. Ključevi bi trebali biti rotirani redovito kako bi se smanjio rizik od kompromitacije. Tajne bi trebale biti pohranjene na siguran način i pristup njima bi trebao biti ograničen samo na ovlaštene entitete.

## Najbolje sigurnosne prakse
### Za implementaciju OAuth 2.0 slijede neke od najboljih sigurnosnih praksi:
- **Korištenje HTTPS-a** za sve komunikacije između klijenta i servera.
- **Snažni tokeni** koji su teško pogodivi ili krivotvorivi.
- **Redovita rotacija ključeva** i tokena kako bi se smanjio rizik od kompromitacije.
- **Sigurno pohranjivanje tajni** i ograničavanje pristupa njima samo ovlaštenim entitetima.
- **Revizija i praćenje** svih autorizacijskih i autentifikacijskih događaja kako bi se identificirali i odgovorili na moguće sigurnosne incidente.
