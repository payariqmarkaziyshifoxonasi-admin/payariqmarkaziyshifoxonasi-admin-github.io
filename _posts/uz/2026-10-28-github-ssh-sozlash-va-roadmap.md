---
title: "GitHub SSH sozlash va loyihani joylashtirish yo‘l xaritasi"
date: 2026-10-28 08:30:00 +0500
categories: [Dasturlash, GitHub]
tags: [github, git, ssh, wsl, jekyll, chirpy, loyiha]
---

## Maqsad

Ushbu maqolada Payariq Markaziy Shifoxonasi sisadmini veb-sayti loyihasini WSL muhitida ishlab chiqish, GitHub bilan SSH orqali ulash va mavjud fayllarni xavfsiz tarzda repository'ga joylashtirish jarayoni qayd etiladi.

Asosiy maqsad — mavjud Jekyll Chirpy loyihasini saqlab qolish, GitHub bilan barqaror ishlashni yo‘lga qo‘yish va keyingi ishlab chiqish bosqichlarini izchil hujjatlashtirish.

## 1. Loyiha haqida

**GitHub repository:**

`git@github.com:payariqmarkaziyshifoxonasi-admin/payariqmarkaziyshifoxonasi-admin-github.io.git`

**Lokal loyiha papkasi:**

`/mnt/c/Users/Admin/Desktop/Markaziy Shifoxona/Sayt`

**GitHub repository nusxasi:**

`/mnt/c/Users/Admin/Desktop/Markaziy Shifoxona/Sayt-github`

Loyiha Jekyll va Chirpy asosida tashkil etilgan. Mavjud loyiha fayllari GitHub repository'ga ulanishdan oldin lokal kompyuterda yaratilgan.

## 2. SSH kalitini yaratish

GitHub bilan SSH orqali autentifikatsiya qilish uchun loyiha uchun alohida Ed25519 kaliti yaratildi.

WSL terminalida bajarilgan buyruq:

```bash
ssh-keygen -t ed25519 \
  -C "payariqmarkaziyshifoxonasi@gmail.com" \
  -f ~/.ssh/id_ed25519_payariq
```

Kalit yaratish jarayonida parol belgilandi. Bu parol shaxsiy SSH kalitini qo‘shimcha himoya qilish uchun ishlatiladi.

Natijada ikkita fayl yaratildi:

- `~/.ssh/id_ed25519_payariq` — maxfiy SSH kaliti.
- `~/.ssh/id_ed25519_payariq.pub` — ochiq SSH kaliti.

**Xavfsizlik qoidasi:** maxfiy kalitni hech qachon GitHub repository'ga, ochiq maqolaga yoki boshqa ommaviy manbaga joylashtirmaslik kerak.

## 3. Ochiq kalitni GitHub'ga qo‘shish

Ochiq kalit mazmuni quyidagi buyruq yordamida ko‘rildi:

```bash
cat ~/.ssh/id_ed25519_payariq.pub
```

Olingan ochiq kalit GitHub hisobining SSH keys bo‘limiga qo‘shildi.

Kalit GitHub'da quyidagi nom bilan saqlandi:

`sysadmin roadmap`

GitHub ko‘rsatgan fingerprint:

`SHA256:N1pQ7hOeB0P8XgxY+suN2jJ/pE4r0POTh0ijAxm66pg`

Fingerprint kalitni aniqlash uchun ishlatiladi. U maxfiy kalitning o‘rnini bosmaydi.

## 4. SSH ulanishini tekshirish

Yangi kalit orqali GitHub bilan autentifikatsiya tekshirildi:

```bash
ssh -T -i ~/.ssh/id_ed25519_payariq git@github.com
```

Tekshiruv muvaffaqiyatli yakunlandi. GitHub quyidagi hisob orqali autentifikatsiya qilinganini tasdiqladi:

`payariqmarkaziyshifoxonasi-admin`

GitHub SSH orqali shell xizmatini taqdim etmaydi. Shu sababli muvaffaqiyatli autentifikatsiyadan keyin shell mavjud emasligi haqidagi xabar odatiy holat hisoblanadi.

## 5. SSH kalitlarining zaxira nusxasi

Kalitlarni boshqa kompyuterda ham ishlatish va zaxira nusxasini saqlash maqsadida ikkala kalit fayli arxivlandi.

Lokal arxiv nomi:

`payariq-ssh-backup.zip`

Arxiv loyiha papkasida yaratilgan.

**Muhim xavfsizlik eslatmalari:**

- Ushbu arxiv maxfiy SSH kalitini o‘z ichiga oladi.
- Arxivni ommaviy GitHub repository'ga yuklash mumkin emas.
- Arxiv parolini hech kimga oshkor qilmaslik kerak.
- `zip -e` orqali yaratilgan arxiv an’anaviy ZipCrypto shifrlashidan foydalanadi; bu zamonaviy AES-256 shifrlashiga teng emas.
- Zaxira nusxasini faqat ishonchli va himoyalangan joyda saqlash kerak.

Arxivni boshqa kompyuterda ishlatishdan oldin kalit fayllarining ruxsatlarini va SSH konfiguratsiyasini tekshirish zarur.

## 6. GitHub repository'ni clone qilish

Mavjud lokal loyihani xavfsiz saqlash uchun GitHub repository alohida papkaga clone qilindi.

