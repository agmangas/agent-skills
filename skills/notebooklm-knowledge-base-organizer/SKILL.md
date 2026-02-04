---
name: notebooklm-knowledge-base-organizer
description: Organizes files specifically for NotebookLM knowledge bases. Converts unsupported formats (PPTX→PDF, XLSX→CSV), applies flat structure with descriptive snake_case names, removes duplicates, and optimizes content for RAG retrieval. Keeps sources under 500k words and 200MB limits.
---

# NotebookLM Knowledge Base Organizer

This skill prepares your files for optimal use in NotebookLM by converting formats, organizing structure, and ensuring compatibility with NotebookLM's source requirements [web:3][web:10].

## When to Use This Skill

- Preparing documents for a new NotebookLM notebook
- Converting a messy folder into NotebookLM-ready sources
- Hitting NotebookLM's 50-source limit and need consolidation
- Files are in unsupported formats (PPTX, XLSX, complex PDFs)
- Documents exceed 500k words or 200MB per file [web:17]
- Building a knowledge base for research, projects, or learning
- Organizing sources for Deep Research or Audio Overview generation

## What This Skill Does

1. **Converts to Supported Formats**: PPTX→PDF, XLSX→CSV, DOCX (keeps as-is), ensures text-selectable PDFs [web:10]
2. **Applies Flat Structure**: No nested folders—NotebookLM works best with flat source lists [web:5]
3. **Descriptive Naming**: snake_case names like `project_requirements_2026.pdf` for easy identification
4. **Removes Duplicates**: Finds identical content across formats [web:5]
5. **Splits Large Files**: Breaks documents >500k words into parts (part_1, part_2) [web:18]
6. **Optimizes for RAG**: Smaller, focused documents improve NotebookLM's retrieval [web:5]

## NotebookLM Supported Formats

**Supported** [web:3][web:10][web:14]:
- PDF (text-selectable, not scanned images)
- Google Docs, Sheets (<100k tokens), Slides (<100 slides)
- Microsoft Word (.docx)
- Text files (.txt, .md)
- Images (PNG, JPEG, TIFF, WEBP, etc.)
- Audio (MP3, WAV, AAC, OGG—with clear speech)
- URLs (websites, YouTube, Google Drive links)
- Copy-pasted text

**Convert These**:
- PPTX → PDF (Google Slides max 100 slides [web:3])
- XLSX → CSV or Google Sheets
- Scanned PDFs → OCR to text-selectable PDF
- Large Sheets → CSV (<100k tokens)

## File Limits [web:17][web:21]

**Per Source**:
- 500,000 words max
- 200MB file size max
- No page limit (word limit matters)

**Per Notebook (Free)**:
- 50 sources maximum
- 100 notebooks total

**Strategy**: Prefer many smaller, focused documents over few large ones for better RAG retrieval [web:5].

## How to Use

### Initial Setup

```bash
cd /path/to/your/documents
```

Then ask Claude Code:

```
Prepare these files for NotebookLM - convert formats and organize with descriptive names
```

```
Convert all PPTX and XLSX files to NotebookLM-compatible formats
```

```
Check if any files exceed NotebookLM's 500k word or 200MB limits
```

### Specific Organization Tasks

```
Organize this research folder for a NotebookLM knowledge base
```

```
Find duplicate content across different file formats
```

```
Split this large PDF into NotebookLM-compatible chunks
```

```
Rename these files with descriptive snake_case names for NotebookLM
```

## Instructions

When a user requests NotebookLM organization:

### 1. Understand the Scope

Ask clarifying questions:
- What's the topic/purpose of this knowledge base?
- Which directory contains the source materials?
- What's the target: single notebook or multiple related notebooks?
- Any files that must stay in original format?
- Is this for research, learning, project documentation, or reference?

### 2. Analyze Current State

Review files for NotebookLM compatibility:

