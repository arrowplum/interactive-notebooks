# Interactive Notebooks
This repository contains jupyter notebooks, showing how Aerospike can be used in conjunction with Spark, 

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/aerospike-examples/interactive-notebooks/binder)

## Contents
- AerospikeSparkPython.ipynb : PySpark notebook
- AerospikeSparkScala.ipynb  : Spark scala notebook examples using spylon-kernel

## Setup Python Jupyter Notebook(Linux - CentOS)

yum installer used below - use dbpkg/rpm/other if your Linux distribution does not support yum

``` bash
sudo yum -y install gcc zlib-devel openssl-devel libffi-devel sqlite-devel bzip2-devel bzip2 xz-devel screen
```

Get your own local copy of Python 3.7 ( ignore if you have it already). Below we install to ~/.localpython

``` bash
PYTHON_VERSION=3.7.1
wget http://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz
tar zxvf Python-${PYTHON_VERSION}.tgz
cd Python-${PYTHON_VERSION}
mkdir ~/.localpython
./configure --prefix=$HOME/.localpython
make
make install
```

Set up a virtual Python environment - this is a sandbox which avoids you making system wide changes

``` bash
# Install virtualenv tool
~/.localpython/bin/pip3 install virtualenv
# Create on-disk representation of virtual environment at ~/spark-venv
~/.localpython/bin/virtualenv ~/spark-venv
# Activate virtual environment
source ~/spark-venv/bin/activate
```

Use of a virtual environment is indicated in the command line string - the name of the virtual environment - spark-env is added to the command line prompt - e.g. 

```
(spark-venv) [ec2-user@ip-10-0-0-248 Python-3.7.1]$ 
```

You can return to the system environment by typing ```deactivate``` and reactivate using ```source ~/spark-venv/bin/activate```

Get rid of annoying messages concerning pip upgrade

```
pip install --upgrade pip
```

Note at this point, all our Python related tooling is local to our virtual environment. So ```which pip``` will give

```
~/spark-venv/bin/pip
```

Install Spark and set ```$SPARK_HOME```

``` bash
SPARK_VERSION=2.4.0
HADOOP_VERSION=2.7
cd /tmp
wget https://downloads.apache.org/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz
tar xvfz spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz
sudo mv spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION} /opt/
export SPARK_HOME=/opt/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}
cd ~
```

Install required Python dependencies

```
pip install jupyter findspark numpy pandas matplotlib sklearn
```

Use of the Aerospike Spark Connector requires a valid feature key. The notebooks assume this is located at ```/etc/aerospike/features.conf```. Make sure your feature key is locally available, and if it is not located as above, modify the ```AS_FEATURE_KEY_PATH``` variable at the head of the notebook. You may need to run

``` bash
sudo mkdir /etc/aerospike
sudo chmod 777 /etc/aerospike
```

Make sure you have the interactive-notebooks repository locally

```
git clone git@github.com:aerospike-examples/interactive-notebooks
```

Finally start Jupyter. Change the IP in the string below - it can be localhost, but if you want to access from a remote host, choose the IP of one of your ethernet interfaces. You could replace with $(hostname -I | awk '{print $1}')

Note I set the notebook-dir to point to the directory containing the notebooks in this repository. 

```
jupyter notebook --no-browser --ip=<IP> --port=8888 --notebook-dir=~/interactive-notebooks/spark/
```

You will see output similar to

```
[I 09:36:52.202 NotebookApp] Writing notebook server cookie secret to /home/ec2-user/.local/share/jupyter/runtime/notebook_cookie_secret
[I 09:36:52.370 NotebookApp] Serving notebooks from local directory: /home/ec2-user/interactive-notebooks/spark
[I 09:36:52.370 NotebookApp] Jupyter Notebook 6.1.4 is running at:
[I 09:36:52.370 NotebookApp] http://10.0.0.248:8888/?token=5bf2910a2527567346323e0a4735e94136e1c70d392b561f
[I 09:36:52.370 NotebookApp]  or http://127.0.0.1:8888/?token=5bf2910a2527567346323e0a4735e94136e1c70d392b561f
[I 09:36:52.371 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 09:36:52.373 NotebookApp] 
```

You will need to use the URLs in the output to access jupyter - as the security token is expected.

You can omit this step by omitting the --no-browser flag - in that case jupyter will open a browser window local to itself, and request the Notebook app URL above.


You may wish to run the jupyter startup command from a [screen](https://linuxize.com/post/how-to-use-linux-screen/) so it will stay running if your session terminates. We installed screen at the outset to allow for this.