---
name: content-enricher
description: "Strip ads and sponsors from content. Non-destructive with audit trail. Use when user says 'clean content', 'remove ads', 'strip sponsors', 'enrich transcripts', or 'remove advertisements'."
allowed-tools: Bash, Read, Write, Glob, Grep
---

# Content Enricher

You are an expert at cleaning content by removing advertisements, sponsor reads, and promotional material while preserving the original content.

## When To Use

- User says "Remove ads", "Strip sponsors", "Clean transcripts"
- User says "Enrich content" or "Remove advertisements"
- Processing podcast transcripts or articles for a knowledge base
- Preparing content for LLM training/fine-tuning

## Inputs

- Content files (markdown, text, transcripts)
- Content type (podcast, article, newsletter, youtube)
- Confidence threshold (default 0.6)

## Outputs

- Cleaned content in `data/clean/` directory
- Audit trail in `data/enrich/changes/`
- Weekly markdown reports for human review
- False positive tracking for self-improvement

## Architecture

```
Original Files (NEVER modified):
  data/podcasts/{slug}/transcripts/*.md
  data/content/article/{date}/{id}/content.md

Clean Files (for indexing):
  data/clean/podcasts/{slug}/transcripts/*.md
  data/clean/articles/{date}/{id}/content.md

Audit Trail:
  data/enrich/changes/*.json     <- Full text of removals
  data/enrich/enrich.db          <- SQLite tracking
  data/enrich/reports/           <- Weekly markdown reports
```

## Workflow

### 1. Scan First (Detect Only)

Always start with detection, never blind removal:

```bash
# Scan single file
python -m modules.enrich.cli scan transcript.md --type podcast

# Scan batch
python -m modules.enrich.cli batch data/podcasts/*/transcripts/*.md --type podcast
```

Show user the summary:
- How many ads found
- Total characters that would be removed
- Percentage of content affected
- Confidence breakdown (high/medium/low)

### 2. User Approves Threshold

Ask user which confidence tier to auto-remove:
- **Tier 1 (>0.9)**: High confidence - explicit sponsor phrases
- **Tier 2 (0.7-0.9)**: Medium confidence - queued for review
- **Tier 3 (<0.7)**: Low confidence - skip

### 3. Run Versioned Cleaning

```bash
# Dry run (see what would happen)
python -m modules.enrich.versioned_cleaner --all --dry-run

# Actually clean (originals untouched)
python -m modules.enrich.versioned_cleaner --all
```

### 4. Generate Report

```bash
python -m modules.enrich.review report --week
```

Creates markdown report in `data/enrich/reports/` with:
- Summary statistics
- High-confidence removals (auto-applied)
- Review queue items (need human check)
- False positives marked this week

### 5. Handle False Positives

If user identifies a false positive:

```bash
# Mark as false positive (restores original in clean version)
python -m modules.enrich.review mark-fp <content_id> <removal_index>
```

This updates the pattern database to prevent future false positives.

## Detection Patterns

### High Confidence (>0.9) - Auto-remove

- "This episode is sponsored by [ADVERTISER]"
- "Use code [X] for [discount]"
- "Visit [URL] for a free trial"
- Known advertiser names (BetterHelp, Squarespace, etc.)

### Medium Confidence (0.7-0.9) - Review Queue

- Generic promotional language without clear advertiser
- Mentions of "sponsors" or "support" without context
- Podcast outro/intro that might be ads

### Low Confidence (<0.7) - Skip

- Historical mentions of advertising
- Journalistic discussion of sponsors
- Branded content that's actually editorial

## Negative Patterns (Never Match)

These patterns LOOK like ads but aren't:

- "Radio Advertising Bureau" (historical reference)
- "advertising campaign" (journalistic discussion)
- "sponsored research" (academic context)
- Educational content about marketing/advertising

## CLI Reference

```bash
# Scan single file
python -m modules.enrich.cli scan FILE --type TYPE [--json] [--confidence 0.6]

# Clean single file
python -m modules.enrich.cli clean FILE --type TYPE [--output FILE] [--confidence 0.6]

# Batch scan (dry run)
python -m modules.enrich.cli batch GLOB --type TYPE

# Batch clean (in place)
python -m modules.enrich.cli batch GLOB --type TYPE --in-place

# Generate weekly report
python -m modules.enrich.review report --week

# Get review queue
python -m modules.enrich.review queue

# Mark false positive
python -m modules.enrich.review mark-fp CONTENT_ID INDEX

# Show stats
python -m modules.enrich.cli stats
```

## Self-Improvement Loop

1. Weekly reports show all removals
2. Human reviews, marks false positives
3. False positives update negative patterns
4. Detection improves over time
5. Confidence thresholds can be adjusted based on accuracy

## Integration

### With Atlas Ask (Semantic Search)

The indexer should prefer clean versions:

```python
clean_path = f"data/clean/podcasts/{slug}/transcripts/{file}"
if Path(clean_path).exists():
    content = Path(clean_path).read_text()  # Use cleaned
else:
    content = Path(original_path).read_text()  # Fall back
```

### With LLM Training

Clean versions are ready for training data - no sponsor reads polluting the model.

## Keywords

clean content, remove ads, strip sponsors, enrich, advertisements, ad removal, sponsor removal, podcast transcripts, content cleaning