```bash
# Get file inventory
find . -type f -exec file {} \;

# Check file sizes
find . -type f -exec du -h {} \; | sort -rh

# Count by extension
find . -type f | sed 's/.*\.//' | sort | uniq -c | sort -rn

# Estimate word counts for large files
for f in *.pdf; do pdftotext "$f" - | wc -w; done
```

Categorize findings:
- **Compatible as-is**: PDF, DOCX, TXT, MD, images
- **Needs conversion**: PPTX, XLSX, XLS, PPT, scanned PDFs
- **Too large**: Files >500k words or >200MB
- **Duplicates**: Same content in different formats

### 3. Convert Unsupported Formats

**PowerPoint to PDF**:
```bash
# macOS using LibreOffice
/Applications/LibreOffice.app/Contents/MacOS/soffice --headless --convert-to pdf *.pptx

# Or use Preview/Print to PDF for single files
```

**Excel to CSV**:
```bash
# Using LibreOffice
soffice --headless --convert-to csv:"Text - txt - csv (StarCalc)":44,34,UTF8 *.xlsx

# Or Python for complex sheets
python3 -c "
import pandas as pd
xlsx = pd.ExcelFile('data.xlsx')
for sheet in xlsx.sheet_names:
    df = pd.read_excel(xlsx, sheet)
    df.to_csv(f'{sheet.replace(' ', '_').lower()}.csv', index=False)
"
```

**Scanned PDF to Searchable**:
```bash
# Using ocrmypdf
ocrmypdf input.pdf output_searchable.pdf

# Verify text-selectable
pdftotext test.pdf - | wc -w  # Should return word count
```

### 4. Apply NotebookLM-Optimized Naming

Use this pattern: `category_topic_descriptor_date.ext`

**Examples**:
- `research_quantum_computing_basics_2025.pdf`
- `meeting_notes_project_kickoff_2026_01.txt`
- `client_proposal_acme_corp_final.docx`
- `reference_api_documentation_v2.md`
- `data_sales_figures_q4_2025.csv`

**Naming Script**:
```bash
# Convert to snake_case, remove special chars
for f in *; do
  new_name=$(echo "$f" | tr '[:upper:]' '[:lower:]' | \
    sed 's/[^a-z0-9.]/_/g' | sed 's/__*/_/g' | \
    sed 's/^_//;s/_\././')
  mv "$f" "$new_name"
done
```

### 5. Split Large Documents

For files >500k words or >200MB [web:18]:

```bash
# Check word count
pdftotext document.pdf - | wc -w

# Split by page count estimate (500k words ≈ 1000 pages)
pdftk large.pdf cat 1-500 output large_part_1.pdf
pdftk large.pdf cat 501-1000 output large_part_2.pdf

# Or split text files
split -l 10000 large.txt part_ && \
for f in part_*; do mv "$f" "${f}.txt"; done
```

Name parts clearly:
- `annual_report_2025_part_1_executive_summary.pdf`
- `annual_report_2025_part_2_financials.pdf`
- `annual_report_2025_part_3_appendices.pdf`

### 6. Implement Flat Structure

NotebookLM works best with flat source lists—no nested folders [web:5].

**Before**:
```
docs/
├── project/
│   ├── planning/
│   │   └── requirements.pdf
│   └── research/
│       └── background.pdf
└── reference/
    └── api_docs.pdf
```

**After**:
```
notebooklm_sources/
├── project_requirements_2026.pdf
├── project_background_research.pdf
└── reference_api_documentation.pdf
```

**Implementation**:
```bash
# Create flat output directory
mkdir -p notebooklm_sources

# Copy and rename with path context
find . -type f \( -name "*.pdf" -o -name "*.docx" -o -name "*.txt" \) -exec bash -c '
  dir=$(dirname "$1" | tr "/" "_" | sed "s/^_//")
  base=$(basename "$1")
  name="${base%.*}"
  ext="${base##*.}"
  cp "$1" "notebooklm_sources/${dir}_${name}.${ext}"
' _ {} \;

# Clean up naming
cd notebooklm_sources && rename 's/[^a-z0-9.]/_/g' *
```

