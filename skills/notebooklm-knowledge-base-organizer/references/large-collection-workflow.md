# Large Collection Workflow (237 to 48 Sources)

Complete worked example showing the end-to-end process of reducing a 237-file research collection to 48 optimized sources. Load this reference when handling collections of 100+ sources to see the full workflow in action, including scoring distribution, merge execution, and final selection breakdown.

---

## Initial Assessment

```bash
$ cd research_collection
$ find . -type f \( -name "*.pdf" -o -name "*.docx" -o -name "*.txt" -o -name "*.md" \) | wc -l
237

# File breakdown
$ find . -type f | sed 's/.*\.//' | sort | uniq -c | sort -rn
    156 pdf
     45 docx
     28 txt
      8 md
```

**Problem**: 237 sources, limit is 50. Need to reduce by 187 sources (79% reduction).

---

## Step 1: Categorization and Initial Scoring

```bash
# Generate category manifest
$ find . -type f -name "*.pdf" | while read f; do
  dir=$(dirname "$f" | sed 's|^\./||')
  echo "$dir|$f"
done > .analysis/files_by_category.txt

# Categories discovered:
# - papers_quantum_computing/ (45 files)
# - papers_machine_learning/ (38 files)
# - textbooks_chapters/ (32 files)
# - conference_proceedings/ (18 files)
# - meeting_notes_2024/ (52 files)
# - meeting_notes_2025/ (26 files)
# - project_docs/ (15 files)
# - misc_references/ (11 files)
```

---

## Step 2: Run Scoring

```bash
$ ./score_sources.sh
# [Interactive scoring process for each file]
# Output: source_scores.csv

$ head -20 source_scores.csv | column -t -s,
Source                                          Relevance  Recency  Uniqueness  Density  Total  Decision
paper_quantum_entanglement_nature_2025.pdf      10         10       9           8        37     KEEP
paper_ml_transformers_attention_2024.pdf        9          9        8           9        35     KEEP
textbook_quantum_mechanics_ch5_measurement.pdf  8          5        6           7        26     CONSIDER
meeting_notes_2024_01_15_weekly.txt             4          3        2           3        12     EXCLUDE
conference_neurips_2023_poster_47.pdf           7          6        5           6        24     MERGE
...
```

### Score Distribution

- 35-40 (KEEP): 28 sources
- 30-34 (CONDITIONAL KEEP): 41 sources
- 25-29 (MERGE CANDIDATES): 58 sources
- 15-24 (LIKELY MERGE): 67 sources
- 0-14 (EXCLUDE): 43 sources

---

## Step 3: Identify Strategic Merges

```bash
# Time-series opportunities
$ find meeting_notes_2024 -name "*.txt" | wc -l
52  # Weekly meeting notes for all of 2024

$ find meeting_notes_2025 -name "*.txt" | wc -l
26  # Weekly meeting notes Jan-Jun 2025

# Topic clusters
$ cat .analysis/files_by_category.txt | grep conference | \
  sed 's|.*/||;s|_poster.*||;s|_paper.*||' | sort | uniq -c
  18 conference_neurips_2023  # Same conference, various papers

$ cat .analysis/files_by_category.txt | grep textbook | \
  sed 's|_ch[0-9]*.*||' | sort | uniq -c
  12 textbook_quantum_mechanics  # Chapters 1-12 from same book
  10 textbook_ml_fundamentals    # Chapters 1-10
  10 textbook_linear_algebra     # Chapters 1-10
```

---

## Step 4: Execute Merging Strategy

### Merge 1: Time-Series Consolidation (78 -> 6 sources, savings: 72)

IMPORTANT: Each quarterly summary preserves individual meeting dates as section headers.

