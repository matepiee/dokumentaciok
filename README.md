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
Ha olyan szoftvert használsz (pl. Unity), ami almappát hoz létre:

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
