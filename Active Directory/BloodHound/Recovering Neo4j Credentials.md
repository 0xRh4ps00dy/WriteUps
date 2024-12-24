1. Stop neo4j if it is running

```shell-session
sudo systemctl stop neo4j
```

2. edit `/etc/neo4j/neo4j.conf`, and uncomment `dbms.security.auth_enabled=false`.

3. Start neo4j console:

```shell-session
sudo neo4j console
Directories in use:
home:         /var/lib/neo4j
config:       /etc/neo4j
logs:         /var/log/neo4j
plugins:      /var/lib/neo4j/plugins
import:       /var/lib/neo4j/import
data:         /var/lib/neo4j/data
certificates: /var/lib/neo4j/certificates
licenses:     /var/lib/neo4j/licenses
run:          /var/lib/neo4j/run
Starting Neo4j.
2023-01-05 20:49:46.214+0000 INFO  Starting
...SNIP...
```

4. Navigate to [http://localhost:7474/](http://localhost:7474/) and click `Connect` to log in without credentials.

5. Set a new password for the `neo4j` account with the following query: `ALTER USER neo4j SET PASSWORD 'Password123';`

![text](https://academy.hackthebox.com/storage/modules/69/neo4j_password_recovery1.jpg)

6. Stop neo4j service.
   
7. Edit `/etc/neo4j/neo4j.conf`, and comment out the `dbms.security.auth_enabled=false`.

8. Start Neo4j and use the new password.

| Command                                                                     | Description                            |
| --------------------------------------------------------------------------- | -------------------------------------- |
| `.\SharpHound.exe -c all --zipfilename inlanefreight_bloodhound`            | Run the SharpHound C# ingestor         |
| `Invoke-BloodHound -CollectionMethod all -ZipFileName ilfreight_bloodhound` | Run the SharpHound PowerShell ingestor |
| `bloodhound-python -dc <DC> -gc <GC> -d <DOMAIN -c All -u <user>`           | Run bloodhound-python                  |