Bajarilgan buyruq:

```bash
cd "/mnt/c/Users/Admin/Desktop/Markaziy Shifoxona"

GIT_SSH_COMMAND="ssh -i ~/.ssh/id_ed25519_payariq -o IdentitiesOnly=yes" \
git clone git@github.com:payariqmarkaziyshifoxonasi-admin/payariqmarkaziyshifoxonasi-admin-github.io.git Sayt-github
```

SSH kalit paroli so‘ralgach, autentifikatsiya muvaffaqiyatli yakunlandi va repository nusxasi yaratildi.

Alohida papkadan foydalanish mavjud `Sayt` loyihasini tasodifan o‘zgartirib yuborish xavfini kamaytiradi.

## 7. Repository holatini tekshirish

Clone qilingan repository ichida quyidagi buyruqlar bajarildi:

```bash
cd "/mnt/c/Users/Admin/Desktop/Markaziy Shifoxona/Sayt-github"

git status
git remote -v
ls -la
```

Tekshiruv natijalari:

- Joriy branch: `main`.
- Lokal branch `origin/main` bilan sinxron.
- Ishchi katalog toza: `working tree clean`.
- Remote manzil GitHub repository'ga to‘g‘ri ulangan.
- Repository tarkibida `.gitignore`, `LICENSE` va `README.md` mavjud.

Mavjud `README.md` faylini o‘zgartirmaslik talabi belgilandi.

## 8. Keyingi ishlar yo‘l xaritasi

Loyiha fayllarini GitHub repository'ga joylashtirishdan oldin quyidagi bosqichlar bajarilishi kerak.

### 1-bosqich: fayllarni solishtirish

- Lokal `Sayt` papkasining tarkibini tekshirish.
- `Sayt-github` repository tarkibini tekshirish.
- Ikkala loyihadagi `.gitignore` fayllarini solishtirish.
- `README.md` fayllari orasidagi farqlarni aniqlash.
- Bir xil nomli fayllar ustidan yozilish xavfini baholash.

### 2-bosqich: xavfsiz birlashtirish

- Mavjud Jekyll Chirpy loyihasining kerakli fayllarini repository'ga ko‘chirish.
- `.git` papkasini ko‘chirmaslik.
- `payariq-ssh-backup.zip` arxivini ko‘chirmaslik.
- Maxfiy kalitlar, parollar va boshqa maxfiy ma’lumotlarni tekshirish.
- Zarur fayllarni saqlab qolgan holda birlashtirishni yakunlash.

### 3-bosqich: Git holatini tekshirish

```bash
git status
git diff --stat
git diff -- .gitignore README.md
```

O‘zgarishlarni commit qilishdan oldin barcha fayllar ko‘rib chiqilishi kerak.

### 4-bosqich: saytni lokal tekshirish

Jekyll va Chirpy bog‘liqliklarini tekshirish, saytni lokal ishga tushirish va sahifalardagi xatolarni aniqlash rejalashtirilgan.

### 5-bosqich: GitHub'ga yuborish

Fayllar tekshirilgach, o‘zgarishlarni commit qilish va `main` branch'ga yuborish mumkin.

```bash
git add .
git commit -m "Add existing Jekyll Chirpy website"
git push origin main
```

Ushbu buyruqlar faqat fayllar tekshirilib, maxfiy ma’lumotlar chiqarib tashlanganidan keyin bajarilishi kerak.

### 6-bosqich: yakuniy tekshiruv

- GitHub repository tarkibini tekshirish.
- GitHub Pages sozlamalarini tekshirish.
- Saytning nashr etilishini tasdiqlash.
- Aniqlangan muammolar va ularning yechimlarini hujjatlashtirish.

## 9. Hozirgi holat

**Bajarilgan ishlar:**

- [x] Loyiha uchun alohida SSH kaliti yaratildi.
- [x] Ochiq kalit GitHub hisobiga qo‘shildi.
- [x] SSH autentifikatsiyasi tekshirildi.
- [x] SSH kalitlarining zaxira arxivi yaratildi.
- [x] GitHub repository alohida papkaga clone qilindi.
- [x] Git branch va remote ulanishi tekshirildi.
- [x] Repository ichidagi mavjud fayllar aniqlandi.

**Hali bajarilmagan ishlar:**

- [ ] Lokal loyiha va clone qilingan repository fayllarini solishtirish.
- [ ] `.gitignore` qoidalarini tekshirish va zarur bo‘lsa yangilash.
- [ ] Mavjud loyihani xavfsiz birlashtirish.
- [ ] Jekyll saytini lokal tekshirish.
- [ ] O‘zgarishlarni commit qilish va GitHub'ga yuborish.
- [ ] GitHub Pages orqali nashr etilishini tekshirish.

## Xulosa

GitHub bilan SSH orqali autentifikatsiya muvaffaqiyatli sozlandi. Repository alohida papkaga clone qilindi va uning dastlabki holati tekshirildi.

Keyingi bosqich — mavjud Jekyll Chirpy loyihasini GitHub repository bilan ehtiyotkorlik bilan birlashtirish. Bu jarayonda maxfiy ma’lumotlarni himoya qilish, mavjud fayllarni saqlash va har bir o‘zgarishni tekshirish asosiy talablar bo‘lib qoladi.
