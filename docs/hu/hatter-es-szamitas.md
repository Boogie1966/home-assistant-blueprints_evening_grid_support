# Facebook poszt – esti akkumulátoros visszatáplálás (19:00–22:00)

Készült: 2026-08-03

---

## 1. A poszt (másolható változat)

🔋 **Kérés mindenkinek, akinek napelem + akkumulátor van otthon**

Nyáron a magyar villamosenergia-rendszer legnehezebb órái nem délben vannak, hanem **este 19 és 22 óra között**. A nap lement, a napelemek már nem termelnek, de otthon még megy a klíma, a főzés, a mosogatógép, az e-autó töltő. Ezt a lyukat drága gázos csúcserőművekkel és importtal kell betömni.

Ha nálad **reggelre rendszeresen marad töltés az akkuban**, akkor van egy nagyon egyszerű dolog, amivel segíthetsz: állítsd be az invertert úgy, hogy **19:00 és 22:00 között az akku vigye a házat, a felesleget pedig adja vissza a hálózatra**.

Nem kell mindent kiadni. Elég a kapacitás egy része – a többit nyugodtan tartsd meg magadnak.

**Mit jelent ez számokban?**
Egy 15 kWh-s akkunál a 40% az 6 kWh. Egy háztartásnál ez semmi.
De ha kb. 25 ezer akkus háztartásból kétharmad megcsinálja, az **esténként ~100 MWh, három órán át kb. 33 MW folyamatos teljesítmény** – nagyjából egy közepes gázturbinás erőmű.
Ha a támogatott tárolóprogramok után 40 ezer háztartásból csinálja meg kétharmad: **160 MWh esténként**.
Sok kicsi tényleg sokra megy. 💪

**Mielőtt beállítod, 3 dolgot nézz meg – ez fair, hogy tudd:**

1️⃣ **Szabad-e nálad visszatáplálni?** Sok rendszeren aktív a visszwatt-védelem (nulla betáplálás), és a támogatott rendszereknél is lehet kikötés. Ha nincs betáplálási engedélyed, ez a beállítás nem neked szól. Az akkut **csak napelemből** töltsd – hálózatról tölteni és visszaadni nem HMKE, hanem kereskedés, azt ne csináld.

2️⃣ **Pénzügyileg nem nyerő üzlet, tudni kell róla.** Bruttó elszámolásnál a betáplált kWh-ért ~4,4–5,3 Ft-ot kapsz, reggel viszont 36 Ft körül veszed vissza. 6 kWh-nál ez kb. napi 180–200 Ft. **Szaldós elszámolásnál viszont gyakorlatilag semleges**, mert amit este kiadsz, azt reggel „visszakapod" az elszámolásban. Szóval: szaldósoknak szinte ingyen van, bruttósoknak ez egy tudatosan vállalt, kb. egy kávé árú napi felajánlás.

3️⃣ **Hagyj tartalékot.** Ne menj 20–30% alá, főleg ha az akku szünetmentesként is dolgozik, vagy ha reggel te is használnád.

➕ **Egy technikai kérés:** ne pont 19:00-kor induljunk mind egyszerre. Válassz magadnak egy random időpontot 18:45 és 19:30 között, és 22:00 helyett is inkább fokozatosan állj le. Így nem lesz belőle egy nagy lépcső a rendszerben.

Ha valakinek kell segítség a beállításhoz (Victron, Solax, Deye, Huawei, Fronius, Home Assistant automatizálás), szóljatok kommentben, segítek. 🙂

#napelem #energiatároló #okosotthon #zöldenergia

---

### Rövid (mobilra optimalizált) változat

🔋 Akinek napelem + akku van otthon: nyáron **19 és 22 óra között** a legnehezebb a hálózatnak – nincs napelem, de megy a klíma és a főzés.

Ha reggelre marad töltésed, állítsd be, hogy **este 19–22 között az akku a hálózatot is segítse**. Nem kell mindent kiadni – egy 15 kWh-s akkunál 6 kWh is elég.

Egy háztartásnál ez semmi. 25 ezer akkus otthonból kétharmadnál viszont **~100 MWh esténként, ~33 MW teljesítmény** – egy kisebb erőmű.

⚠️ Előtte nézd meg: van-e betáplálási engedélyed, szaldós vagy bruttós vagy (bruttósnál napi ~200 Ft a ráfizetés), és hagyj 20–30% tartalékot.
👉 És ne pont 19:00-kor induljunk mind – válassz egy random időpontot 18:45 és 19:30 között.

Segítek a beállításban, ha kell. 🙂

---

## 2. Validálás – mi állja meg a helyét és mi nem

### ✅ Ami helyes az ötletben

