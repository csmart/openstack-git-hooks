# Git hooks for OpenStack development

## pre-commit

This hook may be useful in helping you to run tox tests and scripts _before_
the local commit is made. This way we can save everyone time by fixing
simple issues before they break in the check-pipeline.

The hook also runs when fixing up a commit with `git commit --amend` (note
that git does not run pre-commit when doing an interactive rebase).

All prompts default to _no_, so you can easily just hit enter to skip and
continue (but still be reminded).

### Install

To install, clone this `openstack-git-hooks` git repository somewhere. For
example, into your home directory:

```bash
git clone git://github.com/csmart/openstack-git-hooks ~/openstack-git-hooks
```

Go into the `.git/hooks` directory of an existing OpenStack project that you
have already cloned somewhere. For example, perhaps `openstack-ansible` in your
home directory:

```bash
cd ~/openstack-ansible/.git/hooks
```

Create a symlink from the pre-commit file in the freshly cloned
`openstack-git-hooks` repo to pre-commit in your OpenStack project repo. For
example:

```bash
ln -s ~/openstack-git-hooks/pre-commit
cd ~/openstack-ansible
```

That's it!

You can repeat this for any other OpenStack repos you want and they will all
use the same pre-commit script. This way you can modify it and have the change
reflected across all of your projects.

Note that the pre-commit script in the `openstack-git-hooks` repo should
already be executable, but if not then you will need to make it so. For
example:

```bash
chmod u+x ~/openstack-git-hooks/pre-commit
```

### Usage

Just run git commit as you normally would, for example:

```bash
git commit -a
```

### Prompts

The pre-commit hook detects whether there are actually any staged files to
commit (it ignores untracked files). If there are, it presents a simple prompt
to ask whether you want to run tests or scripts at all. If you say _no_ (or
just hit enter) then the script will exit and skip all further prompts (this is
the default).

```bash
Run some tests or scripts? [y/N]: 
```

If you say _yes_ the script will then prompt for guides, tests and scripts.

Firstly, if it looks like you are working on manuals then it will prompt for a
comma separated list of any guides that you want to build (enter to skip).

```bash
Enter any guides to build, e.g.: contributor-guide, install-guide
	[]: 
```

Next the script checks the types of files to be committed and prompts if you
want to run specific tox tests (these are stored in a dictionary in the script
and easily modifiable to suit your needs).

If the test is not found in the tox.ini of this specific OpenStack project,
then it is skipped.

For example:

  * If Python (.py) files are detected, it prompts to run specific tests such
as pep8, py27 and py34.

```bash
Found py files in the commit, run tests:
	tox -e pep8? [y/N]: 
	Test py27 not found in tox.ini, skipping.
	Test py34 not found in tox.ini, skipping.
```

  * If ReStructuredText (.rst) files are detected, it prompts to run the docs
test.

```bash
Found rst files in the commit, run tests:
	tox -e docs? [y/N]: 
```

  * If YAML (.yml) files are detected, it prompts to run the linters test.

```bash
Found yml files in the commit, run tests:
	tox -e linters? [y/N]: 
```

Next, it prompts you for a comma separated list of any other tox tests that
you want to run (enter skips).

```bash
Enter any additional tox tests e.g.: functional, releasenotes
	[]: 
```

Finally, it prompts you for a comma separated list of any scripts that you want
to run.

```bash
Enter any scripts e.g.: run_tests.sh, ~/my_test.sh
	[]: 
```

If any of the guides, tests or scripts fail, then the commit is aborted.

### Skipping tests

All prompts and tests are skipped if the `SKIP_TESTS` variable is set to
something. It can be anything, like _yes_. For example:

```bash
SKIP_TESTS=yes git commit -a
```

Or use the standard git way, for example:

```bash
git commit --no-verify -a
```

### Debug

Debugging (`set -x`) is enabled if the `DEBUG` variable is set to something. It
can be anything, like _yes_. For example:

```bash
DEBUG=yes git commit -a
```

## Feedback
Please send me your feedback, I'm keen to know if this is helpful and how it
can be improved. Thanks!
