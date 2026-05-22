---
name: tooltip
description: Create a Slidev Tooltip component from a csv-bibel.de verse reference
---

# Bible Verse Tooltip Generator

You receive a verse reference string in the format: `<book>-<chapter>#v<verse>`

Example input: `matthaeus-26#v39`

## Steps

1. **Build the URL**: Prepend `https://www.csv-bibel.de/bibel/` to the input string.
   - Example: `https://www.csv-bibel.de/bibel/matthaeus-26#v39`

2. **Fetch the page content**: Run the following command in the terminal to extract the verse text:

   ```bash
   curl -s "https://www.csv-bibel.de/bibel/<book>-<chapter>" | python3 -c "
   import sys, re
   html = sys.stdin.read()
   match = re.search(r'name=\"v<verse>\">(.*?)(?=name=\"v<nextVerse>\"|$)', html, re.DOTALL)
   if match:
       text = match.group(1)
       text = re.sub(r'<[^>]+>', '', text)
       text = re.sub(r'\b[A-Z]\d+(?::\d+)?[a-z]?\b', '', text)
       text = re.sub(r'^\s*\d+\s*', '', text)
       text = re.sub(r'\s+', ' ', text).strip()
       print(text)
   "
   ```

   Replace `<book>`, `<chapter>`, `<verse>`, and `<nextVerse>` (verse + 1) with the values parsed from the input. The output is the clean verse text ready to use in the `text` attribute.

3. **Build the display label**: Convert the reference into a short German Bible citation:
   - Book name mappings (input → label):
     - `1-mose` → `1. Mo`
     - `2-mose` → `2. Mo`
     - `3-mose` → `3. Mo`
     - `4-mose` → `4. Mo`
     - `5-mose` → `5. Mo`
     - `josua` → `Jos`
     - `richter` → `Ri`
     - `ruth` → `Rt`
     - `1-samuel` → `1. Sam`
     - `2-samuel` → `2. Sam`
     - `1-koenige` → `1. Kön`
     - `2-koenige` → `2. Kön`
     - `1-chronika` → `1. Chr`
     - `2-chronika` → `2. Chr`
     - `esra` → `Esra`
     - `nehemia` → `Neh`
     - `esther` → `Est`
     - `hiob` → `Hiob`
     - `psalm` → `Ps`
     - `sprueche` → `Spr`
     - `prediger` → `Pred`
     - `hohelied` → `Hld`
     - `jesaja` → `Jes`
     - `jeremia` → `Jer`
     - `klagelieder` → `Klgl`
     - `hesekiel` → `Hes`
     - `daniel` → `Dan`
     - `hosea` → `Hos`
     - `joel` → `Joel`
     - `amos` → `Am`
     - `obadja` → `Ob`
     - `jona` → `Jona`
     - `micha` → `Mi`
     - `nahum` → `Nah`
     - `habakuk` → `Hab`
     - `zephanja` → `Zeph`
     - `haggai` → `Hag`
     - `sacharja` → `Sach`
     - `maleachi` → `Mal`
     - `matthaeus` → `Mt`
     - `markus` → `Mk`
     - `lukas` → `Lk`
     - `johannes` → `Joh`
     - `apostelgeschichte` → `Apg`
     - `roemer` → `Röm`
     - `1-korinther` → `1. Kor`
     - `2-korinther` → `2. Kor`
     - `galater` → `Gal`
     - `epheser` → `Eph`
     - `philipper` → `Phil`
     - `kolosser` → `Kol`
     - `1-thessalonicher` → `1. Thes`
     - `2-thessalonicher` → `2. Thes`
     - `1-timotheus` → `1. Tim`
     - `2-timotheus` → `2. Tim`
     - `titus` → `Tit`
     - `philemon` → `Phlm`
     - `hebraer` → `Heb` *(URL uses `hebraeer` — two e's)*
     - `jakobus` → `Jak`
     - `1-petrus` → `1. Pet`
     - `2-petrus` → `2. Pet`
     - `1-johannes` → `1. Joh`
     - `2-johannes` → `2. Joh`
     - `3-johannes` → `3. Joh`
     - `judas` → `Jud`
     - `offenbarung` → `Off`
   - Split the segment before `#` by `-`. The chapter is always the last element; everything before it is the book name (rejoin with `-`). Example: `1-mose-2#v3` → book=`1-mose`, chapter=`2`. Example: `matthaeus-26#v39` → book=`matthaeus`, chapter=`26`.
   - The verse (or verse range) comes from the part after `#v`. A single number (e.g. `#v39`) refers to one verse. A range is written as `#v26-27` and refers to verses 26 through 27.
   - Format: `<BookAbbrev> <chapter>,<verse>` for a single verse, or `<BookAbbrev> <chapter>,<verseStart>-<verseEnd>` for a range.
   - Example: `matthaeus-26#v39` → `Mt 26,39`; `matthaeus-26#v26-27` → `Mt 26,26-27`

4. **Generate the Tooltip snippet**: Output the following format exactly:

```html
<Tooltip text="<fetched verse text>" href="<full URL>"><display label></Tooltip>
```

## Example

Input: `matthaeus-26#v39`

Output:
`<Tooltip text="Und er ging ein wenig weiter und fiel auf sein Angesicht und betete und sprach: Mein Vater, wenn es möglich ist, so gehe dieser Kelch an mir vorüber; doch nicht wie ich will, sondern wie du willst." href="https://www.csv-bibel.de/bibel/matthaeus-26#v39">Mt 26,39</Tooltip>`

## Important

- Output ONLY the raw `<Tooltip ...>` element — no code fences, no markdown, no explanation, no extra text before or after.
- The `text` attribute must contain the full verse text as found on the page, with Strong's numbers and verse number prefix removed.
- Do not add line breaks inside the Tooltip element.
- If the input contains a verse range like `#v26-27`, include the text of all verses from 26 to 27 concatenated. If only a single verse number is given (e.g. `#v26`), include only that verse.