| Állítás | Értékelés |
|---|---|
| Az esti 19–22 a kritikus időszak | **Igaz.** Nyáron a rendszerterhelés csúcsa naplemente után van, épp amikor a ~8 GW-nyi hazai napelemes kapacitás kiesik. A „nettó terhelés" (duck curve) meredek esti felfutása a legdrágább és legkarbonintenzívebb óra. |
| Sok kicsi sokra megy | **Igaz**, lásd a számítást lent – ez nagyságrendileg egy erőmű-blokk. |
| Nem kell mindent betáplálni | **Igaz és helyes üzenet.** A részleges felajánlás reálisabb és biztonságosabb. |
| Az elosztóhálózatnak nem árt | **Nagyrészt igaz.** Este a hálózat terhelt, tehát a betáplálás nem okoz feszültségemelkedést – ellentétben a déli csúccsal. Ez a legjobb időpont betáplálni. |

### ⚠️ Amit korrigálni / kiegészíteni kell

1. **Nem mindenki táplálhat vissza.** 2022 novembere és 2024 januárja között a hálózati betáplálás felfüggesztés alatt volt; azóta az ország nagy részén újra engedélyezett, de ahol a hálózat nem tudja fogadni, ott **visszwatt-védelmet (nulla betáplálás)** írnak elő. A poszt ezt nem hallgathatja el, különben sokan hálózati szerződést sértenének.

2. **Hálózatról töltött energiát visszaadni nem legális HMKE-keretben.** A HMKE definíció szerint megújuló forrásból termel; a tárolót csak napelemből szabad tölteni. Aki olcsó éjszakai árammal töltene és este eladná, az energiakereskedést végez engedély nélkül. Ezt külön ki kell mondani.

3. **A pénzügyi oldal aszimmetrikus:**
   - **Bruttó elszámolás:** betáplálási ár lakossági ügyfélnél elosztónként 4,39–5,25 Ft/kWh (E.ON/OPUS 4,39 Ft, ÉMÁSZ 4,94 Ft, ELMŰ 5,11 Ft, DÉMÁSZ 5,25 Ft), a visszavásárlás viszont 36 Ft/kWh-tól (rezsicsökkentett A1) felfelé. **Ez kb. 7-szeres veszteség.** 6 kWh/nap → napi ~186 Ft, havi ~5 600 Ft ráfizetés.
   - **Szaldó elszámolás:** az éves elszámolásban a betáplált és vételezett kWh kiegyenlíti egymást, tehát az esti kiadás–reggeli visszavétel **nagyjából semleges**. A szaldósok a poszt igazi célcsoportja.

4. **Akkumulátor-élettartam.** Egy 15 kWh-s LFP akku ~1,2–1,5 M Ft, ~6 000 ciklus → ~90 MWh átforgatás → **~15 Ft/kWh amortizáció**. Fontos nüansz: ha az energiát amúgy is felhasználtad volna reggel, akkor a ciklus úgyis megtörténik, tehát **a többletkopás nulla**. Csak akkor keletkezik plusz kopás, ha emiatt többször töltesz/sütsz ki.

5. **Szinkronizációs kockázat.** Ha mindenki pontban 19:00-kor indít és 22:00-kor leáll, az a rendszerirányítónak egy hirtelen lépcső (ramp), ami önmagában is szabályozási igényt teremt. **Ezért javasolt a szórt indítás.** Ez a poszt egyik legértékesebb szakmai eleme.

6. **Őszinte ellenérv: a saját fogyasztás fedezése majdnem ugyanannyit ér.** Ha az akku este egyszerűen csak a saját házad fogyasztását viszi, azzal máris ugyanannyi terhelést vettél le a rendszerről, mintha betáplálnál – és nem fizetsz rá. A hálózatba adás csak azon felül ad többletet, ami a saját esti fogyasztásod felett marad. Ezt érdemes tudni: **az igazi üzenet inkább „este ne a hálózatból élj, hanem az akkuból"**, a betáplálás csak a hab a tortán.

7. **A „profi" megoldás létezik:** ez lényegében egy kézi virtuális erőmű (VPP). Aggregátoron keresztül a tárolók a kiegyenlítő szabályozási piacon sokkal magasabb díjat kapnak, mint 5 Ft/kWh. Hosszabb távon érdemes efelé terelni az érdeklődőket.

---

## 3. Pro és kontra

### Mellette

- **Rendszerszintű haszon a legdrágább órában** – kiváltott gázturbina, kevesebb import, alacsonyabb HUPX esti ár, kevesebb CO₂.
- **Hálózatbarát időpont** – este a terhelés magas, a betáplálás feszültségszempontból kedvező, ellentétben a déli órákkal.
- **Nulla beruházás** – meglévő eszközön csak egy időzítés-beállítás.
- **Szaldós ügyfeleknek gyakorlatilag ingyen** van.
- **Közösségi/PR-érték** – látható, mérhető, megosztható közös cselekvés.
- **Ha az akku amúgy is tele marad reggelre, a tárolt energia kihasználatlan** – ez a valódi veszteség, amit a poszt megszüntet.

