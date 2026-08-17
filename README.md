# 🎮 KahpeClient - Minecraft Thin Client Launcher

Özel Minecraft sunucusu için geliştirilmiş, hafif ve modern Minecraft Launcher. Fabric 1.26.2 üzerinde çalışır ve tüm mod/resource pack'ler GitHub entegrasyonu ile otomatik olarak senkronize edilir.

## ✨ Özellikler

- 🪶 **Çok Hafif**: Sadece 8-15MB boyutunda executable
- 🔒 **İzole Ortam**: Varsayılan `.minecraft` dizini kullanmaz, tüm dosyalar kendi klasöründe
- 🔄 **Otomatik Senkronizasyon**: GitHub manifest'ten mods, shaders, configs otomatik download
- ⚡ **Fabric 1.26.2**: Sadece bu sürümü destekler (hardcoded)
- 📊 **Modern UI**: React + TypeScript frontend
- 🛡️ **SHA256 Verification**: Tüm indirilen dosyaların hash kontrolü
- 🐳 **Docker Ready**: Deployment ve build process'i Docker ile hazır

## 📋 Sistem Gereksinimleri

- **OS**: Windows 10+, macOS 10.13+, Linux
- **RAM**: Minimum 2GB, Önerilen 4GB+
- **Disk**: 2GB+ (Minecraft + modlar)
- **İnternet**: İlk kurulum ve güncellemeler için gerekli

## 🚀 Hızlı Başlangıç

### 1. Kurulum (Kullanıcı için)

1. `KahpeClient.exe` dosyasını indirin ve çalıştırın
2. Launcher otomatik olarak game dosyalarını indirecek (ilk açılış biraz zaman alabilir)
3. "Oyna" butonuna basın ve oyuna başlayın

### 2. Geliştirici Kurulumu

```bash
# Repository'yi clone et
git clone https://github.com/your-org/kahpe-client.git
cd kahpe-client

# Bağımlılıkları yükle
cargo build

# Development mode'da çalıştır
cargo tauri dev
```

### 3. Production Build

```bash
# Release build oluştur (~8-12 MB)
cargo tauri build

# Çıktı: src-tauri/target/release/KahpeClient.exe
```

## 🐳 Docker Kullanımı

### Build Image

```bash
docker build -f docker/Dockerfile -t kahpe-client:latest .
```

### File Server (Opsiyonel - GitHub yerine local dosyalar serve etmek için)

```bash
docker-compose -f docker/docker-compose.yml up file-server
```

## 📝 Manifest Dosyası Formatı

GitHub reposunun root'unda `manifest.json` dosyası oluşturun:

```json
{
  "version": "1.0.0",
  "minecraft_version": "1.26.2",
  "fabric_version": "0.14.21",
  "files": [
    {
      "path": "mods/OptiFine-1.26.2.jar",
      "url": "https://raw.githubusercontent.com/owner/repo/main/mods/OptiFine-1.26.2.jar",
      "sha256": "abc123def456...",
      "size": 5242880,
      "optional": false
    },
    {
      "path": "resourcepacks/MyPack.zip",
      "url": "https://raw.githubusercontent.com/owner/repo/main/resourcepacks/MyPack.zip",
      "sha256": "def456abc123...",
      "size": 10485760,
      "optional": false
    },
    {
      "path": "config/my-config.ini",
      "url": "https://raw.githubusercontent.com/owner/repo/main/config/my-config.ini",
      "sha256": "789abc123def...",
      "size": 1024,
      "optional": true
    }
  ]
}
```

## 📂 Dizin Yapısı

```
%APPDATA%\KahpeClient/
├── .kahpe_config/
│   ├── settings.json
│   └── last_sync.json
├── .kahpe_cache/
│   └── manifest.json
├── game/
│   ├── libraries/
│   ├── assets/
│   ├── versions/fabric-1.26.2/
│   ├── mods/
│   ├── shaders/
│   ├── resourcepacks/
│   ├── config/
│   └── jre/
└── logs/
    └── launcher.log
```

## 🔧 Konfigürasyon

### Launcher Settings (settings.json)

```json
{
  "github_repo_url": "https://github.com/your-org/your-repo",
  "manifest_url": "https://raw.githubusercontent.com/your-org/your-repo/main/manifest.json",
  "player_name": "YourName",
  "java_args": "-Xms2G -Xmx4G",
  "auto_update": true
}
```

### JVM Arguments Özelleştirmesi

Launcher'da `config/settings.json` içerisinde JVM arguments'ları düzenleyebilirsiniz:

```json
{
  "java_args": "-Xms4G -Xmx8G -XX:+UseG1GC"
}
```

## 🔄 GitHub Manifest Oluşturma

### 1. SHA256 Hash Hesapla

```bash
# Windows
certutil -hashfile file.jar SHA256

# Linux/macOS
sha256sum file.jar

# Python
python -m hashlib sha256 < file.jar
```

### 2. manifest.json Oluştur

```bash
# Tüm dosyaları scan et ve manifest oluştur
python scripts/generate_manifest.py
```

## 📖 API Endpoints (IPC Commands)

Frontend'den Rust backend'e çağrılar:

```typescript
// check_game_files
const status = await invoke('check_game_files');

// sync_github_files
await invoke('sync_github_files', {
  manifestUrl: 'https://raw.githubusercontent.com/...'
});

// launch_game
await invoke('launch_game', { playerName: 'MyPlayer' });

// get_launcher_status
const status = await invoke('get_launcher_status');
```

## 🛠️ Geliştirme

### Proje Yapısı

- `src-tauri/src/` - Rust backend kodu
  - `main.rs` - Entry point
  - `config/` - Configuration ve paths
  - `github/` - GitHub integration
  - `download/` - File download ve verification
  - `launcher/` - Minecraft launcher logic
  - `commands.rs` - IPC commands

- `src/` - React frontend
  - `components/` - UI components
  - `hooks/` - Custom React hooks
  - `App.tsx` - Main component

### Testing

```bash
# Rust tests
cargo test

# Tauri dev mode
cargo tauri dev
```

## 📋 Checklist - GitHub Setup

- [ ] Repository oluştur
- [ ] `manifest.json` ekle
- [ ] Mod/resource pack dosyalarını upload et
- [ ] SHA256 hashlerini hesapla ve manifest'te güncelle
- [ ] GitHub Actions workflow oluştur (opsiyonel)
- [ ] Release'i oluştur

## 🐛 Troubleshooting

### "Java Runtime not found"
- JRE dosyalarının manifest'te bulunduğundan emin olun
- Game dizininde `jre/bin/java.exe` dosyası olup olmadığını kontrol et

### "Hash mismatch"
- Manifest'teki SHA256 hash'inin doğru olduğundan emin olun
- Dosyayı yeniden hesapla: `sha256sum file.jar`

### "Connection timeout"
- GitHub'a bağlantıyı kontrol et
- Manifest URL'inin doğru olduğundan emin olun
- Firewall/VPN ayarlarını kontrol et

## 📄 Lisans

Bu proje [LICENSE dosyasında](LICENSE) belirtilen lisansa tabidir.

## 🤝 Destek

Sorunlar veya öneriler için GitHub Issues'da issue açın.

---

**Made with ❤️ for the Minecraft community**
