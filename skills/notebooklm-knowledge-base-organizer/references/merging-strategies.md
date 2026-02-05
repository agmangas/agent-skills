# Strategic Merging

Defines three merging patterns (Time-Series, Topic-Based, Format Consolidation), quality criteria, and automated scripts. Load this reference when you need to reduce source count by consolidating related content, or when the collection exceeds 50 sources after scoring and selection.

---

## Core Principle

Merging is a PRIMARY optimization strategy when facing the 50-source limit. Good merges preserve information while reducing source count. Do not treat merging as a last resort -- use it as a first-line approach for related content.

IMPORTANT: When merging time-series or chronological documents, the merged output MUST preserve individual document timestamps and dates as section headers or inline metadata. The chronological context is essential for RAG retrieval and for understanding the evolution of decisions and discussions. Never flatten dates into a single range or discard them.

---

## Pattern 1: Time-Series Consolidation

Combine chronologically related documents into period summaries. Preserve every original date as a section header in the merged output.

```bash
# Example: Daily meeting notes -> Monthly summary
# Preserve timestamps from originals
mkdir -p .merging
for f in meeting_notes_2026_01_*.txt; do
  echo -e "\n\n=== $(basename $f .txt) ===" >> .merging/meeting_notes_2026_01.txt
  cat "$f" >> .merging/meeting_notes_2026_01.txt
done
# Preserve the oldest source timestamp on the merged file
touch -r "$(ls -t meeting_notes_2026_01_*.txt | tail -1)" .merging/meeting_notes_2026_01.txt
```

### Good time-series merge

```
Before (31 sources):
- daily_standup_2026_01_01.txt
- daily_standup_2026_01_02.txt
- ... (31 days)

After (1 source):
- meeting_notes_january_2026_daily_standups.txt
  (includes all 31 entries with clear date headers for each day)

Savings: 30 sources
Information Loss: None (all content preserved with dates)
```

### Bad time-series merge

```
Merging unrelated periods:
- notes_2020_to_2026_everything.txt

Problem: Too broad, loses temporal context, poor searchability
```

---

## Pattern 2: Topic-Based Consolidation

Combine multiple sources on the same specific topic. Keep the scope narrow enough that the merged document remains useful for focused RAG retrieval.

```bash
# Example: Related research papers -> Single comprehensive document
pdfunite \
  quantum_intro_basics.pdf \
  quantum_theory_foundations.pdf \
  quantum_applications_overview.pdf \
  research_quantum_computing_consolidated.pdf

# Add table of contents
echo "Contents:
1. Introduction to Quantum Basics (p1-25)
2. Theoretical Foundations (p26-68)
3. Applications Overview (p69-110)
" | cat - research_quantum_computing_consolidated.pdf > temp.pdf
mv temp.pdf research_quantum_computing_consolidated.pdf
```

### Good topic merge

```
Before (5 sources):
- intro_neural_networks_basics.pdf
- neural_networks_backpropagation.pdf
- neural_networks_activation_functions.pdf
- neural_networks_optimization.pdf
- neural_networks_regularization.pdf

After (1 source):
- reference_neural_networks_comprehensive_guide.pdf
  (combined with chapter markers)

Savings: 4 sources
Information Loss: None
Search Improvement: Better (unified context)
```

### Bad topic merge

```
Merging loosely related topics:
- ai_everything_machine_learning_nlp_computer_vision_robotics.pdf

Problem: Too broad, creates 200+ page megadoc, poor RAG retrieval
```

---

## Pattern 3: Format Consolidation

Combine different formats covering the same event or topic into a single source.

```bash
# Example: Conference session materials
# presentation.pptx (slides) + notes.txt (speaker notes) + transcript.txt (recording) -> PDF

# Convert presentation to PDF
soffice --headless --convert-to pdf presentation.pptx

# Merge text content
cat notes.txt transcript.txt > combined_text.txt

# Create combined PDF with slides + text (requires pdftk or similar)
convert combined_text.txt text.pdf
pdfunite presentation.pdf text.pdf conference_session_complete.pdf

# Preserve the earliest timestamp from the original materials
touch -r presentation.pptx conference_session_complete.pdf
```

### Good format merge

```
Before (4 sources):
- webinar_slides_product_launch.pptx
- webinar_transcript_product_launch.txt
- webinar_qa_session.txt
- webinar_chat_log.txt

After (1 source):
- webinar_product_launch_2026_complete.pdf
  (slides + transcript + Q&A + chat, clearly sectioned)

Savings: 3 sources
Information Loss: None
Context Gain: High (all aspects in one place)
```

---

## Automated Merging Scripts

### Time-Series Merger (Python)

