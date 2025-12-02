# 🔐 Environment Variables Kurulum Rehberi

## n8n Environment Variables Nasıl Ayarlanır?

### Self-Hosted n8n için

#### 1. Docker Compose ile Kurulum

`.env` dosyası oluşturun:

```bash
# n8n temel ayarları
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=your_secure_password

# Webhook URL (production)
WEBHOOK_URL=https://your-domain.com

# Google Sheets
GOOGLE_SHEET_ID=1ABC123XYZ789_your_sheet_id

# Slack
SLACK_CHANNEL=#marketing-automation

# OpenAI
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxx

# n8n veritabanı
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=postgres
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=n8n
DB_POSTGRESDB_USER=n8n
DB_POSTGRESDB_PASSWORD=n8n_password

# Timezone
GENERIC_TIMEZONE=Europe/Istanbul
TZ=Europe/Istanbul
```

#### 2. Docker-Compose.yml

```yaml
version: '3.8'

services:
  n8n:
    image: n8nio/n8n:latest
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=${N8N_BASIC_AUTH_ACTIVE}
      - N8N_BASIC_AUTH_USER=${N8N_BASIC_AUTH_USER}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_BASIC_AUTH_PASSWORD}
      - WEBHOOK_URL=${WEBHOOK_URL}
      - GOOGLE_SHEET_ID=${GOOGLE_SHEET_ID}
      - SLACK_CHANNEL=${SLACK_CHANNEL}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - DB_TYPE=${DB_TYPE}
      - DB_POSTGRESDB_HOST=${DB_POSTGRESDB_HOST}
      - DB_POSTGRESDB_PORT=${DB_POSTGRESDB_PORT}
      - DB_POSTGRESDB_DATABASE=${DB_POSTGRESDB_DATABASE}
      - DB_POSTGRESDB_USER=${DB_POSTGRESDB_USER}
      - DB_POSTGRESDB_PASSWORD=${DB_POSTGRESDB_PASSWORD}
      - GENERIC_TIMEZONE=${GENERIC_TIMEZONE}
      - TZ=${TZ}
    volumes:
      - n8n_data:/home/node/.n8n
    depends_on:
      - postgres

  postgres:
    image: postgres:15-alpine
    restart: always
    environment:
      POSTGRES_DB: ${DB_POSTGRESDB_DATABASE}
      POSTGRES_USER: ${DB_POSTGRESDB_USER}
      POSTGRES_PASSWORD: ${DB_POSTGRESDB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  n8n_data:
  postgres_data:
```

#### 3. Başlatma

```bash
# Docker-compose başlat
docker-compose up -d

# Logları kontrol et
docker-compose logs -f n8n

# Durdur
docker-compose down

# Tamamen sil (volumes dahil)
docker-compose down -v
```

---

### n8n Cloud için

1. **n8n.cloud Dashboard**'a gidin
2. **Settings** → **Environment Variables**
3. Aşağıdaki değişkenleri ekleyin:

```
GOOGLE_SHEET_ID=your_sheet_id_here
SLACK_CHANNEL=#marketing-automation
```

⚠️ **Not**: n8n Cloud'da OpenAI API key gibi sensitive bilgileri **Credentials** olarak saklamalısınız, environment variable olarak değil.

---

## 📋 Gerekli Environment Variables

### GOOGLE_SHEET_ID

**Ne için?** Google Sheets entegrasyonu (BlogQueue ve PostLog)

**Nasıl bulunur?**

Google Sheets URL'sinden ID'yi alın:
```
https://docs.google.com/spreadsheets/d/1ABC123XYZ789/edit#gid=0
                                        ↑
                                   Bu kısım ID
```

**Örnek:**
```bash
GOOGLE_SHEET_ID=1ABC123XYZ789abcDEF456ghiJKL789mnoPQR
```

---

### SLACK_CHANNEL

**Ne için?** Slack bildirimler (başarı/hata)

**Format:**
```bash
# Hashtag ile channel
SLACK_CHANNEL=#marketing-automation

# Veya Channel ID ile
SLACK_CHANNEL=C01234567
```

**Channel ID nasıl bulunur?**
1. Slack → Channel'a sağ tıklayın
2. "View channel details"
3. En altta "Channel ID" göreceksiniz

---

### WEBHOOK_URL (Opsiyonel)

**Ne için?** Production webhook URL'i (self-hosted için)

**Format:**
```bash
WEBHOOK_URL=https://your-n8n-domain.com
```

**Neden gerekli?**
- Test URL'i yerine production URL kullanmak için
- Webhook'ların public erişilebilir olması için

---

### OPENAI_API_KEY (Opsiyonel)

**Ne için?** OpenAI GPT-4 entegrasyonu

**Nasıl alınır?**
1. https://platform.openai.com/
2. **API Keys** → **Create new secret key**
3. Key'i kopyalayın (bir daha göremezsiniz!)

