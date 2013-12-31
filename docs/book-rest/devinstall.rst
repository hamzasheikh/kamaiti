Install Kamaiti Development Environment
=======================================


These instructions have been created and tested using a CentOS 6.4 server. The general idea for the installation remains the same for other Linux distributions; package name and/or package managers may change.

The section for configuring Apache is very much related to CentOS. Other distributions generally don't follow the same process. If you're installing Kamaiti on another distribution, make sure you follow its procedures for configuring Apache.

It's assumed that you are fairly experienced in installing and configuring packages and know your way around a Linux distribution.



Install Local Python
--------------------


You will need to install local Python. The reason is that Kamaiti is built using Python 3.3 and it's not available on CentOS 6.4. There are four major steps in installing your own Python.

* Install Development Tools

* Download Python Source Code

* Compile and Install Python

* Modify $PATH

Install Development Tools
^^^^^^^^^^^^^^^^^^^^^^^^^


You will need some tools to compile Python. The easiest way to obtain them in CentOS is to install the ‘Development Tools’ group.


.. code-block:: text

    kmtuser@server:~$ su -
    root@server:~$ yum groupinstall "Development Tools"

You also need some libraries that Python uses to provide “batteries included” support.

.. code-block:: text

    root@server:~$ yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel


Download Python Source Code
^^^^^^^^^^^^^^^^^^^^^^^^^^^


Head over to Python.org and download version 3.3.2, used in this version of Kamaiti.


.. code-block:: text

    kmtuser@server:~$ mkdir ~/localpython


.. code-block:: text

    kmtuser@server:~$ cd ~/localpython


.. code-block:: text

    kmtuser@server:~/localpython$ curl -O http://python.org/ftp/python/3.3.2/Python-3.3.2.tar.bz2

Now just un-tar the file so you can use its contents.


.. code-block:: text

    kmtuser@server:~/localpython$ tar xvf Python-3.3.2.tar.bz2

If you do an *ls* of the directory,

.. code-block:: text

        kmtuser@server:~/localpython$ ls

you should see these two items in the localpython directory.

        Python-3.3.2  Python-3.3.2.tar.bz2


Compile and Install Python
^^^^^^^^^^^^^^^^^^^^^^^^^^^


You now have Python source and tools to compile it. Change directory to the un-tar’ed source code.


.. code-block:: text

    kmtuser@server:~/localpython$ cd Python-3.3.2

Run configure.


.. code-block:: text

    kmtuser@server:~/localpython/Python-3.3.2$ ./configure

Create a directory where you will install your local Python.


.. code-block:: text

    kmtuser@server:~/localpython/Python-3.3.2$ mkdir -p ~/usr/local

Compile and install Python to the directory you just created.


.. code-block:: text

    kmtuser@server:~/localpython/Python-3.3.2$ make altinstall prefix=$HOME/usr/local exec-prefix=$HOME/usr/local


Modify $PATH
^^^^^^^^^^^^


You now want to add ~/usr/local/bin to your $PATH so you don’t have to enter the full path to the Python binary every time you want to use it. Change directory to where local Python binary is located.


.. code-block:: text

    kmtuser@server:~/localpython/Python-3.3.2$ cd ~/usr/local/bin

Create an alias so you can refer to it as python3. This is optional but I like to do it because I can then use python to refer to the Python that came with CentOS and python3 to refer to my locally installed Python3.


.. code-block:: text

    kmtuser@server:~/usr/local/bin$ ln -s python3.3 python3

Now add this path to your $PATH environment variable. Notice that we add the new path before existing $PATH. This is so that our local Python is always used before any other.


.. code-block:: text

    kmtuser@server:~/usr/local/bin$ echo "export PATH=\$HOME/usr/local/bin:\$PATH" >> ~/.bashrc

Activate the new, modifed $PATH.


.. code-block:: text

    kmtuser@server:~/usr/local/bin$ source ~/.bashrc

Another thing to consider is virtual environments, virtualenv or venv (in Python 3). Since Python3 comes with its built-in venv, just create an alias for its binary as well.


.. code-block:: text

    kmtuser@server:~/usr/local/bin$ ln -s pyvenv-3.3 pyvenv

