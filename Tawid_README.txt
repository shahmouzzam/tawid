# Tawid — check it, pay it, track it

Supporting material for the Money Without Barriers challenge (Stream A — Technical Innovation).
Entrant: Shah Mouzzam Zia.

Everything here uses test values only. No real money or real payment provider is involved.

## Files

The two script files have `.txt` added to their names so they can be sent by email.
Remove the `.txt` ending before running them.

| File | What it is |
|---|---|
| `Tawid_Prototype.html` | The simulated proof of concept. Open it in any browser. No install needed. |
| `tawid-testwallet.mjs.txt` | The same check, pay, track flow written with the official Open Payments SDK, for the Interledger test network. |
| `mock-server.mjs.txt` | A local stand-in for two providers, used only to dry-run the script offline. |

## 1. Browser prototype (`Tawid_Prototype.html`)

1. Open the file in a browser.
2. Press **Check and get price**, then **Continue to Provider A**, then **Approve**.
3. Use **Fault to inject** to see how each failure is explained.

What is real: the client logic, the order of Open Payments calls, the approval hash check, price expiry, single use of a price, the tracking record and the failure dictionary.

What is simulated: both providers, their authorisation and resource servers, the exchange rate, balances and the Interledger packets. They all run inside the page.

## 2. Test-network script (`tawid-testwallet.mjs`)

Needs Node.js 20 or newer and test accounts at https://wallet.interledger-test.dev

```bash
npm init -y
npm install @interledger/open-payments
export TAWID_PAYER_WALLET="https://<payer wallet address>"
export TAWID_SELLER_WALLET="https://<seller wallet address>"
export TAWID_KEY_ID="<key ID from Settings > Developer Keys>"
node tawid-testwallet.mjs      # private.key must be in the same folder
```

The script prints a link. Open it, approve as the payer, return and press Enter.
It saves a tracking record as `tawid-run-<reference>.json`.

Never share or upload `private.key`.

## 3. Offline dry run (`mock-server.mjs`)

```bash
openssl genpkey -algorithm ed25519 -out private.key
node mock-server.mjs &          # FAULT=no_route | declined | failed | mismatch to inject a fault
TAWID_PAYER_WALLET=http://localhost:4010/sam TAWID_SELLER_WALLET=http://localhost:4010/liza \
TAWID_KEY_ID=test-key TAWID_USE_HTTP=1 TAWID_AUTO_APPROVE=1 node tawid-testwallet.mjs
```

## Test status (27 November 2026)

- Browser prototype: 13 of 13 scripted checks passed, in simulation only.
- Script: 6 dry runs against the local stand-in behaved as designed.
- Not done: no payment has been made on the Interledger test network.

## AI disclosure

The code in this folder was written by Claude (Anthropic) under the entrant's direction.
