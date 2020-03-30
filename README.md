# CS 166 Server Access and Database Setup Guide

## Table of Contents  
- [Accessing a Lab Machine via SSH](#accessing-a-lab-machine-via-ssh)  
    - [Accessing from Linux or MacOS](#accessing-from-linux-or-macos)
    - [Accessing from Windows](#accessing-from-windows)
- [Database Setup (Courtesy of Petko Bakalov)](#database-setup-courtesy-of-petko-bakalov))  

## Accessing a Lab Machine via SSH

### Accessing from Linux or MacOS
To do

### Accessing from Windows

#### 1. Obtain and Install WinSCP and PuTTY
- WinSCP: <a href="https://winscp.net/" target="__blank">https://winscp.net/</a>
- PuTTY: <a href="https://www.chiark.greenend.org.uk/~sgtatham/putty/" target="__blank">https://www.chiark.greenend.org.uk/~sgtatham/putty/</a>

#### 2. Set Putty in WinSCP
1. Open WinSCP, click `Tools`, select `Preferences`, this will open the Preferences dialog.

2. Select `Integration` then `Applications`, set `PuTTY/Terminal client path` to **putty.exe**. The default path should work.  
    ![winscp+putty](https://winscp-static-746341.c.cdn77.org/data/media/screenshots/pref_integration_app.png)  
   You may check `Automatically open new sessions in PuTTY` if you want to automatically open a PuTTY windows whenever you open a connection to a server.

3. Click `OK` to save the configurations.

#### 3. (Optional) Create a Site to bolt.cs.ucr.edu
1. In WinSCP's login dialog, select `New Site` in the left panel, then fill the following information in the right ***Session* panel:
    - `File protocol` SFTP (default)
    - `Hostname` *bolt.cs.ucr.edu*
    - `Port number` 22 (default)
    - `User name` Your UCR ID
    - `Password` Your CS password. Leave empty if you don't want to save it.

    ![winscp-login](https://winscp-static-746341.c.cdn77.org/data/media/screenshots/login.png)

2. Click `Save` to create a shortcut for bolt. You may set a `Site name` as **bolt** or any name you prefer. Check `Save password (not recommended)` if you prefer to save your password. Be sure to only check this option in a safe environment. The press `OK` to save the site's configuration.

#### 4. Create a Site to Lab Machine via SSH Tunnel
All lab machines are protected by gateways, meaning you can only access them via a gateway server, such as bolt. To access a lab machine, you need to configure bolt as [SSH Tunnel](https://www.ssh.com/ssh/tunneling/example#what-is-ssh-port-forwarding,-aka-ssh-tunneling?).
1. In WinSCP's login dialog, create a new site with the following information:
    - `File protocol` SFTP (default)
    - `Hostname` *wch129-01.cs.ucr.edu*, assuming you are assigned to machine 01 in lab 129.
    - `Port number` 22 (default)
    - `User name` Your UCR ID
    - `Password` Your CS password. Leave empty if you don't want to save it.

2. Click `Advanced`, this will open `Advanced Site Settings`  
    ![winscp-site-advanced](https://winscp-static-746341.c.cdn77.org/data/media/screenshots/login_environment.png)

3. In the left panel, select `Connection`, then select `Tunnel`.  
    ![winscp-tunnel](https://winscp.net/eng/data/media/screenshots/login_tunnel.png)

4. Check `Connect through SSH tunnel`, and fill the following information:
    - `File protocol` SFTP (default)
    - `Hostname` *bolt.cs.ucr.edu*
    - `Port number` 22 (default)
    - `User name` Your UCR ID
    - `Password` Your CS password. Leave empty if you don't want to save it.
    - `Local tunnel port` *Autoselect* (default)
    - `Private key file` Leave empty (lab machines do not support keyless authentication)  

5. Click `OK` to save the site's advanced settings.

6. Click `Save` to create a shortcut for the site. You may set a `Site name` as **lab-machine**, or **wch129-01** (assuming you are assigned to machine 01 in lab 129), or any name you prefer. Check `Save password (not recommended)` if you prefer to save your password. Be sure to only check this option in a safe environment. The press `OK` to save the site's configuration.   

More details about SSH Tunnel in WinSCP: <a href="https://winscp.net/eng/docs/ui_login_tunnel" target="__blank">Tunnel Page (Advanced Site Settings dialog)</a>

#### 5. Use WinSCP
You can connect to a site by selecting its shortcut in WinSCP's login dialog, by clicking the `Login` button (with a green icon on the left and down arrow on the right).

Through WinSCP, you can transfer files from your local computer to the remote server. You can use the left and right file panel for file transfers. Or you can drag-and-drop files from the server to local, or from local to the server.

To open a **interactive** command line, you can use one of the following options:
- Go to `Commands`, select `Open in PuTTY`
- In the tool bar, click the icon with a flash between 2 monitors
- Use shortcut `Ctrl + P`

*Note*: In PuTTY, you can copy any text content by selecting the characters. You can paste by right clicking.

#### Documentations
- WinSCP: <a href="https://winscp.net/eng/docs/start" target="__blank">https://winscp.net/eng/docs/start</a>
- PuTTY: <a href="[https://the.earth.li/~sgtatham/putty/0.73/htmldoc/" target="__blank">https://the.earth.li/~sgtatham/putty/0.73/htmldoc/</a>


## Database Setup (Courtesy of Petko Bakalov)
1. Log in to lab computer with your CS account.

2. Setup your PostgreSQL environment
    1. Create a folder under /tmp as the environment of your test database:  
        ```console
        mkdir /tmp/$LOGNAME
        ```

    2. Create a data and socket folder under your environment, which will contain Postgre data files and socket information:
        ```bash
        cd /tmp/$LOGNAME
        mkdir -p test/data
        mkdir sockets
        export PGDATA=/tmp/$LOGNAME/test/data
        ```
   
    3. Initialize the database environment:
        ```console
        initdb
        ```

3. Start the PostgreSQL database server
    ```console
    pgctl -o "-c unix_socket_directories=/tmp/$LOGNAME/sockets" -D $PGDATA -l /tmp/$LOGNAME/logfile start
    ```

4. Create your database (Replace `$DB_NAME` with your database name)
    ```bash
    createdb -h /tmp/$LOGNAME/sockets $DB_NAME
    ```

5. Start the interactive environment (Replace `$DB_NAME` with your database name)
    ```console
    psql -h /tmp/$LOGNAME/sockets $DB_NAME
    ```
    The Postgre prompt like **\mydb=#** will show up to accept your SQL commands, 
    where *mydb* is the database name you specified in `$DB_NAME`. Use `\nq` to quit the interactive environment.
    You can also pipeline a SQL script into the interactive environment to execute it in bulk:
    ```console
    psql -h /tmp/$LOGNAME/sockets $DB_NAME < yourscript.sql
    ```

6. SQL Administrator Cook List Show all databases in your Postgre instance:
    ```sql
    SELECT datname FROM pg_database;
    ```
    Show all tables viewable to you:
    ```sql
    SELECT table name
    FROM information_schema.tables
    WHERE table_schema='public';
    ```

7. Stop the database instance
    ```console
    pgctl -o "-c unix_socket_directories=/tmp/$LOGNAME/sockets" -D /tmp/$LOGNAME/test/data stop
   ```