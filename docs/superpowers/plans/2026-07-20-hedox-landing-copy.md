# Hedox Landing Page (kópia go.hedox.sk) — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Presná kópia landing page https://go.hedox.sk/ v Astro 5 + Tailwind v4, s Netlify Forms (GDPR checkbox, Netlify reCAPTCHA, AJAX odoslanie), pripravená na deploy z GitHubu na Netlify.

**Architecture:** Statický Astro web. Každá sekcia = samostatný komponent v `src/components/`, všetky texty v `src/data/content.ts`, dizajnové tokeny v Tailwind `@theme`. Interaktivita (accordion, AJAX formulár) čistým vanilla JS v `<script>` blokoch komponentov.

**Tech Stack:** Astro 5, Tailwind CSS 4 (`@tailwindcss/vite`), Netlify Forms + Netlify reCAPTCHA v2, fonty Poppins (nadpisy) + Inter (text) z Google Fonts.

## Global Constraints

- Commity NIKDY nesmú obsahovať zmienku o AI/Claude — žiadny `Co-Authored-By: Claude`, žiadny `🤖 Generated with...` footer (globálne pravidlo používateľa).
- Texty sú 1:1 zo živého webu (sú v `content.ts` nižšie) — nemeniť ich znenie. Dve vedomé opravy oproti originálu: preklep „Telefónne číšlo“ → „Telefónne číslo“ a mailto odkaz vo footeri (originál má omylom `mailto:soldan@aksoldan.sk`) → `mailto:fukanaizolacia.kmety@gmail.com`.
- Farby: primárna zelená `#859f54`, zelená piluliek/submitu `#7e9d45`, tmavá olivová CTA `#3f4f23`, tmavosivá `#222222`/`#444444`, svetlosivá `#cdcdcd`. Fonty: nadpisy Poppins bold, text Inter.
- Jazyk stránky: `lang="sk"`. Všetky viditeľné texty po slovensky.
- Sťahovanie zo živého webu vyžaduje browser User-Agent hlavičku (server blokuje curl default): `-A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36"`.
- Netlify Forms + reCAPTCHA fungujú až na produkcii — lokálne sa testuje len build, markup a JS logika.

---

### Task 1: Scaffold Astro + Tailwind + Netlify konfigurácia

**Files:**
- Create: `package.json`, `astro.config.mjs`, `tsconfig.json`, `src/styles/global.css`, `netlify.toml`, `src/pages/index.astro` (dočasný minimálny), `src/layouts/Layout.astro`
- Modify: `.gitignore`

