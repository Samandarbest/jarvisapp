# JARVIS Android ilova

JARVIS backend'i (`jarvis/api.php`) bilan gaplashadigan Kotlin ilova. Chat, ovozli buyruq (STT — tizim mikrofoni orqali), ovozli javob (TTS), eslatma bildirishnomalari, va qo'ng'iroq/SMS tayyorlash (dialer/SMS ilovasini ochadi — yuborishni foydalanuvchi tasdiqlaydi).

## Backend tomonda qilish kerak bo'lgan ish

1. `jarvis/config.php`da `ANDROID_API_TOKEN`ni uzun tasodifiy qatorga o'zgartiring.
2. `jarvis/schema.sql`dagi yangi `jarvis_chat_log` jadvalini bazaga qo'shing (agar avval import qilingan bo'lsa, faqat shu jadvalni alohida ijro eting).
3. `jarvis/api.php` faylini serverga yuklang (u `jarvis/` papkasi ichida, boshqa fayllar bilan bir joyda bo'lishi kerak).

## APK qanday yig'iladi (lokal Android Studio shart emas)

Bu loyiha avvalgi Android TV loyihangizdagi kabi **GitHub Actions** orqali quriladi:

1. Ushbu papkani (`JarvisApp/`) yangi GitHub repository'ga yuklang.
2. GitHub'da **Actions** bo'limiga o'ting — push qilinganda avtomatik boshlanadi (yoki "Run workflow" bilan qo'lda ishga tushiring).
3. Tugagach, **Artifacts** bo'limidan `jarvis-debug-apk`ni yuklab oling — bu sizning `.apk` faylingiz.
4. Telefoningizga o'rnating (noma'lum manbalardan o'rnatishga ruxsat berish kerak bo'lishi mumkin).

## Ilovada birinchi ishga tushirish

1. Ilovani oching — avtomatik **Sozlamalar** ekraniga o'tadi.
2. **Server manzili**: `https://SIZNING_DOMAIN/jarvis/api.php` (masalan `https://myxvest1.ru/jarvis/api.php`)
3. **API token**: `config.php`dagi `ANDROID_API_TOKEN` bilan bir xil qiymat.
4. Saqlang — endi chat ishlaydi.

## Imkoniyatlar

- **Matnli chat**: backend bilan bir xil xotira va tool'lardan foydalanadi (Telegram va Android tarixi umumiy — `jarvis_chat_log` jadvali orqali).
- **Ovozli kirish**: mikrofon tugmasi — Android'ning o'z speech-to-text tizimidan foydalanadi (internetsiz ham ishlashi mumkin, qurilmaga bog'liq).
- **Ovozli javob**: JARVIS javobi avtomatik o'qib beriladi (TextToSpeech, o'zbek tiliga sozlangan — qurilmada o'zbekcha TTS mavjud bo'lmasa, tizim standart tilga tushadi).
- **Xavfli amallar tasdiqlash**: fayl o'chirish, bazadan o'chirish kabi amallar chatda "✅ Bajarish / ❌ Bekor qilish" tugmalari bilan chiqadi — xuddi Telegram botdagidek.
- **Eslatmalar**: 15 daqiqada bir marta backend tekshiriladi va bildirishnoma chiqariladi (zaxira mexanizmi — asosiy eslatma Telegram cron orqali keladi).
- **Qo'ng'iroq/SMS tayyorlash**: menyudan kontakt tanlansa, dialer yoki SMS ilovasi ochiladi — haqiqiy qo'ng'iroq/yuborish tugmasini foydalanuvchi bosishi shart (avtomatik yuborilmaydi).

## "Jarvis" wake-word (Alexa/OK Google kabi) — ORIENT

Bu ilova endi dastur yopiq bo'lganda ham fonda ishlaydi va "Jarvis" so'zini eshitganda avtomatik tinglashni boshlaydi.

### Sozlash

1. **console.picovoice.ai** saytida bepul hisob oching, **AccessKey** oling (shaxsiy foydalanish uchun bepul limit yetarli).
2. Ilovada **Sozlamalar**'ga o'ting, AccessKey'ni kiriting va **"Jarvis" so'zini doimiy tinglash** switch'ini yoqing.
3. Saqlashda ilova fon xizmatini (foreground service) ishga tushiradi — ekranda doimiy bildirishnoma ko'rinadi ("Tinglayapman... Jarvis deng"), bu Android talabi (fon mikrofon ishlatilganda bildirishnoma majburiy).
4. "Jarvis" deb ayting — JARVIS "Labbay" deb javob beradi va buyruqni tinglaydi, keyin bajaradi va ovozli javob qaytaradi.

### Muhim eslatmalar

- **Batareya**: doimiy tinglash batareyani ko'proq sarflaydi (Porcupine kutubxonasi juda kam quvvat ishlatadigan qilib yozilgan, lekin baribir fon xizmati doimiy ishlaydi). Kerak bo'lmaganda switch'ni o'chirib qo'ying.
- **"Jarvis" — tayyor (built-in) so'z**: Picovoice Porcupine kutubxonasida "Jarvis" standart o'rnatilgan so'zlardan biri, shuning uchun maxsus o'qitish/`.ppn` fayl yaratish shart emas.
- **Qurilma qayta yoqilganda**: agar wake-word yoqilgan bo'lsa, xizmat avtomatik qayta ishga tushadi (`BootReceiver`).
- **Xotira/tarix**: wake-word orqali kelgan buyruqlar ham xuddi chatdagi kabi backend orqali ishlaydi va tarixga yoziladi — keyin ilovani ochsangiz, chatda ko'rinadi.
- Ba'zi Android ishlab chiqaruvchilari (Xiaomi, Huawei, va h.k.) fon xizmatlarini agressiv o'ldiradi — ilovani "Batareya optimizatsiyasi"dan chiqarib qo'yish tavsiya etiladi (qurilma sozlamalarida).

- Rasm yuborish (`image_input`) — hozircha backend rasmni faqat "rasm yuborildi" deb qayd qiladi, AI vision integratsiyasi hali yo'q.
- Push-bildirishnoma (FCM) — hozirgi versiya WorkManager orqali davriy tekshiradi, bu batareyaga biroz ta'sir qilishi mumkin. Haqiqiy real-time push uchun Firebase Cloud Messaging kerak bo'ladi.
