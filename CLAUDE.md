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
│   │   ├── components/
│   │   │   ├── AuthModal/       ← wallet + email login modal
│   │   │   ├── CartPanel/       ← slide-in cart (replaces floating drawer)
│   │   │   ├── OrderChat/       ← per-order chat window (post-checkout only)
│   │   │   ├── StakingDash/     ← stake PFP, view rewards
│   │   │   ├── CultureGallery/  ← NFT art + lore display
│   │   │   └── Toast/           ← toast notification system
│   │   ├── hooks/
│   │   │   ├── useWallet.js     ← connect / disconnect wallet
│   │   │   ├── useAuth.js       ← unified auth (wallet or email)
│   │   │   ├── useStake.js      ← stake / unstake / claim rewards
│   │   │   ├── useNFT.js        ← read NFT balance + metadata
│   │   │   └── useOrderChat.js  ← open/send/receive order chat
│   │   ├── contracts/           ← ABI JSON files
│   │   ├── pages/               ← route-level views
│   │   └── styles/              ← global CSS, tokens
│   └── vite.config.js
├── contracts/             ← Solidity smart contracts
│   ├── SoulChefNFT.sol    ← ERC-721A collection
│   ├── SoulStaking.sol    ← staking + rewards contract
│   └── SoulToken.sol      ← ERC-20 reward token (if needed)
└── assets/                ← images, metadata
```

**Page / Section Layout (single-page app flow):**
```
/ (home)
├── Nav        — logo · Menu · NFTs · Stake · [wallet/email avatar]
├── Hero       — headline, CTA (Order Now / View Collection)
├── Menu       — food cards, add to cart → slide-in CartPanel
│               └── Checkout → Pickup Details modal → confirmation → OrderChat opens
├── Culture    — NFT gallery (all visitors), art + trait showcase
├── Staking    — [wallet-gated] stake PFP, rewards dashboard
└── Footer     — socials, contact
```

---

## Current Features

### Menu & Ordering
- Static menu array in `index.html` (`MENU` const) — swap `img` paths with real food photos
- Add-to-cart with localStorage persistence (`sfk_cart_v1`)
- Quantity controls, fixed-bottom cart drawer (⚠️ **being replaced** — see UX decisions below), subtotal
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

## UX Decisions (locked in)

### No floating chat-box cart
The current cart drawer is a fixed widget pinned to the bottom-right corner — it reads visually like a chat window and breaks the layout energy. **Replace it** with an inline cart experience (slide-in side panel or dedicated cart page/section) that feels intentional, not like an afterthought widget.

### Auth: Wallet OR Email
Users can sign in / identify themselves two ways:
- **Connect Wallet** — MetaMask, Coinbase Wallet, Abstract Global Wallet, WalletConnect
- **Email login** — magic link or password (simple, no-friction for non-crypto users)
Both flows land on the same user session. Wallet connection unlocks NFT perks. Email is a fallback for food-only customers.
Auth gate is soft — you can browse and build a cart without logging in, but must authenticate to place an order or enter the staking/rewards area.

### Post-Order Chat (per customer)
After a customer places an order, a dedicated chat window opens for **that order only**. This is not a persistent floating chat widget on every page. The flow:
1. Customer places order → confirmation screen
2. Confirmation screen has a "Chat with us about your order" button
3. Chat is private, scoped to that order ID / wallet / email session
4. Kitchen side can coordinate: "Your order is ready", "We're out of yams — want a sub?"
5. Chat history stored per session; not a general-purpose support widget

### Staking = Culture + Rewards
Staking is not just financial — it is the gateway to the culture side of the project:
- Stake a Soulfull Chef PFP → earn $SOUL rewards over time
- $SOUL redeemable for: order discounts, exclusive menu items, early drops
- Staking dashboard doubles as a **culture gallery** — staked NFTs are displayed in a
  stylized "kitchen wall" layout showing the art, traits, and lore of each chef
- Non-holders can still browse the gallery but cannot stake or claim rewards

---

## Planned Features (Roadmap)

### Phase 1 — Redesign Static Site
- [ ] Replace fixed cart drawer with a proper slide-in cart panel (not a chat-style widget)
- [ ] Add auth modal: "Connect Wallet" button + "Continue with Email" option side by side
- [ ] Add real food photography to `assets/` (menu items)
- [ ] Add full NFT collection images to `assets/`
- [ ] Responsive mobile polish (cart panel, hero, nav)
- [ ] Accessibility pass (aria labels, keyboard nav, focus styles)
- [ ] Remove all `alert()` calls — replace with inline toast notifications

### Phase 2 — Auth + Wallet Integration
- [ ] Build auth modal UI (wallet connect + email magic link)
- [ ] Wire wallet connect to Abstract Global Wallet or Wagmi
- [ ] Wire email login (magic link via Resend, Supabase Auth, or similar)
- [ ] Display connected identity in nav (truncated wallet address or email)
- [ ] Read user's NFT balance from contract once wallet is connected
- [ ] Gate NFT-holder perks (badge in nav, exclusive menu items, discount label on cart)

### Phase 3 — Post-Order Chat
- [ ] After order confirmation, open a per-order private chat window (not a persistent widget)
- [ ] Chat scoped to order ID + customer identity (wallet or email session)
- [ ] Kitchen dashboard to see all active order chats in one place
- [ ] Real-time updates: order ready, substitutions, pickup confirmation
- [ ] Tech options: XMTP (wallet-native, encrypted), Pusher, or Socket.io
- [ ] Chat UI styled on-brand — dark green header, chef avatar on kitchen side

### Phase 4 — NFT Staking + Culture Gallery
- [ ] Deploy `SoulStaking.sol` — stake Chef PFPs, earn $SOUL tokens per epoch
- [ ] Staking dashboard shows: staked NFTs (kitchen wall layout), pending rewards, claim button
- [ ] Staked chef gets an animated "cooking" state overlay in the gallery
- [ ] $SOUL redeemable at checkout: discounts, exclusive items, early access drops
- [ ] Culture gallery open to all visitors — staking/rewards gated behind wallet connect
- [ ] Un-stake with optional lock period (penalty if early exit — TBD)

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