**Interfaces:**
- Produces: `Layout.astro` s Props `{ title: string; description: string }` a slotom; Tailwind tokeny `font-heading`, `font-body`, farby `brand` (#859f54), `brand-dark` (#3f4f23), `pill` (#7e9d45).

- [ ] **Step 1: Inicializuj npm projekt a nainštaluj závislosti**

```bash
npm init -y
npm install astro @tailwindcss/vite tailwindcss
npm pkg set scripts.dev="astro dev" scripts.build="astro build" scripts.preview="astro preview" type="module"
```

- [ ] **Step 2: Vytvor `astro.config.mjs`**

```js
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  vite: {
    plugins: [tailwindcss()],
  },
});
```

- [ ] **Step 3: Vytvor `tsconfig.json`**

```json
{
  "extends": "astro/tsconfigs/base"
}
```

- [ ] **Step 4: Vytvor `src/styles/global.css` s dizajnovými tokenmi**

```css
@import "tailwindcss";

@theme {
  --font-heading: "Poppins", sans-serif;
  --font-body: "Inter", sans-serif;
  --color-brand: #859f54;
  --color-brand-dark: #3f4f23;
  --color-pill: #7e9d45;
}
```

- [ ] **Step 5: Vytvor `src/layouts/Layout.astro`**

```astro
---
import '../styles/global.css';

interface Props {
  title: string;
  description: string;
}

const { title, description } = Astro.props;
---

<!doctype html>
<html lang="sk" class="scroll-smooth">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>{title}</title>
    <meta name="description" content={description} />
    <link rel="icon" type="image/webp" href="/images/logo-hedox.webp" />
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Inter:ital,wght@0,300;0,400;0,700;1,400&family=Poppins:ital,wght@0,300;0,400;0,600;0,700;1,400&display=swap"
      rel="stylesheet"
    />
    <!-- TRACKING: sem neskôr vložiť Meta Pixel / GA4 -->
  </head>
  <body class="font-body text-neutral-900 antialiased">
    <slot />
    <!-- TRACKING: sem neskôr vložiť noscript varianty meracích kódov -->
  </body>
</html>
```

- [ ] **Step 6: Vytvor dočasný `src/pages/index.astro`**

```astro
---
import Layout from '../layouts/Layout.astro';
---

<Layout
  title="Znížte náklady s fúkanou izoláciou"
  description="Ušetrite až 30 % na účtoch za kúrenie vďaka našej fúkanej izolácii. Rýchla a čistá realizácia s garanciou kvality. Kontaktujte nás ešte dnes!"
>
  <h1 class="font-heading text-brand">Hedox</h1>
</Layout>
```

- [ ] **Step 7: Vytvor `netlify.toml`**

```toml
[build]
  command = "npm run build"
  publish = "dist"
```

- [ ] **Step 8: Doplň `.gitignore`** — pridaj na koniec existujúceho súboru:

```
.astro/
```

(`node_modules/`, `dist/`, `.netlify/`, `.DS_Store` už v ňom sú.)

- [ ] **Step 9: Over build**

Run: `npm run build`
Expected: build prebehne bez chýb, vznikne `dist/index.html`.

Run: `grep -o 'font-heading\|Hedox' dist/index.html | sort -u`
Expected: vypíše `Hedox` (a trieda font-heading je v HTML).

- [ ] **Step 10: Commit**

```bash
git add package.json package-lock.json astro.config.mjs tsconfig.json src netlify.toml .gitignore
git commit -m "Základná štruktúra Astro projektu s Tailwind a Netlify konfiguráciou"
```

---

### Task 2: Assety zo živého webu + centrálny obsah

**Files:**
- Create: `public/images/` (6 súborov), `ai/vzor/index.html` (referenčné HTML), `src/data/content.ts`

**Interfaces:**
- Produces: `src/data/content.ts` s named exportmi `hero`, `trustItems`, `sluzby`, `benefity`, `postup`, `referencie`, `faq`, `formular`, `realizacie`, `footer` presne v tvare uvedenom nižšie. Obrázky: `/images/logo-hedox.webp`, `/images/hero-bg.png`, `/images/realizacia-01.webp` … `realizacia-04.webp`.

- [ ] **Step 1: Stiahni referenčné HTML a obrázky** (UA hlavička je povinná, viď Global Constraints)

```bash
UA="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36"
mkdir -p public/images ai/vzor
curl -sL -A "$UA" "https://go.hedox.sk/" -o ai/vzor/index.html
curl -s -A "$UA" "https://images.assets-landingi.com/uc/0740dab6-679b-4dc4-9e4c-bc6518ba155f/hedoxlogodark02.webp" -o public/images/logo-hedox.webp
curl -s -A "$UA" "https://images.assets-landingi.com/uc/2e74680a-7cba-4551-9cbb-6cfc7721a1e7/ChatGPTImage226202622_15_30.png" -o public/images/hero-bg.png
curl -s -A "$UA" "https://images.assets-landingi.com/uc/2675ce1f-3ac2-49bf-99cb-c02771170eca/gallery01.webp" -o public/images/realizacia-01.webp
curl -s -A "$UA" "https://images.assets-landingi.com/uc/8a5f5a63-9107-4e6f-80f7-0da3e575fe71/gallery02.webp" -o public/images/realizacia-02.webp
curl -s -A "$UA" "https://images.assets-landingi.com/uc/950435a9-11a3-4e1e-911b-35e6416f652a/gallery20.webp" -o public/images/realizacia-03.webp
curl -s -A "$UA" "https://images.assets-landingi.com/uc/f1a7c10c-f260-406e-8c10-55b30e841513/gallery14.webp" -o public/images/realizacia-04.webp
```

- [ ] **Step 2: Over stiahnuté súbory**

Run: `ls -la public/images/ && file public/images/* && wc -c ai/vzor/index.html`
Expected: 6 obrázkov, `file` ukáže „Web/P image“ resp. „PNG image data“ (NIE HTML — 93-bajtový súbor by znamenal 403), `index.html` má > 40 000 bajtov.

- [ ] **Step 3: Vytvor `src/data/content.ts`** — kompletný obsah, texty nemeniť:

```ts
export const hero = {
  nadpisZelena: 'až 30 % nákladov',
  nadpisPred: 'Ušetrite ',
  nadpisPo: ' na vykurovanie vďaka fúkanej izolácii',
  podnadpis:
    'Profesionálne zateplenie podkrovia, stropov a väzníkových striech bez tepelných mostov. Cenovú ponuku vám pripravíme do 24 hodín.',
  cta: 'Získať cenovú ponuku',
};

export const trustItems = [
  { ikona: 'dom', text: 'Viac ako 300 úspešných realizácií' },
  { ikona: 'medaila', text: '10 rokov skúseností' },
  { ikona: 'stit', text: 'Certifikované materiály A1' },
  { ikona: 'hodiny', text: 'Nezáväzná kalkulácia do 24 hodín' },
] as const;

export const sluzby = {
  nadpis: 'Naše služby',
  podnadpis: 'Vyberieme najvhodnejší spôsob izolácie podľa konštrukcie vášho domu.',
  karty: [
    {
      ikona: 'vietor',
      titulok: 'Fúkaná izolácia',
      text: 'Zateplenie šikmých a plochých striech, väzníkových aj trámových konštrukcií bez vzniku tepelných mostov.',
    },
    {
      ikona: 'stit',
      titulok: 'Parozábrana',
      text: 'Montáž špeciálnej fólie, ktorá chráni izoláciu a krov pred vlhkosťou a vznikom plesní.',
    },
    {
      ikona: 'vrstvy',
      titulok: 'Sadrokartónové stropy',
      text: 'Profesionálna inštalácia stropných podhľadov pre dosiahnutie dokonale rovných a hladkých povrchov.',
    },
  ],
};

export const benefity = {
  nadpis: 'Prečo si vybrať fúkanú izoláciu?',
  polozky: [
    'Dokonale vyplní aj ťažko dostupné miesta',
    'Výborné tepelné a akustické vlastnosti',
    'Eliminuje tepelné mosty a úniky tepla',
    'Rýchla montáž bez veľkého stavebného zásahu',
    'Znižuje náklady na vykurovanie',
    'Čistá realizácia bez zbytočného odpadu',
  ],
};

export const postup = {
  nadpis: 'Ako postupujeme?',
  kroky: [
    {
      titulok: '1. Diagnostika a návrh',
      text: 'Nezáväzne posúdime váš dom a navrhneme vhodné riešenie. Vyberieme správny materiál aj hrúbku izolácie.',
    },
    {
      titulok: '2. Presná cenová kalkulácia',
      text: 'Na základe zamerania pripravíme presnú cenovú ponuku. Bez skrytých poplatkov.',
    },
    {
      titulok: '3. Realizácia za 1 deň',
      text: 'V dohodnutý termín izoláciu aplikujeme. Práce trvajú približne 3–5 hodín a prebiehajú čisto.',
    },
    {
      titulok: '4. Odovzdanie a garancia',
      text: 'Skontrolujeme kvalitu a odovzdáme hotovú realizáciu. Priestor zostáva čistý a pripravený na používanie.',
    },
  ],
};

export const referencie = {
  nadpis: 'Čo o nás hovoria zákazníci',
  cta: 'Získať cenovú ponuku',
  polozky: [
    {
      text: 'S firmou Hedox som bol maximálne spokojný. Realizovali u nás fúkanú izoláciu podkrovia a výsledok je výborný. Prístup pracovníkov bol profesionálny, všetko prebehlo rýchlo a bez problémov. Odporúčam ich každému, kto hľadá kvalitnú izoláciu.',
      meno: 'Marek K.',
      mesto: 'Trnava',
    },
    {
      text: 'Objednali sme si od Hedoxu montáž parozábrany a fúkanej izolácie do našej novostavby. Oceňujem ich odborné poradenstvo a precíznu prácu. Po realizácii sme zaznamenali výrazné zlepšenie tepelného komfortu v dome.',
      meno: 'Lucia S.',
      mesto: 'Špačince',
    },
    {
      text: 'Hedox nám pomohol s izoláciou stropu v staršom rodinnom dome. Boli veľmi flexibilní a prispôsobili sa našim časovým možnostiam. Práca bola vykonaná rýchlo a kvalitne. S výsledkom sme veľmi spokojní.',
      meno: 'Peter M.',
      mesto: 'Senec',
    },
  ],
};

export const faq = {
  nadpis: 'Často kladené otázky',
  polozky: [
    {
      otazka: 'Je odborná konzultácia a zameranie naozaj zadarmo?',
      odpoved:
        'Áno, všetko začína nezáväzným dopytom. Technik s vami prekonzultuje stavbu a navrhne optimálny materiál, hrúbku izolácie aj ďalší postup.',
    },
    {
      otazka: 'Musím sa na realizáciu špeciálne pripravovať alebo vypratávať pôjd?',
      odpoved:
        'Vo väčšine prípadov nie. Proces je navrhnutý tak, aby bol čistý a izoláciu privádzame hadicou priamo z auta. Konkrétny rozsah príprav vám spresníme pri obhliadke alebo po posúdení projektu.',
    },
    {
      otazka: 'Koľko trvá samotné zateplenie rodinného domu?',
      odpoved:
        'Samotné fúkanie izolácie trvá priemerne 3 až 5 hodín. Väčšinu realizácií vieme zvládnuť za 1 deň.',
    },
    {
      otazka: 'Je materiál bezpečný z hľadiska požiaru?',
      odpoved:
        'Pri návrhu riešenia vyberáme vhodný materiál podľa typu konštrukcie a požiadaviek stavby. Po dokončení prác odovzdávame aj certifikát o zhode materiálu.',
    },
    {
      otazka: 'Kedy pocítim reálnu úsporu na energiách?',
      odpoved:
        'Tepelný komfort v dome pocítite hneď po realizácii. Reálna úspora na energiách sa prejaví najmä počas najbližšej vykurovacej sezóny.',
    },
  ],
};

export const formular = {
  nadpis: 'Získajte nezáväznú cenovú ponuku',
  podnadpis:
    'Vyplňte formulár a do 24 hodín vás kontaktujeme s návrhom riešenia a orientačnou kalkuláciou.',
  polia: {
    meno: 'Meno',
    email: 'Email',
    telefon: 'Telefónne číslo',
    mesto: 'Mesto / Obec',
    typProjektu: 'Izoláciu hľadám na',
    typStrechy: 'Typ strechy',
    poznamka: 'Poznámka',
  },
  typProjektuMoznosti: ['Novostavba', 'Rekonštrukcia'],
  typStrechyMoznosti: ['Väzníkový krov', 'Dutý trámový strop', 'Pultová strecha', 'Iné'],
  gdprText: 'Súhlasím so spracovaním osobných údajov',
  gdprUrl: 'https://www.hedox.sk/ochrana-sukromia/',
  odoslat: 'Získať cenovú ponuku zdarma',
  uspech: 'Ďakujeme! Vaša žiadosť bola odoslaná. Ozveme sa vám do 24 hodín.',
  chyba: 'Odoslanie sa nepodarilo. Skúste to prosím znova alebo nám zavolajte.',
};

export const realizacie = {
  nadpis: 'Naše realizácie',
  obrazky: [
    '/images/realizacia-01.webp',
    '/images/realizacia-02.webp',
    '/images/realizacia-03.webp',
    '/images/realizacia-04.webp',
  ],
};

export const footer = {
  ochranaText: 'Ochrana osobných údajov',
  ochranaUrl: 'https://www.hedox.sk/ochrana-sukromia/',
  kontaktNadpis: 'Kontakt',
  email: 'fukanaizolacia.kmety@gmail.com',
  telefony: ['0905 882 201', '0908 830 038'],
  sidlo: 'Sídlo: Jarná 75, 919 43 Cífer',
};
```

- [ ] **Step 4: Over, že content.ts sa kompiluje**

Run: `npm run build`
Expected: build bez chýb (content.ts sa zatiaľ nikde nepoužíva, kompiluje sa ale v rámci projektu — chyba syntaxe by build zhodila... pre istotu aj `npx tsc --noEmit src/data/content.ts` musí prejsť bez výstupu).

- [ ] **Step 5: Commit**

```bash
git add public/images ai/vzor/index.html src/data/content.ts
git commit -m "Assety zo živého webu a centrálny obsah stránky"
```

---

### Task 3: Header, Hero, TrustBar

**Files:**
- Create: `src/components/Header.astro`, `src/components/Hero.astro`, `src/components/TrustBar.astro`
- Modify: `src/pages/index.astro`

**Interfaces:**
- Consumes: `hero`, `trustItems` z `src/data/content.ts`; `/images/logo-hedox.webp`, `/images/hero-bg.png`.
- Produces: komponenty bez Props, CTA odkazy vedú na `#formular` (id formulárovej sekcie z Tasku 6).

- [ ] **Step 1: Vytvor `src/components/Header.astro`**

```astro
<header class="bg-white">
  <div class="mx-auto flex max-w-6xl items-center justify-between px-4 py-4">
    <a href="/">
      <img src="/images/logo-hedox.webp" alt="Hedox – fúkaná izolácia" class="h-14 w-auto" />
    </a>
    <a
      href="#formular"
      class="rounded-lg bg-neutral-800 px-6 py-3 text-sm font-semibold text-white transition hover:bg-neutral-700"
    >
      Kontaktujte nás
    </a>
  </div>
</header>
```

- [ ] **Step 2: Vytvor `src/components/Hero.astro`**

```astro
---
import { hero } from '../data/content';
---

<section
  class="bg-brand bg-cover bg-right"
  style="background-image: url('/images/hero-bg.png')"
>
  <div class="mx-auto max-w-6xl px-4 py-24 md:py-32">
    <div class="max-w-xl">
      <h1 class="font-heading text-4xl font-bold leading-tight text-neutral-900 md:text-5xl">
        {hero.nadpisPred}<span class="text-brand">{hero.nadpisZelena}</span>{hero.nadpisPo}
      </h1>
      <p class="mt-6 text-lg text-neutral-700">{hero.podnadpis}</p>
      <a
        href="#formular"
        class="mt-10 inline-block rounded-lg bg-brand-dark px-8 py-4 font-heading font-semibold text-white transition hover:opacity-90"
      >
        {hero.cta}
      </a>
    </div>
  </div>
</section>
```

- [ ] **Step 3: Vytvor `src/components/TrustBar.astro`** — ikony sú inline SVG (stroke `currentColor`, zelené cez `text-brand`):

```astro
---
import { trustItems } from '../data/content';

const ikony: Record<string, string> = {
  dom: `<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="h-12 w-12"><path stroke-linecap="round" stroke-linejoin="round" d="m2.25 12 8.954-8.955c.44-.439 1.152-.439 1.591 0L21.75 12M4.5 9.75v10.125c0 .621.504 1.125 1.125 1.125H9.75v-4.875c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125V21h4.125c.621 0 1.125-.504 1.125-1.125V9.75" /></svg>`,
  medaila: `<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="h-12 w-12"><path stroke-linecap="round" stroke-linejoin="round" d="M16.5 18.75h-9m9 0a3 3 0 0 1 3 3h-15a3 3 0 0 1 3-3m9 0v-3.375c0-.621-.503-1.125-1.125-1.125h-.871M7.5 18.75v-3.375c0-.621.504-1.125 1.125-1.125h.872m5.007 0H9.497m5.007 0a7.454 7.454 0 0 1-.982-3.172M9.497 14.25a7.454 7.454 0 0 0 .981-3.172M5.25 4.236c-.982.143-1.954.317-2.916.52A6.003 6.003 0 0 0 7.73 9.728M5.25 4.236V4.5c0 2.108.966 3.99 2.48 5.228M5.25 4.236V2.721C7.456 2.41 9.71 2.25 12 2.25c2.291 0 4.545.16 6.75.47v1.516M7.73 9.728a6.726 6.726 0 0 0 2.748 1.35m8.272-6.842V4.5c0 2.108-.966 3.99-2.48 5.228m2.48-5.492a46.32 46.32 0 0 1 2.916.52 6.003 6.003 0 0 1-5.395 4.972m0 0a6.726 6.726 0 0 1-2.749 1.35m0 0a6.772 6.772 0 0 1-3.044 0" /></svg>`,
  stit: `<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="h-12 w-12"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75 11.25 15 15 9.75m-3-7.036A11.959 11.959 0 0 1 3.598 6 11.99 11.99 0 0 0 3 9.749c0 5.592 3.824 10.29 9 11.623 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.571-.598-3.751h-.152c-3.196 0-6.1-1.248-8.25-3.285Z" /></svg>`,
  hodiny: `<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="h-12 w-12"><path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" /></svg>`,
};
---

