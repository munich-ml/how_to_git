# 🔁 USB-Workflow mit `git bundle` (Hin- und Rückweg)
Dieser Workflow erlaubt an einem Nebensystem (welches nicht mit dem Git des Hauptsystems verbunden ist) in ein Git Projekt (im Hauptsystem) zu arbeiten.

## 1️⃣ Hauptsystem – Arbeitsbranch erzeugen

```bash
git checkout main
git pull
git checkout -b usb-branch
```

(Optional sauberer Start)

```bash
git commit --allow-empty -m "Start USB branch"
```

---

## 2️⃣ Hauptsystem – Bundle erzeugen

```bash
git bundle create usb-transfer.bundle usb-branch
```

Optional prüfen:

```bash
git bundle verify usb-transfer.bundle
```

Die Datei `usb-transfer.bundle` auf den USB-Stick kopieren.

---

## 3️⃣ Nebensystem – Repository aus Bundle klonen

```bash
git clone usb-transfer.bundle projekt
cd projekt
git checkout usb-branch
```

---

## 4️⃣ Nebensystem – Arbeiten und committen

Beispiel:

```bash
echo "print('USB work')" > main.py
git add main.py
git commit -m "Add main.py from offline system"
```

Weitere Commits sind möglich.

---

## 5️⃣ Nebensystem – Rück-Bundle erzeugen

```bash
git bundle create usb-return.bundle usb-branch
```

Optional prüfen:

```bash
git bundle verify usb-return.bundle
```

Die Datei `usb-return.bundle` auf den USB-Stick kopieren.

---

## 6️⃣ Hauptsystem – Bundle importieren

Wichtig: Das folgende `git fetch` sollte aus dem `main`-Branch heraus ausgeführt werden.

```bash
git checkout main
git fetch /pfad/zum/usb-return.bundle usb-branch:usb-branch
```

Kontrolle:

```bash
git log usb-branch -n 5
```

---

## 7️⃣ Hauptsystem – Merge in main

```bash
git checkout main
git merge usb-branch
```

Optional:

```bash
git branch -d usb-branch
``` 
