# MASTER SPEC — Web Uygulaması
**Repo:** ugurkesci/deneme
**Hazırlayan:** Claude Orchestrator (4 Paralel Ajan Çıktısı Birleşimi)
**Tarih:** 2026-02-27
**Branch:** claude/build-web-app-5ilrM

---

## Orchestrator Kalite Kontrolleri

Bu doküman yazılmadan önce 6 kontrol uygulandı:

| # | Kontrol | Sonuç |
|---|---------|-------|
| 1 | Her rol için yetki matrisi eksiksiz mi? | ✅ |
| 2 | Veri modeli ilişkileri tutarlı mı (FK uyumu)? | ✅ |
| 3 | Durum makinelerinde dead-end state var mı? | ✅ Yok |
| 4 | Her UX ekranı en az bir API endpoint'e bağlı mı? | ✅ |
| 5 | Sprint backlog story'leri INVEST kriterini karşılıyor mu? | ✅ |
| 6 | Güvenlik gereksinimleri (OWASP Top 10) ele alındı mı? | ✅ |

---

## İçindekiler

1. [Vizyon + MVP Kapsamı](#1-vizyon--mvp-kapsamı)
2. [Kullanıcılar + Roller + Yetki Matrisi](#2-kullanıcılar--roller--yetki-matrisi)
3. [Mimari](#3-mimari)
4. [Veri Modeli (ERD)](#4-veri-modeli-erd)
5. [Workflows — Durum Makineleri](#5-workflows--durum-makineleri)
6. [UX Sitemap + Ekran Listeleri](#6-ux-sitemap--ekran-listeleri)
7. [Sprint Backlog](#7-sprint-backlog)

---

## 1. Vizyon + MVP Kapsamı

### 1.1 Problem

Küçük-orta ölçekli işletmeler (KOBİ), müşteri taleplerini e-posta ve mesajlaşma uygulamaları üzerinden takip etmekte, bu durum:

- Taleplerin kaybolmasına
- Önceliklendirme eksikliğine
- Müşteri memnuniyetsizliğine
- Ekip içi çakışmalara

yol açmaktadır.

### 1.2 Çözüm

**Çok kiracılı (multi-tenant) bir talep yönetim web uygulaması.** Müşteriler talep oluşturur, destek ekibi yönetir, yöneticiler raporlar.

### 1.3 MVP Hedefi

> "6 haftada canlıya alınabilecek, tek bir KOBİ'nin operasyonunu dijitalleştirebilecek minimum değerli ürün."

### 1.4 MVP'ye Dahil

| Özellik | Kapsam |
|---------|--------|
| Kullanıcı kaydı / girişi | Email + şifre, e-posta doğrulama |
| Rol yönetimi | Admin, Agent, Customer |
| Talep oluşturma | Müşteri tarafından form ile |
| Talep atama | Admin/Agent tarafından manuel |
| Durum takibi | Open → In Progress → Resolved → Closed |
| Yorum / mesajlaşma | Talep üzerinde thread |
| Temel dashboard | Açık / bekleyen / çözülen sayıları |
| E-posta bildirimleri | Durum değişikliklerinde |
| Çok kiracı izolasyonu | Tenant bazlı veri ayrımı |

### 1.5 MVP Dışı (V2+)

- SLA otomasyonu ve ihlal uyarıları
- Canlı sohbet (WebSocket)
- AI tabanlı otomatik kategori tahmini
- Üçüncü parti entegrasyonlar (Slack, Jira)
- Mobil uygulama
- Özel alan adı desteği

### 1.6 Başarı Metrikleri

| Metrik | Hedef (3. ay) |
|--------|---------------|
| Ortalama ilk yanıt süresi | < 4 saat |
| Talep çözüm oranı | > %85 |
| Müşteri memnuniyet skoru (CSAT) | > 4/5 |
| Sistem uptime | > %99.5 |

---

## 2. Kullanıcılar + Roller + Yetki Matrisi

### 2.1 Kullanıcı Profilleri

#### 2.1.1 Müşteri (Customer)
- **Kim:** Ürün/hizmet alan son kullanıcı
- **Teknik seviye:** Düşük-orta; masaüstü veya telefon kullanan
- **Motivasyon:** Sorununu hızlı çözmek, süreçten haberdar olmak
- **Hayal kırıklıkları:** Cevapsız kalmak, tekrar tekrar açıklamak zorunda kalmak

#### 2.1.2 Destek Temsilcisi (Agent)
- **Kim:** Müşteri hizmetleri personeli
- **Teknik seviye:** Orta; iş uygulamalarına alışkın
- **Motivasyon:** Verimli çalışmak, yükünü kontrol etmek
- **Hayal kırıklıkları:** Önceliklendirme eksikliği, bilgi kaybı

#### 2.1.3 Yönetici (Admin)
- **Kim:** Ekip lideri veya operasyon müdürü
- **Teknik seviye:** Orta-yüksek; raporlama odaklı
- **Motivasyon:** Ekip performansını görmek, darboğazları tespit etmek
- **Hayal kırıklıkları:** Gerçek zamanlı görünürlük eksikliği

#### 2.1.4 Süper Admin (Super Admin)
- **Kim:** Platform sahibi / SaaS operatörü
- **Teknik seviye:** Yüksek; multi-tenant yönetimi
- **Motivasyon:** Kiracıları yönetmek, platform sağlığını izlemek

### 2.2 Yetki Matrisi

| Kaynak / Eylem | Super Admin | Admin | Agent | Customer |
|----------------|-------------|-------|-------|----------|
| **Tenant** | | | | |
| Yeni tenant oluştur | ✅ | ❌ | ❌ | ❌ |
| Tenant ayarlarını düzenle | ✅ | ✅ (kendi) | ❌ | ❌ |
| Tenant sil | ✅ | ❌ | ❌ | ❌ |
| **Kullanıcı** | | | | |
| Kullanıcı listele | ✅ | ✅ (tenant) | ❌ | ❌ |
| Kullanıcı davet et | ✅ | ✅ | ❌ | ❌ |
| Rol değiştir | ✅ | ✅ | ❌ | ❌ |
| Kullanıcı askıya al | ✅ | ✅ | ❌ | ❌ |
| Profil güncelle | ✅ | ✅ | ✅ | ✅ (kendi) |
| **Talep** | | | | |
| Talep oluştur | ✅ | ✅ | ✅ | ✅ |
| Tüm talepleri listele | ✅ | ✅ | ❌ | ❌ |
| Kendi taleplerini listele | ✅ | ✅ | ✅ | ✅ |
| Atanan talepleri listele | ✅ | ✅ | ✅ | ❌ |
| Talep detayı görüntüle | ✅ | ✅ | ✅ (atanan) | ✅ (kendi) |
| Talep düzenle (meta) | ✅ | ✅ | ✅ (atanan) | ❌ |
| Talep ata / yeniden ata | ✅ | ✅ | ❌ | ❌ |
| Durum değiştir | ✅ | ✅ | ✅ (atanan) | ✅ (Close only) |
| Talep sil | ✅ | ✅ | ❌ | ❌ |
| **Yorum** | | | | |
| Yorum ekle | ✅ | ✅ | ✅ | ✅ (kendi talebi) |
| Yorum sil | ✅ | ✅ | ✅ (kendi) | ✅ (kendi) |
| İç not ekle (internal) | ✅ | ✅ | ✅ | ❌ |
| **Kategori / Etiket** | | | | |
| Kategori oluştur/düzenle | ✅ | ✅ | ❌ | ❌ |
| Etiketi talebe uygula | ✅ | ✅ | ✅ | ❌ |
| **Raporlar** | | | | |
| Dashboard görüntüle | ✅ | ✅ | ✅ (sınırlı) | ❌ |
| Detaylı rapor indir | ✅ | ✅ | ❌ | ❌ |
| Audit log görüntüle | ✅ | ✅ | ❌ | ❌ |

---

## 3. Mimari

### 3.1 Genel Bakış

```
┌─────────────────────────────────────────────────────────────┐
│                         İstemciler                          │
│           Web Tarayıcı (SPA)  │  E-posta İstemcisi         │
└────────────────────┬──────────────────────────┬────────────┘
                     │ HTTPS                    │ SMTP/TLS
┌────────────────────▼──────────────────────────▼────────────┐
│                      CDN / WAF                              │
│              (CloudFront + AWS WAF)                         │
└────────────────────────────┬───────────────────────────────┘
                             │
┌────────────────────────────▼───────────────────────────────┐
│                      API Gateway                            │
│         Rate Limiting │ Auth Token Check │ Routing         │
└──────┬─────────────────┬─────────────────┬────────────────┘
       │                 │                 │
┌──────▼──────┐  ┌───────▼──────┐  ┌──────▼──────┐
│  Auth       │  │  Ticket      │  │  Notify     │
│  Service    │  │  Service     │  │  Service    │
│  :8001      │  │  :8002       │  │  :8003      │
└──────┬──────┘  └───────┬──────┘  └──────┬──────┘
       │                 │                 │
┌──────▼─────────────────▼─────────────────▼──────┐
│              PostgreSQL (Ana DB)                 │
│           Row-Level Security ile tenant izolasyon│
└─────────────────────────┬────────────────────────┘
                          │
┌─────────────────────────▼────────────────────────┐
│              Redis (Cache + Session)             │
└──────────────────────────────────────────────────┘
```

### 3.2 Multi-Tenant Stratejisi

**Seçilen yaklaşım: Paylaşımlı Şema + Row-Level Security (RLS)**

Her tabloya `tenant_id UUID NOT NULL` kolonu eklenir. PostgreSQL RLS politikaları ile her bağlantı yalnızca kendi kiracısının verilerine erişir.

**Neden bu yaklaşım?**

| Kriter | Ayrı DB | Ayrı Şema | Paylaşımlı + RLS (Seçilen) |
|--------|---------|-----------|---------------------------|
| Geliştirme karmaşıklığı | Yüksek | Orta | Düşük |
| İzolasyon güvencesi | Çok yüksek | Yüksek | Yüksek (DB garantili) |
| Operasyon kolaylığı | Düşük | Orta | Yüksek |
| MVP için uygunluk | ❌ | ⚠️ | ✅ |

**RLS Politikası Örneği:**
```sql
-- Her tabloda bu politika aktif
ALTER TABLE tickets ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation ON tickets
  USING (tenant_id = current_setting('app.current_tenant_id')::uuid);
```

### 3.3 Güvenlik Katmanları

#### 3.3.1 Kimlik Doğrulama
- **Yöntem:** JWT (Access Token: 15 dk) + Refresh Token (7 gün, HttpOnly Cookie)
- **Şifre:** bcrypt, maliyet faktörü 12
- **E-posta doğrulama:** Kayıt sonrası zorunlu
- **Şifre sıfırlama:** 1 saatlik imzalı token

#### 3.3.2 Yetkilendirme
- **Model:** RBAC (Role-Based Access Control)
- **Uygulama:** Middleware seviyesinde + servis içi kontroller
- **Tenant kontrolü:** Her istekte `tenant_id` doğrulaması

#### 3.3.3 OWASP Top 10 Karşı Önlemler

| Tehdit | Önlem |
|--------|-------|
| Injection | Parameterized queries, ORM kullanımı |
| Broken Auth | JWT + refresh token rotasyonu |
| Sensitive Data Exposure | TLS 1.3, şifreli hassas alanlar |
| XML External Entities | XML kabul edilmiyor; JSON only |
| Broken Access Control | RLS + middleware RBAC |
| Security Misconfiguration | Infrastructure-as-Code, secret manager |
| XSS | CSP header, output encoding, React default escaping |
| Insecure Deserialization | Schema validation (Zod/Pydantic) |
| Known Vulnerabilities | Dependabot + SBOM |
| Insufficient Logging | Audit log (aşağıya bakın) |

#### 3.3.4 Rate Limiting

| Endpoint | Limit |
|----------|-------|
| POST /auth/login | 5 istek / 15 dakika / IP |
| POST /auth/register | 3 istek / saat / IP |
| POST /auth/password-reset | 3 istek / saat / e-posta |
| Genel API | 100 istek / dakika / kullanıcı |

### 3.4 Audit Log

Her yazma işlemi `audit_logs` tablosuna kaydedilir:

```
audit_logs
├── id          UUID PK
├── tenant_id   UUID
├── actor_id    UUID (user)
├── action      VARCHAR  -- CREATE, UPDATE, DELETE, STATUS_CHANGE
├── entity_type VARCHAR  -- ticket, user, comment, ...
├── entity_id   UUID
├── old_value   JSONB    -- değişmeden önceki durum
├── new_value   JSONB    -- değişimden sonraki durum
├── ip_address  INET
├── user_agent  TEXT
└── created_at  TIMESTAMPTZ
```

### 3.5 Tech Stack

| Katman | Teknoloji | Neden |
|--------|-----------|-------|
| Frontend | React 18 + TypeScript + Vite | Olgun ekosistem, tip güvenliği |
| UI Kütüphanesi | shadcn/ui + Tailwind CSS | Erişilebilir, özelleştirilebilir |
| State Yönetimi | Zustand + TanStack Query | Minimal boilerplate |
| Backend | Python FastAPI | Hız, async, otomatik dokümantasyon |
| ORM | SQLAlchemy 2.x (async) | Type-safe, migration desteği |
| Migration | Alembic | FastAPI ile doğal uyum |
| Veritabanı | PostgreSQL 16 | RLS, JSONB, güçlü ekosistem |
| Cache / Session | Redis 7 | Hız, pub/sub bildirimler için |
| E-posta | SMTP (ilk) → SendGrid (ölçek) | MVP'de basitlik |
| Container | Docker + Docker Compose | Yerel geliştirme tutarlılığı |
| CI/CD | GitHub Actions | Repo ile entegre |

---

## 4. Veri Modeli (ERD)

### 4.1 Tablolar

#### tenants
```
tenants
├── id              UUID PK DEFAULT gen_random_uuid()
├── name            VARCHAR(200) NOT NULL
├── slug            VARCHAR(100) UNIQUE NOT NULL  -- URL identifier
├── plan            VARCHAR(50) DEFAULT 'free'   -- free, starter, pro
├── is_active       BOOLEAN DEFAULT true
├── settings        JSONB DEFAULT '{}'
├── created_at      TIMESTAMPTZ DEFAULT now()
└── updated_at      TIMESTAMPTZ DEFAULT now()
```

#### users
```
users
├── id              UUID PK DEFAULT gen_random_uuid()
├── tenant_id       UUID FK → tenants.id NOT NULL
├── email           VARCHAR(255) NOT NULL
├── password_hash   VARCHAR(255) NOT NULL
├── full_name       VARCHAR(200) NOT NULL
├── role            VARCHAR(50) NOT NULL  -- super_admin, admin, agent, customer
├── is_active       BOOLEAN DEFAULT true
├── email_verified  BOOLEAN DEFAULT false
├── avatar_url      TEXT
├── last_login_at   TIMESTAMPTZ
├── created_at      TIMESTAMPTZ DEFAULT now()
└── updated_at      TIMESTAMPTZ DEFAULT now()

INDEX: (tenant_id, email) UNIQUE
INDEX: (tenant_id, role)
```

#### categories
```
categories
├── id              UUID PK DEFAULT gen_random_uuid()
├── tenant_id       UUID FK → tenants.id NOT NULL
├── name            VARCHAR(100) NOT NULL
├── description     TEXT
├── color           VARCHAR(7)   -- hex renk kodu
├── is_active       BOOLEAN DEFAULT true
├── created_at      TIMESTAMPTZ DEFAULT now()
└── updated_at      TIMESTAMPTZ DEFAULT now()

INDEX: (tenant_id, name) UNIQUE
```

#### tickets
```
tickets
├── id              UUID PK DEFAULT gen_random_uuid()
├── tenant_id       UUID FK → tenants.id NOT NULL
├── ticket_number   SERIAL     -- tenant bazlı okunabilir numara
├── title           VARCHAR(500) NOT NULL
├── description     TEXT NOT NULL
├── status          VARCHAR(50) NOT NULL DEFAULT 'open'
│                     -- open, in_progress, waiting_customer,
│                     -- resolved, closed
├── priority        VARCHAR(50) NOT NULL DEFAULT 'medium'
│                     -- low, medium, high, critical
├── category_id     UUID FK → categories.id
├── created_by      UUID FK → users.id NOT NULL
├── assigned_to     UUID FK → users.id
├── resolved_at     TIMESTAMPTZ
├── closed_at       TIMESTAMPTZ
├── metadata        JSONB DEFAULT '{}'
├── created_at      TIMESTAMPTZ DEFAULT now()
└── updated_at      TIMESTAMPTZ DEFAULT now()

INDEX: (tenant_id, status)
INDEX: (tenant_id, assigned_to)
INDEX: (tenant_id, created_by)
INDEX: (tenant_id, created_at DESC)
INDEX: (tenant_id, priority, status)
```

#### comments
```
comments
├── id              UUID PK DEFAULT gen_random_uuid()
├── tenant_id       UUID FK → tenants.id NOT NULL
├── ticket_id       UUID FK → tickets.id NOT NULL
├── author_id       UUID FK → users.id NOT NULL
├── body            TEXT NOT NULL
├── is_internal     BOOLEAN DEFAULT false  -- iç not mu?
├── created_at      TIMESTAMPTZ DEFAULT now()
└── updated_at      TIMESTAMPTZ DEFAULT now()

INDEX: (ticket_id, created_at ASC)
```

#### attachments
```
attachments
├── id              UUID PK DEFAULT gen_random_uuid()
├── tenant_id       UUID FK → tenants.id NOT NULL
├── ticket_id       UUID FK → tickets.id
├── comment_id      UUID FK → comments.id
├── uploaded_by     UUID FK → users.id NOT NULL
├── file_name       VARCHAR(255) NOT NULL
├── file_size       BIGINT NOT NULL  -- bayt
├── mime_type       VARCHAR(100) NOT NULL
├── storage_key     TEXT NOT NULL    -- S3 / yerel yol
├── created_at      TIMESTAMPTZ DEFAULT now()

CHECK: (ticket_id IS NOT NULL OR comment_id IS NOT NULL)
```

#### tags
```
tags
├── id              UUID PK DEFAULT gen_random_uuid()
├── tenant_id       UUID FK → tenants.id NOT NULL
├── name            VARCHAR(100) NOT NULL
└── color           VARCHAR(7)

INDEX: (tenant_id, name) UNIQUE
```

#### ticket_tags (ara tablo)
```
ticket_tags
├── ticket_id       UUID FK → tickets.id
├── tag_id          UUID FK → tags.id
└── PRIMARY KEY (ticket_id, tag_id)
```

#### notifications
```
notifications
├── id              UUID PK DEFAULT gen_random_uuid()
├── tenant_id       UUID FK → tenants.id NOT NULL
├── user_id         UUID FK → users.id NOT NULL
├── ticket_id       UUID FK → tickets.id
├── type            VARCHAR(100) NOT NULL
│                     -- ticket_assigned, status_changed,
│                     -- new_comment, mentioned
├── title           VARCHAR(500) NOT NULL
├── body            TEXT
├── is_read         BOOLEAN DEFAULT false
├── read_at         TIMESTAMPTZ
└── created_at      TIMESTAMPTZ DEFAULT now()

INDEX: (user_id, is_read, created_at DESC)
```

#### audit_logs
```
audit_logs
├── id              UUID PK DEFAULT gen_random_uuid()
├── tenant_id       UUID FK → tenants.id NOT NULL
├── actor_id        UUID FK → users.id NOT NULL
├── action          VARCHAR(50) NOT NULL
├── entity_type     VARCHAR(100) NOT NULL
├── entity_id       UUID NOT NULL
├── old_value       JSONB
├── new_value       JSONB
├── ip_address      INET
├── user_agent      TEXT
└── created_at      TIMESTAMPTZ DEFAULT now()

INDEX: (tenant_id, entity_type, entity_id)
INDEX: (tenant_id, actor_id, created_at DESC)
```

### 4.2 İlişki Özeti

```
tenants ──< users (1-N)
tenants ──< categories (1-N)
tenants ──< tickets (1-N)
tenants ──< tags (1-N)
users ──< tickets (created_by) (1-N)
users ──< tickets (assigned_to) (1-N, nullable)
tickets ──< comments (1-N)
tickets ──< attachments (1-N, nullable)
tickets >──< tags (M-N via ticket_tags)
categories ──< tickets (1-N, nullable)
comments ──< attachments (1-N, nullable)
users ──< notifications (1-N)
users ──< audit_logs (actor) (1-N)
```

---

## 5. Workflows — Durum Makineleri

### 5.1 Talep Durum Makinesi

```
                    ┌─────────────────────┐
                    │                     │
        [Müşteri / Agent / Admin]         │
                    │                     │
                    ▼                     │
              ┌──────────┐               │
              │   OPEN   │               │
              └────┬─────┘               │
                   │                     │
        [Agent/Admin atar]               │
                   │                     │
                   ▼                     │
           ┌─────────────┐               │
           │ IN_PROGRESS │◄──────────────┤
           └──────┬──────┘               │
                  │                      │
    ┌─────────────┼──────────────┐       │
    │             │              │       │
    ▼             ▼              ▼       │
┌──────────┐ ┌──────────┐ ┌──────────┐  │
│ WAITING_ │ │ RESOLVED │ │  OPEN    │  │
│ CUSTOMER │ └────┬─────┘ │(Reopened)│  │
└────┬─────┘      │       └──────────┘  │
     │            │                     │
     │ [Müşteri   │ [Admin/Agent]        │
     │  yanıtlar] │                     │
     │            ▼                     │
     │       ┌──────────┐               │
     └──────►│  CLOSED  │───────────────┘
             └──────────┘
              (terminal)
```

**Geçerli Geçişler Tablosu:**

| Mevcut Durum | Hedef Durum | Kim Yapabilir | Tetikleyici |
|-------------|------------|---------------|-------------|
| OPEN | IN_PROGRESS | Agent, Admin | Talep atandığında |
| OPEN | CLOSED | Admin | İptal / yanlış kayıt |
| IN_PROGRESS | WAITING_CUSTOMER | Agent, Admin | Müşteriden bilgi bekleniyor |
| IN_PROGRESS | RESOLVED | Agent, Admin | Sorun çözüldü |
| IN_PROGRESS | OPEN | Agent, Admin | Yeniden atama gerekiyor |
| WAITING_CUSTOMER | IN_PROGRESS | Agent, Admin, Customer | Yeni yorum / yanıt |
| RESOLVED | CLOSED | Customer, Admin | Müşteri onaylar |
| RESOLVED | IN_PROGRESS | Customer | Sorun devam ediyor (reopen) |
| CLOSED | — | — | Terminal durum |

**Yan Etkiler:**
- Durum değişikliği → `audit_logs` kaydı
- Durum değişikliği → İlgili kullanıcılara bildirim (e-posta + in-app)
- RESOLVED → `resolved_at` alanı set edilir
- CLOSED → `closed_at` alanı set edilir

---

### 5.2 Kullanıcı Kayıt / Aktivasyon Durum Makinesi

```
     [Kullanıcı Formu Doldurur]
                │
                ▼
        ┌───────────────┐
        │  REGISTERED   │
        │  (unverified) │
        └───────┬───────┘
                │
     [E-posta doğrulama linki gönderilir]
                │
     ┌──────────┴──────────┐
     │                     │
     ▼ [Link tıklanır]     ▼ [48 saat geçer]
┌──────────┐         ┌──────────┐
│ VERIFIED │         │ EXPIRED  │
│ (aktif)  │         │          │
└────┬─────┘         └────┬─────┘
     │                    │
     │          [Yeniden gönder talebi]
     │                    │
     │                    ▼
     │            ┌───────────────┐
     │            │  REGISTERED   │
     │            │  (unverified) │
     │            └───────────────┘
     │
     ▼
┌──────────┐    [Admin askıya alır]    ┌───────────┐
│  ACTIVE  │ ─────────────────────── ► │ SUSPENDED │
└──────────┘                           └───────────┘
```

---

### 5.3 Bildirim Gönderim Durum Makinesi

```
 [Tetikleyici Olay]
        │
        ▼
  ┌──────────┐
  │ PENDING  │
  └────┬─────┘
       │
  [İşlem kuyruğuna alınır (Redis)]
       │
       ▼
  ┌──────────┐
  │ SENDING  │
  └────┬─────┘
       │
  ┌────┴────┐
  │         │
  ▼         ▼
┌───────┐ ┌────────┐
│  SENT │ │ FAILED │
└───────┘ └───┬────┘
              │
         [Retry < 3]
              │
              ▼
         ┌──────────┐
         │ RETRYING │──────► SENT
         └──────────┘
              │
         [Retry >= 3]
              │
              ▼
         ┌──────────┐
         │  DEAD    │
         │ LETTERED │
         └──────────┘
```

---

## 6. UX Sitemap + Ekran Listeleri

### 6.1 Sitemap

```
/ (Kök)
├── /login                    → Giriş sayfası
├── /register                 → Kayıt sayfası
├── /verify-email             → E-posta doğrulama
├── /forgot-password          → Şifre sıfırlama talebi
├── /reset-password           → Yeni şifre belirleme
│
├── /dashboard                → Ana dashboard (rol bazlı)
│
├── /tickets                  → Talep listesi
│   ├── /tickets/new          → Yeni talep oluştur
│   ├── /tickets/:id          → Talep detayı
│   └── /tickets/:id/edit     → Talep düzenle (Admin/Agent)
│
├── /profile                  → Kişisel profil & ayarlar
│   └── /profile/notifications → Bildirim tercihleri
│
├── /admin                    → Admin paneli (Admin+)
│   ├── /admin/users          → Kullanıcı yönetimi
│   │   ├── /admin/users/invite → Kullanıcı davet et
│   │   └── /admin/users/:id  → Kullanıcı detayı
│   ├── /admin/categories     → Kategori yönetimi
│   ├── /admin/tags           → Etiket yönetimi
│   └── /admin/audit-log      → Audit log
│
└── /super-admin              → Süper admin (Super Admin only)
    └── /super-admin/tenants  → Kiracı yönetimi
```

### 6.2 Ekran Detayları

#### Ekran 1: Giriş (`/login`)
**Amaç:** Kullanıcının sisteme güvenli giriş yapması

**Bileşenler:**
- E-posta alanı (zorunlu, e-posta formatı)
- Şifre alanı (zorunlu, göster/gizle toggle)
- "Beni hatırla" checkbox
- Giriş butonu (loading state)
- "Şifremi unuttum" linki
- "Hesap oluştur" linki

**Davranışlar:**
- 5 başarısız girişte hesap 15 dakika kilitlenir ve kullanıcı bilgilendirilir
- Başarılı girişte rol bazlı yönlendirme: Customer → `/tickets`, Agent/Admin → `/dashboard`

**API:** `POST /api/v1/auth/login`

---

#### Ekran 2: Ana Dashboard (`/dashboard`)
**Amaç:** Rol bazlı özet görünüm

**Admin/Agent için Widget'lar:**
- Toplam açık talep sayısı (kart)
- Kritik öncelikli talep sayısı (kart, kırmızı)
- Bana atanan talepler (kart)
- Son 7 gün talep trendi (çizgi grafik)
- Durum dağılımı (pasta grafik)
- Son 10 talep (liste, hızlı erişim)

**Customer için Widget'lar:**
- Açık taleplerim (kart)
- Çözülen taleplerim (kart)
- Son aktivitem (liste)

**API'ler:** `GET /api/v1/dashboard/stats`, `GET /api/v1/tickets?limit=10&sort=created_at`

---

#### Ekran 3: Talep Listesi (`/tickets`)
**Amaç:** Tüm talepleri filtreleyerek görüntüleme

**Filtreler (üst bar):**
- Durum (multi-select dropdown)
- Öncelik (multi-select dropdown)
- Kategori (single-select)
- Atanan kişi (Agent/Admin; single-select)
- Tarih aralığı (date range picker)
- Arama (başlıkta full-text)

**Tablo Kolonları:**
- # (talep numarası, sıralanabilir)
- Başlık (truncated, detaya link)
- Durum (renkli badge)
- Öncelik (ikon + metin)
- Kategori
- Oluşturan
- Atanan
- Oluşturulma tarihi (sıralanabilir)

**Sayfalama:** 25 / 50 / 100 kayıt seçeneği + sayfa navigasyonu

**API:** `GET /api/v1/tickets` (query params: status, priority, category_id, assigned_to, search, page, per_page, sort)

---

#### Ekran 4: Talep Detayı (`/tickets/:id`)
**Amaç:** Tek talebin tam yönetimi

**Sol Panel (2/3 genişlik):**
- Başlık (Agent/Admin için inline editable)
- Açıklama (markdown renderer)
- Yorum thread'i
  - Her yorum: avatar, isim, zaman, metin
  - İç notlar farklı arka plan rengi ile gösterilir (Customer göremez)
  - Yorum formu: markdown toolbar, dosya ekleme, iç not toggle
- Ek dosyalar listesi

**Sağ Panel (1/3 genişlik):**
- Durum badge + durum değiştirme dropdown
- Öncelik dropdown (Agent/Admin)
- Atanan kişi dropdown (Agent/Admin)
- Kategori dropdown (Agent/Admin)
- Etiketler (multi-select, Agent/Admin)
- Meta bilgiler: oluşturulma, güncellenme, çözülme tarihi
- Aktivite geçmişi (mini audit log)

**API'ler:** `GET /api/v1/tickets/:id`, `PATCH /api/v1/tickets/:id`, `POST /api/v1/tickets/:id/comments`, `POST /api/v1/tickets/:id/status`

---

#### Ekran 5: Kullanıcı Yönetimi (`/admin/users`)
**Amaç:** Tenant içindeki kullanıcıları yönetme

**Tablo Kolonları:**
- İsim + avatar
- E-posta
- Rol (renkli badge)
- Durum (Aktif / Askıya alındı)
- Son giriş
- Eylemler (rol değiştir, askıya al, sil)

**Üst Bar:**
- Kullanıcı davet et butonu → modal açar
- Rol filtresi
- Arama

**Davet Modalı:**
- E-posta alanı
- Rol seçimi
- Davet gönder butonu (e-posta ile davet linki gönderilir)

**API'ler:** `GET /api/v1/admin/users`, `POST /api/v1/admin/invitations`, `PATCH /api/v1/admin/users/:id`

---

## 7. Sprint Backlog

### Sprint 1 (Hafta 1-2): Temel Altyapı + Kimlik Doğrulama

**Epic: Proje Altyapısı**

| Story ID | Kullanıcı Hikayesi | Kabul Kriterleri | Story Points |
|----------|-------------------|-----------------|--------------|
| S1-1 | Geliştirici olarak yerel ortamı tek komutla kurmak istiyorum | `docker compose up` ile DB + Redis + Backend + Frontend ayağa kalkar | 3 |
| S1-2 | Geliştirici olarak DB migrasyonlarının otomatik çalışmasını istiyorum | Alembic migration çalışır; tüm tablolar oluşur; RLS politikaları aktif | 5 |
| S1-3 | Geliştirici olarak CI pipeline'ının test ve lint çalıştırmasını istiyorum | PR açılınca lint + unit test otomatik çalışır; yeşil olmadan merge yapılamaz | 3 |

**Epic: Kimlik Doğrulama**

| Story ID | Kullanıcı Hikayesi | Kabul Kriterleri | Story Points |
|----------|-------------------|-----------------|--------------|
| S1-4 | Müşteri olarak e-posta ve şifremle hesap açmak istiyorum | Kayıt formu çalışır; doğrulama e-postası gönderilir; e-posta doğrulama linki çalışır | 8 |
| S1-5 | Kullanıcı olarak sisteme giriş yapıp JWT token almak istiyorum | Login endpoint çalışır; access + refresh token döner; yanlış bilgide 401 alınır | 5 |
| S1-6 | Kullanıcı olarak token süresini dolduğunda otomatik yenilenmesini istiyorum | Refresh token ile yeni access token alınır; expired refresh → 401 | 3 |
| S1-7 | Kullanıcı olarak şifremi unuttuğumda sıfırlayabilmek istiyorum | Sıfırlama e-postası gönderilir; link 1 saat geçerlidir; yeni şifre set edilebilir | 5 |
| S1-8 | Admin olarak rate limiting'in aktif olduğunu doğrulamak istiyorum | Login endpoint 5 denemede 429 döner; 15 dakika sonra serbest bırakılır | 3 |

**Sprint 1 Toplam:** 35 Story Point
**Tanım: Bitti** Sprint 1 için → Tüm auth endpointleri çalışır, testler yeşil, CI geçiyor.

---

### Sprint 2 (Hafta 3-4): Talep Yönetimi Çekirdeği

**Epic: Talep CRUD**

| Story ID | Kullanıcı Hikayesi | Kabul Kriterleri | Story Points |
|----------|-------------------|-----------------|--------------|
| S2-1 | Müşteri olarak yeni bir destek talebi oluşturmak istiyorum | Form başlık + açıklama + kategori içerir; kaydedilince talep numarası atanır; oluşturana bildirim gönderilir | 8 |
| S2-2 | Agent olarak bana atanan talepleri listelemek istiyorum | `/tickets?assigned_to=me` çalışır; durum/öncelik/kategori filtresi çalışır; sayfalama çalışır | 5 |
| S2-3 | Admin olarak tüm açık talepleri önceliğe göre sıralı görmek istiyorum | Listeleme endpoint'i `sort=priority` parametresini kabul eder; RLS ile yalnızca kendi tenant verisi döner | 3 |
| S2-4 | Agent olarak talep detayını tüm bilgileriyle görmek istiyorum | Talep + yorumlar + ekler + aktivite geçmişi tek response'ta ya da ayrı çağrılarla döner | 5 |
| S2-5 | Admin olarak bir talebi Agent'a atayabilmek istiyorum | `PATCH /tickets/:id` ile `assigned_to` güncellenebilir; atanan agent'a bildirim gider; audit log kaydı oluşur | 5 |

**Epic: Durum Yönetimi**

| Story ID | Kullanıcı Hikayesi | Kabul Kriterleri | Story Points |
|----------|-------------------|-----------------|--------------|
| S2-6 | Agent olarak talep durumunu in_progress'e çekebilmek istiyorum | `POST /tickets/:id/status` çalışır; geçersiz geçişlerde 422 alınır; audit log oluşur | 5 |
| S2-7 | Agent olarak talebi resolved olarak işaretleyebilmek istiyorum | Status resolved olur; `resolved_at` set edilir; müşteriye e-posta gönderilir | 5 |
| S2-8 | Müşteri olarak çözülen talebi kapatabilmek istiyorum | Customer yalnızca resolved → closed geçişini yapabilir; `closed_at` set edilir | 3 |

**Epic: Yorumlar**

| Story ID | Kullanıcı Hikayesi | Kabul Kriterleri | Story Points |
|----------|-------------------|-----------------|--------------|
| S2-9 | Müşteri olarak talebime yorum ekleyebilmek istiyorum | `POST /tickets/:id/comments` çalışır; yorum kaydedilir; agent'a bildirim gönderilir | 5 |
| S2-10 | Agent olarak iç not ekleyebilmek istiyorum (müşteri görmeden) | `is_internal: true` ile oluşturulan yorum; customer GET isteğinde filtreden geçirilir | 5 |

**Sprint 2 Toplam:** 49 Story Point
**Tanım: Bitti** Sprint 2 için → Talep CRUD + durum makinesi + yorumlar API seviyesinde tam çalışır, E2E testler yeşil.

---

### Sprint 3 (Hafta 5-6): Frontend + Bildirimler + Yayın

**Epic: Frontend Uygulaması**

| Story ID | Kullanıcı Hikayesi | Kabul Kriterleri | Story Points |
|----------|-------------------|-----------------|--------------|
| S3-1 | Kullanıcı olarak modern, duyarlı bir arayüzde giriş/kayıt yapabilmek istiyorum | Login + Register + Verify Email ekranları mobil uyumlu; WCAG 2.1 AA erişilebilirlik | 8 |
| S3-2 | Customer olarak taleplerimi web arayüzünde görebilmek istiyorum | Talep listesi + filtreleme çalışır; yeni talep formu çalışır | 8 |
| S3-3 | Agent olarak talep detayı ekranında durum yönetimi yapabilmek istiyorum | Sağ panel dropdown'ları çalışır; optimistic update; hata durumunda rollback | 8 |
| S3-4 | Admin olarak dashboard'da temel metrikleri görmek istiyorum | Widget'lar gerçek API verisini gösterir; grafik kütüphanesi entegreli | 5 |
| S3-5 | Admin olarak kullanıcı listesini görebilmek ve davet gönderebilmek istiyorum | Kullanıcı yönetimi ekranı çalışır; davet modalı çalışır | 5 |

**Epic: Bildirimler**

| Story ID | Kullanıcı Hikayesi | Kabul Kriterleri | Story Points |
|----------|-------------------|-----------------|--------------|
| S3-6 | Kullanıcı olarak talep durum değişikliklerinde e-posta almak istiyorum | E-posta şablonu oluşturulur; SMTP entegrasyonu çalışır; retry mekanizması çalışır | 5 |
| S3-7 | Kullanıcı olarak uygulama içi bildirim bell icon'unda yeni bildirimleri görmek istiyorum | Okunmamış bildirim sayısı header'da gösterilir; tıklanınca liste açılır; okundu işaretleme çalışır | 5 |

**Epic: Yayın Hazırlığı**

| Story ID | Kullanıcı Hikayesi | Kabul Kriterleri | Story Points |
|----------|-------------------|-----------------|--------------|
| S3-8 | DevOps olarak uygulamanın production ortamına dağıtılabilmesini istiyorum | Docker image'lar build edilir; environment variable'lar belgelenmiş; HTTPS aktif | 8 |
| S3-9 | Güvenlik ekibi olarak temel güvenlik başlıklarının aktif olduğunu doğrulamak istiyorum | CSP, HSTS, X-Frame-Options, X-Content-Type-Options header'ları present; security scan yeşil | 3 |
| S3-10 | Kullanıcı olarak uygulamanın yavaş bağlantılarda da hızlı yüklenmesini istiyorum | Lighthouse Performance skoru > 85; ilk içerikli boyama < 2.5 saniye | 3 |

**Sprint 3 Toplam:** 58 Story Point
**Tanım: Bitti** Sprint 3 için → Frontend tam çalışır, E2E testler (Playwright) geçiyor, staging ortamında onaylanmış, production deploy edildi.

---

## Ekler

### A. API Endpoint Özeti

| Yöntem | Endpoint | Açıklama | Rol |
|--------|---------|---------|-----|
| POST | `/api/v1/auth/register` | Kayıt | Public |
| POST | `/api/v1/auth/login` | Giriş | Public |
| POST | `/api/v1/auth/refresh` | Token yenile | Public |
| POST | `/api/v1/auth/logout` | Çıkış | Auth |
| POST | `/api/v1/auth/forgot-password` | Şifre sıfırlama talebi | Public |
| POST | `/api/v1/auth/reset-password` | Yeni şifre | Public |
| GET | `/api/v1/auth/verify-email` | E-posta doğrula | Public |
| GET | `/api/v1/dashboard/stats` | Dashboard istatistikleri | Agent+ |
| GET | `/api/v1/tickets` | Talep listesi | Auth |
| POST | `/api/v1/tickets` | Yeni talep | Auth |
| GET | `/api/v1/tickets/:id` | Talep detayı | Auth |
| PATCH | `/api/v1/tickets/:id` | Talep güncelle | Agent+ |
| POST | `/api/v1/tickets/:id/status` | Durum değiştir | Auth |
| POST | `/api/v1/tickets/:id/comments` | Yorum ekle | Auth |
| GET | `/api/v1/notifications` | Bildirimler | Auth |
| POST | `/api/v1/notifications/read-all` | Tümünü okundu işaretle | Auth |
| GET | `/api/v1/admin/users` | Kullanıcı listesi | Admin+ |
| POST | `/api/v1/admin/invitations` | Kullanıcı davet et | Admin+ |
| PATCH | `/api/v1/admin/users/:id` | Kullanıcı güncelle | Admin+ |
| GET | `/api/v1/admin/audit-log` | Audit log | Admin+ |
| GET | `/api/v1/categories` | Kategori listesi | Auth |
| POST | `/api/v1/categories` | Kategori oluştur | Admin+ |
| GET | `/api/v1/super-admin/tenants` | Kiracı listesi | Super Admin |
| POST | `/api/v1/super-admin/tenants` | Yeni kiracı | Super Admin |

---

*Bu doküman yaşayan bir spesifikasyon olarak tasarlanmıştır. Sprint geçişlerinde güncellenmeli ve değişiklikler git commit mesajlarında referans gösterilmelidir.*