<section class="bg-white">
  <div class="mx-auto grid max-w-6xl grid-cols-2 gap-10 px-4 py-16 md:grid-cols-4">
    {
      trustItems.map((item) => (
        <div class="flex flex-col items-center gap-4 text-center">
          <span class="text-brand" set:html={ikony[item.ikona]} />
          <p class="font-heading font-semibold">{item.text}</p>
        </div>
      ))
    }
  </div>
</section>
```

- [ ] **Step 4: Zapoj komponenty do `src/pages/index.astro`** (nahraď dočasný obsah):

```astro
---
import Layout from '../layouts/Layout.astro';
import Header from '../components/Header.astro';
import Hero from '../components/Hero.astro';
import TrustBar from '../components/TrustBar.astro';
---

<Layout
  title="Znížte náklady s fúkanou izoláciou"
  description="Ušetrite až 30 % na účtoch za kúrenie vďaka našej fúkanej izolácii. Rýchla a čistá realizácia s garanciou kvality. Kontaktujte nás ešte dnes!"
>
  <Header />
  <Hero />
  <TrustBar />
</Layout>
```

- [ ] **Step 5: Over build a obsah**

Run: `npm run build && grep -c 'Ušetrite\|Kontaktujte nás\|úspešných realizácií' dist/index.html`
Expected: build OK, grep nájde ≥ 3 výskyty.

- [ ] **Step 6: Vizuálna kontrola**

Run: `npm run dev` (na pozadí), otvor `http://localhost:4321`, porovnaj hornú časť s `ai/vzor/go_hedox.png` (logo vľavo, tmavé tlačidlo vpravo, nadpis so zelenou časťou „až 30 % nákladov“, tmavá olivová CTA, 4 trust položky so zelenými ikonami). Potom dev server ukonči.

