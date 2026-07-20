# Hedox landing page — kópia go.hedox.sk v Astro

**Dátum:** 2026-07-20
**Stav:** schválené

## Cieľ

Presná vizuálna a obsahová kópia existujúcej landing page https://go.hedox.sk/ (pôvodne postavenej na platforme Landingi), prerobená do vlastného kódu. Referenčný screenshot: `ai/vzor/go_hedox.png`.

## Technológie

- **Astro 5** — statický output (`output: 'static'`), žiadny SSR
- **Tailwind CSS v4** — cez `@tailwindcss/vite` plugin, bez čistého CSS
- **Vanilla JS** — pre FAQ accordion, AJAX odoslanie formulára a prípadné mobilné menu; žiadny React/Vue
- **Netlify** — hosting, deploy z GitHub repozitára, formulár cez natívne Netlify Forms
- **GitHub** — remote sa napojí až po vytvorení základnej štruktúry projektu (na pokyn používateľa)

## Štruktúra projektu

```
src/
  components/
    Header.astro        # logo + tlačidlo „Kontaktujte nás“ (scroll na formulár)
    Hero.astro          # hlavný nadpis, podnadpis, CTA „Získať cenovú ponuku“
    TrustBar.astro      # 4 ukazovatele dôveryhodnosti
    Sluzby.astro        # sekcia „Naše služby“ — karty
    Benefity.astro      # „Prečo si vybrať fúkanú izoláciu?“ — zelená sekcia, 6 bodov
    Postup.astro        # „Ako postupujeme?“ — 4 očíslované kroky
    Referencie.astro    # „Čo o nás hovoria zákazníci“
    Faq.astro           # „Často kladené otázky“ — accordion
    KontaktForm.astro   # formulár (Netlify Forms)
    Realizacie.astro    # „Naše realizácie“ — galéria fotiek
    Footer.astro        # kontaktné údaje, logo, link na ochranu osobných údajov
  data/
    content.ts          # všetky texty stránky na jednom mieste
  layouts/
    Layout.astro        # <head>, fonty, meta, vyhradené miesto pre tracking kódy
  pages/
    index.astro         # skladá sekcie v poradí podľa predlohy
public/
  images/               # obrázky stiahnuté zo živého webu
netlify.toml            # build nastavenia pre Netlify
```

## Obsah a assety

- Všetky texty, obrázky (WebP), logo a ikony sa stiahnu priamo zo živého webu go.hedox.sk (obsah patrí klientovi Hedox).
- Fonty sa identifikujú zo živého webu a načítajú cez Google Fonts, prípadne self-host, podľa toho, čo web používa.
- Texty sa uložia do `src/data/content.ts`; komponenty ich len renderujú.
- Rozsah: **iba hlavná stránka**. Podstránka ochrany osobných údajov sa nerobí — footer link povedie na pôvodnú URL.

## Formulár (Netlify Forms)

- Polia podľa predlohy: meno, e-mail, telefón, mesto/obec, typ projektu (select: novostavba/rekonštrukcia), typ strechy (select: väzník/dutý trám/pultová/iné), poznámka. Presné labely a povinnosť polí sa prevezmú zo živého webu.
- `data-netlify="true"` + honeypot pole (`netlify-honeypot`) proti spamu.
- Keďže odoslanie je cez AJAX (fetch POST na `/`), v HTML bude aj statická verzia formulára (skrytá alebo prítomná v build outpute), aby Netlify formulár pri builde zaregistrovalo.
- **Po odoslaní:** formulár sa skryje a na jeho mieste sa zobrazí ďakovacia správa (bez presmerovania). Kód sa napíše tak, aby prechod na samostatnú `/dakujeme` stránku v budúcnosti bola zmena jedného riadku (redirect po úspešnom fetchi).
- Chybový stav: pri zlyhaní odoslania sa zobrazí chybová hláška a formulár ostane vyplnený.

## Tracking

Zatiaľ žiadne meracie kódy. V `Layout.astro` bude komentárom vyznačené miesto v `<head>` a pred `</body>`, kam sa neskôr vložia Meta Pixel / GA4.

## Git workflow

- Lokálny git repozitár od začiatku, commit po každej väčšej zmene.
- Commity bez akýchkoľvek zmienok o AI (globálne pravidlo používateľa).
- GitHub remote a Netlify prepojenie sa nastaví neskôr na pokyn používateľa.

## Overenie

- Lokálne `npm run dev` / `npm run build` bez chýb.
- Vizuálne porovnanie so screenshotom `ai/vzor/go_hedox.png` sekcia po sekcii.
- Test formulára: honeypot, validácie, AJAX odoslanie (plne overiteľné až po nasadení na Netlify — Netlify Forms nefungujú lokálne, čo sa pri odovzdaní explicitne uvedie).
