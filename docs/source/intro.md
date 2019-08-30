# 如何用Sphinx自动化项目文档

sphinx是一款简洁优雅的自动化文档工具，支持代码接口注释的自动化排版，markdown、jupyter notebook文件等，先看看上线后的最终效果： 

![](https://raw.githubusercontent.com/EvilPsyCHo/PicBed/master/img/20190830142945.png?token=AEJXRCTS3PHHWWQYVUXHVVK5NDBJ6)

## 安装Sphinx



```shell
pip install sphinx sphinx-autobuild sphinx_rtd_theme recommonmark
# 版本Sphinx 2.2.0
```

## DEMO项目案例

项目结构如下：

```shell
├── README.md
├── docs
└── sphinx_demo
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-36.pyc
    │   ├── say.cpython-36.pyc
    │   └── some_action.cpython-36.pyc
    ├── intro.md
    ├── jupyter_demo.ipynb
    ├── say.py
    ├── some_action.py
    └── tools
        ├── __init__.py
        ├── __pycache__
        │   ├── __init__.cpython-36.pyc
        │   ├── run.cpython-36.pyc
        │   └── sleep.cpython-36.pyc
        ├── run.py
        └── sleep.py
```

## 初始化Sphinx

```shell
$ cd docs 					
$ sphinx-quickstart 

Welcome to the Sphinx 2.2.0 quickstart utility.

Please enter values for the following settings (just press Enter to
accept a default value, if one is given in brackets).

Selected root path: .

You have two options for placing the build directory for Sphinx output.
Either, you use a directory "_build" within the root path, or you separate
"source" and "build" directories within the root path.
> Separate source and build directories (y/n) [n]: y

The project name will occur in several places in the built documentation.
> Project name: your_project_name
> Author name(s): your_name
> Project release []: 

If the documents are to be written in a language other than English,
you can select a language here by its language code. Sphinx will then
translate text that it generates into that language.

For a list of supported codes, see
https://www.sphinx-doc.org/en/master/usage/configuration.html#confval-language.
> Project language [en]: zh_cn

Creating file ./source/conf.py.
Creating file ./source/index.rst.
Creating file ./Makefile.
Creating file ./make.bat.

Finished: An initial directory structure has been created.
```

初始化完成后，在docs路径下会生成如下文件树：

```shell
.
├── Makefile
├── build
├── make.bat
└── source
    ├── _static
    ├── _templates
    ├── conf.py
    └── index.rst
```

## 生成项目模块文档的HTML文件

1. 修改conf.py文件，将项目import路径添加进来，并增加扩展项：

   ```python
   import os
   import sys
   package_path = os.path.abspath("./../../sphinx_demo/")
   sys.path.insert(0, package_path)   

   extensions = [
    'sphinx.ext.autodoc',
    'sphinx.ext.viewcode',
    'sphinx.ext.todo',]
   ```

   
2. 在docs下执行命令

   ```shell
   sphinx-apidoc -o ./source ../your_package_name/
   ```

3. 在./source/index.rst添加模块索引文件modules
   ```text
   Welcome to sphinx_demo's documentation!
   =======================================

   .. toctree::
      :maxdepth: 2
      :caption: Contents:

      modules   #这里不需要加文件后缀
   ```

4. 生成html文件
   ```shell
   make html		# 可以使用make clean清理之前生成的html文件
   ```

5. 这时，点击./docs/build/html下会生成的index.html文件，可以在浏览器预览到：

![](https://github.com/EvilPsyCHo/PicBed/blob/master/img/20190830142554.png?raw=true)

## 修改主题

修改conf.py文件：

```python																	# 注释这行
import sphinx_rtd_theme                												# 添加这行
# html_theme = 'alabaster'		 # 注释此行																# 注释这行
html_theme = "sphinx_rtd_theme"	 # 修改此行															# 添加这行
html_theme_path = [sphinx_rtd_theme.get_html_theme_path()]		# 添加这行
```

然后重新执行：

```shell
make clean
make html
```
打开./docs/build/html/index.html可以预览到：

![](https://raw.githubusercontent.com/EvilPsyCHo/PicBed/master/img/20190830100325.png?token=AEJXRCQKIXUMCYVMY36Q3H25NCCDI)

## Markdown支持

修改conf.py文件

```python
import recommonmark
from recommonmark.transform import AutoStructify
source_parsers = {
   '.md': 'recommonmark.parser.CommonMarkParser',
}
source_suffix = ['.rst', '.md']
```

## Jupyter Notebook支持

1. 安装nbsphinx

2. 在conf.py添加'nbsphinx'

   ```python
   extensions = [
       'sphinx.ext.autodoc',
       'sphinx.ext.viewcode',
       'sphinx.ext.todo',
       'nbsphinx'                 # Jupyter Notebook支持
   ]
   ```

3. 在index.rst中添加*.ipynb文件和

   ```reStructuredText
   Welcome to sphinx_demo's documentation!
   =======================================
   
   .. toctree::
      :maxdepth: 4                       # 如果发现展示层级不对，可以展示深度
      :caption: Contents:
   
      intro                              # 添加markdown介绍文件
      jupyter_demo.ipynb 				 # 添加Notebook文件
      modules                      
   ```
   
4. 效果

![](https://raw.githubusercontent.com/EvilPsyCHo/PicBed/master/img/20190830142945.png?token=AEJXRCTS3PHHWWQYVUXHVVK5NDBJ6)


5. 自动化托管文档到readthedocs