### 7. Find and Remove Duplicates

Identify duplicate content across formats:

```bash
# Check by content hash
find . -type f -exec md5 {} \; | sort | uniq -d

# Find similar filenames
find . -type f -printf '%f\n' | \
  sed 's/\.[^.]*$//' | sort | uniq -d

# Compare text content of PDFs/DOCX
for pdf in *.pdf; do
  echo "=== $pdf ==="
  pdftotext "$pdf" - | md5
done | sort
```

**Decision Matrix**:
- Same content, different formats → Keep PDF (best for NotebookLM)
- Same content, different names → Keep most descriptive name
- Slight variations → Merge into single document if <500k words
- Truly duplicate → Delete older version

### 8. Optimize for RAG Retrieval

NotebookLM uses RAG, which works best with focused documents [web:5]:

**Best Practices**:
- Split 100-page documents into 3-5 topic-focused files
- Separate chapters/sections into individual sources
- Keep each source focused on one topic/subtopic
- Prefer 20-50 pages per PDF over 200+ page megadocs

**Example Split**:
```
Instead of:
- company_handbook_500_pages.pdf

Create:
- handbook_code_of_conduct.pdf
- handbook_benefits_overview.pdf
- handbook_time_off_policy.pdf
- handbook_remote_work_guidelines.pdf
- handbook_career_development.pdf
```

### 9. Propose Organization Plan

Present plan before making changes:

```markdown
# NotebookLM Knowledge Base Organization Plan

## Current State
- 127 files across 8 nested folders
- Formats: 45 PDFs, 23 DOCX, 18 PPTX, 15 XLSX, 26 other
- Total size: 3.2 GB
- Issues:
  - 18 PPTX need conversion to PDF
  - 15 XLSX need conversion to CSV
  - 3 PDFs exceed 200MB (need splitting)
  - 12 duplicate files found
  - Deep nested structure (7 levels)

## Proposed Structure

```
notebooklm_sources/
├── project_requirements_final.pdf (1.2MB)
├── project_timeline_gantt.pdf (converted from PPTX)
├── project_budget_breakdown.csv (converted from XLSX)
├── meeting_notes_2026_q1.txt (merged 8 docs)
├── research_market_analysis_part_1.pdf (split from 250MB)
├── research_market_analysis_part_2.pdf
└── reference_competitor_overview.docx (3.5MB)
```

Total: 42 sources (under 50-source limit)

## Changes to Make

### Conversions (33 files)
1. Convert 18 PPTX → PDF
2. Convert 15 XLSX → CSV

### Splits (3 files)
1. `research_report.pdf` (600k words) → 2 parts
2. `data_analysis.pdf` (235MB) → 3 parts

### Deletions (12 duplicates)
- `proposal_v1.pdf` (keep v2)
- `data_OLD.xlsx` (keep current)
- [list others...]

### Renames (all files)
- Apply snake_case naming
- Add descriptive context from folder path
- Include dates where relevant

## NotebookLM Compatibility Check
✅ All files <200MB after splits
✅ All files <500k words after splits
✅ All formats supported by NotebookLM
✅ Total sources: 42 (under 50 limit)
✅ Flat structure for easy upload

Ready to proceed? (yes/no/modify)
```

### 10. Execute Organization

After approval:

```bash
#!/bin/bash
# NotebookLM organization script

set -e

# Create output directory
OUTPUT="notebooklm_sources"
mkdir -p "$OUTPUT"

# Log file
LOG="notebooklm_org_$(date +%Y%m%d_%H%M%S).log"

echo "Starting NotebookLM organization..." | tee -a "$LOG"

# Convert PPTX to PDF
for f in **/*.pptx; do
  echo "Converting: $f" | tee -a "$LOG"
  soffice --headless --convert-to pdf "$f" --outdir "$OUTPUT"
done

# Convert XLSX to CSV
for f in **/*.xlsx; do
  echo "Converting: $f" | tee -a "$LOG"
  base=$(basename "$f" .xlsx)
  ssconvert "$f" "$OUTPUT/${base}.csv"
done

# Copy and rename compatible files
for f in **/*.{pdf,docx,txt,md}; do
  if [ -f "$f" ]; then
    # Generate descriptive name from path
    path_context=$(dirname "$f" | tr '/' '_' | sed 's/^\.//')
    base=$(basename "$f")
    new_name="${path_context}_${base}"
    
    # Clean to snake_case
    new_name=$(echo "$new_name" | tr '[:upper:]' '[:lower:]' | \
      sed 's/[^a-z0-9.]/_/g' | sed 's/__*/_/g' | sed 's/^_//')
    
    echo "Copying: $f → $new_name" | tee -a "$LOG"
    cp "$f" "$OUTPUT/$new_name"
  fi
done

# Check file sizes and word counts
cd "$OUTPUT"
echo -e "\n=== File Analysis ===" | tee -a "../$LOG"
for f in *.pdf; do
  size=$(du -h "$f" | cut -f1)
  words=$(pdftotext "$f" - 2>/dev/null | wc -w || echo "N/A")
  echo "$f: $size, ~$words words" | tee -a "../$LOG"
  
  # Warn if over limits
  size_mb=$(du -m "$f" | cut -f1)
  if [ "$size_mb" -gt 200 ]; then
    echo "⚠️  WARNING: $f exceeds 200MB" | tee -a "../$LOG"
  fi
  if [ "$words" != "N/A" ] && [ "$words" -gt 500000 ]; then
    echo "⚠️  WARNING: $f exceeds 500k words" | tee -a "../$LOG"
  fi
done

echo -e "\n✅ Organization complete! Check $OUTPUT/" | tee -a "../$LOG"
echo "Total sources: $(ls -1 | wc -l)" | tee -a "../$LOG"
```

### 11. Provide Upload Instructions

After organization:

```markdown
# ✅ NotebookLM Sources Ready!

## Summary
- **42 sources** organized in `notebooklm_sources/`
- All files <200MB and <500k words
- Flat structure with descriptive snake_case names
- Formats: 28 PDF, 8 CSV, 6 DOCX

## How to Upload to NotebookLM

### Option 1: Direct Upload
1. Go to notebooklm.google.com
2. Create new notebook or open existing
3. Click "Sources" → "Upload"
4. Select all files from `notebooklm_sources/`
5. Wait for processing (shows summaries when done)

### Option 2: Google Drive (Recommended for >20 files)
1. Upload `notebooklm_sources/` to Google Drive
2. In NotebookLM, use "Drive URLs" option
3. Share Drive folder URL
4. NotebookLM imports all files at once

## Files Organized

### Project Documentation (8 files)
- project_requirements_final.pdf
- project_timeline_gantt.pdf
- project_budget_breakdown.csv
- [... list others ...]

### Research & Analysis (12 files)
- research_market_analysis_part_1.pdf
- research_market_analysis_part_2.pdf
- [... list others ...]

### Reference Materials (6 files)
- reference_api_documentation.md
- reference_competitor_overview.docx
- [... list others ...]

## NotebookLM Usage Tips

### For Better Retrieval [wondertools.substack](https://wondertools.substack.com/p/notebooklm-the-complete-guide)
- Use source-specific queries: "According to project_requirements..."
- Break large questions into focused queries
- Use the "Select sources" feature to narrow searches

### Avoiding Limit Issues
- You have 8 sources remaining in your 50-source budget
- Consider creating separate notebooks by topic if adding more
- Free plan: 50 chat queries/day, 3 audio overviews/day [elephas](https://elephas.app/blog/notebooklm-source-limits)

### Generating Outputs
- FAQ: "Create an FAQ from project docs"
- Study Guide: "Generate study guide for research analysis"
- Audio Overview: Click "Audio Overview" in Notebook Guide
- Timeline: "Create a timeline from meeting notes"

## Maintenance

To add new sources:
1. Add files to `notebooklm_sources/`
2. Convert/rename using same conventions
3. Check word count: `pdftotext file.pdf - | wc -w`
4. Upload to existing notebook

To update existing sources:
- NotebookLM doesn't auto-update
- Delete old version, upload new one
- Or create new notebook for updated version
```

