## 从Oracle网站下载JDK最新版本（64位），然后执行以下命令：

```
sudo update-alternatives --install /usr/bin/java java /<path-to-jdk>/bin/java 100
sudo update-alternatives --install /usr/bin/javac javac /<path-to-jdk>/bin/javac 100
```

## 选择java解释器路径

```
sudo update-alternatives --config java
```