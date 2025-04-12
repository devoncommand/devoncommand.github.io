---
# region: Hugo
# author: [] # [string<Array> | string]
date: 2025-02-08
expiryDate: 2026-02-08T23:59:59-03:00 # Hugo excludes expired pages, use `--buildExpired` to include them
draft: false
title: 'Linux Security Hardening: Best Practices'
summary: 'Essential tips for hardening Linux security, including user management, firewall setup, and SSH protection.' # Refer to: 'https://gohugo.io/content-management/summaries/'
description: 'This guide provides an introduction to essential Linux server security practices. Although it primarily focuses on Debian and Ubuntu, the principles discussed here are applicable to other Linux distributions as well. I also recommend exploring these topics further and adapting them to your specific needs.'
tags: ['linux', 'security', 'hardening']
aliases: [] # URL aliases. [string<Array>]
keywords: ['Linux security', 'Linux hardening'] # [string<Array>]
categories: ['Linux'] # [string<Array>]
# regionend

# region: hugo-PaperMod
#CanonicalLinkText: 'Originally published at' # Text before the hostname of the canonical URL. [string]
#canonicalURL: 'https://canonical.url/to/page' # Add a canonical URL to posts. [string]
#ShowCanonicalLink: true # Show the hostname of the canonical URL. [true | false]
#cover:
#    image: 'featured.jpg' # Add a cover image. [string]
#    alt: 'This is the cover image' # Alternate text if the image does not load. [string]
#    caption: 'Caption for image' # Caption for the cover image. [string]
#    hidden: false # Hide cover image on the current single page. [true | false]
#    relative: false # Use relative path for cover image (used in Hugo Page Bundles). [true | false]
#    responsiveImages: true # Enable/disable responsive cover images. [true | false]
#disableShare: false # Hide/show share icons under a page. [true | false]
#ShareButtons: ['linkedin', 'x'] # Customize which share buttons are enabled on the page. [list]
ShowBreadCrumbs: true # Show breadcrumb navigation above single posts/pages. [true | false]
ShowCodeCopyButtons: true # Show/hide code copy button. [true | false]
ShowPostNavLinks: true # Show previous and next post navigation links. [true | false]
ShowReadingTime: true # Show reading time in post metadata. [true | false]
ShowRssButtonInSectionTermList: true # Show RSS button in section, term, and list pages. [true | false]
ShowWordCount: false # Show word count in post metadata. [true | false]
UseHugoToc: false # Use Hugo's default ToC instead of a custom one. [true | false]
#comments: true # Show/hide comments. [true | false]
disableAnchoredHeadings: false # Disable anchored headings. [true | false]
hideFooter: false # Hide the footer text at the end of the page. [true | false]
hideSummary: false # Hide summary displayed on list pages. [true | false]
hidemeta: false # Hide meta elements (date, read-time, author, available translations). [true | false]
robotsNoIndex: false # Prevent a page from being indexed by search engines. [true | false]
searchHidden: false # Hide page from internal search. [true | false]
showtoc: true # Show/hide the Table of Contents. [true | false]
tocopen: false # Keep the Table of Contents open by default on page load. [true | false]
# regionend
---

<!-- @format -->

## Firewall

A properly configured firewall is essential as it controls traffic and prevents unauthorized access to your system. By default, most systems are configured to accept all incoming traffic, which poses some security risks. The best practice is to deny all incoming traffic by default and only allow specific traffic that you explicitly trust and need for your services.

In this guide, we'll be using UFW (Uncomplicated Firewall) to configure the firewall, but you are free to use any other firewall application of your choice, such as iptables or firewalld, depending on your system and preferences.

### Installing UFW

To get started with UFW, first, you need to install it. You can do so with the following command:

```bash
apt install ufw
```

This command will install `UFW` and its necessary dependencies on your server.

> **Important:** Check with your distribution's package manager for the appropriate installation command.

### Allowing Essential Services

Once UFW is installed, you need to define which types of network traffic are allowed to pass through your firewall. For a basic web server setup, you’ll likely want to allow SSH (for remote access) as well as HTTP and HTTPS (for web traffic).

