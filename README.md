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
User flow: **📱 Telegram Account** → price text (`1 Acc = 1,200 ကျပ်`) + **Buy** → phone number + **Get Code** → latest login code from Telegram.

Setup
1. `npm install` (adds the `telegram` package).
2. Set `TG_API_ID` and `TG_API_HASH` (from https://my.telegram.org → API development tools).
3. For each account you own run `node tools/make-session.js` on your computer and copy the printed session string.
4. Admin Panel → **TG Accounts** → add phone (with country code) + session string. Use **စစ်** to test the session. Price is edited on the same page (`TGACC_PRICE` is only the first default).

Notes: sessions are stored AES-256-GCM encrypted (key from `AI_KEY_SECRET`, falling back to `BOT_TOKEN` - if that value changes, stored sessions can no longer be decrypted). Only the Telegram service chat (777000) is read. "Get Code" has an 8 s cooldown and a per-account cap (`TGACC_MAX_CODE_REQUESTS`, default 40). Purchase takes the balance and claims the account atomically; if stock runs out in between, the money is refunded.
