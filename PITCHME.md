<!-- $theme: gaia -->

# Python Packaging: A Survey

---

# Scope

How to get your code installed on a computer for _development_ first and operation second.

---

# Options

1. System-wide install
2. User install
3. Virtual Environments
4. Containers (Sneaky!)

---

# But wait! I need an installer!

## Yes, just use pip.

It's already installed in python 2.7.9+ and 3.4+
If you _don't_ have it you should get it

```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

---

# Windows

If you don't have a pip binary handy you can run pip as a module.

```
python -m pip
```

For the rest of this presentation, if you see `pip` replace it with `python -m pip`.

---

# System-wide Install

```
pip install <package>
```

* requires superuser privileges
* installs directly into system path
* affects all users of the system

---

# User install

```
pip install -U <package>
```

* installs into a user-specific location
* handy for installing tools you want to use on multiple projects

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

# Virtualenv

```virtualenv [options] <path>```

```
~/t/p/virtualenv_example $ virtualenv .
Using base prefix '/Users/jjaggars/.pyenv/versions/3.7.1'
New python executable in /Users/jjaggars/talks/pypkg/virtualenv_example/bin/python3.7
Also creating executable in /Users/jjaggars/talks/pypkg/virtualenv_example/bin/python
Installing setuptools, pip, wheel...
done.
~/t/p/virtualenv_example $ ls
bin     include lib
~/t/p/virtualenv_example $ . bin/activate.fish
(virtualenv_example) ~/t/p/virtualenv_example $
```

---

# venv

```python -m venv <path>```

```
~/t/p/venv_example $ python -m venv .
~/t/p/venv_example $ ls
bin        include    lib        pyvenv.cfg
~/t/p/venv_example $ . bin/activate.fish
(venv_example) ~/t/p/venv_example $
```

---

# pipenv

```
~/t/p/pipenv_example $ pipenv install
Creating a virtualenv for this project…
Pipfile: /Users/jjaggars/talks/pypkg/pipenv_example/Pipfile
... [snip] ...
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
~/t/p/pipenv_example $ ls
Pipfile      Pipfile.lock
```

---

# poetry

The recommended install is kind of weird...
```
curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python
```
You can install it via pip too.

```
pip install --user poetry
```

Using poetry is simple though.
```
~/t/p/poetry_example $ poetry new .
Created package poetry_example in .
~/t/p/poetry_example $ ls
README.rst     poetry_example pyproject.toml tests
```

---

# conda

The [installer](https://www.anaconda.com/download/) is huge.  

```
jhj:conda_example jjaggars$ conda create --name conda_example
Solving environment: done
## Package Plan ##
  environment location: /usr/local/miniconda3/envs/conda_example
Proceed ([y]/n)? y
... [snip] ...
jhj:conda_example jjaggars$ . /usr/local/miniconda3/etc/profile.d/conda.sh
jhj:conda_example jjaggars$ conda activate conda_example
(conda_example) jhj:conda_example jjaggars$
```

---

# Dependencies

---

#### pip

```
pip install <dep>
```

#### pipenv

```
pipenv install <dep>
```

#### poetry

```
poetry add <dep>
```

#### conda

```
conda install <dep>
```

---

# Files	
* pip
	- setup.py
	- requirements.txt
* pipenv
	- Pipfile
* poetry
	- pyproject.toml

---

# setup.py

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

# requirements.txt

```
(venv_example) ~/t/p/venv_example $ pip install requests
Collecting requests
... [snip] ...
(venv_example) ~/t/p/venv_example $ pip freeze > requirements.txt
(venv_example) ~/t/p/venv_example $ cat requirements.txt
certifi==2018.11.29
chardet==3.0.4
idna==2.8
requests==2.21.0
urllib3==1.24.1
```

```
pip install -r requirements.txt
```

---

# Pipfile

```
~/t/p/pipenv_example $ pipenv install requests
```
```toml
~/t/p/pipenv_example $ cat Pipfile
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

# Pipfile.lock
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

# pyproject.toml

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

# poetry.lock
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

