# Git Understanding

# Pull Request

## Why are PRs important in a team workflow?

Pull requests are important because they allow developers to review changes before
they are merged into the main branch. Instead of directly changing the main
codebase, developers can work on separate branches and then submit their work for
review.

## What makes a well-structured PR?

A well-structured PR should have a clear and meaningful title that explains what
was changed. The description should provide enough context about the purpose of
the change, the specific modifications made, and any relevant testing or impact.

## What did you learn from reviewing an open-source PR?

Reviewing an open-source PR helped me understand that code review is not just
about finding mistakes. Reviewers also discuss the design, maintainability,
readability, and potential impact of any changes on the entire codebase.

# Git Commit Messages

Best practices: Keep your messages short, Make your messages as descriptive as
possible while still being short, Be specific

## What makes a good commit message?

Make the messages short, most messages are viewed in someone's terminal, or in
the history on a PR in github, and only the first line is shown by default,
longer messages are hidden.
Make the messages as descriptive as possible while keeping it short. This can be
done by being specific.

examples: 6904a37  adds test for rate limit backoff behavior (4 hours ago)
<justin>
This is good, but the better one would be
6904a37  test summary_client rate limit backoff behavior (4 hours ago) <justin>

This points out what test is added and for which file.

## How does a clear commit message help in team collaboration?

As the example given above:
6904a37  test summary_client rate limit backoff behavior (4 hours ago) <justin>

This helps make it easier to understand what changes were made in the current
commit.

A clear message helps make teammates quickly and identify changes. This makes
debugging and finding previous changes easier. Improves communication between
developers. Make code reviews and pull request discussions clearer.

## How can poor commit messaged cause issues later?

Poor commit messages can cause issues later because they make it difficult for
team members to understand what was changed and why. This can make debugging,
code reviews, and merging branches harder.

# Git bisect

git bisect is a debugging tool that uses binary search to find the exact commit
that introduced a bug or regression into your codebase. By splitting the commit
history in half at each step, it efficiently narrows down the problematic commit.

You would use git bisect in a real-world debugging situation when a bug exists in
the current version of a project, but you do not know which recent commit
introduced it. For example, if an application was working last week but is broken
today, git bisect can pinpoint which commit caused the issue.

git bisect is much faster and more efficient than manually reviewing commits,
especially when a project has many commits. Instead of checking each commit one by
one, git bisect uses a binary search approach to identify the problematic commit
with only a few tests. How ever, manual review can still provide context that
automated tools cannot.

# Git Commands and when to use them

## What does each command do?

```
git checkout main -- <file>
```

This command restores a specific file to the version that exists on the main
branch. It is useful because it only affects the selected file instead of the
entire branch.

```
git cherry-pick <commit>
```

git cherry-pick applies a specific commit from another branch to the current
branch without merging the entire branch. During testing, I created a commit on
one branch and successfully applied it to another.

```
git log
```

git log displays the commit history of a repository. It shows information such as
commit IDs, authors, dates, and commit messages. I used it to view the changes I
had made over time.

```
git blame <file>
```

git blame shows which commit and author last modified each line of a file. It can
help trace a particular line of code back to the change that introduced or
modified it.

## When would you use these commands in a real project?

I would use these commands regularly when working on a large project with
multiple developers. git checkout main -- <file> would be useful when I
accidentally make unwanted changes to one file. git cherry-pick is useful when
I want to apply specific commits from one branch to another without doing a full
merge.

## What surprised you while testing these commands?

What surprised me was how specific Git can be when working with changes. I
initially expected git checkout main -- <file> to affect the whole branch, but
it only restored the selected file. I also found git blame to be incredibly
useful for understanding the history of specific lines of code.

# Git Branching

## Why is pushing directly to main problematic?

Pushing directly to main is problematic, or untested code into the main version
of the project. If multiple developers push changes directly to main, it can also
become difficult to track who made what changes.

## How do branches help with reviewing code?

Branches allow developers to work on features, bug fixes, or other changes
without affecting main. Once the work is ready, the branch can be submitted as a
pull request so other team members can review it.

## What happens if two people edit the same file on different branches?

If two people edit different parts of the same file on separate branches, Git
can oftern merge the changes automatically. However, if they modify the same
lines or nearby sections, Git may produce a merge conflict.

![branches create](<Screenshot 2026-08-18 162715.png>)
![minor changes and commit to new branch](<Screenshot 2026-08-18 163117.png>)

The branch I created was test-rip and the change done is adding a line in the
README.

![In the branch](<Screenshot 2026-08-18 171827.png>)
![In main](<Screenshot 2026-08-18 172045.png>)

We can see in main that the README has no change, and is different from the
branch.

# Staging vs Committing

## What is the difference between staging and commiting?

Imagine you are doing online shopping. You browse through the pages of your
favorite online store and add some products to your shopping cart. The shopping
cart is like staging. Things are there, but you can easily change your mind and
remove items or adjust quantities.

When you are done shopping, you go and pay for the things in your shopping cart.
That can be compared to making a commit. Now there is a record somewhere of what
you have bought and when. There is no going back to undo the payment.

Notice that you can easily change your mind when things are in staging, but once
you have made a commit things are hard to change (although not impossible!).

## Why does Git separate these two steps?

The fact that you need to stage and commit separately in Git has many benefits.
Generally it is a good idea to make your commits small. And to make sure that
all the changes that are included in each commit are logically related.

For examplet a commit that fixes a bug in Feature A should not also include an
improvement you made to Feature B. If you have done two things at once, you
should not commit everything at once.

That way, if someone looks at the Git history of your project, they are not
confused by one giant commit that did a lot of things. They can see clearly that
you performed two different tasks: first one, then the other.

## When would you want to stage changes without committing?

You would want to stage changes without committing when you want to prepare
specific changes for the next commit but are not ready to permanently record them
in the Git history.

For example, you might stage your completed changes, review them with git diff
--cached, and make sure everything is correct before committing. It can also be
useful when you have multiple changes but only want to commit some of them.

![staging vs committing](<Screenshot 2026-08-18 164547.png>)
