---
title: Scrapy框架安装
categories:
  - coding
  - python
tags:
  - web crawler
  - environment
date: 2018-01-18 14:28:22
update: 2018-01-18 14:28:22
---

### Scrapy安装
今天在Ubuntu上安装python的爬虫框架Scrapy，遇到了一些问题。

#### 失败案例
首先想用pip来安装Scrapy：
```
pip install Scrapy
```

<!--more-->

出现了如下错误：
```
 Complete output from command python setup.py egg_info:
    Couldn't find index page for 'incremental' (maybe misspelled?)
    No local packages or download links found for incremental>=16.10.1
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "/tmp/pip-build-wIgIGQ/Twisted/setup.py", line 21, in <module>
        setuptools.setup(**_setup["getSetupArgs"]())
      File "/usr/lib/python2.7/distutils/core.py", line 111, in setup
        _setup_distribution = dist = klass(attrs)
      File "build/bdist.linux-x86_64/egg/setuptools/dist.py", line 260, in __init__
      File "build/bdist.linux-x86_64/egg/setuptools/dist.py", line 284, in fetch_build_eggs
      File "build/bdist.linux-x86_64/egg/pkg_resources.py", line 563, in resolve
      File "build/bdist.linux-x86_64/egg/pkg_resources.py", line 799, in best_match
      File "build/bdist.linux-x86_64/egg/pkg_resources.py", line 811, in obtain
      File "build/bdist.linux-x86_64/egg/setuptools/dist.py", line 327, in fetch_build_egg
      File "build/bdist.linux-x86_64/egg/setuptools/command/easy_install.py", line 434, in easy_install
    
      File "build/bdist.linux-x86_64/egg/setuptools/package_index.py", line 475, in fetch_distribution
    AttributeError: 'NoneType' object has no attribute 'clone'
    
    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-wIgIGQ/Twisted/
```

google和百度了半天，都没有找到合适的解决方案：

参考了一个很像的[博客](https://www.cnblogs.com/hizf/p/7804711.html)，运行了`sudo -H pip install --upgrade setuptools`。

出现了如下的错误：
```
Traceback (most recent call last):
  File "/usr/lib/python2.7/dist-packages/pip/basecommand.py", line 209, in main
    status = self.run(options, args)
  File "/usr/lib/python2.7/dist-packages/pip/commands/install.py", line 328, in run
    wb.build(autobuilding=True)
  File "/usr/lib/python2.7/dist-packages/pip/wheel.py", line 748, in build
    self.requirement_set.prepare_files(self.finder)
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 360, in prepare_files
    ignore_dependencies=self.ignore_dependencies))
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 448, in _prepare_file
    req_to_install, finder)
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 397, in _check_skip_installed
    finder.find_requirement(req_to_install, self.upgrade)
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 442, in find_requirement
    all_candidates = self.find_all_candidates(req.name)
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 400, in find_all_candidates
    for page in self._get_pages(url_locations, project_name):
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 545, in _get_pages
    page = self._get_page(location)
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 648, in _get_page
    return HTMLPage.get_page(link, session=self.session)
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 757, in get_page
    "Cache-Control": "max-age=600",
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 480, in get
    return self.request('GET', url, **kwargs)
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 378, in request
    return super(PipSession, self).request(method, url, *args, **kwargs)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 468, in request
    resp = self.send(prep, **send_kwargs)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 576, in send
    r = adapter.send(request, **kwargs)
  File "/usr/share/python-wheels/CacheControl-0.11.5-py2.py3-none-any.whl/cachecontrol/adapter.py", line 46, in send
    resp = super(CacheControlAdapter, self).send(request, **kw)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/adapters.py", line 376, in send
    timeout=timeout
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/connectionpool.py", line 610, in urlopen
    _stacktrace=sys.exc_info()[2])
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/util/retry.py", line 228, in increment
    total -= 1
TypeError: unsupported operand type(s) for -=: 'Retry' and 'int'
```

**最终放弃**

#### 成功方案

参考[官方文档](https://doc.scrapy.org/en/latest/intro/install.html)的另一种安装方式：`conda`。

要使用`conda`，需要安装[`Anaconda`](https://docs.anaconda.com/anaconda/)或[`Miniconda`](https://conda.io/docs/user-guide/install/index.html)。
`Miniconda`是简化办的`Anaconda`。图方便安装了`Miniconda`。（安装方法：https://conda.io/docs/user-guide/install/linux.html ）

安装好`conda`之后，运行`conda install -c conda-forge scrapy`安装`Scrapy`。

**安装成功**:
```
$ scrapy
Scrapy 1.5.0 - no active project

Usage:
  scrapy <command> [options] [args]

Available commands:
  bench         Run quick benchmark test
  fetch         Fetch a URL using the Scrapy downloader
  genspider     Generate new spider using pre-defined templates
  runspider     Run a self-contained spider (without creating a project)
  settings      Get settings values
  shell         Interactive scraping console
  startproject  Create new project
  version       Print Scrapy version
  view          Open URL in browser, as seen by Scrapy

  [ more ]      More commands available when run from project directory

Use "scrapy <command> -h" to see more info about a command
```

## 文章
- [Installation guide](https://doc.scrapy.org/en/latest/intro/install.html)
- [Anaconda](https://docs.anaconda.com/anaconda/)
- [Miniconda](https://conda.io/docs/user-guide/install/index.html)
- [Conda: Installing on Linux](https://conda.io/docs/user-guide/install/linux.html)
- [ubuntu 16.04 安装 scrapy 一个花了我5个小时的bug最终解决](https://www.cnblogs.com/hizf/p/7804711.html)
- [使用conda管理python环境](https://zhuanlan.zhihu.com/p/22678445)