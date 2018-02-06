# python-ingest-docs
---

Install/update **pip** (to vsn >= 9.0.1) and **virtualenv**

- Make sure a recent version of `pip` is installed:
```
sudo apt-install get python3{,-dev} python-pip
sudo -H pip install --upgrade pip
hash -r  ##__ if necessary to pick up new pip binary in /usr/local/bin/
```
Install necessary packages for python code needs to make **MPI** calls.

```
sudo apt-get install libopenmpi-dev openmpi-bin
pip install mpi4py
```
Create the virtual python environment using the system `python3` binary 

`virtualenv -p python3 testenv3`

Install RQ, which will be used to monitor the worker queues

```


```
