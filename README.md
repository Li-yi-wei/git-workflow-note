# git 工作流程筆記

### 同步remote的程式
因為git通常是多個人共同編寫程式時常會用到的工具，所以工作前會需要先與remote同步

如果工作的電腦裡沒有程式就先使用 git clone將程式複製到電腦裡
```
git clone <clone程式的網址>
```
![圖片](https://hackmd.io/_uploads/B10eqj2Dkl.png)

如果已經有程式，但是是舊的(其他人更新的內容沒有更新到電腦)，就使用pull
```
git pull
```

### 建立新的branch
#### 建立新的branch的好處
* 如果剛修改的程式有bug，不至於會讓main branch搞到無法工作
* 有利於多人合作
#### 建立branch
```
git checkout -b <branch的名字>
```
這會把目前branch內的東西複製一分到新的branch上，以下為了區分將剛新增的branch叫my-branch，原本的叫main branch
>此時的my-branch和main-branch內容是一樣的
### 將修改後的內容傳給git
#### git diff
使用git diff可以看到修改後的程式與原先存在git的有什麼不同，這步不是必要，但有時可以避免ㄧ些錯誤
```
git diff
```
#### git add
使用git add可以將修改的內容告知git
```
git add <修改的文件名稱>
```
如果想把全部的內容都告知給git可以使用`git add .`
#### git commit
將使用add後告知給git的內容放進git裡面
```
git commit 
```
此時git裡就會新增一個commit在my-branch裡(main-branch不變)
### 將local git的內容告知給remote
剛才做的那些動作都只有修改到local git的部分，remote那端完全不知道做了什麼改變，所以必須將修改內容push到remote上
```
git push origin <branch名稱>
```
以此為例就是`git push origin my-branch`

此時remote會多出一個branch叫做my-branch
### 同步main branch的內容到自己的branch
因為git常用於多人合作開發，所以有時會發生自己上傳自己的branch前main branch多了一個commit，此時必須測試my-branch在新的commit下還能不能正常運作
#### 更新local branch
目前local git的main-branch和remote的main-brach是不同的，所以要先同步
```
git checkout main
git pull origin master
```
這麼做會先切換到local的main branch，然後把remote更新過後的main branch同步到local的main branch
#### rebase main
因為my-branch的內容是沒有remote更新過後的內容，所以必須先將更新過後的內容update到my-branch
```
git checkout my-branch
git rebase main
```
這麼做會把main的修改放在my-branch的修改前，這麼做後有可能會rebase conflict，如果發生了就必須手動選擇要保留什麼

此時就等同於在更新後的main branch做修改
#### 再次push
```
gii push -f origin my-branch
```
將含有修改後的main branch和自己修改的部分push到remote
這次多了-f的原因是有rebase過，所以必須加個-f強行push否則無法push到my-branch
### pull request
這個是請求的項目的主人將my-branch的內容pull到main branch
此時項目的主人審查了修改後如果認為內容沒問題通常會使用squash and merge將my-branch的修改merge到main branch，接下來會將remote的branch刪除，只剩下main branch
### 收尾
remote的branch刪掉了，local通常也要刪掉
```
git checkout main 
git branch -D my-branch
```
接下來把local的main branch也跟remote的main branch同步
```
git pull origin master
```
此時local git和remote git的內容就相同了，接下來要做新的修改就回到前面重複一樣的事情