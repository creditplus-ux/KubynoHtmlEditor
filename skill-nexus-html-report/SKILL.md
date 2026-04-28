---
name: skill-nexus-html-report
description: >
  Designer de rapoarte HTML compatibile Nexus ERP. Folosește acest skill ori de câte ori
  utilizatorul cere să creeze, convertească, reproiecteze sau îmbunătățească un raport HTML
  pentru Nexus ERP — inclusiv contracte de muncă, decizii, adeverințe, sau orice alt document
  generat din ERP. Triggers: "raport Nexus", "html Nexus", "contract de munca html", "template
  ERP", "formular html nexus", "converteste word in html nexus", "redesign raport", "câmpuri
  #%...%#", "rapoarte_coloane_view".
---

# Nexus ERP — HTML Report Designer

## Contextul general

Nexus ERP randează rapoarte ca documente HTML statice. Valorile dinamice sunt injectate prin
placeholder-e cu sintaxa:

```txt
#%denumire%#
```

`denumire` trebuie să existe în coloana `denumire` din `rapoarte_coloane_view` pentru raportul
curent (nu codul intern `camp`).

## Reguli HTML pentru Nexus ERP

### Ce funcționează sigur
- Inline styles (`style="..."`) pe elemente.
- Tag-uri simple: `<div>`, `<span>`, `<table>`, `<tr>`, `<td>`, `<b>`, `<strong>`, `<i>`, `<em>`,
  `<br>`, `<hr>`, `<center>`.
- Proprietăți: `font-family`, `font-size`, `color`, `text-align`, `font-weight`, `line-height`,
  `padding`, `margin`, `border`.
- CSS în `<style>` în `<head>`.
- Charset UTF-8 explicit.

### Ce de evitat
- Resurse externe (CDN, fonturi, imagini externe).
- JavaScript.
- Flexbox/Grid fără confirmare de compatibilitate.
- `@media` queries.

### Regula critică de spațiere
Nu folosi `<br>` între secțiuni de bloc (`div`, `p`, secțiuni), deoarece Nexus poate dubla
spațierea.

Reguli recomandate:
- Spațiere între secțiuni: `margin-top` (max 10–12px).
- Paragrafe: `<p style="margin: 4px 0;">`.
- Separator „și”: un singur `<p style="text-align:center; margin: 6px 0;">și</p>`.
- `<br>` doar în inline / în interior de `<td>`.
- Fără `<br><br>`.
- `line-height` sigur: 1.45–1.6.

## Structura recomandată

```html
<html>
<head>
  <meta charset="UTF-8">
  <style>
    body { font-family: Arial, sans-serif; font-size: 14px; line-height: 1.6; color: #222; margin: 20px 40px; }
    .header-firma { font-size: 13px; margin-bottom: 4px; }
    .titlu-doc { text-align: center; font-size: 20px; font-weight: bold; margin: 20px 0 6px; }
    .subtitlu-doc { text-align: center; font-size: 14px; margin-bottom: 20px; }
    .sectiune { margin-top: 14px; }
    .label-sectiune { font-weight: bold; }
    .rand-semnaturi { margin-top: 30px; }
  </style>
</head>
<body>
  <!-- Continut raport -->
</body>
</html>
```

## Workflow de lucru

1. **Înțelege modelul clientului** (HTML existent / descriere verbală / text din Word).
2. **Validează câmpurile**:
   - Extrage toate `#%...%#`.
   - Verifică fiecare `denumire` în lista câmpurilor raportului.
   - Semnalează explicit câmpurile lipsă/invalide.
3. **Construiește HTML-ul**:
   - Păstrează fidelitatea documentului original.
   - Curățenie de cod, ierarhie vizuală clară, placeholders integrați logic.
4. **Validare finală**:
   - Câmpuri valide, UTF-8, fără resurse externe, HTML bine format.

## Câmpuri speciale tip tabel

Câmpurile precum `#%Tabel stagiu cotizare%#`, `#%Tabel venituri brute%#`,
`#%Tabel somaj tehnic%#` se inserează ca bloc direct, fără `<table>` extern:

```html
<div style="margin-top:16px;">#%Tabel stagiu cotizare%#</div>
```

## Adaptare pentru alte rapoarte

Solicită câmpurile din:

```sql
SELECT denumire, camp, provenienta
FROM rapoarte_coloane_view
WHERE ID_RAP = X
ORDER BY linia, coloana
```

Aplică același workflow pentru orice tip de raport Nexus (HR sau comercial).

## Referințe

- `references/fields_rap59.md` — câmpuri raport RAP 59 (contract muncă).
- `references/fields_rap1697.md` — exemplu listă câmpuri pentru raport ID 1697.
