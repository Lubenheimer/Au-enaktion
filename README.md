# Abbauaktion Zelt – KiTa Außengelände

Schlanke Eintrageseite für die Abbauaktion des Zelts auf dem KiTa-Außengelände.  
Personen können sich für einzelne Tage eintragen. Daten werden zentral in **Supabase** (PostgreSQL) gespeichert und in Echtzeit auf alle Geräte synchronisiert.

---

## 1 · Supabase einrichten (einmalig, ~5 Minuten)

### 1.1 Projekt anlegen
1. [supabase.com](https://supabase.com) → **Start your project** → mit GitHub anmelden
2. **New project** → Name vergeben (z. B. `kita-zelt`) → Region: `West EU (Frankfurt)` → Passwort setzen → **Create new project**

### 1.2 Tabelle anlegen
Im Supabase-Dashboard links auf **SQL Editor** klicken und folgendes SQL ausführen:

```sql
-- Tabelle anlegen
CREATE TABLE signups (
    id        uuid DEFAULT gen_random_uuid() PRIMARY KEY,
    date_id   text        NOT NULL,
    name      text        NOT NULL,
    created_at timestamptz DEFAULT now()
);

-- Öffentlichen Lese- und Schreibzugriff erlauben (kein Login nötig)
ALTER TABLE signups ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Lesen erlaubt"   ON signups FOR SELECT USING (true);
CREATE POLICY "Eintragen erlaubt" ON signups FOR INSERT WITH CHECK (true);
CREATE POLICY "Austragen erlaubt" ON signups FOR DELETE USING (true);
```

### 1.3 Echtzeit aktivieren
1. Links auf **Table Editor** → Tabelle `signups` anklicken
2. Oben rechts auf das **Realtime**-Symbol klicken → aktivieren  
   *(oder: Database → Replication → `signups` einschalten)*

### 1.4 API-Zugangsdaten kopieren
1. Links auf **Project Settings** (Zahnrad) → **API**
2. Folgende zwei Werte notieren:
   - **Project URL** → sieht aus wie `https://xxxxxxxxxxx.supabase.co`
   - **anon / public** Key → langer JWT-String

---

## 2 · Konfiguration in index.html eintragen

Die Datei `index.html` öffnen und die zwei Platzhalter (~Zeile 130) ersetzen:

```js
const SUPABASE_URL      = 'https://xxxxxxxxxxx.supabase.co';   // ← Project URL
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIsInR5c...';     // ← anon key
```

---

## 3 · Auf GitHub Pages veröffentlichen

### 3.1 Repository anlegen & pushen
```bash
git init
git add index.html README.md
git commit -m "init: Abbauaktion Zelt"
```

1. Auf [github.com](https://github.com) ein **neues Repository** anlegen (z. B. `kita-zelt`)
2. Remote hinzufügen und pushen:
```bash
git remote add origin https://github.com/DEIN_USERNAME/kita-zelt.git
git push -u origin main
```

### 3.2 GitHub Pages aktivieren
1. Im Repository: **Settings → Pages**
2. Source: **„Deploy from a branch"**
3. Branch: `main` / Ordner: `/ (root)` → **Save**

Nach ~1 Minute erreichbar unter:  
`https://DEIN_USERNAME.github.io/kita-zelt/`

---

## Hinweise

| Thema | Detail |
|---|---|
| **Gleiche Namen** | Zwei „Max" können sich beide eintragen – jeder Eintrag bekommt eine eigene ID |
| **Demo-Modus** | Ohne Supabase-Konfiguration läuft die App lokal ohne Speicherung |
| **Echtzeit** | Alle Geräte sehen Änderungen sofort (Supabase Realtime) |
| **Datenschutz** | Nur eingetragene Namen werden gespeichert – keine Cookies, kein Tracking |
| **Kosten** | Supabase Free Tier reicht für diesen Use Case vollständig aus |
