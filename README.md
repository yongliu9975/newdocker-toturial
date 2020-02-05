# newdocker-toturial

## 1. 创建新docker

因为要进行端口映射，所以要新建一个docker：

```
sudo docker run -p 6666:6006 --name test  -it 镜像id
```

上述命令则是创建一个名为`test`，将`docker上6006`映射到`服务器上6666`的docker，具体将什么端口映射到什么端口可以按自己的需要确定

## 2. 启动http服务

进入新的docker之后，首先安装好conda环境等，然后运行以下命令安装`http-server`：

```
sudo apt-get install npm
sudo npm install http-server -g
```

启动http服务的命令如下：

```
http-server [path] [options]
```

假设现在想要查看当前文件夹下的文件，那么运行如下命令：

```
$ http-server ./ -p 6006
Starting up http-server, serving .
Available on:
  http://127.0.0.1:6006
  http://172.17.0.11:6006
Hit CTRL-C to stop the server
```

## 3. 将服务器的端口映射到本地

除了以上步骤，还要先将相应的端口映射到本地端口，这样才能在本地游览器上查看docker中的文件。在上面的例子中，我们将docker的6006端口映射到了服务器上的6666端口，接着我们还需要将服务器上的6666读那口映射到本地（假设映射到本地的16006端口），需要在登陆服务器的命令上加上一句`-L 16006:127.0.0.1:6666`，登陆命令如下：

```
ssh -L 16006:127.0.0.1:6666 username@113.209.128.210 -p 18822
```

登陆成功后，使用以下命令查看所有的docker，并找到刚刚创建的container ID：

```
sudo docker ps -a
```

然后进入到docker内（假设container ID为43555bfe9b6c）：

```
sudo docker exec -it 43555bfe9b6c /bin/bash
```

这时候再根据前面的命令启动你想要查看的路径的http服务就可以了，在本地游览器输入`127.0.0.1:16006`就可以看到好东西了

## 4. acc_monitor的使用

首先需要将checkpoint设置为200，即每两百步计算一次模型在train数据集上的acc，然后将`acc_monitor.py`放到对应的model文件夹下，然后设置test数据的路径（test数据设置成500较为合适）：

```python
# acc_monitor.py
# tgt is the reactants
TGT_FILE_PATH = '../../data/all_in_one/test_tgt.txt'
# src is the products
SRC_FILE_PATH = '../../data/all_in_one/test_src.txt'
```

然后设置用于计算acc的GPU的物理机序号，即设置device的值：

```python
opt, translator = load_model(file, BEAM_SIZE, TOPK, device=4, tokenizer='char')
```

