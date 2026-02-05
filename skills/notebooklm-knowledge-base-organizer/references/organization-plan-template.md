# Organization Plan Template

Markdown template to present to the user before executing changes. Covers current state assessment, source selection strategy, consolidation tracking, proposed structure, and compatibility checklist. Load this reference when you are ready to propose the organization plan (after analysis, scoring, and merge identification).

---

## Template

Copy and fill in the template below. Replace all bracketed placeholders with actual values.

```markdown
# NotebookLM Knowledge Base Organization Plan

## Current State
- [N] files across [N] nested folders
- Formats: [N] PDFs, [N] DOCX, [N] PPTX, [N] XLSX, [N] other
- Total size: [X] GB
- Issues:
  - [N] PPTX need conversion to PDF
  - [N] XLSX need conversion to CSV
  - [N] PDFs exceed 200MB (need splitting)
  - [N] duplicate files found
  - Deep nested structure ([N] levels)
  - [If >50: WARNING: N sources exceeds 50-source limit by M]

## Source Selection Strategy

### Prioritization Applied
- **Method**: Significance scoring (Relevance + Recency + Uniqueness + Density)
- **Threshold**: Sources scoring [N]+ auto-selected ([N] sources)
- **Manual review**: Sources scoring [N]-[N] ([N] sources reviewed, [N] kept)
- **Final selection**: [N] sources ([N] slots reserved for future additions)

### Sources Excluded ([N] total)
**Low-priority sources (score <25)**: [N] files
- Example: `[filename]` (score: [N])
  - Reason: [why excluded]
- Example: `[filename]` (score: [N])
  - Reason: [why excluded]

**Duplicates identified**: [N] files
- `[old_file]` (keeping `[kept_file]` instead)
- `[old_file]` (keeping `[kept_file]` instead)

**Merged into consolidated sources**: [N] files
- [N] [type] -> [N] [merged result description]
- [N] [type] -> [N] [merged result description]
- See "Consolidation Performed" section below

### Consolidation Performed

**Time-Series Merges** (savings: [N] sources)
1. [Description of merge] ([N] files) -> `[merged_filename]`
   - Original: [N] separate [size] [type]
   - Merged: 1 chronological [size] document
   - Dates preserved: [YES -- individual dates retained as section headers]
   - Information loss: None

**Topic-Based Merges** (savings: [N] sources)
1. [Description] ([N] files) -> `[merged_filename]`
   - Combined: [what was combined]
   - Result: [description of merged output]
   - Words: [N] (well under 500k limit)

**Format Consolidation** (savings: [N] sources)
1. [Description] ([N] x [N] formats) -> [N] complete docs
   - [input_file] + [input_file] -> [output_file]

**Total savings: [N] sources through strategic merging**

## Proposed Structure

```
notebooklm_sources/
+-- [filename] ([size])
+-- [filename] (converted from [format])
+-- [filename] (converted from [format])
+-- [filename] (merged [N] docs)
+-- [filename] (split from [size])
+-- [filename]
+-- [filename]
```

Total: [N] sources (under 50-source limit)

## Changes to Make

### Conversions ([N] files)
1. Convert [N] PPTX -> PDF
2. Convert [N] XLSX -> CSV

### Splits ([N] files)
1. `[filename]` ([size]) -> [N] parts
2. `[filename]` ([size]) -> [N] parts

### Deletions ([N] duplicates)
- `[filename]` (keep [alternative])
- `[filename]` (keep [alternative])

### Renames (all files)
- Apply snake_case naming
- Add descriptive context from folder path
- Include dates in ISO format (YYYY-MM-DD) where relevant

## NotebookLM Compatibility Checklist

Verify all items before proceeding:

- [ ] All files <200MB after splits
- [ ] All files <500k words after splits
- [ ] All formats supported by NotebookLM
- [ ] Total sources: [N] (under 50 limit, [N] slots reserved)
- [ ] Flat structure for easy upload
- [ ] Source selection optimized for quality over quantity
- [ ] Strategic merging preserves information density
- [ ] Original file timestamps preserved on converted/copied files
- [ ] Merged time-series documents retain per-entry date headers

## Selection Justification
- **Coverage**: All critical topics represented
- **Quality**: Average source score: [N] (high quality maintained)
- **Recency**: [N]% of sources from last 2 years
- **Uniqueness**: Minimal content overlap after merging
- **Future-proof**: [N] slots available for updates

Ready to proceed? (yes/no/modify)
```
