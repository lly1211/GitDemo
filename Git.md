****

## Git

### git branch

```apl
1、切换到某分支　　git checkout branchName
2、创建某分支　　git branch branchName
3、查看本地所有分支　　git branch
4、查看远程所有分支　　git branch -r
5、查看本地和远程所有分支　　git branch -a
6、删除本地分支　　git branch -d branchName
7、删除远程分支　　git push origin :branchName　　(":"代表删除)
9、重命名　　git branch -m oldName newName
```



### git fetch

```apl
1、将更新取回　　git fetch origin
2、将某分支更新取回　　git fetch origin branchName
```



### git pull

```apl
1、git pull 可以理解为 fetch + merge
git fetch orgin master + git merge FETCH_HEAD = git pull master
2、将远程主机的某个分支更新取回，并与本地合并　　git pull <远程主机名> <远程分支名> : <本地分支名>
3、默认和当前分支合并　　git pull origin next
```



### git log

```apl
1、查询日志信息　　git log
```