# 📈 Akcijų portfelis

Asmeninis akcijų portfelio sekimo įrankis. Statinė vieno failo (`index.html`) aplikacija, veikianti GitHub Pages.

**URL:** https://railadeividas.github.io/stocks/

## Funkcionalumas

- ✅ Pirkimų ir pardavimų įrašymas (simbolis, kiekis, kaina, data, pastabos)
- ✅ Portfelio suvestinė — investuota suma, **rinkos vertė**, **neralizuotas P/L (€ ir %)**, realizuotas P/L, **bendras P/L**
- ✅ Turimos pozicijos su vidutine vieneto kaina (average-cost bazė), rinkos verte ir **portfelio dalimi (%)**
- ✅ **Kelių valiutų palaikymas** (EUR bazė) — akcijos gali būti USD/GBP/CHF, EUR savikaina skaičiuojama iš įvestos EUR sumos; valiutų kursai imami iš Yahoo arba įvedami rankiniu būdu
- ✅ **Gyvos kainos iš Yahoo Finance** — atnaujinamos tik paspaudus mygtuką (jokio automatinio polling'o), rodomas paskutinio atnaujinimo laikas
- ✅ **Interaktyvus kainos grafikas** (SVG) — „Kainos ir grafikas" sekcijoje pasirenki bet kurią akciją iš transakcijų (net be duomenų) ir paspaudi „Atnaujinti grafiką"; paima dienos duomenis smulkiu intervalu (`interval=5m&range=1d`) iš Yahoo. Užvedus pelyte rodomas žymeklis (crosshair) su tikslia kaina ir laiku
- ✅ **Stebimų akcijų sąrašas** — gali pridėti bet kurį simbolį (net jei jo nėra pirkimuose) ir žiūrėti jo grafiką; nedaro įtakos portfelio skaičiavimams
- ✅ **Dabartinės kainos įvedimas** ir rankiniu būdu tiesiog lentelėje → automatiškai skaičiuojamas neralizuotas pelnas/nuostolis
- ✅ **Atskira „Realizuoti sandoriai" lentelė** — uždarytos pozicijos nedingsta, matai realizuotą grąžą pagal simbolį
- ✅ Realizuotas pelnas / nuostolis skaičiuojamas parduodant (average-cost)
- ✅ Transakcijų istorija su paieška ir filtravimu (pirkimai / pardavimai)
- ✅ Redagavimas ir trynimas
- ✅ **Duomenų eksportas** (rodo pretty JSON su kopijavimo mygtuku) ir **importas** (JSON įklijavimas su validacija)
- ✅ **Statistikos eksportas Markdown formatu** (suvestinė, pozicijos ir istorija — su kopijavimo mygtuku)
- ✅ Duomenys saugomi naršyklėje (**IndexedDB**) — jokio serverio
- ✅ Lietuviška sąsaja, tamsi tema
- ✅ Nulinės priklausomybės — grynas HTML + JavaScript

## Kaip naudotis

1. Atsidaryk https://railadeividas.github.io/stocks/
2. **+ Nauja transakcija** → užpildyk formą (pirkimas arba pardavimas)
3. „Kainos ir grafikas" sekcijoje pasirink akciją ir spausk **Atnaujinti grafiką** — paims kainą ir grafiko duomenis iš Yahoo (kaina automatiškai atsiras ir turimų pozicijų lentelėje). Kainą taip pat gali įvesti rankiniu būdu lentelėje. Norėdamas žiūrėti akciją, kurios neturi — įrašyk simbolį į **„Stebėti simbolį"** ir spausk „Pridėti į sąrašą" (pašalinti — mygtuku „Pašalinti iš sąrašo")
4. Arba turimų pozicijų lentelėje įvesk **dabartinę kainą** rankiniu būdu → iškart matysi neralizuotą pelną/nuostolį, rinkos vertę ir portfelio dalį
5. Stebėk suvestinę, turimas pozicijas ir realizuotus sandorius viršuje
6. **⬇ Eksportuoti** — parodo transakcijų pretty JSON; paspausk „Kopijuoti" ir išsaugok (atsarginė kopija).
   - Pažymėjus **„Įtraukti simbolių duomenis"** — pridedama kainų istorija, stebimi simboliai ir dabartinės kainos
   - Pažymėjus **„Įtraukti valiutų duomenis"** — pridedama kursų istorija ir dabartiniai kursai
   - Be varnelių eksportas lieka paprastas transakcijų sąrašas (suderinamas su senesnėmis versijomis)
7. **⬆ Importuoti** — įklijuok JSON į laukelį ir spausk „Importuoti"; kainų/valiutų duomenys aptinkami automatiškai.
   - Nepažymėjus **„Perrašyti viską (override)"** — importuoti duomenys **sujungiami** su esamais (pridedami tik nauji įrašai)
   - Pažymėjus **override** — esamos transakcijos pakeičiamos importuotomis; kainų/kursų istorija visada **sujungiama**
8. **📝 Ataskaita** — sugeneruoja statistikos ataskaitą Markdown formatu

## Duomenų saugojimas

