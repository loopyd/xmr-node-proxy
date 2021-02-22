# xmr-node-proxy

## This is a fork

You are browsing a fork of the original xmr-node-proxy repository.  You will not recieve any support, whatsoever for choosing to use this fork.

__You do so at your own risk__

## Fork Features

1. Cleaned and tidied up installation script output (with color, and clean code!)
2. Installer sanity checking
3. Updated to work in modern versions of Ubuntu (20.04 LTS+)
4. No donations, whatsover.  Complete removal of malicious node IP addresses from code.

Based on a clean ("green-field") Ubuntu 20.04 LTS Server Minimal installation.

## Deployment via Installer

1. Create a user 'nodeproxy' and assign a password (or add an SSH key. If you prefer that, you should already know how to do it)

```bash
useradd -d /home/nodeproxy -m -s /bin/bash nodeproxy
passwd nodeproxy
```

2. Add your user to `/etc/sudoers`, this must be done so the script can sudo up and do it's job.  We suggest passwordless sudo.  Suggested line: `<USER> ALL=(ALL) NOPASSWD:ALL`.  Our sample builds use: `nodeproxy ALL=(ALL) NOPASSWD:ALL`

```bash
echo "nodeproxy ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
```

3. Log in as the **NON-ROOT USER** you just created and run the [deploy script](https://raw.githubusercontent.com/Snipa22/xmr-node-proxy/master/install.sh).  This is very important!  This script will install the proxy to whatever user it's running under!

```bash
curl -L https://raw.githubusercontent.com/loopyd/xmr-node-proxy/master/install.sh | bash
```

3. Once it's complete, copy `example_config.json` to `config.json` and edit as desired.
4. Run: `source ~/.bashrc`  This will activate NVM and get things working for the following pm2 steps.
8. Once you're happy with the settings, go ahead and start all the proxy daemon, commands follow.

```shell
cd ~/xmr-node-proxy/
pm2 start proxy.js --name=proxy --log-date-format="YYYY-MM-DD HH:mm Z"
pm2 save
```
You can check the status of your proxy by either issuing

```
pm2 logs proxy
```

or using the pm2 monitor

```
pm2 monit
```

## Known Issues

VMs with 512Mb or less RAM will need some swap space in order to compile the C extensions for node.  Bignum and the CN libraries can chew through some serious memory during compile.  In regards to this, one of our users has put together a guide for T2.Micro servers: https://docs.google.com/document/d/1m8E4_pDwKuFo0TnWJaO13LDHqOmbL6YrzyR6FvzqGgU (Credit goes to MayDay30 for his work with this!)

If not running on an Ubuntu 16.04 system, please make sure your kernel is at least 3.2 or higher, as older versions will not work for this.

Many smaller VMs come with ulimits set very low. We suggest looking into setting the ulimit higher. In particular, `nofile` (Number of files open) needs to be raised for high-usage instances.

If your system doesn't have AES-NI, then it will throw an error during the node-multi-hashing install, as this requires AES-NI.  If this is the case, go ahead and change the following line:
"multi-hashing": "git+https://github.com/Snipa22/node-multi-hashing-aesni.git",
to:
"multi-hashing": "git://github.com/clintar/node-multi-hashing.git#Nan-2.0",

In your `packages.json`, do a `npm install`, and it should pass.

## Performance

The proxy gains a massive boost over a basic pool by accepting that the majority of the hashes submitted _will_ not be valid (does not exceed the required difficulty of the pool).  Due to this, the proxy doesn't bother with attempting to validate the hash state nor value until the share difficulty exceeds the pool difficulty.

In testing, we've seen AWS t2.micro instances take upwards of 2k connections, while t2.small taking 6k.  The proxy is extremely light weight, and while there are more features on the way, it's our goal to keep the proxy as light weight as possible.

## Configuration Guidelines

Please check the [wiki](https://github.com/Snipa22/xmr-node-proxy/wiki/config_review) for information on configuration

## Developer Donations

This has been hacked out of the loopyd fork to make it donation-free.  The devPool option is no longer supported in this unofficial fork.

## Installation/Configuration Assistance

Absolutely no support is offered for this fork of XMR-Node-Proxy from Snipa22.  You will **BE REFUSED** support if you are using the loopyd fork.

## Known Working Pools

* [XMRPool.net](https://xmrpool.net)
* [supportXMR.com](https://supportxmr.com)
* [pool.xmr.pt](https://pool.xmr.pt)
* [minemonero.pro](https://minemonero.pro)
* [XMRPool.xyz](https://xmrpool.xyz)
* [ViaXMR.com](https://viaxmr.com)
* [mine.MoneroPRO.com](https://mine.moneropro.com)
* [MinerCircle.com](https://www.minercircle.com)
* [xmr.p00ls.net](https://www.p00ls.net)
* [MoriaXMR.com](https://moriaxmr.com)
* [MoneroOcean.stream](https://moneroocean.stream)
* [SECUmine.net](https://secumine.net)
* [Chinaenter.cn](http://xmr.chinaenter.cn)
* [XMRPool.eu](https://xmrpool.eu)

This list has been updated from the original repository README, and may fall behind as a result of the fork.
