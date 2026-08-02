\# anychain-assets



Public configuration data consumed by \*\*AnyChain Wallet\*\* at runtime.



Whenever you push to this repo, every installed app picks up the changes on

its next launch — \*\*no Play Store update is required\*\* to add a new chain or

token.



\## Files



| File | What it controls |

| --- | --- |

| `chains.json` | All blockchains the wallet supports (native coins, RPC URLs, explorer APIs). |

| `tokens.json` | ERC-20 / TRC-20 tokens the wallet knows about by default. |

| `icons/\*.png` | Icons referenced by `imgPath` URLs inside the two JSON files. |



\## How the app loads them



```

App launch

&#x20;  ↓

1\. Read cached JSON from secure storage  ← INSTANT

&#x20;  (or, on a brand-new install, the apk's bundled fallback)

&#x20;  ↓

2\. Render home / manage tokens

&#x20;  ↓

3\. Background fetch:

&#x20;     https://raw.githubusercontent.com/solutionprovider25/anychain-assets/main/chains.json

&#x20;     https://raw.githubusercontent.com/solutionprovider25/anychain-assets/main/tokens.json

&#x20;  ↓

4\. If different → update cache + notify UI (no restart needed).

```



A failed network fetch is non-fatal — last cached version is used. A first

install with no network falls back to the apk-bundled

`assets/data/chains.json`.



\## Adding a chain



1\. Add an object to `chains.json` (see existing entries for the schema).

2\. Drop the icon into `icons/<symbol>.png` (transparent PNG, 256×256 recommended).

3\. Commit + push. Done.



\### Required fields



| Field | Notes |

| --- | --- |

| `name` | Human-readable name shown in UI (e.g. `Base`). |

| `symbol` | UNIQUE short symbol used as the internal id (e.g. `BASE`). |

| `coingeckoId` | Used by `PriceService` to fetch USD price. |

| `imgPath` | URL to the chain icon. |

| `coinType` | 60 for EVM, 0 for BTC, 195 for TRX, 501 for SOL, 2 for LTC, 3 for DOGE, 144 for XRP. |

| `derivationPath` | BIP44 path. EVM uses `m/44'/60'/0'`. |

| `chainId` | EVM chain ID (null for non-EVM). |

| `rpcUrl` | Public RPC endpoint. |

| `explorerUrl` | Block explorer URL with trailing `/tx/`. |

| `decimals` | Native coin decimals (18 for most EVM, 8 for BTC, 6 for TRX/XRP, 9 for SOL). |

| `apiUrl` | Etherscan-compatible API for tx history. For EVM use `https://api.etherscan.io/v2/api` (V2 unified, paid key required). For others, the chain's Blockscout `/api`. |

| `apiKey` | Etherscan V2 API key. Set to `null` for Blockscout. |

| `price` | Always `0.0` — overwritten at runtime by the price service. |



\## Adding a token



1\. Add an object to `tokens.json`.

2\. The `onChainSymbol` must exactly match a chain's `symbol` in `chains.json`.



\### Required fields



| Field | Notes |

| --- | --- |

| `name` | UI label (e.g. `USDC`). |

| `symbol` | UI ticker (e.g. `USDC`). |

| `coingeckoId` | Price lookup id. |

| `imgPath` | URL to the token icon. |

| `contractAddress` | Token contract (case-sensitive for TRC-20). |

| `decimals` | Most stablecoins use 6; BSC-USDT uses 18. |

| `onChainSymbol` | Parent chain symbol (must exist in `chains.json`). |



\## Security notes



\- `chains.json` currently has the Etherscan V2 API key in plaintext. Anyone

&#x20; who pulls this repo can see it. \*\*Rotate the key periodically\*\* or move it

&#x20; into the app code as a constant (and leave `apiKey: null` here).

\- RPC URLs are public infrastructure — fine to expose.

\- Never commit private keys, mnemonics, or signing secrets to this repo.



\## Versioning



The app does NOT use a version field — it always trusts the latest published

JSON. Roll back by reverting the commit.

