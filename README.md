# BLDC driver
 [TI DRV10983-Q1](https://www.ti.com/product/DRV10983-Q1/) based BLDC motor controller

 Development software: KiCad 8.0.5 (latest)

 ## *Üzenet*

 Szerintem majd ide a README-be írogathatjuk dokumnetáció vázlatos részét,
 a számítást meg Excelbe vagy valami python scriptbe lehet nyomni
 (ugyanígy version controllozva), aztán innen aztán mehet majd Word-be a bővebb leírás.

 Éés szerintem nyugodtan írogathatunk ide kommenteket is, talán kevésbé veszik
 el mint a chatben.

 Légyszi nézzétek meg a katalógust is (bent van a datasheet mappában), meg az áramkört is,
 igyekeztem leírni a dolgokat amiket csináltam. Mindezek alapján remélem, hogy nektek is érthető lesz majd, hogy mi mit csinál.
 Ha bármi kérdésetek van, írjátok akár ide valahova, akár messengeren. Légyszi mindketten olvassátok el az egész README-t!
 Köszi :)

 Ide kigyűjtöttem, hogy szerintem még mi kell:

 ## További lépések (nagy vonalakban)

  - Komponensek értékeinek kiszámítása --> komponensek választása
  - Fizikai layout kialakítása
  - (adhatnánk majd a hozzá passzoló motorokra egy példát)

## Dokumentáció vázlatosan (kapcsolás)
(ömlesztett gondolatok, egy csöppet össze-vissza)

