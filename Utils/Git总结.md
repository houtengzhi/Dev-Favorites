## Git总结

##### 1. 合并多个commit
参考: https://segmentfault.com/a/1190000007748862
     https://www.jianshu.com/p/964de879904a

1. 指明要合并的commit之前的commit id.
`git rebase -i 3a44333g`

2. 保留一个commit的pick作为要执行的commit, 其它需要合并的commit的pick改为squash;

