# Singapore Employment & Education Visualisation
### AAI1001 Data Engineering & Visualisation — Team 32

> **Does being more educated mean you're more likely to be employed, and does gender make a difference?**

---

## Who We Are

**Team 32** · Adheesh · Shannon · Thiha Thein · Jinghong · Zafri  
Module: AAI1001 Data Engineering & Visualisation  
Submission: Week 13 Final Project

---

## What This Project Is About

Singapore's workforce has quietly undergone one of the fastest educational transformations in the world — the share of degree-holders in the workforce has more than tripled over the past 30 years. But that success story comes with a real question: **does higher education actually translate into better employment outcomes?** And critically — **does the answer look different for women?**

We started from a chart published by FYT Consultants (September 2025) that attempted to tell this story. The chart was thought-provoking, but it buried the employment angle entirely and made gender comparison genuinely difficult to do at a glance. So we rebuilt it from scratch — using official Ministry of Manpower (MOM) 2024 data — around the questions that actually matter.

The result is a **butterfly chart** that encodes four dimensions simultaneously: age group, gender, employment rate (bar width), and education composition (stacked colour segments) — all in a single, readable static image.

---

## The Three Questions We Set Out to Answer

1. How does educational attainment among *employed* Singaporeans vary across age groups — and does it tell a different story from raw population data?
2. Are there meaningful gender gaps in the education–employment relationship that aggregate statistics tend to conceal?
3. Does the data support or challenge the narrative that young, highly-educated workers are having a harder time finding jobs?

---

## Project Structure

```
team32-project/
│
├── slides.qmd                  # Main Quarto RevealJS presentation (renders to slides.html)
├── custom.scss                 # Slide theme — fonts, cards, badges, layout utilities
│
├── data/
│   ├── LFR2024_SectionB_EmploymentRate.xlsx          # MOM Table B1: Employment rates by age & sex
│   └── LFR2024_SectionG_MedianAge_DemographicProfile.xlsx  # MOM Table G4: Education counts by age & sex
│
├── slides.html                 # Pre-rendered HTML output (self-contained)
├── presentation.mp4            # Recorded presentation (≤ 15 min)
│
└── README.md                   # This file
```

---

## Data Sources

All data comes from official Singapore government sources.

| Table | Description | Source |
|:------|:------------|:-------|
| **B1** | Resident Employment Rate by Age and Sex, 2014–2024 | MOM Comprehensive Labour Force Survey 2024 |
| **G4** | Employed Residents by Highest Qualification Attained, Age and Sex | MOM Comprehensive Labour Force Survey 2024 |

