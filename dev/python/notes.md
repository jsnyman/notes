
### pyenv
* Simple Python Version Management: [pyenv](https://github.com/pyenv/pyenv)

* [pyenv tutorial](https://amaral.northwestern.edu/resources/guides/pyenv-tutorial)


* pyenv does...
  - Let you change the global Python version on a per-user basis.
  - Provide support for per-project Python versions.
  - Allow you to override the Python version with an environment variable.
  - Search commands from multiple versions of Python at a time. This may be helpful to test across Python versions with tox.


### Virtualenv
* virtualenv is a tool to create isolated Python environments.


### pipenv
* [Pipenv: A Guide to the New Python Packaging Tool](https://realpython.com/pipenv-guide/)
* [Basic Usage of Pipenv](https://pipenv-fork.readthedocs.io/en/latest/basics.html)

* Pipenv is a packaging tool for Python that solves some common problems associated with the typical workflow using pip, virtualenv, and the good old requirements.txt.



* Create the pipenv environment
```
mkdir project
cd project
pipenv install                  # Creates the Pipfile if one does not exist. If requirements.txt exists it will use this.
```

* Install libraries
```
pipenv install Flask            # installs the latest version of Flask for this virtual environment
pipenv install Flask~=1.1.1     # installs Flask version 1.1.1 for this virtual environment  
```

* Run the project with the libraries installed in this virtual environment
```
pipenv shell                  # To activate this project's virtualenv, run pipenv shell.
python mypythonfile.py
```






 * Create a virtual environment if one doesn’t already exist.
```

```

* This will create/update your Pipfile.lock, which you’ll never need to (and are never meant to) edit manually. You should always use the generated file.
```
$ pipenv lock             
```

* Now, once you get your code and Pipfile.lock in your production environment, you should install the last successful environment recorded:
```
$ pipenv install --ignore-pipfile
```

* Now let’s say another developer wants to make some additions to your code. In this situation, they would get the code, including the Pipfile, and use this command:
```
$ pipenv install --dev
```

* This command will print out a tree-like structure showing your dependencies.
```
$ pipenv graph
```