There’s one more modification you need to make to get pyvenv to work. Open the file and change the path to your locally installed Python.


.. code-block:: text

    kmtuser@server:~/usr/local/bin$ vim pyvenv-3.3

Original line is:

 #!/usr/local/bin/python3.3

After changing it should look something like:

    #!/home/kmtuser/usr/local/bin/python3.3

You can now use this local pyvenv to create your virtualenvs.

Create Virtual Environment
----------------------------

Python 3 comes with its own virtualenv built in, and it’s called venv. Run the following command to create your virtualenv.


.. code-block:: text

    kmtuser@server:~/usr/local/bin$ pyvenv ~/kmtvenv

If you do an ls you’ll notice that the python you used to create this virtualenv is copied into your virtualenv. Thus your virtualenv will continue to use its local copy. You now need to “activate” this virtualenv.


.. code-block:: text

    kmtuser@server:~/usr/local/bin$ source ~/kmtvenv/bin/activate

Sidebar: At some point you’ll want to exit this virtualenv. Run the ``deactivate`` command.

Next step is to install setuptools. At one point Distribute was set to replace/supersede setuptools but that’s not the case anymore. Don’t worry, we’ll still use pip but to first get pip we need setuptools.


.. code-block:: text

    kmtuser@server:~/usr/local/bin$ cd ~/kmtvenv


.. code-block:: text

    kmtuser@server:~/kmtvenv$ mkdir pypioffline


.. code-block:: text

    kmtuser@server:~/kmtvenv$ cd pypioffline

The latest version of setup tools at the time of writing was 1.1.6 so we’ll download it to the temporary/optional directory pypioffline.


.. code-block:: text

    kmtuser@server:~/kmtvenv/pypioffline$ curl -O https://pypi.python.org/packages/source/s/setuptools/setuptools-1.1.6.tar.gz


.. code-block:: text

    kmtuser@server:~/kmtvenv/pypioffline$ tar xvzf setuptools-1.1.6.tar.gz


.. code-block:: text

    kmtuser@server:~/kmtvenv/pypioffline$ cd setuptools-1.1.6


.. code-block:: text

    kmtuser@server:~/kmtvenv/pypioffline/setuptools-1.1.6$ python ez_setup.py


.. code-block:: text

    kmtuser@server:~/kmtvenv/pypioffline/setuptools-1.1.6$ easy_install pip

You have installed pip and can now start using it to install all the other things.

Install git
-------------

You will need to install git to download the latest source code for Kamaiti from GitHub.


.. code-block:: text

    kmtuser@server:~$ su -


.. code-block:: text

    root@server:~$ yum install git

The git version is older in the CentOS repositories. I recommend installing the latest git release locally, similar to what we did with Python 3.3.


.. code-block:: text

    kmtuser@server:~$ mkdir ~/localgit



.. code-block:: text

    kmtuser@server:~$ git clone https://github.com/git/git ~/localgit


.. code-block:: text

    kmtuser@server:~$ su -


.. code-block:: text

    root@server:~$ yum install libcurl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel


.. code-block:: text

    root@server:~$ logout


.. code-block:: text

    kmtuser@server:~$ cd ~/localgit


.. code-block:: text

    kmtuser@server:~/localgit$ cd git


.. code-block:: text

    kmtuser@server:~/localgit/git$ make prefix=~/usr/local all


.. code-block:: text

    kmtuser@server:~/localgit/git$ make prefix=~/usr/local install


.. code-block:: text

    kmtuser@server:~/localgit/git$ ls /usr/local/bin


.. code-block:: text

    kmtuser@server:~/localgit/git$ source ~/.bashrc

You should now be able to use the latest git you just installed locally.

Install PostgreSQL
--------------------------


You will need to install PostgreSQL. You could technically use MySQL instead since there's no PGSQL specific things used in Kamaiti. But the code was developed and tested using PGSQL so it's recommended.

One reason for using CentOS as development machine is the level of support you get from various projects. PostgreSQL is no exception. They provide their own repositories which make it easy to install the latest version of PostgreSQL.

