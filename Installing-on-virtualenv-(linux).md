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
`wget https://github.com/bugy/script-server/releases/download/1.12.0/script-server.zip`
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