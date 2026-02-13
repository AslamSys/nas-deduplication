# 🧹 Deduplication Engine

## 🔗 Navegação

**[🏠 AslamSys](https://github.com/AslamSys)** → **[📚 _system](https://github.com/AslamSys/_system)** → **[📂 NAS (RPi 5 8GB)](https://github.com/AslamSys/_system/blob/main/hardware/nas/README.md)** → **nas-deduplication**

### Containers Relacionados (nas)
- [nas-brain](https://github.com/AslamSys/nas-brain)
- [nas-file-sync](https://github.com/AslamSys/nas-file-sync)
- [nas-photo-backup](https://github.com/AslamSys/nas-photo-backup)
- [nas-object-storage](https://github.com/AslamSys/nas-object-storage)
- [nas-smb-server](https://github.com/AslamSys/nas-smb-server)
- [nas-backup-manager](https://github.com/AslamSys/nas-backup-manager)
- [nas-media-indexer](https://github.com/AslamSys/nas-media-indexer)

---

**Container:** `deduplication`  
**Stack:** rmlint + Btrfs  
**Propósito:** Detectar e remover duplicatas

---

## 📋 Propósito

Economizar espaço removendo arquivos duplicados. Hash SHA-256, preserva original, remove cópias.

---

## 🎯 Features

- ✅ Hash-based deduplication (SHA-256)
- ✅ Detecta duplicatas exatas
- ✅ Preserva arquivo mais antigo
- ✅ Cria hardlinks (economiza espaço)
- ✅ Scan agendado (semanal)

---

## 🚀 Docker Compose

```yaml
deduplication:
  build: ./deduplication
  environment:
    - SCAN_PATH=/storage
    - SCAN_CRON=0 3 * * 0  # Domingo 3h
  volumes:
    - /hot-storage:/storage/hot
    - /cold-storage:/storage/cold
  deploy:
    resources:
      limits:
        cpus: '0.5'
        memory: 768M
```

---

## 🧪 Código (rmlint)

```bash
#!/bin/bash
# Scan for duplicates

## 🔗 Navegação

**[🏠 AslamSys](https://github.com/AslamSys)** → **[📚 _system](https://github.com/AslamSys/_system)** → **[📂 NAS (RPi 5 8GB)](https://github.com/AslamSys/_system/blob/main/hardware/nas/README.md)** → **nas-deduplication**

### Containers Relacionados (nas)
- [nas-brain](https://github.com/AslamSys/nas-brain)
- [nas-file-sync](https://github.com/AslamSys/nas-file-sync)
- [nas-photo-backup](https://github.com/AslamSys/nas-photo-backup)
- [nas-object-storage](https://github.com/AslamSys/nas-object-storage)
- [nas-smb-server](https://github.com/AslamSys/nas-smb-server)
- [nas-backup-manager](https://github.com/AslamSys/nas-backup-manager)
- [nas-media-indexer](https://github.com/AslamSys/nas-media-indexer)

---
rmlint /storage/hot /storage/cold \
    --size 1M \
    --algorithm sha256 \
    --output=sh:dedup.sh \
    --output=json:dedup.json

# Review duplicates

## 🔗 Navegação

**[🏠 AslamSys](https://github.com/AslamSys)** → **[📚 _system](https://github.com/AslamSys/_system)** → **[📂 NAS (RPi 5 8GB)](https://github.com/AslamSys/_system/blob/main/hardware/nas/README.md)** → **nas-deduplication**

### Containers Relacionados (nas)
- [nas-brain](https://github.com/AslamSys/nas-brain)
- [nas-file-sync](https://github.com/AslamSys/nas-file-sync)
- [nas-photo-backup](https://github.com/AslamSys/nas-photo-backup)
- [nas-object-storage](https://github.com/AslamSys/nas-object-storage)
- [nas-smb-server](https://github.com/AslamSys/nas-smb-server)
- [nas-backup-manager](https://github.com/AslamSys/nas-backup-manager)
- [nas-media-indexer](https://github.com/AslamSys/nas-media-indexer)

---
cat dedup.json | jq '.[] | select(.type=="duplicate_file")'

# Execute (remove duplicates, create hardlinks)

## 🔗 Navegação

**[🏠 AslamSys](https://github.com/AslamSys)** → **[📚 _system](https://github.com/AslamSys/_system)** → **[📂 NAS (RPi 5 8GB)](https://github.com/AslamSys/_system/blob/main/hardware/nas/README.md)** → **nas-deduplication**

### Containers Relacionados (nas)
- [nas-brain](https://github.com/AslamSys/nas-brain)
- [nas-file-sync](https://github.com/AslamSys/nas-file-sync)
- [nas-photo-backup](https://github.com/AslamSys/nas-photo-backup)
- [nas-object-storage](https://github.com/AslamSys/nas-object-storage)
- [nas-smb-server](https://github.com/AslamSys/nas-smb-server)
- [nas-backup-manager](https://github.com/AslamSys/nas-backup-manager)
- [nas-media-indexer](https://github.com/AslamSys/nas-media-indexer)

---
./dedup.sh --dry-run  # Test first
./dedup.sh            # Execute

# Publish stats

## 🔗 Navegação

**[🏠 AslamSys](https://github.com/AslamSys)** → **[📚 _system](https://github.com/AslamSys/_system)** → **[📂 NAS (RPi 5 8GB)](https://github.com/AslamSys/_system/blob/main/hardware/nas/README.md)** → **nas-deduplication**

### Containers Relacionados (nas)
- [nas-brain](https://github.com/AslamSys/nas-brain)
- [nas-file-sync](https://github.com/AslamSys/nas-file-sync)
- [nas-photo-backup](https://github.com/AslamSys/nas-photo-backup)
- [nas-object-storage](https://github.com/AslamSys/nas-object-storage)
- [nas-smb-server](https://github.com/AslamSys/nas-smb-server)
- [nas-backup-manager](https://github.com/AslamSys/nas-backup-manager)
- [nas-media-indexer](https://github.com/AslamSys/nas-media-indexer)

---
curl -X POST http://mordomo-nats:4222/nas.dedup.completed \
  -d "{\"files_removed\": 150, \"space_saved_gb\": 8.5}"
```

---

## 📊 Stats

```yaml
Scan Results:
  - Total files: 50,000
  - Duplicates: 1,200 (2.4%)
  - Space saved: 8.5 GB
  - Time: ~25 minutes
```

---

## 🔄 Changelog

### v1.0.0
- ✅ rmlint integration
- ✅ SHA-256 hashing
- ✅ Weekly cron
