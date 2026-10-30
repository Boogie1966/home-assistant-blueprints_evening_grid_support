<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="../brand/siot-logo-horizontal-dark.svg">
    <img src="../brand/siot-logo-horizontal.svg" alt="S/IoT" width="300">
  </picture>
</p>

# Esti hálózatsegítő akkumulátor-kisütés

*English: [../../README.md](../../README.md)*

[![Nyisd meg a Home Assistantodat és importáld a blueprintet.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FBoogie1966%2Fhome-assistant-blueprints_evening_grid_support%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fboogie1966%2Festi_halozatsegito_kisutes.yaml)

👆 **Egy kattintás.** Megnyílik a saját Home Assistantodban a blueprint-importáló, előre kitöltve.

---

## Mit csinál?

Nyáron a magyar villamosenergia-rendszer legnehezebb órái nem délben vannak, hanem **este 19 és 22 óra között**: a nap lement, a ~8 GW napelemes kapacitás kiesett, de megy a klíma, a főzés, az e-autó töltő. Ezt a lyukat drága gázos csúcserőművekkel és importtal kell betömni.

Akinek van tárolója és **reggelre rendszeresen marad benne töltés**, az ezt az energiát gyakorlatilag parlagon hagyja. Ez a blueprint 19:00–22:00 között kisüti a beállított tartalék töltöttségig, majd garantáltan visszaáll normál üzemmódba.

## Miért gyártófüggetlen?

A blueprint csak a **logikát** adja: időzítés, szórás, SoC-védelem, hibakezelés, visszaállás. Az inverter-specifikus parancsokat te töltöd ki három akciómezőben. Ugyanaz a fájl megy Victronon, Solaxon, Deye/Sunsynken, Huaweien, SolarEdge-en, Sungrow-on, Growatton.

👉 **[Márkánkénti kiindulási példák (angol): ../inverter-examples.md](../inverter-examples.md)**

## A négy ág

| Trigger | Mit csinál |
|---|---|
| `19:00` | **0–45 perc véletlen** késleltetés → feltételek → indító parancsok |
| `21:45` | Teljesítmény-visszavétel — lágy leállás |
| `22:00` **vagy** SoC < tartalék | Leállítás, **feltétel nélkül** |
| HA indulás | Ablakon belül folytat, ablakon kívül **leállít** |

Az utolsó sor a fontos: ha egy áramszünet vagy HA-újraindítás export módban hagyta az invertert, ez magától kijavítja. A `mode: restart` pedig azt oldja meg, hogy a véletlen késleltetés alatt beérkező SoC-riasztás ne ragadjon be a sor mögé.

## Miért kell a szórás?

Ha több ezer háztartás pont 19:00:00-kor lépne be és 22:00:00-kor ki, az **két meredek lépcső** a rendszerben — pont az ellenkezője annak, amit el akarunk érni. Ezért van benne a randomizálás. **Kérlek, hagyd bekapcsolva.**

---

## ⚠️ Mielőtt bekapcsolod

**1. Csak érvényes betáplálási engedéllyel.** Sok rendszeren aktív a visszwatt-védelem (nulla betáplálás), akár szabályozásból, akár a hálózati csatlakozási szerződésből adódóan. Ha nálad ez a helyzet, ez a blueprint nem alkalmazható.

**2. A tárolót csak napelemből töltsd.** A HMKE definíció szerint megújuló forrásból termel. Hálózatról tölteni és este eladni engedélyköteles energiakereskedés, nem HMKE.

**3. Bruttó elszámolásnál ez ráfizetés.** A betáplálásért lakossági ügyfélnek elosztótól függően **4,39–5,25 Ft/kWh** jár (E.ON/OPUS 4,39 · ÉMÁSZ 4,94 · ELMŰ 5,11 · DÉMÁSZ 5,25), reggel viszont 36 Ft/kWh-tól veszed vissza. 6 kWh-nál ez kb. **napi 186 Ft, havi 5 600 Ft** ráfizetés.

**Szaldó elszámolásnál nagyjából semleges**, mert az éves elszámolásban a betáplált és a vételezett kWh kiegyenlíti egymást. A szaldósok a fő célcsoport.

**4. Az őszinte ellenérv.** Ha az akku este egyszerűen csak a saját házadat viszi, azzal máris majdnem ugyanannyi terhelést vettél le a rendszerről. A betáplálás csak a saját esti fogyasztásod feletti rész. Az igazi üzenet inkább: *este ne a hálózatból élj.*

**5. Akkumulátor-kopás.** Ha az energiát amúgy is felhasználtad volna reggel, a ciklus úgyis megtörténik — a többletkopás nulla. Csak akkor keletkezik plusz kopás, ha emiatt több ciklust futtatsz.

**6. Először teszteld szárazon.** Hagyd üresen az akciómezőket, nézd meg a nyomkövetésben (traces), hogy jó időben, jó ágon fut-e le. Csak utána élesítsd.

---

## Mennyit ér ez összesen?

15 kWh-s átlagos akkunál 40% = **6 kWh** háztartásonként, három óra alatt **2 kW** átlagteljesítmény.

| Akkus háztartás | Résztvevő (2/3) | Esti energia | Átlagteljesítmény | Az esti csúcs %-a |
|---|---|---|---|---|
| 10 000 | 6 667 | 40 MWh | 13,3 MW | 0,21% |
| 25 000 | 16 667 | **100 MWh** | 33,3 MW | 0,51% |
| 40 000 | 26 667 | 160 MWh | 53,3 MW | 0,82% |
| 65 000 | 43 333 | 260 MWh | 86,7 MW | 1,33% |

**100 MWh** ≈ kb. 14 000 magyar háztartás teljes napi áramfogyasztása. **33 MW** ≈ egy közepes gázturbinás csúcserőmű-egység — pont az a típus, amit este indítanak.

> **Bázis:** a Napenergia Plusz Programban 25 000+ háztartás készült el tárolóval, a 2026-os Otthoni Energiatároló Program pedig 100 Mrd Ft-ból kb. 40 000 háztartást és ~200 MW lakossági kapacitást céloz. Reális 2026 végi bázis nagyságrendileg 50–65 ezer akkus háztartás, de a 2026-os pályázat egy része még nem üzemel.

---

## „De miért nem aggregátoron keresztül?"

Jogos kérdés. A professzionális megoldás a **virtuális erőmű (VPP)**: aggregátoron keresztül a tárolók a kiegyenlítő szabályozási piacon lényegesen többet kapnak, mint a HMKE 5 Ft/kWh-s átvételi ára.

Ez itt egy „kézi VPP", aminek két előnye van: **ma, ingyen, engedélyeztetés nélkül** indítható, és nem kell hozzá szerződni senkivel. Ha valaki komolyan gondolja hosszú távon, mindenképp az aggregátoros irány a jobb — ez inkább belépő szint, és egy demonstráció, hogy a lakossági tárolóflottában van néhány tíz MW.

---

## Hozzájárulás-számláló

A [`packages/evening_grid_support.yaml`](../../packages/evening_grid_support.yaml) méri a tényleges hozzájárulásodat: `min(betáplálás, akku-kisütés)` az esti ablakban, Riemann-integrállal kWh-ra, napi/havi/összes utility_meterrel. Így a 19:00-kor még sütő napelem közvetlen betáplálása nem számít bele.

Írd át a fájl tetején a két placeholder entitásnevet, tedd a `config/packages/` mappába, és a `configuration.yaml`-ben legyen:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

---

## Közreműködés

Ha van működő beállításod olyan inverterhez, ami még nincs a példák között, nyiss egy issue-t vagy PR-t a `start_actions` / `stop_actions` tartalmával és az integráció nevével. Nagyon hasznos lenne.
