How to rebase a public feature branch without going crazy
=========

Imagine the following scenario (which should not be too hard since we face this situation __all the time__):
  - You have a `master` branch
  - You also have a `feature` branch
  - You do your work on the `feature` branch and then make a pull request, for which you have to do `git push --set-upstream origin feature`
    first
  - Your CTO is not happy yet and has some change requests
  - You continue working on the `feature` branch
  - In the meantime, a whole lot of other work is going on in the public master
  - You are now done with your fixes and ready to push your finished `feature` branch
  - Before doing that, you have to do `git rebase master` while inside your `feature` branch.
  - You try `git push origin feature`, but the server rejects your changes, because you changed history when rebasing your branch with the current `master`.

Now, in this situation three options come to mind:
  - You can do `git push -f origin feature`. But that is just evil, for several reasons:
   - What if you are already thinking about this hotfix you have to push into master in a couple of minutes, and your brain makes you type `git push -f origin master`? That would overwrite the public `master` with your local `feature`, and I think I do not have to explain why that is bad.
   - Even if you push to the correct branch: What if another team member was working on the `feature` branch, too? You would either overwrite their work, or force them into force pushing their work, and that is double evil.
  - Your second, much less dangerous option would be to merge the current `master` into your `feature` branch. That is completely safe, but makes the repo's history very ugly. Also, the CTO told you to never do that.
  - You can always quit you job and start that career as a singer-songwriter you have always dreamed of.

Of course, none of these options are good enough for a top-notch software company like this place.

Before I present to you a new workflow for rebasing our branches, let us summarize what we actually want:
 - We want to be able to work on a public feature branch.
 - We want to be able to integrate the current `master` into it at any point.
 - We want a clean history.
 - We __do not__ want to use `git push -f` __ever__ again. If we ever catch ourselves typing this line, we have to immediatly get up and stand in a corner for an hour, while our work mates throw last Wednesday's leftovers at us.

Here is what we have to do after doing some work in our (public) `feature` branch:
```
     [feature]> git checkout master
      [master]> git pull origin master
      [master]> git checkout -b feature-temp
[feature-temp]> git rebase feature
[feature-temp]> git checkout feature
     [feature]> git rebase feature-temp
     [feature]> git push origin feature
```

Let's see what we just did, line for line:
 0. After finishing our work on `feature`, we checkout `master`.
 0. We fetch the current public `master`.
 0. We create a new temporary branch that will never be pushed and is identical to the current `master`. We also checkout the new branch.
 0. We put all the work we did in `feature` on top of the temporary branch. It now contains all the work from `feature`, but also everything from the current `master`.
 0. We switch back to the `feature` branch.
 0. We rebase `feature` with the temporary branch. It is now identical to the temporary branch.
 0. We push the feature branch to the server. Notice how we did not have to force-push?

 And that's it!