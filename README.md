git-stash(1) Manual Page
NAME
git-stash - Stash the changes in a dirty working directory away

SYNOPSIS
git stash list [<log-options>]
git stash show [-u | --include-untracked | --only-untracked] [<diff-options>] [<stash>]
git stash drop [-q | --quiet] [<stash>]
git stash pop [--index] [-q | --quiet] [<stash>]
git stash apply [--index] [-q | --quiet] [<stash>]
git stash branch <branchname> [<stash>]
git stash [push [-p | --patch] [-S | --staged] [-k | --[no-]keep-index] [-q | --quiet]
             [-u | --include-untracked] [-a | --all] [(-m | --message) <message>]
             [--pathspec-from-file=<file> [--pathspec-file-nul]]
             [--] [<pathspec>…​]]
git stash save [-p | --patch] [-S | --staged] [-k | --[no-]keep-index] [-q | --quiet]
             [-u | --include-untracked] [-a | --all] [<message>]
git stash clear
git stash create [<message>]
git stash store [(-m | --message) <message>] [-q | --quiet] <commit>
DESCRIPTION
Use git stash when you want to record the current state of the working directory and the index, but want to go back to a clean working directory. The command saves your local modifications away and reverts the working directory to match the HEAD commit.

The modifications stashed away by this command can be listed with git stash list, inspected with git stash show, and restored (potentially on top of a different commit) with git stash apply. Calling git stash without any arguments is equivalent to git stash push. A stash is by default listed as "WIP on branchname …​", but you can give a more descriptive message on the command line when you create one.

The latest stash you created is stored in refs/stash; older stashes are found in the reflog of this reference and can be named using the usual reflog syntax (e.g. stash@{0} is the most recently created stash, stash@{1} is the one before it, stash@{2.hours.ago} is also possible). Stashes may also be referenced by specifying just the stash index (e.g. the integer n is equivalent to stash@{n}).

COMMANDS
push [-p|--patch] [-S|--staged] [-k|--[no-]keep-index] [-u|--include-untracked] [-a|--all] [-q|--quiet] [(-m|--message) <message>] [--pathspec-from-file=<file> [--pathspec-file-nul]] [--] [<pathspec>…​]
Save your local modifications to a new stash entry and roll them back to HEAD (in the working tree and in the index). The <message> part is optional and gives the description along with the stashed state.

For quickly making a snapshot, you can omit "push". In this mode, non-option arguments are not allowed to prevent a misspelled subcommand from making an unwanted stash entry. The two exceptions to this are stash -p which acts as alias for stash push -p and pathspec elements, which are allowed after a double hyphen -- for disambiguation.

save [-p|--patch] [-S|--staged] [-k|--[no-]keep-index] [-u|--include-untracked] [-a|--all] [-q|--quiet] [<message>]
This option is deprecated in favour of git stash push. It differs from "stash push" in that it cannot take pathspec. Instead, all non-option arguments are concatenated to form the stash message.

list [<log-options>]
List the stash entries that you currently have. Each stash entry is listed with its name (e.g. stash@{0} is the latest entry, stash@{1} is the one before, etc.), the name of the branch that was current when the entry was made, and a short description of the commit the entry was based on.

stash@{0}: WIP on submit: 6ebd0e2... Update git-stash documentation
stash@{1}: On master: 9cc0589... Add git-stash
The command takes options applicable to the git log command to control what is shown and how. See git-log(1).

show [-u|--include-untracked|--only-untracked] [<diff-options>] [<stash>]
Show the changes recorded in the stash entry as a diff between the stashed contents and the commit back when the stash entry was first created. By default, the command shows the diffstat, but it will accept any format known to git diff (e.g., git stash show -p stash@{1} to view the second most recent entry in patch form). If no <diff-option> is provided, the default behavior will be given by the stash.showStat, and stash.showPatch config variables. You can also use stash.showIncludeUntracked to set whether --include-untracked is enabled by default.

pop [--index] [-q|--quiet] [<stash>]
Remove a single stashed state from the stash list and apply it on top of the current working tree state, i.e., do the inverse operation of git stash push. The working directory must match the index.

