# python-ingest-docs


## Install libraries for `MPI` calls from python.

```
sudo apt-get install libopenmpi-dev openmpi-bin
```

---

Install/update **pip** and **virtualenv**

##  Make sure a recent version of `pip` is installed:

```
sudo apt-get install python{,3}-dev python-pip
sudo -H pip install --upgrade pip
```

(At this point, `pip --version` should probably be picking up the proper binary, ie from `/usr/local/...` and not `/usr`.  
You should see '*/local/*' somewhere in the reported library path, and pip should declare itself as version ***>= 9.0.1***.  If not, then reissue the version query after doing a `hash -r` (csh => `rehash`).

## Create a Python3 virtual environment

Install `virtualenv` via `pip` :
`sudo -H pip install virtualenv`
Then create the virtual environment under `$HOME`:
```
cd ; virtualenv -p python3 testenv3
```

## Install & run REDIS server

Follow the installation instructions for [REDIS](https://redis.io/download) and start up an instance of the  server in a separate terminal window.


## Install prerequisites for Ingest Tools

Now drop into the virtual python environment we created previously:
```
source ~/testenv3/bin/activate
```

Your prompt should (at least, if using `bash`) reflect you are in the virtual environment.  
While in the virtual environment, install some other needed prerequisites from irods related repositories:

* Install **mpi4py** : `pip install mpi4py`
* Install **RQ** , which will be used to monitor the worker queues: `pip install rq rq-dashboard `

From here on out, running `rq-dashboard &` and monitoring on port 9181 will give the status of all worker queues on:  
  `http://<ip>:9181`  
or just the queue named ***normal*** on:  
  `http://<ip>:9181/normal`  

Now install the python iRODS client library and development version of the ingest tools :

```
pip install git+https://github.com/irods/python-irodsclient
pip install git+https://github.com/irods-contrib/irods_tools_ingest@dev```
```

Now create a directory $HOME/pyingest and `cd` into it , clone the pwalk (parallelwalk) module:  
```
cd ; mkdir pyingest ; cd pyingest ; git clone https://github.com/irods/pwalk
cd pwalk
git checkout PY3 
```

It also helps to append these lines to your `.bashrc` :  
```
PYINGESTMODULES="$HOME/pyingest"
export PYTHONPATH+=${PYTHONPATH:+:}$PYINGESTMODULES/pwalk
```  
and then `exec bash` to make sure these are effective in your terminal window.  

***(We'll assume they are in effect for the next parts of this section.)***

## DEMO time ...

Either open 4 new windows, or spawn subwindows within this terminal window (***tmux*** is handy at this point ) in which your bash settings are active.



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
 
