# Nginx Setup for pysshauthz

This playbook will configure nginx and LetsEncrypt's certbot on the virtual machine.

## Requirement
* [Openstack cli-client designate module](https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html)


## Setup nginx

### Configure DNS Entry
This section requires the designate client to be installed and that DNS zone is configured.
If the DNS entry is already configured, skip this section and [run the playbook role](#Run-the-role)

1. List the zones available for your project
```
openstack zone list
```

The command should return a list of zones on your particular allocation. One of the zones should match the domain in the `default_url` in `group_vars/all` file.
```
+--------------------------------------+----------------------------------+---------+------------+--------+--------+
| id                                   | name                             | type    |     serial | status | action |
+--------------------------------------+----------------------------------+---------+------------+--------+--------+
| <ZONE ID HERE>                       | zone.domain.                     | PRIMARY | 1234569012 | ACTIVE | NONE   |
+--------------------------------------+----------------------------------+---------+------------+--------+--------+
```
2. Create an A record that matches the entry in `default_url`:

```
openstack recordset create --type A --record ip.address.here zone.domain. hostname
```

### Run the role
```
ansible-playbook -i inventory play.yml --tags=setup-nginx
```

## Configuring Other Web Servers
This playbook does not cover the configuration of other web servers, but here's some general:

1. Configure mapping for request uri to access token parameter variable and access token header parameter as such: 
```
# Example of nginx syntax

map $request_uri $access_token_param {
   "~\?access_token=(?<token>[A-Za-z0-9]+)" "Bearer $token";
}
map $access_token_param $access_token_header {
   ~Bearer $access_token_param;
   default $http_authorization;
}
```

2. Configure the proxy pass for the web server from port 80 and 443 to port 8080 for location `/pysshauthz/`
```
# Example of nginx syntax
location /pysshauthz/ {
  proxy_pass http://localhost:8080/;
  proxy_http_version 1.1;
  proxy_set_header Authorization $access_token_header;
  proxy_set_header   X-Forwarded-Proto    $scheme;
  proxy_set_header X-Forwarded-Host $host:$server_port;
  proxy_set_header X-Forwarded-Server $host;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

3. Configure the SSL certificates
```
# Example of nginx syntax
listen [::]:443 ssl ipv6only=on; # managed by Certbot
listen 443 ssl; # managed by Certbot
ssl_certificate /etc/letsencrypt/live/www.example.com/fullchain.pem; # managed by Certbot
ssl_certificate_key /etc/letsencrypt/live/www.example.com/privkey.pem; # managed by Certbot
include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
```