- [ ] **Step 7: Commit**

```bash
git add src/components/Header.astro src/components/Hero.astro src/components/TrustBar.astro src/pages/index.astro
git commit -m "Sekcie header, hero a trust bar"
```

---

### Task 4: Naše služby + Prečo si vybrať fúkanú izoláciu

**Files:**
- Create: `src/components/Sluzby.astro`, `src/components/Benefity.astro`
- Modify: `src/pages/index.astro`

**Interfaces:**
- Consumes: `sluzby`, `benefity` z `src/data/content.ts`.

- [ ] **Step 1: Vytvor `src/components/Sluzby.astro`** — sekcia s gradientom z tmavosivej do bielej:

```astro
---
import { sluzby } from '../data/content';

const ikony: Record<string, string> = {
  vietor: `<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.8" stroke="currentColor" class="h-10 w-10"><path stroke-linecap="round" stroke-linejoin="round" d="M3.75 9h9.75a2.25 2.25 0 1 0-2.25-2.25M3.75 12.75h13.5a2.25 2.25 0 1 1-2.25 2.25M3.75 16.5h6a1.875 1.875 0 1 1-1.875 1.875" /></svg>`,
  stit: `<svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24" class="h-10 w-10"><path fill-rule="evenodd" d="M12.516 2.17a.75.75 0 0 0-1.032 0 11.209 11.209 0 0 1-7.877 3.08.75.75 0 0 0-.722.515A12.74 12.74 0 0 0 2.25 9.75c0 5.942 4.064 10.933 9.563 12.348a.749.749 0 0 0 .374 0c5.499-1.415 9.563-6.406 9.563-12.348 0-1.39-.223-2.73-.635-3.985a.75.75 0 0 0-.722-.516l-.143.001c-2.996 0-5.717-1.17-7.734-3.08Z" clip-rule="evenodd" /></svg>`,
  vrstvy: `<svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24" class="h-10 w-10"><path d="M11.644 1.59a.75.75 0 0 1 .712 0l9.75 5.25a.75.75 0 0 1 0 1.32l-9.75 5.25a.75.75 0 0 1-.712 0l-9.75-5.25a.75.75 0 0 1 0-1.32l9.75-5.25Z" /><path d="m3.265 10.602 7.668 4.129a2.25 2.25 0 0 0 2.134 0l7.668-4.13 1.37.739a.75.75 0 0 1 0 1.32l-9.75 5.25a.75.75 0 0 1-.71 0l-9.75-5.25a.75.75 0 0 1 0-1.32l1.37-.738Z" /><path d="m10.933 19.231-7.668-4.13-1.37.739a.75.75 0 0 0 0 1.32l9.75 5.25c.221.12.489.12.71 0l9.75-5.25a.75.75 0 0 0 0-1.32l-1.37-.738-7.668 4.13a2.25 2.25 0 0 1-2.134-.001Z" /></svg>`,
};
---

