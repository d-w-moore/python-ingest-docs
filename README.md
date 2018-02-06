# python-ingest-docs

## Install libraries for `MPI` calls from python.

```
sudo apt-get install libopenmpi-dev openmpi-bin
```

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

Follow the installation instructions for [REDIS](https://redis.io/download) and start up an instance of the  server in a separate terminal window.  Redis will need to be running for the queue and worker objects to be registered and visualized.


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

---

## DEMO time ...

Either open new windows for the following, or spawn subwindows within this terminal window (with `tmux` or similar) in which your new `bash` settings are active.

Following are the commands to issue in various windows for the different components of the demo.

A persistent connection to an iRODS server to register and ingest the files will be necessary , therefore make sure the appropriate **irods-client-icommands** package is already installed via the [iRODS download page](http://irods.org/download) and `iinit` has been run to initiate the server connection. 


 ```
# For register (must be inside the python3 test environment):
PATHTOSCRIPT=$(dirname $([ -n "$VIRTUAL_ENV" ] && find "$VIRTUAL_ENV" -name 'enqu*.py' -type f 2>/dev/null||echo .))
# __ if we're not in a virtual environment, the next command only works if the enqueue_reg_jobs.py script is
#    located in the current working directory :
mpiexec -n 5 python "$PATHTOSCRIPT/enqueue_reg_jobs.py" /projects/irods/ingest_test/5000_files/ /projects/irods/ingest_test /tempZone/home/rods/test2

# pattern is:
# mpiexec -n 5 python [src_physical_path]  [leading_path_mask]  [dest_irods_path]


For put (ingest):
irodsqueue ingest -f --timer /PATH/TO/LOCAL/DIR

# Launch workers
#  high normal low - are queue names

for i in {1..16}; do sleep .1; do
  rq worker -v --burst -w irodsqueue.irodsworker.IrodsWorker high normal low & 
done
 ```
 
