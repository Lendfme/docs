## Contributing to Lendf.Me

- [Code of Conduct](#codeOfConduct)
- [Issues](#issues)
- [Discussions And General Help](#discussions)
- [Pull Requests](#pullRequest)
- [Step 1: Fork](#fork)
- [Step 2: Branch](#branch)
- [Step 3: Code](#code)
- [Step 4: Commit](#commit)
- [Step 5: Rebase](#rebase)
- [Step 6: PRs](#pr)


Contributions to Lendf.Me could come in different forms. Some contribute code changes, others contribute docs, others help answer questions from users, help keep the infrastructure running,

We welcome all contributions from folks who are willing to work in good faith with the community. No contribution is too small and all contributions are valued.

<h3 id='codeOfConduct'> Code of Conduct </h3>

The Code of Conduct is designed and intended, above all else, to help establish a culture within the project that allows anyone and everyone who wants to contribute to feel safe doing so.

Open, diverse, and inclusive communities live and die on the basis of trust. Contributors can disagree with one another so long as they are done in good faith and everyone is working towards a common goal.

<h3 id='issues'> Issues </h3>

Issues in Lendfme are the primary means by which bug reports and general discussions are made. A contributor is allowed to create an issue, discuss, and provide a fix if needed.

Before opening an issue, check to see if there are any current issues with similar key words. This helps us cut down on duplicate tickets.

When you open an issue, you'll notice four templates: bug, custom, discussion, feature. When starting a new issue, please do your best to be as detailed and specific as possible.

1. **Bug report** - use this to create a bug report to help us improve Lendfme

2. **Discussion** - use this template to start a discussion

3. **Feature request** - use this to suggest a project idea

4. **Custom report** - use this to report an issue that doesn't fall under any other category

<h3 id='discussions'> Discussions And General Help </h3>

As Lendfme is still at its early stages, drop by [our discord channel](https://discordapp.com/invite/Gbtd3MR) and say hi to know what's next / to get your answers cleared up.

<h3 id='pullRequest'> Pull Requests </h3>

Pull Requests are the way in which concrete changes are made to the code and documentation.

### Prerequisites

You must have a valid installation of `node/npm` to run the code, and `eslint` (`npm install -g eslint`) to find and fix writing style problems in our code.

At here, we use the repo `oraclesystem` as an example:

<h3 id='fork'> Step 1: Fork </h3>

Fork the project [oraclesystem](https://github.com/Lendfme/oraclesystem.git) on GitHub and clone your fork locally.

```
git clone git@github.com:username/oraclesystem.git
cd oraclesystem
git remote add upstream https://github.com/Lendfme/oraclesystem.git
git fetch upstream
```

<h3 id='branch'> Step 2: Branch </h3>

It's always better to create local branches to work on a specific issue. Makes life easier for you if you are the kind who enjoys multiple things parallely. These should also be created directly off of the master branch.

```
git checkout -b my-branch -t upstream/master
```

<h3 id='code'> Step 3: Code </h3>

To keep the style of the Javascript code consistent we have a basic linting configured. To check your contributed code for errors run `npm run lint`. To make life easy use the automatic fixing by running `npm run lint:fix` before your commit.

- Use the pre-configured eslint for Javascript
- Avoid trailing whitespace & un-necessary white lines
- Indentation is as follows:
    - 1 tab = 2 spaces for `.js` files
    - 1 tab = 4 spaces for everything else

<h3 id='commit'> Step 4: Commit </h3>

1. Ensure your code changes adhere to our styling and linting standards: `npm run lint:fix`
2. List all your changes as a list if needed else simply give a brief description on what the changes are.
3. All lines at 100 columns.
4. If your PR fixed an issue, Use the `Fixes:` prefix and the full issue URL. For other references use `Refs:`.

  *Examples:*

  - `Fixes: https://github.com/Lendfme/oraclesystem/issues/10`
  - `Refs: https://github.com/Lendfme/oraclesystem/issues/23`

5. Sample commit

 ```
 - if you can write down the changes explaining it in a paragraph which each line wrapped within 100 lines.
 - list out your changes as points if there are many changes,
 - if needed you can also send it across as all wrapped within 100 lines

 Fixes: https://github.com/Lendfme/oraclesystem/issues/10
 Refs: https://github.com/Lendfme/oraclesystem/issues/23
 ```

6. [Squashing](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History) and [Merging](https://git-scm.com/docs/git-merge) your commits to make our history neater is always welcomed, but squashing can be handled during the merge process.

<h3 id='rebase'> Step 5: Rebase </h3>

Ensure you neat description on what your PR is for, so that it's easier for folks to understand the gist of it before jumping to the the code / doc.

As a best practice, once you have committed your changes, it is a good idea to use `git rebase` (not `git merge`) to ensure your changes are placed at the top. Plus merge conflicts can be resolved

```
git fetch upstream
git rebase upstream/master
```

<h3 id='pr'> Step 6: PRs </h3>

Please ensure that your pull request follows all of the community guidelines to include:

- Title is descriptive and generally focused on what the PR addresses (If your PR is a work in progress, include WIP in the title. Once the PR is ready for review, please remove WIP)
- Description explains what the PR achieves or addresses
- The PR passes all CI checks, to include: Travis CI.
- If tests are failing or coverage is decreased while adding logic to any backend code, you will be asked to include relevant tests and your PR will not be merged until all checks pass.
