#summary How to contribute source code to Ostinato

If you would like to contribute source code changes (fixes, new features etc.) to Ostinato, you can send a patch or provide a url for a server side clone.

# Submit a patch #
  * Use only for small changes, for larger submissions use a [Server-side clone](#Server_Side_Clone.md) (aka fork)
  * Use your judgement to classify 'small' or 'large'
  * Patch should be generated using `hg diff -gp`
  * Patch should be sent to the Ostinato mailing list

# Server Side Clone #
  * Use for all submissions unless its a small fix in which case you can [submit a patch](#Submit_a_patch.md)
  * Go to the [Source Checkout Page](http://code.google.com/p/ostinato/source/checkout) and click on the "Create a clone" button. This will create a server side clone of the repository (see [Why should I create a server-side clone](https://code.google.com/p/support/wiki/MercurialFAQ#Why_should_I_create_a_server-side_clone?))
  * Use `hg clone <server-side-clone-repo>` on your development machine to pull a copy of the clone
  * Develop your code in this workspace. If you already have changes in another workspace, patch your changes into this workspace
  * Build and Test
  * `hg commit` (ideally you should have multiple commits where each commit has a number of related changes - makes it easier to review)
  * `hg push` your changes to the server
  * Go to the [issue tracker](http://code.google.com/p/ostinato/issues/list) and create a new issue of type "Review Request". Include the url of your clone and the changesets that you want to be reviewed. ([code review tips](https://code.google.com/p/support/wiki/CodeReviews))

**If you intend (or see a good chance) to contribute source code more often than just a one-off thing, using a server side clone for your regular development is highly recommended as it encourages an open development culture.**

# Contribution Licensing #
Ostinato follows a **"_inbound = outbound_"** licensing policy with respective to all code contributions to the project.

What this means is that any code contribution by a contributor to the project (the _inbound_) via any of the above means is **automatically assumed** to be licensed for use by the project under the **same license as the project's license** (the _outbound_) as on the date of the submission **just by virtue of the act of submitting** the contribution.

Currently the outbound license used by the project is **GPLv3 or later**.

**All contributed code remains the copyright of the author/contributor**.