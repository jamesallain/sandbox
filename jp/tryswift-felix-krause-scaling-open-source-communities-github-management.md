Felix discusses the different stages of open source projects, how to handle PRs and support change when projects scale, and how to keep innovating with a bigger user base. He goes into detail on how developers can solve those problems, in particular automating workflows, staying in closer contact with contributors and improving your product and documentation.

* * *

### Introduction (0:00)

I'm Felix Krause - [@KrauseFx](https://twitter.com/KrauseFx) at Twitter - and I'm the creator of [fastlane](https://fastlane.tools), an open source tool that allows you to publish apps to the App Store very easily. I was lucky enough to grow a small side project to a tool that is now part of Google, with over 12,000 commits and 13,000 stars on GitHub.

Here, I want to share what I learned when scaling the project from zero to tens of thousands of active users.

### Four Stages of Open Source Projects (1:44)

In general, you can group open source projects into four different stages. 

1. You publish source code on GitHub without interaction with other developers - there are no issues, pull requests, and stars. Around 40% of all GitHub projects have no interaction at all.

2. Other developers start using your software. You might realize that people start submitting issues or pull request for your software.

3. Your software becomes the de-facto standard for the specific problem.

4. When your project is hyper scale. You get hundreds of notifications every day, people may start attacking your project on blogs, and there may be commercial versions built on top of it. For example, React Native by Facebook started directly as a hyper-scale project because it became successful immediately.

### Scaling Open Source Projects is Difficult (3:03)

#### Keeping the momentum

The bigger your project becomes, the harder it is to keep the momentum and innovation you had in the beginning. For example, you will have to consider hundreds of use cases and thousands of different production setups, so you cannot just remove an option or change a default value.

#### Handling support and receiving feature requests

Once you pass a few thousand active users, you will realize that you spend more time helping your users than writing code on the project.

Most GitHub issues are not actual issues, but are feature requests or questions. You have to decide how you want to receive feature requests for your projects. You can either have people submit GitHub issues, where you label these accordingly, or you can have a separate website where people can submit ideas and features. You can also elect to not accept any new features at all, thereby keeping it feature frozen.

All features have to align with your project vision, so you should have a way to decline a specific feature or pull request that doesn't align with it. For *fastlane*, we can decline by saying that it doesn't fit our vision, then close the issue.

#### Reviewing external pull requests

Very similarly on features, you also have to decide what pull requests get merged into your codebase. If the feature breaks sometime in the future, you will be the one who has to fix it, and fixing it will take as much or more time than reviewing the code. As such, it's important to review the external pull requests carefully.

#### Maintainers stop being users

When project maintainers stop being a user of the product, it becomes a potential issue. This happened with *fastlane* - the software was started because the project-maintainer needed it for themselves. But after we started working on it full-time, we were no longer strict users of the product.

Today with *fastlane*, we use it to deploy *fastlane*, so everybody working on it also uses the product. If this is not possible for your product, make sure to onboard yourself by setting up the project, just to get an idea of how you should spend your time on your product.

#### Information imbalance

There is a big information imbalance between the project maintainers and the users. For you as a maintainer, an issue is just another task to be prioritized, but for the user, it may mean something else entirely, which may determine whether they use the product.


### How to Scale Your Open Source Project (8:09)

#### Improve your error messages

It's important to improve error messages. No matter how good your software actually is, you will always run into an unexpected situation.

With *fastlane*, you could see the complete stack trace and a lot of error information. We go as far as posting Stack Overflow answers in error messages, and even showing extra debug information, like the certificate ID.

#### Make it easy to find existing issues

Users usually search for an error message on GitHub or on Google, so as a project maintainer, you should make it as easy as possible to provide result.

[Orta](https://twitter.com/orta) built [gh_inspector](https://github.com/orta/gh_inspector), which a Ruby gem that would show you similar GitHub issues right in the user's terminal. Using gh_inspector, whenever fastlane runs into an unexpected situation, it will not only show all the similar GitHub issues, but also print a search URL on GitHub, allowing users to dive in even deeper.

#### Answers based on keywords

If you receive similar issues often based on keyboards, you should always attempt to fix the underlying issue, or improve the documentation. In the case of *fastlane*, many issues are related to code signing, but there is no ultimate solution to fix code signing if you're not Apple, so we wrote an extensive guide on how to set up code signing, and how to troubleshoot problems.

#### Stale issues and reasons for them

At *fastlane*, when an issue has no activity for over two months, the fastlane bot will ask if the issue is still relevant with the most recent fastlane release. If no one replies to the bot within a month, the issue gets auto-closed with a note that a new issue can be submitted for further discussion.

#### Lock resolved and inactive issues

Locking old and resolved issues is a great way to avoid resurfacing of GitHub issues from previous releases. For *fastlane*, we experienced that users would comment on issues that kind of sound similar, but they're actually not related at all. Sometimes people would comment on issues that have a lot of subscribers, triggering unwanted email notifications for people who already found a workaround.