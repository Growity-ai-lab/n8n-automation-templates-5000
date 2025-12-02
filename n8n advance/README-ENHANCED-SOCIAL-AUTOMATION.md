# 🚀 Blog'dan Sosyal Medyaya AI Otomasyonu (Gelişmiş Versiyon)

> Blog yazılarınızı GPT-4 ile otomatik olarak LinkedIn, Instagram ve Twitter'a paylaşın!

[![n8n](https://img.shields.io/badge/n8n-Workflow-orange)](https://n8n.io/)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4-green)](https://openai.com/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 📋 Hızlı Bakış

Bu n8n workflow'u, blog yazılarınızı **tek tıkla** veya **tamamen otomatik** olarak sosyal medyaya paylaşır.

### ✨ Ne Yapar?

```
Blog Yazısı → GPT-4 İçerik Üretimi → Platform-Specific İçerik →
→ Akıllı Hashtag Üretimi → 3 Platformda Eşzamanlı Paylaşım →
→ Google Sheets Logging → Slack Bildirimi
```

### 🎯 Özellikler

- ✅ **AI-Destekli İçerik**: GPT-4 ile her platform için optimize edilmiş içerik
- ✅ **Akıllı Hashtagler**: Platform bazlı hashtag stratejisi (LinkedIn: 3-5, Instagram: 15-20, Twitter: 2-3)
- ✅ **Çoklu Tetikleme**: Webhook (manuel) veya Schedule (otomatik 6 saatte bir)
- ✅ **Google Sheets Entegrasyonu**: Blog kuyruğu ve log yönetimi
- ✅ **Slack Bildirimleri**: Başarı/hata bildirimleri
- ✅ **Görsel Desteği**: Instagram için image URL desteği
- ✅ **Hata Yönetimi**: Her adımda try-catch ve detaylı logging
- ✅ **Çok Dilli**: Türkçe, İngilizce ve diğer diller
- ✅ **URL Tracking**: Blog URL'sini otomatik ekler

---

## 📦 Paket İçeriği

| Dosya | Açıklama |
|-------|----------|
| `generate-social-from-blog-ai-enhanced.json` | Ana n8n workflow dosyası |
| `SETUP-GUIDE-TURKISH.md` | Detaylı kurulum rehberi (Türkçe) |
| `ENV-SETUP-GUIDE.md` | Environment variables kurulum rehberi |
| `EXAMPLE-USAGE.md` | API örnekleri (cURL, WordPress, Node.js, Python, PHP) |
| `google-sheets-template-blogqueue.csv` | BlogQueue sheet template |
| `google-sheets-template-postlog.csv` | PostLog sheet template |
| `README-ENHANCED-SOCIAL-AUTOMATION.md` | Bu dosya |

---

## ⚡ Hızlı Başlangıç

### 1. Ön Gereksinimler

- [ ] n8n kurulumu (self-hosted veya n8n.cloud)
- [ ] OpenAI API key (GPT-4 erişimi)
- [ ] LinkedIn, Instagram, Twitter hesapları
- [ ] Google Sheets (opsiyonel ama önerilen)
- [ ] Slack workspace (opsiyonel)

### 2. Kurulum (3 Adım)

#### Adım 1: Workflow'u İçe Aktar

```bash
1. n8n → Workflows → Import from File
2. generate-social-from-blog-ai-enhanced.json seçin
3. Import tıklayın
```

#### Adım 2: Credentials Ekle

```
Settings → Credentials:
- OpenAI API
- LinkedIn OAuth2
- Instagram OAuth2
- Twitter OAuth1
- Google Sheets OAuth2 (opsiyonel)
- Slack (opsiyonel)
```

#### Adım 3: Environment Variables

```bash
# n8n Settings → Environment Variables
GOOGLE_SHEET_ID=your_sheet_id
SLACK_CHANNEL=#marketing-automation
```

### 3. İlk Testi Yapın

#### Webhook ile Test (cURL)

```bash
curl -X POST https://your-n8n.com/webhook/blog-to-social-enhanced \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Test Blog",
    "content": "Bu bir test içeriğidir...",
    "blog_url": "https://example.com/blog/test",
    "language": "tr"
  }'
```

✅ **Başarılı!** LinkedIn, Instagram ve Twitter'da postlarınızı göreceksiniz! 🎉

---

## 🎮 Kullanım Modları

### Mod 1: Webhook (Manuel Tetikleme)

Blog yayınlandığında webhook'a POST request gönderin:

**Kullanım Senaryoları:**
- WordPress otomatik entegrasyon
- Custom CMS entegrasyonu
- API-driven blog platformları

**Avantajları:**
- ✅ Anında paylaşım
- ✅ Tam kontrol
- ✅ Event-driven

### Mod 2: Schedule (Otomatik)

n8n her 6 saatte bir Google Sheets'i kontrol eder:

**Kullanım Senaryoları:**
- Toplu blog paylaşımı
- İçerik takvimi yönetimi
- Zamanlanmış paylaşımlar

**Avantajları:**
- ✅ Tamamen otomatik
- ✅ Google Sheets üzerinden yönetim
- ✅ Batch işleme

---

## 📊 Google Sheets Yapısı

### Sheet 1: BlogQueue

Bekleyen blogları yönetin:

| Blog Title | Blog Content | Image URL | Blog URL | Language | Status | Posted Date |
|------------|--------------|-----------|----------|----------|--------|-------------|
| Blog 1 | İçerik... | https://... | https://... | tr | **pending** | |
| Blog 2 | İçerik... | https://... | https://... | en | completed | 2025-12-02 |

### Sheet 2: PostLog

Otomatik log tutar:

| Timestamp | Blog Title | LinkedIn | Instagram | Twitter | Status |
|-----------|------------|----------|-----------|---------|--------|
| 2025-12-02 10:30 | Blog 1 | ✅ Success | ✅ Success | ❌ Failed | completed |

---

## 🤖 AI İçerik Örnekleri

### LinkedIn (Profesyonel)

```
🚀 Dijital Pazarlamada Yapay Zeka Devrimi

AI araçları, pazarlama dünyasını dönüştürüyor. GPT-4 gibi
teknolojiler sayesinde:

✅ Kişiselleştirilmiş içerik üretimi
✅ Akıllı müşteri segmentasyonu
✅ Predictive analytics

Sizin AI stratejiniz ne?

#DigitalMarketing #ArtificialIntelligence #MarketingTech

🔗 https://blog.com/ai-marketing
```

### Instagram (Engaging)

```
AI + Pazarlama = 💥

Yapay zeka artık sadece sci-fi filmlerinde değil,
pazarlama ekiplerinde! 🤖

📊 Veriye dayalı kararlar
🎯 Hyper-personalization
⚡ Otomasyon

Detaylar blogda 👆

#AI #DigitalMarketing #MarketingAutomation #ContentCreation
#SocialMediaMarketing #TechInnovation #FutureOfMarketing
#AITools #MarketingStrategy #GrowthHacking

🔗 Link in bio!
```

### Twitter (Concise)

```
AI pazarlamada game-changer! 🚀

GPT-4 ile içerik üretimi, segmentasyon ve
personalizasyon artık çok daha kolay.

Detaylar: https://blog.com/ai-marketing

#DigitalMarketing #AI
```

---

## 🔌 Entegrasyonlar

### WordPress

`functions.php` dosyanıza ekleyin:

```php
add_action('publish_post', 'send_to_n8n_social', 10, 2);

function send_to_n8n_social($post_id, $post) {
    $webhook_url = 'https://your-n8n.com/webhook/blog-to-social-enhanced';

    wp_remote_post($webhook_url, array(
        'body' => json_encode(array(
            'title' => get_the_title($post_id),
            'content' => wp_strip_all_tags($post->post_content),
            'image_url' => get_the_post_thumbnail_url($post_id),
            'blog_url' => get_permalink($post_id),
            'language' => 'tr'
        )),
        'headers' => array('Content-Type' => 'application/json')
    ));
}
```

### Node.js/Express

```javascript
const axios = require('axios');

app.post('/publish-blog', async (req, res) => {
  await axios.post('https://your-n8n.com/webhook/blog-to-social-enhanced', {
    title: req.body.title,
    content: req.body.content,
    blog_url: req.body.url,
    language: 'tr'
  });
});
```

### Python/Flask

```python
import requests

@app.route('/publish', methods=['POST'])
def publish():
    requests.post('https://your-n8n.com/webhook/blog-to-social-enhanced',
        json={
            'title': request.json['title'],
            'content': request.json['content'],
            'blog_url': request.json['url'],
            'language': 'tr'
        })
```

**Daha fazla örnek:** `EXAMPLE-USAGE.md` dosyasına bakın.

---

## 💰 Maliyet Tahmini

### OpenAI GPT-4

| İşlem | Token | Maliyet |
|-------|-------|---------|
| İçerik Üretimi | ~2500 token | $0.025 |
| Hashtag Üretimi | ~500 token | $0.005 |
| **TOPLAM (per blog)** | ~3000 token | **$0.03** |

**Aylık 100 blog:** ~$3
**Aylık 500 blog:** ~$15

### Platform Rate Limits

| Platform | Limit | Kısıtlama |
|----------|-------|-----------|
| LinkedIn | 100 posts/day | Kullanıcı bazlı |
| Instagram | 25 posts/day | Hesap bazlı |
| Twitter | 300 tweets/3h | Hesap bazlı |

---

## 📈 Performans

### Ortalama İşlem Süresi

| Adım | Süre |
|------|------|
| Webhook → Data Processing | 2-3 saniye |
| GPT-4 Content Generation | 8-15 saniye |
| GPT-4 Hashtag Generation | 3-5 saniye |
| Social Media Posting | 5-10 saniye |
| Logging & Notifications | 2-3 saniye |
| **TOPLAM** | **20-36 saniye** |

---

## 🚨 Sorun Giderme

### Problem 1: "OpenAI API Error"

**Çözüm:**
- API key'i kontrol edin
- GPT-4 erişiminizi doğrulayın
- Bakiye kontrol edin

### Problem 2: "Instagram: Media Required"

**Çözüm:**
Instagram için `image_url` zorunludur. Varsayılan bir görsel belirleyin.

### Problem 3: "Twitter: Over 280 Characters"

**Çözüm:**
GPT-4 prompt'una "MAKSIMUM 250 karakter" ekleyin.

**Daha fazla:** `SETUP-GUIDE-TURKISH.md` → Sorun Giderme bölümü

---

## 🎓 İleri Seviye

### Custom Brand Voice

GPT-4 prompt'una brand personality ekleyin:

```
"Brand Voice: [Şirketiniz] olarak konuşuyoruz.
Tonumuz: Profesyonel ama samimi, eğlenceli ama bilgilendirici.
Değerlerimiz: İnovasyon, müşteri odaklılık, sürdürülebilirlik."
```

### Multi-Language Support

```javascript
const prompts = {
  tr: "Türkçe profesyonel bir post yaz...",
  en: "Write a professional post in English...",
  de: "Schreiben Sie einen professionellen Beitrag..."
};
```

### Image Generation (DALL-E 3)

Blog için otomatik görsel üret:

```json
{
  "model": "dall-e-3",
  "prompt": "Professional blog header for: {{ $json.title }}",
  "size": "1792x1024"
}
```

---

## 📚 Dokümantasyon

| Dosya | Ne İçeriyor |
|-------|-------------|
| `SETUP-GUIDE-TURKISH.md` | Detaylı kurulum, konfigürasyon, best practices |
| `ENV-SETUP-GUIDE.md` | Environment variables, Docker setup, security |
| `EXAMPLE-USAGE.md` | API örnekleri, WordPress/Node.js/Python/PHP kodları |

---

## 🤝 Katkıda Bulunma

Issues ve pull request'ler hoş geldiniz!

1. Fork yapın
2. Feature branch oluşturun (`git checkout -b feature/amazing`)
3. Commit yapın (`git commit -m 'Add amazing feature'`)
4. Push edin (`git push origin feature/amazing`)
5. Pull Request açın

---

## 📝 Lisans

MIT License - Özgürce kullanın, değiştirin, paylaşın!

---

## 🙏 Teşekkürler

- **n8n** - Amazing workflow automation platform
- **OpenAI** - GPT-4 API
- **Community** - Feedback ve contributions

---

## 📞 Destek

- 📖 **Dokümantasyon**: Bu repository'deki MD dosyaları
- 💬 **n8n Community**: https://community.n8n.io/
- 🐛 **Issues**: GitHub Issues
- 📧 **Email**: [Your contact email]

---

## 🎉 Son Söz

Artık blog yazılarınız **tamamen otomatik** olarak LinkedIn, Instagram ve Twitter'da paylaşılıyor!

**Mutlu otomasyonlar! 🚀**

---

## 📊 Changelog

### v1.0.0 (2025-12-02)
- ✨ İlk sürüm
- ✅ GPT-4 content generation
- ✅ AI-powered hashtags
- ✅ LinkedIn, Instagram, Twitter support
- ✅ Google Sheets integration
- ✅ Slack notifications
- ✅ Error handling & logging
- ✅ Multi-language support
- ✅ Image support
- ✅ Webhook & Schedule triggers

---

**Made with ❤️ for Digital Marketers**
