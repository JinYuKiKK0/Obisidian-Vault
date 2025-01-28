## Objects  
  
### blob  
  
- 保存的文件内容  
- 文件内容,文件内容对应的SHA-1  
  
### commit  
  
- 属性:message,parents,date,blobID  
- metaData(commit date & message)  
- 提交的文件名以及指向这些文件内容(blob)的引用  
- 对blob的引用通过记录blob的SHA-1值实现  
- 对父提交的引用  
  
### HEAD & master  
  
- HEAD始终指向当前工作分支的头部  
  
## pointer问题  
  
由于序列化会保存该对象内部所有的指针以及指针所指向的对象，这会导致文件过大\  
solution:take place pointer with String 用文件名代替指针  
  
## Command  
  
### init  
  
创建项目目录  
  
```  
.gitlet/  
    - objects/  
        Commits/  
            - ...(files of commits)  
        blobs/  
            - ...(files of blobs)  
    - branches/  
        - master  
        - ...(other branches)  
    - HEAD  
    - BRANCH  
    - Stages/  
        addStage  
        removeStage  
```  
  
- 创建添加暂存区和删除暂存区(addStage,removeStage)  
- 在Commits中创建initial commit,同时创建HEAD指针和master指针指向initial commit\  
  **失败的情况:A Gitlet version-control system already exists in the current directory.**  
  
### add [filename]  
  
- 创建该文件的blob，将待添加的文件的文件名与内容存入blob  
- 如果文件当前工作版本与当前Commit中的版本一致，则不要暂存。如果已经暂存，将其从暂存区移除  
- 检查addStage，若该文件已暂存，则用新内容覆盖暂存区中的先前条目  
- 若该文件位于removeStage，移出(涉及 rm command)  
- 在添加暂存区中写入 待添加文件与对应的blob之间的映射关系 Hello.txt -> blob 0\  
- 待添加文件的情况：  
1. 文件被跟踪  
   1. 文件被跟踪，但在CWD中有更改，add会将文件的修改暂存  
   2. 文件被跟踪，但在CWD中已经删除，从removeStage中移除  
   3. 文件被跟踪，且与当前commit中的版本一致，不进行任何操作  
2. 文件未被跟踪，但已经暂存  add会更新暂存区中的文件内容  
3. 文件未被跟踪，也未暂存 是新文件，添加至暂存区  
4. 文件不存在于CWD与gitlet中，打印File does not exist.并退出  
  **失败的情况:File does not exist.**  
  
### commit [message]  
  
- 在Commits中克隆HEAD指向的commit,然后根据用户提供的message和当前data修改克隆后的commit的metadata  
- 根据Stage中文件与blob的映射关系，修改新commit对blob的引用  
- 对于addStage中的信息，在commit中添加对应blob的引用  
- 对于removeStage中的信息，在commit中删除对应blob的引用，同时删除该blob  
- 标明该commit的parent commit，同时移动HEAD和master至最新的commit  
- commit后,暂存区应当清空\  
  **失败的情况:1.若没有任何文件暂存,打印 No changes added to the commit.**\  
  **失败的情况:2.每个提交都必须包含非空消息 如果它不包含,则打印 Please enter a commit message.**\  
  <img src="C:\Users\JinYu\Pictures\Screenshots\屏幕截图 2025-01-04 145912.png"/>  
### rm [filename]  
  
- 若当前文件已暂存入addStage，则从addStage移除  
- 如果文件在当前提交中被跟踪，  
  
1. 该文件存在于工作目录中：放入removeStage并从工作目录删除。在下次commit中记录  
2. 该文件不存在于工作目录中：放入removeStage即可（commit后手动删除文件）  
  
- blob只有在完全不再被任何提交引用时，才会被删除  
  **失败的情况:如果文件既未暂存也未由 HEAD 提交跟踪，则打印错误消息 No reason to remove the file.**  
  
### log  
  
- 从HEAD提交开始，对提交树进行DFS，显示每个提交信息  
- 忽略合并提交中发现的任何第二个父提交  
- 对于每个提交历史记录，应该显示：CommitID ,Commit date ,message  
- 对于合并提交(包含两个父提交的提交),在commitID的下一行插入\  
  <img src="C:\Users\JinYu\Pictures\Screenshots\屏幕截图 2025-01-11 174312.png"/>- “Merge:” 后面的两个十六进制数字依次由第一个和第二个父提交 ID 的前七位数字组成。第一个父提交是您进行合并时所处的分支；第二个是合并进来的分支。  
  
### global-log  
  
- 显示所有提交的信息，不考虑提交的顺序，使用plains...方法  
  
### find [commit message]  
  
- 打印所有包含指定提交信息的提交ID，每行一个;若有多个提交，则打印在不同的行  
- commit message是一个单参数，若指示多词信息，将参数放入引号，使用plains...方法  
  **失败的情况:若不存在此提交，打印 Found no commit with that message.**  
  
### status  
  
- Branches展示当前处于哪个分支，并用*标记当前分支  
- 每个部分间用换行符隔开，status最后以换行符结束。每个条目以字典序排列，使用Java字符串比较顺序(星号不算)  
- Modification Not Staged For Commit 的文件有四种情况  
  
