# tnl-verwijzers — aanmeldpagina voor verwijzers

Publiek single-page appje waar freelance verwijzers uit het netwerk van
The Next Level een bedrijf ("lead") aanmelden. Onderdeel van het Finder's
Fee-systeem (spec: `Finders_Fee_App_Specificatie.md` in de website-repo).

## Wat het doet

1. Verwijzer vult zijn eigen gegevens in + één of meer bedrijven met
   kwalificatie-info (kwalificatienota, band met contactpersoon, concreet
   signaal, uitdaging, urgentie).
2. "Aanmelden" schrijft rechtstreeks weg in het offerte-Supabase-project via
   de beveiligde DB-functie `registreer_verwijzer_aanmelding` (SECURITY
   DEFINER) — dezelfde aanpak als de publieke QuickScan. Geen edge function,
   geen server.

De verwijzer heeft **geen** login en kan geen enkele tabel lezen. De opvolging
(aanvaarden/weigeren, AI-beoordeling, mail naar de lead) gebeurt volledig door
Dirk in de Offerte-app, tab "Verwijzers".

## Architectuur

```
index.html  (GitHub Pages, aanmelden.thenextlevel.consulting)
   │  anon-key  ->  POST /rest/v1/rpc/registreer_verwijzer_aanmelding
   ▼
Supabase-project sphmxlfpzzowsekzjltd
   │  SECURITY DEFINER-functie schrijft in:
   ▼
verwijzer / verwijzer_lead   (RLS: enkel 'authenticated' = Dirk ingelogd)
```

De AI-scoring van een lead zit **niet** hier maar in de Offerte-app
(Verwijzers-tab, knop "🤖 AI beoordelen"), met Dirk's eigen Claude-sleutel —
zoals de andere AI-functies daar.

## Deploy

- GitHub Pages vanaf `main`, custom domain via `CNAME`
  (`aanmelden.thenextlevel.consulting` — pas aan als je een andere naam kiest,
  en zet de DNS-CNAME naar `<user>.github.io`).
- De DB-functie + tabellen komen uit migratie `0042_verwijzers.sql` in de
  Offerte-app repo (in de Supabase SQL Editor draaien).

## Config in index.html

`SB_URL` / `SB_KEY` = publieke url + anon-key van het offerte-project (geen
secret; RLS beschermt de data). Meer niet.
