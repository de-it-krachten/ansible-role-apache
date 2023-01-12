[![CI](https://github.com/de-it-krachten/ansible-role-apache/workflows/CI/badge.svg?event=push)](https://github.com/de-it-krachten/ansible-role-apache/actions?query=workflow%3ACI)


# ansible-role-apache

Manages apache webserver 



## Dependencies

#### Roles
None

#### Collections
- community.general

## Platforms

Supported platforms

- Red Hat Enterprise Linux 7<sup>1</sup>
- Red Hat Enterprise Linux 8<sup>1</sup>
- Red Hat Enterprise Linux 9<sup>1</sup>
- CentOS 7
- RockyLinux 8
- RockyLinux 9
- OracleLinux 8
- OracleLinux 9
- AlmaLinux 8
- AlmaLinux 9
- Debian 10 (Buster)
- Debian 11 (Bullseye)
- Ubuntu 18.04 LTS
- Ubuntu 20.04 LTS
- Ubuntu 22.04 LTS
- Fedora 35
- Fedora 36

Note:
<sup>1</sup> : no automated testing is performed on these platforms

## Role Variables
### defaults/main.yml
<pre><code>
# Main path to create vhosts into
apache_wwwdir: /var/www
apache_vhostdir: '{{ apache_wwwdir }}'

# List of vhosts
apache_vhosts: []

# SSL/TLS keys
apache_ssl: true
apache_ssl_key: "{{ apache_ssl_priv_path }}/{{ apache_fqdn }}.key"
apache_ssl_crt: "{{ apache_ssl_certs_path }}/{{ apache_fqdn }}.crt"
apache_ssl_chain: "{{ apache_ssl_certs_path }}/{{ apache_fqdn }}.chain.crt"
apache_ssl_fullchain: "{{ apache_ssl_certs_path }}/{{ apache_fqdn }}.fullchain.crt"

# SSL/TLS settings
apache_ssl_settings:
  Listen: "443 {{ '127.0.0.1:' if (sslh_active is defined and sslh_active|bool) }}https"
  SSLCertificateFile: '{{ apache_ssl_crt }}'
  SSLCertificateKeyFile: '{{ apache_ssl_key }}'
  SSLCertificateChainFile: '{{ apache_ssl_chain }}'
  SSLProtocol: 'all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1'
  SSLCipherSuite: 'HIGH:!aNULL:!MD5:!3DES:!SEED:!IDEA'
  SSLHonorCipherOrder: 'on'

# Defaul firewall ports
apache_fw_ports:
  - { port: 80, proto: tcp }
  - { port: 443, proto: tcp }
</pre></code>

### defaults/family-RedHat.yml
<pre><code>
# SSL private + certificate store
apache_ssl_certs_path: /etc/pki/tls/certs
apache_ssl_priv_path: /etc/pki/tls/private

# Packages required
apache_packages:
  - httpd
  - mod_ssl
  - openssl

# log directory
apache_logdir: /var/log/httpd

# Apache service
apache_service: httpd

# Apache configuration directory
apache_conf_dir: /etc/httpd/conf.d

# Apache SSL configuration
apache_ssl_conf: /etc/httpd/conf.d/ssl.conf

# Default user / group
apache_user: apache
apache_group: apache
</pre></code>

### defaults/family-Debian.yml
<pre><code>
# SSL private + certificate store
apache_ssl_certs_path: /etc/ssl/certs
apache_ssl_priv_path: /etc/ssl/private

# Packages required
apache_packages:
  - apache2
  - apache2-utils
  - openssl

# log directory
apache_logdir: /var/log/apache2

# Apache service
apache_service: apache2

# Apache configuration directory
apache_conf_dir: /etc/apache2/sites-available

# Apache SSL configuration
apache_ssl_conf: /etc/apache2/sites-available/default-ssl.conf

# Default user / group
apache_user: www-data
apache_group: www-data
</pre></code>




## Example Playbook
### molecule/default/converge.yml
<pre><code>
- name: sample playbook for role 'apache'
  hosts: all
  become: "yes"
  vars:
    openssl_fqdn: server.example.com
    openssl_fqdn_additional: ['vhost1.example.com', 'vhost2.example.com']
    apache_fqdn: server.example.com
    apache_ssl_key: "{{ openssl_server_key }}"
    apache_ssl_crt: "{{ openssl_server_crt }}"
    apache_ssl_chain: "{{ openssl_server_crt }}"
    apache_index_html: True
    apache_vhosts: "[{'vhost': 'vhost1.example.com', 'alias': 'vhost1-alias.example.com', 'domain': 'vhost1.example.com', 'template': 'vhost.conf.j2', 'listen': '*', 'port': '443', 'ssl': True, 'ssl_copy': True, 'ssl_key': 'files/vhost1.example.com.key', 'ssl_crt': 'files/vhost1.example.com.crt', 'ssl_chain': 'files/vhost1.example.com.crt'}, {'vhost': 'vhost2.example.com', 'alias': 'vhost2-alias.example.com', 'domain': 'vhost2.example.com', 'template': 'vhost.conf.j2', 'listen': '*', 'port': '443', 'ssl': True, 'ssl_copy': True, 'ssl_key': '{{ openssl_server_key }}', 'ssl_crt': '{{ openssl_server_crt }}', 'ssl_chain': '{{ openssl_server_crt }}'}]"
  roles:
    - deitkrachten.openssl
  tasks:
    - name: Include role 'apache'
      ansible.builtin.include_role:
        name: apache
</pre></code>
