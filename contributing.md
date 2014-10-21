Contributing
============

1. Introduction
2. Bugs
3. New features
4. Which branch?
5. Security vulnerabilities

1.Introduction
--------------

Themosis is an open-source project and anyone can contribute to it. Themosis  contains multiple repositories and they're all stored on Github.

If you feel confident writing code, we have 3 repositories handling the different part of the framework:

- [themosis/themosis](https://github.com/themosis/themosis): which provides the full code structure for a collaborative development installing the latest WordPress, framework plugin and theme versions.
- [themosis/framework](https://github.com/themosis/framework): which is the framework plugin containing all the core APIs.
- [themosis/theme](https://github.com/themosis/theme): which provides a dedicated code structure for developing your website/application theme.

For each of these repositories, you can accordingly assign `bug` and `feature` requests.

> Make sure to apply those tags in the Github issues.

We also need help on the documentation. If you see grammar typos or think the docs are lacking of explanation or examples, feel free to complete it:

- [themosis/documentation](https://github.com/themosis/documentation)

2.Bugs
------

Regarding `code` issues, please **add your code to the request** and give detailed explanations about what you're trying to achieve.

If you're code is in multiple files or is more complex, **setup your own repository** so people can look at it.

We highly encourage you to send pull requests with the bug fix. Make sure to work on the latest stable release and **not** the `master` branch.

If you're unsure how to retrieve the latest (tagged) stable release, check these resources:

- [Stackoverflow](http://stackoverflow.com/questions/5582208/checkout-git-tag?answertab=votes#tab-top)
- [Git](http://git-scm.com/docs/git-checkout)

3.New features
--------------

You can make feature proposal from the Github issues.

> Please add the `feature` tag to your request or we won't look at it.

If your proposal is a good fit, you can submit a pull request with the new functionality and its unit tests.

**Minor** features or enhancements should be sent to the latest stable branch.

**Major** features should always be sent to the `master` branch which contains the code for the next release.

4.Which branch?
---------------

**All** bug fixes should be sent to the latest stable **branch**. Bug fixes should never be sent to the master branch unless they fix features that exist only in the upcoming release.

**Minor** features that are fully backwards compatible with the current Themosis release may be sent to the latest stable branch.

**Major** new features should always be sent to the `master` branch, which contains the upcoming release.

5.Security vulnerabilities
--------------------------

If you discover a security vulnerability within Themosis, please send an e-mail at <support@themosis.com>. All security vulnerabilities will be promptly addressed.