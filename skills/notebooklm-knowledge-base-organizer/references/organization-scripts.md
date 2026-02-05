# Organization Scripts

Executable scripts for the organization phase: full organization runner, naming convention converter, flat structure builder, and file verification. Load this reference when you are ready to execute the organization step (after scoring, selection, and merging are complete).

---

## Full Organization Execution Script

```bash
#!/bin/bash
# notebooklm_organize.sh - Complete NotebookLM organization script
# Run after scoring, selection, and merging are finalized.

set -e

# Create output directory
OUTPUT="notebooklm_sources"
mkdir -p "$OUTPUT"

# Log file
LOG="notebooklm_org_$(date +%Y%m%d_%H%M%S).log"

echo "Starting NotebookLM organization..." | tee -a "$LOG"

# Convert PPTX to PDF (preserve timestamps)
for f in **/*.pptx; do
  [ -f "$f" ] || continue
  echo "Converting: $f" | tee -a "$LOG"
  soffice --headless --convert-to pdf "$f" --outdir "$OUTPUT"
  # Preserve original modification timestamp
  converted="$OUTPUT/$(basename "${f%.pptx}.pdf")"
  touch -r "$f" "$converted"
done

# Convert XLSX to CSV (preserve timestamps)
for f in **/*.xlsx; do
  [ -f "$f" ] || continue
  echo "Converting: $f" | tee -a "$LOG"
  base=$(basename "$f" .xlsx)
  ssconvert "$f" "$OUTPUT/${base}.csv"
  # Preserve original modification timestamp
  touch -r "$f" "$OUTPUT/${base}.csv"
done

# Copy and rename compatible files (preserve timestamps)
for f in **/*.{pdf,docx,txt,md}; do
  if [ -f "$f" ]; then
    # Generate descriptive name from path
    path_context=$(dirname "$f" | tr '/' '_' | sed 's/^\.//')
    base=$(basename "$f")
    new_name="${path_context}_${base}"

    # Clean to snake_case
    new_name=$(echo "$new_name" | tr '[:upper:]' '[:lower:]' | \
      sed 's/[^a-z0-9.]/_/g' | sed 's/__*/_/g' | sed 's/^_//')

    echo "Copying: $f -> $new_name" | tee -a "$LOG"
    cp "$f" "$OUTPUT/$new_name"
    # Preserve original modification timestamp
    touch -r "$f" "$OUTPUT/$new_name"
  fi
done

echo "" | tee -a "$LOG"
echo "Organization complete. Running verification..." | tee -a "$LOG"

# Run verification
cd "$OUTPUT"
echo -e "\n=== File Analysis ===" | tee -a "../$LOG"
for f in *.pdf; do
  [ -f "$f" ] || continue
  size=$(du -h "$f" | cut -f1)
  words=$(pdftotext "$f" - 2>/dev/null | wc -w || echo "N/A")
  echo "$f: $size, ~$words words" | tee -a "../$LOG"

  # Warn if over limits
  size_mb=$(du -m "$f" | cut -f1)
  if [ "$size_mb" -gt 200 ]; then
    echo "  WARNING: $f exceeds 200MB" | tee -a "../$LOG"
  fi
  if [ "$words" != "N/A" ] && [ "$words" -gt 500000 ]; then
    echo "  WARNING: $f exceeds 500k words" | tee -a "../$LOG"
  fi
done

total=$(ls -1 | wc -l)
echo "" | tee -a "../$LOG"
echo "Total sources: $total" | tee -a "../$LOG"
if [ "$total" -le 50 ]; then
  echo "OK: Under 50-source limit" | tee -a "../$LOG"
else
  echo "WARNING: Over 50-source limit ($total sources)" | tee -a "../$LOG"
fi
```

---

## Naming Convention Script

Apply snake_case naming with descriptive context. Always preserve timestamps.

```bash
#!/bin/bash
# rename_snake_case.sh - Convert filenames to NotebookLM-friendly snake_case
# Pattern: category_topic_descriptor_date.ext
# Examples:
#   research_quantum_computing_basics_2025.pdf
#   meeting_notes_project_kickoff_2026_01.txt
#   data_sales_figures_q4_2025.csv

TARGET_DIR="${1:-.}"

cd "$TARGET_DIR" || exit 1

for f in *; do
  [ -f "$f" ] || continue

  # Save original for timestamp preservation
  original="$f"

  # Convert to snake_case, remove special chars
  new_name=$(echo "$f" | tr '[:upper:]' '[:lower:]' | \
    sed 's/[^a-z0-9.]/_/g' | sed 's/__*/_/g' | \
    sed 's/^_//;s/_\././')

  if [ "$f" != "$new_name" ]; then
    mv "$f" "$new_name"
    # Preserve original modification timestamp
    touch -r "$original" "$new_name" 2>/dev/null || true
    echo "Renamed: $f -> $new_name"
  fi
done
```