To allow SSH, HTTP, and HTTPS traffic, run the following commands:

```bash
ufw limit 22/tcp  # This command allows SSH connections but limits the number of login attempts to help prevent brute-force attacks (default port: 22).
ufw allow 80/tcp  # This opens the HTTP port (port 80) for web traffic.
ufw allow 443/tcp # This opens the HTTPS port (port 443) for encrypted web traffic.
```

You can adjust the service ports if you're using custom ports or other services. For example, if you're running a web application on a non-standard port, you can use `ufw allow <port_number>/tcp.`

### Setting Default Policies

Once you've allowed the necessary services, it's time to set the default policies for incoming and outgoing connections.

```bash
ufw default deny incoming  # This ensures that any traffic not explicitly allowed will be blocked.
ufw default allow outgoing #
```

These default settings mean that only the services you have explicitly allowed (such as SSH, HTTP, and HTTPS) will be accessible, while everything else is blocked, helping to reduce your attack surface.

### Enabling UFW

Once you have configured your firewall rules, it’s time to enable UFW.

This will activate the firewall with the rules you've set up:

```bash
ufw enable
```

After enabling UFW, the firewall will start filtering traffic based on your rules. The ufw status command can be used to check the current status and see a list of active rules:

```bash
ufw status
```

This will display whether UFW is active and show the current set of allowed and denied services.

## Secure SSH

SSH is commonly used for remote access, making it crucial to secure it with strong passwords, key-based authentication, and by disabling root login to prevent brute force or unauthorized logins. While SSH already provides strong encryption, there are additional best practices you can implement to further harden it and make unauthorized access even more difficult.

### Use SSH Key Authentication

Before editing the SSH configuration, it's essential to set up SSH key authentication. SSH keys are more secure than passwords and provide a more convenient way to log in to your server.

#### Generating SSH Keys

To generate an SSH key pair, run the following command on your local machine:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

This command generates a new SSH key pair using the Ed25519 algorithm. You can also use the RSA algorithm by replacing `-t ed25519` with `-t rsa`. The `-C` flag is optional and allows you to add a comment to the key. `ED25519` keys are more secure and faster than `RSA` keys and are recommended for most use cases.

After running this command, you will be prompted to enter a file in which to save the key. Press Enter to save the key in the default location (`~/.ssh/id_ed25519`), or specify a different location if needed.

You can also set a passphrase for additional security. The passphrase is optional but highly recommended for an extra layer of protection.

#### Copying the Public Key to the Server

After generating the SSH key pair, you need to copy the public key to your server. You can do this using the `ssh-copy-id` command:

```bash
ssh-copy-id user@hostname
```

Replace `user` with your username (registered on the server) and `hostname` with your server's IP address or domain name. This command will copy your public key to the server and add it to the `~/.ssh/authorized_keys` file.

#### Logging in with SSH Keys

After copying the public key to the server, you can log in using SSH keys:

```bash
ssh user@hostname
```

This command will log you in to the server using the SSH key pair you generated. If you set a passphrase, you will be prompted to enter it.

If you have a custom SSH key location, you can specify it using the `-i` flag:

```bash
ssh -i ~/.ssh/custom_key user@hostname
```

### Editing SSH Configuration

Securing SSH starts with modifying the SSH server's configuration file. This file is typically located at `/etc/ssh/sshd_config`.

#### Disable Challenge-Response Authentication

Challenge-response authentication can be used in 2FA setups but often just asks for a password, which doesn’t add much security. This setting is used by some 2FA methods, however, many 2FA methods do not require it. Unless you are using a 2FA method that specifically requires `ChallengeResponseAuthentication`, disabling it can streamline the login process.

```properties
# Disable unless required by your 2FA method
ChallengeResponseAuthentication no
```

If you are using 2FA, consult the documentation for your specific 2FA method to determine whether `ChallengeResponseAuthentication` needs to be enabled.

#### Enable PAM

PAM (Pluggable Authentication Modules) is a framework that allows the integration of various authentication methods beyond just passwords, such as account lockouts, password strength policies, and multi-factor authentication (2FA). `UsePAM` should be enabled to allow your system to support these advanced security mechanisms.

