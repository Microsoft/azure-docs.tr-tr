---
title: Azure AD Domain Services bir RHEL VM 'ye katma | Microsoft Docs
description: Red Hat Enterprise Linux bir sanal makineyi Azure AD Domain Services yönetilen bir etki alanına nasıl yapılandıracağınızı ve katılacağınızı öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: 9472abd7a16c887a796e36b8190e8530c84dafa9
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755719"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Red Hat Enterprise Linux sanal makinesini Azure AD Etki Alanı Hizmetleri tarafından yönetilen bir etki alanına katma

Kullanıcıların Azure 'da tek bir kimlik bilgileri kümesi kullanarak sanal makinelerde (VM) oturum açmalarına izin vermek için, VM 'Leri Azure Active Directory Domain Services (AD DS) yönetilen bir etki alanına katabilirsiniz. Bir VM 'yi Azure AD DS yönetilen bir etki alanına katdığınızda, etki alanındaki Kullanıcı hesapları ve kimlik bilgileri, sunucuları oturum açmak ve yönetmek için kullanılabilir. Azure AD DS yönetilen etki alanındaki grup üyelikleri, VM 'deki dosya ve hizmetlere erişimi denetlemenize olanak sağlamak için de uygulanır.

Bu makalede bir Red Hat Enterprise Linux (RHEL) VM 'sini Azure AD DS yönetilen bir etki alanına nasıl katılabilmeniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [bir Azure Active Directory Domain Services örneği oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>RHEL Linux VM 'si oluşturma ve bu makineye bağlanma

