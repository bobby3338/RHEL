##The IdM Infrastructure Topology
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
