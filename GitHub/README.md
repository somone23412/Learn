[resource here](https://akrabat.com/the-beginners-guide-to-contributing-to-a-github-project/)
# [Note]How to contribute to opensource?

#### Step 1: Set up a working copy on your computer

1. git clone.
2. git remote add upstream.


#### Step 2: Get it working on your machine

If you get it working, but `the documentation is unclear`, then improving that bit should `be your first PR to the project`.

#### Step 3: Do some work

 It's usually best to start by `fixing a bug` that is either annoying you or you've found on the project's `issue` tracker.
 
 ##### Branch!
 
`Put each piece of work on its own branch!`

+ **Bug fixing:** branch from `master`.
+ **Adding a new feature:** branch from `develop`.

1. git checkout master
2. git pull upstream master && git push origin master
3. git checkout -b hotfix/readme-update

The git pull command will sync our local copy with the upstream project and the git push syncs it to our `forked` GitHub project. If the project uses git-flow, then there are specific naming conventions where the branch is prefixed with "hotfix/" or "feature/".


+ **If the project has tests:** run them to ensure you haven't broken anything.
+ You may also add a new test to show that your change fixes the original problem.

#### Step 4: Create the PR

`Push your branch` to the origin remote and then `press some buttons` on GitHub.

1.  git push -u origin hotfix/readme-update

`-u` flag links this branch with the remote one, so that in the future, you can simply type git push origin.

Swap back to the browser and navigate to `your fork of the project`  and you'll see that your new branch is listed at the top with a handy `"Compare & pull request" button`:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190324233840591.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190324233817844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTkxNQ==,size_16,color_FFFFFF,t_70)
Double check that the diff of changes contains what you expect.

#### Step 5: Review by the maintainers

`Maintainers is considering:`
+ What This Change Does?
+ Does It Relate To A Ticket (And Only One Ticket)?
+ Do These Changes Touch The Tree As I'd Expect?
+ Does The Commit History Make Sense?
+ Eyeball The Diff.
+ Run The Code.

#### To sum up

1. Fork the project & clone locally.
2. Create an upstream remote and sync your local copy before you branch.
3. Branch for each separate piece of work.
4. Do the work, write good commit messages, and read the CONTRIBUTING file if there is one.
5. Push to your origin repository.
6. Create a new PR in GitHub.
7. Respond to any code review feedback.

`The best open source project to pick is the one that you are using yourself.`The maintainers will appreciate it!