```properties
# Enable PAM for advanced authentication features
UsePAM yes
```

#### Disable Password Authentication

Disabling password authentication entirely is one of the most important settings you can take. Brute-force attacks rely on guessing passwords. By disabling password authentication, you eliminate this attack vector entirely. SSH keys are significantly more secure and are the recommended way to access SSH servers.

```properties
# Disable password authentication; use SSH keys
PasswordAuthentication no
```

#### Disable Root Login

Allowing root login via SSH is a major security risk. This is another essential setting, disabling direct root login forces attackers to first compromise a regular user account. Even if they manage to do that, they still need to escalate privileges.

```properties
# Disable root login via SSH
PermitRootLogin no
```

This setting also allows for better auditing of administrative actions, as you can track which user performed `sudo` commands.

#### Optional Settings for Additional Protection

While these options may seem useless, they can potentially limit the attack vectors.

##### Limit SSH to IPv6

While IPv4 is more widely used, restricting SSH to IPv6 can limit potential attack vectors and reduce some automated attacks (bots scanning for open SSH servers on IPv4), though it's not a strict security measure and may cause issues on networks that don't support IPv6.

```properties
# Generally NOT recommended; can cause connectivity issues
AddressFamily inet6
```

Generally, do not restrict SSH to IPv6. The usability problems outweigh the minimal security benefit and attackers who are determined can still scan for IPv6 addresses. It's security by obscurity, which is generally not recommended. Focus on stronger authentication and authorization methods instead.

##### Change the Default Port

By default, SSH listens on port 22, which is well-known and often targeted by attackers. Changing the port to something non-standard can reduce the likelihood of automated attacks, though it is not a foolproof security measure. Choose a port number between 1024 and 65535 that isn’t already in use.

```properties
# Change SSH port (optional, but can deter some automated attacks)
Port <your_custom_port>
```

This setting can provide a tiny bit of extra security but should not be relied upon as a primary defense. Focus on stronger authentication and authorization methods instead.

#### Apply Changes and Restart SSH

After making the necessary changes, save and exit the file. To apply the changes, restart the SSH service:

```bash
systemctl restart sshd
```

## Fail2Ban

Fail2Ban is a tool that scans log files and bans IPs that show malicious activity. It is often used to prevent brute-force attacks on services like SSH.

### Installation

To get started with Fail2Ban, first, you need to install it. You can do so with the following command:

```bash
apt install fail2ban
```

This command will install `fail2ban` and its necessary dependencies on your server.

> **Important:** Check with your distribution's package manager for the appropriate installation command.

### Configuring Fail2Ban

After installation, you can create or edit the local configuration file to customize your settings. The default configuration is good for most users, but you can fine-tune it based on your needs.

```bash
$ cat /etc/fail2ban/jail.d/sshd.local

[DEFAULT]
bantime           = 3600 # Duration of the ban in seconds
bantime.increment = true # Increment the ban time for each new ban
findtime          = 600  # Time window for failed login attempts
maxretry          = 5    # Number of failed login attempts before a ban is initiated

[sshd]
enabled   = true                                   # Enable Fail2Ban for SSH
port      = 22                                     # If you changed the default SSH port, update it here
logpath   = %(sshd_log)s                           # Fail2Ban variable for the SSH log path
backend   = %(sshd_backend)s                       # Fail2Ban variable for the SSH backend
ignoreip  = 127.0.0.1/8 your_ip_address another_ip # Add your IP to the ignore list
banaction = ufw                                    # Optional: Use ufw if installed and configured. Otherwise, iptables is used.
```

Make sure the `logpath` for sshd is correct and the file exists as it can result in failure while loading Fail2Ban.

### Enable Fail2Ban

To enable Fail2Ban run the following command:

```bash
systemctl enable --now fail2ban.service
```

Fail2Ban will now monitor the SSH log file for failed login attempts and automatically block IP addresses that exceed the configured `maxretry` limit. The `bantime`, `findtime`, and `maxretry` settings can be adjusted in the `` section of a .local jail file.

## Auto updates