```python
#!/usr/bin/env python3
# merge_timeseries.py - Combine chronological documents
# IMPORTANT: Preserves individual document dates as section headers.

import glob
import os
import re
from datetime import datetime
from pathlib import Path

def merge_timeseries(pattern, output_name, date_pattern=r'\d{4}_\d{2}_\d{2}'):
    """
    Merge files matching pattern with date stamps.
    Each source file's date is preserved as a section header.
    Example: merge_timeseries('notes_*.txt', 'notes_2026_q1.txt')
    """
    files = sorted(glob.glob(pattern))

    with open(output_name, 'w') as outfile:
        outfile.write(f"# Consolidated Time-Series Document\n")
        outfile.write(f"# Source files: {len(files)}\n")
        outfile.write(f"# Generated: {datetime.now().isoformat()}\n")
        outfile.write(f"# Date range: {Path(files[0]).name} to {Path(files[-1]).name}\n\n")

        for filepath in files:
            # Extract date from filename
            date_match = re.search(date_pattern, filepath)
            date_str = date_match.group(0) if date_match else "Unknown"

            outfile.write(f"\n{'='*80}\n")
            outfile.write(f"Source: {Path(filepath).name} | Date: {date_str}\n")
            outfile.write(f"{'='*80}\n\n")

            with open(filepath, 'r') as infile:
                outfile.write(infile.read())
            outfile.write("\n\n")

    # Preserve the oldest file's timestamp on the merged output
    oldest = min(files, key=os.path.getmtime)
    os.utime(output_name, (os.path.getatime(oldest), os.path.getmtime(oldest)))

    print(f"Merged {len(files)} files into {output_name}")
    print(f"Saved {len(files)-1} source slots")
    print(f"Timestamp preserved from: {oldest}")

# Usage examples
if __name__ == "__main__":
    # Merge daily notes into monthly
    merge_timeseries('daily_notes_2026_01_*.txt', 'monthly_notes_2026_01.txt')

    # Merge weekly reports into quarterly
    merge_timeseries('weekly_report_2026_q1_*.md', 'quarterly_report_2026_q1.md')
```

### Topic-Based PDF Merger (Bash)

```bash
#!/bin/bash
# merge_topic_pdfs.sh - Combine related PDFs with TOC

TOPIC="$1"
OUTPUT="$2"
shift 2
INPUTS=("$@")

if [ ${#INPUTS[@]} -eq 0 ]; then
  echo "Usage: $0 <topic> <output.pdf> <input1.pdf> [input2.pdf ...]"
  exit 1
fi

# Record the oldest input file for timestamp preservation
OLDEST=$(ls -tr "${INPUTS[@]}" | head -1)

# Create table of contents
TOC_MD="# ${TOPIC} - Consolidated Reference\n\n## Sources\n\n"
page_count=1

for ((i=0; i<${#INPUTS[@]}; i++)); do
  pdf="${INPUTS[$i]}"
  pages=$(pdfinfo "$pdf" | grep Pages | awk '{print $2}')
  TOC_MD+="$((i+1)). $(basename "$pdf" .pdf) (pages $page_count-$((page_count+pages-1)))\n"
  page_count=$((page_count + pages))
done

# Create TOC PDF
echo -e "$TOC_MD" | pandoc -o toc.pdf

# Merge all PDFs
pdfunite toc.pdf "${INPUTS[@]}" "$OUTPUT"

rm toc.pdf

# Preserve timestamp from oldest source
touch -r "$OLDEST" "$OUTPUT"

echo "Created $OUTPUT from ${#INPUTS[@]} sources"
echo "Saved $((${#INPUTS[@]}-1)) source slots"
echo "Timestamp preserved from: $OLDEST"
```

---

## Merge vs. Split Decision Tree

```
Is the content coherent and related?
+-- YES: Consider merging
|   +-- Do merged files stay under 500k words?
|   |   +-- YES: Merge is viable
|   |   |   +-- Will merge improve search context?
|   |   |   |   +-- YES: MERGE
|   |   |   |   +-- NO: Keep separate
|   |   +-- NO: Cannot merge (would exceed limits)
+-- NO: Do not merge
    +-- Is individual file >500k words?
    |   +-- YES: SPLIT by topic/chapter
    |   +-- NO: Keep as-is
```

---

## Example Merge Scenarios

### Scenario 1: Weekly Sprint Reports

```
Input: 52 weekly sprint reports from 2025
Strategy: Merge by quarter, preserve each week's date as a section header

Before:
week_01_sprint_report.md
week_02_sprint_report.md
...
week_52_sprint_report.md
(52 sources)

After:
sprint_reports_2025_q1_weeks_01_13.md
sprint_reports_2025_q2_weeks_14_26.md
sprint_reports_2025_q3_weeks_27_39.md
sprint_reports_2025_q4_weeks_40_52.md
(4 sources)

Each file contains week-by-week entries with date headers.

Savings: 48 sources
Quality: High (maintains chronology, quarter-level granularity)
```

### Scenario 2: Conference Proceedings

```
Input: 30 individual paper PDFs from same conference

Before:
paper_01_neural_networks.pdf
paper_02_computer_vision.pdf
...
paper_30_robotics.pdf
(30 sources)

After:
conference_ai_2025_papers_neural_ml.pdf (papers 1-10)
conference_ai_2025_papers_vision_nlp.pdf (papers 11-20)
conference_ai_2025_papers_robotics_systems.pdf (papers 21-30)
(3 sources)

Savings: 27 sources
Quality: Medium (loses individual paper granularity, but grouped by subtopic)
```

### Scenario 3: Product Documentation

```
Input: 45 API endpoint documentation pages

Before:
api_users_get.md
api_users_post.md
api_users_delete.md
... (45 endpoints)
(45 sources)

After:
api_documentation_users_endpoints.md (15 endpoints)
api_documentation_products_endpoints.md (12 endpoints)
api_documentation_orders_endpoints.md (10 endpoints)
api_documentation_admin_endpoints.md (8 endpoints)
(4 sources)

Savings: 41 sources
Quality: High (maintains organization, improves context for related endpoints)
```

---

## Good vs. Poor Merge Indicators

**Good merge candidates:**

- Files share a common topic or time period
- Combined size stays under 500k words
- Merging improves context (related content together)
- Individual files have low-to-medium value scores (25-30)
- Chronological documents from the same series

**Poor merge candidates:**

- High-value unique sources (score 35+) -- keep these separate
- Unrelated topics forced together
- Combined size would exceed limits
- Content needs to stay separate for clarity
- Merging would create a 200+ page unfocused megadoc
