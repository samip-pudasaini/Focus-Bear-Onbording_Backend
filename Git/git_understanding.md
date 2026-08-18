# Git Understanding

# Pull Request
## Why are PRs important in a team workflow?
    Pull requests are important because they allow developers to review changes before they are merged into the main branch. Instead of directly changing the main codebase, developers can work on separate branches and use a PR to propose their changes. Other team members can review the code, provide feedback, identify bugs, and suggest improvements. PRs also provide a record of what was changed and why, which makes collaboration adn project history easier to understand.

## What makes a well-structured PR?
    A well-structured PR should have a clear and meaningful title that explains what was changed. The description should provide enough context about the purpose of the change, the specific modifications made, and any relevant testing that was completed. A PR should ideally be focused on one task rather than containing many unrelated changes. Linking the PR to a relevant issue can also help connect the implementation to the original requirement. Keeping changes small and organised makes the PR easier for other developers to understand

## What did you learn from reviewing an open-source PR?
    Reviewing an open-source PR helped me understand that code review is not just about finding mistakes. Reviewers also discuss the design, maintainability, readability, and potential impact of a change. I observed how reviewers asked questions and requested changes, while the developer responded by updating the implementation. This showed me how PR discussions support collaboration and help improve the quality of code before it becomes part of the main project.


# Git Commit Messages
    Best practices: Keep your messages short, Make your messages as descriptive as possible while still being short, Be specific

## What makes a good commit message?
    Make the messages short, most messages are viewed in someone's terminal, or in the history on a PR in github, and only the first line is shown by default, longer messages are hidden.
    Make the messages as descriptive as possible while keeping it short. This can be done by being specific.

    examples: 6904a37  adds test for rate limit backoff behavior (4 hours ago) <justin> 
    This is good, but the better one would be
    6904a37  test summary_client rate limit backoff behavior (4 hours ago) <justin>

    This points out what test is added and for which file.

## How does a clear commit message help in team collaboration?
    As the example given above:
        6904a37  test summary_client rate limit backoff behavior (4 hours ago) <justin>

    This helps make it easier to understand what changes were made in the current commit.

    A clear message helps make teammates quickly and identify changes. This makes debugging and finding previous changes easier. Improves communication between developers. Make code reviews and pull request easier to understand. Help identify which commit introduced or fixed an issue.

## How can poor commit messaged cause issues later?\
    Poor commit messages can cause issues later because they make it difficult for team members to understand what was changed and why. This can make debugging, code reviews, and merging branches more difficult and time-consuming. Developers may also struggle to find or revert specific changes when needed. Clear commit messages provide useful context and help the team understand the project's history.


# Git bisect
    git bisect is a debugging tool that uses binary search to find the exact commit that introduced a bug or regression into your codebase. By splitting the commit history in half at each step, it dramatically reduces the number of commits you need to manually test. For example, searching through 1000 commits only take about 10 steps.

    You would use git bisect in a real-world debugging situation when a bug exists in the current version of a project, but you do not know which recent commit introduced it. For example, if an application was working correctly last week but is now crashing after several developers made changes, you could use git bisect to identify the problematic commit. you would mark a known working commit as good and the current broken commit as bad, then Git would check commits between them using binary search. This is useful because it can quickly narrow down the exact change responsible for the bug instead of manually reviewing every commit.

    git bisect is much faster and more efficient than manually reviewing commits, especially when a project has many commits. Instead of checking each commit one by one, git bisect uses a binary search to repeatedly narrow down the range between a known working and broken version. 
    Manual reviewing can take a lot of time and may require reading large amounts of code, while git bisect can identify the problematic commit with only a few tests. How ever, manual review can still be useful after git bisect identifies the commit because developers can then examine the exact code changes to understand and fix the problem.