Keeping your system up-to-date with the latest security patches is vital to protect against known vulnerabilities. `unattended-upgrades` can automate this process, but it's important to understand the trade-offs. While automatic updates are highly recommended for security, they can occasionally cause unexpected issues (configuration conflicts, dependency problems), especially on servers. Therefore, many administrators prefer a more controlled approach.

Unattended upgrades are usually most important for **security** updates. It's often acceptable to handle other types of updates (like feature updates or package upgrades) manually.

### installing unattended-upgrades

Start by installing the `unattended-upgrades` package using the following command:

```bash
apt install unattended-upgrades

```

This command will install `unattended-upgrades` and its necessary dependencies on your server.

> **Important:** Check with your distribution's package manager for the appropriate installation command.

### configuring unattended-upgrades

After installing the package, **you'll need to configure it**. The `dpkg-reconfigure` command allows you to easily adjust the settings for unattended upgrades, including setting the priority level.

To enable unattended-upgrades and configure its settings, run:

```bash
dpkg-reconfigure unattended-upgrades
```

#### Configuration file

The default configuration file for the `unattended-upgrades` package is at `/etc/apt/apt.conf.d/50unattended-upgrades`. Any local customization should be in `/etc/apt/apt.conf.d/52unattended-upgrades-local`. For more details, you can read the official documentation at [Debian's wiki on unattended upgrades](https://wiki.debian.org/unattendedupgrades)

##### Control which updates are installed

This setting controls which updates are allowed to be installed automatically by editing the `Unattended-Upgrade::Allowed-Origins` section. To install **only** security updates (the recommended configuration), add the following to your local customization file:

```python
Unattended-Upgrade::Allowed-Origins {
    "dists/${distro}-security";
};
```

##### Enable automatic reboots

If you want the system to reboot automatically after installing updates, you can enable this feature by setting the `Unattended-Upgrade::Automatic-Reboot` and `Unattended-Upgrade::Automatic-Reboot-Time` options.

```python
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00"; // Adjust the time as needed
```

##### Exclude specific packages from automatic updates

Adjust the section `Unattended-Upgrade::Package-Blacklist` to exclude specific packages from automatic updates. For example:

```python
Unattended-Upgrade::Package-Blacklist {
"package_name";
};
```

##### Combined configuration

Here's an example of a combined configuration file:

```python
Unattended-Upgrade::Origins-Pattern {
    "origin=Debian,codename=${distro_codename},label=Debian-Security";
    "origin=Debian,codename=${distro_codename},label=Debian";
    "origin=Debian,codename=${distro_codename},label=Debian-Updates";
    "origin=Debian,codename=${distro_codename},label=Debian";
};

Unattended-Upgrade::Package-Blacklist {
    "package_name";
};

Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```

### how unattended-upgrades works

Unattended-upgrades is a separate package that works in conjunction with the regular apt update mechanism. It's not a replacement for apt update and apt upgrade.

Once enabled and configured, `unattended-upgrades` will periodically check for and install updates. These updates are automatically downloaded and installed, reducing the need for manual intervention. This helps keep your system secure by ensuring that critical updates are applied quickly. A typical workflow is:

1. `sudo apt update`: Refreshes the local package list.
2. `sudo apt upgrade`: Installs available updates (this is usually done manually).
3. `unattended-upgrades`: Periodically checks for and installs updates as configured (usually just security updates).

> **Important**: Some updates, especially kernel updates, require a reboot to take effect. Configure unattended-upgrades to reboot automatically (or plan for manual reboots) to ensure that all updates are applied correctly.

## App Armor or SELinux

These security modules provide Mandatory Access Control (MAC)\*\* to restrict what applications can do, but their complexity makes them slightly less critical than the above steps, depending on the use case.

> **Important**: You should only enable either AppArmor **or** SELinux. Do use use both, as they **conflict** which each other.

### SELinux

SELinux is typically used with distributions like CentOS, Fedora, and RHEL, but it can also be used on Ubuntu if needed. You can configure SELinux in enforcing mode, which strictly enforces security policies, or permissive mode for testing.

SELinux has two operational modes:

- Enforcing (default) → Strictly applies security policies.
- Permissive → Logs violations but does not block actions (useful for debugging).

> **Important**: If you plan on using SELinux on **Ubuntu**, make sure to **uninstall AppArmor**, as it comes pre-installed and conflicts with SELinux.

#### Installing SELinux

To install the required SELinux packages on **Ubuntu** run the command below:

```bash
apt install policycoreutils selinux-basics selinux-utils
```

This command will install `selinux` and its necessary dependencies on your server.

> **Important:** If you are not using Ubuntu, it's better and recommended to check your distribution's documentation for the most complete SELinux installation instructions.

#### Activating SELinux

Activate SELinux by running the command below.

```bash
selinux-activate
```

The activation will ask to reboot the system, and it is **required**, but first, finish the configuration before rebooting.

#### Change Policy to Targeted

Setting the SELinux policy to `targeted` or a more specialized policy ensures the system will confine processes that are likely to be targeted for exploitation, such as network or system services.

The SELinux targeted policy is appropriate for general-purpose desktops and servers, as well as systems in many other roles. To configure the system to use this policy, add or correct the following line in `/etc/selinux/config`:

```text
SELINUX=enforcing
SELINUXTYPE=targeted
```

Other policies, such as `mls`, provide additional security labeling and greater confinement but are not compatible with many general-purpose use cases which may break some applications.

#### Rebooting

Once configured, reboot the system (**It's required after activating SELinux**):

```bash
shutdown -r now
```

#### Checking SELinux Status

To view the current state of your new SELinux host after rebooting. Run the commands `getenforce` and `sestatus`. Both commands will show the state of SELinux. The only difference is that `sestatus` will provide more detailed output.

```bash
getenforce
```

If SELinux is installed and active, you'll see:

```text
Enforcing
```

This means that your SELinux is ready to work and it’s active.

### AppArmor

AppArmor is often easier to configure and is commonly used on Ubuntu-based distributions. AppArmor works by confining programs to a limited set of resources. It is default on Ubuntu and provides security without requiring complex policy management.

#### Installing AppArmor

To install AppArmor, run the following command:

```bash
apt install apparmor
```

This command will install `apparmor` and its necessary dependencies on your server.

> **Important:** Check with your distribution's package manager for the appropriate installation command.

#### Checking AppArmor Status

To check the status of AppArmor use:

```bash
aa-status
```

If active, you'll see something like:

```text
apparmor module is loaded.
105 profiles are loaded.
10 profiles are in enforce mode.
[...]
```

#### Managing AppArmor Profiles

`AppArmor` uses profiles to define allowed behaviors for specific applications. The profiles are usually located in `/etc/apparmor.d/`. Also,

The `aa-status` command will show which profiles are loaded and in which mode (enforce or complain). To list all loaded profiles:

```bash
aa-status
```

To disable a specific profile (e.g., for nginx):

```bash
aa-disable /etc/apparmor.d/usr.sbin.nginx
```

To enable a profile:

```bash
aa-enforce /etc/apparmor.d/usr.sbin.nginx
```

#### Disabling AppArmor (if switching to SELinux)

To permanently disable AppArmor before switching to SELinux:

```bash
systemctl stop apparmor
systemctl disable apparmor
apt remove apparmor
```

Then, **reboot** before installing SELinux.

## kernel configuration

While important for system hardening, kernel tweaks are typically advanced and often require deeper knowledge of system operations. It's critical but can be tackled once the primary security measures are in place.

The `sysctl` utility is used to modify kernel parameters at runtime. These parameters control a wide variety of aspects of system performance and behavior. By editing the `sysctl.conf` or adding specific configuration files in `/etc/sysctl.d/`, you can harden the Linux kernel to better defend against various attacks and secure your server.

Below is an example of how you can secure your server using `sysctl` settings. We’ll create and edit a custom configuration file to ensure that the settings persist across reboots.

### Create and Edit the `sysctl` Configuration File

To begin, create a custom configuration file:

```bash
vim /etc/sysctl.d/secure-server.conf
```

You can now add the following settings to the file:

#### _Prevent IP Spoofing:_

The `rp_filter` (reverse path filtering) recommended setting is to enable strict mode to prevent IP spoofing from DDos attacks. If using asymmetric routing or other complicated routing, then loose mode is recommended (3).

```text
net.ipv4.conf.all.rp_filter=1
net.ipv4.conf.default.rp_filter=1
net.ipv6.conf.all.rp_filter=1
net.ipv6.conf.default.rp_filter=1

```

#### _Disable Accepting ICMP Redirects:_

ICMP redirects are used by routers to inform hosts of a better route to reach a destination. However, this can be exploited by attackers in Man-in-the-Middle (MITM) attacks. By disabling ICMP redirects, we ensure that the server won't participate in these redirects.

This feature of the IPv4 protocol has few legitimate uses. It should be disabled unless absolutely required."

```text
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0
net.ipv6.conf.all.secure_redirects = 0
```

#### _Disable Sending ICMP Redirects:_

Just as you shouldn’t accept ICMP redirects, you should also prevent your server from sending them. These messages contain information from the system’s route table possibly revealing portions of the network topology. This further secures the server by ensuring that it does not act as a router in the network.

The ability to send ICMP redirects is only appropriate for systems acting as routers, thus, it should be disabled unless absolutely required.

```text
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv6.conf.all.send_redirects = 0
net.ipv6.conf.default.send_redirects = 0
```

#### _Disable IP Source Routing:_

Source routing allows a sender to specify the route that packets should take through the network. This feature can be exploited by attackers to bypass firewalls or other network defenses. This setting should be disabled unless it is absolutely required, such as when IPv4 forwarding is enabled and the system is legitimately functioning as a router.

```text
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0
```

#### _Disable Router Advertisements:_

An illicit router advertisement message could result in a man-in-the-middle attack.

```text
net.ipv6.conf.all.accept_ra = 0
net.ipv4.conf.all.accept_ra = 0
```

#### _Enable Martian Packets Log:_

Martian packets are packets with addresses that are reserved for special use and are not routable on the network. These are often signs of malicious activity or misconfiguration. By logging these packets, you can detect abnormal traffic that might indicate an attack or misconfigured device.

```text
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
net.ipv6.conf.all.log_martians = 1
net.ipv6.conf.default.log_martians = 1
```

#### _Disable echo requests:_

Responding to broadcast (ICMP) echoes facilitates network mapping and provides a vector for amplification attacks.

Ignoring ICMP echo requests (pings) sent to broadcast or multicast addresses makes the system slightly more difficult to enumerate on the network.

```text
net.ipv4.icmp_echo_ignore_broadcasts = 1
net.ipv6.icmp_echo_ignore_broadcasts = 1
```

#### _Disable IP Forwarding:_

Routing protocol daemons are typically used on routers to exchange network topology information with other routers. If this capability is used when not required, system network information may be unnecessarily transmitted across the network.

```text
net.ipv4.ip_forward = 0
net.ipv6.conf.all.forwarding = 0
```

#### _Prevent TCP SYN Flood attack:_

A TCP SYN flood attack can cause a denial of service by filling a system’s TCP connection table with connections in the SYN_RCVD state. Syncookies can be used to track a connection when a subsequent ACK is received, verifying the initiator is attempting a valid connection and is not a flood source. This feature is activated when a flood condition is detected, and enables the system to continue servicing valid connection requests.

```text
net.ipv4.tcp_syncookies = 1
net.ipv6.tcp_syncookies = 1
```

## Conclusion

Setting up SSH on a server is a fundamental step in securing remote access. By properly configuring SSH, disabling root login, using key-based authentication, and tweaking security settings, you can significantly reduce the risk of unauthorized access.

If you haven't already, consider taking additional security measures, such as using a firewall (e.g., ufw or iptables), implementing Fail2Ban to prevent brute-force attacks, and keeping your system updated.

By following these best practices, you ensure that your SSH server remains both secure and efficient, allowing you to manage your systems with confidence. Remember that security is an ongoing process, and it's essential to stay informed about the latest security threats and updates. Regularly review and update your security measures to protect your server from potential vulnerabilities.
