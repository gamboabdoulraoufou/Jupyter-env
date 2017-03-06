### Jupyter-env

#### 0 - Set-up package source
sudo apt-get update
sudo apt-get upgrade

#### 1 - Install python and all dependencies
```sh
# Download Anaconda
wget https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda2-4.0.0-Linux-x86_64.sh

# Install Anacondo
bash Anaconda2-4.0.0-Linux-x86_64.sh

# Add Anaconda path to paht
export PATH="/home/gamboabdoulraouf/anaconda2/bin:$PATH"

#close and reopen the terminal

  ```
  
#### 2- Install Jupyter 
##### 2-1- Install pip3 and other dependencies
```sh
sudo add-apt-repository ppa:fkrull/deadsnakes
sudo apt-get update
sudo apt-get install python3.5

wget https://bootstrap.pypa.io/get-pip.py

chmod 777 get-pip.py

sudo /usr/bin/python3.5 get-pip.py

sudo apt-get install npm nodejs-legacy
sudo npm install -g configurable-http-proxy

```

##### 2-2- Install JupyterHub and Jupyter for python 3 kernel
```sh
sudo /usr/bin/python3.5 -m pip install jupyterhub
sudo /usr/bin/python3.5 -m pip install "ipython[notebook]"
sudo apt-get -y install python-dev python-setuptools
sudo apt-get install python-pip python-dev build-essential
sudo pip install py4j
sudo pip install "ipython[notebook]"

```

#### 3- Configure Jupyter 
##### 3-1- Create Jupyter configuration file
```sh
jupyterhub --generate-config -f /home/gamboabdoulraouf/jupyterhub_config.py

```

##### 3-2- Create and add SSL certificat, so that your password is not sent unencrypted by your browser
```sh
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -nodes -days 365

```

##### 3-3- Create cookie secret
```sh
openssl rand -base64 2048 > /home/gamboabdoulraouf/cookie_secret
sudo chmod a-srwx /home/gamboabdoulraouf/cookie_secret

```

##### 3-4- Create auth token
```sh
openssl rand -hex 32 > /home/gamboabdoulraouf/proxi_auth_token

```

##### 3-5- Create auth token
```sh
sudo touch /var/log/jupyterhub.log

```

##### 3-6- Change Jupyter configuration
```sh
# Edit configuration file
/home/gamboabdoulraouf/jupyterhub_config.py

# Add the content below in configugation file
c = get_config()
# IP and Port
c.JupyterHub.ip = '10.19.0.0' # IP local
c.JupyterHub.port = 443
# Security - SSL
c.JupyterHub.ssl_key = '/home/gamboabdoulraouf/key.pem'
c.JupyterHub.ssl_cert = '/home/gamboabdoulraouf/cert.pem'
# Security - cookie secret
c.JupyterHub.cookie_secret_file ='/home/gamboabdoulraouf/cookie_secret'
c.JupyterHub.db_url = '/home/gamboabdoulraouf/jupyterhub.sqlite'
# Security - http token
c.JupyterHub.proxy_auth_token = '/home/gamboabdoulraouf/proxi_auth_token'
# put the log file in /var/log
c.JupyterHub.extra_log_file = '/var/log/jupyterhub.log'
# specify users and admin
c.Authenticator.whitelist = {'test'}
c.Authenticator.admin_users = {'test'}

```

#### 4- Setup Spark kernel
```sh
# Create folder for Spark Kernel
sudo mkdir -p /usr/local/share/jupyter/kernels/pyspark/

# Create and add configuration information json file
cat <<EOF | sudo tee /usr/local/share/jupyter/kernels/pyspark/kernel.json
{
 "display_name": "PySpark",
 "language": "python",
 "argv": [
  "/usr/bin/python",
  "-m",
  "IPython.kernel",
  "-f",
  "{connection_file}"
 ],
 "env": {
  "SPARK_HOME": "/home/hadoop/spark-install/",
  "PYTHONPATH": "/home/hadoop/spark-install/python/:/home/hadoop/spark-install/python/lib/py4j-0.8.2.1-src.zip",
  "PYTHONSTARTUP": "/home/hadoop/spark-install/python/pyspark/shell.py",
  "PYSPARK_SUBMIT_ARGS": "--master spark://spark-cluster-m:7077 --num-executors 1 --executor-memory 2G --total-executor-cores 1 pyspark-shell"
 }
}
EOF

```

#### 5- Setup Python 2.7 kernel
```sh
# Create folder for Python 2 Kernel
sudo mkdir -p /usr/local/share/jupyter/kernels/python2.7/

# Create and add configuration information json file
cat <<EOF | sudo tee /usr/local/share/jupyter/kernels/python2.7/kernel.json
{"display_name": "Python 2", 
"language": "python", 
"argv": 
  ["/home/gamboabdoulraouf/anaconda2/bin/python", 
  "-c", 
  "from ipykernel.kernelapp import main; main()", 
  "-f", 
  "{connection_file}" ]
}
EOF

```

#### 6- Setup R kernel
```sh
# Install R
conda install -c r r-essentials

# Create folder for R kernel
sudo mkdir -p /usr/local/share/jupyter/kernels/r/

# Create and add configuration information json file
cat <<EOF | sudo tee /usr/local/share/jupyter/kernels/r/kernel.json
{
  "argv": [
    "/home/gamboabdoulraouf/anaconda2/lib64/R/bin/R", 
    "--slave", 
	"-e", 
	"IRkernel::main()", 
	"--args", 
	"{connection_file}"],
  "display_name": "R",
  "language": "R"
}
EOF

```

#### 7- Run Jupyter
```sh
# Launch Jupyter server
sudo jupyterhub -f /home/gamboabdoulraouf/jupyterhub_config.py

# Or
nohup sudo jupyterhub -f /home/gamboabdoulraouf/jupyterhub_config.py &

```

__Go to https://IP or your.host.com and enjoy!__