<section class="bg-[linear-gradient(180deg,#444444_0%,#ffffff_65%)]">
  <div class="mx-auto max-w-6xl px-4 py-20">
    <h2 class="text-center font-heading text-4xl font-bold text-white">{sluzby.nadpis}</h2>
    <p class="mt-4 text-center text-white">{sluzby.podnadpis}</p>
    <div class="mt-12 grid gap-8 md:grid-cols-3">
      {
        sluzby.karty.map((karta) => (
          <div class="rounded-2xl bg-white p-8 text-center shadow-lg">
            <span class="mx-auto inline-block text-brand" set:html={ikony[karta.ikona]} />
            <h3 class="mt-4 font-heading text-xl font-bold">{karta.titulok}</h3>
            <p class="mt-4 text-sm text-neutral-600">{karta.text}</p>
          </div>
        ))
      }
    </div>
  </div>
</section>
```

- [ ] **Step 2: Vytvor `src/components/Benefity.astro`** — zelené pilulky s fajkou, 2 stĺpce:

```astro
---
import { benefity } from '../data/content';
---

<section class="bg-white">
  <div class="mx-auto max-w-5xl px-4 py-20">
    <h2 class="text-center font-heading text-4xl font-bold">{benefity.nadpis}</h2>
    <div class="mt-12 grid gap-4 md:grid-cols-2">
      {
        benefity.polozky.map((text) => (
          <div class="flex items-center gap-3 rounded-lg bg-pill px-6 py-4 font-semibold text-white shadow">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor" class="h-5 w-5 shrink-0 opacity-60">
              <path stroke-linecap="round" stroke-linejoin="round" d="m4.5 12.75 6 6 9-13.5" />
            </svg>
            {text}
          </div>
        ))
      }
    </div>
  </div>
</section>
```

- [ ] **Step 3: Pridaj do `src/pages/index.astro`** — importuj `Sluzby` a `Benefity` a vlož ich za `<TrustBar />`:

```astro
import Sluzby from '../components/Sluzby.astro';
import Benefity from '../components/Benefity.astro';
```
```astro
  <TrustBar />
  <Sluzby />
  <Benefity />
```

- [ ] **Step 4: Over build a obsah**

Run: `npm run build && grep -c 'Naše služby\|Parozábrana\|Dokonale vyplní' dist/index.html`
Expected: build OK, ≥ 3 výskyty.

- [ ] **Step 5: Commit**

```bash
git add src/components/Sluzby.astro src/components/Benefity.astro src/pages/index.astro
git commit -m "Sekcie služby a benefity fúkanej izolácie"
```

---

### Task 5: Ako postupujeme + Referencie

**Files:**
- Create: `src/components/Postup.astro`, `src/components/Referencie.astro`
- Modify: `src/pages/index.astro`

**Interfaces:**
- Consumes: `postup`, `referencie` z `src/data/content.ts`.

- [ ] **Step 1: Vytvor `src/components/Postup.astro`** — zelený gradient (91°, #859f54 → biela), 4 biele karty:

```astro
---
import { postup } from '../data/content';
---

<section class="bg-[linear-gradient(91deg,#859f54,#ffffff)]">
  <div class="mx-auto max-w-6xl px-4 py-20">
    <h2 class="text-center font-heading text-4xl font-bold">{postup.nadpis}</h2>
    <div class="mt-12 grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
      {
        postup.kroky.map((krok) => (
          <div class="rounded-xl bg-white p-6 text-center shadow-lg">
            <h3 class="font-heading text-sm font-bold">{krok.titulok}</h3>
            <p class="mt-3 text-xs leading-relaxed text-neutral-600">{krok.text}</p>
          </div>
        ))
      }
    </div>
  </div>
</section>
```

- [ ] **Step 2: Vytvor `src/components/Referencie.astro`**:

```astro
---
import { referencie } from '../data/content';
---

