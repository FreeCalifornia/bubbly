# Bubbly

TODO: Copy NGINX Configs and update instructions.

### For configuring Certbot with Nginx

[![Build Status](https://travis-ci.org/eustasy/bubbly.svg?branch=master)](https://travis-ci.org/eustasy/bubbly)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/ecf6627cc61b43d6a1b52b9991b86680)](https://www.codacy.com/app/lewisgoddard/bubbly?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=eustasy/bubbly&amp;utm_campaign=Badge_Grade)
[![Code Climate](https://codeclimate.com/github/eustasy/bubbly/badges/gpa.svg)](https://codeclimate.com/github/eustasy/bubbly)
[![Bountysource](https://www.bountysource.com/badge/tracker?tracker_id=25541893)](https://www.bountysource.com/teams/eustasy/issues?tracker_ids=25541893)

If you want an A+ score on Qualys [SSL Labs](https://www.ssllabs.com/ssltest/index.html), then this is what you'll need to do. You won't need any familiarity with [Certbot](https://github.com/certbot/certbot), [Let's Encrypt](https://letsencrypt.org/), the ACME spec, or SSL in general, just basic Nginx configuration.

**1. Clone Certbot and Bubbly**

We'll start off by cloning the projects into the home folder with git.

```
cd &&
sudo apt install git &&
git clone https://github.com/certbot/certbot &&
git clone https://github.com/eustasy/bubbly
```

**2. Generate Statics**

Generate the static keys once per server.

```
chmod 755 ~/bubbly/bubbly-generate-statics.sh &&
~/bubbly/bubbly-generate-statics.sh
```

As it will warn, this will take a while.

Have a seat.

**3. Replace Domains & Configure**

When you've gone and made something in the 15 minutes that could well take, or you've just set up a new SSH session, replace the instances of `example.com` in the Nginx and Crontab configuration files in `*.conf` with your actual domain name. Also take a look at the `[OPTION]`s and `[WARNING]`s.

```
nano ~/bubbly/*.conf
```

**4. Enable Verification**

```
sudo ln -s ~/bubbly/nginx.verify.conf /etc/nginx/sites-enabled/nginx.verify.conf &&
sudo nginx -t &&
sudo service nginx reload
```

Alternatively, you can simply copy the location block from `nginx.verify.conf`, if you have an existing site you want to continue working while we upgrade.

**5. Fetch Certificates**

Fetch your certificates like this:

```
~/bubbly/bubbly-renew-ssl.sh -d example.com -d www.example.com
```

It will ask for the root password, and an email address, so hang around, it shouldn't take more than a few seconds.

**6. Start using the Certificates**

Now we need to link in the actual site, test it, and reload.

```
sudo rm /etc/nginx/sites-enabled/nginx.verify.conf &&
sudo ln -s ~/bubbly/nginx.conf /etc/nginx/sites-enabled/nginx.conf &&
sudo nginx -t && sudo service nginx reload
```

**6. Automate Renewal**

Install the edited `crontab.conf` for automatic renewal.

```
cat ~/bubbly/crontab.conf > /tmp/bubbly-crontab &&
crontab -l >> /tmp/bubbly-crontab &&
crontab /tmp/bubbly-crontab
```

This is important, since Let's Encrypt certificates expire in three months.

---

![Screenshot from 2015-11-05 04:16:13.png](https://raw.githubusercontent.com/eustasy/certbot-with-nginx/master/Screenshot%20from%202015-11-05%2004%3A16%3A13.png "Screenshot from 2015-11-05 04:16:13.png")
