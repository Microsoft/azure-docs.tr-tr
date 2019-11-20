---
title: Ubuntu VM 'sini Azure AD Domain Services 'e ekleme | Microsoft Docs
description: Ubuntu Linux bir sanal makineyi Azure AD Domain Services yönetilen bir etki alanına nasıl yapılandıracağınızı ve katılacağınızı öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: 9fb41b08cb29a68b39fb416b4b7b7bcce9e821dd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754357"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Ubuntu Linux bir sanal makineyi Azure AD Domain Services yönetilen bir etki alanına katma

Kullanıcıların Azure 'da tek bir kimlik bilgileri kümesi kullanarak sanal makinelerde (VM) oturum açmalarına izin vermek için, VM 'Leri Azure Active Directory Domain Services (AD DS) yönetilen bir etki alanına katabilirsiniz. Bir VM 'yi Azure AD DS yönetilen bir etki alanına katdığınızda, etki alanındaki Kullanıcı hesapları ve kimlik bilgileri, sunucuları oturum açmak ve yönetmek için kullanılabilir. Azure AD DS yönetilen etki alanındaki grup üyelikleri, VM 'deki dosya ve hizmetlere erişimi denetlemenize olanak sağlamak için de uygulanır.

Bu makalede bir Ubuntu Linux VM 'yi Azure AD DS yönetilen bir etki alanına nasıl katılabilmeniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [bir Azure Active Directory Domain Services örneği oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Ubuntu Linux VM oluşturma ve bu makineye bağlanma