# Git Commands and when to use them
## What does each command do?
    ``` git checkout main -- <file> ```-> This command restores a specific file to the version that exists on the main branch. It is useful because it only affects the selected file instead of changing other files or branches. During testing, I modified git_understanding.md and then used git checkout main -- git_understanding.md to restore it.

    ``` git cherry-pick <commit> ``` -> git cherry-pick applies a specific commit from another branch to the current branch without merging the entire branch. During testing, I created a commit on test-rip, switched to main, and cherry-picked that commit. This allowed me to bring only the required change into main.

    ``` git log ``` ->  git log displays the commit history of a repository. It shows information such as commit IDs, authors, dates, and commit messages. I used it to view the changes I had made and find the commit ID needed for the cherry-pick test.

    ``` git blame <file> ``` -> git blame shows which commit and author last modified each line of a file. It can help trace a particular line of code back to the change that introduced or modified it. I used it to examine the history of my file and see which commits were responsible for different lines.

## When would you use these commands in a real project?
    I would use these commands regularly when working on a large project with multiple developers. git checkout main -- <file> would be useful when I accidentally make unwanted changes to one file but want to keep my other work. git cherry-pick would be useful when a specific bug fix or important change needs to be moved from one branch to another without merging unfinished work. git log would help me understand how the project has developed and investigate previous changes. git blame would be useful when debugging or trying to understand why a particular line of code was changed and which commit introduced it.

## What surprised you while testing these commands?
    What surprised me was how specific Git can be when working with changes. I initially expected git checkout main -- <file> to affect the whole branch, but it only restored the selected file. I also found git cherry-pick useful because it allowed me to take one specific commit without merging everything from the other branch. Using git log and git blame also showed me how much information Git keeps about the history of a project. These commands made me realise that Git is not only useful for saving versions of code but also for investigating, managing, and understanding changes in a project.

# Git Branching
## Why is pushing directly to main problematic?
    Pushing directly to main is problematic, or untested code into the main version of the project. If multiple developers push changes directly to main, it can also become difficult to track who made a change or why it was made. A mistake pushed to main could affect other team members and potentially break the application. Using branches allows developers to work on changes separately before they are added to the main project.\

## How do branches help with reviewing code?
    Branches allow developers to work on features, bug fixes, or other changes without affecting main. Once the work is ready, the branch can be submitted as a pull request so other team members can review the changes before they are merged. This gives the team an opportunity to identify bugs, suggest improvements, and make sure the code meets project requirements. It also makes collaboratiuon safer because changes are reviewed before becoming part of the main codebase.

## What happens if two people edit the same file on different branches?
    If two people edit different parts of the same file on separate branches, Git can oftern merge the changes automatically. However, if they modify the same lines or nearby sections, Git may produce a merge conflict. The developers then need to manually review the conflicting changes and decide which version to keep or how to combine them. This is one reason why communicating with team members and regularly synchronising branches is important in a collaborative project.
![branches create](<Screenshot 2026-08-18 162715.png>)
![minor changes and commit to new branch](<Screenshot 2026-08-18 163117.png>)

# Staging vs Committing
## What is the difference between staging and commiting?
    Imagine you are doing online shopping. You browse through the pages of your favorite online store and add some products to your shopping cart. The shopping cart is like staging. Things are there ready to be paid for. You can keep adding stuff to it or remove some from it.
    When you are done shopping, you go and pay for the things in your shopping cart. That can be compared to making a commit. Now there is a record somewhere of what you have bought and when. There is a history.
    Notice that you can easily change your mind when things are in staging, but once you have made a commit things are hard to change (although not impossible!).

## Why does Git separate these two steps?
    The fact that you need to stage and commit separately in Git has many benefits. Generally it is a good idea to make your commits small. And to make sure that all the changes that are included in one commit are related to each other.

    For examplet a commit that fixes a bug in Feature A should not also include an improvement you made to Feature B. If you have done two things at once, you should not commit everything at once. You should seperate them by staging the bug fixes and committing those first and then staging the improvements to Feature and making a second commit.

    That way, if someone looks at the Git history of your project, they are not confused by one giant commit that did a lot of things. They can see clearly that you performed two different tasks: one bug fix in Feature A and one improvement to Feature B.

## When would you want to stage changes without committing?
    You would want to stage changes without committing when you want to prepare specific changes for the next commit but are not ready to permanently record them in the Git history.
    For example, you might stage your completed changes, review them with 
    git diff --cached, 
    and make sure everything is correct before committing. It can also be useful when you have multiple changes but only want to commit some of them.

