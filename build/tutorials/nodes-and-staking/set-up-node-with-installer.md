＃使用安装脚本在Linux上运行Avalanche节点

我们有一个外壳\（bash \）脚本，可以在您的计算机上安装AvalancheGo。该脚本可在几分钟之内建立完整的，正在运行的节点，而所需的用户输入最少。

##开始之前

该安装脚本假定：

*操作系统：Ubuntu的18.04或20.04 \（对不起，MacOS和视窗还不支持\）* AvalancheGo没有运行，并且尚未安装为服务*用户运行该脚本有超级用户权限\（可以运行`须藤` \）



###环境注意事项

如果您运行其他类型的Linux，则该脚本可能无法按预期运行。它假定` systemd `用于运行系统服务。其他Linux风格可能使用其他东西，或者文件所处的位置与脚本所假定的不同。

如果您的节点已经在计算机上运行，​​请在运行脚本之前将其停止。

####节点从终端运行

如果您的节点是在终点站它按运行' CTRL + C '。

####节点作为服务运行

如果您的节点已经作为服务运行，那么您可能不需要此脚本。你很好。

####节点在后台运行

如果您的节点是在后台\运行（通过运行` nohup的`，例如\），然后找到程序运行运行的节点'的ps aux | grep雪崩`。这将产生如下输出：

```文本
的ubuntu 6834 0.0 0.0 2828 676点/ 1 S + 19时54 0:00 grep的雪崩
的ubuntu 2630 26.1 9.4 2459236 753316？SL Dec02 1220：52 /家庭/ Ubuntu的/编译/ avalanchego` ''查找没有grep的行。在此示例中，这是第二行。它显示有关您的节点的信息。请注意进程ID，在这种情况下为2630。通过运行kill -2 2630来停止该节点。####节点工作文件如果您以前在此计算机上运行过AvalancheGo节点，则本地节点文件将存储在$ HOME / .avalanchego目录中。这些文件将不会受到干扰，并且脚本设置的节点将以与以前相同的身份和状态继续运行。话虽如此，为了节点的安全，请备份位于$ HOME / .avalanchego / staking中的staker.crt和staker.key文件，并将它们存储在安全的地方。如果需要，可以使用这些文件在另一台计算机上重新创建节点。###网络注意事项若要成功运行，AvalancheGo需要在网络端口“ 9651”上接受来自Internet的连接。在继续安装之前，您需要确定您的节点将在其中运行的网络环境。####在云提供商上运行














如果您的节点在云提供商计算机实例上运行，则它将具有静态IP。找出什么是静态IP，或者如果尚未设置，请进行设置。该脚本将尝试自行查找IP，但可能并非在所有环境中都有效，因此您需要检查IP或自己输入IP。####在家庭连接上运行如果您正在住宅Internet连接上的计算机上运行节点，则您具有动态IP；因此，请执行以下操作：也就是说，您的IP将定期更改。安装脚本将针对这种情况适当地配置节点。但是，对于家庭连接，您将需要设置端口“ 9651”的入站端口转发，该端口从Internet到安装该节点的计算机。As there are too many models and router configurations, we cannot provide instructions on what exactly to do, but there are online guides to be found \(like [this](https://www.noip.com/support/knowledgebase/general-port-forwarding-guide/), or [this](https://www.howtogeek.com/66214/how-to-forward-ports-on-your-router/) \), and your service provider support might help too.## Running the scriptSo, now that you prepared your system and have the info ready, let's get to it.To download and run the script, enter the following in the terminal:```文本














的wget -nd -m的https：//raw.githubusercontent.com/ava-labs/avalanche-docs/master/scripts/avalanchego-installer.sh; \
文件模式755 avalanchego-installer.sh; \ 
./avalanchego -installer.sh ```我们出发了！输出应如下所示：``` text AvalancheGo installer ---------------------正在准备环境...找到了arm64体系结构...正在寻找最新的arm64构建...将尝试下载：https : //github.com/ava-labs/avalanchego/releases/download/v1.1.1/avalanchego-linux-arm64-v1.1.1.tar.gzavalanchego-linux-arm64-v1.1.1.tar.gz 100％[================================= =======================================>] 29.83M 75.8MB / s在0.4s 