Azure 'da mevcut bir Ubuntu Linux sanal makinesi varsa, SSH kullanarak buna bağlanın ve sonra [VM 'yi yapılandırmaya başlamak](#configure-the-hosts-file)için sonraki adımla devam edin.

Bir Ubuntu Linux VM oluşturmanız veya bu makaleyle kullanmak üzere bir test sanal makinesi oluşturmak istemeniz gerekiyorsa, aşağıdaki yöntemlerden birini kullanabilirsiniz:

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
* *Ubuntu* , yönetilen etki alanına katıldığınız Ubuntu VM 'nizin ana bilgisayar adıdır.

Bu adları kendi değerlerinizle güncelleştirin:

```console
127.0.0.1 ubuntu.contoso.com ubuntu
```

İşiniz bittiğinde, düzenleyicinin `:wq` komutunu kullanarak *konaklar* dosyasını kaydedin ve kapatın.

## <a name="install-required-packages"></a>Gerekli paketleri yükleme

VM 'nin VM 'ye Azure AD DS tarafından yönetilen etki alanına katılması için bazı ek paketlere ihtiyacı vardır. Bu paketleri yüklemek ve yapılandırmak için `apt-get` kullanarak etki alanına ekleme araçlarını güncelleştirin ve yüklemeyi yapın

Kerberos yüklemesi sırasında, *krb5-User* PAKETI tüm büyük harfle bölge adı ister. Örneğin, Azure AD DS yönetilen etki alanının adı *contoso.com*ise, bölge olarak *contoso.com* girin. Yükleme, */etc/kronb5,conf* yapılandırma dosyasına `[realm]` ve `[domain_realm]` bölümlerini yazar. Bölgeyi tümü büyük harfle belirttiğinizden emin olun:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Ağ saati protokolünü yapılandırma (NTP)

Etki alanı iletişiminin doğru çalışması için, Ubuntu VM 'nizin tarih ve saati Azure AD DS tarafından yönetilen etki alanıyla eşitlenmelidir. Azure AD DS yönetilen etki alanının NTP ana bilgisayar adını */etc/NTP.conf* dosyasına ekleyin.

1. Bir düzenleyici ile *NTP. conf* dosyasını açın:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. *NTP. conf* dosyasında, Azure AD DS yönetilen etkı alanının DNS adını eklemek için bir satır oluşturun. Aşağıdaki örnekte, *contoso.com* için bir giriş eklenmiştir. Kendi DNS adınızı kullanın:

    ```console
    server contoso.com
    ```

    İşiniz bittiğinde, düzenleyicinin `:wq` komutunu kullanarak *NTP. conf* dosyasını kaydedin ve kapatın.

1. VM 'nin Azure AD DS yönetilen etki alanı ile eşitlendiğinden emin olmak için aşağıdaki adımlar gereklidir:

    * NTP sunucusunu durdur
    * Yönetilen etki alanından tarih ve saati güncelleştirme
    * NTP hizmetini başlatın

    Bu adımları gerçekleştirmek için aşağıdaki komutları çalıştırın. @No__t_0 komutuyla kendi DNS adınızı kullanın:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate contoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>VM 'yi yönetilen etki alanına katma

Gerekli paketler VM 'de yüklü olduğundan ve NTP yapılandırıldıktan sonra, VM 'yi Azure AD DS yönetilen etki alanına ekleyin.

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
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM' --install=/
    ```

VM 'nin Azure AD DS yönetilen etki alanına katılması birkaç dakika sürer. Aşağıdaki örnek çıktı, sanal makinenin Azure AD DS yönetilen etki alanına başarıyla katıldığını göstermektedir:

```output
Successfully enrolled machine in realm
```

VM 'niz etki alanına ekleme işlemini başarıyla tamamlayamadıysanız, VM 'nin ağ güvenlik grubunun TCP + UDP bağlantı noktası 464 üzerinde giden Kerberos trafiğinin Azure AD DS yönetilen etki alanınıza yönelik sanal ağ alt ağına izin verdiğinden emin olun.

## <a name="update-the-sssd-configuration"></a>SSSD yapılandırmasını güncelleştirme

Önceki bir adımda yüklenen paketlerden biri sistem güvenlik hizmetleri Daemon (SSSD) için idi. Bir Kullanıcı, etki alanı kimlik bilgilerini kullanarak bir VM 'de oturum açmaya çalıştığında SSSD, isteği bir kimlik doğrulama sağlayıcısına geçirir. Bu senaryoda SSSD, isteğin kimliğini doğrulamak için Azure AD DS kullanır.

1. *Sssd. conf* dosyasını bir düzenleyici ile açın:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. *Use_fully_qualified_names* için satırı şu şekilde Açıklama:

    ```console
    # use_fully_qualified_names = True
    ```

    İşiniz bittiğinde, düzenleyicinin `:wq` komutunu kullanarak *sssd. conf* dosyasını kaydedin ve çıkın.

1. Değişikliği uygulamak için SSSD hizmetini yeniden başlatın:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Kullanıcı hesabı ve grup ayarlarını yapılandırma

VM, Azure AD DS yönetilen etki alanına katılmış ve kimlik doğrulaması için yapılandırıldıktan sonra, tamamlanacak birkaç Kullanıcı yapılandırma seçeneği vardır. Bu yapılandırma değişiklikleri, parola tabanlı kimlik doğrulamasına izin vermeyi ve etki alanı kullanıcıları ilk kez oturum açtığında yerel sanal makinede otomatik olarak giriş dizinleri oluşturmayı içerir.

### <a name="allow-password-authentication-for-ssh"></a>SSH için parola kimlik doğrulamasına izin ver

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

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Otomatik Ana Dizin oluşturmayı yapılandırma

Bir Kullanıcı ilk kez oturum açtığında ana dizinin otomatik olarak oluşturulmasını etkinleştirmek için aşağıdaki adımları izleyin:

1. */Etc/Pam.d/Common-Session* dosyasını düzenleyicide açın:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Bu dosyaya aşağıdaki satırı `session optional pam_sss.so` ekleyin:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    İşiniz bittiğinde, düzenleyicinin `:wq` komutunu kullanarak *ortak oturum* dosyasını kaydedin ve kapatın.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>' AAD DC yöneticileri ' Grup sudo ayrıcalıklarına izin verme

Ubuntu VM 'de *AAD DC yöneticileri* grubu yönetici ayrıcalıklarının üyelerine izin vermek için, */etc/suoners*'a bir giriş eklersiniz. Eklendikten sonra, *AAD DC yöneticileri* grubunun üyeleri Ubuntu VM üzerinde `sudo` komutunu kullanabilir.

1. Şu şekilde düzenlenecek *sudoers* dosyasını açın:

    ```console
    sudo visudo
    ```

1. */Etc/sudoers* dosyasının sonuna aşağıdaki girişi ekleyin:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    İşiniz bittiğinde `Ctrl-X` komutunu kullanarak düzenleyiciyi kaydedin ve kapatın.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bir etki alanı hesabı kullanarak VM 'de oturum açma

VM 'nin Azure AD DS tarafından yönetilen etki alanına başarıyla katıldığını doğrulamak için, bir etki alanı kullanıcı hesabı kullanarak yeni bir SSH bağlantısı başlatın. Bir giriş dizininin oluşturulduğunu ve etki alanındaki grup üyeliğinin uygulandığını doğrulayın.

1. Konsolınızdan yeni bir SSH bağlantısı oluşturun. @No__t_1 gibi `ssh -l` komutunu kullanarak yönetilen etki alanına ait bir etki alanı hesabı kullanın ve ardından sanal makinenizin adresini girin (örneğin, *Ubuntu.contoso.com*). Azure Cloud Shell kullanıyorsanız, iç DNS adı yerine VM 'nin genel IP adresini kullanın.

    ```console
    ssh -l contosoadmin@CONTOSO.com ubuntu.contoso.com
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
    sudo apt-get update
    ```

## <a name="next-steps"></a>Sonraki adımlar

VM 'yi Azure AD DS tarafından yönetilen etki alanına bağlama veya bir etki alanı hesabıyla oturum açma sorunları yaşıyorsanız, bkz. [etki alanına ekleme sorunlarını giderme](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
