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
  
#### 2- Jupyter configuration 
##### 2-1- Install pip3 and other dependencies
```sh
sudo apt-get -y install python3-pip npm nodejs-legacy
sudo npm install -g configurable-http-proxy

```

##### 2-2- Install JupyterHub and Jupyter for python 3 kernel
```sh
sudo pip3 install jupyterhub
sudo pip3 install "ipython[notebook]"
sudo apt-get -y install python-dev python-setuptools
sudo apt-get install python-pip python-dev build-essential
sudo pip install py4j
sudo pip install "ipython[notebook]"

```

##### 2-3- create configuration file 
```sh
jupyterhub --generate-config -f /home/gamboabdoulraouf/jupyterhub_config.py
```

##### 2-3- Create and add SSL certificat, so that your password is not sent unencrypted by your browser
```sh
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -nodes -days 365

```

##### 2-4- create cookie secret
```sh
openssl rand -base64 2048 > /home/gamboabdoulraouf/cookie_secret
sudo chmod a-srwx /home/gamboabdoulraouf/cookie_secret

```

##### 2-5- create auth token
```sh
openssl rand -hex 32 > /home/gamboabdoulraouf/proxi_auth_token

sudo touch /var/log/jupyterhub.log

"""
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
"""

```

##### 2-6- Setup Spark kernel
sudo mkdir -p /usr/local/share/jupyter/kernels/pyspark/
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

##### 2-7- Setup Python 2.7 kernel
```sh
sudo mkdir -p /usr/local/share/jupyter/kernels/python2.7/

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

#### 2-8- Setup R kernel
```sh
conda install -c r r-essentials

sudo mkdir -p /usr/local/share/jupyter/kernels/r/
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

##### 2-9- Run Jupyter
```sh
sudo jupyterhub -f /home/gamboabdoulraouf/jupyterhub_config.py

nohup sudo jupyterhub -f /home/gamboabdoulraouf/jupyterhub_config.py &

```

# Go to https://IP or your.host.com and enjoy!

