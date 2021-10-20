# git merge与git rebase

在Git中整合不同分支的修改主要有两种方法：`merge`以及`rebase`。

我们最常用的就是`merge`啦。

### merge

在Git工作流中，我们通常使用master分支作为稳定发布版本，使用dev分支作为开发分支。

![1571887982306](http://cdn.geker.net/1571887982306.png)

那么问题来了，假如我们在`dev`上的工作完成了，就可以把`dev`合并到`master`上。Git怎么合并呢？最简单的方法，就是使用`git merge`命令，就完成了合并：

```bash
$ git checkout master

Switched to branch 'master'

$ git merge dev

Merge made by the 'recursive' strategy.
index.html |    1 +
1 file changed, 1 insertion(+)
```

![1571888594661](http://cdn.geker.net/1571888594661.png)

可以看到merge后，master分支会产生一个新的commit，这是因为master分支所在提交不是dev分支所在提交的直接祖先提交，git需要做一些额外的工作。git会使用两个分支的末端也就是001和002，以及两个分支的祖先(0000)，做一个简单的三方合并。

#### 发生冲突

在不同的分支中，对同一个文件的同一部分进行了不同的修改，这时git没法去合并他们。也就需要我们自己去解决冲突，这里就不再细说啦。

### rebase

我们已经学过了merge，merge是将两个分支的最后一次提交的快照，以及二者最近的共同祖先进行三方合并，合并后生成一个新的快照。

![未整合](http://cdn.geker.net/1571887982306.png)

merge整合：

![merge整合](http://cdn.geker.net/1571888594661.png)

而rebase是另外一种整合不同分支的修改的方法，它可以让你提取`task: 002-commit2`中的修改，然后在`task: 001-commit1`的基础上应用一次。这个操作就叫做变基。我们可以使用rebase命令将提交到某一分支上的所有修改都移到另一分支上。

我们恢复master和dev分支的提交到未整合的状态：

![未整合](http://cdn.geker.net/1571887982306.png)

运行命令：

```bash
$ git checkout dev

Already on 'dev'

$ git rebase master

First, rewinding head to replay your work on top of it...
Applying: task: 002-commit2
```

这样就将dev分支上所有的分支应用到master分支了。

它的原理是首先找到这两个分支(当前分支`dev`，`rebase`操作的目标基底分支`master`)的最近共同祖先`task: 0000-commit00`，然后对比当前分支相对于该祖先的历次提交，提取相应得修改并存为临时文件，然后将当前分支指向目标基底`task: 001-commit1`，最后以此将之前另存为临时文件的修改依序应用。

变基整合后：

![变基后](http://cdn.geker.net/1571896973275.png)

整合后，rebase例子中的master分支和merge例子中的master分支就一模一样了。merge和rebase的最终结果没有任何区别，但是rebase使提交历史记录更加整洁。我们在查看一个经过rebase的分支的提交记录时会发现，尽管实际的开发工作是并行的，但他们看上去就像是串行的一样，提交记录是一条直线没有分叉。

#### 发生冲突

![1571899707055](http://cdn.geker.net/1571899707055.png)

如图，master分支和dev分支都修改了index.html文件，然后执行rebase命令：

```bash
$ git checkout master

Already on 'master'

$ git rebase dev

First, rewinding head to replay your work on top of it...
Applying: task: 001-commit1
Using index info to reconstruct a base tree...
Falling back to patching base and 3-way merge...
Auto-merging index.html
CONFLICT (add/add): Merge conflict in index.html
error: Failed to merge in the changes.
hint: Use 'git am --show-current-patch' to see the failed patch
Patch failed at 0001 task: 001-commit1

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

这样就冲突了，这时rebase进入了临时分支中，需要我们去解决冲突的文件：

![1571899943239](http://cdn.geker.net/1571899943239.png)

解决后使用git add命令添加文件：

```bash
$ git add index.html
```

让git rebase继续工作：

```bash
$ git rebase --continue

Applying: task: 001-commit1
```

这样就解决啦。

#### 退出rebase

如果在rebase的过程中，我们需要退出rebase，这时我们需要执行命令：

```bash
$ git rebase --abort
```

#### 使用场景

**自己的开发分支有过多的无用提交记录，怎么解决？**

示例：

```bash
$ git log

commit b0df2b9ed8b0959349388a643978e314bc26366e (HEAD -> dev)
Author: Eick <oe52920@gmail.com>
Date:   Thu Oct 24 14:47:01 2019 +0800

    task: 002-commit2

commit 9739d8f2511379d0497e489e9e7d69aa3721390d
Author: Eick <oe52920@gmail.com>
Date:   Sat Oct 12 18:24:49 2019 +0800

    task(<E5><95><8A>): 256-test

commit cb164ee29e5f80f129c111e4b35f705b95276e52
Author: Eick <oe52920@gmail.com>
Date:   Sat Oct 12 18:23:22 2019 +0800

    task: 256-test

commit 7bc38caa5a3314449dd3717b50f8f508a2ce428a
Author: Eick <oe52920@gmail.com>
Date:   Sat Oct 12 18:22:44 2019 +0800

    task: 256 test
```

这时我们需要rebase自己的开发分支，使用git rebase -i：

```bash
$ git rebase -i 7bc38caa5a3314449dd3717b50f8f508a2ce428a

pick cb164ee task: 256-test
pick 9739d8f task(▒~U~J): 256-test
pick b0df2b9 task: 002-commit2

# Rebase 7bc38ca..b0df2b9 onto 7bc38ca (3 commands)
```

我们需要将无用的提交记录信息前的`pick`改成`squash`，或简写`s`，然后保存让rebase继续工作：

```bash
# This is a combination of 3 commits.
# This is the 1st commit message:

task: 256-test

# This is the commit message #2:

task(▒~U~J): 256-test

# This is the commit message #3:

task: 002-commit2
```

这里需要我们调整提交记录，可以只保留一条，修改后保存。

```bash
[detached HEAD 613b00c] task: 256-test
 Date: Sat Oct 12 18:23:22 2019 +0800
 3 files changed, 12 insertions(+), 1 deletion(-)
 create mode 100644 index.html
Successfully rebased and updated refs/heads/dev.
```

这样就解决无用的提交记录啦。

### 区别

#### 结果

* merge和rebase整合的最终结果所指向的快照始终是一样的，只不过提交记录不同罢了。

#### 机制

* merge是把最终结果合在一起。
* rebase是将一系列提交按照原有次序依次应用到另一分支上。