KORREKCIÓ: A TVS DIÓDÁT EGYENLŐRE KISZEDTEM, VAN BEÉPÍTETT A CHIPBE
  
  - a chip modellt (footprint, stb...) a
  TI-os lapról töltöttem és illesztettem be: [link](https://vendor.ultralibrarian.com/TI/embedded/?gpn=DRV10983-Q1&package=PWP&pin=24&sid=&c=0)
  (lementettem saját global libraryba... ha nektek valami miatt nem látszódna, akkor
  ezt lehet nektek is meg kell csinálni)
  - IC bekötése a datasheet 64. oldalán található 71.figureban, egy csomó komponens (főleg kondik) értéke innen van
  található kapcsolás alapján (lehet arról írni esetleg, hogy milyen funkciói vannak
  az IC-nek)
    - 12Vról megy, (esetleg 24V, de ha 28-ig visszatölt, akkor már egyébként lekapcsolja a powerstaget)
    - I2C max 400kHz
    - milyen lekapcsolási-biztonsági funkciók vannak (Over voltage, o.current, thermal shutdown...)
  - a schematic hierarhikus, több lapból áll (drive, power), de nem is biztos hogy szükség lenne rá, mert elég egyszerű,
  csak kicsit fancybb és esetleg segíti a logikát átlátni
  - a pontos típus: [DRV10983SQ](https://www.ti.com/product/DRV10983-Q1/part-details/DRV10983SQPWPRQ1) legyen, ha standby modeban is 
  használni szeretnénk az 5V-os buck convertert --> mikrovezérlő meghajtható róla (ld. katalógus 3.o.)
  - I2C védelmet és szűrést [ez](https://www.we-online.com/components/media/o734709v410%20ANP121a%20%20Filter%20and%20surge%20protection%20for%20I2C%20Bus%20EN.pdf)
  alapján csináltam --> itt javasolt komponensekkel (ferrit gyöngy és TVS dióda) megegyező paraméterűeket lesz majd érdemes választani
  (egyenlőre egy kb hasonlót raktam be)
  - terveim szerint csinálok 2 verziót:
    - csatlakozós (Táp & motor is csatlakozós)
    - beforrasztós (táp& motornak csak pad-ek vannak amikre majd rá lehet forrasztani a vezetékeket)
    (ez az ahol a testpoint (TP) nevű kivezetések vannak)
  - a TI IC alatt a túloldalon van egy PAD (majd thermal viákkal összekötve) a GND-hez kötve, hogy hőt disszipáljon
  és ide lehet(ne) rakni majd a hűtőbordát is
  - van egy sima GND és egy PWRGND, hogy a motor áramai minél kevésbé zavarjanak bele a digitális jelekbe, ezek össze vannak kötve egy 0ohmos ellenállással
  - az 5V (output) jelenlétét LED jelzi
  - decoupling capacitorok közel az IC-hez (majd a kialakításnál fizikailag)
  - minden jel-bemenet és kimenet, ami ki van vezetve (SCL, SDA, FG, SPEED, DIR) 3V3 TVS diódával védve ESD ellen
  - I2C csak 3V3 szinten mehet
  - FG pin 3V3-as impulzusokat ad (to be checked in datasheet)
  - DIR és SPEED bemeneten használható 5V-os jel (azaz megtáplálható egy sima potiról és kapcsolóról, ennek az egy panelnek a segítségével)
    - viszont ehhez előbb I2C-n be kell konfigurálni
    - a bemenetek egy-egy feszültségosztóval le vannak skálázva 3v3-ra
    - ehhez majd lesznek számítások, poti értéke
  - a legvégén esetleg kérhetünk majd árajánlatot ha unatkozunk :P

  - az I2C-t úgy lehet használni, ha a külső áramkör GND-je össze van kötve a motordriver GND_D-jével
  - az 5V-os bemenet használatához is legalább a GND-t kell bekötni


  ## Szükséges számítások, paraméterek
  Légyszi Kristóf ide, vagy valami fileba majd gyűjtsd ki a komponens nevét/számát (pl.: R1) és a linket hogy melyiket választottuk...
  Majd belepakkolom a schematicba és generálok belőle egy BOM .csv filet.
  ( Lehetséges oldalak: farnell, rs-online, lomex, TME ...ilyesmik. Csak legyen magyar oldal! )
  (Esetleg lehet egy specifikus motor amit kinézünk, és annak a pramaétereit is felhasználjuk a számításokhoz)

  - elsősorban a TI-os katalógus alapján, a typical application résznél van egy lista hogy ők mit javasolnak
  - csak SMD alkatrészeket please
  - alapból ilyen 0603-as (mármint inches 0603, mert a metrikus méretek el vannak tolva) méretű ellenállásokat és kondikat stb. keresünk,
  esetleg kicsit kisebb vagy nagyobb (de nyilván ha nagyobb kell, akkor nagyobb kell, pl valszeg a táp kondija az gyanús, hogy nagyobb)
    - ellenállás választásnál ki kell számolni a teljesítményét és aszerint kell választani (0.25W; 1W ...)
    - LED-nél választani kell egy LED-et, majd az ahhoz előírt mA-hez választunk egy ellenállást, a színét rád bízom Kristóf ;)
    - kondiknál a feszültséget és a kapacitást kell nézni alapból
    - SPEED és DIR inputokra olyan feszültségosztó kell (ellenállásértékek), amik leskálázzák a bejövő 5V-ot 3,3-ra
  - I2C védelem: ld. dokumentáció vázlatpontjai közt, illetve ezek alapján még újra számíthatók a felhúzó ellenállások értékei is
  - ha nagyon fancy-k akarunk lenni, egy thermal calculationt is nyomhatunk és esetleg egy hűtőbordát is válasszunk (1. gyakorlat -> pdf kb. 10. oldal)
  - TVS diódák kapcsolása nem látszik a kapcs rajzon de az assets mappában megtalálható

  ## Kiválasztott alkatrészek
  Feltöltöttem egy Excel táblában a katalógusokból kiválasztott áramköri elemeket. A dokumentációhoz ideírom, amit csináltam:

  - Katalógusból kiválasztottam az ajánlások miatt előre ismert áramköri elemeket + a LED-et
  - Három kis számolás volt:
    - LED ellenállás,
    - feszültségosztók (TI online számolójával: [link](https://www.ti.com/download/kbase/volt/volt_div3.htm)),
    - I2C felhúzó ellenállások min-max értékeinek újraszámolása (az ajánlottak jók maradtak).

  Minden számolás és az összes alkatrész az Excelben. Alkatrészekhez van típuszsám, link, footprint adat és méretezési feltétel is (ha volt).
  A termikus számítások nem voltak kivitelezhetők, nincs elég adat veszteségekre vagy case-sink hőellenállásra (vagy hogy egyáltalán becsüljük).
  Elvileg a driver alá kerülő thermal pad elegendő, bele lehet írni a dokumentációba, hogy igény szerint lehet még adni hozzá.

  ## PCB alias NYÁK design
  - csatolásmentesítő kondik közel az IC-hez, mindegyiknek saját viája van a GND-hez
  - 45°-os track törésszögek a jelreflexió elkerülésére
  - thermal via-k: 20mil hole diameter, 8mil hole (for least resistance) ahogy [itt](https://www.ti.com/lit/an/slva959b/slva959b.pdf) a 2.5-ben írják
  - a teljesítményfolyamban lévő track-ek és viák méretezve vannak (viából van egy dupla is, bár nem feltétlen lenne rá szükség) 
  a számításokról készített screenshot az assetsben
  - digitális <--> teljesítény oldalak elválasztása fizikailag amennyire lehet
  - teljesítményvonalak kialakításánál fontos volt, hogy minél kisebb loopot csináljanak és rövidek legyenek
  - GND és GNDPWR egy 0ohmos ellenállással (R4) összekötve: kis vezetékellenállású összeköttetés a bemeneti GND és a digitális jelek
  GND-je közt
  - buck konverter trackjei default értéken vannak, de van róla számítás hogy elég (assetsben)
  - a power bemenetek jelölve vannak, a csatlakozónál légyszi csinálj egy ábrát, ami "katalógusba" mehetne
  - két sarokban letörések...mert csak... :P 
  - M2.5-ös mounting holeok hogy lehessen odacsavarozni valahova
  - az alsó oldalra ragasztható heatsink ha akarja a felhasználó, de figyelni kell,
  hogy ezen a részen galvanikus kapcsolat ne alakuljon ki a külvilággal
  - földkiöntés a sima GND-vel
  (- esetleg át lehetne alakítani hogy a heatsink-pad a sima GND-hez kapcsolódjon és a PWRGND-t másmerre vezetni, ha van idő...)
  - ... és ezeken kívül bármi amit látsz a boardon és érdemes leírni