**Format:**
```bash
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

⚠️ **Güvenlik Notu**: Self-hosted için environment variable olarak kullanabilirsiniz, ancak **n8n Cloud için mutlaka Credentials olarak** kaydedin!

---

### GENERIC_TIMEZONE & TZ

**Ne için?** Zamanlama ve log timestamp'leri için timezone

**Format:**
```bash
GENERIC_TIMEZONE=Europe/Istanbul
TZ=Europe/Istanbul
```

**Diğer timezone örnekleri:**
```bash
Europe/London
America/New_York
America/Los_Angeles
Asia/Tokyo
Asia/Dubai
```

**Tam liste:** https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

---

## 🔒 Güvenlik Best Practices

### 1. API Keys'i Asla Commit Etmeyin

`.gitignore` dosyanıza ekleyin:
```
.env
.env.local
.env.production
docker-compose.override.yml
```

### 2. Environment Variables yerine Secrets Kullanın

**Hassas bilgiler için:**
- OpenAI API Key → n8n Credentials
- LinkedIn OAuth → n8n Credentials
- Slack Token → n8n Credentials

**Hassas olmayan bilgiler için:**
- Google Sheet ID → Environment Variable
- Slack Channel → Environment Variable
- Timezone → Environment Variable

### 3. Production'da Strong Passwords

```bash
# Güçlü password generate et
openssl rand -base64 32
```

### 4. SSL/HTTPS Kullanın

Self-hosted n8n için:
```bash
# Let's Encrypt ile SSL
docker run -d \
  --name nginx-proxy \
  -p 80:80 \
  -p 443:443 \
  -v /var/run/docker.sock:/tmp/docker.sock:ro \
  -v ~/certs:/etc/nginx/certs \
  jwilder/nginx-proxy

# n8n'e SSL ekleyin
docker run -d \
  --name n8n \
  -e VIRTUAL_HOST=n8n.yourdomain.com \
  -e LETSENCRYPT_HOST=n8n.yourdomain.com \
  -e LETSENCRYPT_EMAIL=your@email.com \
  n8nio/n8n
```

### 5. Rate Limiting

nginx ile rate limit ekleyin:
```nginx
limit_req_zone $binary_remote_addr zone=n8n_limit:10m rate=10r/s;

server {
    location / {
        limit_req zone=n8n_limit burst=20 nodelay;
        proxy_pass http://n8n:5678;
    }
}
```

---

## 🧪 Test Environment

Development ve production için ayrı environment kullanın:

### .env.development
```bash
N8N_BASIC_AUTH_ACTIVE=false
WEBHOOK_URL=http://localhost:5678
GOOGLE_SHEET_ID=test_sheet_id
SLACK_CHANNEL=#test-automation
```

### .env.production
```bash
N8N_BASIC_AUTH_ACTIVE=true
WEBHOOK_URL=https://n8n.yourdomain.com
GOOGLE_SHEET_ID=production_sheet_id
SLACK_CHANNEL=#marketing-automation
```

**Kullanım:**
```bash
# Development
docker-compose --env-file .env.development up

# Production
docker-compose --env-file .env.production up -d
```

---

## 🐛 Sorun Giderme

### Hata: "Environment variable not found"

**Çözüm 1: Docker Container'ı Restart Edin**
```bash
docker-compose restart n8n
```

**Çözüm 2: Environment Variable'ları Kontrol Edin**
```bash
# Container içindeki env var'ları göster
docker exec n8n env | grep GOOGLE_SHEET_ID
```

**Çözüm 3: Workflow'da `$env` Kullanın**
```javascript
// Yanlış
{{ GOOGLE_SHEET_ID }}

// Doğru
{{ $env.GOOGLE_SHEET_ID }}
```

### Hata: "Cannot read environment variable in workflow"

n8n ayarlarını kontrol edin:

```bash
# Environment variables'ın workflow'larda kullanılmasına izin ver
N8N_BLOCK_ENV_ACCESS_IN_NODE=false
```

Docker-compose.yml'e ekleyin:
```yaml
environment:
  - N8N_BLOCK_ENV_ACCESS_IN_NODE=false
```

---

## 📚 Referanslar

- **n8n Environment Variables Docs**: https://docs.n8n.io/hosting/environment-variables/
- **Docker Compose Docs**: https://docs.docker.com/compose/
- **n8n Cloud Docs**: https://docs.n8n.io/hosting/cloud/

---

## ✅ Checklist

Kurulumu tamamladıktan sonra kontrol edin:

- [ ] `.env` dosyası oluşturuldu
- [ ] `GOOGLE_SHEET_ID` doğru ayarlandı
- [ ] `SLACK_CHANNEL` doğru ayarlandı
- [ ] Timezone ayarlandı
- [ ] Docker-compose.yml güncellendi
- [ ] `.gitignore` içinde `.env` var
- [ ] n8n container başlatıldı
- [ ] Environment variables'lar workflow'da erişilebilir
- [ ] SSL/HTTPS aktif (production için)
- [ ] Backup stratejisi var

---

**Kurulum tamamlandı! 🎉**
