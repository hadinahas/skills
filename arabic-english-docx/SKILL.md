---
name: arabic-english-docx
description: >
  Apply this skill whenever creating or fixing Word (.docx) documents that contain
  Arabic text, mixed Arabic/English content, or any RTL document.
  Triggers: Arabic text in docx, RTL paragraphs, mixed-direction sentences,
  boxes showing instead of Arabic letters, text direction issues, diacritics as boxes,
  italic Arabic as boxes. Always use proactively when generating any Arabic docx.

  WRITING RULES FOR ARABIC (apply to ALL Arabic content, not just docx):
  - No diacritics (tashkeel) anywhere — strip all harakat
  - No italic on Arabic text — ever
  - Common loanwords stay in English (Onboarding, OKR, KPI, Playbook, etc) — never transliterated
  - English acronyms and brand names inside Arabic sentences stay in English
  - Arabic is always right-aligned, RTL
  - Business Arabic — no literary or formal register, plain and direct
---

# Arabic / English Mixed-Direction DOCX — Complete Rules

All rules discovered and fixed in production across real bilingual business documents
(onboarding feedback, operational guides, weekly check-in docs, leadership materials).

---

## WRITING RULES (apply everywhere — docx, pptx, presentations, all content)

1. **No diacritics** — strip all tashkeel/harakat characters (U+0610–U+061A, U+064B–U+065F).
   Business Arabic never uses them. They cause rendering failures in Word on Office 2024.

2. **No italic on Arabic** — ever. Traditional Arabic and Calibri lack italic variants.
   Word renders italic Arabic as boxes. Use bold for emphasis instead.

3. **Common loanwords stay in English** — "Onboarding", not "الأونبوردينغ". Keep them as Latin words inline.

4. **English acronyms and brand names stay English** — OKR, OKRs, NPS, KPI, Playbook, Scorecard,
   Google Sheets, Buddy, Mentor, P&C, Induction, L10, WhatsApp, etc.
   Do not transliterate brand names or management terms into Arabic.

5. **Direct, plain register** — no literary Arabic, no formal MSA flourishes.
   Write as you would speak in a professional Levantine business meeting.

---

## DOCX TECHNICAL FIXES

### Problem 1 — Arabic text renders as boxes (font)

**Fix:** `w:cs="Calibri"` on all Arabic runs. Never Arial for complex script.

```xml
<w:rFonts w:ascii="Arial" w:cs="Calibri" w:eastAsia="Arial" w:hAnsi="Arial"/>
```

**In docx-js:**
```javascript
new TextRun({
  text: "النص العربي",
  font: { name: "Arial", cs: "Calibri" },
  language: { bidirectional: "ar-SA" }
})
```

---

### Problem 2 — Diacritics render as boxes

**Fix:** Strip all diacritics from source text.

```python
import re
DIACRITICS = re.compile(
  r'[\u0610-\u061A\u064B-\u065F\u0670\u06D6-\u06DC\u06DF-\u06E4\u06E7\u06E8\u06EA-\u06ED]'
)
clean = DIACRITICS.sub('', text)
```

---

### Problem 3 — English words flip to wrong side in Arabic sentences

**Fix — two parts:**

**Part A:** Split mixed runs into separate sub-runs:
- Arabic segments → RTL run with `<w:rtl w:val="1"/>`
- English/number segments → LTR run with `<w:rtl w:val="0"/>` and `w:val="en-US"`

```python
LTR_PATTERN = re.compile(
  r'(Onboarding|OKR|OKRs|NPS|KPI|KPIs|P&C|Google Sheets?|'
  r'Playbook|Scorecard|Buddy|Mentor|WhatsApp|[A-Z]{2,}|[0-9]+)'
)
```

> Extend the pattern with any company-specific acronyms or product names you need to preserve in English.

**Part B:** Add explicit `<w:rtl w:val="1"/>` to ALL Arabic runs in RTL paragraphs.
Prevents direction inheritance from preceding LTR runs.

---

### Problem 4 — Missing language tag

**Required on every Arabic run:**
```xml
<w:lang w:bidi="ar-SA"/>
```

---

### Problem 5 — Italic renders as boxes

**Fix:** Remove `<w:i/>` and `<w:iCs/>` from all Arabic runs entirely.

```python
for el_name in ["i", "iCs"]:
    el = rpr.find(tag(el_name))
    if el is not None:
        rpr.remove(el)
```

---

## COMPLETE POST-BUILD FIX SCRIPT

Apply after any docx is built to catch all issues in one pass:

