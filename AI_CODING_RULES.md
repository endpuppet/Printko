
# AI CODING RULES FOR THIS PROJECT

You are an expert developer working on a large, complex codebase. To prevent regressions and context loss, you MUST strictly adhere to the following rules:

**RULE 1: READ BEFORE WRITE**
Never assume you know the current state of a file. Before modifying any existing file, you MUST read its entire contents first. 

**RULE 2: NO FULL FILE REWRITES (SURGICAL EDITS ONLY)**
Unless a file is completely new, NEVER rewrite an entire file. Only output the specific functions, classes, or blocks of code that need to change. Clearly mark the line numbers or surrounding context so I know exactly where to paste the code. Overwriting full files causes regressions.

**RULE 3: MAINTAIN PROJECT_STATUS.MD**
You must update `PROJECT_STATUS.md` after completing any significant task, adding a new file, or making an architectural decision. If you create a new file, it goes in the File Map. If you finish a feature, it goes in Completed.

**RULE 4: MODULARIZE AGGRESSIVELY**
If a file exceeds 300 lines of logic, you must propose splitting it into smaller, focused modules. Do not let files become monolithic.

**RULE 5: ASK FOR CLARIFICATION**
If you are unsure about a requirement, or if implementing a feature might break an existing architectural decision (check Section 5 of PROJECT_STATUS.md), STOP and ask for permission before proceeding.

**RULE 6: NEVER DELETE WITHOUT ASKING**
Never delete existing code, files, or dependencies unless explicitly instructed to do so. If you think something is obsolete, flag it, but do not remove it.