# Node.js REST API Başlangıç Şablonu

Yeni bir Node.js projesine sıfırdan başlamak yerine, kimlik doğrulama ve yetkilendirme katmanı hazır gelen bir iskeletle başlamak için hazırlanmış bir **boilerplate**. Express üzerine kurulu; rol ve yetki (RBAC) yönetimi, JWT tabanlı kimlik doğrulama, merkezî hata yönetimi, standart API yanıt formatı ve çift katmanlı loglama (konsol + veritabanı denetim kaydı) kutudan çıktığı gibi çalışır durumdadır.

Şablonun ana fikri şudur: **yeni bir iş modülü eklemek için `routes/` klasörüne bir dosya bırakmak yeterlidir.** Router bu klasörü açılışta tarar ve dosya adını URL yolu olarak otomatik mount eder.

![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=node.js&logoColor=white)
![Express](https://img.shields.io/badge/Express-4.16-000000?style=flat&logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-Mongoose-47A248?style=flat&logo=mongodb&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-Auth-000000?style=flat&logo=jsonwebtokens&logoColor=white)
![Winston](https://img.shields.io/badge/Winston-logging-231F20?style=flat)
![License](https://img.shields.io/badge/License-MIT-green?style=flat)

> **Not:** Bu depo, [`modular-node.js-back-end`](https://github.com/db-akyol/modular-node.js-back-end) deposuyla aynı kod tabanını paylaşır. Bu depo şablon/başlangıç noktası olarak, diğeri ise mimarinin ayrıntılı dokümantasyonuyla birlikte tutulmaktadır.

---

## Neler Hazır Geliyor

| Katman | Ne sağlıyor |
|---|---|
| **Kimlik doğrulama** | `POST /api/users/register` ve `POST /api/users/auth` ile kayıt ve JWT token üretimi |
| **Yetkilendirme (RBAC)** | Roller, rol yetkileri ve kullanıcı-rol eşleşmesi için hazır modeller (`Roles`, `RolePrivileges`, `UserRoles`) |
| **Otomatik router keşfi** | `routes/` klasörüne eklenen her `.js` dosyası, adıyla aynı URL yoluna otomatik bağlanır |
| **Standart yanıt formatı** | `lib/Response.js` — tüm uç noktalar aynı başarı/hata gövde yapısını döner |
| **Merkezî hata yönetimi** | `lib/Error.js` ile özel hata sınıfı, `config/Enum.js` ile HTTP kodu sabitleri |
| **Loglama** | `lib/logger/` üzerinden Winston (konsol/dosya) + `lib/AuditLogs.js` ile veritabanına denetim kaydı |
| **Veritabanı** | `db/Database.js` — Mongoose bağlantısını tek noktadan yöneten singleton |

---

## Teknolojiler

| Paket | Rolü |
|---|---|
| Express 4.16 | HTTP sunucusu ve yönlendirme |
| Mongoose | MongoDB ODM |
| jsonwebtoken / passport-jwt | JWT üretimi ve doğrulaması |
| bcrypt | Şifre hash'leme |
| Winston | Yapılandırılmış loglama |
| Morgan | HTTP istek logu |
| dotenv | Ortam değişkeni yönetimi |
| EJS | Hata sayfası şablonu |

---

## Klasör Yapısı

```
api/
├── app.js                  # Express uygulaması, middleware zinciri, hata yakalama
├── bin/www                 # HTTP sunucu girişi
├── config/
│   ├── index.js            # Ortam değişkenlerinin tek noktadan okunması
│   ├── Enum.js             # HTTP kodları ve sabitler
│   └── role_privileges.js  # Sistemdeki yetki tanımları
├── db/
│   ├── Database.js         # Mongoose bağlantı yönetimi
│   └── models/             # Users, Roles, RolePrivileges, UserRoles, Categories, AuditLogs
├── lib/
│   ├── auth.js             # Passport-JWT stratejisi ve yetki kontrolü
│   ├── Response.js         # Standart API yanıt üreticisi
│   ├── Error.js            # Özel hata sınıfı
│   ├── AuditLogs.js        # Veritabanına denetim kaydı yazma
│   └── logger/             # Winston yapılandırması
├── routes/
│   ├── index.js            # Klasörü tarayıp diğer router'ları otomatik mount eder
│   ├── users.js            # Kayıt, giriş, kullanıcı CRUD
│   ├── roles.js            # Rol ve rol yetkisi CRUD
│   ├── categories.js       # Örnek iş modülü (CRUD deseni referansı)
│   └── auditlogs.js        # Denetim kaydı sorgulama
└── views/                  # EJS hata/karşılama sayfaları
```

---

## API Uç Noktaları

Tüm yollar `/api` öneki altındadır.

### Kullanıcılar — `/api/users`

| Metot | Yol | Açıklama |
|---|---|---|
| POST | `/register` | İlk kullanıcı kaydı (süper admin oluşturur) |
| POST | `/auth` | Giriş yapar ve JWT token döner |
| GET | `/` | Kullanıcıları listeler |
| POST | `/add` | Yeni kullanıcı ekler |
| POST | `/update` | Kullanıcı ve rollerini günceller |
| POST | `/delete` | Kullanıcı siler |

### Roller — `/api/roles`

| Metot | Yol | Açıklama |
|---|---|---|
| GET | `/` | Rolleri ve yetkilerini listeler |
| POST | `/add` | Yeni rol oluşturur |
| POST | `/update` | Rolü ve yetkilerini günceller |
| POST | `/delete` | Rol siler |
| GET | `/role_privileges` | Sistemdeki tüm yetki tanımlarını döner |

### Kategoriler — `/api/categories`

| Metot | Yol | Açıklama |
|---|---|---|
| GET | `/` | Kategorileri listeler |
| POST | `/add` | Kategori ekler |
| POST | `/update` | Kategori günceller |
| POST | `/delete` | Kategori siler |

### Denetim Kayıtları — `/api/auditlogs`

| Metot | Yol | Açıklama |
|---|---|---|
| POST | `/` | Denetim kayıtlarını tarih aralığına göre sorgular |

---

## Ortam Değişkenleri

`api/.env.example` dosyasını `api/.env` olarak kopyalayın ve doldurun:

| Değişken | Açıklama |
|---|---|
| `CONNECTION_STRING` | MongoDB bağlantı adresi (örn. `mongodb://localhost:27017/veritabani-adi`) |
| `LOG_LEVEL` | Winston log seviyesi: `error` \| `warn` \| `info` \| `debug` |

> `.env` dosyası `.gitignore` içindedir ve **asla commit edilmemelidir**.

---

## Kurulum ve Kullanım

```bash
# 1. Şablonu klonlayın
git clone https://github.com/db-akyol/nodejs-rest-api-boilerplate.git projenizin-adi
cd projenizin-adi

# 2. Bağımlılıkları kurun
cd api
npm install

# 3. Ortam değişkenlerini hazırlayın
cp .env.example .env
#    .env dosyasını kendi MongoDB adresinizle düzenleyin

# 4. Sunucuyu başlatın
npm start
```

Sunucu varsayılan olarak `http://localhost:3000` üzerinde çalışır.

İlk kullanım için `POST /api/users/register` ile süper admin hesabını oluşturun, ardından `POST /api/users/auth` ile aldığınız token'ı diğer isteklerde `Authorization: Bearer <token>` başlığıyla gönderin.

---

## Yeni Modül Ekleme

Şablonun otomatik router keşfi sayesinde yeni bir kaynak eklemek üç adımdır:

**1. Modeli tanımlayın** — `api/db/models/Products.js`:

```js
const mongoose = require("mongoose");

const schema = new mongoose.Schema({
  name: { type: String, required: true },
  price: { type: Number, required: true }
}, { versionKey: false, timestamps: true });

class Products extends mongoose.Model {}
schema.loadClass(Products);
module.exports = mongoose.model("products", schema);
```

**2. Yetkileri ekleyin** — `api/config/role_privileges.js` içine `product_view`, `product_add`, `product_update`, `product_delete` girdilerini yazın.

**3. Router'ı oluşturun** — `api/routes/products.js`. Dosyayı oluşturmanız yeterlidir; `routes/index.js` klasörü tarar ve bu dosyayı otomatik olarak `/api/products` yoluna bağlar. Mevcut `categories.js` dosyası, `Response` ve `Error` katmanlarının nasıl kullanıldığını gösteren referans örnektir.

---

## Lisans

Bu proje [MIT Lisansı](LICENSE) ile lisanslanmıştır.
