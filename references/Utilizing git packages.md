# Utilizing Git Packages
Ran into this today trying to pull down data-science-utils package for the streamlit app.


Here are the steps you want to run through


## Clone and install the package
Use the command line to clone and install the package.  

Here’s the code you want to run in your terminal:
```bash
# clone the repo
git clone git@github.com:git/repo

# install as editbale pkg using -e 
## the ‘.’ is how you install the whole package
cd git/repo 
pip install -e .
```
Note that the install line needs the ‘e’ to install as editable, or you won’t be able to fix any errors you find when you test in an interactive python setting.  Instead, it will make a copy of the code, which is not what you want.

## Test in an interactive python session
Once you have the editable repo installed as a package, enter an interactive python session.  I like to use ipython.  (run a conda install if you need to for ipython first).


Create an import line to test that your package will import into your file as you expect.  If there are errors in the package setup, you will see them now.

```bash
# start and interactive python session
ipython
```
in iPython, add an import line like you would at the top of a file.  Note : the name of the package should be the folder name that is located at the same level as the setup.py file.
```python
import custom_python_pkg as cpp
#where 'custom_python_pkg' is your package name and 'cpp' is your abbreviation that you would use in the file
```
Your test is done.  

If no errors came up, you can use this import line in your py file without issue.  If you found errors, fix them with commits and send a pull request.


Here is a good format for that:

# Description of problem
Found some bugs when importing this package

```bash
# clone the repo
git clone git@github.com:company/git/repo
# install as editbale pkg
cd git-repo
pip install -e .
```
Then found the following line caused errors:

`import repo_package as rp`

* Error 1
```bash
ModuleNotFoundError: No module named 'repo_package.databases'
```

* Error 2
```bash
TabError: inconsistent use of tabs and spaces in indentation
```
Each commit corrects the errors.