# Ghid acces — Project Planner

Cine ce poate face și cum modifici accesul. **Doar adminii (Marius, Martin) gestionează accesul.**

## Niveluri de acces

| Nivel | Poate | Cine |
|---|---|---|
| **Admin** | tot + gestionează utilizatorii + vede costuri | Marius, Martin |
| **Editor** | editează planner-ul (proiecte, date, progres) | Marius, Martin |
| **Vizualizare** | doar vede planner-ul + sincronizează ore TT | restul echipei |
| *fără acces* | nimic (ecran „acces interzis") | cine nu e în lista de utilizatori |

**Costurile / profitabilitatea** le văd DOAR adminii — sunt într-o listă SharePoint separată, blocată pe server.

## Cum funcționează (2 straturi)

Securitatea reală e în **SharePoint** (permisiunile pe liste). Codul aplicației doar potrivește interfața. Cele două trebuie să fie de acord:

- **SharePoint** = zidul real: cine poate scrie de fapt.
- **Codul** (`EDITORS` în `index.html`) = ce vede userul (butoane de editare sau read-only).

De-asta, ca să dai **editare**, modifici în ambele locuri. Pentru **vizualizare**, doar în aplicație.

---

## ➕ Acces de VIZUALIZARE (read-only)

Persoana trebuie să aibă deja acces la site-ul SharePoint — adică să fie membru al grupului M365 **„Coordonare proiecte"** (colegii existenți deja sunt). Apoi:

1. Loghează-te ca admin → buton **👥 Utilizatori**.
2. Scrie emailul (`prenume.nume@me-concept.de`) → **+ Adaugă**.

Gata — vede planner-ul, dar nu poate modifica nimic.

> Dacă e cineva complet nou, care nu e pe site: adaugă-l întâi în grupul M365 „Coordonare proiecte" (din Teams/Outlook), ca să capete drept de citire.

## ✏️ Acces de EDITARE

Presupunând că persoana are deja vizualizare (vezi mai sus), două gesturi:

**1. În cod** (`index.html`) — caută linia `const EDITORS = [` și adaugă emailul:
```js
const EDITORS = ['m.poenar@me-concept.de', 'm.mayer@me-concept.de', 'x.nou@me-concept.de'];
```
Commit (se poate direct pe GitHub: editezi `index.html` → Commit changes).

**2. În SharePoint** (zidul real):
- Site `Coordonare proiecte` → lista **ProjectPlannerData** → ribbon **Permissions**.
- **Grant Permissions** → scrie persoana → Show Options → Permission Level = **Edit** → Share.

După ~1 min (deploy GitHub Pages) persoana poate edita. Hard refresh în browser (`Ctrl+Shift+R`).

## ➖ Scoatere editor (îl faci read-only)

Invers:
1. **Cod:** scoate emailul din lista `EDITORS`. Commit.
2. **SharePoint:** ProjectPlannerData → Permissions → bifează persoana → **Edit User Permissions** → **Read** (sau **Remove User Permissions** dacă era grant direct).

## ➖ Scoatere completă (fără acces)

În aplicație: **👥 Utilizatori** → **Elimină** lângă email.

---

## Unde sunt datele (SharePoint)

Site: `/sites/Coordonareproiecte`
- **ProjectPlannerData** — planner + jurnal + lista de utilizatori. Permisiuni: echipa = **Read**, Marius+Martin = **Edit**.
- **ProjectPlannerFinancials** — fee / cost / profit / overhead. Permisiuni: **DOAR Marius+Martin** (moștenire ruptă). ⚠️ Nu atinge moștenirea aici.

Orele lucrate vin din TrackingTime prin proxy (Railway) — nu se scriu în SharePoint.

## Test rapid după o schimbare de acces

- **Editor nou:** persoana → hard refresh → vede butoane de editare, salvează fără erori.
- **Read-only:** vede planner-ul, fără „+ Proiect"/„+ task", fără drag, badge „👁 Doar vizualizare", nicio eroare de salvare.
- **Verificare pe server** (opțional): ProjectPlannerData → Permissions → **Check Permissions** → scrie persoana → vezi `Read` sau `Edit`.

---
*Pentru logica de cod: `index.html` → `isEditor()`, `isCostAdmin()` (`COST_ADMINS`), `isAdmin()` (`ADMIN_USERS`).*
