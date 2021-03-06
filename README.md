# Heptacoin-Seeder

Heptacoin-seeder is a crawler for the Heptacoin network, which exposes a list of reliable nodes via a built-in DNS server.

Run your Heptacoin seeder and post it in https://github.com/heptacoin-project/heptacoin/issues/3. We need a lot of seed nodes to be fully decentralized!

Features:
* regularly revisits known nodes to check their availability
* bans nodes after enough failures, or bad behaviour
* accepts nodes down to v0.3.19 to request new IP addresses from, but only reports good post-v0.3.24 nodes.
* keeps statistics over (exponential) windows of 2 hours, 8 hours, 1 day and 1 week, to base decisions on.
* very low memory (a few tens of megabytes) and cpu requirements.
* crawlers run in parallel (by default 96 threads simultaneously).


## Prerequisites

```
$ sudo apt-get install build-essential libboost-all-dev libssl-dev
```

## Usage

Assuming you want to run a dns seed on dnsseed.example.com, you will need an authorative NS record in example.com's domain record, pointing to for example vps.example.com:

```
$ dig -t NS dnsseed.example.com

;; ANSWER SECTION
dnsseed.example.com.   86400    IN      NS     vps.example.com.
```

On the system vps.example.com, you can now run heptacoin-seeder:

```
./heptacoin-seeder -h dnsseed.example.com -n vps.example.com
```

If you want the DNS server to report SOA records, please provide an e-mail address (with the @ part replaced by .) using -m.

## Compiling

Compiling will require boost and ssl.  On debian systems, these are provided by `libboost-dev` and `libssl-dev` respectively.

```
$ make
```

This will produce the `heptacoin-seeder` binary.

## Running as non-root

Typically, you'll need root privileges to listen to port 53 (name service).

One solution is using an iptables rule (Linux only) to redirect it to a non-privileged port:

```
$ iptables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-port 5353
```

If properly configured, this will allow you to run heptacoin-seeder in userspace, using the -p 5353 option.

Alternatively, you can use the CAP_NET_BIND_SERVICE capability on Linux kernel versions greater than 2.6.24.

```
$ setcap 'cap_net_bind_service=+ep' /path/to/heptacoin-seeder
```

`setcap` usually comes pre-installed on most modern distributions, but is available in the `libcap2-bin` package on debian and libcap2 on RedHat.

## Docker

Build the docker image by running the following command:

$ docker build -t ltcz/heptacoin-seeder .

Run the docker image with the following command:

```
$ docker run -d --name heptacoin-seeder -p 53:53/udp -p 53:53 ltcz/heptacoin-seeder -h dnsseed.example.com -n vps.example.com -m email@gmail.com
```

Run the docker image with a restart policy:

```
$ docker run -d --restart always --name heptacoin-seeder -p 53:53/udp -p 53:53 ltcz/heptacoin-seeder -h dnsseed.example.com -n vps.example.com -m email@gmail.com
```

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

## Security Warnings

**Heptacoin is experimental and a work-in-progress.** Use at your own risk.
