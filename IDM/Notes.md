##The IdM Infrastructure Topology
```
  IdM server, IdM replica, support up to 60 replica in single IdM topology
    service SRV record,
  IdM client
IdM domain: Linux, Unix, intergration with active directory
  IdM client
    use the kerberos protocol to request authentication
    request tickets fo SSO
    use LDAP service to query identity and policy information
    Uses the DNS service to discover IdM server
    Detect services and information to connect to them
  IdM Server
    Certificate Authority
    Kerberos
    LDAP
    DNS
    Samba
    OTP
    System Security Services Daemon (SSSD)
    Certmonger
  IdM server roles
    Certificate Authority Server
    DNS server
    Key Recovery Authority Server
    Active Directory controller / trust agent
```
```
  ssh idm
  sudo -i
  rpm -qa | grep ipa
  ipa-server-install
  systemctl status kadmin                                        # IdM admin
  systemctl status krb5kdc.service                                # Kerberos
  systemctl status dirsrv@LAB-EXAMPLE-COM.service                # LDAP service
  systemctl status pki-tomcat@pki-tomcat.service                  # PKI tomcat service
  systemctl status httpd                                           # web console

 ipa-client-install
  cat /etc/krb5.conf
  cat/etc/sssd/sssd.conf

  ipa -vv user-show admin
  systemctl stop ipa.service
  ipactl status
    Directory Service: RUNNING
    krb5kdc Service: RUNNING
    kadmin Service: RUNNING
    httpd Service: RUNNING
    ipa-custodia Service: RUNNING
    pki-tomcatd Service: RUNNING
    ipa-otpd Service: RUNNING
    ipa: INFO: The ipactl command was successful
  ipa help topics
  ipa help user
  ipa user-find
  ipa env
  ipa -vv group-show admins
```
## SSSD / sssctl / sss_cache / sss_override / sss_seed
```
nss_ldap 
pam_krb5
pam_ccreds

NSS (name service switch )
authconfig replaced by authselect in RHEl8
sssd-tools installed for sssctl command
/etc/nsswitch.conf
  passwd:     sss files systemd                                                   # sssd is queried before /etc/passwd and /etc/group
  shadow:     files
  group:      sss files systemd
  hosts:      files dns myhostname
  services:   files sss
  netgroup:   sss
  automount:  files sss

  aliases: files
  ethers: files
  gshadow: files
  networks: files dns
  protocols: files
  publickey: files
  rpc: files

  /etc/pam.conf                     # service type control module-path module-arguments
  /etc/pam.d/                        # drop in path
  /etc/pam.d/sshd
  /etc/security/sepermit.conf        # can only log in in enforcing mode
  /var/run/nolgoin /etc/noglin       # pam_nologin.so prevents all users other than root from logging in when either of the files exist
  /etc/motd                          # message of the day
  man 8 pam_unix
# sshd verification
  cat /etc/pam.d/sshd
    auth  substack  password-auth
  cat /etc/pam.d/password-auth
    auth  sufficient  pam_unix.so nullok

  man 8 pam_unix          search nullok
  ssh admin@idm
  su -
  sssctl domain-list
  sssctl user-show admin]
  authselect list
    mininal / sssd / winbind
  authselect show sssd
  authselect test -a sssd with-mkhomedir with-sudo

# Kerberos Authentication Protocol - secure not sending password over the network
  Ticket Granting Service / Ticket Granting Ticket / Key Distribution Center
  



