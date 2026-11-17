# Магтаалын баг — тохируулах заавар

Энэ апп Claude-гүйгээр ажиллана. Хэн ч холбоосоор нь орж дуу, жагсаалтыг **үзэх** боломжтой.
**Засварлах** хүмүүс Google (Gmail)-ээр нэвтэрнэ, тэдэнд эзэмшигч эрх олгоно.

Бүгд **үнэгүй**. Нэг удаа ~20–30 минут зарцуулна. Компьютер дээр хийвэл хялбар.

Багцад байгаа файлууд:
- `index.html` — апп өөрөө
- `manifest.json`, `icon-192.png`, `icon-512.png` — утасны нүүр дэлгэцэнд апп шиг харагдуулна
- `firestore.rules` — хэн юу засах эрхтэйг тодорхойлох дүрэм

---

## 1-р алхам. Firebase (өгөгдөл хадгалах газар) үүсгэх

1. https://console.firebase.google.com руу өөрийн Gmail-ээр орно.
2. **Create a project** → нэр өгнө (жишээ: `magtaal-app`) → Google Analytics хэрэггүй (унтраана) → **Create**.
3. Зүүн цэс **Build → Firestore Database → Create database**
   - Location: `asia-east2 (Hong Kong)` эсвэл ойролцоох бүс
   - **Production mode** сонгоно → Create.
4. Firestore дотор **Rules** таб → байгаа бүхнийг устгаад `firestore.rules` файлын агуулгыг буулгана.
   - `tanii.ner@gmail.com` гэснийг **өөрийн Gmail хаягаар** (жижиг үсгээр) солино → **Publish**.
5. Зүүн цэс **Build → Authentication → Get started → Sign-in method → Google → Enable** → өөрийн и-мэйлээ сонгоод **Save**.
6. Дээд зүүн талын ⚙ **Project settings** → доош гүйлгээд **Your apps → `</>` (Web)** дүрс → нэр өгөөд **Register app**.
   - Гарч ирэх `firebaseConfig = { apiKey: ..., authDomain: ..., ... }` хэсгийг хуулж авна.

## 2-р алхам. index.html-д тохиргоо оруулах

`index.html` файлыг текст засварлагчаар (Notepad, VS Code) нээж **«ТОХИРГОО»** гэсэн хэсгийг олно:

```js
const FIREBASE_CONFIG = {
  apiKey: "...",
  authDomain: "....firebaseapp.com",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
const OWNER_EMAIL = "tanii.ner@gmail.com";
```

Хуулсан утгуудаа хашилтан дотор буулгаж, `OWNER_EMAIL`-д өөрийн Gmail-ээ бичээд хадгална.

> 💡 Хэрэв энэ хэсэг төвөгтэй бол `firebaseConfig`-оо Claude-д буулгаад «оруулж өгөөч» гэхэд бэлэн файл гаргаж өгнө.
> (Эдгээр утга нууц биш — вэб апп бүрт ил байдаг. Аюулгүй байдлыг `firestore.rules` хангана.)

## 3-р алхам. Интернэтэд байршуулах (GitHub Pages, үнэгүй)

1. https://github.com дээр бүртгэл үүсгэнэ (байхгүй бол).
2. Баруун дээд **+ → New repository** → нэр: `magtaal` → **Public** → **Create repository**.
3. **uploading an existing file** холбоос дээр дарж 5 файлаа (index.html, manifest.json, icon-192.png, icon-512.png, README.md) чирж оруулаад **Commit changes**.
4. Repository-ийн **Settings → Pages** → Source: **Deploy from a branch**, Branch: **main / (root)** → **Save**.
5. 1–2 минутын дараа хаяг гарна: `https://ТАНЫ-НЭР.github.io/magtaal/`

## 4-р алхам. Нэвтрэлтийг зөвшөөрөх

Firebase → **Authentication → Settings → Authorized domains → Add domain** → `ТАНЫ-НЭР.github.io` нэмнэ.
(Үүнийг хийхгүй бол Google-ээр нэвтрэхэд алдаа гарна.)

## 5-р алхам. Ашиглаж эхлэх

1. Хаягаа нээж баруун дээд **Нэвтрэх** → өөрийн Gmail (эзэмшигч) → одоо бүгдийг засах боломжтой.
2. **Баг** таб → доод хэсэг **«Засах эрхтэй хүмүүс»** → засварлах хүмүүсийн Gmail-ийг нэмнэ.
3. Холбоосоо багийнхандаа явуулна. Үзэхэд нэвтрэх шаардлагагүй.
4. Утасны нүүр дэлгэцэнд нэмэх:
   - iPhone: Safari → Share ⬆️ → **Add to Home Screen**
   - Android: Chrome → ⋮ → **Add to Home screen / Install app**

## Эрхийн тойм

| | Нэвтрээгүй хүн | Нэвтэрсэн, эрхгүй | Засах эрхтэй | Эзэмшигч |
|---|---|---|---|---|
| Дуу, жагсаалт үзэх, Удирдах горим | ✓ | ✓ | ✓ | ✓ |
| Жагсаалт үүсгэх, засах (жагсаалт доторх дууг засах орно) | | | ✓ | ✓ |
| Дууны санд дуу нэмэх, засах | | | | ✓ |
| Багийн гишүүд (утас) харах | | | ✓ | ✓ |
| Гишүүд нэмэх, засах, баг үүсгэх | | | | ✓ |
| Засах эрх олгох | | | | ✓ |

## Зурагнаас таних (заавал биш)

Энэ функцэд Claude API түлхүүр хэрэгтэй (төлбөртэй, нэг дуу ~хэдэн цент).
https://console.anthropic.com → API Keys → Create key → апп дээрх ⚙ **Тохиргоо**-нд оруулна.
Түлхүүр зөвхөн тухайн төхөөрөмж дээр хадгалагдана — хэнд ч бүү дамжуулаарай.

## Дараа нь засвар оруулах

GitHub дээрх `index.html`-ийг шинэ хувилбараар солиход (Add file → Upload files) апп шинэчлэгдэнэ. Өгөгдөл (дуунууд) Firebase-д хадгалагдсан тул устахгүй.
