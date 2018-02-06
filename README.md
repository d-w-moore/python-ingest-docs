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
```
cd ; virtualenv -p python3 testenv3
```

Follow the installation instructions for [REDIS](https://redis.io/download) and start up an instance of the  server in a separate terminal window.

Now drop into the virtual python environment we created previously:
```
source ~/testenv3/bin/activate
```

Your prompt should (at least, if using `bash`) reflect you are in the virtual environment.  
While in the virtual environment, install some other needed prerequisites from irods related repositories:


Install RQ, which will be used to monitor the worker queues

```
pip install rq rq-dashboard 
```

From here on out, running `rq-dashboard &` and monitoring on port 9181 will give the status of all worker queues on:  
  `http://<ip>:9181/normal`  
or just the queue named ***normal*** on:  
  `http://<ip>:9181/normal`  

Now create a directory $HOME/pyingest and `cd` into it , clone the pwalk (parallelwalk) module and the development version of the ingest tools :

```
cd ; mkdir pyingest
pip install git+https://github.com/irods/python-irodsclient
pip install git+https://github.com/irods-contrib/irods_tools_ingest@dev
```

It might also help to append these lines to your `.bashrc` :  
```
PYINGESTMODULES="$HOME/pyingest"
export PYTHONPATH+=${PYTHONPATH:+:}$PYINGESTMODULES/pwalk:$PYINGESTMODULES/irods_tools_ingest

```



---


 ```
 # __each time__

For register:
mpiexec -n 5 python ./enqueue_reg_jobs.py /projects/irods/ingest_test/5000_files/ /projects/irods/ingest_test /tempZone/home/rods/test2

For put (ingest):
irodsqueue ingest -f --timer /PATH/TO/LOCAL/DIR

PYINGESTMODULES="$HOME/pyingest"
export PYTHONPATH+=${PYTHONPATH:+:}$PYINGESTMODULES/pwalk:$PYINGESTMODULES/irods_tools_ingest

# Launch workers
#  high normal low - are queue names

for i in {1..16}; do sleep .1; do
  rq worker -v --burst -w irodsqueue.irodsworker.IrodsWorker high normal low & 
done 

mpiexec -n 5 python ./enqueue_reg_jobs.py   /data/irods/ /data /tempZone/home/rods/test

 ```
 
