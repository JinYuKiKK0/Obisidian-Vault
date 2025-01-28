### git commit "message"
- 在Commit Tree上创建新的`Commit`节点，并将父提交指向旧的`Commit`
- 移动`HEAD`指针至新的`Commit`节点
### git branch [branchName]
- 在当前`HEAD`处创建一个分支指针
- 添加新的`Commit`节点时，当前所在分支将向前移动，而其余分支不变
### git checkout [branchName]
- 切换到该分支
- 使用`git checkout -b <branchName>`创建一个新的分支并切换过去
### git merge [branchName]
- 创建一个新的`Commit`节点，此节点继承当前分支与该分支的`Commit`内容，同时移动当前分支到新节点

