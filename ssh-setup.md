# Set up SSH access to the LTS2 lab cluster from a MacBook

You need an enabled LTS2 cluster account and access to the EPFL network (on campus,
or through the EPFL VPN off campus). Ask the lab administrator if your account
has not been provisioned. macOS includes the `ssh` client; check with `ssh -V`.

## 1. Create or reuse a local SSH key

On your **MacBook**:

```sh
mkdir -p ~/.ssh
chmod 700 ~/.ssh
ls ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub
```

If you already have a key, reuse it; **do not overwrite it**. If you don't, create one:

```sh
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -N ''
```

`-N ''` creates the key **without a passphrase**, so a local agent can use it
without an unlock prompt. If you generate it interactively instead, press Enter
at both passphrase prompts. Anyone who obtains this private key can use it;
keep it private and protect access to your MacBook.

Only `id_ed25519.pub` is public. Never copy or share `id_ed25519`, the private key.
If you use a different key filename, adapt the commands and SSH config below.

## 2. Register your public key on the cluster

On your MacBook, replace `YOUR_GASPAR_USERNAME` with your EPFL/GASPAR username
(it may differ from your MacBook username):

```sh
ssh YOUR_GASPAR_USERNAME@stivm0163.xaas.epfl.ch
```

Enter your GASPAR password when prompted. Run `whoami` on the cluster to confirm
your username.

On the **cluster**:

```sh
mkdir -p ~/.ssh
chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

On your **MacBook**, copy the public key to your clipboard with
`pbcopy < ~/.ssh/id_ed25519.pub`. Append that single public-key line to
`~/.ssh/authorized_keys` **on the cluster**, using an editor like `nano`. Don't replace
other keys already in that file. Then log out with `exit`.

## 3. Add the local SSH alias

On your **MacBook**, back up `~/.ssh/config` if it exists, then add the following
entry using an editor like `nano`. Preserve your other host entries; if `lts2` already exists,
update that entry rather than adding a duplicate.

```sshconfig
Host lts2
    HostName stivm0163.xaas.epfl.ch
    User YOUR_GASPAR_USERNAME
    IdentityFile ~/.ssh/id_ed25519
```

```sh
chmod 600 ~/.ssh/config
ssh lts2
```

With the passphrase-free key configured, you should now connect without a
password prompt. Before using an agent, test non-interactive access:

```sh
ssh -o BatchMode=yes lts2 'hostname'
```

If that fails, fix network/VPN access or key authentication in your terminal;
don't disable host-key checks or give the agent your password/private key.

Next: [set up your agent and install the lts2 skill](agent-setup.md).