First you need to configure your CentOS repository to not install the PostgreSQL that comes with CentOS. Edit the file /etc/yum.repos.d/CentOS-Base.repo.


.. code-block:: text

    kmtuser@server:~$ su -


.. code-block:: text

    root@server:~$ vim /etc/yum.repos.d/CentOS-Base.repo

Under both sections [base] and [updates], append the following line and save the file.

exclude=postgresql*

Next, you want to identify the version of PostgreSQL you want to install. This project uses PostgreSQL 9.3 64-bit. For this reason download the appropriate rpm package.


.. code-block:: text

    root@server:~$ curl -O http://yum.postgresql.org/9.3/redhat/rhel-6-x86_64/pgdg-centos93-9.3-1.noarch.rpm

Install the downloaded rpm. It will install the PostgreSQL repository so you can then use yum to install PostgreSQL 9.3.


.. code-block:: text

    root@server:~$ rpm -ivh pgdg-centos93-9.3-1.noarch.rpm


.. code-block:: text

    root@server:~$ yum install postgresql93-server


.. code-block:: text

    root@server:~$ service postgresql-9.3 initdb


.. code-block:: text

    root@server:~$ chkconfig postgresql-9.3 on

You should now be able to use the latest git you just installed locally.

For more information visit https://wiki.postgresql.org/wiki/YUM_Installation.

Configure PostgreSQL
---------------------


I recommend that you create a universal admin account for PGSQL, which you can use to administer the installation.


.. code-block:: text

    kmtuser@server:~$ su -


.. code-block:: text

    root@server:~$ su postgres


.. code-block:: text

    bash-4.1$ createuser --pwprompt --superuser pgdbadmin

You'll be asked to provide a user password. Since this is for the administrator, choose a really secure one.


.. code-block:: text

    bash-4.2$ exit


.. code-block:: text

    root@server:~$ vim /var/lib/pgsql/9.3/data/pg_hba.conf

Add the following line just above "local all all peer", then save the file and close vim.

    local   all             pgdbadmin                               password


.. code-block:: text

    root@server:~$ service postgresql-9.3 reload

Once you have a PGSQL server installed and running, you need to configure it for Kamaiti. You need to decide three things: database name, username, and password. I highly recommend that you use the default database and user name but choose your own secure password. These three pieces of information will also be required to later configure Django.


.. code-block:: text

    root@server:~$ su postgres


.. code-block:: text

    bash-4.2$ createdb --owner pgdbadmin --template template1 --encoding UTF8 --lc-collate 'en_US.UTF-8' --lc-ctype 'en_US.UTF-8' kamaitidb


.. code-block:: text

    bash-4.2$ createuser --pwprompt kamaitidbadmin

Provide a password and answer 'no' to the first two questions ("Shall the new role be a superuser?" and "Shall the new role be allowed to create databases?") and answer 'yes' to the final question ("Shall the new role be allowed to create more new roles?").

Remember that this user/password will be required/used in Django's settings file.


.. code-block:: text

    bash-4.2$ psql kamaitidb -W pgdbadmin

Provide the password to login.


.. code-block:: text

    kamaitidb=# grant all on database kamaitidb to kamaitidbadmin


.. code-block:: text

    kamaitidb=# \q


.. code-block:: text

    bash-4.2$ exit


.. code-block:: text

    root@server:~$ vim /var/lib/pgsql/9.3/data/pg_hba.conf

Add the following line just below "local all pgdbadmin password", then save the file and close vim.

    local   kamaitidb       kamaitidbadmin                          password


.. code-block:: text

    root@server:~$ service postgresql-9.3 reload


.. code-block:: text

    root@server:~$ exit

Verify the setup was successful


.. code-block:: text

    kmtuser@server:~$ psql kamaitidb -W kamaitidbadmin


.. code-block:: text

    kamaitidb=# \q

Download Kamaiti
----------------


You'll download the code for Kamaiti from GitHub to your home directory.


.. code-block:: text

    kmtuser@server:~$ git clone git://github.com/hamzasheikh/kamaiti.git ~/kamaiti

That's really all you need to do to get the code and start hacking on it.



