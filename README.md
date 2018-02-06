# python-ingest-docs
---

Install/update **pip** and **virtualenv**

##  Make sure a recent version of `pip` is installed:
```
sudo apt-get install python{,3}-dev python-pip
sudo -H pip install --upgrade pip
```

(At this point, `pip --version` should probably be picking up the proper binary, ie from `/usr/local/...` and not `/usr`.  
You should see '/local/' somewhere in the reported library path, and pip should declare itself as version ***>= 9.0.1***.  If not, then reissue the version query after doing a `hash -r` (csh => `rehash`).

## Install necessary packages for python code needs to make **MPI** calls.

sudo apt-get install libopenmpi-dev openmpi-bin
pip install mpi4py
```
Create the virtual python environment using the system `python3` binary 

`virtualenv -p python3 testenv3`

Install RQ, which will be used to monitor the worker queues

```
pip install rq-dashboard

```
