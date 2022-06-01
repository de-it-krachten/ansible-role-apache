[![CI](https://github.com/de-it-krachten/ansible-role-apache/workflows/CI/badge.svg?event=push)](https://github.com/de-it-krachten/ansible-role-apache/actions?query=workflow%3ACI)


# ansible-role-apache

Manages apache webserver 


## Platforms

Supported platforms

- Red Hat Enterprise Linux 7<sup>1</sup>
- Red Hat Enterprise Linux 8<sup>1</sup>
- CentOS 7
- RockyLinux 8
- AlmaLinux 8<sup>1</sup>
- Debian 10 (Buster)
- Debian 11 (Bullseye)
- Ubuntu 18.04 LTS
- Ubuntu 20.04 LTS
- Ubuntu 22.04 LTS

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

### vars/family-RedHat.yml
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
apache_ssl_conf: "{{ apache_conf_dir }}/ssl.conf"
</pre></code>

### vars/family-Debian.yml
<pre><code>
# SSL private + certificate store
apache_ssl_certs_path: /etc/pki/tls/certs
apache_ssl_priv_path: /etc/pki/tls/private

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
apache_ssl_conf: "{{ apache_conf_dir }}/default-ssl.conf"
</pre></code>



## Example Playbook
### molecule/default/converge.yml
<pre><code>
- name: sample playbook for role 'apache'
  hosts: all
  vars_files:
    - vars.yml
  pre_tasks:
    - name: Create 'remote_tmp'
      file:
        path: /root/.ansible/tmp
        state: directory
        mode: "0700"
  roles:
    - openssl
  tasks:
    - name: Include role 'apache'
      include_role:
        name: apache
</pre></code>