Azure 'da var olan bir RHEL Linux sanal makinesi varsa, SSH kullanarak buna bağlanın ve sonra [VM 'yi yapılandırmaya başlamak](#configure-the-hosts-file)için sonraki adıma geçin.

Bir RHEL Linux sanal makinesi oluşturmanız veya bu makaleyle kullanmak üzere bir test sanal makinesi oluşturmak istiyorsanız aşağıdaki yöntemlerden birini kullanabilirsiniz:

* [Azure portalda](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM 'yi oluştururken, sanal makınenın Azure AD DS yönetilen etki alanıyla iletişim kurabildiğinden emin olmak için sanal ağ ayarlarına dikkat edin:

* Sanal makineyi aynı veya Azure AD Domain Services etkinleştirdiğiniz bir eşlenen sanal ağa dağıtın.
* VM 'yi Azure AD Domain Services örneğinden farklı bir alt ağa dağıtın.

VM dağıtıldıktan sonra, SSH kullanarak VM 'ye bağlanma adımlarını izleyin.

## <a name="configure-the-hosts-file"></a>Hosts dosyasını yapılandırma

VM ana bilgisayar adının yönetilen etki alanı için doğru yapılandırıldığından emin olmak için, */etc/hosts* dosyasını düzenleyin ve ana bilgisayar adını ayarlayın:

```console
sudo vi /etc/hosts
```

*Konaklar* dosyasında, *localhost* adresini güncelleştirin. Aşağıdaki örnekte:

* *contoso.com* , Azure AD DS yönetilen etkı alanının DNS etki alanı adıdır.
* *RHEL for* , yönetilen etki alanına katıldığınız RHEL for VM 'nizin ana bilgisayar adıdır.

Bu adları kendi değerlerinizle güncelleştirin:

```console
127.0.0.1 rhel rhel.contoso.com
```

İşiniz bittiğinde, düzenleyicinin `:wq` komutunu kullanarak *konaklar* dosyasını kaydedin ve kapatın.

## <a name="install-required-packages"></a>Gerekli paketleri yükleme

VM 'nin VM 'ye Azure AD DS tarafından yönetilen etki alanına katılması için bazı ek paketlere ihtiyacı vardır. Bu paketleri yüklemek ve yapılandırmak için `yum` kullanarak etki alanına ekleme araçlarını güncelleştirin ve yapılandırın:

 **RHEL 7** 

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

 **RHEL 6** 

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>VM 'yi yönetilen etki alanına katma

Gerekli paketler VM 'ye yüklendikten sonra, VM 'yi Azure AD DS yönetilen etki alanına katın.
 
  **RHEL 7**
     
1. Azure AD DS yönetilen etki alanını saptamak için `realm discover` komutunu kullanın. Aşağıdaki örnek, *contoso.com*bölgesini bulur. Azure AD DS yönetilen etki alanı adınızı tüm büyük harfle belirtin:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   @No__t_0 komutu Azure AD DS yönetilen etki alanınızı bulamazsa, aşağıdaki sorun giderme adımlarını gözden geçirin:
   
    * Etki alanına VM 'den erişilebildiğinden emin olun. Pozitif bir yanıtın döndürülüp döndürülmediğini görmek için `ping contoso.com` deneyin.
    * VM 'nin aynı veya Azure AD DS yönetilen etki alanının kullanılabildiği eşlenmiş bir sanal ağa dağıtıldığını denetleyin.
    * Sanal ağ için DNS sunucu ayarlarının, Azure AD DS yönetilen etki alanının etki alanı denetleyicilerini işaret etmek üzere güncelleştirildiğinden emin olun.

1. Şimdi `kinit` komutunu kullanarak Kerberos başlatın. *AAD DC Administrators* grubuna ait olan bir kullanıcı belirtin. Gerekirse, [Azure AD 'de bir gruba bir kullanıcı hesabı ekleyin](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Azure AD DS yönetilen etki alanı adının tümü büyük harfle girilmelidir. Aşağıdaki örnekte, `contosoadmin@contoso.com` adlı hesap, Kerberos 'u başlatmak için kullanılır. *AAD DC Administrators* grubunun üyesi olan kendi kullanıcı hesabınızı girin:
    
    ```console
    kinit contosoadmin@CONTOSO.COM
    ``` 

1. Son olarak, `realm join` komutunu kullanarak makineyi Azure AD DS tarafından yönetilen etki alanına katın. Önceki `kinit` komutunda belirttiğiniz *AAD DC Yöneticiler* grubunun bir üyesi olan kullanıcı hesabını kullanın, örneğin `contosoadmin@CONTOSO.COM`:

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM'
    ```

VM 'nin Azure AD DS yönetilen etki alanına katılması birkaç dakika sürer. Aşağıdaki örnek çıktı, sanal makinenin Azure AD DS yönetilen etki alanına başarıyla katıldığını göstermektedir:

```output
Successfully enrolled machine in realm
```

  **RHEL 6** 

1. Azure AD DS yönetilen etki alanını saptamak için `adcli info` komutunu kullanın. Aşağıdaki örnek, *contoso.com*bölgesini bulur. Azure AD DS yönetilen etki alanı adınızı tüm büyük harfle belirtin:

    ```console
    sudo adcli info contoso.com
    ```
    
   @No__t_0 komutu Azure AD DS yönetilen etki alanınızı bulamazsa, aşağıdaki sorun giderme adımlarını gözden geçirin:
   
    * Etki alanına VM 'den erişilebildiğinden emin olun. Pozitif bir yanıtın döndürülüp döndürülmediğini görmek için `ping contoso.com` deneyin.
    * VM 'nin aynı veya Azure AD DS yönetilen etki alanının kullanılabildiği eşlenmiş bir sanal ağa dağıtıldığını denetleyin.
    * Sanal ağ için DNS sunucu ayarlarının, Azure AD DS yönetilen etki alanının etki alanı denetleyicilerini işaret etmek üzere güncelleştirildiğinden emin olun.

1. İlk olarak, `adcli join` komutunu kullanarak etki alanına katılarak, bu komut makinenin kimliğini doğrulamak için de keytab öğesini oluşturur. *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı kullanın. 

    ```console
    sudo adcli join contoso.com -U contosoadmin
    ```

1. Şimdi `/ect/krb5.conf` yapılandırıp `contoso.com` Active Directory etki alanını kullanmak için `/etc/sssd/sssd.conf` dosyaları oluşturun. 
   @No__t_0 kendi etki alanı adınızla değiştirildiğinden emin olun:

    @No__t_0 dosyasını bir düzenleyici ile açın:

    ```console
    sudo vi /etc/krb5.conf
    ```

    @No__t_0 dosyasını aşağıdaki örnekle eşleşecek şekilde güncelleştirin:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = CONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     CONTOSO.COM = {
     kdc = CONTOSO.COM
     admin_server = CONTOSO.COM
     }
    
    [domain_realm]
     .CONTOSO.COM = CONTOSO.COM
     CONTOSO.COM = CONTOSO.COM
    ```
    
   @No__t_0 dosyasını oluşturun:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    @No__t_0 dosyasını aşağıdaki örnekle eşleşecek şekilde güncelleştirin:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = CONTOSO.COM
    
    [domain/CONTOSO.COM]
    
     id_provider = ad
    ```

1. @No__t_0 izinlerinin 600 olduğundan ve kök kullanıcıya ait olduğundan emin olun:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. VM 'yi AD Linux tümleştirmesi hakkında bildirmek için `authconfig` kullanın:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```
    
1. Sssd hizmetini başlatıp etkinleştirin:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

VM 'niz etki alanına ekleme işlemini başarıyla tamamlayamadıysanız, VM 'nin ağ güvenlik grubunun TCP + UDP bağlantı noktası 464 üzerinde giden Kerberos trafiğinin Azure AD DS yönetilen etki alanınıza yönelik sanal ağ alt ağına izin verdiğinden emin olun.

Şimdi `getent` kullanarak Kullanıcı AD bilgilerini sorgulayabilir olup olmadığınızı kontrol edin

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>SSH için parola kimlik doğrulamasına izin ver

Varsayılan olarak, kullanıcılar yalnızca SSH ortak anahtar tabanlı kimlik doğrulaması kullanarak bir VM 'de oturum açabilirler. Parola tabanlı kimlik doğrulaması başarısız olur. VM 'yi Azure AD DS yönetilen bir etki alanına katdığınızda, bu etki alanı hesaplarının parola tabanlı kimlik doğrulamasını kullanması gerekir. Aşağıdaki gibi, parola tabanlı kimlik doğrulamasına izin vermek için SSH yapılandırmasını güncelleştirin.

1. *Sshd_conf* dosyasını bir düzenleyici ile açın:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *Passwordaduthentication* satırını *Evet*olarak güncelleştirin:

    ```console
    PasswordAuthentication yes
    ```

    İşiniz bittiğinde, düzenleyicinin `:wq` komutunu kullanarak *sshd_conf* dosyasını kaydedin ve kapatın.

1. Değişiklikleri uygulamak ve kullanıcıların bir parola kullanarak oturum açmasını sağlamak için SSH hizmetini yeniden başlatın:

   **RHEL 7** 
    
    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6** 
    
    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>' AAD DC yöneticileri ' Grup sudo ayrıcalıklarına izin verme

RHEL VM 'de *AAD DC yöneticileri* grubunun üyelerine yönetici ayrıcalıkları vermek için, */etc/sudoers*'a bir giriş eklersiniz. Eklendikten sonra, *AAD DC yöneticileri* grubunun üyeleri RHEL VM 'de `sudo` komutunu kullanabilir.

1. Şu şekilde düzenlenecek *sudoers* dosyasını açın:

    ```console
    sudo visudo
    ```

1. */Etc/sudoers* dosyasının sonuna aşağıdaki girişi ekleyin. *AAD DC Administrators* grubu adında boşluk içerir, bu nedenle Grup adına ters eğik çizgi kaçış karakteri ekleyin. *Contoso.com*gibi kendi etki alanı adınızı ekleyin:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@contoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    İşiniz bittiğinde, düzenleyicinin `:wq` komutunu kullanarak düzenleyiciyi kaydedin ve kapatın.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bir etki alanı hesabı kullanarak VM 'de oturum açma

VM 'nin Azure AD DS tarafından yönetilen etki alanına başarıyla katıldığını doğrulamak için, bir etki alanı kullanıcı hesabı kullanarak yeni bir SSH bağlantısı başlatın. Bir giriş dizininin oluşturulduğunu ve etki alanındaki grup üyeliğinin uygulandığını doğrulayın.

1. Konsolınızdan yeni bir SSH bağlantısı oluşturun. @No__t_1 gibi `ssh -l` komutunu kullanarak yönetilen etki alanına ait bir etki alanı hesabı kullanın ve ardından sanal makinenizin adresini girin (örneğin, *RHEL.contoso.com*). Azure Cloud Shell kullanıyorsanız, iç DNS adı yerine VM 'nin genel IP adresini kullanın.

    ```console
    ssh -l contosoadmin@CONTOSO.com rhel.contoso.com
    ```

1. VM 'ye başarıyla bağlandığınızda, giriş dizininin doğru şekilde başlatıldığını doğrulayın:

    ```console
    pwd
    ```

    Kullanıcı hesabıyla eşleşen kendi dizininizin bulunduğu */Home* dizininde olmanız gerekir.

1. Şimdi grup üyeliklerinin doğru çözümlendiğini kontrol edin:

    ```console
    id
    ```

    Grup üyeliklerinizi Azure AD DS yönetilen etki alanından görmeniz gerekir.

1. VM 'de *AAD DC Administrators* grubunun bir üyesi olarak oturum açtıysanız, `sudo` komutunu doğru şekilde kullanıp kullandığınıza bakın:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Sonraki adımlar

VM 'yi Azure AD DS tarafından yönetilen etki alanına bağlama veya bir etki alanı hesabıyla oturum açma sorunları yaşıyorsanız, bkz. [etki alanına ekleme sorunlarını giderme](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
