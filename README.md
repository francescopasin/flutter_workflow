# flutter_workflow
This repository describes a CI and CD workflow for Flutter mobile apps.
[Github Actions](https://github.com/features/actions) and [Fastlane](https://fastlane.tools) are used for CI/CD.

## What's included
- :compass: [Versioning workflow guidelines](#versioning-workflow-guidelines)
- :heavy_check_mark: [Continuous Integration with Github actions](#continuous-integration)
- :rocket: [Continuous Deployment with Github actions and Fastlane](#continuous-deployment)

## How to use this
This repository can be used as a template for your Flutter projects.

### Github actions
Copy all the files inside the [.github/workflows](.github/workflows) directory and insert them in the same directory in your repository.
### Github settings
In your repository go to the "Settings" page and set the following settings.
#### Project collaborators
You can add your team members to your repo by adding their Github accounts in the "Manage access" tab.
#### Automatic branch deletion
If you want your branches to be automatically deleted after a successfully merge you can set this in the "General" tab under the "Merge button" section ("Automatically delete head branches").
#### Setup branch protection
Your main branch should be protected, in order to allow commits only with pull requests.  
Go to the "Branches" tab and add a new branch protection rule. Name it as you want (I simply suggest "main") and set the following settings:
- "Require pull request reviews before merging". This ensures that a pull request is approved by a minimum amount of people (set in the "Required approving reviews") before it can be merged). You can also set the "Require review from Code Owners" if you want the pull requests to be always approved by the code owners.
- "Require status checks to pass before merging" (and check all the status check in the section below). Every time you make a pull requests the CI workflow starts and with this options you "lock" the possibility to merge unless all the tests have passed. If you want to reduce merge conflicts you should also set "Require branches to be up to date before merging".
- "Include administrators". It should be checked in order to prevent the amministrators to merge a request even if it doesn't pass the tests.
### Develop
Now you are ready to get started! Clone your repo locally and create your new Flutter project. For more information see the sections below.

## Versioning workflow guidelines
This section suggests how you should manage your branches, pull requests, reviews and releases.  

There are differents workflows with differents branches management and the choice should depend on your specific requirements, your team and your project.  
Here the [Github flow](https://guides.github.com/introduction/flow) is suggested for its simplicity and popularity.

### Development
Every time you want to create a new feature, fix something or do any change to the code, you should create a new branch, with a name that describes what you are making (eg. "feature/about-page"). You can prefix the name with "feature/", "bugfix/" or similar to better indicate what you are doing.  
When your feature is ready and stable you can open a pull request, in order to merge the new features in the main branch. Then your code can be commented, reviewed, fixed or modified (if necessary) and finally merged.  
**The main branch should be protected and you should never directly push to it.**

A pull request, in order to be merged, has to pass all the CI tests and be approved by a minimum amount of reviewers (you can choose some "code reviewers" that must approve all the pull requests. See [Setup branch protection](#setup-branch-protection)). Every branch should be up to date with the main branch before merging, in order to prevent conflicts.  
When a branch is merged it can be deleted ([automatically](#automatic-branch-deletion) or manually).

**The main branch should always be deployable** and for this reason all the pull requests need to pass all the tests and be stable.  
When you are ready to release your app you can [create a new release](#create-a-new-release)

## Continuous Integration
This repository contains a [workflow](.github/workflows/ci.yml) for github actions that manage continuos integration on each pull request.  
If the tests fails, the pull request shouldn't be merged with the main branch.
Continuous integration helps reducing the bugs in your code: every time you introduce a new feature all the previous tests are runned in order to see if the new code brokes something. The addition of required reviews from other people ensures that the main branch remains always stable and deployable.

### Features
- Code formatting check (```flutter format```)
- Static dart analisis (```flutter analyze```)
- Tests (```flutter test```)
### Flutter version
You can change the Flutter version and channel in the [workflow](.github/workflows/ci.yml) file, by changing the value of "flutter-version" and "channel" inputs in the flutter-action (more infos on the [flutter-action page](https://github.com/marketplace/actions/flutter-action)).

## Continuous Deployment
### Create a new release
**TODO**
