# addon-sshagent
OpenNebula addon that provide configuration of a systemd service that start the ssh-agent when opennebula start

## Installation

Copy the systemd configuiraion files on the frontend node(s)

```bash
cp -a etc/* /etc/
echo "export SSH_AUTH_SOCK=/var/lib/one/.ssh/onessh-agent.sock" >> ~oneadmin/.bashrc
```

Edit _~/oneadmin/.ssh/config_ and add the folloing lines:

```
AddKeysToAgent yes
ForwardAgent yes
``` 

Reload the systemd configuration and stop/start the opennebula service:

```
systemctl daemon-reload
systemctl stop opennebula
systemctl start opennebula
```

To test the configuration try to connect to a host as the oneadmin user and check is the ssh-agent forwarded:

```
[root@frontend ~]# su - oneadmin
[oneadmin@frontend ~]$ ssh host1
Last login: ....
[oneadmin@host1 ~] ssh-add -l
2048 SHA256:nqrrqjUR1O1GvVGlqmRQyInaVW25R/8DaX5tFxiCAKc  (RSA)

# optionally remove the ssh key file and test connecting to another host
[oneadmin@host1 ~] rm -f .ssh/id_rsa
[oneadmin@host1 ~] ssh host2
Last login: ....
[oneadmin@host2 ~]
```

To finalize the re-configuration remove the ssh key file from all nodes.

Please note that you shold omit copying the ssh key file when adding/recovering a host to the OpenNebula cluster.

