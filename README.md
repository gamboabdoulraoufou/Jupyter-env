# Jupyter-env

# 0 - Set-up package source
sudo apt-get update
sudo apt-get upgrade

# 1 - Install python and all dependencies
wget https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda2-4.0.0-Linux-x86_64.sh
bash Anaconda2-4.0.0-Linux-x86_64.sh
export PATH="/home/gamboabdoulraouf/anaconda2/bin:$PATH"

#sudo apt-get install build-essential libxml2-dev libssl-dev libffi-dev python-dev libxslt-dev
#close and reopen the terminal

# A-Ipython notebook
# 3 - On install Ipython
sudo apt-get install python-pip
sudo pip install "jupyter[notebook]"

# Prepare a hashed password
$ python
>>> from IPython.lib import passwd
passwd()
# Enter password:
# Verify password:
# You get something like this: 'sha1:3820a75317b6:dca2a273323078fcc532094784e3d6a23b5ec8f7'
>>> # Ctrl + D to leave python

# Create and add SSL certificat, so that your password is not sent unencrypted by your browser
$ openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
$ jupyter notebook --certfile=mycert.pem


# Create a profil to using ipython
$ ipython profile create abdoul

$ nano /home/gamboabdoulraouf/.jupyter/jupyter_notebook_config.py

# Add the following code in ipython_notebook_config.py
c = get_config()

# Kernel config
c.IPKernelApp.pylab = 'inline'  # if you want plotting support always

# Notebook config
c.NotebookApp.certfile = u'/home/abdoul/mycert.pem'
c.NotebookApp.ip = '*'
c.NotebookApp.open_browser = False
c.NotebookApp.password = u'sha1:3c82442d66a4:ecfe4df7266af02286c2907801da2acab07346bd'
# It is a good idea to put it on a known, fixed port
c.NotebookApp.port = 9999

jupyter notebook

# You can start ipython on your browser
https://IP or your.host.com:9999


# B- Jupyter 
# Install pip3 and other dependencies
sudo apt-get -y install python3-pip npm nodejs-legacy
sudo npm install -g configurable-http-proxy

# Install JupyterHub and Jupyter for python 3 kernel
sudo pip3 install jupyterhub
sudo pip3 install "ipython[notebook]"
sudo apt-get -y install python-dev python-setuptools
sudo apt-get install python-pip python-dev build-essential
sudo pip install py4j
sudo pip install "ipython[notebook]"

# create configuration file 
#jupyterhub --generate-config.py
jupyterhub --generate-config -f /home/gamboabdoulraouf/jupyterhub_config.py

# Create and add SSL certificat, so that your password is not sent unencrypted by your browser
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -nodes -days 365

# create cookie secret
openssl rand -base64 2048 > /home/gamboabdoulraouf/cookie_secret
sudo chmod a-srwx /home/gamboabdoulraouf/cookie_secret

# create auth token
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


# Setup Spark kernel
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

# Setup Python 2.7 kernel
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


# Setup R kernel
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


# Run Jupyter
sudo jupyterhub -f /home/gamboabdoulraouf/jupyterhub_config.py
nohup sudo jupyterhub -f /home/gamboabdoulraouf/jupyterhub_config.py &


# Go to https://IP or your.host.com


4] Create user home directory in HDFS
The next step is to create a directory structure in HDFS for the new user.
For that from the admin user, create a directory structure.

$ hadoop dfs –mkdir /user/username/

[5] Change the ownership of user home directory in HDFS
The ownership of the newly created directory structure is with superuser.With this new user will not be able to run mapreduce programs. So change the ownership of newly created directory in HDFS to the new user.

$ hadoop dfs –chown –R username:groupname /user/test

echo "coucou" > fichier.txt
hadoop fs -copyFromLocal fichier.txt /user/test/

text_file = sc.textFile("hdfs:///user/test/fichier.txt")
counts = text_file.flatMap(lambda line: line.split(" ")) \
             .map(lambda word: (word, 1)) \
             .reduceByKey(lambda a, b: a + b)
counts.saveAsTextFile("hdfs://results.txt")
