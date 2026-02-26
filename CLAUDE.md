# CLAUDE.md — Soulfull Kitchen

> **Vibe:** Crypto culture meets comfort food. Think a cooler, street-smart Super Mario who cooks soul food and holds blue-chip JPEGs. Healthy, fly, and on-chain.

---

## Project Identity

| Field | Value |
|---|---|
| Project Name | Soulfull Kitchen |
| Collection Name | Soulfull Chef NFTs |
| Aesthetic | Pixelated / cartoon chef PFPs — Super Mario energy, streetwear cool |
| Theme | Crypto culture · great food · healthy vibes |
| Contact | soulfullkitchen@proton.me |
| Social | @SoulfullKitchen (Instagram) · @Soulfullkit (Twitter/X) |

---

## Tech Stack

### Current (Static MVP)
- **HTML/CSS/JS** — vanilla, zero frameworks, single `index.html`
- **Fonts** — Bungee (display/brand), Inter (body)
- **Dev server** — `live-server` (port 3000)
- **Formatter** — Prettier

### Planned Web3 Layer
- **Wallet** — Abstract Global Wallet (AGW) or Wagmi + viem
- **Chain** — TBD (Ethereum mainnet, Base, or Abstract L2 — decide before contract deployment)
- **NFT Standard** — ERC-721 or ERC-721A (gas efficient for larger collections)
- **Staking Contract** — Custom ERC-721 staking with time-locked reward distribution
- **Reward Token** — ERC-20 ($SOUL or similar — TBD)
- **Bundler/RPC** — Alchemy or QuickNode
- **Frontend framework upgrade** — React + Vite when Web3 hooks are wired in

---

## Design System (Brand Tokens)

These CSS custom properties are defined in `index.html` and must be used consistently across all new components:

```css
--cream:          #F8EFD8   /* page background */
--soul-green:     #22665A   /* primary / nav / footer */
--apron-teal:     #4FA69C   /* surfaces / secondary */
--chef-orange:    #F47A1F   /* CTA buttons */
--platter-yellow: #FDBF2D   /* highlights / pills / gradients */
--ink:            #1C1B19   /* body text */
--radius:         16px
--shadow:         0 10px 24px rgba(0,0,0,.12)
```

**Typography rules:**
- `Bungee` — headings, brand name, section titles only
- `Inter` — all body text, labels, UI elements
- Never mix display fonts in body copy

**Button variants (existing):**
- `.btn--primary` — chef-orange fill, bold CTA
- `.btn--ghost` — soul-green outline, secondary action

---

## File Structure

```
SoulfullKitchensite/
├── CLAUDE.md              ← you are here
├── index.html             ← main app (single page)
├── package.json           ← dev scripts
├── assets/
│   ├── chef.png           ← Soulfull Chef #1 PFP (hero + logo)
│   └── nft_soulfull_preview.png  ← Soulfull Chef #2 PFP
│
│   (add menu item images here as: menu_<id>.jpg)
│   (add NFT images here as: chef_<number>.png)
```

**When the project grows, target this structure:**

```
SoulfullKitchensite/
├── index.html             ← landing / static shell
├── app/                   ← React + Vite app (when added)
│   ├── src/
│   │   ├── components/    ← UI components
│   │   ├── hooks/         ← Web3 hooks (useWallet, useStake, useNFT)
│   │   ├── contracts/     ← ABI JSON files
│   │   ├── pages/         ← route-level views
│   │   └── styles/        ← global CSS, tokens
│   └── vite.config.js
├── contracts/             ← Solidity smart contracts
│   ├── SoulChefNFT.sol    ← ERC-721A collection
│   ├── SoulStaking.sol    ← staking + rewards contract
│   └── SoulToken.sol      ← ERC-20 reward token (if needed)
└── assets/                ← images, metadata
```

---

## Current Features

### Menu & Ordering
- Static menu array in `index.html` (`MENU` const) — swap `img` paths with real food photos
- Add-to-cart with localStorage persistence (`sfk_cart_v1`)
- Quantity controls, cart drawer, subtotal
- Checkout modal collects: name, phone, pickup time, notes
- Confirms via `mailto:` to `soulfullkitchen@proton.me` — **no backend required**
- Payment: cash on pickup only (by design)

### NFT Gallery
- Static `NFTS` array — add new PFPs by pushing objects: `{ id, name, img }`
- Currently shows preview cards with a "Details" placeholder button

### Wallet Connect
- `#walletBtn` is a placeholder — triggers `alert()` today
- Ready to be wired to AGW or Wagmi `connect()`