```python
from lxml import etree
import re, copy

W = "http://schemas.openxmlformats.org/wordprocessingml/2006/main"
def tag(n): return f"{{{W}}}{n}"

DIACRITICS = re.compile(
  r'[\u0610-\u061A\u064B-\u065F\u0670\u06D6-\u06DC\u06DF-\u06E4\u06E7\u06E8\u06EA-\u06ED]'
)
LTR_PATTERN = re.compile(
  r'(Onboarding|OKR|OKRs|NPS|KPI|KPIs|P&C|Google Sheets?|'
  r'Playbook|Scorecard|Buddy|Mentor|WhatsApp|Induction|[A-Z]{2,}|[0-9]+)'
)

def fix_arabic_docx(doc_xml_path):
    tree = etree.parse(doc_xml_path)
    root = tree.getroot()

    # 1. Strip diacritics
    for t in root.iter(tag("t")):
        if t.text:
            t.text = DIACRITICS.sub('', t.text)

    # 2. Fix all Arabic runs
    for r in root.iter(tag("r")):
        rpr = r.find(tag("rPr"))
        if rpr is None: continue
        lang = rpr.find(tag("lang"))
        if lang is None or lang.get(f"{{{W}}}bidi") != "ar-SA": continue
        # Fix cs font
        rFonts = rpr.find(tag("rFonts"))
        if rFonts is not None:
            rFonts.set(f"{{{W}}}cs", "Calibri")
        # Remove italic
        for el_name in ["i", "iCs"]:
            el = rpr.find(tag(el_name))
            if el is not None: rpr.remove(el)

    # 3. Split mixed runs + add explicit RTL
    for p in root.iter(tag("p")):
        ppr = p.find(tag("pPr"))
        if ppr is None or ppr.find(tag("bidi")) is None: continue
        for r in [el for el in list(p) if el.tag == tag("r")]:
            t_el = r.find(tag("t"))
            if t_el is None or not t_el.text: continue
            if not LTR_PATTERN.search(t_el.text): continue
            # Split and rebuild runs
            parts = []
            last = 0
            for m in LTR_PATTERN.finditer(t_el.text):
                if m.start() > last:
                    parts.append((t_el.text[last:m.start()], False))
                parts.append((m.group(), True))
                last = m.end()
            if last < len(t_el.text):
                parts.append((t_el.text[last:], False))
            if len(parts) <= 1: continue
            rpr = r.find(tag("rPr"))
            idx = list(p).index(r)
            p.remove(r)
            for i, (seg, is_ltr) in enumerate(parts):
                if not seg: continue
                new_r = etree.Element(tag("r"))
                if rpr is not None:
                    new_rpr = copy.deepcopy(rpr)
                    rtl_el = new_rpr.find(tag("rtl"))
                    if rtl_el is None:
                        rtl_el = etree.SubElement(new_rpr, tag("rtl"))
                    rtl_el.set(f"{{{W}}}val", "0" if is_ltr else "1")
                    new_r.append(new_rpr)
                new_t = etree.SubElement(new_r, tag("t"))
                new_t.text = seg
                if seg[0] == ' ' or seg[-1] == ' ':
                    new_t.set("{http://www.w3.org/XML/1998/namespace}space", "preserve")
                p.insert(idx + i, new_r)

    # 4. Add explicit RTL to all Arabic runs in RTL paragraphs
    for p in root.iter(tag("p")):
        ppr = p.find(tag("pPr"))
        if ppr is None or ppr.find(tag("bidi")) is None: continue
        for r in p.findall(tag("r")):
            rpr = r.find(tag("rPr"))
            if rpr is None: continue
            lang = rpr.find(tag("lang"))
            is_arabic = lang is not None and lang.get(f"{{{W}}}bidi") == "ar-SA"
            rtl_el = rpr.find(tag("rtl"))
            is_ltr = rtl_el is not None and rtl_el.get(f"{{{W}}}val") == "0"
            if is_arabic and not is_ltr and rtl_el is None:
                new_rtl = etree.SubElement(rpr, tag("rtl"))
                new_rtl.set(f"{{{W}}}val", "1")

    tree.write(doc_xml_path, xml_declaration=True, encoding="utf-8", standalone=True)
```

---

## CHECKLIST — Before delivering any Arabic docx

- [ ] All Arabic runs: `w:cs="Calibri"`
- [ ] All Arabic runs: `w:lang w:bidi="ar-SA"`
- [ ] All Arabic runs in RTL paragraphs: `<w:rtl w:val="1"/>`
- [ ] No `<w:i/>` or `<w:iCs/>` on Arabic runs
- [ ] No diacritics in any text
- [ ] English/numbers in Arabic sentences: split into LTR runs with `<w:rtl w:val="0"/>`
- [ ] All Arabic paragraphs: `<w:bidi/>` and `<w:jc w:val="right"/>`
- [ ] styles.xml docDefaults: `w:cs="Calibri"`
- [ ] Common loanwords (Onboarding, etc.) in English, not transliterated
- [ ] No italic anywhere on Arabic text

---

## DOCX-JS TEMPLATE (correct Arabic run)

```javascript
new Paragraph({
  bidirectional: true,
  alignment: AlignmentType.RIGHT,
  children: [
    new TextRun({
      text: "النص العربي",
      font: { name: "Arial", cs: "Calibri" },
      size: 20,
      color: "1A1A1A",
      language: { bidirectional: "ar-SA" }
      // Never: italic: true
    })
  ]
})
```

---

## PPTX — Arabic in presentations

Same font rules apply. In pptxgenjs:
```javascript
sl.addText("النص العربي", {
  fontFace: "Arial",
  // No italic
  align: "right",
  rtlMode: true,
  lang: "ar-SA"
});
```

For mixed Arabic/English runs in pptx, use rich text arrays with separate
objects per language segment — do not mix Arabic and English in one text string.
