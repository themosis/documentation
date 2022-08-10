Contributing
============

- [Introduction](#introduction)
- [Reporting issues](#reporting-issues)
- [New features](#new-features)
- [Which branch?](#which-branch)
- [Security vulnerabilities](#security-vulnerabilities)

Introduction
------------

Themosis is an open-source project and anyone can contribute to it. Themosis contains multiple repositories and they're all stored on Github.

If you feel confident writing code, we have 4 repositories handling the different part of the framework:

- [themosis/themosis](https://github.com/themosis/themosis): which provides the full code structure for a collaborative development installing the latest WordPress, framework and theme versions.
- [themosis/framework](https://github.com/themosis/framework): which is the framework core containing all the APIs.
- [themosis/theme](https://github.com/themosis/theme): which provides a boilerplate code structure for developing your website/application theme.
- [themosis/plugin](https://github.com/themosis/plugin): which provides a boilerplate code structure for developing custom plugins for your application.

For each of these repositories, you can accordingly report any issues you may find or provide pull requests.

> Make sure to double check before posting a code issue. Generally, code issues should be reported on the `themosis/framework` repository.

We also need help on the documentation. If you see grammar typos or think the docs are lacking some explanation or examples, feel free to complete it:

- [themosis/documentation](https://github.com/themosis/documentation)

Reporting issues
----------------

Regarding `code` issues, please **submit your code with the request** and give **detailed explanations** about what you're trying to achieve.

If your code is in multiple files or is more complex, **setup your own repository** so people can look at it.

We highly encourage you to send pull requests with the bug fix. Make sure to work on the latest stable release and **not** the `master` branch. The framework version `2.0.*` has a `2.0` branch from which you have to write your pull request for. A pull request **must have unit tests** along your code.

If you're unsure how to retrieve the latest (tagged) stable release, check these resources:

- [Stackoverflow](http://stackoverflow.com/questions/5582208/checkout-git-tag?answertab=votes#tab-top)
- [Git](http://git-scm.com/docs/git-checkout)

New features
------------

You can make feature proposal from the Github issues.

> Please add the `feature` tag to your request or we won't look at it.

If your proposal is a good fit, you can submit a pull request with the new functionality and its unit tests.

**Minor** features or enhancements should be sent to the latest stable branch.

**Major** features should always be sent to the `master` branch which contains the code for the next release.

Which branch?
-------------

**All** bug fixes should be sent to the latest stable **branch**. Bug fixes should never be sent to the master branch unless they fix features that exist only in the upcoming release.

**Minor** features that are fully backwards compatible with the current Themosis release may be sent to the latest stable branch.

**Major** new features should always be sent to the `master` branch, which contains the upcoming release.

Security vulnerabilities
------------------------

If you discover a security vulnerability within Themosis, please send an e-mail at <support@themosis.com>. All security vulnerabilities will be promptly addressed.