1. 被当前Commit追踪，工作目录中的版本更改与Commit中的版本不同，但尚未暂存  
2. 已处于addStage，但工作目录中存在更改，与addStage中版本不同  
3. 已处于addStage，但已从工作目录中移除  
4. 不处于removeStage，从工作目录中移除，但被当前Commit追踪  
   //CWD中文件已更改  
   //1.文件被追踪，CWD中版本与Commit中追踪版本不同，尚未暂存  
   //2.未追踪但已被暂存，CWD中版本与addStage中不同  
   //CWD中文件已移除  
   //1.文件已处于addStage，但在CWD中移除  
   //2.文件不处于removeStage，从CWD中移除，但被当前Commit追踪  
  
- Untracked Files  
  
1. 包含工作目录中存在，但既未暂存添加也未跟踪的文件。\  
   <img src="C:\Users\JinYu\Pictures\Screenshots\屏幕截图 2025-01-11 175817.png">  
### checkout [fileName] checkout [commit id] --  [fileName] checkout[branch Name]  
  
- checkout -- [fileName] 将HEAD提交中的同名文件恢复到CWD,如果该文件在CWD已存在，则覆盖它  
  **失败的情况: 若该提交中不存在此文件，不要更改CWD，打印：File does not exist in that commit.**  
- checkout [commit id] -- [fileName] 将指定id的提交中的同名文件恢复到CWD，若文件已存在，覆盖它\  
  **1.失败的情况: 不存在给定id的提交，打印：No commit with that id exists.**\  
  **2.失败的情况: 若该提交中不存在此文件，不要更改CWD，打印：File does not exist in that commit.**  
- checkout [branchName]  
  
1. 将checked branch的头提交中的所有文件复制到CWD，若文件已存在，覆盖它。  
2. 该命令执行完毕后，给定分支将被视为当前分支(HEAD)  
3. current branch跟踪但未出现在checkout branch中的文件将被删除。除非checkout branch is current branch\  
   checked branch和当前所跟踪的文件分为三类  
   - 仅被当前commit 跟踪(删除这些文件)  
   - 被checked branch和当前commit都跟踪(用checked branch的blobs覆写这些文件)  
   - 仅被checked branch跟踪的文件 分为两类  
      1. 该文件不存在于CWD中,恢复并覆盖  
      2. **失败的情况**:同名文件存在于CWD,打印：There is an untracked file in the way; delete it, or add and commit it  
           first.并退出，**在执行任何其他操作之前进行此检查**  
4. 将当前分支改为该分支  
5. 清空暂存区\  
**1.失败的情况：如果不存在该名称的分支，则打印 No such branch exists.**\  
**2.失败的情况：No need to checkout the current branch.**\  
**3.如果当前分支中存在未跟踪的工作文件，并且检出操作会覆盖该文件，则打印 There is an untracked file in the way; delete it, or add and commit it first. 并退出**  
### branch [branchName]  
- 创建一个具有给定名称的新分支，并将其指向当前头提交  
- 该命令不会将当前分支立即切换到新创建分支  
**失败的情况：如果已存在具有给定名称的分支，则打印错误信息 A branch with that name already exists.**  
### rm-branch [branchName]  
- 删除具有给定名称的分支，仅删除与该分支相关联的指针，不删除在该分支下创建的所有提交  
**1. 失败的情况：如果不存在指定名称的分支，则中止。打印错误消息 A branch with that name does not exist.**  
**2. 失败的情况：尝试删除当前所在的分支，则中止，并打印错误消息 Cannot remove the current branch.**  
### reset [commit id]  
<p> 该命令将版本回滚到指定的commitId处，并将HEAD指向给定的commitId，清空暂存区</p>  
  
### merge [branchName]  
- 编写findSplitPoint(),  
- 如果split point与给定分支的提交相同，合并完成，打印 Given branch is an ancestor of the current branch.  
- 如果split point是当前分支，检出给定分支，打印 Current branch fast-forwarded. 否则，执行以下操作\  
- 每当HEAD列与result列产生差异的时候，将HEAD列的文件暂存  
- 对于某一个文件，只要是有一个Branch有过修改，就保留这个Branch中对于文件的修改，如果两个Branch都有修改，内容相同，也保留，产生的新commit message为Merged [given branch name] into [current branch name].  

### 三路合并算法  
> 先判断HEAD/Other相对于split是否有修改或是否已删除，然后按照下方规则合并  
##### split存在该文件  
- 若仅一方修改，合并后保留该修改,overwrite  
- 若双方皆修改且内容一致，合并后保留一致修改，overwrite  
- 若双方皆删除，合并后保留删除,delete  
- 若有一方删除，另一方未删除且未修改，合并后保持删除,delete  
- 双方皆修改文件，且内容不一致,conflict  
- 一方修改，另一方删除,conflict  
___  
##### split不存在该文件  
- 仅某个分支新增文件，合并后保留新增文件,write  
- 双方文件内容不一致,conflict  
##### 冲突的情况  
- split存在 双方皆修改文件，且内容不一致  
- split存在 一方修改，另一方删除  
- split不存在 双方文件内容不一致  
  
  
| File | contents in split | contents in HEAD | contents in otherBranch | Result |  
|:----:|:-----------------:|:----------------:|:-----------------------:|:------:|  
|  a   |         A         |        A         |           !A            |   !A   |  
|  b   |         B         |        !B        |            B            |   !B   |  
|  c   |         C         |        /         |            /            |   /    |  
|  d   |         D         |        D         |            /            |   /    |  
|  e   |         E         |        /         |            E            |   /    |  
|  f   |         /         |        /         |           !F            |   !F   |  
|  g   |         /         |        G         |            /            |   G    |