```bash
# Create quarterly meeting summaries
$ python3 merge_timeseries.py \
  "meeting_notes_2024_q1_*.txt" \
  ".merging/meeting_notes_2024_q1.txt"
# Merged 13 files

$ python3 merge_timeseries.py \
  "meeting_notes_2024_q2_*.txt" \
  ".merging/meeting_notes_2024_q2.txt"
# Merged 13 files

$ python3 merge_timeseries.py \
  "meeting_notes_2024_q3_*.txt" \
  ".merging/meeting_notes_2024_q3.txt"
# Merged 13 files

$ python3 merge_timeseries.py \
  "meeting_notes_2024_q4_*.txt" \
  ".merging/meeting_notes_2024_q4.txt"
# Merged 13 files

$ python3 merge_timeseries.py \
  "meeting_notes_2025_q1_*.txt" \
  ".merging/meeting_notes_2025_q1.txt"
# Merged 13 files

$ python3 merge_timeseries.py \
  "meeting_notes_2025_q2_*.txt" \
  ".merging/meeting_notes_2025_q2.txt"
# Merged 13 files

# Result: 78 weekly notes -> 6 quarterly summaries
# Each preserves per-meeting dates as section headers
```

### Merge 2: Textbook Chapter Consolidation (32 -> 3 sources, savings: 29)

```bash
# Combine chapters from each textbook
$ ./merge_topic_pdfs.sh "Quantum Mechanics" \
  ".merging/textbook_quantum_mechanics_complete.pdf" \
  textbooks_chapters/quantum_mechanics_ch*.pdf
# Merged 12 chapters, 487 pages total, 245k words (within limits)

$ ./merge_topic_pdfs.sh "ML Fundamentals" \
  ".merging/textbook_ml_fundamentals_complete.pdf" \
  textbooks_chapters/ml_fundamentals_ch*.pdf
# Merged 10 chapters, 392 pages total, 198k words

$ ./merge_topic_pdfs.sh "Linear Algebra" \
  ".merging/textbook_linear_algebra_complete.pdf" \
  textbooks_chapters/linear_algebra_ch*.pdf
# Merged 10 chapters, 356 pages total, 178k words
```

### Merge 3: Conference Proceedings by Topic (18 -> 3 sources, savings: 15)

```bash
# Group conference papers by subtopic
$ ./merge_topic_pdfs.sh "NeurIPS 2023 Computer Vision" \
  ".merging/conference_neurips_2023_cv.pdf" \
  conference_proceedings/neurips_2023_*cv*.pdf \
  conference_proceedings/neurips_2023_*vision*.pdf
# Merged 6 papers

$ ./merge_topic_pdfs.sh "NeurIPS 2023 NLP" \
  ".merging/conference_neurips_2023_nlp.pdf" \
  conference_proceedings/neurips_2023_*nlp*.pdf \
  conference_proceedings/neurips_2023_*language*.pdf
# Merged 7 papers

$ ./merge_topic_pdfs.sh "NeurIPS 2023 Reinforcement Learning" \
  ".merging/conference_neurips_2023_rl.pdf" \
  conference_proceedings/neurips_2023_*rl*.pdf \
  conference_proceedings/neurips_2023_*reinforcement*.pdf
# Merged 5 papers
```

### Merge 4: Low-Priority Similar Content (67 -> 8 sources, savings: 59)

```bash
# Merge introduction/overview papers by topic
$ pdfunite intro_ml_basics_*.pdf .merging/reference_ml_basics_consolidated.pdf
# Merged 8 intro papers

$ pdfunite overview_quantum_*.pdf .merging/reference_quantum_overview_consolidated.pdf
# Merged 12 overview papers

# ... [similar for other low-priority topic clusters]
```

### Total Merging Results

- Original: 237 sources
- After merging: 62 sources (175 sources merged into 20 consolidated sources)
- Target: 50 sources
- Still need to reduce: 12 sources

---

## Step 5: Final Selection (62 -> 48 sources)

```bash
$ ./select_top_50.sh
# Auto-select using scoring
# - Keep all 37+ scores: 28 sources
# - Keep high-uniqueness 30-36 scores: 12 sources
# - Keep merged consolidated sources: 20 sources
# Total: 60 sources (need to cut 10 more)

# Manual review of borderline (30-34 scores)
# Cut 12 lowest scores in this range, keeping highest uniqueness
```

