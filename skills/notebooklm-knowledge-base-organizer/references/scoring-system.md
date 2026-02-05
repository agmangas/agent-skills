# Source Significance Scoring System

Defines the 0-40 scoring rubric used to evaluate each source across four dimensions. Load this reference when the collection exceeds 50 sources and you need to score and rank files for inclusion.

---

## Scoring Dimensions (0-10 each)

### 1. Relevance (0-10)

How directly does this source address the notebook's primary purpose?

- 10 = Core essential content, directly answers main questions
- 5 = Moderately relevant, provides supporting context
- 0 = Tangentially related or off-topic

**Assessment commands:**

```bash
# Examine content keywords
pdftotext source.pdf - | head -100 | tr ' ' '\n' | sort | uniq -c | sort -rn | head -20

# Check topic density
grep -io "keyword\|topic\|concept" source.txt | wc -l

# Compare with requirements
echo "Does this file address: [key question 1], [key question 2]?"
```

### 2. Recency (0-10)

How current is the information?

- 10 = Published/created in last 6 months
- 7 = Last 1-2 years
- 5 = 2-5 years old
- 2 = 5-10 years old
- 0 = 10+ years old (unless historical research)

IMPORTANT: Preserving original file timestamps is critical for accurate recency scoring. Always verify timestamps have not been modified during prior file operations (copies, conversions, moves). If timestamps were lost, fall back to metadata or in-document dates.

**Assessment commands:**

```bash
# Check file modification date
stat -f "%Sm %N" -t "%Y-%m-%d" *.pdf | sort -r

# Check metadata dates
exiftool -CreateDate -ModifyDate source.pdf

# Find publication dates in text
pdftotext source.pdf - | grep -Eo "[0-9]{4}" | sort -u | tail -5

# Verify timestamps were not altered during file operations
# Compare original and copied file modification times
stat -f "%Sm" -t "%Y-%m-%d %H:%M:%S" original.pdf
stat -f "%Sm" -t "%Y-%m-%d %H:%M:%S" copy.pdf
# These should match. If they differ, the copy lost its timestamp.
```

### 3. Uniqueness (0-10)

How unique is the information this source provides?

- 10 = Completely unique content, no other source has this
- 5 = Some unique content, overlaps with 1-2 other sources
- 0 = Fully duplicated elsewhere or generic information

**Assessment commands:**

```bash
# Check content similarity (requires pdftotext)
for f in *.pdf; do
  echo "=== $f ==="
  pdftotext "$f" - | md5
done | paste - - | sort -k2 | uniq -f1 -D

# Find similar file sizes (potential duplicates)
find . -type f -printf "%s %p\n" | sort -n | uniq -d -w10

# Compare text overlap
pdftotext source1.pdf - > temp1.txt
pdftotext source2.pdf - > temp2.txt
comm -12 <(sort temp1.txt) <(sort temp2.txt) | wc -l  # shared lines
```

### 4. Information Density (0-10)

How much useful information per page/size?

- 10 = Densely packed with facts, data, insights
- 5 = Moderate detail with some filler
- 0 = Sparse content, mostly whitespace/formatting

**Assessment commands:**

```bash
# Word count per page ratio
pages=$(pdfinfo source.pdf | grep Pages | awk '{print $2}')
words=$(pdftotext source.pdf - | wc -w)
echo "scale=2; $words / $pages" | bc  # >300 = high density

# Check for actual content vs formatting
pdftotext source.pdf - | sed 's/[[:space:]]//g' | wc -c  # chars without whitespace

# Unique word ratio (vocabulary richness)
total=$(pdftotext source.pdf - | wc -w)
unique=$(pdftotext source.pdf - | tr ' ' '\n' | sort -u | wc -l)
echo "scale=2; $unique / $total" | bc  # >0.3 = high density
```

---

## Scoring Examples

### High-Priority Source (Total: 35-40)

```
File: research_quantum_computing_2026_nature.pdf
- Relevance: 10 (directly addresses core research question)
- Recency: 10 (published 2 months ago)
- Uniqueness: 9 (primary research, novel findings)
- Density: 8 (dense scientific paper, heavy content)
Total: 37 -> KEEP
```

### Medium-Priority Source (Total: 20-30)

```
File: conference_presentation_quantum_2024.pdf
- Relevance: 8 (good coverage of subtopic)
- Recency: 7 (1 year old)
- Uniqueness: 5 (some overlap with other sources)
- Density: 6 (slides, moderate detail)
Total: 26 -> CONSIDER (merge with related content)
```

### Low-Priority Source (Total: 10-15)

```
File: general_introduction_computing_2015.pdf
- Relevance: 4 (broad overview, not specific to topic)
- Recency: 2 (9 years old)
- Uniqueness: 3 (generic intro, covered in other sources)
- Density: 3 (basic explanations, lots of whitespace)
Total: 12 -> EXCLUDE or MERGE
```

---

## Batch Scoring Script

```bash
#!/bin/bash
# score_sources.sh - Semi-automated scoring assistant
# Generates source_scores.csv with per-file evaluations.
# Recency and Density are auto-calculated; Relevance and Uniqueness require manual input.

echo "Source,Relevance,Recency,Uniqueness,Density,Total,Decision" > source_scores.csv

for file in *.pdf; do
  echo "Analyzing: $file"

  # Auto-calculate recency from file modification date.
  # WARNING: Only accurate if original timestamps were preserved.
  # Use `touch -r "$original" "$converted"` during all file operations.
  mod_date=$(stat -f "%Sm" -t "%Y" "$file")
  current_year=$(date +%Y)
  age=$((current_year - mod_date))

  if [ $age -eq 0 ]; then recency=10
  elif [ $age -eq 1 ]; then recency=7
  elif [ $age -le 5 ]; then recency=5
  elif [ $age -le 10 ]; then recency=2
  else recency=0
  fi

  # Auto-calculate density
  pages=$(pdfinfo "$file" 2>/dev/null | grep Pages | awk '{print $2}')
  words=$(pdftotext "$file" - 2>/dev/null | wc -w)
  if [ -n "$pages" ] && [ "$pages" -gt 0 ]; then
    words_per_page=$((words / pages))
    if [ $words_per_page -gt 400 ]; then density=9
    elif [ $words_per_page -gt 300 ]; then density=7
    elif [ $words_per_page -gt 200 ]; then density=5
    elif [ $words_per_page -gt 100 ]; then density=3
    else density=1
    fi
  else
    density=5  # default
  fi

  echo "File: $file"
  echo "Auto-scores: Recency=$recency, Density=$density"
  echo -n "Enter Relevance (0-10): "; read relevance
  echo -n "Enter Uniqueness (0-10): "; read uniqueness

  total=$((relevance + recency + uniqueness + density))

  if [ $total -ge 30 ]; then decision="KEEP"
  elif [ $total -ge 20 ]; then decision="CONSIDER"
  else decision="EXCLUDE/MERGE"
  fi

  echo "$file,$relevance,$recency,$uniqueness,$density,$total,$decision" >> source_scores.csv
  echo "Total: $total -> $decision"
  echo ""
done

echo "Scoring complete! See source_scores.csv"
sort -t, -k6 -rn source_scores.csv | column -t -s,
```