### Ellene

- **Bruttó elszámolásnál egyértelmű anyagi ráfizetés** (~7-szeres ár-különbözet).
- **Jogi/szerződéses korlát**: visszwatt-védelem, betáplálási engedély hiánya, támogatási program kikötései.
- **Biztonsági tartalék elvesztése** – lemerült akku nem tud szünetmentes funkciót ellátni; nyári vihar utáni áramszünetnél ez számít.
- **Ciklus-amortizáció**, ha többlet-ciklust okoz.
- **Szinkronizált lépcső** kockázata, ha mindenki azonos időzítést használ.
- **Marginális haszon a saját fogyasztás fedezéséhez képest** – a többlet csak a saját esti fogyasztás feletti rész.
- **Reggeli visszatöltés** rosszabb esetben hálózatról történik (borús reggel), ami rontja a mérleget.

---

## 4. Számítás

### Bemenő feltevések

| Paraméter | Érték | Forrás / megjegyzés |
|---|---|---|
| Átlagos akkukapacitás | 15 kWh | felhasználói feltevés; a 2026-os program minimuma 10 kWh, felső korlát nincs |
| Visszatáplált hányad | 40% | felhasználói feltevés |
| Részvételi arány | 2/3 | felhasználói feltevés |
| Időablak | 19:00–22:00 (3 óra) | |

**Egy háztartás:** 15 kWh × 40% = **6 kWh**, 3 óra alatt = **2 kW átlagteljesítmény**

### Forgatókönyvek

| Akkus háztartás (db) | Résztvevő (2/3) | Esti energia | Átlagteljesítmény 19–22 | Az esti csúcs %-a (~6 500 MW) |
|---|---|---|---|---|
| 10 000 | 6 667 | **40 MWh** | 13,3 MW | 0,21% |
| 25 000 | 16 667 | **100 MWh** | 33,3 MW | 0,51% |
| 40 000 | 26 667 | **160 MWh** | 53,3 MW | 0,82% |
| 65 000 | 43 333 | **260 MWh** | 86,7 MW | 1,33% |

> **Bázis-becslés:** a Napenergia Plusz Program keretében 25 000+ háztartás készült el tárolóval, a 2026-os Otthoni Energiatároló Program pedig 100 Mrd Ft-ból kb. 40 000 háztartást és ~200 MW lakossági kapacitást céloz. Reális 2026 végi bázis tehát nagyságrendileg **50–65 ezer akkus háztartás**, de a 2026-os pályázat egy része még nem üzemel.

### Mit jelent ez a gyakorlatban? (25 000 háztartásos alapeset)

- **100 MWh** ≈ kb. **14 000 magyar háztartás teljes napi áramfogyasztása** (átlag ~7,1 kWh/nap).
- **33 MW** ≈ egy közepes gázturbinás csúcserőmű-egység teljesítménye – pont az a típus, amit este indítanak.
- **Éves szinten** (kb. 200 használható nap): **~20 GWh**.
- **CO₂-megtakarítás**: ha gázos csúcstermelést vált ki (~0,45 kg CO₂/kWh), 20 GWh ≈ **~9 000 tonna CO₂/év**.
- **Piaci érték**: esti csúceáron (~100–150 EUR/MWh) a 20 GWh nagyságrendileg **2–3 M EUR/év** – amiből a háztartások az 5 Ft/kWh-s átvételi áron összesen ~100 M Ft-ot látnak. Ez jól mutatja, miért lenne jobb aggregátoron keresztül csinálni.

### Egy résztvevő mérlege (6 kWh/este)

| Tétel | Bruttó elszámolás | Szaldó elszámolás |
|---|---|---|
| Betáplálásból bevétel | ~30 Ft/este | kWh-jóváírás |
| Elmaradt saját felhasználás értéke | −216 Ft (6 × 36 Ft) | kiegyenlítődik |
| **Nettó** | **≈ −186 Ft/este (~−5 600 Ft/hó)** | **≈ 0** |
| Akku-amortizáció | 0 Ft, ha nincs többletciklus; egyébként ~90 Ft/este | ugyanaz |

---

## 5. Ellenőrző lista a posztolás előtt

- [ ] Az árak (4,39–5,25 Ft betáplálás, 36 Ft A1) posztolás előtt még egyszer ellenőrizve
- [ ] A poszt nem sugallja, hogy ez pénzügyileg nyerő
- [ ] Szerepel benne, hogy csak érvényes betáplálási engedéllyel csináld
- [ ] Szerepel benne, hogy hálózatról tölteni és eladni tilos
- [ ] Szerepel a szórt indítás javaslata
- [ ] Van tartalék-SoC ajánlás
