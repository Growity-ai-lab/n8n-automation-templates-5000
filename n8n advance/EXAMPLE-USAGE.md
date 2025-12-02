# 🎯 Kullanım Örnekleri ve API Requests

## 📋 İçindekiler
1. [cURL ile Webhook Kullanımı](#curl-ile-webhook-kullanımı)
2. [WordPress Entegrasyonu](#wordpress-entegrasyonu)
3. [Node.js/JavaScript](#nodejsjavascript)
4. [Python](#python)
5. [PHP](#php)
6. [Zapier/Make.com](#zapiermakecom)
7. [Google Sheets Formula](#google-sheets-formula)

---

## 🌐 cURL ile Webhook Kullanımı

### Temel Kullanım

```bash
curl -X POST https://your-n8n-instance.com/webhook/blog-to-social-enhanced \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Blog Başlığınız",
    "content": "Blog içeriğiniz buraya...",
    "image_url": "https://example.com/image.jpg",
    "blog_url": "https://yourwebsite.com/blog/post-slug",
    "language": "tr"
  }'
```

### Başarılı Response

```json
{
  "success": true,
  "message": "Blog successfully queued for social media posting",
  "blog_title": "Blog Başlığınız"
}
```

### Hata Response

```json
{
  "success": false,
  "message": "Error processing blog",
  "error": "Missing required field: title"
}
```

---

### Gerçek Blog Örneği

```bash
curl -X POST https://your-n8n-instance.com/webhook/blog-to-social-enhanced \
  -H "Content-Type: application/json" \
  -d '{
    "title": "2025 Dijital Pazarlama Trendleri",
    "content": "Dijital pazarlama dünyası sürekli değişiyor. 2025 yılında AI, video content ve personalization ön planda olacak. İşte dikkat etmeniz gereken 10 trend:\n\n1. AI-Powered Content Creation: ChatGPT ve benzeri araçlar içerik üretimini demokratikleştiriyor.\n2. Short-Form Video Dominance: TikTok, Reels, Shorts formatları engagement için kritik.\n3. Voice Search Optimization: Alexa, Siri optimizasyonu artık zorunluluk.\n4. Privacy-First Marketing: Cookie-less dünyada first-party data stratejileri.\n5. Social Commerce: Instagram Shop, TikTok Shop doğrudan satış kanalları.\n\nDaha fazlası için tam blogu okuyun!",
    "image_url": "https://images.unsplash.com/photo-1460925895917-afdab827c52f?w=1200",
    "blog_url": "https://yourwebsite.com/blog/2025-dijital-pazarlama-trendleri",
    "language": "tr"
  }'
```

---

### Batch Request (Birden Fazla Blog)

```bash
#!/bin/bash

# Blog listesi
blogs=(
  '{"title":"Blog 1","content":"Content 1...","blog_url":"https://example.com/1","language":"tr"}'
  '{"title":"Blog 2","content":"Content 2...","blog_url":"https://example.com/2","language":"tr"}'
  '{"title":"Blog 3","content":"Content 3...","blog_url":"https://example.com/3","language":"en"}'
)

# Her blog için request gönder
for blog in "${blogs[@]}"
do
  echo "Sending: $blog"
  curl -X POST https://your-n8n-instance.com/webhook/blog-to-social-enhanced \
    -H "Content-Type: application/json" \
    -d "$blog"

  # Rate limiting için 5 saniye bekle
  sleep 5
done
```

---

## 🔌 WordPress Entegrasyonu

### functions.php - Otomatik Paylaşım

```php
<?php
/**
 * Blog yayınlandığında otomatik olarak n8n'e gönder
 */

add_action('publish_post', 'send_blog_to_n8n_social', 10, 2);

function send_blog_to_n8n_social($post_id, $post) {
    // Sadece blog post'ları için (custom post type'ları dahil etmek için değiştirin)
    if ($post->post_type !== 'post') {
        return;
    }

    // Revision'ları skip et
    if (wp_is_post_revision($post_id)) {
        return;
    }

    // n8n webhook URL
    $webhook_url = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced';

    // Blog verilerini hazırla
    $blog_content = wp_strip_all_tags($post->post_content);

    // Excerpt varsa kullan, yoksa ilk 500 karakteri al
    if (!empty($post->post_excerpt)) {
        $blog_content = $post->post_excerpt . "\n\n" . $blog_content;
    }

    // Maksimum 2000 karakter (GPT-4 token limiti için)
    $blog_content = substr($blog_content, 0, 2000);

    // Featured image URL
    $image_url = get_the_post_thumbnail_url($post_id, 'full');
    if (!$image_url) {
        // Varsayılan görsel
        $image_url = 'https://yourwebsite.com/wp-content/uploads/default-blog-image.jpg';
    }

    // Dil tespiti (WPML veya Polylang kullanıyorsanız)
    $language = 'tr'; // Varsayılan

    // WPML için
    if (defined('ICL_LANGUAGE_CODE')) {
        $language = ICL_LANGUAGE_CODE;
    }

    // Polylang için
    if (function_exists('pll_get_post_language')) {
        $language = pll_get_post_language($post_id);
    }

    // Data payload
    $data = array(
        'title' => get_the_title($post_id),
        'content' => $blog_content,
        'image_url' => $image_url,
        'blog_url' => get_permalink($post_id),
        'language' => $language,
        'author' => get_the_author_meta('display_name', $post->post_author),
        'categories' => wp_get_post_categories($post_id, array('fields' => 'names')),
        'tags' => wp_get_post_tags($post_id, array('fields' => 'names'))
    );

    // n8n'e gönder
    $response = wp_remote_post($webhook_url, array(
        'headers' => array('Content-Type' => 'application/json'),
        'body' => json_encode($data),
        'timeout' => 30,
        'blocking' => false // Asenkron gönder (WordPress yavaşlamasın)
    ));

    // Hata logla (opsiyonel)
    if (is_wp_error($response)) {
        error_log('n8n webhook error: ' . $response->get_error_message());
    }

    // Admin bildirimi (opsiyonel)
    update_post_meta($post_id, '_n8n_social_sent', current_time('mysql'));
}

/**
 * Admin'de n8n durumunu göster
 */
add_action('post_submitbox_misc_actions', 'show_n8n_social_status');

function show_n8n_social_status() {
    global $post;

    $sent_time = get_post_meta($post->ID, '_n8n_social_sent', true);

    if ($sent_time) {
        echo '<div class="misc-pub-section">';
        echo '<span class="dashicons dashicons-share" style="color: #46b450;"></span> ';
        echo 'Sosyal medyaya gönderildi: ' . $sent_time;
        echo '</div>';
    }
}

/**
 * Manuel gönderim için Quick Action ekle
 */
add_filter('post_row_actions', 'add_n8n_social_quick_action', 10, 2);

function add_n8n_social_quick_action($actions, $post) {
    if ($post->post_status === 'publish' && $post->post_type === 'post') {
        $url = wp_nonce_url(
            admin_url('admin-post.php?action=send_to_n8n&post_id=' . $post->ID),
            'send_to_n8n_' . $post->ID
        );

        $actions['send_to_n8n'] = sprintf(
            '<a href="%s">%s</a>',
            $url,
            __('Sosyal Medyaya Gönder')
        );
    }

    return $actions;
}

// Manuel gönderim handler
add_action('admin_post_send_to_n8n', 'handle_manual_n8n_send');

function handle_manual_n8n_send() {
    $post_id = isset($_GET['post_id']) ? intval($_GET['post_id']) : 0;

    if (!$post_id || !wp_verify_nonce($_GET['_wpnonce'], 'send_to_n8n_' . $post_id)) {
        wp_die('Invalid request');
    }

    $post = get_post($post_id);
    send_blog_to_n8n_social($post_id, $post);

    wp_redirect(admin_url('edit.php?n8n_sent=1'));
    exit;
}
?>
```

---

## 📜 Node.js/JavaScript

### Basit Kullanım

```javascript
const axios = require('axios');

const webhookUrl = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced';

async function sendBlogToSocial(blogData) {
  try {
    const response = await axios.post(webhookUrl, {
      title: blogData.title,
      content: blogData.content,
      image_url: blogData.imageUrl,
      blog_url: blogData.url,
      language: blogData.language || 'tr'
    }, {
      headers: {
        'Content-Type': 'application/json'
      },
      timeout: 30000 // 30 saniye timeout
    });

    console.log('✅ Success:', response.data);
    return response.data;
  } catch (error) {
    console.error('❌ Error:', error.response?.data || error.message);
    throw error;
  }
}

// Kullanım örneği
const blogPost = {
  title: 'Yapay Zeka ve Dijital Pazarlama',
  content: 'AI araçları dijital pazarlamada devrim yaratıyor...',
  imageUrl: 'https://example.com/ai-marketing.jpg',
  url: 'https://myblog.com/ai-digital-marketing',
  language: 'tr'
};

sendBlogToSocial(blogPost);
```

### Express.js API Endpoint

```javascript
const express = require('express');
const axios = require('axios');
const app = express();

app.use(express.json());

const N8N_WEBHOOK = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced';

// Blog yayınlama endpoint'i
app.post('/api/publish-blog', async (req, res) => {
  try {
    const { title, content, image_url, blog_url, language } = req.body;

    // Validation
    if (!title || !content) {
      return res.status(400).json({
        error: 'Title and content are required'
      });
    }

    // Blog'u database'e kaydet (örnek)
    // await saveBlogToDatabase(req.body);

    // n8n'e sosyal medya paylaşımı için gönder
    const socialResponse = await axios.post(N8N_WEBHOOK, {
      title,
      content,
      image_url,
      blog_url,
      language: language || 'tr'
    }, {
      timeout: 30000
    });

    res.json({
      success: true,
      message: 'Blog published and queued for social media',
      social_status: socialResponse.data
    });
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Next.js API Route

```javascript
// pages/api/publish-to-social.js
import axios from 'axios';

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  try {
    const { title, content, image_url, blog_url, language } = req.body;

    const response = await axios.post(
      process.env.N8N_WEBHOOK_URL,
      {
        title,
        content,
        image_url,
        blog_url,
        language: language || 'tr'
      },
      {
        headers: { 'Content-Type': 'application/json' },
        timeout: 30000
      }
    );

    res.status(200).json(response.data);
  } catch (error) {
    console.error('n8n webhook error:', error);
    res.status(500).json({
      error: 'Failed to queue for social media',
      details: error.response?.data || error.message
    });
  }
}
```

---

## 🐍 Python

### Basit Kullanım

```python
import requests
import json

def send_blog_to_social(title, content, image_url=None, blog_url=None, language='tr'):
    """
    Blog'u n8n webhook'a gönder
    """
    webhook_url = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced'

    payload = {
        'title': title,
        'content': content,
        'image_url': image_url or '',
        'blog_url': blog_url or '',
        'language': language
    }

    try:
        response = requests.post(
            webhook_url,
            json=payload,
            headers={'Content-Type': 'application/json'},
            timeout=30
        )

        response.raise_for_status()

        print('✅ Success:', response.json())
        return response.json()

    except requests.exceptions.RequestException as e:
        print('❌ Error:', str(e))
        if hasattr(e.response, 'text'):
            print('Response:', e.response.text)
        raise

# Kullanım örneği
if __name__ == '__main__':
    send_blog_to_social(
        title='Python ile Otomasyon',
        content='Python, otomasyon için güçlü bir dil. Bu yazıda n8n entegrasyonu göstereceğiz...',
        image_url='https://example.com/python-automation.jpg',
        blog_url='https://myblog.com/python-automation',
        language='tr'
    )
```

### Django View

```python
# views.py
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt
from django.views.decorators.http import require_http_methods
import requests
import json

@csrf_exempt
@require_http_methods(["POST"])
def publish_to_social(request):
    """
    Blog'u sosyal medyaya paylaş
    """
    try:
        data = json.loads(request.body)

        # Validation
        if not data.get('title') or not data.get('content'):
            return JsonResponse({
                'error': 'Title and content are required'
            }, status=400)

        # n8n webhook'a gönder
        webhook_url = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced'

        payload = {
            'title': data['title'],
            'content': data['content'],
            'image_url': data.get('image_url', ''),
            'blog_url': data.get('blog_url', ''),
            'language': data.get('language', 'tr')
        }

        response = requests.post(
            webhook_url,
            json=payload,
            timeout=30
        )

        response.raise_for_status()

        return JsonResponse({
            'success': True,
            'message': 'Queued for social media posting',
            'n8n_response': response.json()
        })

    except requests.exceptions.RequestException as e:
        return JsonResponse({
            'error': 'Failed to queue for social media',
            'details': str(e)
        }, status=500)

    except json.JSONDecodeError:
        return JsonResponse({
            'error': 'Invalid JSON'
        }, status=400)
```

### Flask API

```python
from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

N8N_WEBHOOK = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced'

@app.route('/api/publish-to-social', methods=['POST'])
def publish_to_social():
    data = request.get_json()

    if not data.get('title') or not data.get('content'):
        return jsonify({'error': 'Title and content required'}), 400

    try:
        response = requests.post(
            N8N_WEBHOOK,
            json={
                'title': data['title'],
                'content': data['content'],
                'image_url': data.get('image_url', ''),
                'blog_url': data.get('blog_url', ''),
                'language': data.get('language', 'tr')
            },
            timeout=30
        )

        response.raise_for_status()

        return jsonify({
            'success': True,
            'data': response.json()
        })

    except Exception as e:
        return jsonify({
            'error': str(e)
        }), 500

if __name__ == '__main__':
    app.run(debug=True, port=5000)
```

---

## 🐘 PHP

### Basit Kullanım

```php
<?php
function sendBlogToSocial($title, $content, $imageUrl = '', $blogUrl = '', $language = 'tr') {
    $webhookUrl = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced';

    $data = array(
        'title' => $title,
        'content' => $content,
        'image_url' => $imageUrl,
        'blog_url' => $blogUrl,
        'language' => $language
    );

    $options = array(
        'http' => array(
            'header'  => "Content-Type: application/json\r\n",
            'method'  => 'POST',
            'content' => json_encode($data),
            'timeout' => 30
        )
    );

    $context  = stream_context_create($options);
    $result = file_get_contents($webhookUrl, false, $context);

    if ($result === FALSE) {
        throw new Exception('Failed to send to n8n webhook');
    }

    return json_decode($result, true);
}

// Kullanım
try {
    $response = sendBlogToSocial(
        'PHP ile Otomasyon',
        'PHP ve n8n ile blog paylaşımı otomasyonu...',
        'https://example.com/php-automation.jpg',
        'https://myblog.com/php-automation',
        'tr'
    );

    echo "✅ Success: " . json_encode($response);
} catch (Exception $e) {
    echo "❌ Error: " . $e->getMessage();
}
?>
```

### Laravel Controller

```php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;

class SocialMediaController extends Controller
{
    public function publishToSocial(Request $request)
    {
        $request->validate([
            'title' => 'required|string|max:500',
            'content' => 'required|string',
            'image_url' => 'nullable|url',
            'blog_url' => 'nullable|url',
            'language' => 'nullable|string|size:2'
        ]);

        try {
            $response = Http::timeout(30)->post(
                config('services.n8n.webhook_url'),
                [
                    'title' => $request->title,
                    'content' => $request->content,
                    'image_url' => $request->image_url ?? '',
                    'blog_url' => $request->blog_url ?? '',
                    'language' => $request->language ?? 'tr'
                ]
            );

            if ($response->successful()) {
                return response()->json([
                    'success' => true,
                    'message' => 'Queued for social media posting',
                    'data' => $response->json()
                ]);
            }

            return response()->json([
                'success' => false,
                'error' => 'Failed to queue for social media'
            ], 500);

        } catch (\Exception $e) {
            return response()->json([
                'success' => false,
                'error' => $e->getMessage()
            ], 500);
        }
    }
}
```

---

## ⚡ Zapier/Make.com

### Zapier Zap

**Trigger:** WordPress - New Post Published

**Action:** Webhooks by Zapier - POST

**Setup:**
```
URL: https://your-n8n-instance.com/webhook/blog-to-social-enhanced
Payload Type: JSON
Data:
{
  "title": {{post_title}},
  "content": {{post_content_plain}},
  "image_url": {{post_featured_image}},
  "blog_url": {{post_url}},
  "language": "tr"
}
Headers:
Content-Type: application/json
```

### Make.com (Integromat) Scenario

**Module 1:** WordPress - Watch Posts

**Module 2:** HTTP - Make a Request

**Configuration:**
```
URL: https://your-n8n-instance.com/webhook/blog-to-social-enhanced
Method: POST
Headers:
  Content-Type: application/json
Body:
{
  "title": "{{1.title.rendered}}",
  "content": "{{1.content.rendered}}",
  "image_url": "{{1.featured_media}}",
  "blog_url": "{{1.link}}",
  "language": "tr"
}
```

---

## 📊 Google Sheets Formula

### Apps Script ile Otomatik Gönderim

Google Sheets → Extensions → Apps Script:

```javascript
function sendBlogToN8N(row) {
  const sheet = SpreadsheetApp.getActiveSheet();
  const data = sheet.getRange(row, 1, 1, 6).getValues()[0];

  const [title, content, imageUrl, blogUrl, language, status] = data;

  // Sadece pending olanları gönder
  if (status !== 'pending') return;

  const webhookUrl = 'https://your-n8n-instance.com/webhook/blog-to-social-enhanced';

  const payload = {
    title: title,
    content: content,
    image_url: imageUrl || '',
    blog_url: blogUrl || '',
    language: language || 'tr'
  };

  const options = {
    method: 'post',
    contentType: 'application/json',
    payload: JSON.stringify(payload),
    muteHttpExceptions: true
  };

  try {
    const response = UrlFetchApp.fetch(webhookUrl, options);
    const result = JSON.parse(response.getContentText());

    if (result.success) {
      // Status'u completed yap
      sheet.getRange(row, 6).setValue('completed');
      sheet.getRange(row, 7).setValue(new Date());

      Logger.log('✅ Success: Row ' + row);
    } else {
      Logger.log('❌ Error: ' + result.message);
    }
  } catch (error) {
    Logger.log('❌ Exception: ' + error);
  }
}

// Tüm pending blogları gönder
function sendAllPendingBlogs() {
  const sheet = SpreadsheetApp.getActiveSheet();
  const lastRow = sheet.getLastRow();

  for (let i = 2; i <= lastRow; i++) {
    sendBlogToN8N(i);
    Utilities.sleep(2000); // 2 saniye bekle (rate limiting)
  }
}

// Trigger: Her saat çalışsın
function createHourlyTrigger() {
  ScriptApp.newTrigger('sendAllPendingBlogs')
    .timeBased()
    .everyHours(1)
    .create();
}
```

---

## 🧪 Test Etme

### Postman Collection

```json
{
  "info": {
    "name": "n8n Social Media Automation",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Send Blog to Social Media",
      "request": {
        "method": "POST",
        "header": [
          {
            "key": "Content-Type",
            "value": "application/json"
          }
        ],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"title\": \"Test Blog Post\",\n  \"content\": \"This is a test blog content for social media automation...\",\n  \"image_url\": \"https://picsum.photos/1200/630\",\n  \"blog_url\": \"https://example.com/blog/test-post\",\n  \"language\": \"tr\"\n}"
        },
        "url": {
          "raw": "https://your-n8n-instance.com/webhook/blog-to-social-enhanced",
          "protocol": "https",
          "host": ["your-n8n-instance", "com"],
          "path": ["webhook", "blog-to-social-enhanced"]
        }
      }
    }
  ]
}
```

---

## 📝 Best Practices

### 1. Error Handling

Tüm implementasyonlarda:
- ✅ Timeout ayarlayın (30 saniye önerilir)
- ✅ Try-catch kullanın
- ✅ Hataları logla
- ✅ Retry logic ekleyin (opsiyonel)

### 2. Rate Limiting

Batch gönderimlerinde:
- ✅ Request'ler arasında 2-5 saniye bekleyin
- ✅ Aynı anda max 5-10 request gönderin
- ✅ Queue sistemi kullanın

### 3. Content Optimization

- ✅ Blog içeriği: 500-2000 karakter
- ✅ Image URL: Always provide for Instagram
- ✅ Blog URL: Always include for tracking

### 4. Monitoring

- ✅ Response'ları logla
- ✅ Success/failure oranlarını takip edin
- ✅ Slack notifications aktif tutun

---

**Happy Automating! 🚀**
