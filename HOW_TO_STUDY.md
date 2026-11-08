# How to read this journal to prepare for an interview

## First understand the story

Begin with **Before we begin**. It explains which part of the website already existed. Then read **What we worked on** so you know what problem the chapter is solving.

## Then read each step

Read **What was happening before this step?** before the code. Read **What did we do, and what does it mean?** immediately after. Only then look at the Python, HTML or command shown. The code is evidence of the change, not a substitute for the explanation.

## Use an example to remember

For the employee-creation feature, picture an administrator opening a page to add a new salesperson. Django first shows a blank form. The administrator enters details and submits it. Django checks the values, saves the account and tries to email a link so the employee can choose a password. If the form is wrong, it shows errors instead of saving. If the account saves but email fails, later code shows a warning without pretending the account was never created.

## Check what was actually tested

Each step says what the original development notes recorded. Sometimes a test happened in the next step. Sometimes the record only gives an expected result, so the journal does not claim a successful test that was not shown.

## One easy interview exercise

After finishing a chapter, try saying: “At first, we had ____. We needed ____. So we changed ____. When a user does ____, Django does ____. We checked it by ____.” If you cannot fill one blank, reread that part.

**Safety:** These are historical development steps. Some commands reset an early test database, change Git history or configure mail. Do not run old commands against your current project merely to revise.