---

## Planned Features (Roadmap)

### Phase 1 — Polish Static Site
- [ ] Add real food photography to `assets/` (menu items)
- [ ] Add full NFT collection images to `assets/`
- [ ] Responsive mobile polish (cart, hero)
- [ ] Accessibility pass (aria labels, keyboard nav, focus styles)

### Phase 2 — Wallet Integration
- [ ] Wire `#walletBtn` to Abstract Global Wallet or Wagmi
- [ ] Display connected wallet address (truncated) in nav
- [ ] Read user's NFT balance from contract
- [ ] Gate NFT-holder perks (badge on nav, exclusive menu items)

### Phase 3 — NFT Staking
- [ ] Deploy `SoulStaking.sol` — stake Soulfull Chef PFPs, earn $SOUL tokens
- [ ] Rewards distributed per epoch (daily, weekly — TBD)
- [ ] Staking dashboard: show staked NFTs, pending rewards, claim button
- [ ] Un-stake with optional lock period penalty
- [ ] Design: staked chef gets an animated "cooking" state in the UI

### Phase 4 — Private Customer Chat
- [ ] Per-customer private chat linked to wallet address (not public)
- [ ] Use case: real-time order updates, custom order requests
- [ ] Options: XMTP (Web3 native messaging), Pusher, or Socket.io (simpler)
- [ ] Chat gated behind wallet connection — anonymous wallets get a generated chef alias
- [ ] Messages encrypted client-side if using XMTP

---

## Smart Contract Notes

### SoulChefNFT.sol (ERC-721A)
- Max supply: TBD
- Mint price: TBD
- Metadata: IPFS (Pinata or NFT.storage)
- Traits: chef hat style, skin tone, apron color, accessories, background
- Royalties: ERC-2981 on-chain royalty standard

### SoulStaking.sol
- Stake ERC-721 tokens, receive ERC-20 rewards
- Pattern: non-custodial staking preferred (NFT stays in wallet, uses `setApprovalForAll`)
- Reward rate: configurable by owner, per NFT per epoch
- Emergency withdraw: always include a no-penalty escape hatch

### SoulToken.sol (ERC-20, optional)
- Name: $SOUL (or decide with community)
- Minting: only callable by staking contract
- Use cases: discounts on orders, future governance, exclusive menu unlocks

---

## Web3 Conventions

- **Never** store private keys anywhere in this repo
- **Never** commit `.env` files — use `.env.example` with placeholder values
- All contract addresses go in `src/contracts/addresses.js` keyed by chainId
- ABI files go in `src/contracts/abis/` as JSON
- Use `viem` for contract reads, `wagmi` hooks for React component binding
- Always handle: wallet not connected, wrong network, transaction pending, tx failed states
- Show toast notifications for all async wallet actions (not `alert()`)

---

## Dev Commands

```bash
# Install deps
npm install

# Local dev server (port 3000, auto-reload)
npm run dev

# Static file server
npm run serve

# Format all HTML/CSS/JS/JSON
npm run format
```

**When React + Vite is added:**
```bash
npm run dev        # Vite HMR dev server
npm run build      # production build to dist/
npm run preview    # preview production build locally
```

---

## Style & Code Rules

- **No frameworks yet** — keep it vanilla HTML/CSS/JS until Web3 complexity demands React
- **Mobile first** — all new CSS uses `min-width` breakpoints upward from 375px
- **No inline styles** in new code — use classes or CSS custom properties
- **No `alert()`** in new features — use a toast/snackbar component
- **Comments** only where logic is non-obvious; don't comment self-evident code
- **Security** — sanitize any user-rendered content with `textContent`, not `innerHTML`
- **XSS safe** — never interpolate raw user input into HTML template literals

---

## Contribution Notes

- Branch: `claude/chef-nft-genius-file-XSNk6` (active dev branch)
- All commits go to feature branches; `main` is production
- Commit messages: imperative, present tense — "Add staking hook", not "Added staking"
- Keep commits focused — one logical change per commit

---

## The Vibe (for AI context)

This is not a generic NFT project. Soulfull Kitchen sits at the intersection of:
- **Black food culture** — soul food, comfort, community
- **Crypto-native aesthetic** — PFPs, on-chain perks, wallet-gated experiences
- **Health consciousness** — seamoss drinks, fresh ingredients, intentional eating
- **Retro game energy** — the chefs are fun, colorful, and full of personality like a Super Mario character but with more sauce

When writing copy, UI text, or naming things: keep it warm, confident, and community-first. Not corporate. Not sterile. Soulful.
