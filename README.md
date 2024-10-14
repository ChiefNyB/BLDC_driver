# BLDC driver
 [TI DRV10983-Q1](https://www.ti.com/product/DRV10983-Q1/) based BLDC motor controller

 Development software: KiCad 8.0.5 (latest)

 ## *Üzenet*

 Szerintem majd ide a README-be írogathatjuk dokumnetáció vázlatos részét,
 a számítást meg Excelbe vagy valami python scriptbelehet nyomni
 (ugyanígy version controllozva), aztán innen aztán mehet majd Word-be a bővebb leírás.

 Éés szerintem nyugodtan írogathatunk ide kommenteket is, talán kevésbé veszik
 el mint a chatben.

 Szerintem egyébként pedig valahogy így tudnánk elindulni:

 ## Következő lépések

  - IC alapján kapcsolási rajz elkezdése
  - Komponensek értékeinek kiszámítása...
  - További cuccok (táp, csatlakozók...) illesztése az áramkörbe
  - Fizikai layout
  - ...

## Dokumentáció vázlatosan
(ömlesztett gondolatok, amit esetleg már leírtál az törölhető)
  
  - modell (footprint...) letöltése & hozzáadása a
  TI-os lapról: [itt](https://vendor.ultralibrarian.com/TI/embedded/?gpn=DRV10983-Q1&package=PWP&pin=24&sid=&c=0)
  (lementettem saját global libraryba
  ezt lehet nektek is meg kell csinálni, hogy látszódjon)
  - IC bekötése a datasheet 64. oldalán található 71.figureban
  található kapcsolás alapján (lehet arról írni esetleg, hogy milyen funkciói vannak
  az IC-nek)
  - a schematic hierarhikus, több lapból áll amik lehet még nincsenek mind készen (drive, power) (nem is biztos hogy szükség lenne rá, mert elég egyszerű, de segíti a logikát átlátni)
  - a pontos típus: [DRV10983SQ](https://www.ti.com/product/DRV10983-Q1/part-details/DRV10983SQPWPRQ1) legyen, hogy standby modeban is 
  használható legyen az 5V-os buck converter --> mikrovezérlő meghajtható róla (ld. katalógus 3.o.)