## Examples

### Example 1: Research Paper Collection

**User**: "Prepare my PhD research papers folder for NotebookLM"

**Process**:
1. Finds 35 PDFs, 12 DOCX, 8 PPTX across nested folders
2. Converts 8 PPTX → PDF (slides to searchable docs)
3. Identifies 2 papers >500k words, splits into parts
4. Renames: `smith_2024.pdf` → `research_quantum_entanglement_smith_2024.pdf`
5. Creates flat structure in `phd_research_sources/`
6. **Result**: 48 sources ready for upload, organized by topic in filenames

### Example 2: Company Knowledge Base

**User**: "Convert our company wiki exports to NotebookLM format"

**Output**:
```markdown
# Converted Company Wiki (145 pages)

Original: Single 145-page PDF export
Problem: Too large for optimal RAG retrieval

Solution: Split by section
- company_overview_history_mission.pdf (8 pages)
- company_org_chart_departments.pdf (12 pages)
- company_policies_hr_guidelines.pdf (28 pages)
- company_it_security_protocols.pdf (18 pages)
- company_product_documentation.pdf (45 pages)
- company_sales_processes.pdf (22 pages)
- company_customer_support_faq.pdf (12 pages)

Result: 7 focused sources instead of 1 large doc
Benefit: Faster, more accurate searches in NotebookLM
```

### Example 3: Excel Data to NotebookLM

**User**: "I have 10 Excel files with research data - how do I add to NotebookLM?"

