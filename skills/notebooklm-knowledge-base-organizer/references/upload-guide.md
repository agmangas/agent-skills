# Upload Guide

Instructions for uploading organized sources to NotebookLM, including direct upload and Google Drive methods, usage tips, and maintenance procedures. Load this reference at the final step after organization is complete and verified, to present upload instructions to the user.

---

## Upload Summary Template

Present this to the user after organization. Fill in actual values.

```markdown
# NotebookLM Sources Ready

## Summary
- **[N] sources** organized in `notebooklm_sources/`
- All files <200MB and <500k words
- Flat structure with descriptive snake_case names
- Formats: [N] PDF, [N] CSV, [N] DOCX, [N] TXT/MD

## How to Upload to NotebookLM

### Option 1: Direct Upload
1. Go to notebooklm.google.com
2. Create new notebook or open existing
3. Click "Sources" then "Upload"
4. Select all files from `notebooklm_sources/`
5. Wait for processing (summaries appear when done)

### Option 2: Google Drive (Recommended for >20 files)
1. Upload `notebooklm_sources/` folder to Google Drive
2. In NotebookLM, use the "Drive URLs" option
3. Share the Drive folder URL
4. NotebookLM imports all files at once

## Files Organized

### [Category 1] ([N] files)
- [filename]
- [filename]
- [...]

### [Category 2] ([N] files)
- [filename]
- [filename]
- [...]

### [Category 3] ([N] files)
- [filename]
- [filename]
- [...]
```

---

## NotebookLM Usage Tips

### Better Retrieval
- Use source-specific queries: "According to project_requirements..."
- Break large questions into focused queries
- Use the "Select sources" feature to narrow searches to specific files

### Avoiding Limit Issues
- Reserve 2-3 source slots for future additions
- Create separate notebooks by topic if you need to add more sources
- Free plan: 50 chat queries/day, 3 audio overviews/day

### Generating Outputs
- **FAQ**: "Create an FAQ from project docs"
- **Study Guide**: "Generate study guide for research analysis"
- **Audio Overview**: Click "Audio Overview" in Notebook Guide
- **Timeline**: "Create a timeline from meeting notes"

---

## Maintenance Instructions

### Adding New Sources
1. Place new files in `notebooklm_sources/`
2. Convert and rename using the same conventions (see `organization-scripts.md`)
3. Verify word count: `pdftotext file.pdf - | wc -w`
4. Preserve timestamps: `touch -r original.pptx converted.pdf`
5. Upload to existing notebook
6. Track remaining source budget (50 minus current count)

### Updating Existing Sources
- NotebookLM does not auto-update sources
- Delete the old version in NotebookLM, then upload the new one
- Alternatively, create a new notebook for the updated version

### Periodic Review
- Re-score sources if the collection grows significantly
- Replace outdated sources with newer ones rather than just adding
- Re-run merging if many small new files accumulate
