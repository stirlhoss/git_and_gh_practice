# Using git rebase (altering commits)

## What is git rebase

Git rebase is a tool that allows you to go back in time to edit commits. It allows for a wide range of edits that I can't really wrap my head around, but I discovered some useful features anyway. I wanted to experiment with this feature a bit and do some documentation for practice. Here are my findings.

## The command to get started

In most cases, the command to rebase is passed with the argument -i, for interactive. It is possible to rebase without using interactive mode, but it seems like the easiest way. The command will look like this:

```
git rebase -i <after-this-commit>
```
It is important to note that you can only start rebasing after a commit. This means that you cannot use this method to alter an initial commit. There are 2 syntax's that can be used to select a starting point for rebasing.

1. `HEAD~N` where N is the number of commits you want to go back

2. By using the first seven digits of the SHA key that accompanies each commit

## Making your changes

After entering the rebase command, 2 things will happen.

1. Your terminal will show the message `hint: Waiting for your editor to close the file... `

2. Your text editor will have opened a document that looks like this:
    
![Screen Shot 2022-02-08 at 00 11 53](https://user-images.githubusercontent.com/96191917/152936124-0fe8c2f5-d9f8-4983-a177-25f911456a1b.png)

This document is fairly intuitive once you understand the process, but very confusing on first blush. The uncommented lines show you 3 pieces of information:
1. The command being used (pick is default)
2. The 7 digit SHA key
3. Your 1 line commit message

The commented text gives you a wide range of information about what you can do with these commits. You can delete, re-order and reword commit messages, but you can also alter the code in a commit or merge commits into each other. If you are just trying to fix a typo in your commit message, the only thing you will want to do in this sheet is change the command for the commit you want to alter.

Lets say I want to want to add more detail to my commit that says "Add destination". I change `pick` to `reword` and close the document.

![Screen Shot 2022-02-08 at 00 22 59](https://user-images.githubusercontent.com/96191917/152936057-acd5f6e2-fc0b-43ae-85dd-97ed7e873556.png)

A new document should appear that looks like this:

![Screen Shot 2022-02-08 at 00 24 45](https://user-images.githubusercontent.com/96191917/152936004-8a8a8689-7358-4885-a559-b2536490a714.png)

Follow the prompt and reword your commit message that appears on line 1. Then close the document.

Your terminal should show you a message like this:
```
[detached HEAD 17b0148] Add destination
 Date: Mon Feb 7 22:50:41 2022 -0600
 1 file changed, 7 insertions(+)
Successfully rebased and updated refs/heads/main.
```
and you will feel very accomplished that you saved yourself from a silly typo.

BUT WAIT!! THERE'S MORE!!!

## Pushing your changes

Don't get too excited yet, because what you have done has created a problem. Namely, your local branch is now behind your remote branch. when you try to `git push` you are met with this message:
```
To github.com:stirlhoss/variable_practice.git
 ! [rejected]        main -> main (non-fast-forward)
error: failed to push some refs to 'github.com:stirlhoss/variable_practice.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
This is a classic case of git misleading you. Git does not allow you to push to remote if you are not up to date with the remote of the branch you are pushing too. This makes sense and prevents you from pushing conflicting code in most cases, but in this case you don't want to pull down the remote before pushing your revision. If you pull the remote down in this situation, it will pull back down all of the typo commits that exist on GitHub and create duplicates of all proceeding commits. You will now have a big mess of duplicate commits that all have different SHA keys (I think that rebasing regenerates all Proceeding SHA keys).

What you want to do is force the remote repository to reflect your local repository so you need to force git to ignore its "pull before push" rule. You can do this by passing `git push` with the argument `--force` tacked on to the end. Like so:
```
git push --force
```
Now your remote repository should accurately reflect your local repository, and it should include the changes that you made to your commit messages. 
