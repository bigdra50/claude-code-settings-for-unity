Synchronize English documentation from `rules/` and `commands/` directories to Japanese translations in `rules_jp/` and `commands_jp/` directories.

Follow these steps:

## Phase 1: Discovery and Analysis (Parallel)

Use the Task tool to launch multiple agents in parallel for the following tasks:

1. **List source files**: Identify all `.md` files in `rules/` and `commands/` directories
2. **Check target directories**: Verify if `rules_jp/` and `commands_jp/` exist, create if needed
3. **Inventory existing translations**: List all existing files in `rules_jp/` and `commands_jp/` (if exist)

## Phase 2: Comparison and Planning

After Phase 1 completes:

1. For each file in `rules/` and `commands/`:
   - Check if corresponding file exists in `rules_jp/` or `commands_jp/`
   - If exists: Read both English source and Japanese translation
   - Compare by checking if the structure/content length differs significantly (>10% difference in line count or major structural changes)
   - If not exists or differs: Mark for translation

2. Create a list of files that need translation/update with reasons:
   ```
   Files requiring update:
   rules/:
   - 00-code-of-conduct.md (does not exist in rules_jp/)
   - 01-coding.md (content updated, line count differs by 15%)

   commands/:
   - run-tests.md (does not exist in commands_jp/)
   - create-doc.md (content updated, line count differs by 20%)
   ```

3. Show the list to user and confirm before proceeding

## Phase 3: Translation (Parallel)

After user confirmation, use the Task tool to launch multiple translation agents in parallel:

For each file requiring update:
1. Read the English source file from `rules/` or `commands/`
2. Translate to Japanese while:
   - Preserving all markdown formatting (headers, lists, code blocks, links)
   - Keeping code examples unchanged
   - Maintaining the same structure and section order
   - Translating technical terms appropriately for Japanese developers
   - Using natural Japanese technical writing style
   - Keeping URLs and file paths unchanged
3. Write the translated content to `rules_jp/[filename]` or `commands_jp/[filename]` accordingly
4. Verify the file was written successfully

## Translation Guidelines

- Use です・ます調 for consistency
- Technical terms: Use commonly accepted Japanese terms when available, otherwise use katakana or keep English
- Code blocks: Do not translate
- Commands, file paths, URLs: Do not translate
- Header structure: Maintain exact same hierarchy
- Comments in code: Translate if they are explanatory, keep if they are code-related

## Phase 4: Verification

After all translations complete:
1. List all files in `rules_jp/` and `commands_jp/` to confirm synchronization
2. Report summary:
   - Number of files translated (by directory)
   - Number of files updated (by directory)
   - Any errors encountered

## Important Notes

- Use Task tool with multiple parallel agents for Phase 1 and Phase 3 to maximize efficiency
- Each file translation should be an independent task
- Preserve exact formatting and structure
- Keep all technical references (URLs, commands, file paths) in original form
- Maintain consistency in terminology across all translated files
