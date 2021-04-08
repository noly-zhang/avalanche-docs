＃升级您的AvalancheGo节点

{％embed url =“ https://youtu.be/o4Fww-sHoaQ”％}

## **备份您的节点** 

升级节点之前，建议您备份放样文件，这些文件用于标识网络上的节点。在默认安装中，可以通过运行以下命令来复制它们：

```文本
cd 
cp〜/ .avalanchego / staking / staker.crt。
cp〜/ .avalanchego / staking / stroker.key。```


然后下载` staker.crt `和` staker.key `文件并保存在安全的地方和私人。如果您的节点发生任何事情或机器节点在运行，则这些文件可用于完全重新创建您的节点。

如果您将节点用于开发目的并且在节点上有密钥库用户，则也应该备份这些用户。

##使用安装程序脚本安装的节点

如果使用[安装程序脚本]（set-up-node-with-installer.md）安装了节点，则要升级节点，只需再次运行安装程序脚本即可。

```text
./avalanchego-installer.sh
```

它将检测到您已经安装了AvalancheGo：

```text
AvalancheGo installer
---------------------
Preparing environment...
Found 64bit Intel/AMD architecture...
Found AvalancheGo systemd service already installed, switching to upgrade mode.
Stopping service...
```

It will then upgrade your node to the latest version, and after it's done, start the node back up, and print out the information about the latest version:

```text
Node upgraded, starting service...
New node version:
avalanche/1.1.1 [network=mainnet, database=v1.0.0, commit=f76f1fd5f99736cf468413bbac158d6626f712d2]
Done!
```

And that is it, your node is upgraded to the latest version.

If you installed your node manually, proceed with the rest of the tutorial.

## **Stop the old node version**

After the backup is secured, you may start upgrading your node. Begin by stopping the currently running version.

### Node running from terminal

If your node is running in a terminal stop it by pressing `ctrl+c`.

### Node running as a service

If your node is running as a service, stop it by entering:

`sudo systemctl stop avalanchego.service`

\(your service may be named differently, `avalanche.service`, or similar\)

### Node running in background

If your node is running in the background \(by running with `nohup`, for example\) then find the process running the node by running `ps aux | grep avalanche`. This will produce output like:

```text
ubuntu  6834  0.0  0.0   2828   676 pts/1    S+   19:54   0:00 grep avalanche
ubuntu  2630 26.1  9.4 2459236 753316 ?      Sl   Dec02 1220:52 /home/ubuntu/build/avalanchego
```

In this example, second line shows information about your node. Note the process id, in this case, `2630`. Stop the node by running `kill -2 2630`.

Now we are ready to download the new version of the node. You can either download the source code and then build the binary program, or you can download the pre-built binary. You don’t need to do both.

Downloading pre-built binary is easier and recommended if you're just looking to run your own node and stake on it.

Building the node [from source](upgrade-your-avalanchego-node.md#build-from-source) is recommended if you're a developer looking to experiment and build on Avalanche.

## **Download Pre-built Binary**

If you want to download a pre-built binary instead of building it yourself, go to our [releases page](https://github.com/ava-labs/avalanchego/releases), and select the release you want \(probably the latest one.\)

Under `Assets`, select the appropriate file.

For MacOS:  
Download: `avalanchego-macos-<VERSION>.zip`  
Unzip: `unzip avalanchego-macos-<VERSION>.zip`  
The resulting folder, `avalanchego-<VERSION>`, contains the binaries.

For Linux on PCs or cloud providers:  
Download: `avalanchego-linux-amd64-<VERSION>.tar.gz`  
Unzip: `tar -xvf avalanchego-linux-amd64-<VERSION>.tar.gz`  
The resulting folder, `avalanchego-<VERSION>-linux`, contains the binaries.

For Linux on RaspberryPi4 or similar Arm64-based computers:  
Download: `avalanchego-linux-arm64-<VERSION>.tar.gz`  
Unzip: `tar -xvf avalanchego-linux-arm64-<VERSION>.tar.gz`  
The resulting folder, `avalanchego-<VERSION>-linux`, contains the binaries.

You are now ready to run the new version of the node.

### Running the node from terminal

If you are using the pre-built binaries on MacOS:

```cpp
./avalanchego-<VERSION>/build/avalanchego
```

If you are using the pre-built binaries on Linux:

```cpp
./avalanchego-<VERSION>-linux/avalanchego
```

Add `nohup` at the start of the command if you want to run the node in the background.

### Running the node as a service

If you're running the node as a service, you need to replace the old binaries with the new ones.

`cp -r avalanchego-<VERSION>-linux/* <DIRECTORY_WITH_OLD_BINARIES>`

and then restart the service with `sudo systemctl start avalanchego.service`.

## **Build from source**

First clone our Github repo \(you can skip this step if you’ve done this before\):

```text
git clone https://github.com/ava-labs/avalanchego.git
```

Then move to the avalanchego directory:

```text
cd avalanchego
```

Pull the latest code:

```text
git pull
```

Check that your local code is up to date. Do:

```文本
混帐REV-HEAD解析```


并检查打印出的前7个字符是否与我们[ Github。]（https://github.com/ava-labs/avalanchego）

现在构建二进制文件：

```text
./scripts/build.sh
```

这应该打印：

```文本
构建成功```


您可以通过执行以下操作来检查正在运行的版本：

```文本
./build/avalanchego --version ```


您可以使用以下命令运行节点：

```text
./build/avalanchego
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE5MDE0ODM1M119
-->