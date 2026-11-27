# Multi-faith editions: design notes

Status: **not implemented.** These notes are for a later build.

## Goal

At setup, the user chooses their tradition. The recovery structure stays the same for everyone. Each tradition supplies its own map of the ascent, its virtues, its path of repentance, its teaching on marriage and the dignity of women, and its sacred texts.

| Edition | Ladder map (5 phases) | Weekly virtues (7) | Return | Remedy after a fall |
|---|---|---|---|---|
| Judaism (current) | Five worlds: Asiyah → Adam Kadmon | Seven lower sefirot | Teshuvah | Tikkun HaKlali |
| Islam | Stages of the nafs | Seven heavens of the Mi'raj, or seven virtues | Tawbah | Wudu, two rak'ahs, istighfar |
| Sikhi | Five khands of Japji Sahib | Virtues against the five thieves | Ardas and seeking forgiveness | Naam simran, Japji Sahib |
| Bahá'í Faith | Spiritual stages, from the Four Valleys or a curated arc | The Seven Valleys | Prayer and turning to God alone | Long Healing Prayer, obligatory prayer |
| Interfaith | Neutral ladder: the five rungs of the ascent | Shared virtues | Repentance | Prayer from the user's chosen traditions |

**Moral monotheism is the frame for every edition.** Every edition rests on one God, a moral law, the real possibility of return, the sanctity of marriage, and the dignity of women. The interfaith edition draws only on what these traditions share. It makes no claim that they are the same.

## Architecture: one core engine, separate faith packs

### The core does not change between editions

- The tier weights, the weekly ceiling (35 → 0, one point a week), and the content cap per phase
- Placement, the real-calendar week, check-in, missed days, the week review, repeating a week and stepping back
- The SOS timer, PIN lock, journal, triggers and fences
- The covenant mechanism: one commitment added per phase, kept for the rest of the climb, reviewed weekly, with a return flow when broken
- Moments of light, the glow per phase, the ladder visual

### A faith pack supplies all the content

Each pack is a single data file, for example `packs/islam.json`.

```jsonc
{
  "id": "islam",
  "name": "Islam",
  "appName": "Mi'raj",                       // per-edition name; "Sulam" for Judaism
  "script": { "lang": "ar", "dir": "rtl", "font": "Amiri" },
  "vocab": {                                  // words the core UI uses
    "repentance": "Tawbah", "companion": "Accountability brother",
    "covenant": "Amanah", "reflection": "Muhasabah", "remedy": "Salat al-Tawbah"
  },
  "phases": [                                 // exactly 5, bottom to top
    { "name": "Nafs al-Ammara", "native": "النفس الأمارة", "en": "The soul that commands to evil",
      "desc": "...", "color": "#D07A3A",
      "teaching": { "title": "...", "body": "..." },   // dignity of women / marriage
      "commit": { "single": "...", "married": "..." } }
  ],
  "weeks": [                                  // exactly 7 virtues, one per week of a phase
    { "name": "Sabr", "native": "صبر", "theme": "Patience", "practice": "..." }
  ],
  "returnFlow": [                             // the steps of the repentance flow
    { "key": "regret", "label": "Nadam · Remorse", "prompt": "..." },
    { "key": "cease",  "label": "Iqla' · Stopping", "prompt": "..." },
    { "key": "resolve","label": "'Azm · Resolve", "prompt": "..." }
  ],
  "remedy": { "title": "...", "items": [ { "label": "...", "url": "..." } ] },
  "sourceLink": "https://quran.com/{ref}",    // turns a quote's ref into a link
  "quotes": [
    { "native": "...", "en": "...", "src": "Qur'an 24:30", "ref": "24/30",
      "tags": ["urge", "daily"], "verified": false, "reviewer": null }
  ]
}
```

The current Jewish content (`WORLDS`, `SEFIROT`, `KEDUSHAH`, `QUOTES`, `TIKKUN`, the teshuvah steps) moves into `packs/judaism.json` unchanged. The core then reads everything faith-specific from the active pack.

### Setup

1. A new first step: **"Which tradition guides you?"**
2. If the user picks **Interfaith**, they also choose which traditions to draw quotes from (all by default).
3. The rest of setup is unchanged.
4. The tradition can be changed later from the Journal. Progress is kept, because the ladder position is tradition-neutral.

### Rendering