### Final Selection Breakdown

**Category Distribution (48 sources):**

1. **Quantum Computing Research** (12 sources)
   - 10 high-impact papers (scores 35-40)
   - 1 textbook (merged chapters)
   - 1 overview reference (merged)

2. **Machine Learning Research** (11 sources)
   - 9 high-impact papers (scores 35-39)
   - 1 textbook (merged chapters)
   - 1 NeurIPS proceedings (merged)

3. **Project Documentation** (10 sources)
   - Requirements, design docs, reports (scores 32-38)

4. **Meeting Notes** (6 sources)
   - Quarterly summaries 2024-2025 (merged from 78 files)
   - Each preserves per-meeting dates for temporal context

5. **Reference Materials** (9 sources)
   - API docs, guides, consolidated overviews

**Excluded Sources (189 total):**
- 43 low-score sources (score <15): Outdated, generic, redundant
- 146 sources merged into consolidated documents

**Information Preservation:**
- High-value content: 100% preserved
- Medium-value content: 95% preserved (merged)
- Low-value content: Excluded (redundant or outdated)

---

## Step 6: Quality Verification

```bash
# Check all final sources meet limits
$ cd .merging
$ for f in *.pdf; do
  words=$(pdftotext "$f" - 2>/dev/null | wc -w)
  size_mb=$(du -m "$f" | cut -f1)
  echo "$f: ${words} words, ${size_mb}MB"

  if [ "$words" -gt 500000 ]; then
    echo "  WARNING: OVER WORD LIMIT"
  fi
  if [ "$size_mb" -gt 200 ]; then
    echo "  WARNING: OVER SIZE LIMIT"
  fi
done

# All checks passed
```

---

## Final Result

```
Original Collection: 237 sources
Final Knowledge Base: 48 sources (2 slots reserved)

Optimization Techniques Used:
1. Time-series merging: 78 -> 6 (saved 72 sources)
2. Textbook consolidation: 32 -> 3 (saved 29 sources)
3. Conference grouping: 18 -> 3 (saved 15 sources)
4. Topic consolidation: 67 -> 8 (saved 59 sources)
5. Duplicate elimination: 43 excluded
6. Score-based selection: Top 48 of remaining

Information Quality:
- Average source score: 33.8 (high quality)
- Coverage: All major topics represented
- Recency: 89% from last 2 years
- Searchability: Improved (better context in merged docs)

Time Investment:
- Scoring: 3 hours
- Merging: 1.5 hours
- Verification: 0.5 hours
- Total: 5 hours

Value: Transformed 237-file chaos into curated 48-source knowledge base
       optimized for NotebookLM's RAG capabilities.
```

### Final Organized Structure

```
notebooklm_sources/
+-- quantum/
|   +-- paper_quantum_entanglement_nature_2025.pdf (score: 37)
|   +-- paper_quantum_computing_breakthrough_science_2025.pdf (score: 38)
|   +-- ... (10 papers)
|   +-- textbook_quantum_mechanics_complete.pdf (merged 12 chapters)
|   +-- reference_quantum_overview_consolidated.pdf (merged 12 papers)
+-- ml/
|   +-- paper_ml_transformers_attention_2024.pdf (score: 35)
|   +-- ... (9 papers)
|   +-- textbook_ml_fundamentals_complete.pdf (merged 10 chapters)
|   +-- conference_neurips_2023_nlp.pdf (merged 7 papers)
+-- project/
|   +-- ... (10 project docs)
+-- meetings/
|   +-- meeting_notes_2024_q1.txt (merged 13 weekly, dates preserved)
|   +-- ... (6 quarterly summaries)
+-- reference/
    +-- ... (9 consolidated reference docs)

Total: 48 sources, ready for upload
```
