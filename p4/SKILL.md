---
name: p4
description: Perforce (P4) version control workflows. Use this skill whenever working with Perforce — including checking out, adding, deleting, or submitting files via p4 commands. Automatically apply when any "Permission denied" or "Read-only file system" error occurs during file editing, or when the user mentions p4, Perforce, changelists, workspaces, depots, or p4 submit/edit/add/revert. Always use this skill before touching any file in a Perforce workspace.
---
 
# Perforce (P4) Workflow Instructions
 
You are working inside a Perforce (P4) workspace. Unlike Git, **all files are read-only by default** and must be explicitly checked out before modification.
 
Strictly follow these P4 workflow rules whenever handling files.
 
---
 
## 1. Editing an Existing File (Required Before Any Edit)
 
Before modifying a file or using any edit tool, always run:
 
```bash
p4 edit <file_path>
```
 
This grants write permission. If you encounter a `Permission denied` or `Read-only file system` error, immediately run `p4 edit` on the affected file and retry.
 
---
 
## 2. Adding a New File
 
After creating a new file, register it with Perforce:
 
```bash
p4 add <file_path>
```
 
---
 
## 3. Deleting a File
 
Use Perforce's delete command instead of `rm`:
 
```bash
p4 delete <file_path>
```
 
---
 
## 4. Checking Status & Reverting Changes
 
| Goal | Command |
|------|---------|
| List files currently open for edit | `p4 status` or `p4 opened` |
| Revert a specific file | `p4 revert <file_path>` |
| Revert all unchanged files | `p4 revert -a` |
 
---
 
## 5. Submitting Changes
 
When the user asks you to submit/commit changes, follow these steps exactly — do not skip any:
 
1. **Check pending changes**
   `bash
   p4 status
   `
 
2. **Export a changelist template**
   `bash
   p4 change -o > change.txt
   `
 
3. **Fill in the description** Edit `change.txt` — replace `<enter description here>` with a clear description of the work done. Remove any files from the `Files:` section that should be excluded from this submission.
 
4. **Create the changelist**
   `bash
   p4 change -i < change.txt
   `
   Note the changelist number printed in the output.
 
5. **Submit to the server**
   `bash
   p4 submit -c <changelist_number>
   `
 
6. **Clean up**
   `bash
   rm change.txt
   `
 
---
 
## 6. Viewing Shelved File Diffs Against Last Revision
 
To see the diff between a shelved file in another user's changelist and its last submitted revision:
 
`bash
p4 diff2 <file_path>@=<changelist_number> <file_path>#head
`
 
- `@=<changelist_number>` — refers to the shelved version in that specific changelist
- `#head` — refers to the latest submitted revision in the depot
 
**Example — diff all shelved files in CL 12345 against their head revisions:**
 
```bash
p4 describe -S -du 12345
```
 
- `-S` — shows shelved files instead of submitted files
- `-du` — shows the unified diff of each file against its last revision
 
This is the most convenient single command to review someone else's shelved changelist diff.
 
**Example — diff a single shelved file:**
 
```bash
p4 diff2 //depot/path/to/file.cpp@=12345 //depot/path/to/file.cpp#head
```

---

## 7. Viewing Full File Contents & Handling Encoding Issues

If you need to read the complete context of a file at a specific shelved revision (e.g., for a thorough code review), you must use `p4 print`. 

However, outputting directly to the terminal may cause encoding errors (e.g., `No Translation for parameter 'data'`) or raw hex strings. **To avoid this, always save the output to a temporary file first:**

```bash
p4 print -q //depot/path/to/file.cs@=<changelist_number> > temp_review.cs
cat -n temp_review.cs
rm temp_review.cs
```

- `-q` — suppresses the header line, outputting only the file content.
 
---
 
## Quick Reference
 
```bash
p4 edit <file>       # Check out for editing
p4 add <file>        # Stage new file
p4 delete <file>     # Mark file for deletion
p4 revert <file>     # Undo changes to a file
p4 revert -a         # Undo all unchanged files
p4 status            # Show open files
p4 opened            # Show open files (alternate)
p4 submit -c <num>               # Submit a numbered changelist
p4 describe -S -du <cl_num>      # Show shelved file diffs vs head revision
p4 diff2 <file>@=<cl_num> <file>#head  # Diff single shelved file vs head
p4 print -q <file>@=<cl_num> > temp.cs # Read full shelved file without encoding errors
```