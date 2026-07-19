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

## Пошта connect@gettruecolor.com

Сайт і privacy policy вказують контакт `connect@gettruecolor.com`. Щоб він працював (безкоштовний email-форвардинг Namecheap):

1. Namecheap → **Domain List → Manage** (біля gettruecolor.com) → вкладка **Domain** → секція **Redirect Email**.
2. **Add Forwarder**: Alias `connect` → Forward to: ваша особиста скринька → Save.
3. Namecheap сам додасть MX-записи (`eforward*.registrar-servers.com`) в Advanced DNS; у **Mail Settings** має стояти «Email Forwarding». Наші A/CNAME-записи для сайту це не зачіпає.
4. Через ~30 хв надішліть тестовий лист на connect@ і переконайтесь, що він доходить.

Це **тільки прийом**. Відповідь із Gmail піде з особистої адреси. Якщо захочете і надсилати від `connect@`: найпростіше — iCloud+ Custom Email Domain (входить у підписку iCloud+; замінить MX на Apple) або безкоштовний план Zoho Mail. Для GDPR-контакту достатньо прийому.

## Скріншоти

Зараз у галереї — стилізовані плейсхолдери. Щоб замінити:

1. Покладіть файли в `images/` (портрет iPhone, ~1206×2622 або будь-який 9:19.5).
   З симулятора: `xcrun simctl io booted screenshot images/01-editor.png`.
2. В `index.html` у секції `#gallery` замініть кожен `<div class="shot-placeholder">…</div>` на
   `<img src="images/01-editor.png" alt="…" width="1206" height="2622">` — шаблон є в коменті прямо над галереєю.
3. За бажанням додайте `images/og.png` (1200×630) і розкоментуйте `og:image` в `<head>`.

## Privacy Policy і реліз апки

`privacy.html` написана так, щоб покривати і сайт, і саму апку (on-device обробка, TestFlight-краші через Apple). Після деплою її URL можна одразу ставити в `AppConfig.privacyPolicyURL` — це закриває відповідний пункт реліз-ранвею. Дата набуття чинності — в кінці сторінки; за суттєвих змін оновіть її.
