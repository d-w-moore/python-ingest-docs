# python-ingest-docs

---

Install/update **pip** and **virtualenv**


##  Make sure a recent version of `pip` is installed:

```
sudo apt-get install python{,3}-dev python-pip
sudo -H pip install --upgrade pip
```

(At this point, `pip --version` should probably be picking up the proper binary, ie from `/usr/local/...` and not `/usr`.  
You should see '*/local/*' somewhere in the reported library path, and pip should declare itself as version ***>= 9.0.1***.  If not, then reissue the version query after doing a `hash -r` (csh => `rehash`).

Install `virtualenv` via `pip` :
`sudo -H pip install virtualenv`
Then create the virtual environment under `$HOME`:
```cd ; virtualenv -p python3 testenv3
```

Follow the installation instructions for [REDIS](https://redis.io/download) and start up an instance of the  server in a separate terminal window.
Drop into the virtual python environment we created previously:
```
source ~/testenv3/bin/activate
```

Your prompt should (at least, if using `bash`) reflect you are in the virtual environment.  
While in the virtual environment, install some other needed prerequisites from irods related repositories:

```
pip install git+https://github.com/irods/python-irodsclient
pip install git+https://github.com/irods-contrib/irods_tools_ingest@dev
```

Install RQ, which will be used to monitor the worker queues

```
pip install rq rq-dashboard 
```
