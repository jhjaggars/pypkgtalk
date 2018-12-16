<!-- $theme: gaia -->

# Python Packaging: A Brief Survey

---

# Scope

How to get your code installed on a computer for _development_ first and operation second.

This presentation is *not* comprehensive.

---

# Options

1. System-wide install
2. User install
3. Virtual Environments
4. Containers (Sneaky!)

---

### But wait! I need an installer!

#### Yes, just use pip.

It's already installed in python 2.7.9+ and 3.4+

```shell
$ python -m pip
```
If you _don't_ have it you should get it

```shell
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
$ python get-pip.py
```

---

### Windows

If you don't have a pip binary handy you can run pip as a module.

```shell
$ python -m pip
```

For the rest of this presentation, if you see `pip` replace it with `python -m pip`.

---

### System-wide Install

```shell
$ pip install <package>
```

* requires superuser privileges
* installs directly into system path
* affects all users of the system

---

### User install

```shell
$ pip install --user <package>
```

* installs into a user-specific location
* handy for installing tools you want to use on multiple projects
* [pipsi](https://github.com/mitsuhiko/pipsi) is even better for that though.

---

# Virtual Environments

#### A way to isolate installed packages

---

# Why Virtual Environments?

1. Multiple versions of the same package installed on a single system
2. Avoid user and system-installed packages
3. Discrete list of dependencies
4. Repeatable Build

---

### virtualenv

```shell
$ virtualenv [options] <path>
```

```shell
$ virtualenv .
Using base prefix '/Users/jjaggars/.pyenv/versions/3.7.1'
New python executable in /Users/jjaggars/talks/pypkg/virtualenv_example/bin/python3.7
Also creating executable in /Users/jjaggars/talks/pypkg/virtualenv_example/bin/python
Installing setuptools, pip, wheel...
done.
$ ls
bin     include lib
$ source bin/activate
```

---

### venv

```shell
$ python -m venv <path>
```

```shell
$ python -m venv .
$ ls
bin        include    lib        pyvenv.cfg
$ source bin/activate
```

---

### pipenv

```shell
$ pipenv install
Creating a virtualenv for this projectÃ¢â‚¬Â¦
Pipfile: /Users/jjaggars/talks/pypkg/pipenv_example/Pipfile
... [snip] ...
$ ls
Pipfile      Pipfile.lock
```

---

### poetry

The recommended install is kind of weird...
```shell
$ curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python
```
You can install it via pip too.

```shell
$ pip install --user poetry
```

Using poetry is simple though.

```shell
$ poetry new .
Created package poetry_example in .
$ ls
README.rst     poetry_example pyproject.toml tests
```

---

### conda

The [installer](https://www.anaconda.com/download/) is huge.  

```shell
$ conda create --name conda_example
Solving environment: done
## Package Plan ##
  environment location: /usr/local/miniconda3/envs/conda_example
Proceed ([y]/n)? y
... [snip] ...
$ . /usr/local/miniconda3/etc/profile.d/conda.sh
$ conda activate conda_example
```

---

# Dependencies

---

###### pip

```shell
$ pip install <dep>
```

###### pipenv

```shell
$ pipenv install <dep>
```

###### poetry

```shell
$ poetry add <dep>
```

###### conda

```shell
$ conda install <dep>
```

---

#### Files	
* pip
	- setup.py
	- requirements.txt
* pipenv
	- Pipfile
* poetry
	- pyproject.toml

---

### setup.py

```
from setuptools import find_packages, setup

setup(
    name="example",
    version="1.0.0",
    description="a very small example",
    author="jhjaggars",
    packages=find_packages(),
    install_requires=['dep', 'dep2'],
)
```
Many (many) more options are available for setup.py.
Kenneth Reitz has a [good guide](https://github.com/kennethreitz/setup.py)

---

### requirements.txt

```shell
$ pip install requests
Collecting requests
... [snip] ...
$ pip freeze > requirements.txt
$ cat requirements.txt
certifi==2018.11.29
chardet==3.0.4
idna==2.8
requests==2.21.0
urllib3==1.24.1
```

```shell
$ pip install -r requirements.txt
```

---

### Pipfile

```shell
$ pipenv install requests
```

```toml
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]
requests = "*"

[requires]
python_version = "3.7"

```

---

### Pipfile.lock
Contains hashes of the installed dependencies.
```json
{
    "_meta": {},
    "default": {
        "certifi": {
            "hashes": [
                "sha256:47f9c83ef4c0c621eaef743f133f09fa8a74a9b75f037e8624f83bd1b6626cb7",
                "sha256:993f830721089fef441cdfeb4b2c8c9df86f0c63239f06bd025a76a7daddb033"
            ],
            "version": "==2018.11.29"
        },   
    },
    "develop": {}
}
```

---

### pyproject.toml

```toml
[tool.poetry]
name = "poetry_example"
version = "0.1.0"
description = ""
authors = ["Jesse Jaggars <jhjaggars@gmail.com>"]

[tool.poetry.dependencies]
python = "^2.7"
requests = "^2.21"

[tool.poetry.dev-dependencies]
pytest = "^3.0"

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"
```

---

### poetry.lock
Serves the same purpose as Pipfile.lock
```toml
[[package]]
category = "dev"
description = "Atomic file writes."
name = "atomicwrites"
optional = false
python-versions = ">=2.7, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3.*"
version = "1.2.1"

[metadata.hashes]
atomicwrites = ["0312ad34fcad8fac3704d441f7b317e50af620823353ec657a53e981f92920c0", "ec9ae8adaae229e4f8446952d204a3e4b5fdd2d099f9be3aaf556120135fb3ee"]
```
---

# What do I do?

---

# I just use pip+venv or pipenv.
###### I'm investigating poetry, hatch, and flit though.

---

### pip + venv is simple for library development

You can create multiple virtual environments for the same project easily.

```shell
$ python -m venv 3.7.1
$ pyenv local 3.6.5
$ python -m venv 3.6.5
$ ls
3.6.5 3.7.1
```
When working on a particular version just activate the proper venv.

---

#### pipenv is nice for application development

```shell
$ pipenv shell
$ pipenv open <module>
$ pipenv check
```

You can use multiple virtual environments with pipenv, but you still only have one Pipfile and Pipfile.lock

---

### poetry

Poetry is nice for application development too.

Poetry makes publishing to pypi really easy.

```shell
$ poetry publish --build
```

Builds source and wheel distributions and pushes them to PyPI.

---

### Development-only Dependencies

Pip doesn't keep up with the lifecycle of dependencies
```shell
$ pip install -r development.txt
```

Pipenv and poetry do

```shell
$ pipenv install --dev <dep>
```

```shell
$ poetry add -D <dep>
```

---

### distutils and setuptools

You can specify _extras_ in your `setup.py` as a way of managing the lifecycle of your dependencies.  This is handy for complex setups.
```python
setup(
    extras_require={
        "develop": [
            "pytest>=3.0.0",
        ]
    }
)
```

```shell
$ pip install .[develop]
```

---

### Multiple *local* projects

If you have a distutils/setuptools project you can install it into a virtual environment with pip.

```shell
$ pip install -e <local/path>
```
The `-e` argument makes a link rather than copying the dependency in.  This is very useful if you want to develop on the dependency.

This works with pipenv too.
```shell
$ pipenv install -e <local/path>
```

---

# Questions

---

# Thank You

* [Slides](https://gitpitch.com/jhjaggars/pypkgtalk)
* [Hitchhiker's Guide to Packaging](https://the-hitchhikers-guide-to-packaging.readthedocs.io/en/latest/)
* [Pipenv](https://pipenv.readthedocs.io/en/latest/)
* [Poetry](http://poetry.eustace.io/)
* [Flit](https://flit.readthedocs.io/en/latest/)
* [Hatch](https://github.com/ofek/hatch)
* [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [setuptools](https://setuptools.readthedocs.io/en/latest/)
