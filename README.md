# 👥 Team Dashboard – Wie doet nu wat

Een lichtgewicht online dashboard om per teamlid en project bij te houden in welke fase van de bouwteamfase men zit, wanneer die fase eindigt en wanneer de Realisatie start.

Gebouwd voor de **NuRijnland Perceelplanning**, maar eenvoudig aan te passen voor andere projectportfolio's.

## ✨ Features

- 📊 **KPI-cards**: teamleden, actieve toewijzingen, projecten in uitvoering, aflopend deze week
- 📈 **Grafieken**: verdeling per fase (donut) en toewijzingen per teamlid (bar)
- ⚠️ **Waarschuwing** voor fasen die binnen 7 dagen aflopen
- 🔍 **Filters & zoek**: op teamlid, project, fase of vrije tekst
- ✏️ **CRUD** via modal-formulier met dropdowns voor de 21 NuRijnland-projecten
- 💾 **Data-opslag** in browser (localStorage) + JSON export/import
- 🎨 **Modern responsive design** met dark-blue thema (#1F4E78)
- 🔌 **Zero dependencies** — één enkel HTML-bestand, alleen Chart.js via CDN

## 🚀 Live demo (GitHub Pages)

Zodra je onderstaande deploy-stappen hebt uitgevoerd, is de tool bereikbaar op:

```
https://<jouwgebruikersnaam>.github.io/team-dashboard/
```

## 📖 Snelstart deployen op GitHub Pages

Zie voor de uitgebreide instructie: [`docs/DEPLOY.md`](docs/DEPLOY.md)

**Kort samengevat:**
1. Maak een GitHub-account aan op https://github.com
2. Maak een nieuwe **public repository** aan met de naam `team-dashboard`
3. Upload alle bestanden uit deze folder naar de repo
4. Ga naar **Settings → Pages** en activeer Pages op branch `main`, folder `/ (root)`
5. Wacht 1–2 minuten; jouw dashboard staat live ✅

## 🏗️ Structuur

```
team-dashboard/
├── index.html          # De volledige dashboard-app (self-contained)
├── README.md
├── LICENSE             # MIT-licentie
├── .gitignore
├── CNAME               # (optioneel) custom domain
└── docs/
    └── DEPLOY.md       # Uitgebreide deploy-handleiding
```

## 🌐 Custom domain (optioneel)

1. Zet je domein in `CNAME` (één regel)
2. Voeg een CNAME-record toe bij je DNS-provider
3. In GitHub: **Settings → Pages → Custom domain** invullen

## 🔐 Data & privacy

De tool slaat gegevens **uitsluitend lokaal in de browser** op (`localStorage`). Er wordt niets naar een server verstuurd. Gebruik de Export JSON-knop voor backups.

## 📝 Licentie

MIT — zie [LICENSE](LICENSE).

---

_Gemaakt voor NuRijnland Perceelplanning · v1.0_