<section class="bg-white">
  <div class="mx-auto max-w-6xl px-4 py-20">
    <h2 class="text-center font-heading text-4xl font-bold">{referencie.nadpis}</h2>
    <div class="mt-12 grid gap-6 md:grid-cols-3">
      {
        referencie.polozky.map((ref) => (
          <div class="rounded-xl border border-neutral-200 bg-white p-8 shadow-sm">
            <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24" class="h-8 w-8 text-brand">
              <path d="M4.583 17.321C3.553 16.227 3 15 3 13.011c0-3.5 2.457-6.637 6.03-8.188l.893 1.378c-3.335 1.804-3.987 4.145-4.247 5.621.537-.278 1.24-.375 1.929-.311 1.804.167 3.226 1.648 3.226 3.489a3.5 3.5 0 0 1-3.5 3.5c-1.073 0-2.099-.49-2.748-1.179Zm10 0C13.553 16.227 13 15 13 13.011c0-3.5 2.457-6.637 6.03-8.188l.893 1.378c-3.335 1.804-3.987 4.145-4.247 5.621.537-.278 1.24-.375 1.929-.311 1.804.167 3.226 1.648 3.226 3.489a3.5 3.5 0 0 1-3.5 3.5c-1.073 0-2.099-.49-2.748-1.179Z" />
            </svg>
            <p class="mt-4 text-sm leading-relaxed text-neutral-700">{ref.text}</p>
            <p class="mt-5 font-heading font-semibold text-brand">{ref.meno}</p>
            <p class="text-sm text-neutral-500">{ref.mesto}</p>
          </div>
        ))
      }
    </div>
    <div class="mt-12 text-center">
      <a
        href="#formular"
        class="inline-block rounded-lg bg-brand-dark px-8 py-4 font-heading font-semibold text-white transition hover:opacity-90"
      >
        {referencie.cta}
      </a>
    </div>
  </div>
</section>
```

- [ ] **Step 3: Pridaj do `src/pages/index.astro`** za `<Benefity />`:

```astro
import Postup from '../components/Postup.astro';
import Referencie from '../components/Referencie.astro';
```
```astro
  <Benefity />
  <Postup />
  <Referencie />
```

- [ ] **Step 4: Over build a obsah**

Run: `npm run build && grep -c 'Ako postupujeme\|Marek K.\|Špačince' dist/index.html`
Expected: build OK, ≥ 3 výskyty.

- [ ] **Step 5: Commit**

```bash
git add src/components/Postup.astro src/components/Referencie.astro src/pages/index.astro
git commit -m "Sekcie postup realizácie a referencie zákazníkov"
```

---

### Task 6: FAQ accordion

**Files:**
- Create: `src/components/Faq.astro`
- Modify: `src/pages/index.astro`

**Interfaces:**
- Consumes: `faq` z `src/data/content.ts`.

- [ ] **Step 1: Vytvor `src/components/Faq.astro`** — natívny `<details>`/`<summary>` accordion (bez JS, prístupný):

```astro
---
import { faq } from '../data/content';
---

<section class="bg-[linear-gradient(180deg,#ececec_0%,#ffffff_40%)]">
  <div class="mx-auto max-w-3xl px-4 py-20">
    <h2 class="text-center font-heading text-4xl font-bold">{faq.nadpis}</h2>
    <div class="mt-12 space-y-3">
      {
        faq.polozky.map((polozka) => (
          <details class="group rounded-lg bg-white px-6 py-4 shadow">
            <summary class="flex cursor-pointer list-none items-center justify-between gap-4 font-heading font-semibold [&::-webkit-details-marker]:hidden">
              {polozka.otazka}
              <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" class="h-5 w-5 shrink-0 transition-transform group-open:rotate-180">
                <path stroke-linecap="round" stroke-linejoin="round" d="m19.5 8.25-7.5 7.5-7.5-7.5" />
              </svg>
            </summary>
            <p class="pt-4 text-sm leading-relaxed text-neutral-600">{polozka.odpoved}</p>
          </details>
        ))
      }
    </div>
  </div>
</section>
```

- [ ] **Step 2: Pridaj do `src/pages/index.astro`** za `<Referencie />`:

```astro
import Faq from '../components/Faq.astro';
```
```astro
  <Referencie />
  <Faq />
```

- [ ] **Step 3: Over build a obsah**

Run: `npm run build && grep -c 'Často kladené otázky\|vypratávať pôjd' dist/index.html`
Expected: build OK, ≥ 2 výskyty.

- [ ] **Step 4: Commit**

```bash
git add src/components/Faq.astro src/pages/index.astro
git commit -m "Sekcia často kladené otázky s accordionom"
```

---

### Task 7: Kontaktný formulár (Netlify Forms + GDPR + reCAPTCHA + AJAX)

**Files:**
- Create: `src/components/KontaktForm.astro`
- Modify: `src/pages/index.astro`

**Interfaces:**
- Consumes: `formular` z `src/data/content.ts`.
- Produces: sekcia s `id="formular"` (cieľ všetkých CTA odkazov). Netlify formulár s názvom `cenova-ponuka`.

**Dôležité detaily Netlify Forms:**
- Formulár je v statickom HTML (Astro prerender), takže Netlify ho pri deployi zaregistruje z atribútu `data-netlify="true"` — skrytý duplikát netreba.
- AJAX POST musí ísť na `/` s `Content-Type: application/x-www-form-urlencoded` a telo musí obsahovať `form-name=cenova-ponuka` (preto hidden input).
- `data-netlify-recaptcha="true"` + prázdny `<div data-netlify-recaptcha="true">` — Netlify pri post-processingu doplní widget aj skript. Widget vloží `g-recaptcha-response` priamo do formulára, takže `FormData` ho zachytí automaticky. Lokálne widget neexistuje — kód s tým musí počítať (neblokovať odoslanie lokálne, Netlify validuje na serveri).

- [ ] **Step 1: Vytvor `src/components/KontaktForm.astro`**

```astro
---
import { formular } from '../data/content';

const inputClass =
  'w-full rounded-md border border-neutral-200 bg-white px-4 py-3 text-sm shadow-sm placeholder:text-neutral-400 focus:border-brand focus:outline-none';
---

