# Backup Whatsapp database *(without root)*

Dependencies: `adb`, `wget`, `java`

The procedure involves installing an old version of Whatsapp (particularly, 2.11.431) that is known to allow backing up the messages database in clear text, without requiring root access to the `/data` partition.

### 1. Connect phone via ADB

Check via `adb devices`.

### 2. Backup current version

```bash
adb shell am force-stop com.whatsapp
adb pull /data/app/com.whatsapp-1/base.apk current.apk
```

### 3. Uninstall current version

```bash
adb shell pm uninstall -k com.whatsapp
```

### 4. Download and install legacy version

```bash
wget https://www.cdn.whatsapp.net/android/2.11.431/WhatsApp.apk
# OR
wget https://whatcrypt.com/WhatsApp-2.11.431.apk

adb install -r -d WhatsApp.apk
```

### 5. Backup and Extract Whatsapp databases

```bash
adb backup -f whatsapp.ab com.whatsapp
```

Press "proceed" on the smartphone, then wait for completion.

```bash
wget https://github.com/nelenkov/android-backup-extractor/releases/download/20210409062249-e30cc24/abe.jar
java -jar abe.jar unpack whatsapp.ab whatsapp.tar
```

The relevant file is **`database/msgstore.db`**.

### 5. Reinstall current version

```bash
adb install -r -d current.apk
```

---

#### Sources

- https://github.com/YuvrajRaghuvanshiS/WhatsApp-Key-Database-Extractor
- https://github.com/nelenkov/android-backup-extractor