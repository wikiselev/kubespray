## Installing Jupyter notebook on a single instance

This instruction is based on https://github.com/jupyter/docker-stacks

1. Create an Ubuntu instance with Docker installed (xenial-isg-docker-c52f7acc02b0c11d41b174707e4c271b16f52996)

2. Separately prepare a password for user according to this instruction:
https://jupyter-notebook.readthedocs.io/en/stable/public_server.html#preparing-a-hashed-password

For that you will need a locally installed jupyter.

3. `mkdir username`

4. `vim username/install.sh`

```
# add your pip install <package> or conda install <package> here
# do not change the last line!
jupyter notebook --NotebookApp.password="sha1:98f440753bfe:5b41f67f42e6530343c2d5ccdb199b54a0b921b3"
```
5. `sudo chown 1000 username`

6. 
```
docker run --name jpt \
         -d \
          -p 80:8888 \
        --user 1000 --group-add users \
        -v /home/ubuntu/username:/home/jovyan \
 jupyter/scipy-notebook:2c80cf3537ca \
 start.sh bash /home/jovyan/install.sh
 ```



Useful for debugging:
```
docker logs jpt
docker exec -it jpt bash
```


## Instruction for user:


 If you want to install additional software, do it according to this instruction: https://jakevdp.github.io/blog/2017/12/05/installing-python-packages-from-jupyter/

Like that:
 
- Install a conda package in the current Jupyter kernel
```
import sys
!conda install --yes --prefix {sys.prefix} numpy
```
 
Or
 
- Install a pip package in the current Jupyter kernel
```
import sys
!{sys.executable} -m pip install numpy
```

Note that the notebook runs in a Docker container, so all the additionally installed packages will be lost on container restart (which should not happen often but must be bared in my mind). So, if you want this software to be available after container restart, after installing software from within jupyter notebook, add a usual `conda install <package>` or `pip install <package>` instruction to `install.sh` file in the root of your jupyter directory. This will only take effect on container restart.
 
All the data in the root directory of Jupyter notebook will persist.
 