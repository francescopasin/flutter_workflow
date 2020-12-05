# Flutter CI/CD workflow
![ci](https://github.com/francescopasin/flutter_workflow/workflows/Flutter%20CI/badge.svg)  

This repository describes a CI and CD workflow for Flutter mobile apps.
[Github Actions](https://github.com/features/actions) and [Fastlane](https://fastlane.tools) are used for CI/CD.

## What's included
- :compass: [Versioning workflow guidelines](#versioning-workflow-guidelines)
- :heavy_check_mark: [Continuous Integration with Github actions](#continuous-integration)
- :rocket: [Continuous Deployment with Github actions and Fastlane](#continuous-deployment)

## How to use this
This repository can be used as a template for your Flutter projects. Just download the repository code and create your own new repo.
If you want to start from scratch you can follow the step by step guide.
- [Quick setup](#quick-setup)
- [Step by step setup](#step-by-step-setup)

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
This repository contains a [workflow](.github/workflows/cd.yml) for github actions that manage continuos deployment on specific tags.
### Create a new release
When you are ready to create a new release you can simple create a new tag on the main branch. The tag should be in the format ```v<version>``` where ```<version>``` is the version of your app (like 1.0.0. The use of [semantic versioning](https://semver.org/) for your app is strongly recommended.  
If you need to create Android or Ios only releases you can prefix your tag with ```android-``` or ```ios-``` respectively.  
Every time you add a release tag the app is deployed to the stores and a new github release is created.

## Quick setup
The following guide let you have a ready-to-start project with almost all the configurations and CI/CD actions already done. The configuration process may seem a bit long at the beginning but it will reduce your release time a lot.
### Repository setup
1. Copy this repository (clone it or download it)
1. Create your remote repository on github and connect it to the local repo you just copied (and push the code to the remote)
1. Configure repository's settings:
   - Turn on "Automatically delete head branches" under the "Merge button" section in the "General" tab (optional)
   - Add your team members in the "Manage access" tab (optional)
   - Setup main branch protection: in the "Branches" tab add a new branch protection rule and name it as you want (I simply suggest "main"), then set the following settings:
     - "Require pull request reviews before merging". This ensures that a pull request is approved by a minimum amount of people (set in the "Required approving reviews") before it can be merged). You can also set the "Require review from Code Owners" if you want the pull requests to be always approved by the code owners.
     - "Require status checks to pass before merging" (and check all the status check in the section below). Every time you make a pull requests the CI workflow starts and with this options you "lock" the possibility to merge unless all the tests have passed. If you want to reduce merge conflicts you should also set "Require branches to be up to date before merging".
     - "Include administrators". It should be checked in order to prevent the amministrators to merge a request even if it doesn't pass the tests.

Read [Versioning workflow guidelines](#versioning-workflow-guidelines) for more info on how to organize your repository.
### Android setup
1. Choose your package name. Choose your unique package name (or applicationId) and set it in the following files:
   - ```android/app/build.gradle``` in the ```defaultConfig``` section after the ```applicationId``` key
   - ```android/fastlane/Appfile``` inside ```package_name```
1. Create your android signing key:
   1. Create a keystore https://flutter.dev/docs/deployment/android#create-a-keystore (remember the password you set because is needed later)
   1. Move the file you just created in the ```android``` folder
   1. Create a file named ```key.properties``` in the ```android``` folder
   1. Inside the ```key.properties``` file write the following:  
      ```properties
      storePassword=<PASSWORD_YOU_SET_IN_STEP_1>
      keyPassword=<PASSWORD_YOU_SET_IN_STEP_1>
      keyAlias=key
      storeFile=../key.jks```
1. Create Google service account (this step requires you to already have set up the app on the play console):
   1. Follow the steps on http://docs.fastlane.tools/getting-started/android/setup/#collect-your-google-credentials to download your service account key
   1. Move the .json file you just downloaded in the ```android``` folder and rename it ```service_account_key.json```
1. Encrypt android keys (for security reasons):
   1. Create a zip (named ```android_keys.zip```) with the 3 files you just created:  
   ```zip android_keys.zip key.jks key.properties service_account_key.json```
   1. Ecrypt the zip archive using [GPG](https://gnupg.org/) (remember the password you set because is needed later):   
   ```gpg --symmetric --cipher-algo AES256 android_keys.zip```  
  **Attention: you should NEVER upload your signing keys and service account keys. The only file that can be uploaded is the encrypted archive.**
1. The password you used to encrypt the keys needs to be set in the Github secrets, in order to be used during the CD workflow.  
Go to the Github settings page, in the "Secrets" tab and create a new secret named ```ANDROID_KEYS_SECRET_PASSPHRASE```. Set as value the password you created in the step before.

**IMPORTANT**  
You have to manually create a first build and upload it on the store or Fastlane won't work.

### Apple setup
**TODO**

### Automatic store metadata
The CD tools will upload all the metadata (description, screenshot...) to the stores.
All the Play Console metadata are inside the ```android/fastlane/metadata``` folder, divided into folders for each language. Every time your app is automatically deployed also the metadata are deployed. You can grab the metadata from your play console by running ```fastlane supply init``` in your ```android``` folder (see https://docs.fastlane.tools/getting-started/android/setup/#fetch-your-app-metadata).
If you don't want to use this functionality you can disable it by adding the following lines to the ```upload_to_play_store``` function in the ```android/fastlans/Fastfile``` file:
```
skip_upload_metadata: true,
skip_upload_images: true,
skip_upload_screenshots: true
```

### Develop! 
Now you are ready to start developing your app. The automatic CI and CD actions will improve your workflow and ensure the stability of your code. For more info read the three section in [What's included](#what's-included)

## Step by step setup
The following guide let you create and configure everything from scratch. If you want to reduce your boilerplate see the [Quick setup](#quick-setup) section.

### Github actions
TODO
### Github settings
TODO
#### Project collaborators
TODO
#### Automatic branch deletion
TODO
#### Setup branch protection
TODO
### Fastlane configuration
TODO
### Generate Google service account
TODO
### Configure app signing
TODO
### Encrypt sensitive files
TODO
### Github secrets
TODO
### Gitignore
TODO
### Gradle config
TODO
### First build and play console configuration
TODO
### Fastlane supply and metadata
TODO

## Credits
Andrea Valenzano for the tags parsing function : https://gist.github.com/andrea689/307b5c3e37c117bb0acad8798bf84baa#file-flutter_publish_release-yml-L1

Joshua de Guzman for the fastlane configuration and workflows inspiration: https://blog.joshuadeguzman.net/continuous-delivery-for-your-flutter-using-fastlane-github-actions-android