Applying the state can fail with conflicts; in this case, it is not removed from the stash list. You need to resolve the conflicts by hand and call git stash drop manually afterwards.

apply [--index] [-q|--quiet] [<stash>]
Like pop, but do not remove the state from the stash list. Unlike pop, <stash> may be any commit that looks like a commit created by stash push or stash create.

branch <branchname> [<stash>]
Creates and checks out a new branch named <branchname> starting from the commit at which the <stash> was originally created, applies the changes recorded in <stash> to the new working tree and index. If that succeeds, and <stash> is a reference of the form stash@{<revision>}, it then drops the <stash>.

This is useful if the branch on which you ran git stash push has changed enough that git stash apply fails due to conflicts. Since the stash entry is applied on top of the commit that was HEAD at the time git stash was run, it restores the originally stashed state with no conflicts.

clear
Remove all the stash entries. Note that those entries will then be subject to pruning, and may be impossible to recover (see Examples below for a possible strategy).

drop [-q|--quiet] [<stash>]
Remove a single stash entry from the list of stash entries.

create
Create a stash entry (which is a regular commit object) and return its object name, without storing it anywhere in the ref namespace. This is intended to be useful for scripts. It is probably not the command you want to use; see "push" above.

store
Store a given stash created via git stash create (which is a dangling merge commit) in the stash ref, updating the stash reflog. This is intended to be useful for scripts. It is probably not the command you want to use; see "push" above.

OPTIONS
-a
--all
This option is only valid for push and save commands.

All ignored and untracked files are also stashed and then cleaned up with git clean.

-u
--include-untracked
--no-include-untracked
When used with the push and save commands, all untracked files are also stashed and then cleaned up with git clean.

When used with the show command, show the untracked files in the stash entry as part of the diff.

--only-untracked
This option is only valid for the show command.

Show only the untracked files in the stash entry as part of the diff.

--index
This option is only valid for pop and apply commands.

Tries to reinstate not only the working tree’s changes, but also the index’s ones. However, this can fail, when you have conflicts (which are stored in the index, where you therefore can no longer apply the changes as they were originally).

-k
--keep-index
--no-keep-index
This option is only valid for push and save commands.

All changes already added to the index are left intact.

-p
--patch
This option is only valid for push and save commands.

Interactively select hunks from the diff between HEAD and the working tree to be stashed. The stash entry is constructed such that its index state is the same as the index state of your repository, and its worktree contains only the changes you selected interactively. The selected changes are then rolled back from your worktree. See the “Interactive Mode” section of git-add(1) to learn how to operate the --patch mode.

The --patch option implies --keep-index. You can use --no-keep-index to override this.

-S
--staged
This option is only valid for push and save commands.

Stash only the changes that are currently staged. This is similar to basic git commit except the state is committed to the stash instead of current branch.

The --patch option has priority over this one.

--pathspec-from-file=<file>
This option is only valid for push command.

Pathspec is passed in <file> instead of commandline args. If <file> is exactly - then standard input is used. Pathspec elements are separated by LF or CR/LF. Pathspec elements can be quoted as explained for the configuration variable core.quotePath (see git-config(1)). See also --pathspec-file-nul and global --literal-pathspecs.

--pathspec-file-nul
This option is only valid for push command.

Only meaningful with --pathspec-from-file. Pathspec elements are separated with NUL character and all other characters are taken literally (including newlines and quotes).

-q
--quiet
This option is only valid for apply, drop, pop, push, save, store commands.

Quiet, suppress feedback messages.

--
This option is only valid for push command.

Separates pathspec from options for disambiguation purposes.

<pathspec>…​
This option is only valid for push command.

The new stash entry records the modified states only for the files that match the pathspec. The index entries and working tree files are then rolled back to the state in HEAD only for these files, too, leaving files that do not match the pathspec intact.

For more details, see the pathspec entry in gitglossary(7).

<stash>
This option is only valid for apply, branch, drop, pop, show commands.

A reference of the form stash@{<revision>}. When no <stash> is given, the latest stash is assumed (that is, stash@{0}).
