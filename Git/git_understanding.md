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