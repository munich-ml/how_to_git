# 🔄 Kontinuierliche USB-Synchronisation mit Bare-Repository (empfohlene Profi-Variante)

Ziel: Der USB-Stick enthält ein **Bare-Repository** als zentrales Austausch-Remote.  
Haupt- und Nebensystem pushen/pullen jeweils in dieses Repo.  
Keine Bundles, kein manuelles Ref-Handling, vollständige Branch- und Tag-Synchronisation.

---

# Architektur

```
Hauptsystem  ⇄  USB (bare repo)  ⇄  Nebensystem
```

Das Bare-Repo übernimmt die Rolle eines portablen Git-Servers.

---

## 1️⃣ USB-Stick – Bare-Repository initialisieren (einmalig)

USB-Stick einstecken und ein zentrales Repo erzeugen:

```bash
cd /pfad/zum/usb
git init --bare projekt.git
```

Ergebnis:
```
/usb/projekt.git
```

Ein Bare-Repo hat **kein Arbeitsverzeichnis**, nur Git-Objekte und Refs.

---

## 2️⃣ Hauptsystem – USB-Remote hinzufügen

Im bestehenden Projekt:

```bash
cd /pfad/zum/projekt
git remote add usb /pfad/zum/usb/projekt.git
```

Prüfen:

```bash
git remote -v
```

---

## 3️⃣ Hauptsystem – Initiale vollständige Synchronisation

Alle Branches und Tags auf den USB-Stick pushen:

```bash
git push usb --all
git push usb --tags
```

Jetzt enthält das Bare-Repo den vollständigen Stand.

---

## 4️⃣ Nebensystem – Repository vom USB klonen (einmalig)

USB-Stick einstecken:

```bash
git clone /pfad/zum/usb/projekt.git projekt
cd projekt
```

Optional Remote prüfen:

```bash
git remote -v
```

---

## 5️⃣ Nebensystem – Arbeiten und zurück zum USB pushen

Beispiel:

```bash
git checkout -b feature/offline-x
# Änderungen
git add .
git commit -m "Offline feature implementation"
```

Anschließend zurück zum USB:

```bash
git push origin --all
git push origin --tags
```

(Hinweis: `origin` zeigt hier auf das USB-Bare-Repo.)

---

## 6️⃣ Hauptsystem – Änderungen vom USB holen

USB-Stick wieder am Hauptsystem anschließen:

```bash
git fetch usb --all
git fetch usb --tags
```

Branches anzeigen:

```bash
git branch -r
```

Branch lokal übernehmen:

```bash
git checkout -b feature/offline-x usb/feature/offline-x
```

Oder direkt mergen:

```bash
git checkout main
git merge usb/feature/offline-x
```

---

## 7️⃣ Hauptsystem – Optional zu GitLab pushen

Nach Integration:

```bash
git push origin --all
git push origin --tags
```

---

# 🔁 Wiederholungszyklus (kontinuierlicher Betrieb)

### Auf dem Hauptsystem

```bash
git push usb --all
git push usb --tags
```

→ USB mitnehmen

---

### Auf dem Nebensystem

```bash
git fetch origin --all
# Arbeiten
git push origin --all
git push origin --tags
```

→ USB zurückbringen

---

### Auf dem Hauptsystem

```bash
git fetch usb --all
git merge usb/<branch>
```

---

# ✅ Warum diese Variante technisch überlegen ist

- Kein Bundle-Handling
- Kein manuelles Ref-Mapping
- Vollständige Branch- und Tag-Unterstützung
- Exakt wie ein echter Git-Server
- Beliebig oft wiederholbar
- Robust für langfristige Air-Gap-Entwicklung

---

# 🎯 Best Practice

Setze auf beiden Systemen explizit:

```bash
git config --local user.name "Dein Name"
git config --local user.email deine@email.de
```

Damit bleiben Author-Informationen konsistent.

---

Wenn du möchtest, kann ich dir noch eine Variante mit **automatisierten Sync-Skripten (sync-to-usb.sh / sync-from-usb.sh)** erstellen, damit der Prozess mit jeweils einem einzigen Befehl läuft.
