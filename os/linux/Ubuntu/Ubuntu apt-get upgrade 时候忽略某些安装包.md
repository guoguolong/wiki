> sudo apt-get update
> sudo apt-get upgrade

的时候遇到一个问题，有些包由于网速问题始终更新不了，所以想忽略某些特定包升级。

> sudo apt-mark hold gitlab-ce

等执行完 sudo apt-get upgrade再执行：

> sudo apt-mark unhold gitlab-ce

即可.