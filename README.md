# SMM Panel Telegram Bot

## Providers
`shweboost`, `secsers`, `hiroshi`, `morethanpanel` (list lives in `provider-ids.js`).

| Provider | .env keys |
|---|---|
| ShweBoost | `SHWEBOOST_API_URL`, `SHWEBOOST_API_KEY` |
| Secsers | `SECSERS_API_URL`, `SECSERS_API_KEY` |
| Hiroshi | `HIROSHI_API_URL`, `HIROSHI_API_KEY` |
| MoreThanPanel | `MORETHANPANEL_API_KEY` (URL defaults to `https://morethanpanel.com/api/v2`) |

Optional per provider: `<NAME>_USD_TO_MMK`, `<NAME>_MARKUP_MULTIPLIER` (or `/setrate morethanpanel 4400 2.3`).

Customers never see provider names. If a provider fails (e.g. "Not enough funds on balance"),
the customer gets the editable text `order_error_contact_admin`, their money is refunded,
and the admins get a Telegram alert naming the provider and reason.

## Telegram Account shop
Shop: **📱 Telegram Account** → *Account Instock* + **Buy Account** (or *Out Of Stock*) → name / balance / price of the next account + **ဝယ်မည်** → phone number + 2step psw + **Get Otp** (reads the code sent in the last 5 min; Copy 2step psw button).
History: **📜 History** → *Orders History ကိုရွေးချယ်ပါ* → **Account ဝယ်ယူမှုမှတ်တမ်း** (5 per page, hidden after 10 days - the account itself is not removed) / **Boost မှတ်တမ်း**. A number opens **Profile ကိုကြည့်ရန် / Code ယူရန် / Remove From History**; *Code ယူရန်* offers **5min code** and **old lastest code** (Copy Code / Copy 2Step buttons).

Setup
1. `npm install` (adds the `telegram` package).
2. Set `TG_API_ID` and `TG_API_HASH` (https://my.telegram.org -> API development tools).
3. For each account you own run `node tools/make-session.js` on your computer and copy the printed session string.
4. Admin Panel -> **TG Accounts**: add phone (with country code, any country), the account's own price, optional 2step password, and the session string. **စစ်** tests a session; **ဈေး** edits the price of an unsold account.

Notes: sessions and 2step passwords are stored AES-256-GCM encrypted (key from `AI_KEY_SECRET`, else `BOT_TOKEN` - changing it makes stored values unreadable). Only the Telegram service chat (777000) is read. Code requests have an 8 s cooldown and a per-account cap (`TGACC_MAX_CODE_REQUESTS`, default 40). Buying claims the account atomically, then takes its price; if the balance is too low the account is released again.

New-device notice: after Get Otp / 5min code the bot keeps a connection to that account for 5 minutes (max `30` accounts at once) and forwards Telegram's "New login ... from a new device" message, with the account's phone number, to the buyer.