**Process**:
1. Analyzes Excel files: mix of tables, charts, multiple sheets
2. Converts each sheet to separate CSV (NotebookLM can't read XLSX well [web:3])
3. Names: `data_survey_responses_2025.csv`, `data_demographics_breakdown.csv`
4. Creates summary docs: `data_overview_methodology.txt` explaining each dataset
5. **Result**: 10 XLSX → 23 CSV files + 1 overview doc, all importable

### Example 4: Conference Notes & Recordings

**User**: "Organize my conference materials for a knowledge base"

**Input**:
- 12 audio recordings (MP3)
- 8 slide decks (PPTX)
- 15 handwritten notes (JPG photos)
- 5 schedule PDFs

**Process**:
1. Keeps MP3 as-is (NotebookLM transcribes on upload [web:3])
2. Converts PPTX → PDF
3. Keeps JPG photos (NotebookLM reads handwriting via OCR [web:10])
4. Renames all with pattern: `conf_session_title_speaker_date.ext`
5. **Result**: 40 sources in flat folder, ready for upload

## Common Patterns

### Academic Research
```
research_[topic]_[author]_[year].pdf
notes_[course]_[topic]_[date].md
textbook_[subject]_chapter_[n]_[title].pdf
```

### Business Projects
```
project_[name]_requirements.pdf
project_[name]_timeline.csv
meeting_[project]_[date]_notes.txt
client_[name]_proposal_final.docx
```

### Learning/Courses
```
course_[name]_lecture_[n]_[topic].pdf
course_[name]_readings_week_[n].pdf
course_[name]_assignment_[n].docx
```

### Personal Knowledge Base
```
article_[topic]_[author]_[date].pdf
book_notes_[title]_[author].md
tutorial_[skill]_[topic].pdf
reference_[tool]_documentation.pdf
```

## Pro Tips

### 1. Optimize for Search
Use descriptive names that include search keywords:
- **Good**: `tutorial_python_async_programming_advanced.pdf`
- **Bad**: `tutorial_5.pdf`

### 2. Topic-Based Splitting
When splitting large docs, split by topic not arbitrary page count:
- ✅ `handbook_benefits.pdf`, `handbook_policies.pdf`
- ❌ `handbook_part_1.pdf`, `handbook_part_2.pdf`

### 3. Date Formatting
Use ISO format (YYYY-MM-DD) for sortability:
- ✅ `meeting_notes_2026_02_04.txt`
- ❌ `meeting_notes_feb_4_2026.txt`

### 4. Consolidate Related Text
Merge small related text files before upload:
```bash
# Combine weekly notes into monthly
cat week_*.txt > meeting_notes_2026_02.txt
```

### 5. Extract Text from Scans
Scanned PDFs don't work in NotebookLM—must be text-selectable [web:6]:
```bash
# Test if text-selectable
pdftotext test.pdf - | head

# If blank, OCR it
ocrmypdf input.pdf output.pdf
```

### 6. Preserve Metadata
When converting, preserve dates:
```bash
# Keep original modification date
touch -r original.pptx converted.pdf
```

### 7. Use Prefixes for Ordering
Add numeric prefixes for logical ordering:
```
01_project_overview.pdf
02_project_requirements.pdf
03_project_design.pdf
04_project_implementation.pdf
```

### 8. Test Before Bulk Upload
Upload 2-3 files first to verify:
- Files process correctly
- Summaries are accurate
- Search works as expected
Then upload the rest.

## Best Practices Summary

### File Naming
✅ Descriptive snake_case: `research_ai_ethics_2025.pdf`
✅ Include key searchable terms
✅ Add dates in ISO format (YYYY-MM-DD)
✅ Keep under 100 characters
❌ Avoid generic names: `document1.pdf`
❌ No spaces or special characters
❌ Don't use version numbers (use dates instead)

### Format Selection
✅ PDF for presentations and mixed content
✅ CSV for spreadsheet data
✅ DOCX/TXT/MD for text documents
✅ MP3/WAV for audio (must have speech)
✅ JPG/PNG for images and handwritten notes
❌ PPTX (convert to PDF)
❌ XLSX (convert to CSV or Google Sheets)
❌ Scanned PDFs without OCR

### File Size Management
✅ Keep files under 200MB
✅ Keep text under 500k words
✅ Split large docs by topic/chapter
✅ Use multiple focused sources vs. one large source
❌ Upload 1000-page megadocs
❌ Combine unrelated content into single file

### Organization Structure
✅ Flat structure (one folder, all files)
✅ Descriptive names include folder context
✅ Group related files with naming prefixes
✅ Stay under 50 sources per notebook (free plan)
❌ Nested folder hierarchies
❌ Cryptic abbreviations in filenames
❌ Trying to fit everything in one notebook

## Integration with NotebookLM Features

### For Deep Research [web:10]
Organize sources by:
1. Background/overview documents
2. Detailed research papers
3. Data/statistics (CSV)
4. Reference materials

### For Audio Overviews [web:21]
- Include 5-10 key sources per topic
- Mix document types for comprehensive coverage
- Ensure sources have clear summaries

### For Study Guides [web:16]
- Textbook chapters as separate PDFs
- Lecture notes in chronological order
- Practice problems in separate files
- Reference sheets/cheatsheets

### For FAQs [web:9]
- Policy documents
- User manuals
- Support tickets (if text files)
- Common questions database

## Related Use Cases

- Academic literature review preparation
- Project knowledge base creation
- Corporate wiki conversion
- Learning resource organization
- Research dataset documentation
- Meeting notes consolidation
- Technical documentation compilation
- Course material structuring

---

## Implementation Checklist

Before organizing for NotebookLM:

- [ ] Identify target notebook topic/purpose
- [ ] Locate all source files
- [ ] Check file formats (note conversions needed)
- [ ] Estimate word counts for large files
- [ ] Plan topic-based splits for megadocs
- [ ] Design naming convention with keywords
- [ ] Create flat output directory
- [ ] Convert unsupported formats
- [ ] Apply descriptive naming
- [ ] Remove duplicates
- [ ] Verify all files <200MB and <500k words
- [ ] Test upload 2-3 files
- [ ] Upload remaining sources
- [ ] Verify NotebookLM processing/summaries
- [ ] Test search functionality