---

## Flat Structure Implementation Script

Flatten nested directories into a single folder. Include path context in filenames.

```bash
#!/bin/bash
# flatten_structure.sh - Convert nested dirs to flat NotebookLM-ready structure
# NotebookLM works best with flat source lists, no nested folders.

SOURCE_DIR="${1:-.}"
OUTPUT_DIR="${2:-notebooklm_sources}"

mkdir -p "$OUTPUT_DIR"

find "$SOURCE_DIR" -type f \( -name "*.pdf" -o -name "*.docx" -o -name "*.txt" -o -name "*.md" -o -name "*.csv" \) | while read f; do
  # Build a descriptive name from the directory path
  dir=$(dirname "$f" | sed "s|^${SOURCE_DIR}/||" | tr '/' '_' | sed 's/^_//')
  base=$(basename "$f")
  name="${base%.*}"
  ext="${base##*.}"

  # Combine path context with filename
  if [ -n "$dir" ] && [ "$dir" != "." ]; then
    new_name="${dir}_${name}.${ext}"
  else
    new_name="${name}.${ext}"
  fi

  # Clean to snake_case
  new_name=$(echo "$new_name" | tr '[:upper:]' '[:lower:]' | \
    sed 's/[^a-z0-9.]/_/g' | sed 's/__*/_/g' | sed 's/^_//')

  cp "$f" "$OUTPUT_DIR/$new_name"
  # Preserve original modification timestamp
  touch -r "$f" "$OUTPUT_DIR/$new_name"
  echo "$f -> $OUTPUT_DIR/$new_name"
done

echo ""
echo "Flattened $(find "$OUTPUT_DIR" -type f | wc -l) files into $OUTPUT_DIR/"
```

---

## File Size and Word Count Verification

Run after organization to confirm all sources meet NotebookLM limits.

```bash
#!/bin/bash
# verify_sources.sh - Check all organized sources meet NotebookLM limits
# Limits: 200MB per file, 500k words per file, 50 sources total

SOURCE_DIR="${1:-notebooklm_sources}"
ERRORS=0

echo "=== NotebookLM Source Verification ==="
echo "Directory: $SOURCE_DIR"
echo ""

total=$(find "$SOURCE_DIR" -type f | wc -l)
echo "Total sources: $total"
if [ "$total" -gt 50 ]; then
  echo "  WARNING: Exceeds 50-source limit"
  ERRORS=$((ERRORS + 1))
elif [ "$total" -gt 48 ]; then
  echo "  NOTE: Near limit, consider reserving slots for future additions"
fi
echo ""

echo "=== Per-File Checks ==="
find "$SOURCE_DIR" -type f | sort | while read f; do
  size_mb=$(du -m "$f" | cut -f1)
  mod_date=$(stat -f "%Sm" -t "%Y-%m-%d" "$f")

  # Check size limit
  if [ "$size_mb" -gt 200 ]; then
    echo "FAIL [SIZE]: $f (${size_mb}MB > 200MB)"
    ERRORS=$((ERRORS + 1))
  fi

  # Check word count for text-based files
  ext="${f##*.}"
  case "$ext" in
    pdf)
      words=$(pdftotext "$f" - 2>/dev/null | wc -w)
      if [ "$words" -gt 500000 ]; then
        echo "FAIL [WORDS]: $f ($words words > 500k)"
        ERRORS=$((ERRORS + 1))
      fi
      echo "OK: $f (${size_mb}MB, ${words} words, modified: $mod_date)"
      ;;
    txt|md|csv)
      words=$(wc -w < "$f")
      if [ "$words" -gt 500000 ]; then
        echo "FAIL [WORDS]: $f ($words words > 500k)"
        ERRORS=$((ERRORS + 1))
      fi
      echo "OK: $f (${size_mb}MB, ${words} words, modified: $mod_date)"
      ;;
    *)
      echo "OK: $f (${size_mb}MB, modified: $mod_date)"
      ;;
  esac
done

echo ""
if [ "$ERRORS" -eq 0 ]; then
  echo "All checks passed."
else
  echo "$ERRORS issue(s) found. Fix before uploading."
fi
```
