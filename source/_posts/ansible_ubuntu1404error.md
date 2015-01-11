title: Ubuntu 14.04 server 安装 ansible 抛出异常
date: 2014-05-09 15:50
categories: Ansible
comments: true
tags: 
        - 运维自动化
---

一.抛出log信息
```python
Command /usr/bin/python -c "import setuptools, tokenize;__file__='/tmp/pip_build_root/pycrypto/setup.py';exec(compile(getattr(tokenize, 'open', open)(__file__).read().replace('\r\n', '\n'), __file__, 'exec'))" install --record /tmp/pip-Ii3Ksu-record/install-record.txt --single-version-externally-managed --compile failed with error code 1 in /tmp/pip_build_root/pycrypto
Storing debug log for failure in /root/.pip/pip.log
```
解决办法:
   安装这个软件包即可:
	`apt-get install python-dev`

