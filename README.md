# AdGuardHome Upstream for OpenWrt

[![adguardhome-upstream](https://img.shields.io/badge/GitHub-AdGuardHome%20Upstream-blueviolet?style=flat-square&logo=github)](https://github.com/fernvenue/adguardhome-upstream)
[![adguardhome-upstream](https://img.shields.io/badge/GitLab-AdGuardHome%20Upstream-orange?style=flat-square&logo=gitlab)](https://gitlab.com/fernvenue/adguardhome-upstream)

Use [felixonmars/dnsmasq-china-list](https://github.com/felixonmars/dnsmasq-china-list) with [AdGuardHome](https://github.com/AdGuardTeam/AdGuardHome).

* [Steps for usage](#steps-for-usage)
  * [Before starting](#before-starting)
  * [Get and run the script](#get-and-run-the-script)
  * [Use systemd timer to automate](#use-systemd-timer-to-automate)
* [Features and details](#features-and-details)
  * [Features](#features)
  * [Files in repository](#files-in-repository)
  * [How felixonmars's dnsmasq-china-list works?](#how-felixonmarss-dnsmasq-china-list-works)
  * [Why it's better than other methods?](#why-its-better-than-other-methods)
  * [Important mentions!](#important-mentions)
* [Something else](#something-else)
  * [Always use the recommended configuration first](#always-use-the-recommended-configuration-first)
  * [This is not for...](#this-is-not-for)
  * [Links](#links)

## Steps for usage

### Before starting

First, [cURL](https://curl.se/) and [sed](https://www.gnu.org/software/sed/) are required. And before starting, you need to change some settings in `AdGuardHome.yaml`:

- `upstream_dns_file` **must be** `/usr/share/adguardhome.upstream`.
- `all_servers` **should be** `true`.
- `cache_optimistic` is recommended to be `true`.

<details><summary>What do these options do?</summary>


The option `upstream_dns_file` allows you to loading upstreams from a file, `all_servers` enables parallel queries to all configured upstream servers to speed up resolving, and `cache_optimistic` makes AdGuardHome respond to client from cache first and send new request at the same time to the upstream and update the cache. For more information please read the [AdGuardHome Wiki](https://github.com/AdguardTeam/AdGuardHome/wiki/Configuration).

</details>

OpenWrt  AdGuardHome.yaml path `/etc/AdGuardHome.yaml`

By the way, it's highly recommended to set reliable DNS servers as [`fallback_dns`](https://github.com/AdguardTeam/AdGuardHome/wiki/Configuration#configuration-file).

### Get and run the script

one-step.sh

```bash
wget https://raw.githubusercontent.com/sumuen/adguardhome-upstream/master/one_step.sh --no-check-certificate && chmod +x ./one_step.sh  && ./one_step.sh 
```

</details>

## Features and details

### Features

- Improve resolve speed for Chinese domains.
- Get the best CDN results.
- Prevent DNS poisoning.
- Better than other methods.

### Files in repository

- [LICENSE](./LICENSE): BSD3 Clause Liscense.
- [README.md](./README.md): Description file.
- [upstream.service](./upstream.service): Systemd service template.
- [upstream.timer](./upstream.timer): Systemd timer template.
- [upstream.sh](./upstream.sh): Updating and converting scripts.
- [v4.conf](./v4.conf): Recommended IPv4 only upstream configuration.
- [v6only.conf](./v6only.conf): Recommended IPv6 only upstream configuration.
- [v6.conf](./v6.conf): Recommended IPv4 and IPv6 upstream configuration.

### How felixonmars's dnsmasq-china-list works?

Using specific upstreams for some domains is a common way to accelerate internet in mainland China. This list collects domains that use NS servers located in mainland China, allowing us to use some DNS servers for them that don't break CDN or geo-based results, while using encrypted and trusted DNS servers for other domains.

### Why it's better than other methods?

On the one hand, for DNS resolution, when the domain's name server is in other region, even if the domain is resolved to an address in mainland China, we can still get the fastest resolution by DNS request from the other region in most cases, you might say that some DNS servers have caches, usually it brings a lot of problems. In fact, AdGuardHome has adopted optimistic caching since v0.107, which is much better than relying on upstream DNS caching. On the other hand, many tests are showing that some of the poisoned results are IP addresses located in anywhere. Therefore, it is impractical to infer whether the result is poisoned by the location of the IP address. This list only includes domains that use NS servers from mainland China, that's why it is better than redir-host or any other similar methods. 

### Important mentions!

It's highly NOT recommanded that use any other list, because felixonmars's dnsmasq-china-list actively updated, and has clear rule to determine whether a domain should or shouldn't be added to the list, it's also very precise and efficient, some shit project like to list all subdomains of a domain to a list, but actually you can just use `[/example.com/]` to include them, because a domain itslef and all it's subdomains use same NS! Why these shit project like to list all these subdomains to make things slow and complicated?! Upstream list is definitely not better just because it's larger, it's better when it's more accurate, and actually a larger list is even worse in most cases!

By the way, it's also highly NOT recommanded that use domain SNI instead of IP address for some public DNS servers has certificates on their IP addresses, like `tls://8.8.4.4`, it's actually better than `tls://dns.google`, using an IP address can not only prevent SNI-based RST, but also save additional DNS query to the DNS server itself, why these shit project like to use domain instead of IP address?

## Something else

### Always use the recommended configuration first

The recommended configurations will be automatically selected and used by the script. These upstreams are carefully selected, they include encrypted and trusted and unfiltered upstreams, and they all have SSL certificates configured on their IP addresses, so there is no need for additional resolution by Bootstrap DNS servers, and they can respond to requests as quickly as possible in parallel request mode. If your network environment is not very special, **DO NOT** change the script or recommended configurations.

### This is not for...

This is **NOT FOR** breaking any network firewall, and in fact it **CAN NOT** be used for that either. It's only used to accelerate internet in mainland China such as improve DNS resolve speed for Chinese domains, get the best CDN or geo-based results and so on, please don't misunderstand it.

### Links

- AdGuardHome: https://github.com/AdguardTeam/AdGuardHome
- felixonmars/dnsmasq-china-list: https://github.com/felixonmars/dnsmasq-china-list
- Google Public DNS: https://developers.google.com/speed/public-dns
- Cloudflare DNS: https://www.cloudflare.com/dns/
- TUNA DNS: https://tuna.moe/help/dns/