# GitHub Repository Létrehozása és Működtetési Útmutató

Ez a dokumentáció lépésről lépésre bemutatja, hogyan hozzunk létre egy biztonságos GitHub tárolót (repository), hogyan állítsuk be a jogosultságokat, és mi a helyes munkafolyamat csapatmunka esetén.

## Tartalomjegyzék
1. [I. Repository Előkészítése (Admin feladat)](#i-repository-előkészítése-admin-feladat)
    - [1. Üres Repository létrehozása](#1-üres-repository-létrehozása)
    - [2. Lokális inicializálás és az első feltöltés](#2-lokális-inicializálás-és-az-első-feltöltés)
    - [3. A .gitignore fájl beállítása](#3-a-gitignore-fájl-beállítása)
    - [4. Védelmi szabályok (Branch Protection) beállítása](#4-védelmi-szabályok-branch-protection-beállítása)
    - [5. Csapattársak hozzáadása (Collaborators)](#5-csapattársak-hozzáadása-collaborators)
2. [II. Fejlesztési Munkafolyamat (Napi használat)](#ii-fejlesztési-munkafolyamat-napi-használat)
    - [1. Új feladat indítása (Branching)](#1-új-feladat-indítása-branching)
    - [2. Projekt fájlok kezelése (Pl. Unity létrehozásakor)](#2-projekt-fájlok-kezelése-pl-unity-létrehozásakor)
    - [3. Munka mentése és beküldése](#3-munka-mentése-és-beküldése)
3. [III. Pull Request és Mergelés](#iii-pull-request-és-mergelés)
    - [1. Pull Request (PR) létrehozása (Fejlesztő feladata)](#1-pull-request-pr-létrehozása-fejlesztő-feladata)
    - [2. PR elfogadása és Mergelés (Tulajdonos feladata)](#2-pr-elfogadása-és-mergelés-tulajdonos-feladata)
    - [3. Takarítás (Fontos!)](#3-takarítás-fontos)
4. [IV. Csapattársak Csatlakozása](#iv-csapattársak-csatlakozása)
    - [Első alkalommal (Cloning)](#első-alkalommal-cloning)
    - [Napi szinkronizálás (Fetch vs Pull)](#napi-szinkronizálás-fetch-vs-pull)
5. [V. Konfliktuskezelés (Merge Conflicts)](#v-konfliktuskezelés-merge-conflicts)
6. [VI. Speciális Forgatókönyvek (Scenarios)](#vi-speciális-forgatókönyvek-scenarios)
    - [A) Unity Projekt Git Kezelése ("Mappamozgatós")](#a-forgatókönyv-unity-projekt-git-kezelése-a-mappamozgatós-módszer)
    - [B) Monorepo (Frontend + Backend)](#b-forgatókönyv-frontend--backend-egy-repóban-monorepo)

---

## I. Repository Előkészítése (Admin feladat)

### 1. Üres Repository létrehozása
A GitHub webes felületén:
1. Kattints a **New repository** gombra.
2. **Repository name:** Adj meg egy nevet.
3. **Public/Private:** Válaszd ki a láthatóságot.
4. **Initialize this repository with:**
   - Hagy **ÜRESEN** az *Add a README file* opciót.
   - Hagy **ÜRESEN** az *Add .gitignore* opciót.
   - Hagy **ÜRESEN** a *Choose a license* opciót.
5. Kattints a **Create repository** gombra.

### 2. Lokális inicializálás és az első feltöltés
A saját számítógépeden nyisd meg a terminált a kívánt mappában.

**Repository indítása:**
```bash
git init
```

**Kezdőfájl létrehozása (hogy legyen mit feltölteni):**
```bash
echo "# Projekt Neve" > README.md
```

**Fájlok előkészítése és rögzítése:**
```bash
git add README.md
git commit -m "first commit"
```

**Branch elnevezésének biztosítása és összekötés:**
```bash
git branch -M main
git remote add origin [REPOSITORY_URL]
```
*(A `[REPOSITORY_URL]` helyére a GitHub által generált linket illeszd be.)*

**Feltöltés:**
```bash
git push -u origin main
```

### 3. A .gitignore fájl beállítása
Fontos, hogy a projekt típusának megfelelő (pl. Unity, Node.js) szemetet kiszűrjük.

1. Keress egy hivatalos sablont (pl. [github/gitignore](https://github.com/github/gitignore)).
2. Hozz létre a projekt gyökerében egy `.gitignore` nevű fájlt.
3. Másold bele a sablon tartalmát és mentsd el.
4. **Töltsd fel:**
   ```bash
   git add .gitignore
   git commit -m "add .gitignore"
   git push
   ```

### 4. Védelmi szabályok (Branch Protection) beállítása
Ez a lépés biztosítja, hogy a `main` ágra ne lehessen közvetlenül írni, csak Pull Requesten keresztül, ugyanakkor a Tulajdonosnak (Admin) legyen felülbírálati joga.

A GitHubon: **Settings -> Branches -> Add branch protection rule**

1. **Branch name pattern:** `main`
2. **Require a pull request before merging:** Pipáld be.
   - **Require approvals:** Pipáld be, és állítsd **1**-re.
   - *Magyarázat: Ez megakadályozza, hogy a csapattársak review nélkül mergeljenek.*
3. **Do not allow bypassing the above settings:** Ezt hagyd **KIPIPÁLATLANUL**.
   - *Magyarázat: Ez teszi lehetővé neked (Admin), hogy felülbíráld a szabályt és review nélkül is mergelj, ha szükséges.*

### 5. Csapattársak hozzáadása (Collaborators)
A GitHubon: **Settings -> Collaborators -> Add people**

1. Írd be a felhasználónevet és küldd el a meghívót.
2. **Jogosultság:**
   - *Személyes (Personal) fiók esetén:* A rendszer automatikusan **Collaborator** (írási) jogot ad. Ez megfelelő, mert tudnak dolgozni, de a védett szabályokat nem tudják kijátszani.
   - *Szervezet (Organization) esetén:* Válaszd a **Write** jogot.

---

## II. Fejlesztési Munkafolyamat (Napi használat)

> **FIGYELEM:** Soha ne dolgozz közvetlenül a `main` ágon!

### 1. Új feladat indítása (Branching)
Mielőtt bármit csinálsz, frissítsd a repót, majd hozz létre új ágat:

```bash
git checkout main
git pull
git checkout -b [feature/feladat-neve]
```

### 2. Projekt fájlok kezelése (Pl. Unity létrehozásakor)
Ha olyan szoftvert használsz (pl. Unity), ami almappát hoz létre (Részletes útmutató a VI. fejezetben):

1. Generáld le a projektet az új ágon lévő mappába.
2. Lépj be a keletkezett almappába, és vágd ki (`Cut`) a tartalmát.
3. Illeszd be (`Paste`) a repository gyökerébe.
4. Töröld az üres almappát.
   - *Így a fájlok (pl. Assets, Packages) közvetlenül a repo gyökerében lesznek.*

### 3. Munka mentése és beküldése
Ha kész vagy a fejlesztéssel:

```bash
git add .
git commit -m "Rövid leírás a változtatásról"
git push -u origin [feature/feladat-neve]
```

---

## III. Pull Request és Mergelés

### 1. Pull Request (PR) létrehozása (Fejlesztő feladata)
1. A GitHubon kattints a **Compare & pull request** gombra.
2. Töltsd ki a címet és a leírást.
3. Kattints a **Create pull request** gombra.
4. **Státusz:** A fejlesztő látni fogja, hogy a "Merge" gomb inaktív ("Review required"), amíg a Tulajdonos nem lép közbe.

### 2. PR elfogadása és Mergelés (Tulajdonos feladata)
1. Nyisd meg a PR-t és ellenőrizd a **Files changed** fülön a kódot.
2. Ha minden rendben, a **Conversation** fülön a "Merge pull request" gombnál pipáld be a "Merge without waiting..." opciót (ha szükséges), vagy egyszerűen kattints a **Merge pull request** gombra.
3. **Erősítsd meg:** Confirm merge.

### 3. Takarítás (Fontos!)
A munka végeztével törölni kell a feleslegessé vált ágat.

- **GitHubon:** Nyomj a **Delete branch** gombra a merge után.
- **Lokálisan (Terminál):**
  ```bash
  git checkout main
  git pull
  git branch -d [feature/feladat-neve]
  ```

---

## IV. Csapattársak Csatlakozása

### Első alkalommal (Cloning)
Ha valaki először tölti le a projektet:

```bash
git clone [REPOSITORY_URL]
```
*(Unity esetén: A Unity Hub-ban az "Add" gombbal kell betallózni a letöltött mappát).*

### Napi szinkronizálás (Fetch vs Pull)
Mielőtt bárki munkához lát, **kötelező** frissíteni a gépét:

- **Ellenőrzés (csak infó):** `git fetch` (megnézi, van-e új dolog a szerveren).
- **Letöltés (frissítés):** `git pull` (letölti és alkalmazza az új fájlokat).

> Mindig `git pull` paranccsal kezdjük a napot a `main` ágon!

---

## V. Konfliktuskezelés (Merge Conflicts)
Előfordulhat, hogy te és a csapattársad ugyanannak a fájlnak ugyanazt a sorát módosítottátok. Ilyenkor a Git nem tudja eldönteni, melyik a helyes, és **Conflict** hibát jelez a `git pull` vagy a `git merge` során.

### A konfliktus feloldása lépésről lépésre:
1. **Hiba észlelése:** A terminál kiírja: `CONFLICT (content): Merge conflict in [fájlnév]`.
2. **Fájl megnyitása:** Nyisd meg a problémás fájlt egy szövegszerkesztőben (VS Code, Notepad, stb.).
3. **Jelölések keresése:** A Git ilyen jelekkel mutatja a problémát:
   ```plaintext
   <<<<<<< HEAD
   Ez a te verziód a kódból.
   =======
   Ez a szerverről jött verzió.
   >>>>>>> [commit-hash]
   ```
4. **Javítás:** Töröld ki a Git jelöléseit (`<<<`, `===`, `>>>`), és döntsd el, melyik kódrészlet maradjon meg (vagy gyúrd össze a kettőt).
5. **Rögzítés:**
   ```bash
   git add [fájlnév]
   git commit -m "Resolved merge conflict"
   git push
   ```

---

## VI. Speciális Forgatókönyvek (Scenarios)

### A) Forgatókönyv: Unity Projekt Git Kezelése (A "Mappamozgatós" módszer)
Unity-nél a legnagyobb kihívás, hogy a Unity Hub szeret egy saját mappát létrehozni a projektnévvel, de mi azt akarjuk, hogy a Repository gyökerében legyenek a fájlok (Assets, Packages), ne pedig egy almappában eldugva.

**Előfeltétel:** A Repo már létezik (`main` branch, `.gitignore` Unity-hez beállítva, `README.md` kész).

#### 1. Előkészületek (Branching)
Nyisd meg a terminált a repo mappájában:

```bash
git checkout main
git pull
git checkout -b feature/unity-init
```

#### 2. A Projekt Generálása (A trükkös rész)
1. Nyisd meg a **Unity Hub**-ot.
2. Kattints a **New Project** gombra.
3. **Location:** Válaszd ki a Git repositoryd mappáját.
4. **Project Name:** Adj meg egy ideiglenes nevet (pl. `TempUnity`).
5. Hozd létre a projektet (**Create project**).
6. **FONTOS:** Várd meg, amíg a Unity teljesen betölt, majd **ZÁRD BE** a Unity szerkesztőt!
   - *Miért? Mert a Windows nem engedi áthelyezni a fájlokat, amíg a Unity fut.*

#### 3. Fájlok helyretětele (Takarítás)
Most a mappád így néz ki: `Repo/.git`, `Repo/TempUnity`. Ezt javítjuk ki.

1. Nyisd meg a Fájlkezelőt.
2. Lépj be a `TempUnity` mappába.
3. Jelöld ki az összes mappát és fájlt (`Assets`, `Packages`, `ProjectSettings`, `UserSettings` stb.).
4. Vágd ki őket (**Ctrl + X**).
5. Lépj vissza egyet a Repo gyökerébe.
6. Illeszd be őket (**Ctrl + V**).
7. Töröld ki az üres `TempUnity` mappát.

#### 4. Commit és Push
Most ellenőrizzük, hogy a `.gitignore` teszi-e a dolgát.

```bash
git status
```
- **Jó:** Ha csak az `Assets`, `Packages`, `ProjectSettings` mappákat látod zölddel/pirossal.
- **Rossz:** Ha látsz `Library`, `Temp`, `Logs` mappákat. (Ha ilyet látsz, ellenőrizd a `.gitignore`-t!).

Ha tiszta a terep:

```bash
git add .
git commit -m "Unity projekt inicializálása a gyökérkönyvtárban"
git push -u origin feature/unity-init
```

---

### B) Forgatókönyv: Frontend + Backend egy repóban (Monorepo)
Ebben az esetben a repository gyökere csak egy gyűjtőhely, a valódi kód két külön mappában (frontend, backend) lakik.

**Előfeltétel:** A Repo már létezik (`main` branch, `README.md` kész).
**Gitignore tipp:** Érdemes egyetlen közös `.gitignore`-t használni a gyökérben, ami mindkettő szemetét szűri (pl. `node_modules/` a frontendnek, `__pycache__/` vagy `venv/` a backendnek).

#### 1. Előkészületek
```bash
git checkout main
git pull
git checkout -b feature/project-skeleton
```

#### 2. Mappaszerkezet kialakítása
Kézzel hozzuk létre a struktúrát a terminálban vagy fájlkezelőben.

```bash
mkdir frontend
mkdir backend
```

#### 3. Backend létrehozása (Példa: Python/Node.js)
Ahelyett, hogy a gyökérbe generálnánk, belelépünk a mappába.

Példa (Node.js backend):
```bash
cd backend
npm init -y
echo "console.log('Backend running')" > index.js
cd ..  # Visszalépés a gyökérbe!
```

#### 4. Frontend létrehozása (Példa: React/Vite)
Szintén belelépünk a mappába, vagy megmondjuk az eszköznek, hova dolgozzon.

Példa (Vite):
```bash
# Itt feltételezzük, hogy a 'frontend' mappa üres
npm create vite@latest frontend -- --template react
# (A fenti parancs a meglévő 'frontend' mappába teszi a fájlokat, ha az üres)
```

#### 5. Ellenőrzés és Commit
**A legfontosabb szabály:** A Git parancsokat mindig a gyökérből adjuk ki! (Onnan, ahol a `.git` mappa van).

Győződj meg róla, hogy a gyökérben vagy (`cd ..` ha kell).

**Ellenőrzés:**
```bash
git status
```
Látnod kell: `backend/index.js`, `frontend/src/...` stb.

**Commit:**
```bash
git add .
git commit -m "Frontend és Backend mappa szerkezet létrehozása"
git push -u origin feature/project-skeleton
```

#### 6. Hogyan dolgozunk ezzel később? (Fontos workflow infó!)
Amikor fejlesztesz, két terminált szokás nyitni:

1. **Terminál (Git kezelés):** Ez mindig a gyökérben marad. Itt adod ki a `git add`, `git commit`, `git push` parancsokat.
2. **Terminál (Futtatás):** Ezzel belelépsz az adott mappába (`cd frontend`), hogy elindítsd a szervert (`npm run dev`), telepíts csomagot (`npm install axios`), stb.

> **Soha ne csinálj `git init`-et az almappákban (backend/frontend)! Csak egy `.git` mappa legyen, a legtetején.**