**Full citation:**  
Ministry of Manpower, *Labour Force in Singapore 2024*, MOM, 2024.  
[stats.mom.gov.sg](https://stats.mom.gov.sg/Pages/Labour-Force-In-Singapore-2024.aspx)

**Original visualisation we critiqued:**  
FYT Consultants, *"A Data-Driven Look at Singapore's Graduate Unemployment Debate"*, September 2025  
[fytconsultants.com](https://www.fytconsultants.com/single-post/a-data-driven-look-at-singapore-s-graduate-unemployment-debate)

---

## What We Critiqued About the Original

The original chart had some genuine strengths — showing three dimensions at once, pairing percentage and count panels, and having a clear legend. But we identified six problems that limited its usefulness:

| Code | Problem |
|:-----|:--------|
| W1 | 18 rotated axis labels made it hard to trace which bar belonged to which age group |
| W2 | No visual link between the percentage panel and the count panel |
| W3 | Floating stacked segments made cross-age-group comparisons almost impossible |
| W4 | The oversized 65+ count bar compressed every other bar into near-invisibility |
| W5 | Employment rate was completely absent — the chart showed *who lives here*, not *who is working* |
| W6 | No colour-blind-safe palette |

---

## Our Six Improvements

| Code | What we changed | Why |
|:-----|:----------------|:----|
| I1 | Switched to a butterfly layout | Makes Male vs Female comparison instant — just scan across any row |
| I2 | Bar width encodes employment rate, not population count | Shows who is *actually working*, not just who lives in Singapore |
| I3 | Degrees stacked closest to the spine | Creates a shared anchor for every row comparison |
| I4 | Rate-normalised axis | Prevents any single age group from distorting the picture |
| I5 | Wong (2011) colour-blind-safe palette | Readable for all viewers regardless of colour-vision ability |
| I6 | Printed rate labels on each bar | Exact figures are readable without any interactivity needed |

---

## Data Engineering Pipeline

The raw Excel files have awkward multi-row headers and merged cells. Here's how we handled them:

```
Load Excel (skip = 6)
  → Forward-fill gender label row-by-row
    → Filter to 10 age bands (25–29 through 70+)
      → Coerce to numeric, replace "-" with 0
        → Compute education % shares within each age × sex cell (G4)
          → left_join B1 + G4 on sex × age_group
            → Factor-order age_group → employment_data (20 rows × 8 cols)
```

The trickiest part was recovering the gender label. In both Excel files, `"Male"` and `"Female"` appear as standalone header rows — not as column values. Every age-band row beneath them has `NA` in the gender column. We used a row-by-row loop with a running `current_sex` variable to forward-fill the label down to the data rows it applies to.

---

## Key Findings

**1 — Young women are outpacing young men at entry level**  
At 25–29, female employment rates exceed male rates by around 5 percentage points — the only age band where this happens. Higher degree attainment on entry to the workforce appears to be driving this reversal.

**2 — Degrees peak in the 30s, but the gender gap persists**  
Females aged 30–39 are the most degree-qualified group in the entire dataset (over 60% hold a university degree). Yet they still trail male counterparts in employment rates. Structural factors matter more than qualifications alone.

**3 — The gap grows with age**  
From 50 onwards, male employment rates stay high while female rates decline sharply. This points to earlier labour force withdrawal among women — not lower education — as the main driver of the divergence.

---

## How to Reproduce

### Requirements

- R (≥ 4.3)
- Quarto (≥ 1.4)
- R packages: `readxl`, `dplyr`, `tidyr`, `ggplot2`, `scales`, `knitr`, `kableExtra`

Install all packages at once:

```r
install.packages(c("readxl", "dplyr", "tidyr", "ggplot2", "scales", "knitr", "kableExtra"))
```

### Render the slides

```bash
quarto render slides.qmd
```

This produces `slides.html` — a fully self-contained HTML file. No internet connection is needed to view it once rendered.

### File paths

The `.qmd` file expects the data files to be in a `data/` subfolder relative to itself:

```
data/LFR2024_SectionB_EmploymentRate.xlsx
data/LFR2024_SectionG_MedianAge_DemographicProfile.xlsx
```

Make sure both files are in place before rendering.

---

## Colour Palette

All colours in the visualisation use the **Wong (2011) colour-blind-safe palette**:

| Education Level | Hex |
|:----------------|:----|
| Below Secondary | `#E69F00` |
| Secondary | `#56B4E9` |
| Post-Secondary (Non-Tertiary) | `#009E73` |
| Diploma & Prof. Qual. | `#0072B2` |
| Degree | `#CC79A7` |

**Reference:** B. Wong, *Points of View: Color Blindness*, Nature Methods, vol. 8, no. 6, pp. 441–441, May 2011. [doi:10.1038/nmeth.1618](https://doi.org/10.1038/nmeth.1618)

---

## Presentation Structure

The slide deck follows the seven required sections:

1. Original Data Visualisation
2. Critiques & Proposed Improvements
3. Data Selection & Sources
4. Data Engineering Workflow
5. Improved Visualisation
6. Insights & Analysis
7. Conclusion

---

*AAI1001 Data Engineering & Visualisation · Team 32 · March 2026*
