### 卸载

```bash
sudo apt-get autoremove mysql-server
sudo apt-get remove mysql-common (非常重要)
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

