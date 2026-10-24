# Trädgård

Trädgård är en liten webbapp (PWA) för att hålla reda på **vad du har planterat var** i trädgården, och för att få tips om **vilka växter som trivs ihop** (kompisplantering). Den går att installera på hemskärmen och fungerar offline. Inga beroenden och inget byggsteg: bara statiska filer.

## Vad appen gör

- **Bäddar:** du skapar bäddar som motsvarar riktiga ytor i trädgården, t.ex. en odlingslåda på 4×3 rutor (upp till 10×10).
- **Plantering per ruta:** tryck på en ruta och välj vilken växt som står där. Appen sparar planteringsdatum och låter dig skriva en anteckning (sort, sådatum, skörd …).
- **Grannkontroll:** rutor med växter som trivs med sina grannar får grön ram och ✓. Rutor med en växt som samsas dåligt med en granne får röd ram och !. Symbolerna finns med så att färgen inte är det enda som visar status.
- **Val med stöd:** när du väljer växt till en tom ruta visar listan hur varje växt passar ihop med rutans grannar (✓ trivs, ! undvik) och sorterar de bästa först.
- **Förslag per bädd:** under bädden visas växter som passar ihop med det du redan har ("Passar till det du har") och växter du bör undvika i samma bädd ("Undvik i samma bädd").
- **Växtguide:** en flik där du kan söka bland växterna och se vad var och en trivs med respektive bör undvika.
- **Säkerhetskopia:** exportera och importera alla dina bäddar som en JSON-fil.

Växtdatabasen har 25 vanliga köksväxter, blommor och kryddor. Kompisplantering bygger mest på tradition och odlarerfarenhet, inte på hårda studier. Se det som tips och testa själv.

## Hur den fungerar

- **Grannskap:** en ruta har upp till 8 grannar, alltså även de diagonala. Rutor utanför bädden räknas inte.
- **Relationer:** varje växt har en lista med växter den trivs med och en med växter den bör undvika. Relationerna gäller åt båda håll, så det räcker att de anges på ena växten. Om två grannar har både en positiv och en negativ koppling i konflikt, väger det negativa tyngst och rutan blir röd.
- **Förslagen** räknar ut hur många av bäddens växtsorter en kandidat trivs med. Växter som har en negativ koppling till någon sort i bädden föreslås inte utan hamnar under "Undvik". Förslagen gäller hela bädden, medan listan vid val av ruta tar hänsyn till just den rutans grannar.
- **Lagring:** all data sparas lokalt i enhetens webbläsare (`localStorage`). Ingenting skickas till någon server och det finns inget konto. Det betyder också att datan inte synkas mellan enheter. Använd Exportera/Importera om du vill flytta eller säkra den.
- **Offline:** ett service worker-skript (`sw.js`) cachar appens filer, så appen startar utan nät efter första besöket.

### Filer

| Fil | Innehåll |
|---|---|
| `index.html` | Hela appen: gränssnitt, stilar, logik och växtdatabas |
| `manifest.webmanifest` | Namn, färger och ikoner för installation |
| `sw.js` | Service worker för offline-stöd |
| `icon-192.png`, `icon-512.png` | Appikoner |

## Lägg upp som privat repo på GitHub

Med GitHub CLI, inne i projektmappen:

    git init -b main
    git add .
    git commit -m "Trädgård"
    gh repo create tradgard --private --source . --push

Eller skapa ett tomt privat repo på github.com och följ deras instruktioner för "push an existing repository".

## Publicera (krävs för att kunna installera)

En PWA måste serveras över **HTTPS**. Det går alltså inte att installera genom att bara öppna `index.html` från en fil. Välj ett alternativ:

- **Cloudflare Pages, Netlify eller Vercel** (gratis, fungerar med privata repon): koppla ditt GitHub-repo, lämna build-kommandot tomt och sätt publiceringsmapp till repots rot (`/`).
- **GitHub Pages:** privata repon stöds bara med betalt konto (Pro/Team). Settings → Pages → Deploy from branch → `main` / root.

Webbadressen blir publik även om repot är privat, men appen innehåller ingen data. Den ligger kvar i din telefon.

## Installera på iPhone

Du behöver iOS 16.4 eller senare och adressen där appen är publicerad (se ovan).

1. Öppna adressen i **Safari**.
2. Tryck på **Dela**-knappen (rutan med en pil uppåt) längst ned i Safari.
3. Scrolla i menyn och välj **Lägg till på hemskärmen**. Syns inte alternativet, tryck först på **Mer** längst ned i menyn.
4. Kontrollera att namnet är "Trädgård" och att **Öppna som webbapp** är påslaget om den inställningen visas. Tryck **Lägg till**.
5. Starta appen från ikonen på hemskärmen. Den öppnas i helskärm utan Safaris adressfält.

Bra att veta på iPhone:

- **Installera innan du lägger in data.** Appen på hemskärmen har egen lagring, skild från Safari. Bäddar du skapade i Safari-fliken följer alltså inte med. Behöver du flytta dem, använd Exportera i Safari och Importera i den installerade appen.
- **Ta säkerhetskopior ibland** med Exportera. Datan finns bara på telefonen, och om du raderar appen eller byter telefon försvinner den.
- **Uppdateringar:** när en ny version publicerats, stäng appen helt (svep bort den från app-växlaren) och öppna den igen. Ibland behövs två starter innan den nya versionen syns.

## Installera på Android

Öppna adressen i Chrome → ⋮ → **Installera app** (eller **Lägg till på startskärmen**).

## Uppdatera appen

Ändrar du något i `index.html`, höj `CACHE`-versionen i `sw.js` (t.ex. från `tradgard-v1` till `tradgard-v2`), annars kan telefonen fortsätta visa den gamla cachade versionen.

## Lägg till egna växter

Växtdatabasen ligger överst i skriptet i `index.html` (konstanten `RAW`). Format per rad:

    id|emoji|Namn|trivs med (id:n, mellanslag emellan)|undvik (id:n)

Exempel:

    ringblomma|🌼|Ringblomma|tomat potatis pumpa jordgubbar bonor kal|

Id:n skrivs utan å, ä och ö (t.ex. `lok` för lök). Alla id som nämns måste finnas som egen rad i listan.
