# SpaceX Falcon 9 – Colectarea datelor prin API

Notebook de laborator (IBM Data Science – Capstone, Modulul 1) în care se colectează date despre lansările SpaceX folosind **SpaceX REST API v4**, apoi se curăță și se structurează într-un `DataFrame` pentru analize ulterioare (de exemplu, predicția reușitei aterizării primei trepte a rachetei Falcon 9).

## Conținut

Fișier: `module1_Complete_the_Data_Collection_API_Lab.ipynb`

## Ce face notebook-ul

1. **Importă bibliotecile** necesare: `requests`, `pandas`, `numpy`, `datetime`.
2. **Definește funcții helper** care interoghează API-ul pentru detalii suplimentare, pornind de la ID-urile din lista de lansări:
   - `getBoosterVersion` – numele rachetei (`/rockets/{id}`)
   - `getLaunchSite` – numele, longitudinea și latitudinea rampei (`/launchpads/{id}`)
   - `getPayloadData` – masa încărcăturii și orbita (`/payloads/{id}`)
   - `getCoreData` – blocul, numărul de reutilizări, serialul, rezultatul aterizării, aripioarele (grid fins), picioarele de aterizare etc. (`/cores/{id}`)
3. **Descarcă lansările trecute** de la `https://api.spacexdata.com/v4/launches/past` și le normalizează cu `pd.json_normalize`. Pentru reproductibilitate, se folosește și un fișier JSON static al cursului.
4. **Filtrează datele**:
   - păstrează doar coloanele `rocket`, `payloads`, `launchpad`, `cores`, `flight_number`, `date_utc`;
   - elimină lansările cu mai multe nuclee sau mai multe încărcături;
   - convertește `date_utc` în dată și păstrează lansările până la **13.11.2020**.
5. **Apelează API-ul** pentru fiecare lansare și umple listele globale (`BoosterVersion`, `PayloadMass`, `Orbit`, `LaunchSite`, `Outcome`, `Flights`, `GridFins`, `Reused`, `Legs`, `LandingPad`, `Block`, `ReusedCount`, `Serial`, `Longitude`, `Latitude`).
6. **Construiește `DataFrame`-ul final** `df` din aceste liste.
7. **Filtrează doar Falcon 9** (elimină Falcon 1) în `data_falcon9`.
8. **Tratează valorile lipsă**: înlocuiește `NaN` din `PayloadMass` cu media coloanei.

## Gestionarea API-ului indisponibil

API-ul public SpaceX nu răspunde întotdeauna corect, așa că notebook-ul include mai multe variante de rezervă:

- un dicționar static `rocket_names` care mapează ID-urile rachetelor la nume (Falcon 1, Falcon 9, Falcon Heavy, Starship);
- o variantă a funcțiilor cu `api_get()` (timeout, cache, detectarea căderii API-ului) care revine la date statice;
- un flag `API_OK`: dacă apelurile eșuează, `df` se încarcă din fișierul CSV de rezervă al cursului (`dataset_part_1.csv`).

> Notă: notebook-ul conține mai multe definiții succesive ale acelorași funcții (originală, cu fallback, cu excepții). Se aplică ultima definiție rulată, deci celulele trebuie executate în ordine.

## Coloanele din setul final

| Coloană | Descriere |
|---|---|
| `FlightNumber` | Numărul zborului |
| `Date` | Data lansării |
| `BoosterVersion` | Versiunea rachetei |
| `PayloadMass` | Masa încărcăturii (kg) |
| `Orbit` | Orbita țintă |
| `LaunchSite` | Rampa de lansare |
| `Outcome` | Rezultatul aterizării (succes/eșec + tip) |
| `Flights` | Numărul de zboruri ale nucleului |
| `GridFins` | Are aripioare de control (grid fins) |
| `Reused` | Nucleu reutilizat |
| `Legs` | Are picioare de aterizare |
| `LandingPad` | ID-ul platformei de aterizare |
| `Block` | Versiunea (block) a nucleului |
| `ReusedCount` | De câte ori a fost reutilizat nucleul |
| `Serial` | Serialul nucleului |
| `Longitude`, `Latitude` | Coordonatele rampei |

## Cerințe

- Python 3.8+
- Jupyter Notebook / JupyterLab / Google Colab
- Pachete: `requests`, `pandas`, `numpy`

```bash
pip install requests pandas numpy
```

## Rulare

```bash
jupyter notebook module1_Complete_the_Data_Collection_API_Lab.ipynb
```

Rulează celulele de sus în jos. Este nevoie de conexiune la internet (API-ul SpaceX și fișierele statice ale cursului).

## Surse de date

- SpaceX API v4: <https://api.spacexdata.com/v4/>
- Fișiere statice IBM Skills Network (JSON și CSV de rezervă)

## Pași următori

Setul de date rezultat este baza pentru modulele următoare: web scraping, data wrangling, EDA, vizualizări interactive și modele de clasificare pentru a prezice dacă prima treaptă Falcon 9 va ateriza cu succes.
