1. Install python virtualenv using (replace `apt` with your package manager)
`sudo apt install python3-venv`
2. Create virtualenv named _envname_ for python3 using
`python3 -m venv any/path/envname`
3. Enter to virtual env
`source any/path/envname/bin/activate`
4. Check python version using
`python -V`
5. Create a folder for script server and navigate to it
`mkdir -p any/path/script-server && cd any/path/script-server`
6. Download latest release
`wget https://github.com/bugy/script-server/releases/latest/download/script-server.zip`
7. Extract zip contents
`unzip script-server.zip` (If unzip command not found, install unzip with `sudo apt-get install unzip`)
8. Install requirements
`pip install -r requirements.txt`
9. Run the server
`python launcher.py`

**Bonus:**
If using virtualenv's python, then [systemd](Running-as-a-linux-service#systemd) startup script python path would be:
1. Find out virtualenv's python path (When virtualenv is activated)
`which python`
copy result
2. Edit `/lib/systemd/system/script-server.service`
`ExecStart=/usr/bin/python3` would be -> `paste here`
Example something like this:
`/home/user/script-server/envname/bin/python`

The similar change should be done for init.d

**Notes:**
If you're getting 

```
pip install -r requirements.txt
Collecting typing (from -r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/4a/bd/eee1157fc2d8514970b345d69cb9975dcd1e42cd7e61146ed841f6e68309/typing-3.6.6-py3-none-any.whl
Collecting tornado<6 (from -r requirements.txt (line 2))
  Downloading https://files.pythonhosted.org/packages/e6/78/6e7b5af12c12bdf38ca9bfe863fcaf53dc10430a312d0324e76c1e5ca426/tornado-5.1.1.tar.gz (516kB)
    100% |████████████████████████████████| 522kB 2.3MB/s 
Building wheels for collected packages: tornado
  Running setup.py bdist_wheel for tornado ... error
  Complete output from command /home/shinee/sites/script-server/env/bin/python3 -u -c "import setuptools, tokenize;__file__='/tmp/pip-build-9m5zqpq7/tornado/setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" bdist_wheel -d /tmp/tmp8sqzva6bpip-wheel- --python-tag cp36:
  usage: -c [global_opts] cmd1 [cmd1_opts] [cmd2 [cmd2_opts] ...]
     or: -c --help [cmd1 cmd2 ...]
     or: -c --help-commands
     or: -c cmd --help
  
  error: invalid command 'bdist_wheel'
  
  ----------------------------------------
  Failed building wheel for tornado
  Running setup.py clean for tornado
Failed to build tornado
Installing collected packages: typing, tornado
  Running setup.py install for tornado ... done
Successfully installed tornado-5.1.1 typing-3.6.6
```

error either update your pip version or install wheel package.

`pip install --upgrade pip` or `pip install -U wheel`