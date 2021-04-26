---
title: Azure NetApp Files için NFS istemcisi yapılandırma | Microsoft Docs
description: NFS istemcilerinin Azure NetApp Files ile kullanmak üzere nasıl yapılandırılacağını açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: e0b86a7014af42f2ffb067c2de797f270a5b1855
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967481"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Azure NetApp Files için NFS istemcisini yapılandırma

Bu makalede açıklanan NFS istemci yapılandırması, [NFSv 4.1 Kerberos şifrelemesini yapılandırırken](configure-kerberos-encryption.md) veya [bir çift protokol birimi oluşturduğunuzda](create-volumes-dual-protocol.md)kurulum 'un bir parçasıdır. Azure NetApp Files ile kullanılabilecek çok çeşitli Linux dağıtımları vardır. Bu makalede, daha yaygın olarak kullanılan ortamların ikisi için yapılandırma açıklanmaktadır: RHEL 8 ve Ubuntu 18,04. 

## <a name="requirements-and-considerations"></a>Gereksinimler ve önemli noktalar  

Kullandığınız Linux Flavor ne olursa olsun, aşağıdaki konfigürasyonlar gereklidir:

* Zaman eğimiyle ilgili sorunları önlemek için bir NTP İstemcisi yapılandırın.
* Ad çözümlemesi için Linux istemcisinin DNS girişlerini yapılandırın.  
    Bu yapılandırma, "A" (ileri) kaydı ve PTR (ters) kaydını içermelidir. 
* Etki alanına katılması için, hedef Active Directory Linux istemcisi için bir bilgisayar hesabı oluşturun (bölge JOIN komutu sırasında oluşturulur). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Aşağıdaki komutlarda kullanılan değişken, hedeflenen kuruluş biriminde bir bilgisayar hesabı oluşturmak için izinlere veya temsilciliine sahip bir kullanıcı hesabı olmalıdır.

## <a name="rhel-8-configuration"></a>RHEL 8 yapılandırması 

Bu bölümde NFSv 4.1 Kerberos şifrelemesi ve ikili protokol için gereken RHEL yapılandırmalarının açıklanmaktadır.  

Bu bölümdeki örneklerde aşağıdaki etki alanı adı ve IP adresi kullanılır:  

* Etki alanı adı: `contoso.com`
* Özel IP: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>NFSv 4.1 Kerberos Şifrelemesi kullanıyorsanız RHEL 8 yapılandırması

1. `/etc/resolv.conf`Doğru DNS sunucusuyla yapılandırın.  

    Örnek:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. DNS ileriye ve geriye doğru arama bölgesi için DNS sunucusuna NFS istemci kaydını ekleyin.

3. DNS 'i doğrulamak için NFS istemcisinden aşağıdaki komutları kullanın:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Paketleri yükler:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  NTP istemcisini yapılandırın.  

    RHEL 8 varsayılan olarak zaman hatası kullanır. [ `Chrony` NTP 'yi yapılandırmak Için paketini kullanma](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)konusundaki yapılandırma yönergelerini takip edin.

6.  Active Directory etki alanına ekleyin:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Örnek: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    `default_realm`' De sağlanan bölge olarak ayarlandığından emin olun `/etc/krb5.conf` .  Aksi takdirde, `[libdefaults]` Aşağıdaki örnekte gösterildiği gibi dosyanın bölümüne ekleyin:
    
    `default_realm = CONTOSO.COM`

7. Tüm NFS hizmetlerini yeniden başlatın:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    Yeniden başlatma, `“mount.nfs: an incorrect mount option was specified”` Kerberos bağlama sırasında hata koşulunu önler.

8. `kinit`Anahtarları almak için kullanıcı hesabıyla komutunu çalıştırın: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Örnek:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>İkili protokol kullanıyorsanız RHEL 8 yapılandırması

Aşağıdaki adımlar isteğe bağlıdır. Yalnızca NFS istemcisinde Kullanıcı eşlemeyi kullanıyorsanız adımları gerçekleştirmeniz gerekir: 

1. [NFSv 4.1 Kerberos şifrelemesi bölümünü kullanıyorsanız RHEL 8 yapılandırmasında](#rhel8_nfsv41_kerberos) açıklanan tüm adımları izleyin.   

2. SSSD yapılandırma dosyasında IP adresini kullanmak yerine, AD için tam etki alanı adı (FQDN) kullanmak üzere/etc/hosts dosyanıza statik bir DNS kaydı ekleyin:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. AD LDAP sunucusundan tanımlayıcıları çözümlemek için etki alanları için ek bir bölüm ekleyin:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Girişe sahip olduğunuzdan emin olun `/etc/nsswitch.conf` `sss` :   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Hizmeti yeniden başlatma `sssd` ve önbelleği temizleme:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. İstemcinizin LDAP sunucusuyla tümleşik olduğundan emin olmak için test edin:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu yapılandırması   

Bu bölümde NFSv 4.1 Kerberos şifrelemesi ve ikili protokol için gereken Ubuntu yapılandırmalarının açıklanmaktadır. 

Bu bölümdeki örneklerde aşağıdaki etki alanı adı ve IP adresi kullanılır:  

* Etki alanı adı: `contoso.com`
* Özel IP: `10.6.1.4`

1. `/etc/resolv.conf`Doğru DNS sunucusu ile yapılandırın:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. DNS ileriye ve geriye doğru arama bölgesi için DNS sunucusuna NFS istemci kaydı ekleyin.
 
    DNS 'i doğrulamak için NFS istemcisinden aşağıdaki komutları kullanın:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Paketleri yükler:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    İstendiğinde, `$DOMAIN.NAME` varsayılan Kerberos bölgesi olarak giriş (örneğin, büyük harf kullanarak `CONTOSO.COM` ).

4. Hizmeti yeniden başlatın `rpc-gssd.service` : 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18,04, varsayılan olarak zaman hatası kullanır. [Ubuntu Bionic](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)içindeki yapılandırma yönergelerinden sonra, NTP 'yi yapılandırmak için zaman hatası kullanımı.

6. Active Directory etki alanına ekleyin:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Örnek:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. `kinit`Bilet almak için kullanıcıyla birlikte gerçekleştirin: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Örnek:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>İkili protokol kullanıyorsanız Ubuntu yapılandırması  

Aşağıdaki adımlar isteğe bağlıdır.  Yalnızca NFS istemcisinde Kullanıcı eşlemesini kullanmak istiyorsanız bu adımları gerçekleştirmeniz gerekir:  

1. Yüklü paketleri yükseltmek için aşağıdaki komutu çalıştırın:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Aşağıdaki örnek, örnek değerleri kullanır. Komut sizden giriş yapmanızı isterse, ortamınıza bağlı olarak giriş sağlamanız gerekir. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Hizmetini yeniden başlatıp etkinleştirmek için aşağıdaki komutu çalıştırın:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

Aşağıdaki örnek, bir LDAP kullanıcısı için Ubuntu LDAP istemcisinden AD LDAP sunucusunu sorgular `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Sonraki adımlar  

* [Azure NetApp Files için NFS birimi oluşturma](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files için çift protokol birimi oluşturma](create-volumes-dual-protocol.md)