<section id="formular" class="bg-white scroll-mt-8">
  <div class="mx-auto max-w-2xl px-4 py-20">
    <h2 class="text-center font-heading text-4xl font-bold">{formular.nadpis}</h2>
    <p class="mx-auto mt-4 max-w-lg text-center text-neutral-600">{formular.podnadpis}</p>

    <form
      id="cenova-ponuka-form"
      name="cenova-ponuka"
      method="POST"
      action="/"
      data-netlify="true"
      data-netlify-honeypot="bot-field"
      data-netlify-recaptcha="true"
      class="mt-12 space-y-5"
    >
      <input type="hidden" name="form-name" value="cenova-ponuka" />
      <p class="hidden">
        <label>Nevypĺňajte: <input name="bot-field" /></label>
      </p>

      <div>
        <label for="meno" class="mb-1 block font-heading font-semibold">{formular.polia.meno}</label>
        <input id="meno" name="meno" type="text" required placeholder={formular.polia.meno} class={inputClass} />
      </div>

      <div>
        <label for="email" class="mb-1 block font-heading font-semibold">{formular.polia.email}</label>
        <input id="email" name="email" type="email" required placeholder={formular.polia.email} class={inputClass} />
      </div>

      <div>
        <label for="telefon" class="mb-1 block font-heading font-semibold">{formular.polia.telefon}</label>
        <input id="telefon" name="telefon" type="tel" required placeholder={formular.polia.telefon} class={inputClass} />
      </div>

      <div>
        <label for="mesto" class="mb-1 block font-heading font-semibold">{formular.polia.mesto}</label>
        <input id="mesto" name="mesto-obec" type="text" required placeholder={formular.polia.mesto} class={inputClass} />
      </div>

      <div>
        <label for="typ-projektu" class="mb-1 block font-heading font-semibold">{formular.polia.typProjektu}</label>
        <select id="typ-projektu" name="typ-projektu" class={inputClass}>
          {formular.typProjektuMoznosti.map((moznost) => <option value={moznost}>{moznost}</option>)}
        </select>
      </div>

      <div>
        <label for="typ-strechy" class="mb-1 block font-heading font-semibold">{formular.polia.typStrechy}</label>
        <select id="typ-strechy" name="typ-strechy" class={inputClass}>
          {formular.typStrechyMoznosti.map((moznost) => <option value={moznost}>{moznost}</option>)}
        </select>
      </div>

      <div>
        <label for="poznamka" class="mb-1 block font-heading font-semibold">{formular.polia.poznamka}</label>
        <textarea id="poznamka" name="poznamka" rows="4" class={inputClass}></textarea>
      </div>

      <label class="flex items-start gap-3 text-sm text-neutral-700">
        <input type="checkbox" name="gdpr-suhlas" value="áno" required class="mt-0.5 h-4 w-4 accent-brand" />
        <span>
          <a href={formular.gdprUrl} target="_blank" rel="noopener" class="underline hover:text-brand">
            {formular.gdprText}
          </a>
        </span>
      </label>

      <div data-netlify-recaptcha="true"></div>

      <button
        type="submit"
        class="w-full rounded-md bg-pill py-4 font-heading font-semibold text-white transition hover:opacity-90 disabled:opacity-50"
      >
        {formular.odoslat}
      </button>
    </form>

    <div id="form-uspech" hidden class="mt-12 rounded-lg bg-pill/10 p-8 text-center">
      <p class="font-heading text-xl font-semibold text-brand-dark">{formular.uspech}</p>
    </div>

    <p id="form-chyba" hidden class="mt-4 rounded-md bg-red-50 p-4 text-center text-sm text-red-700">
      {formular.chyba}
    </p>
  </div>
</section>

<script>
  const form = document.getElementById('cenova-ponuka-form') as HTMLFormElement | null;

  form?.addEventListener('submit', async (event) => {
    event.preventDefault();
    const tlacidlo = form.querySelector<HTMLButtonElement>('button[type="submit"]');
    const chyba = document.getElementById('form-chyba');
    if (tlacidlo) tlacidlo.disabled = true;
    chyba?.setAttribute('hidden', '');

    try {
      const data = new FormData(form);
      const body = new URLSearchParams(
        [...data.entries()].map(([kluc, hodnota]) => [kluc, String(hodnota)])
      );
      const odpoved = await fetch('/', {
        method: 'POST',
        headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
        body: body.toString(),
      });
      if (!odpoved.ok) throw new Error(`HTTP ${odpoved.status}`);

      // Budúci prechod na samostatnú ďakovaciu stránku = nahradiť nasledujúce
      // dva riadky za: window.location.href = '/dakujeme';
      form.hidden = true;
      document.getElementById('form-uspech')?.removeAttribute('hidden');
    } catch {
      chyba?.removeAttribute('hidden');
      if (tlacidlo) tlacidlo.disabled = false;
    }
  });
</script>
```

- [ ] **Step 2: Pridaj do `src/pages/index.astro`** za `<Faq />`:

```astro
import KontaktForm from '../components/KontaktForm.astro';
```
```astro
  <Faq />
  <KontaktForm />
```

- [ ] **Step 3: Over build a Netlify atribúty**

Run: `npm run build && grep -o 'data-netlify="true"\|data-netlify-recaptcha="true"\|netlify-honeypot="bot-field"\|name="form-name"\|gdpr-suhlas' dist/index.html | sort | uniq -c`
Expected: build OK; `data-netlify="true"` ×1, `data-netlify-recaptcha="true"` ×2 (form atribút + div), `netlify-honeypot` ×1, `form-name` ×1, `gdpr-suhlas` ×1.

- [ ] **Step 4: Over JS logiku v prehliadači**

Spusti `npm run dev`, otvor stránku, vyplň formulár bez GDPR checkboxu → prehliadač zablokuje odoslanie (required). Zaškrtni GDPR, odošli → lokálne fetch na `/` vráti 200 (dev server) a zobrazí sa ďakovacia správa namiesto formulára. Ukonči dev server. (Skutočné doručenie do Netlify Forms sa dá overiť až po deployi — poznamenať používateľovi pri odovzdaní.)

- [ ] **Step 5: Commit**

```bash
git add src/components/KontaktForm.astro src/pages/index.astro
git commit -m "Kontaktný formulár s Netlify Forms, GDPR súhlasom a reCAPTCHA"
```

---

### Task 8: Realizácie, Footer, finálna kompozícia a vizuálna kontrola

**Files:**
- Create: `src/components/Realizacie.astro`, `src/components/Footer.astro`
- Modify: `src/pages/index.astro`

**Interfaces:**
- Consumes: `realizacie`, `footer` z `src/data/content.ts`; obrázky `realizacia-01..04.webp`, `logo-hedox.webp`.

- [ ] **Step 1: Vytvor `src/components/Realizacie.astro`**

```astro
---
import { realizacie } from '../data/content';
---