2020-12-28 14:57:47 URL：https：//github-production-release-asset-2e65be.s3.amazonaws.com/246387644/f4d27b00-4161-11eb-8fb2-156a992fd2c8？X-Amz-Algorithm = AWS4-HMAC-SHA256＆X-AMZ-凭证= AKIAIWNJYAX4CSVEH53A％2F20201228％2Fus -东- 1％2Fs3％2Faws4_request＆X-AMZ-日期= 20201228T145747Z＆X-AMZ-过期= 300＆X-AMZ-签名= ea838877f39ae940a37a076137c4c2689494c7e683cb95a5a4714c062e6ba018＆X-AMZ-SignedHeaders =宿主actor_id = 0＆KEY_ID = 0＆repo_id = 246387644＆response-content-disposition = attachment％3B％20filename％3Davalanchego-linux-arm64-v1.1.1.tar.gz＆response-content-type = application％2Foctet-stream [31283052/31283052]->“ avalanchego-linux-arm64 -v1.1.1.tar。gz“ [1]
正在解压缩节点文件...
avalanchego-V1.1.1 /插件/ 
avalanchego-V1.1.1 /插件/ EVM 
avalanchego-V1.1.1 / avalanchego
节点文件解压到/家庭/ Ubuntu的/雪崩节点```


然后脚本将提示您输入有关网络环境的信息：```文本要完成设置，需要一些网络信息。在安装了节点：1）住宅网络（动态IP）2）云提供商（静态IP）输入连接类型[1,2]：```enter `1` if you have dynamic IP, and `2` if you have a static IP. If you are on a static IP, it will try to auto-detect the IP and ask for confirmation.```textDetected '3.15.152.14' as your public IP. Is this correct? [y,n]:```Confirm with `y`, or `n` if the detected IP is wrong \(or empty\), and then enter the correct IP at the next prompt.The script will then continue with system service creation and finish with starting the service.```textInstalling service with public IP: 3.15.152.14





















创建了符号链接/etc/systemd/system/multi-user.target.wants/avalanchego.service→/etc/systemd/system/avalanchego.service。完毕！您的节点现在应该在主网上进行引导。要检查服务是否正在运行，请使用以下命令（q退出）：sudo systemctl status avalanchego要跟踪日志使用（ctrl + C停止）：sudo journalctl -u avalanchego -f在https：// chat上找到我们.avax.network（如果您遇到问题）。```The script is finished, and you should see the system prompt again.## Post installationAvalancheGo should be running in the background as a service. You can check that it's running with:```textsudo systemctl status avalanchego```This will print the node's latest logs, which should look like this:```text● avalanchego.service - AvalancheGo systemd service已加载：已加载（/etc/systemd/system/avalanchego.service；已启用；供应商预设：已启用）



























活动：自UTC星期二2021-01-05 10:38:21起处于活动（运行）状态；51s前
主PID：2142（avalanchego）
任务：8（限制：4495）
内存：223.0M 
CGroup：/system.slice/avalanchego.service└─2142/ 
home / ubuntu / avalanche-node / avalanchego --plugin-dir = / home / ubuntu / avalanche-node / plugins --dynamic-public-ip = opendns --http-host = Jan 05 10:38:45 ip-172-31-30-64 avalanchego [2142]：信息[01- 05 | 10：38：45] <P链> avalanchego / vms / platformvm / vm.go＃322：将最后接受的块初始化为2FUFPVPxbTpKNn39moGSzsmGroYES4NZRdw3mJgNvMkMiMHJ9e1月5日10:38:45 ip-172-31-30-64 avalanchego [2142]：信息[01-05 | 10：38：45] <P链> avalanchego / snow / engine / snowman / transitive.go＃58 ：初始化共识引擎1月5日10:38:45 ip-172-31-30-64 avalanchego [2142]：信息[01-05 | 10：38：45] avalanchego / api / server.go＃143：添加路线/ ext / bc / 11111111111111111111111111111111LpoYY 




