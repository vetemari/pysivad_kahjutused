# Põllukultuuride püsivate kahjustuste tuvastamine Sentinel-2 andmete abil Eestis
### Sentinel-2 EVI aegridade analüüs (2017–2024) | Google Earth Engine

Projekt tuvastab püsivalt kahjustunud põllumajandusalasid üle Eesti, kasutades Sentinel-2 satelliidipilte ja täiustatud taimkatte indeksit (EVI). Töötati välja magistritöö raames, mille eesmärk on analüüsida pikaajaliste põllukultuurikahjustuste ruumilist jaotust ja potentsiaalseid põhjuseid Eesti põllumajandusmaastikes.

---

## Ülevaade

Skript tuvastab piksleid, kus taimkate on püsivalt kehv 8-aastase perioodi (2017–2024) jooksul, kasutades kahte reguleeritavat lävendväärtust:

- **EVI lävend** — maksimaalne EVI väärtus, mida loetakse kahjustunuks (nt ≤ 0,30; 0,33 või 0,35)
- **Ajaline lävend** — minimaalne osakaal vaatlustest, mil EVI peab olema lävendist madalam (nt 55%, 60%, 65%, 70%, 75%)

Kokku genereeritakse **15 mudelikombinatsiooni**. Tulemused kombineeritakse **soojuskaardiks (0–15)**, mis näitab, mitu mudelit klassifitseerib iga piksli kahjustunuks — kõrgem väärtus tähendab suuremat kindlust püsiva kahjustuse suhtes.

---

## Uurimisala

Eesti — põllumajandusmaa, mis oli klassifitseeritud põllumaaks **vähemalt 7 aastal 8-st** (2017–2024), lähtudes iga-aastastest põldude piiride vektorkihtidest. Uurimisala mask genereeritakse dünaamiliselt aastaste põldude andmete põhjal.

**Koordinaatsüsteem:** EPSG:3301 (Eesti riiklik koordinaatsüsteem)  
**Ruumiline lahutus:** 10 m

---

## Metoodika

### 1. Põllumajandusmaa mask
Aastased põllumajandusmaa vektorkihid teisendatakse rasterkujule ja liidetakse. Analüüsi kaasatakse ainult pikslid, mis olid põllumaa vähemalt 7 aastal 8-st.

### 2. Pilvefilter
Sentinel-2 SR  pildid filtreeritakse `QA60` kihi abil — läbipaistmatud pilved (bitt 10) ja tsirrus-pilved (bitt 11) eemaldatakse.

### 3. EVI arvutamine
Iga aasta **juuni ja juuli** kohta arvutatakse kuised mediaankomposiidid, kasutades standardset EVI valemit:

```
EVI = 2,5 × (NIR − RED) / (NIR + 6×RED − 7,5×BLUE + 1)
```

Kasutatakse Sentinel-2 kanaleid: B8 (NIR), B4 (RED), B2 (BLUE), skaleeritud peegeldusvõimeks (÷10000).

### 4. Püsivate kahjustuste tuvastamine
Iga 15 lävikombinatsiooni puhul klassifitseeritakse piksel kahjustunuks, kui selle EVI on allpool EVI lävendit vähemalt määratud osakaalul kõigist ajasammudest. Tulemused eksporditakse eraldi failidena ja kombineeritakse soojuskaardiks.

---

## Väljundid

| Fail | Kirjeldus |
|---|---|
| `fields_0to8.tif` | Põllumajandusmaa loendusraster (0–8 aastat) |
| `heatmap_0to15.tif` | Koondsoojuskaart — mitu mudelit klassifitseerib piksli kahjustunuks (0–15) |
| `mudel_eviXX_timeYY.tif` | Üksikmudelite tulemused (15 faili) |
| `studyRaster_7of8` | Uurimisala mask eksporditud GEE assetina |

Kõik väljundid eksporditakse **10 m lahutusega** koordinaatsüsteemis **EPSG:3301**.

---

## Nõuded

- Google Earth Engine konto
- Ligipääs järgmistele GEE assetitele (või asenda oma põldude piiride andmetega):
  ```
  projects/magister-458413/assets/neg10_pollud20XX  (2017–2024)
  ```
- Kaks huvipunkti geomeetriat (`roi1`, `roi2`) defineeritud GEE-s graafikute visualiseerimiseks

---

## Kasutamine

1. Ava skript [Google Earth Engine koodiredaktoris](https://code.earthengine.google.com/)
2. Defineeri `roi1` ja `roi2` punkt- või polügoongeomeetriatena EVI graafikute jaoks
3. Käivita skript — kihid lisatakse kaardile
4. Kasuta **Tasks** paneeli eksporditööde käivitamiseks Google Drive'i

---


## Autor
Vete-Mari Kuningas
Magistritöö projekt — Tartu Ülikool
