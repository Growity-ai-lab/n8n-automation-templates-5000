# 🚀 Blog'dan Sosyal Medyaya AI Otomasyonu - Kurulum Rehberi

## 📋 İçindekiler
1. [Genel Bakış](#genel-bakış)
2. [Özellikler](#özellikler)
3. [Gereksinimler](#gereksinimler)
4. [Adım Adım Kurulum](#adım-adım-kurulum)
5. [Google Sheets Kurulumu](#google-sheets-kurulumu)
6. [Kullanım Örnekleri](#kullanım-örnekleri)
7. [Sorun Giderme](#sorun-giderme)

---

## 🎯 Genel Bakış

Bu n8n workflow'u, blog yazılarınızı **otomatik olarak** sosyal medyaya paylaşır. GPT-4 ile her platform için optimize edilmiş içerik ve hashtagler oluşturur.

### Nasıl Çalışır?

```
Blog Yazısı → GPT-4 İçerik Üretimi → Hashtag Üretimi →
→ LinkedIn + Instagram + Twitter Paylaşımı →
→ Google Sheets Log → Slack Bildirimi
```

### İki Kullanım Modu

1. **📥 Webhook (Manuel Tetikleme)**
   - Blog yayınlandığında webhook'a istek gönderin
   - Anında paylaşım yapılır

2. **⏰ Zamanlanmış (Otomatik)**
   - Her 6 saatte bir Google Sheets'i kontrol eder
   - Bekleyen blogları otomatik paylaşır

---

## ✨ Özellikler

### 🎨 AI-Destekli İçerik Üretimi
- ✅ **LinkedIn**: Profesyonel ve değer odaklı (250-300 kelime)
- ✅ **Instagram**: Duygusal ve ilgi çekici (150-200 kelime)
- ✅ **Twitter/X**: Kısa ve etkili (280 karakter max)

### 🏷️ Akıllı Hashtag Üretimi
- ✅ **LinkedIn**: 3-5 profesyonel hashtag
- ✅ **Instagram**: 15-20 popüler + niche hashtag
- ✅ **Twitter**: 2-3 trending hashtag
- ✅ Türkçe ve İngilizce karma hashtag desteği

### 📊 Yönetim & Takip
- ✅ Google Sheets ile blog kuyruğu yönetimi
- ✅ Paylaşım logları (timestamp, durum, platform başarı durumu)
- ✅ Slack bildirimleri (başarı/hata)
- ✅ Görsel (image) URL desteği
- ✅ Blog URL'si otomatik ekleme

### 🛡️ Hata Yönetimi
- ✅ Her adımda hata yakalama
- ✅ Platform bazlı başarısızlık takibi
- ✅ Slack üzerinden hata bildirimleri
- ✅ Google Sheets'te detaylı log

### 🌐 Çok Dilli Destek
- ✅ Türkçe içerik desteği (varsayılan)
- ✅ İngilizce ve diğer diller

---

## 📦 Gereksinimler

### 1. n8n Kurulumu
- **Self-hosted**: [n8n.io/docs](https://docs.n8n.io/)
- **Cloud**: [n8n.cloud](https://n8n.cloud/) (Önerilen başlangıç için)

### 2. API Keys & Credentials

#### 🤖 OpenAI API Key (Zorunlu)
- Hesap: https://platform.openai.com/
- API Key oluşturun
- GPT-4 erişimi gerekli
- **Maliyet**: ~$0.03-0.05 per blog post

#### 💼 LinkedIn OAuth2 (Zorunlu)
- LinkedIn Developer Portal: https://www.linkedin.com/developers/
- OAuth 2.0 App oluşturun
- Permissions: `w_member_social`, `r_basicprofile`

#### 📸 Instagram OAuth2 (Zorunlu)
- Facebook Developer: https://developers.facebook.com/
- Instagram Graph API
- Business/Creator hesap gerekli
- Permissions: `instagram_basic`, `instagram_content_publish`

#### 🐦 Twitter OAuth1 (Zorunlu)
- Twitter Developer Portal: https://developer.twitter.com/
- OAuth 1.0a App oluşturun
- API v2 erişimi
- Permissions: `Read and Write`

#### 📊 Google Sheets OAuth2 (Opsiyonel ama önerilen)
- Google Cloud Console: https://console.cloud.google.com/
- Google Sheets API enable
- OAuth 2.0 credentials

#### 🔔 Slack Webhook (Opsiyonel)
- Slack App oluşturun
- Incoming Webhooks aktif edin
- Workspace'e yükleyin

---

## 🔧 Adım Adım Kurulum

### Adım 1: n8n Workflow'u İçe Aktarın

1. n8n'i açın
2. Sol menüden **"Workflows"** → **"Import from File"**
3. `generate-social-from-blog-ai-enhanced.json` dosyasını seçin
4. **"Import"** tıklayın

### Adım 2: Credentials Ekleyin

#### OpenAI API

```
1. Settings → Credentials → Add Credential
2. "OpenAI" seçin
3. API Key girin
4. "Save" tıklayın
5. Credential ID: "openai_credential" olarak kaydedin
```

#### LinkedIn OAuth2

```
1. Settings → Credentials → Add Credential
2. "LinkedIn OAuth2 API" seçin
3. Client ID ve Client Secret girin
4. OAuth flow tamamlayın
5. Credential ID: "linkedin_credential"
```

#### Instagram OAuth2

```
1. Settings → Credentials → Add Credential
2. "Instagram OAuth2 API" seçin
3. Facebook App ID ve Secret girin
4. Instagram Business Account bağlayın
5. Credential ID: "instagram_credential"
```

#### Twitter OAuth1

```
1. Settings → Credentials → Add Credential
2. "Twitter OAuth1 API" seçin
3. Consumer Key/Secret ve Access Token/Secret girin
5. Credential ID: "twitter_credential"
```

#### Google Sheets OAuth2 (Opsiyonel)

```
1. Settings → Credentials → Add Credential
2. "Google Sheets OAuth2 API" seçin
3. OAuth flow tamamlayın
4. Credential ID: "google_sheets_credential"
```

#### Slack (Opsiyonel)

```
1. Settings → Credentials → Add Credential
2. "Slack" seçin
3. Webhook URL veya OAuth token girin
4. Credential ID: "slack_credential"
```

### Adım 3: Environment Variables Ayarlayın

n8n Settings → Environment Variables:

```bash
# Google Sheets ID (Opsiyonel)
GOOGLE_SHEET_ID=your_google_sheet_id_here

# Slack Channel (Opsiyonel)
SLACK_CHANNEL=#marketing-automation
```

**Google Sheet ID nasıl bulunur?**
```
Google Sheets URL'sinden:
https://docs.google.com/spreadsheets/d/1ABC123XYZ789/edit

ID: 1ABC123XYZ789
```

### Adım 4: Workflow'u Aktif Edin

1. Workflow'u açın
2. Sağ üst köşede **"Active"** toggle'ı açın
3. ✅ Workflow aktif!

---

## 📊 Google Sheets Kurulumu

### 1. Yeni Google Sheet Oluşturun

Template: [Google Sheets Template Link](#)

### 2. İki Sheet Oluşturun

#### Sheet 1: "BlogQueue"

| A: Blog Title | B: Blog Content | C: Image URL | D: Blog URL | E: Language | F: Status | G: Posted Date |
|---------------|-----------------|--------------|-------------|-------------|-----------|----------------|
| Blog Başlığı 1 | Blog içeriği... | https://... | https://... | tr | pending | |
| Blog Başlığı 2 | Blog içeriği... | https://... | https://... | en | pending | |

**Kolonlar:**
- **Blog Title**: Blog başlığı (zorunlu)
- **Blog Content**: Blog içeriği veya özet (zorunlu, 500+ karakter önerilen)
- **Image URL**: Görsel URL (opsiyonel, Instagram için önemli)
- **Blog URL**: Blog yazısının linki (önerilen)
- **Language**: Dil kodu (tr, en, vb. - varsayılan: tr)
- **Status**: `pending` (bekliyor) veya `completed` (tamamlandı)
- **Posted Date**: Otomatik doldurulur

#### Sheet 2: "PostLog"

| A: Timestamp | B: Blog Title | C: Blog URL | D: LinkedIn | E: Instagram | F: Twitter | G: Status | H: Notes |
|--------------|---------------|-------------|-------------|--------------|------------|-----------|----------|
| 2025-12-02... | Blog 1 | https://... | ✅ Success | ✅ Success | ❌ Failed | completed | Auto-posted |

**Otomatik doldurulur** - Elle düzenleme gerektirmez.

### 3. Sheet'i Paylaşın

1. Google Sheets → Share
2. n8n service account email ekleyin
3. **Editor** yetkisi verin

---

## 🎮 Kullanım Örnekleri

### Kullanım 1: Webhook ile Manuel Tetikleme

#### Webhook URL'i Alın

1. Workflow'u açın
2. "📥 Webhook: New Blog Post" node'una tıklayın
3. **Test URL** veya **Production URL** kopyalayın

Örnek URL:
```
https://your-n8n-instance.com/webhook/blog-to-social-enhanced
```

#### Blog Gönderme (cURL)

```bash
curl -X POST https://your-n8n-instance.com/webhook/blog-to-social-enhanced \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Dijital Pazarlamada Yapay Zeka Kullanımı",
    "content": "Yapay zeka, dijital pazarlama dünyasında devrim yaratıyor. GPT-4 gibi modeller, içerik üretiminden müşteri segmentasyonuna kadar birçok alanda kullanılıyor...",
    "image_url": "https://example.com/images/ai-marketing.jpg",
    "blog_url": "https://yourwebsite.com/blog/ai-in-marketing",
    "language": "tr"
  }'
```

#### WordPress Entegrasyonu

WordPress'te **functions.php**'ye ekleyin:

```php
<?php
// Blog yayınlandığında n8n'e gönder
add_action('publish_post', 'send_to_n8n_social', 10, 2);

function send_to_n8n_social($post_id, $post) {
    // Sadece blog post'ları için
    if ($post->post_type !== 'post') return;

    $webhook_url = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced';

    $data = array(
        'title' => get_the_title($post_id),
        'content' => wp_strip_all_tags($post->post_content),
        'image_url' => get_the_post_thumbnail_url($post_id, 'full'),
        'blog_url' => get_permalink($post_id),
        'language' => 'tr' // veya ICL_LANGUAGE_CODE (WPML)
    );

    wp_remote_post($webhook_url, array(
        'headers' => array('Content-Type' => 'application/json'),
        'body' => json_encode($data),
        'timeout' => 15
    ));
}
?>
```

#### Zapier/Make.com Entegrasyonu

1. **Trigger**: WordPress - New Post
2. **Action**: Webhooks - POST Request
3. **URL**: n8n webhook URL'iniz
4. **Body**: JSON formatında blog verisi

### Kullanım 2: Google Sheets ile Otomatik

#### 1. BlogQueue Sheet'e Blog Ekleyin

| Blog Title | Blog Content | Image URL | Blog URL | Language | Status |
|------------|--------------|-----------|----------|----------|--------|
| Yeni Blog | İçerik... | https://... | https://... | tr | **pending** |

#### 2. Workflow Otomatik Çalışır

- Her **6 saatte** bir Google Sheets'i kontrol eder
- `Status = pending` olan blogları bulur
- Sosyal medyaya paylaşır
- `Status = completed` olarak günceller

#### 3. Zamanlama Ayarı Değiştirme

Workflow'da "⏰ Schedule: Check New Blogs" node'unu düzenleyin:

```json
// Her 6 saat (varsayılan)
"interval": [{ "field": "hours", "hoursInterval": 6 }]

// Her 3 saat
"interval": [{ "field": "hours", "hoursInterval": 3 }]

// Günde 1 kez (sabah 9:00)
"interval": [{ "field": "cronExpression", "cronExpression": "0 9 * * *" }]

// Her gün sabah 9 ve akşam 6
"interval": [{ "field": "cronExpression", "cronExpression": "0 9,18 * * *" }]
```

### Kullanım 3: Zapier/Integromat ile WordPress Otomasyonu

#### Zapier Flow:
```
1. Trigger: WordPress - New Post Published
2. Action: Google Sheets - Add Row (BlogQueue sheet'e)
3. n8n otomatik kontrol edip paylaşır (6 saatte bir)
```

veya

```
1. Trigger: WordPress - New Post Published
2. Action: Webhooks - POST (n8n webhook'a direkt gönder)
```

---

## 🔍 Workflow Detayları

### Node Açıklamaları

#### 1️⃣ **Trigger Nodes**

- **📥 Webhook: New Blog Post**
  - Manuel tetikleme için
  - POST request bekler
  - JSON body: `{title, content, image_url, blog_url, language}`

- **⏰ Schedule: Check New Blogs**
  - Otomatik çalışma (6 saatte bir)
  - Google Sheets'i kontrol eder

#### 2️⃣ **Data Processing Nodes**

- **📊 Read Blog Queue**
  - Google Sheets'ten bekleyen blogları okur
  - Range: `BlogQueue!A2:F`

- **🔍 Filter Pending Blogs**
  - Sadece `status = pending` olanları filtreler

- **⚙️ Extract & Validate Blog Data**
  - Webhook veya Sheets'ten gelen veriyi normalize eder
  - Zorunlu alanları kontrol eder (title, content)

#### 3️⃣ **AI Content Generation Nodes**

- **🤖 AI: Generate Social Content**
  - GPT-4 ile platform-specific içerik üretir
  - Temperature: 0.7 (yaratıcı)
  - Max tokens: 2000
  - Output: JSON {linkedin, instagram, twitter}

- **🏷️ AI: Generate Hashtags**
  - GPT-4 ile platform-specific hashtagler üretir
  - Temperature: 0.5 (dengeli)
  - Max tokens: 500
  - Output: JSON {linkedin: [], instagram: [], twitter: []}

#### 4️⃣ **Merge & Error Handling**

- **🔄 Merge Content & Hashtags**
  - AI çıktılarını birleştirir
  - İçerik + Hashtagler + Blog URL
  - Hata kontrolü yapar

- **❓ Check for Errors**
  - Hata varsa Slack'e bildirim gönderir
  - Hata yoksa sosyal medyaya devam eder

#### 5️⃣ **Social Media Posting Nodes**

- **💼 Post to LinkedIn**
  - Professional post
  - Public visibility
  - `continueOnFail: true` (diğer platformlar etkilenmesin)

- **📸 Post to Instagram**
  - Image ile birlikte post
  - Caption + Hashtags
  - Business account gerekli

- **🐦 Post to Twitter/X**
  - 280 karakter limit
  - URL otomatik kısaltma

#### 6️⃣ **Logging & Notification Nodes**

- **📝 Log to Google Sheets**
  - Her paylaşımı PostLog sheet'e kaydeder
  - Timestamp, başarı durumu, notlar

- **✅ Update Queue Status**
  - BlogQueue sheet'te status'u `completed` yapar
  - Posted date ekler

- **🔔 Slack: Success Notification**
  - Başarılı paylaşım bildirimi
  - Platform bazlı durum raporu

- **🚨 Slack: Error Notification**
  - Hata durumunda bildirim
  - Error mesajı ve blog bilgisi

#### 7️⃣ **Webhook Response Nodes**

- **✅ Webhook Response: Success**
  - HTTP 200 response
  - JSON: {success: true, message, blog_title}

- **❌ Webhook Response: Error**
  - HTTP 500 response
  - JSON: {success: false, message, error}

---

## 📈 Optimizasyon İpuçları

### 1. İçerik Kalitesi İçin

#### Blog İçeriği
- **Minimum**: 300 kelime
- **Optimal**: 500-1000 kelime
- **Maksimum**: 2000 kelime (GPT-4 token limiti)

**Neden?** Daha fazla içerik = GPT-4 daha iyi context anlayışı = Daha kaliteli sosyal medya içeriği

#### Dil Seçimi
```json
{
  "language": "tr"  // Türkçe içerik için
  "language": "en"  // İngilizce içerik için
}
```

GPT-4'e dil belirtmek, hashtag ve ton ayarlamalarında kritik!

### 2. Maliyet Optimizasyonu

#### Token Kullanımı
- **Her blog post**: ~3000-4000 token
- **Maliyet (GPT-4)**: ~$0.03-0.05 per post

#### Maliyet Azaltma
```
1. Blog content'i 1000 kelime ile sınırlayın
2. GPT-3.5-turbo kullanın (10x daha ucuz, biraz daha düşük kalite)
3. Batch işleme: Birden fazla blog'u aynı anda işleyin
```

GPT-3.5-turbo için `modelId` değiştirin:
```json
"modelId": "gpt-3.5-turbo"  // Varsayılan: "gpt-4"
```

### 3. Zamanlama Stratejisi

#### En İyi Paylaşım Zamanları

**LinkedIn**
- Salı-Perşembe, 9:00-11:00
- Salı-Perşembe, 17:00-18:00

**Instagram**
- Pazartesi-Cuma, 11:00-13:00
- Çarşamba, 19:00-21:00

**Twitter**
- Pazartesi-Cuma, 8:00-10:00
- Çarşamba-Perşembe, 21:00-22:00

#### Zamanlama Ayarı

Workflow'u manuel tetiklemek + Zapier Schedule kullanın:

```
Zapier:
1. Trigger: Schedule (Salı-Perşembe 09:00)
2. Action: Google Sheets - Get Rows (status=pending)
3. Action: Webhooks - POST to n8n
```

### 4. A/B Testing

Farklı içerik stilleri deneyin:

#### LinkedIn Prompt Varyasyonları

**Profesyonel & Formal**
```
"Profesyonel bir LinkedIn post yaz. Formal dil kullan.
Data ve istatistikler ekle. CTA güçlü olsun."
```

**Hikaye Anlatımı**
```
"LinkedIn için storytelling tarzında bir post yaz.
Kişisel deneyim ekle. Duygusal bağ kur."
```

**Sorulu Yaklaşım**
```
"LinkedIn için thought-provoking bir post yaz.
İlginç sorular sor. Tartışma başlat."
```

### 5. Hashtag Stratejisi

#### Manuel Hashtag Listesi Ekleyin

Google Sheets'te yeni bir sheet: "HashtagBank"

| Category | LinkedIn | Instagram | Twitter |
|----------|----------|-----------|---------|
| Dijital Pazarlama | #DigitalMarketing, #MarketingTips | #DigitalMarketing, #SocialMedia, #MarketingTips, ... | #DigitalMarketing, #MarketingTips |
| AI & Tech | #ArtificialIntelligence, #Tech | #AI, #Technology, #Innovation, ... | #AI, #Tech |

Workflow'a **hashtag lookup** node ekleyin (opsiyonel):

```javascript
// Category-based hashtag lookup
const category = $json.category || 'general';
const hashtagSheet = $node["Read Hashtag Bank"].json;
const hashtags = hashtagSheet.find(row => row.Category === category);

return [{
  json: {
    ...$ json,
    predefined_hashtags: hashtags
  }
}];
```

---

## 🚨 Sorun Giderme

### Hata 1: "OpenAI API Error: Invalid API Key"

**Çözüm:**
1. OpenAI API key'inizi kontrol edin
2. n8n Credentials → OpenAI → Test connection
3. API key'in aktif ve bakiyeli olduğundan emin olun

### Hata 2: "LinkedIn: Invalid Access Token"

**Çözüm:**
1. LinkedIn OAuth token'ı yenileyin
2. Permissions'ı kontrol edin: `w_member_social`
3. LinkedIn App'in "Verified" olduğundan emin olun

### Hata 3: "Instagram: Media URL Required"

**Çözüm:**
Instagram post için görsel (image_url) zorunludur.

**Workaround**: Varsayılan bir görsel belirleyin:

```javascript
// Instagram için varsayılan görsel
const imageUrl = $json.image_url || 'https://yourwebsite.com/default-blog-image.jpg';
```

### Hata 4: "Twitter: Status is Over 280 Characters"

**Çözüm:**
GPT-4 prompt'unu güncelleyin:

```
"Twitter için MAKSIMUM 250 karakter (emojiler dahil) bir tweet yaz."
```

### Hata 5: "Google Sheets: Range Not Found"

**Çözüm:**
1. Sheet adlarını kontrol edin: `BlogQueue` ve `PostLog` (case-sensitive)
2. Kolon başlıklarının ilk satırda olduğundan emin olun
3. Range'i güncelleyin: `BlogQueue!A2:F` (A1 header)

### Hata 6: "Slack: Channel Not Found"

**Çözüm:**
1. Slack channel adını kontrol edin: `#marketing-automation`
2. Slack App'in channel'a eklendiğinden emin olun
3. Environment variable: `SLACK_CHANNEL=#your-channel`

### Hata 7: "Webhook Timeout"

**Çözüm:**
Webhook timeout değerini artırın:

```
n8n Settings → Workflow Settings → Execution Timeout: 300 seconds
```

### Hata 8: "GPT-4 Rate Limit Exceeded"

**Çözüm:**
1. OpenAI Rate Limits: https://platform.openai.com/account/rate-limits
2. Upgrade to higher tier (Tier 2+)
3. Request batch'leyerek gönderin (delay ekleyin)

**Delay Node Ekleyin:**
```json
{
  "parameters": {
    "amount": 2,
    "unit": "seconds"
  },
  "name": "Wait 2 Seconds",
  "type": "n8n-nodes-base.wait"
}
```

### Debug Modu

Workflow'u test etmek için:

1. **Manual Execution**: Workflow → Execute Workflow
2. **Test Data**:
```json
{
  "title": "Test Blog",
  "content": "Test içeriği. Bu bir test blog yazısıdır. Dijital pazarlama konusunda yeni trendler...",
  "image_url": "https://picsum.photos/1200/630",
  "blog_url": "https://test.com/blog/test",
  "language": "tr"
}
```
3. **Node-by-Node Check**: Her node'un output'unu inceleyin
4. **Error Output**: Hata mesajlarını loglardan okuyun

---

## 📞 Destek & İletişim

### Dokümantasyon
- **n8n Docs**: https://docs.n8n.io/
- **OpenAI API**: https://platform.openai.com/docs
- **LinkedIn API**: https://docs.microsoft.com/en-us/linkedin/
- **Instagram API**: https://developers.facebook.com/docs/instagram-api
- **Twitter API**: https://developer.twitter.com/en/docs

### Community
- **n8n Forum**: https://community.n8n.io/
- **Discord**: https://discord.gg/n8n

### Issues
- GitHub: [n8n-automation-templates issues](https://github.com/your-repo/issues)

---

## 📊 Performans Metrikleri

### Beklenen Süre (Ortalama)

| İşlem | Süre |
|-------|------|
| Webhook → AI Content Generation | ~5-10 saniye |
| AI Content Generation | ~8-15 saniye |
| Hashtag Generation | ~3-5 saniye |
| Social Media Posting (3 platform) | ~5-10 saniye |
| Logging & Notifications | ~2-3 saniye |
| **TOPLAM** | **~23-43 saniye** |

### API Call Limitleri

| Platform | Rate Limit | n8n Handling |
|----------|------------|--------------|
| OpenAI GPT-4 | 10,000 tokens/min | Batch processing |
| LinkedIn | 100 posts/day | Auto-throttle |
| Instagram | 25 posts/user/day | Retry logic |
| Twitter | 300 tweets/3 hours | Queue system |
| Google Sheets | 100 requests/100 seconds | Built-in throttle |

---

## 🎓 İleri Seviye Özelleştirmeler

### 1. Multi-Language Support Ekleme

```javascript
// Language-specific prompts
const prompts = {
  tr: "Türkçe profesyonel bir LinkedIn post yaz...",
  en: "Write a professional LinkedIn post in English...",
  de: "Schreiben Sie einen professionellen LinkedIn-Beitrag auf Deutsch..."
};

const language = $json.language || 'tr';
const systemPrompt = prompts[language];
```

### 2. Custom Brand Voice

GPT-4 prompt'una brand voice ekleyin:

```
"Brand Voice: [Şirket Adı] olarak konuşuyoruz.
Tonumuz: Profesyonel ama samimi, eğlenceli ama bilgilendirici.
Değerlerimiz: İnovasyon, müşteri odaklılık, sürdürülebilirlik.
Asla: Agresif satış, abartılı iddialar, jargon."
```

### 3. Image Generation (DALL-E 3)

Blog için otomatik görsel oluştur:

```json
{
  "parameters": {
    "resource": "image",
    "operation": "generate",
    "model": "dall-e-3",
    "prompt": "={{ 'Professional blog header image for: ' + $json.title }}",
    "size": "1792x1024",
    "quality": "hd"
  },
  "name": "Generate Blog Image (DALL-E 3)",
  "type": "n8n-nodes-base.openAi"
}
```

### 4. Sentiment Analysis

İçeriği analiz edip uygun emojiyi otomatik ekle:

```json
{
  "parameters": {
    "model": "gpt-4",
    "messages": [
      {
        "role": "system",
        "content": "Analyze sentiment: positive, neutral, or negative. Return JSON: {sentiment: 'positive', emoji: '🎉'}"
      },
      {
        "role": "user",
        "content": "{{ $json.content }}"
      }
    ]
  },
  "name": "Sentiment Analysis"
}
```

### 5. Content Calendar Integration

Google Calendar ile entegrasyon:

```
1. BlogQueue'dan tarihleri çek
2. Google Calendar'a event ekle
3. Reminder set et (publish date - 1 day)
4. n8n scheduled trigger ile otomatik paylaş
```

---

## 🏆 Best Practices

### Content Quality
✅ Blog içeriği 500+ kelime olmalı
✅ Açıklayıcı başlık kullanın
✅ Her zaman blog URL ekleyin
✅ Kaliteli görsel kullanın (Instagram için zorunlu)

### Posting Strategy
✅ Prime time'larda paylaşın (schedule kullanın)
✅ Her platform için uygun içerik üretin
✅ Hashtag stratejisi belirleyin
✅ A/B test yapın (farklı promptlar deneyin)

### Monitoring
✅ Google Sheets logları düzenli kontrol edin
✅ Slack bildirimleri aktif tutun
✅ Platform analytics takip edin
✅ Engagement metrics ölçün

### Security
✅ API keys'i güvenli saklayın (environment variables)
✅ Webhook URL'lerini gizli tutun
✅ OAuth tokens'ı düzenli yenileyin
✅ n8n instance'ı SSL ile koruyun

---

## 🎉 Kurulum Tamamlandı!

Artık blog yazılarınızı **tamamen otomatik** olarak sosyal medyaya paylaşabilirsiniz!

### Hızlı Test:

1. Google Sheets → BlogQueue → Yeni satır ekleyin
2. Status: `pending`
3. 6 saat bekleyin VEYA webhook'a manuel istek gönderin
4. Slack'te bildirim alacaksınız 🔔
5. LinkedIn, Instagram, Twitter'da postlarınızı görün 🚀

### Sorularınız mı var?

- 📧 Email: support@yourcompany.com
- 💬 Slack: #marketing-automation
- 📖 Docs: https://docs.yourcompany.com/n8n

---

**Mutlu otomasyonlar! 🎯**