Jan 05 10:38:45 ip-172-31-30-64 avalanchego [2142]：INFO [01-05 | 10：38：45] avalanchego / api / server.go＃88：HTTP API服务器在“：9650”上侦听
Jan 05 10:38:58 ip-172-31-30-64 avalanchego [2142]：信息[01-05 | 10：38：58] <P链> avalanchego / snow / engine / common / bootstrapper.go＃185：引导程序开始与已接受边界中的1个顶点同步
1月5日10:39:02 ip-172-31-30-64 avalanchego [2142]：信息[01-05 | 10：39：02] <P链> avalanchego / snow / engine / snowman / bootstrap / bootstrapper.go ＃210：获取了2500个块
Jan 05 10:39:04 ip-172-31-30-64 avalanchego [2142]：信息[01-05 | 10：39：04] <P链> avalanchego / snow / engine / snowman /bootstrap/bootstrapper.go#210：获取了5000个区块
1月5日10:39:06 ip-172-31-30-64 avalanchego [2142]：信息[01-05 | 10：39：06] <P链> avalanchego /snow/engine/snowman/bootstrap/bootstrapper.go#210：提取了7500个块
1月5日10:39:09 ip-172-31-30-64 avalanchego [2142]：信息[01-05 | 10：39：09] <P链> avalanchego /雪/引擎/雪人/引导程序/ bootstrap.go＃210：获取了10000个块
1月5日10:39:11 ip-172-31-30-64 avalanchego [2142]：信息[01-05 | 10：39：11] <P链> avalanchego / snow / engine / snowman / bootstrap / bootstrapper.go＃210：取出12500块```注意'active（running）`，它表明服务运行正常。您可能需要按“ q”返回命令提示符。要找到用于标识网络节点的NodeID，请运行以下命令：``` text须藤journalctl -u avalanchego | grep的“例程ID” ```它将产生如下输出：``` text年1月5 10点38分38秒的IP-172-31-30-64 avalanchego [2142]：INFO [01-05 | 10：38：38] avalanchego /节点/ node.go＃428：设置处理器的ID来6seStrauyCnVV7NEVwRbfaT9B6EnXEzfY”`将NodeID-放在值之前，例如，获取NodeID-6seStrauyCnVV7NEVwRbfaT9B6EnXE。储存；放样或查找节点将需要它。您的节点现在应该正在引导中。您可以通过监视发出以下命令进度：```













文本须藤journalctl -u avalanchego -f ```按下`CTRL + C`当你想停止阅读节点输出。##停止节点停止AvalancheGo，运行：```文本须藤systemctl停止avalanchego ```To start it again, run:```textsudo systemctl start avalanchego```## Node upgradeAvalancheGo is an ongoing project and there are regular version upgrades. Most upgrades are recommended but not required. Advance notice will be given for upgrades that are not backwards compatible. When a new version of the node is released, you will notice log lines like:```text1月8日10:26:45 ip-172-31-16-229 avalanchego [6335]：信息[01-08 | 10：26：45] avalanchego / network / peer.go＃526：beacon 9CkG9MBNavnw7EVSRsuFr7ws9gascDQy3尝试与较新的版本连接雪崩版本1.1.1。您可能需要更新您的客户端```


































建议始终升级到最新版本，因为新版本会带来错误修复，新功能和升级。要升级您的节点，只需再运行安装脚本：```文本./avalanchego-installer.sh ```It will detect that you already have AvalancheGo installed:```textAvalancheGo installer---------------------Preparing environment...Found 64bit Intel/AMD architecture...Found AvalancheGo systemd service already installed, switching to upgrade mode.Stopping service...```


















It will then upgrade your node to the latest version, and after it's done, start the node back up, and print out the information about the latest version:

```text
Node upgraded, starting service...
New node version:
avalanche/1.1.1 [network=mainnet, database=v1.0.0, commit=f76f1fd5f99736cf468413bbac158d6626f712d2]
完毕！```


## What next?

That's it, you're running an AvalancheGo node! Congratulations! Let us know you did it on our [Twitter](https://twitter.com/avalancheavax), [Telegram](https://t.me/avalancheavax) or [Reddit](https://t.me/avalancheavax)!

If you're on a residential network \(dynamic IP\), don't forget to set up port forwarding. If you're on a cloud service provider, you're good to go.

现在，您可以[与您的节点进行交互]（../../ avalanchego-apis / issuing-api-calls.md），[放权令牌] -avalanche-wallet.md），或通过设置[节点监视]（setting-up-node-monitoring.md）来升级安装，以更好地了解您的节点在做什么。

如果您有任何疑问或需要帮助，请随时在我们的[ Discord ]（https://chat.avalabs.org/）服务器上与我们联系。

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMDUxMjY5OV19
-->