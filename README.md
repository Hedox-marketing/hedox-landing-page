# Hedox – landing page

Landing page pre [Hedox](https://hedox-landing-page.netlify.app) – fúkanú izoláciu (zateplenie podkroví, stropov a väzníkových striech). Jednostránkový web s kontaktným formulárom na nezáväznú cenovú ponuku.
Originál web [hedox.sk](https://www.hedox.sk/)

## Technológie

- [Astro 5](https://astro.build) – statický web bez JS frameworku
- [Tailwind CSS 4](https://tailwindcss.com) – štýlovanie (cez `@tailwindcss/vite`)
- [Netlify](https://www.netlify.com) – hosting, deploy z GitHubu a natívne Netlify Forms s reCAPTCHA

## Lokálny vývoj

```bash
npm install
npm run dev      # dev server na http://localhost:4321
npm run build    # produkčný build do dist/
npm run preview  # náhľad produkčného buildu
```

## Štruktúra projektu

```
src/
  components/     # sekcie stránky (Hero, Sluzby, Faq, KontaktForm, ...)
  data/
    content.ts    # všetky texty webu na jednom mieste
  layouts/
    Layout.astro  # <head>, meta tagy, fonty, tracking kódy (GTM)
  pages/
    index.astro   # hlavná stránka – skladá sekcie
    dakujeme.astro# ďakovacia stránka po odoslaní formulára
public/
  images/         # obrázky, ikony, favicon
netlify.toml      # build nastavenia pre Netlify
```

Úprava textov = úprava `src/data/content.ts`, komponenty ich len renderujú.

## Formulár

Kontaktný formulár `cenova-ponuka` používa **Netlify Forms**:

- natívne odoslanie s presmerovaním na `/dakujeme`
- reCAPTCHA v2 (kľúče dodáva Netlify) + honeypot pole proti spamu
- povinný GDPR súhlas
- e-mail notifikácie sa nastavujú v Netlify: *Forms → Form notifications*

Formulár funguje **až na produkcii** – lokálne sa reCAPTCHA nezobrazuje a odoslania sa nikam neukladajú. Ak odoslanie nepríde medzi *Verified submissions*, skontroluj záložku *Spam submissions* a označ ho ako verified.

## Deploy

Každý push do vetvy `main` spustí automatický deploy na Netlify (build nastavenia číta z `netlify.toml`).

## SEO

- canonical URL + Open Graph / Twitter meta tagy (doména `go.hedox.sk`)
- sitemap (`/sitemap-index.xml`) + `robots.txt`
- štruktúrované dáta JSON-LD: `HomeAndConstructionBusiness` a `FAQPage`
- ďakovacia stránka má `noindex` a nie je v sitemape

## Tracking

- **Google Tag Manager** (`GTM-5Z64TM7`) je vložený v `src/layouts/Layout.astro` – skript v `<head>` a noscript varianta hneď za `<body>`, platí pre všetky stránky
- ďalšie meracie kódy (Meta Pixel, GA4) sa nasadzujú cez GTM kontajner, netreba ich pridávať do kódu