- `quoteHTML` uses `pack.script` for the native line: `lang`, `dir` and font. Arabic and Persian are right to left. Gurmukhi is left to right.
- Fonts to add, all on Google Fonts: **Amiri** or **Noto Naskh Arabic** for Arabic and Persian; **Noto Sans Gurmukhi** or **Mukta Mahee** for Gurmukhi.
- Each pack's phase colours feed `WORLD_COLORS`.

## Islam (Mi'raj)

**Ladder map: the stages of the nafs**, from classical tasawwuf. Choose five of the traditional seven:

1. **An-Nafs al-Ammara** (12:53), the soul that commands to evil
2. **An-Nafs al-Lawwama** (75:2), the self-reproaching soul
3. **An-Nafs al-Mulhama** (91:7–8), the inspired soul
4. **An-Nafs al-Mutma'inna** (89:27), the soul at peace
5. **An-Nafs ar-Radiya / al-Mardiyya** (89:28), well-pleased and well-pleasing

The summit could be the Mi'raj itself: an ascent toward nearness to God.

**Weekly virtues (7):** for example tawbah, sabr, shukr, taqwa, haya', ikhlas and tawakkul. Alternatively, the seven heavens of the Mi'raj, each with a prophet the Prophet ﷺ met there (Adam, 'Isa and Yahya, Yusuf, Idris, Harun, Musa, Ibrahim). **Yusuf in the third heaven** parallels Yosef HaTzaddik closely.

**Return flow (tawbah):** the conditions given by the scholars:
- remorse (nadam)
- stopping the sin now (iqla')
- firm resolve not to return ('azm)

The flow ends with Qur'an 39:53: do not despair of the mercy of Allah.

**Remedy after a fall:** wudu; two rak'ahs of Salat al-Tawbah; istighfar (Sayyid al-Istighfar); Ayat al-Kursi.

**Marriage and the dignity of women (candidate sources):**
- Qur'an 30:21: tranquillity, affection (mawaddah) and mercy (rahmah) between spouses
- Qur'an 2:187: spouses are garments for one another
- Qur'an 24:30–31: lowering the gaze (ghadd al-basar) and guarding chastity
- Qur'an 17:32: do not approach zina
- Bukhari 5066: whoever is able should marry, for it lowers the gaze; whoever cannot should fast, for it is a shield
- The hadith of the seven who are shaded: a man invited by a woman of beauty and position who says "I fear Allah"
- Sahih International and similar translations are copyrighted. Render quotes in our own words, or license a translation.

**In the urge and after a fall (candidates):**
- Qur'an 12:23–33: Yusuf's restraint, "Prison is dearer to me" (12:33)
- Qur'an 79:40–41: whoever restrains the soul from desire
- Tirmidhi 2499: every child of Adam sins, and the best of sinners are those who repent
- Jihad al-nafs as the greater struggle (the hadith's authenticity is debated, so present it as a teaching of the scholars)

**Links:** `quran.com/{surah}/{ayah}`, `sunnah.com/{collection}:{number}`.

**Covenant (amanah), by phase, as a draft:**
1. End haram relationships
2. Delete dating apps; seek marriage through family, community or a trusted intermediary
3. Lower the gaze
4. Learn the rights of spouses
5. Pray for a righteous spouse, or for your spouse, daily

Married users get the equivalents for strengthening the marriage.

## Sikhi

**Ladder map: the five khands of Japji Sahib (pauris 34–37).** These are five realms that match the five phases exactly:

1. **Dharam Khand**, the realm of duty and righteous action
2. **Gian Khand**, the realm of knowledge
3. **Saram Khand**, the realm of effort and spiritual shaping
4. **Karam Khand**, the realm of grace
5. **Sach Khand**, the realm of truth, where the Formless One dwells

**Framing:** of the five thieves (kaam, krodh, lobh, moh, ahankar), **kaam**, lust, is the struggle this edition addresses. Weekly virtues are the counter-virtues:
- sat (truth)
- santokh (contentment)
- daya (compassion)
- nimrata (humility)
- pyaar (love)
- sanjam (self-control)
- seva

**Return flow:**
- Ardas seeking forgiveness
- Acknowledging the fault before Waheguru
- Resolve, with the Guru's grace

For Amritdhari Sikhs, adultery is one of the four bajjar kurehits. A serious lapse involves the Panj Pyare (pesh hona). The app should explain this and point to the sangat and Gurdwara, never replace them.

**Remedy:** naam simran; Japji Sahib; Sukhmani Sahib; sangat.

**Marriage and the dignity of women (candidate sources):**
- Guru Amar Das, Ang 788: those who merely sit together are not husband and wife; husband and wife are those with *one light in two bodies* (ek jot doe moorti). This is the "one soul in two bodies" teaching in Sikh form.
- Guru Nanak, Asa di Var, Ang 473: "So kyo manda aakhiai, jit jameh rajaan": why call her lesser, from whom kings are born?
- Sukhmani Sahib (around Ang 274): not gazing at another's wife with lust (par triya roop na pekhai netr). Verify the exact line.
- Anand Karaj: the four laavan as the soul's union with the Divine, mirrored in marriage

**Links:** SikhiToTheMax or GurbaniNow by Ang and line. Show Gurmukhi, transliteration and English.

## Bahá'í Faith

**Ladder map:** use a curated five-phase arc, for example:
1. Search
2. Love
3. Knowledge
4. Unity
5. Contentment and Detachment

Alternatively, use the Four Valleys plus a summit.

**Weekly virtues: the Seven Valleys of Bahá'u'lláh** (Search, Love, Knowledge, Unity, Contentment, Wonderment, True Poverty and Absolute Nothingness). These replace the seven sefirot one for one.

**Return flow:**
- Bahá'ís confess to God alone, never to another person, so the confession step is a private prayer.
- Then resolve, and the Long Healing Prayer or obligatory prayer.
- The weekly review maps directly to the Hidden Words (Arabic, no. 31): bring thyself to account each day.

**Marriage, chastity and women (candidate sources):**
- Shoghi Effendi, *The Advent of Divine Justice*: the call to a "chaste and holy life"
- Bahá'u'lláh's marriage prayer: marriage as a fortress for well-being and salvation
- 'Abdu'l-Bahá: humanity has two wings, men and women, and cannot fly until both are equally strong
- Chastity before marriage and fidelity within it, and parental consent to marriage

**Links:** the Bahá'í Reference Library (bahai.org/library). Check its terms before quoting at length.

## Interfaith edition (moral monotheism, including Unitarian Universalist users)

**Who it's for:** people formed by more than one tradition, Unitarian Universalists, and seekers who want a God-centred path without one denomination's vocabulary.

**Note:** Unitarian Universalism is non-creedal, and many UUs are not monotheists. This edition **deliberately keeps the moral-monotheist frame**, as you asked: one God, moral law, return, the covenant of marriage, the dignity of women. Say so honestly in its description.

**Design:**
- **Ladder map:** five neutral phases (for example Action, Feeling, Mind, Spirit, Will), each illustrated by the matching phase from every pack, such as "Asiyah · Nafs al-Ammara · Dharam Khand".
- **Weekly virtues:** a shared set: honesty, restraint, compassion, perseverance, humility, faithfulness, gratitude.
- **Quotes:** drawn from all installed packs. Each quote is **labelled with its tradition and source** and never blended into a single voice. The user can filter by tradition.
- **Return flow:** regret, confession to God, resolve, using neutral vocabulary ("return", "covenant", "companion").
- **Remedy:** the user chooses from the remedies of their selected traditions.

## Open questions

- **Christianity:** worth adding as its own edition? Candidate sources include 1 Corinthians 6:19–20 (the body as a temple), Matthew 5:28, Ephesians 5:25, and the Song of Songs.
- **Naming:** one app with several editions, or separate app names per tradition (Sulam, Mi'raj, …)?
- **Women users:** the current content is written for men. Each pack needs a women's track: content, covenant wording and teachings.

## Content review: required before release

Every pack needs a qualified reviewer before it ships:
- **Judaism:** a rav
- **Islam:** an 'alim or imam
- **Sikhi:** a granthi or a scholar of the Rehat Maryada
- **Bahá'í:** someone familiar with the Writings and the Universal House of Justice's guidance
- **Interfaith:** each tradition's reviewer signs off on how that tradition is presented there

In the data:
- Every quote carries `verified` and `reviewer` fields.
- Unverified quotes show a marker in development builds and are excluded from release builds.
- Never AI-generate a quote, and never paraphrase without marking it as a summary.

## Suggested order of work

1. Move the Jewish content into `packs/judaism.json` and make the core read from the active pack. No visible change.
2. Add the tradition step to setup and switching in the Journal.
3. Build the Islam pack (the largest audience), get it reviewed, and ship it.
4. Build the Sikhi pack. Its five khands map so cleanly that it is the easiest second pack.
5. Build the Bahá'í pack.
6. Build the interfaith pack last, since it draws on all the others.