<section class="bg-white">
  <div class="mx-auto max-w-6xl px-4 py-20">
    <h2 class="text-center font-heading text-4xl font-bold">{realizacie.nadpis}</h2>
    <div class="mt-12 grid grid-cols-2 gap-4 md:grid-cols-4">
      {
        realizacie.obrazky.map((src, i) => (
          <img
            src={src}
            alt={`Realizácia fúkanej izolácie ${i + 1}`}
            class="aspect-[3/4] w-full rounded-lg object-cover"
            loading="lazy"
          />
        ))
      }
    </div>
  </div>
</section>
```

- [ ] **Step 2: Vytvor `src/components/Footer.astro`**

```astro
---
import { footer } from '../data/content';
---

<footer class="bg-[linear-gradient(180deg,#ffffff,#cdcdcd)]">
  <div class="mx-auto flex max-w-6xl flex-col gap-10 px-4 py-16 md:flex-row md:items-start md:justify-between">
    <div>
      <img src="/images/logo-hedox.webp" alt="Hedox – fúkaná izolácia" class="h-14 w-auto" />
      <a
        href={footer.ochranaUrl}
        target="_blank"
        rel="noopener"
        class="mt-4 inline-block text-sm text-neutral-600 underline hover:text-brand"
      >
        {footer.ochranaText}
      </a>
    </div>
    <div class="text-sm">
      <p class="font-heading text-xs font-semibold uppercase tracking-wide text-neutral-500">
        {footer.kontaktNadpis}
      </p>
      <a href={`mailto:${footer.email}`} class="mt-3 block font-semibold hover:text-brand">
        {footer.email}
      </a>
      {footer.telefony.map((cislo) => (
        <a href={`tel:${cislo.replaceAll(' ', '')}`} class="mt-1 block font-semibold hover:text-brand">
          {cislo}
        </a>
      ))}
      <p class="mt-3 text-neutral-600">{footer.sidlo}</p>
    </div>
  </div>
</footer>
```

- [ ] **Step 3: Finálny `src/pages/index.astro`** — over, že po všetkých úpravách vyzerá takto:

```astro
---
import Layout from '../layouts/Layout.astro';
import Header from '../components/Header.astro';
import Hero from '../components/Hero.astro';
import TrustBar from '../components/TrustBar.astro';
import Sluzby from '../components/Sluzby.astro';
import Benefity from '../components/Benefity.astro';
import Postup from '../components/Postup.astro';
import Referencie from '../components/Referencie.astro';
import Faq from '../components/Faq.astro';
import KontaktForm from '../components/KontaktForm.astro';
import Realizacie from '../components/Realizacie.astro';
import Footer from '../components/Footer.astro';
---

<Layout
  title="Znížte náklady s fúkanou izoláciou"
  description="Ušetrite až 30 % na účtoch za kúrenie vďaka našej fúkanej izolácii. Rýchla a čistá realizácia s garanciou kvality. Kontaktujte nás ešte dnes!"
>
  <Header />
  <Hero />
  <TrustBar />
  <Sluzby />
  <Benefity />
  <Postup />
  <Referencie />
  <Faq />
  <KontaktForm />
  <Realizacie />
  <Footer />
</Layout>
```

- [ ] **Step 4: Over build a kompletnosť sekcií**

Run: `npm run build && for t in "Ušetrite" "Naše služby" "Prečo si vybrať" "Ako postupujeme" "hovoria zákazníci" "kladené otázky" "nezáväznú cenovú" "Naše realizácie" "Cífer"; do grep -q "$t" dist/index.html && echo "OK: $t" || echo "CHÝBA: $t"; done`
Expected: 9× `OK`, žiadne `CHÝBA`.

- [ ] **Step 5: Vizuálna kontrola celej stránky**

Spusti `npm run dev`, prejdi stránku zhora nadol a porovnaj so `ai/vzor/go_hedox.png` (poradie sekcií, farby, gradienty: služby tmavá→biela, postup zelená→biela, footer biela→sivá; klik na CTA scrolluje na formulár; accordion sa otvára/zatvára; mobilná šírka — sekcie sa stackujú). Odchýlky oprav priamo v komponentoch. Ukonči dev server.

- [ ] **Step 6: Commit**

```bash
git add src/components/Realizacie.astro src/components/Footer.astro src/pages/index.astro
git commit -m "Sekcie realizácie a footer, finálna kompozícia stránky"
```

---

## Po dokončení plánu (mimo tasky — na pokyn používateľa)

1. Používateľ dodá URL GitHub repozitára → `git remote add origin <URL> && git push -u origin main`.
2. V Netlify: New site from Git → vybrať repo (build nastavenia si Netlify prečíta z `netlify.toml`).
3. Po prvom deployi: v Netlify UI Forms → overiť, že formulár `cenova-ponuka` je zaregistrovaný, nastaviť e-mail notifikácie; otestovať odoslanie vrátane reCAPTCHA na produkcii.
4. Pripomenúť používateľovi: preklep „Telefónne číšlo“ z originálu bol opravený na „Telefónne číslo“; mailto vo footeri opravený na skutočný e-mail; hero má na produkcii fotografiu na pozadí (v referenčnom screenshote sa nenačítala kvôli lazy loadingu).
