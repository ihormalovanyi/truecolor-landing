# True Color — лендінг

Односторінковий статичний сайт для iOS-фоторедактора **True Color**: хіро, фічі, галерея скріншотів, форма «Join the beta» (email-розсилка) і Privacy Policy.

## Структура

```
index.html      — лендінг (семантичний HTML + вбудований CSS, без JS і без CDN)
privacy.html    — Privacy Policy (EN) → https://gettruecolor.com/privacy.html (для AppConfig.privacyPolicyURL)
images/         — сюди класти скріншоти (поки на сторінці плейсхолдери)
CNAME           — власний домен для GitHub Pages (gettruecolor.com)
```

Збірка не потрібна — сайт деплоїться як є.

## Локальний перегляд

```sh
python3 -m http.server 4173
# → http://localhost:4173
```

## Email-розсилка (Buttondown)

Форма на сторінці — embed-форма [Buttondown](https://buttondown.com) (double opt-in з коробки, безкоштовно до 100 підписників).

1. ✅ Акаунт створено, username `sandbox`.
2. ✅ У `index.html` `action` форми вказує на `.../embed-subscribe/sandbox`.
3. ⏳ У Buttondown: **Settings → Subscribing** — переконайтесь, що double opt-in увімкнено (лист-підтвердження; без підтвердження адреса не потрапляє в список).
4. ⏳ У Buttondown: **Settings** — вкажіть фізичну поштову адресу відправника перед першою розсилкою.

> ⚠️ **Не запускайте розсилку, поки не вказана реальна фізична адреса.** CAN-SPAM (US) вимагає робочу поштову адресу відправника в кожному листі. Підійде і P.O. Box чи адреса virtual mailbox-сервісу. Buttondown автоматично додає адресу з налаштувань і лінк відписки у футер кожного листа.

Легальні вимоги, які вже закриті самою сторінкою:

- Згода — окремий **незапередзаповнений** checkbox з чіткою метою («news about True Color and an invitation to the TestFlight beta»).
- Лінк на Privacy Policy прямо біля форми.
- Мінімізація даних: збираємо лише email (+ Buttondown фіксує час згоди/підтвердження).
- Double opt-in описаний користувачу прямо під формою.
- Механізм відкликання згоди: відписка в кожному листі + connect@gettruecolor.com.
- Без cookies, трекерів і аналітики — cookie-банер не потрібен.

Що залишається на вас при надсиланні листів: чесні теми листів (CAN-SPAM), реальна фізична адреса (див. вище).

> Якщо колись додасте аналітику — або cookieless (self-hosted Plausible), або тільки з consent-банером. Краще без неї.

## Деплой на GitHub Pages + домен `gettruecolor.com`

Домен уже зареєстровано на Namecheap. Репозиторій **уже створено і задеплоєно**: <https://github.com/ihormalovanyi/truecolor-landing>, GitHub Pages увімкнено (гілка `main`, домен `gettruecolor.com` з файлу `CNAME`), білд успішний. **Залишився лише крок 3 — DNS.**

### 1. Репозиторій ✅ зроблено

`gh repo create truecolor-landing --public --source . --push` — виконано.

### 2. Pages ✅ зроблено

Увімкнено через API (Source: `main` / root). Custom domain = `gettruecolor.com`.

### 3. DNS на Namecheap ← ЗАЛИШИЛОСЯ ВАМ

**Domain List → Manage (біля `gettruecolor.com`) → Advanced DNS.**

Спершу **видаліть** дефолтні записи, які Namecheap ставить сам: `CNAME www → parkingpage.namecheap.com` і `URL Redirect Record`. Далі додайте:

| Type | Host | Value | TTL |
|------|------|-------|-----|
| A Record | `@` | `185.199.108.153` | Automatic |
| A Record | `@` | `185.199.109.153` | Automatic |
| A Record | `@` | `185.199.110.153` | Automatic |
| A Record | `@` | `185.199.111.153` | Automatic |
| CNAME Record | `www` | `ihormalovanyi.github.io.` | Automatic |

(Опційно, IPv6 — 4 × AAAA на `@`: `2606:50c0:8000::153`, `…8001::153`, `…8002::153`, `…8003::153`.)

Namecheap DNS оновлюється зазвичай за 30 хв (буває до кількох годин).

### 4. Увімкнути HTTPS

Повернутись у **Settings → Pages**, дочекатись, поки GitHub перевірить DNS, і поставити галку **Enforce HTTPS**. Сертифікат безкоштовний і автоматичний. Готово: сайт на `https://gettruecolor.com`.

### 5. Після підключення

- `AppConfig.privacyPolicyURL` в апці → `https://gettruecolor.com/privacy.html` (canonical/og:url у HTML уже прописані).
- У Buttondown: **Settings** → адреса сайту `https://gettruecolor.com`.
- Пошту для розсилки бажано слати з `@gettruecolor.com` (виглядає охайно і краще для доставки) — у Buttondown це налаштовується в **Settings → Sending**, з додаванням DKIM/SPF-записів у той самий Namecheap Advanced DNS.

> **Альтернатива — Cloudflare Pages:** проєкт → Custom domains → Add `gettruecolor.com` → майстер сам створить DNS-записи (якщо перенести домен/NS у Cloudflare). У README лишаємо основним GitHub Pages.

## Автоматична видача TestFlight-посилання

Ланцюг: людина лишає email → Buttondown шле лист-підтвердження → людина тисне лінк → **автоматично потрапляє на `beta.html`, де лежить кнопка TestFlight**.

### 1. Отримати публічне посилання TestFlight

App Store Connect → ваша апка → вкладка **TestFlight** → секція **Public Link** → **Enable Public Link**. Отримаєте URL виду `https://testflight.apple.com/join/XXXXXXXX` (ліміт — 10 000 тестувальників).

> Публічне посилання за задумом Apple є публічним: будь-хто з URL може приєднатись. Це нормально для відкритої бети.

### 2. Вставити його в `beta.html`

У файлі `beta.html` замініть `https://testflight.apple.com/join/REPLACE_ME` на своє посилання. Сторінка має `noindex`, тож у пошук не потрапить.

### 3. Увімкнути редірект після підтвердження

Buttondown → **Settings → Subscribing** → поле редіректу **після підтвердження підписки** (redirect after confirmation) → `https://gettruecolor.com/beta.html`.

Це працює **на безкоштовному плані** і має приємний побічний ефект: сторінку бачать лише ті, хто реально підтвердив пошту.

### Альтернатива: лист замість сторінки (платно)

Якщо хочеться, щоб посилання приходило **листом**, а не лише показувалось на сторінці:

- **Settings → Subscribing → Welcome** — лист, що йде одразу після підтвердження. Це фіча плану **Standard** (платний).
- **Automations** (тригер `subscriber.confirmed` → дія «Send an email») — теж платні, але дають складніші сценарії: затримки, розгалуження за тегами, серії листів.

Наша форма вже проставляє тег `testflight-beta` — за ним зручно фільтрувати автоматизацію, коли перейдете на платний план.

Актуальні ціни: <https://buttondown.com/pricing> (безкоштовно до 100 підписників).

## Надсилання з @gettruecolor.com

**За замовчуванням Buttondown шле зі свого домену.** Щоб листи йшли від вас, треба підключити sending domain — це **безкоштовно на всіх планах** (Buttondown навмисно не ховає це за пейволом).

Buttondown → **Settings → Domains → Sending** → додати домен. Рекомендований «managed» спосіб: ви делегуєте **піддомен** (наприклад `mail.gettruecolor.com`) двома `NS`-записами, і Buttondown сам керує SPF/DKIM/DMARC.

У Namecheap → **Advanced DNS → Add New Record**:

| Type | Host | Value |
|------|------|-------|
| NS Record | `mail` | *(перший сервер із налаштувань Buttondown)* |
| NS Record | `mail` | *(другий сервер із налаштувань Buttondown)* |

> ⚠️ **Беріть саме піддомен `mail`, не `@`.** Тоді це не зачепить ні A-записи сайту (GitHub Pages), ні MX-записи форвардингу `connect@`. Делегування apex зламало б і сайт, і пошту.
>
> ⚠️ У полі Host пишеться лише мітка (`mail`), без домену — Namecheap додасть його сам.

Після верифікації листи підуть від `@mail.gettruecolor.com`. У **Settings → Sending domain → Add-ons** можна окремо виставити reply-to на `connect@gettruecolor.com`, щоб відповіді падали вам у скриньку.

## Пошта connect@gettruecolor.com

Сайт і privacy policy вказують контакт `connect@gettruecolor.com`. Щоб він працював (безкоштовний email-форвардинг Namecheap):

1. Namecheap → **Domain List → Manage** (біля gettruecolor.com) → вкладка **Domain** → секція **Redirect Email**.
2. **Add Forwarder**: Alias `connect` → Forward to: ваша особиста скринька → Save.
3. Namecheap сам додасть MX-записи (`eforward*.registrar-servers.com`) в Advanced DNS; у **Mail Settings** має стояти «Email Forwarding». Наші A/CNAME-записи для сайту це не зачіпає.
4. Через ~30 хв надішліть тестовий лист на connect@ і переконайтесь, що він доходить.

Це **тільки прийом**. Відповідь із Gmail піде з особистої адреси. Якщо захочете і надсилати від `connect@`: найпростіше — iCloud+ Custom Email Domain (входить у підписку iCloud+; замінить MX на Apple) або безкоштовний план Zoho Mail. Для GDPR-контакту достатньо прийому.

## Медіа

Усі зображення лежать у `media/`, у двох ширинах і двох форматах (WebP + JPEG-фолбек), метадані вичищені:

| Файл | Де використовується |
|------|---------------------|
| `hero-demo.mp4`, `hero-poster.jpg` | відео-демо в hero (2× швидкість) |
| `castle-*.{jpg,webp}` | before/after — ліворуч оригінал, праворуч CSS-грейд |
| `bee-*.{jpg,webp}` | інтерактивна панель «Grade it yourself» |
| `shot-1-home` … `shot-5-filters` | галерея скріншотів (5 шт.) |

Щоб замінити або додати скріншот:

```sh
# 1. зняти (симулятор або AirDrop з телефона)
xcrun simctl io booted screenshot ~/Downloads/new.png
# 2. згенерувати веб-версії (замініть NAME)
ffmpeg -y -i ~/Downloads/new.png -map_metadata -1 -vf scale=400:-2 -q:v 4 media/NAME-400.jpg
ffmpeg -y -i ~/Downloads/new.png -map_metadata -1 -vf scale=800:-2 -q:v 4 media/NAME-800.jpg
cwebp -q 80 -metadata none media/NAME-400.jpg -o media/NAME-400.webp
cwebp -q 80 -metadata none media/NAME-800.jpg -o media/NAME-800.webp
```

Потім додайте `<figure class="shot">` у секцію `#gallery` за зразком сусідніх.

> `-map_metadata -1` і `-metadata none` обовʼязкові: оригінали з телефона містять GPS-координати.

За бажанням додайте `media/og.png` (1200×630) і розкоментуйте `og:image` в `<head>`.

## Privacy Policy і реліз апки

`privacy.html` написана так, щоб покривати і сайт, і саму апку (on-device обробка, TestFlight-краші через Apple). Після деплою її URL можна одразу ставити в `AppConfig.privacyPolicyURL` — це закриває відповідний пункт реліз-ранвею. Дата набуття чинності — в кінці сторінки; за суттєвих змін оновіть її.
