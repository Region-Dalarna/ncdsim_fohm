# ncdsim_fohm

Detta repository innehåller en Shinyapplikation (`ncdsim_fohm`) för Samhällsanalys, Region Dalarna.

Appkoden under `app/` är importerad från ett befintligt repo via `git subtree`:

- **Källa:** https://github.com/FohmAnalys/ncdsim.git
- **Branch vid import:** main

## Struktur

- All appkod ligger i katalogen `app/` (importerad via git subtree)
- `_publicering_till_server.yml` i root styr vilken Shiny-server som är default för `shinyapp_publicera()`

- Deployment sker via GitHub Actions:
  - `.github/workflows/deploy.yml` – publicerar vid push till `publicera-publik` eller `publicera-intern`
  - `.github/workflows/avpublicera.yml` – tar bort appen från vald server (manuell trigger)

  Appmapp på servern: `/srv/shiny-server/ncdsim_fohm`.

## Hämta uppdateringar från källrepot

Appkoden under `app/` är kopplad till källrepot via `git subtree`. När
källrepot uppdateras kan du dra in ändringarna med ett kommando.

### Så här fungerar det

`git subtree pull` hämtar senaste från källrepot och slår ihop ändringarna
till en enda squashad commit under `app/`. Resten av repot (`.github/`,
README, deploy-workflows) påverkas inte.

Subtree pull kräver att working tree är **clean** — alla ändringar måste
vara committade eller stashade innan du kör. Annars får du felet:

```
fatal: working tree has modifications. Cannot add.
```

### Kommandon (Terminal, från repots mapp)

Stå i `c:/gh/ncdsim_fohm` när du kör kommandona:

```
cd c:/gh/ncdsim_fohm
```

**1. Kontrollera att working tree är clean:**

```
git status
```

Om det visar `nothing to commit, working tree clean` — hoppa till steg 3.

**2. Om det finns ändringar — committa eller stasha dem:**

Ett vanligt fall: `.Rproj`-filen ändras när du öppnat projektet i RStudio.
Committa den då:

```
git add .
git commit -m "Lokala ändringar innan subtree pull"
```

Eller stasha tillfälligt om du inte vill committa:

```
git stash
```

(återställ sedan efter pull med `git stash pop`)

**3. Hämta uppdateringar från källrepot:**

```
git subtree pull --prefix=app https://github.com/FohmAnalys/ncdsim.git main --squash
```

Om en editor öppnas med merge-meddelandet — bara spara och stäng:
- I **vim**: `Esc`, sen `:wq` + Enter
- I **nano**: `Ctrl+O`, Enter, `Ctrl+X`

**4. Pusha till GitHub:**

```
git push
```

### Alternativ: kör från R-konsolen

Om du föredrar att stanna i R kan du köra hela kedjan så här
(fungerar oavsett var i R du står — `-C` byter mapp åt dig):

```r
system2("git", c("-C", "c:/gh/ncdsim_fohm", "add", "."))
system2("git", c("-C", "c:/gh/ncdsim_fohm", "commit",
                 "-m", "Lokala ändringar innan subtree pull"))
system2("git", c("-C", "c:/gh/ncdsim_fohm",
                 "subtree", "pull", "--prefix=app",
                 "https://github.com/FohmAnalys/ncdsim.git", "main", "--squash"))
system2("git", c("-C", "c:/gh/ncdsim_fohm", "push"))
```

