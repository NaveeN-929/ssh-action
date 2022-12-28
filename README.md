# 🚀 SSH for GitHub Actions

**Important**: Only support **Linux** [docker](https://www.docker.com/) container.

## Input variables

See [action.yml](./action.yml) for more detailed information.

* `host` - ssh host
* `port` - ssh port, default is `22`
* `username` - ssh username
* `password` - ssh password
* `passphrase` - the passphrase is usually to encrypt the private key
* `sync` - synchronous execution if multiple hosts, default is false
* `timeout` - timeout for ssh to remote host, default is `30s`
* `command_timeout` - timeout for ssh command, default is `10m`
* `key` - content of ssh private key. ex raw content of ~/.ssh/id_rsa, remember include the BEGIN and END lines 
* `key_path` - path of ssh private key
* `fingerprint` - fingerprint SHA256 of the host public key, default is to skip verification
* `script` - execute commands
* `script_stop` - stop script after first failure
* `envs` - pass environment variable to shell script
* `debug` - enable debug mode
* `cipher` - the allowed cipher algorithms. If unspecified then a sensible

SSH Proxy Setting:

* `proxy_host` - proxy host
* `proxy_port` - proxy port, default is `22`
* `proxy_username` - proxy username
* `proxy_password` - proxy password
* `proxy_passphrase` - the passphrase is usually to encrypt the private key
* `proxy_timeout` - timeout for ssh to proxy host, default is `30s`
* `proxy_key` - content of ssh proxy private key.
* `proxy_key_path` - path of ssh proxy private key
* `proxy_fingerprint` - fingerprint SHA256 of the proxy host public key, default is to skip verification
* `proxy_use_insecure_cipher` - include more ciphers with use_insecure_cipher
* `proxy_cipher` - the allowed cipher algorithms. If unspecified then a sensible

## Usage

Executing remote ssh commands.

```yaml
name: remote ssh command
on: [push]
jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
    - name: executing remote ssh commands using password
      uses: naveenkumarck/ssh-action@v1.0.0
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        password: ${{ secrets.PASSWORD }}
        port: ${{ secrets.PORT }}
        script: whoami
```

output:

```sh
======CMD======
whoami
======END======
out: ***
==============================================
✅ Successfully executed commands to all host.
==============================================
```

### Setting up a SSH Key

Make sure to follow the below steps while creating SSH Keys and using them.
The best practice is create the SSH Keys on local machine not remote machine.
Login with username specified in Github Secrets. Generate a RSA Key-Pair:

<details>
<summary>rsa</summary>
<p>

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

</p>
</details>

<details>
<summary>ed25519</summary>
<p>

```bash
ssh-keygen -t ed25519 -a 200 -C "your_email@example.com"
```

</p>
</details>

Add newly generated key into Authorized keys. Read more about authorized keys [here](https://www.ssh.com/ssh/authorized_keys/).

<details>
<summary>rsa</summary>
<p>

```bash
cat .ssh/id_rsa.pub | ssh b@B 'cat >> .ssh/authorized_keys'
```

</p>
</details>

<details>
<summary>ed25519</summary>
<p>

```bash
cat .ssh/id_ed25519.pub | ssh b@B 'cat >> .ssh/authorized_keys'
```

</p>
</details>

Copy Private Key content and paste in Github Secrets.

<details>
<summary>rsa</summary>
<p>

```bash
clip < ~/.ssh/id_rsa
```

</p>
</details>

<details>
<summary>ed25519</summary>
<p>

```bash
clip < ~/.ssh/id_ed25519
```

</p>
</details>

See the detail information about [SSH login without password](http://www.linuxproblem.org/art_9.html).

**A note** from one of our readers: Depending on your version of SSH you might also have to do the following changes:

* Put the public key in `.ssh/authorized_keys2`
* Change the permissions of `.ssh` to 700
* Change the permissions of `.ssh/authorized_keys2` to 640

### If you are using OpenSSH

If you are currently using OpenSSH and are getting the following error:

```bash
ssh: handshake failed: ssh: unable to authenticate, attempted methods [none publickey]
```

Make sure that your key algorithm of choice is supported. On Ubuntu 20.04 or later you must explicitly allow the use of the ssh-rsa algorithm. Add the following line to your OpenSSH daemon file (which is either `/etc/ssh/sshd_config` or a drop-in file under 
`/etc/ssh/sshd_config.d/`):

```bash
CASignatureAlgorithms +ssh-rsa
```

Alternatively, `ed25519` keys are accepted by default in OpenSSH. You could use this instead of rsa if needed:

```bash
ssh-keygen -t ed25519 -a 200 -C "your_email@example.com"
```
