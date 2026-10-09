# Pterodactyl Installation and Upgrade Guide

A simple guide for setting up a VPS, installing Pterodactyl Panel and Wings, and reviewing the v2 upgrade process.

## 1. Create a VPS and connect your domain

- Create a VPS with root access. The dependency commands below target **Ubuntu 24+**.
- Purchase or use an existing domain.
- Connect your domain to Cloudflare if you want to manage DNS there.
- Create a DNS record for your Panel hostname, such as `panel.example.com`, pointing to your VPS public IP.
- If Wings runs on another VPS, prepare its hostname and DNS record too.

Replace example hostnames and IP addresses with your own values.

## 2. Open your VPS terminal

Use your VPS provider's console, or connect using SSH:

```bash
ssh root@YOUR_VPS_IP
```

Run the following installation commands as root. If you connect as another user, switch first:

```bash
sudo -i
```

## 3. Open the documentation

[Pterodactyl documentation](https://docs.pterodactyl.io/)

Keep the documentation open while installing so you can check the requirements for your operating system and selected version.

## 4. Install dependencies

Reference: [Dependency installation for v1](https://docs.pterodactyl.io/v1/panel/getting-started#example-dependency-installation)

These are the Ubuntu 22.04 commands supplied for this guide:

```bash
# Update the package list first
apt update

# Add the add-apt-repository command and required tools
apt -y install software-properties-common curl apt-transport-https ca-certificates gnupg

# Add the PHP repository for Ubuntu 22.04
LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php

# Add the official Redis APT repository
curl -fsSL https://packages.redis.io/gpg | gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/redis.list

# Update repositories
apt update

# Install dependencies
apt -y install php8.3 php8.3-{common,cli,gd,mysql,mbstring,bcmath,xml,fpm,curl,zip} mariadb-server nginx tar unzip git redis-server
```

## 5. Run the community installer

Repository: [pterodactyl-installer](https://github.com/pterodactyl-installer/pterodactyl-installer)

This is a community installer, separate from the official Pterodactyl project. Review its instructions before running it with root access.

```bash
bash <(curl -s https://pterodactyl-installer.se)
```

Choose the option for your setup:

- **Install the Panel** for the web dashboard.
- **Install Wings** for the service that manages game servers.
- **Install both** if the Panel and Wings will share one VPS.

Follow the prompts for your domain, database, administrator account, and HTTPS configuration. Save your account details securely.

## 6. Finish Panel and Wings setup

After installing the v1 Panel and Wings:

1. Open your Panel domain and sign in as administrator.
2. Configure a location, node, and server allocations in the Panel.
3. Apply the node configuration to Wings using the node's configuration instructions.
4. Check that the node connects and a test game server starts.

Installing both components is only the first part; the node must also be configured.

## 7. Upgrade from v1 to v2 — optional

**Documentation checked: 9 October 2026.** The documentation currently marks v2 as pre-release and recommends testing the upgrade on a copy.

Use these links for different tasks:

| Task | Documentation |
| --- | --- |
| Upgrade a v1 Panel to v2 | [Upgrading from v1](https://docs.pterodactyl.io/v2/upgrading/upgrading-from-v1) |
| Update an existing v2 Panel | [Updating v2](https://docs.pterodactyl.io/v2/panel/updating) |

Before moving from v1 to v2:

- Start with Panel 1.12.0 or newer.
- Back up the database, `.env`, and Panel files; preserve the existing `APP_KEY`.
- Check PHP 8.3 or newer, the `intl` extension, and Node.js 22.12 or newer.
- Follow the migration guide's separate-directory installation and webserver changes.
- Do not use `php artisan p:upgrade` to migrate v1 to v2.

For PHP 8.3, add the required extension:

```bash
apt -y install php8.3-intl
systemctl restart php8.3-fpm
php -m | grep -i intl
```

Follow the full migration guide in order. The v2 update page alone is not a v1-to-v2 migration procedure.

## 8. Troubleshooting

Community reference supplied for this guide:

[Pterodactyl troubleshooting notes](https://gist.github.com/yuuslokrobjakkroval/64b9f5773f20fd4519746cf56c3b2ffa)

The linked Gist could not be verified while preparing this file. Check that its instructions match your installed version before applying a fix.

For an error, record the failed command and its full error output. Check Panel logs and service status:

```bash
cd /var/www/pterodactyl
ls -lt storage/logs/
systemctl status nginx php8.3-fpm mariadb redis-server pteroq wings --no-pager
```

If Panel and Wings are on separate VPSs, check each service on the machine where it is installed.

## Completion checklist

- [ ] VPS created and accessible
- [ ] Domain points to the correct VPS
- [ ] Dependencies installed
- [ ] Panel installed and accessible
- [ ] HTTPS configured
- [ ] Wings installed and connected
- [ ] Test game server starts
- [ ] Backups available before an upgrade
- [ ] Correct version-specific guide reviewed