Visi duomenys saugomi naršyklės **IndexedDB** duomenų bazėje (`stocks_portfolio`):
- `transactions` — transakcijos.
- `kv` — įvestos kainos, valiutų kursai, kainų/grafikų kešas, stebimų simbolių sąrašas, grafiko laikotarpis.
- `priceHistory` — dienos akcijų kainų istorija (`{ symbol, date, close, currency }`), atsiunčiama iš Yahoo.
- `fxHistory` — dienos valiutų kursų istorija (`{ currency, date, rate }`).

Kainų ir kursų istorija pildoma paspaudus **„Atnaujinti visas kainas"** (užpildo dienos duomenis
nuo seniausios transakcijos iki šiandien). Iš jos sudaromi ilgo laikotarpio grafikai ir **portfelio
vertės laike** grafikas (skaičiuojamas iš istorijos + transakcijų).

> Jei anksčiau naudojai versiją su `localStorage`, duomenys automatiškai perkeliami
> į IndexedDB pirmą kartą atidarius naują versiją.

Duomenys neišeina iš tavo įrenginio ir nesiunčiami niekur. Kad neprarastum jų valydamas
naršyklės duomenis ar keisdamas įrenginį — reguliariai naudok **Eksportuoti**.

## Gyvos kainos (Yahoo Finance)

Kainos imamos iš neoficialaus Yahoo Finance endpoint'o, pvz.:

```
https://query1.finance.yahoo.com/v8/finance/chart/AAPL?interval=1h&range=1d
```

**CORS:** Yahoo šiam endpoint'ui nesiunčia CORS antraščių, todėl iš statinio puslapio
(GitHub Pages) tiesioginė užklausa būtų blokuojama. Todėl einama per CORS tarpinį serverį.

Kad vieno tarpinio serverio gedimas (`Failed to fetch`) nesustabdytų visko, `index.html`
faile yra **kelių proxy sąrašas** (`PROXIES`) — bandoma iš eilės, kol vienas suveikia:

```js
const PROXIES = [
  { build: (u) => "https://api.allorigins.win/get?url=" + encodeURIComponent(u), unwrap: ... },
  { build: (u) => "https://api.allorigins.win/raw?url=" + encodeURIComponent(u), unwrap: ... },
  { build: (u) => "https://corsproxy.io/?url=" + encodeURIComponent(u), unwrap: ... },
  { build: (u) => "https://thingproxy.freeboard.io/fetch/" + u, unwrap: ... },
];
```

Pirmas (`allorigins /get`) įpakuoja atsakymą į `{contents}` ir grąžina 200 net Yahoo klaidoms,
tad nežinomas tickeris duoda aiškią „nerasta" žinutę, o ne CORS. Nori pridėti/pakeisti proxy —
redaguok šį sąrašą (kiekvienas turi `build` — kaip sudaryti URL, ir `unwrap` — kaip išgauti Yahoo JSON).

## Valiutos (EUR bazė + rankinis kursas)

Portfelis skaičiuojamas viena **EUR baze**, bet akcijos gali būti kitos valiutos (USD, GBP, CHF).

- **Transakcijoje** pasirink valiutą. Ne-EUR atveju įvedi **kainą už akciją vietine valiuta**
  (pvz. USD, kaip rodo brokeris) ir **konvertuotą EUR sumą (be mokesčio)**. Iš to apskaičiuojama
  tiksli **EUR savikaina** ir pirkimo kursas (matomas kaip užuomina formoje, abiem kryptimis).
- **Mokestis (€)** — FX mokestis / komisiniai. Įtraukiamas į savikainą (perkant pridedamas,
  parduodant atimamas iš pajamų), tad realizuotas ir neralizuotas P/L rodo tikrą grąžą po mokesčių.
  Veikia ir EUR akcijoms (komisiniai).
- **Dabartinė kaina** turimų pozicijų lentelėje įvedama/atnaujinama iš Yahoo **vietine valiuta**.
- **Valiutų kursai** (virš pozicijų lentelės) — gali **paimti iš Yahoo** (🔄 mygtukas, pora
  `<val>EUR=X`, pvz. `USDEUR=X` grąžina EUR už 1 USD) arba įvesti **rankiniu būdu**
  (pvz. `1 USD = 0,92 €`). Pagal juos vietinė rinkos kaina konvertuojama į EUR (rinkos vertė,
  neralizuotas P/L). Jei kursas neįvestas, ta pozicija rinkos verte skaičiuojama pagal savikainą.

> EUR akcijoms (pvz. BMW) nieko keisti nereikia — valiuta lieka EUR, jokio kurso.

Kursai saugomi IndexedDB (`kv` store).

## Paleidimas lokaliai

Kadangi tai vienas statinis failas, pakanka atidaryti `index.html` naršyklėje.
Arba paleisk paprastą serverį:

```bash
python3 -m http.server 8000
# atsidaryk http://localhost:8000
```

## GitHub Pages diegimas

Repozitorijoje: **Settings → Pages → Source: `master` branch / `/root`**.
Puslapis pasieks per kelias minutes adresu aukščiau.

## Licencija

Asmeniniam naudojimui.
