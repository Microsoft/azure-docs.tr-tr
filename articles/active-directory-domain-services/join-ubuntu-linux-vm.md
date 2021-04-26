---
title: Ubuntu VM 'sini Azure AD Domain Services 'e ekleme | Microsoft Docs
description: Ubuntu Linux bir sanal makineyi Azure AD Domain Services yönetilen bir etki alanına nasıl yapılandıracağınızı ve katılacağınızı öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.custom: fasttrack-edit
ms.openlocfilehash: 8b6d022b9a1f3be70f69943a53754c9dd909ca99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619496"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ubuntu Linux bir sanal makineyi Azure Active Directory Domain Services yönetilen bir etki alanına katma

Kullanıcıların Azure 'da tek bir kimlik bilgileri kümesi kullanarak sanal makinelerde (VM) oturum açmalarına izin vermek için VM 'Leri Azure Active Directory Domain Services (Azure AD DS) yönetilen bir etki alanına katabilirsiniz. Bir VM 'yi Azure AD DS yönetilen bir etki alanına katdığınızda, etki alanındaki Kullanıcı hesapları ve kimlik bilgileri, sunucuları oturum açmak ve yönetmek için kullanılabilir. Yönetilen etki alanındaki grup üyelikleri, VM 'deki dosyalara veya hizmetlere erişimi denetlemenize olanak sağlamak için de uygulanır.

Bu makalede bir Ubuntu Linux VM 'yi yönetilen bir etki alanına nasıl katılabilmeniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Yönetilen etki alanının bir parçası olan bir kullanıcı hesabı.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Ubuntu Linux VM oluşturma ve bu makineye bağlanma

Azure 'da mevcut bir Ubuntu Linux sanal makinesi varsa, SSH kullanarak buna bağlanın ve sonra [VM 'yi yapılandırmaya başlamak](#configure-the-hosts-file)için sonraki adımla devam edin.

Bir Ubuntu Linux VM oluşturmanız veya bu makaleyle kullanmak üzere bir test sanal makinesi oluşturmak istemeniz gerekiyorsa, aşağıdaki yöntemlerden birini kullanabilirsiniz:

* [Azure portalı](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM 'yi oluştururken sanal ağ ayarlarına dikkat ederek VM 'nin yönetilen etki alanıyla iletişim kurabildiğinden emin olun:

* Sanal makineyi aynı veya Azure AD Domain Services etkinleştirdiğiniz bir eşlenen sanal ağa dağıtın.
* VM 'yi Azure AD Domain Services yönetilen etki alanından farklı bir alt ağa dağıtın.

VM dağıtıldıktan sonra, SSH kullanarak VM 'ye bağlanma adımlarını izleyin.

## <a name="configure-the-hosts-file"></a>Hosts dosyasını yapılandırma

VM ana bilgisayar adının yönetilen etki alanı için doğru yapılandırıldığından emin olmak için, */etc/hosts* dosyasını düzenleyin ve ana bilgisayar adını ayarlayın:

```console
sudo vi /etc/hosts
```

*Konaklar* dosyasında, *localhost* adresini güncelleştirin. Aşağıdaki örnekte:

* *aaddscontoso.com* , yönetilen etkı alanının DNS etki alanı adıdır.
* *Ubuntu* , yönetilen etki alanına katıldığınız Ubuntu VM 'nizin ana bilgisayar adıdır.

Bu adları kendi değerlerinizle güncelleştirin:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *konaklar* dosyasını kaydedin ve kapatın `:wq` .

## <a name="install-required-packages"></a>Gerekli paketleri yükleme

VM 'nin yönetilen etki alanına sanal makineye katılması için bazı ek paketlere ihtiyacı vardır. Bu paketleri yüklemek ve yapılandırmak için, kullanarak etki alanına ekleme araçları 'nı güncelleştirme ve yüklemeyi `apt-get`

Kerberos yüklemesi sırasında, *krb5-User* PAKETI tüm büyük harfle bölge adı ister. Örneğin, yönetilen etki alanının adı *aaddscontoso.com* ise, bölge olarak *AADDSCONTOSO.com* girin. Yükleme, `[realm]` `[domain_realm]` */etc/kronb5,conf* yapılandırma dosyasına ve bölümlerini yazar. Bölgeyi tümü büyük harfle belirttiğinizden emin olun:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Ağ saati protokolünü yapılandırma (NTP)

Etki alanı iletişiminin doğru çalışması için Ubuntu VM 'nizin tarih ve saati, yönetilen etki alanı ile eşitlenmelidir. Yönetilen etki alanı NTP ana bilgisayar adını */etc/NTP.conf* dosyasına ekleyin.

1. Bir düzenleyici ile *NTP. conf* dosyasını açın:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. *NTP. conf* dosyasında, yönetilen etkı alanının DNS adını eklemek için bir satır oluşturun. Aşağıdaki örnekte, *aaddscontoso.com* için bir giriş eklenmiştir. Kendi DNS adınızı kullanın:

    ```console
    server aaddscontoso.com
    ```

    İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *NTP. conf* dosyasını kaydedin ve kapatın `:wq` .

1. VM 'nin yönetilen etki alanı ile eşitlendiğinden emin olmak için aşağıdaki adımlar gereklidir:

    * NTP sunucusunu durdur
    * Yönetilen etki alanından tarih ve saati güncelleştirme
    * NTP hizmetini başlatın

    Bu adımları gerçekleştirmek için aşağıdaki komutları çalıştırın. Şu komutla kendi DNS adınızı kullanın `ntpdate` :

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>VM 'yi yönetilen etki alanına katma

Gerekli paketler VM 'de yüklü olduğundan ve NTP yapılandırıldıktan sonra VM 'yi yönetilen etki alanına ekleyin.

1. `realm discover`Yönetilen etki alanını bulma komutunu kullanın. Aşağıdaki örnek, *AADDSCONTOSO.com* bölgesini bulur. Tüm büyük harfle yönetilen etki alanı adınızı belirtin:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   `realm discover`Komut yönetilen etki alanınızı bulamazsa, aşağıdaki sorun giderme adımlarını gözden geçirin:

    * Etki alanına VM 'den erişilebildiğinden emin olun. `ping aaddscontoso.com`Olumlu bir yanıtın döndürülüp döndürülmediğini görmeyi deneyin.
    * VM 'nin, yönetilen etki alanının kullanılabildiği aynı veya eşlenmiş bir sanal ağa dağıtılmış olduğunu denetleyin.
    * Sanal ağ için DNS sunucu ayarlarının, yönetilen etki alanının etki alanı denetleyicilerini işaret etmek üzere güncelleştirildiğinden emin olun.

1. Şimdi komutunu kullanarak Kerberos başlatın `kinit` . Yönetilen etki alanının bir parçası olan bir kullanıcı belirtin. Gerekirse, [Azure AD 'de bir gruba bir kullanıcı hesabı ekleyin](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Daha sonra, yönetilen etki alanı adının tümü büyük harfle girilmelidir. Aşağıdaki örnekte, adlı hesap, `contosoadmin@aaddscontoso.com` Kerberos 'u başlatmak için kullanılır. Yönetilen etki alanının bir parçası olan kendi kullanıcı hesabınızı girin:

    ```console
    kinit -V contosoadmin@AADDSCONTOSO.COM
    ```

1. Son olarak, komutunu kullanarak VM 'yi yönetilen etki alanına katın `realm join` . Önceki komutta belirttiğiniz yönetilen etki alanının bir parçası olan kullanıcı hesabını kullanın `kinit` , örneğin `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

VM 'nin yönetilen etki alanına katılması birkaç dakika sürer. Aşağıdaki örnek çıktıda VM 'nin yönetilen etki alanına başarıyla katıldığını gösterilmektedir:

```output
Successfully enrolled machine in realm
```

VM 'niz etki alanına ekleme işlemini başarıyla tamamlayamadıysanız, VM 'nin ağ güvenlik grubunun, TCP + UDP bağlantı noktası 464 üzerinde giden Kerberos trafiğine, yönetilen etki alanınıza ait sanal ağ alt ağına izin verdiğinden emin olun.

*BELIRTILMEYEN GSS hatası hatasını aldıysanız.  Küçük kod daha fazla bilgi sağlayabilir (Kerberos veritabanında bulunmayan sunucu)*, */etc/kronb5,conf* dosyasını açın ve bölümüne aşağıdaki kodu ekleyin `[libdefaults]` ve yeniden deneyin:

```console
rdns=false
```

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

    İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *sssd. conf* dosyasını kaydedin ve çıkın `:wq` .

1. Değişikliği uygulamak için SSSD hizmetini yeniden başlatın:

    ```console
    sudo systemctl restart sssd
    ```

## <a name="configure-user-account-and-group-settings"></a>Kullanıcı hesabı ve grup ayarlarını yapılandırma

Yönetilen etki alanına katılmış ve kimlik doğrulaması için yapılandırılmış VM ile, tamamlanacak birkaç Kullanıcı yapılandırma seçeneği vardır. Bu yapılandırma değişiklikleri, parola tabanlı kimlik doğrulamasına izin vermeyi ve etki alanı kullanıcıları ilk kez oturum açtığında yerel sanal makinede otomatik olarak giriş dizinleri oluşturmayı içerir.

### <a name="allow-password-authentication-for-ssh"></a>SSH için parola kimlik doğrulamasına izin ver

Varsayılan olarak, kullanıcılar yalnızca SSH ortak anahtar tabanlı kimlik doğrulaması kullanarak bir VM 'de oturum açabilirler. Parola tabanlı kimlik doğrulaması başarısız olur. VM 'yi yönetilen bir etki alanına katdığınızda, bu etki alanı hesaplarının parola tabanlı kimlik doğrulaması kullanması gerekir. Aşağıdaki gibi, parola tabanlı kimlik doğrulamasına izin vermek için SSH yapılandırmasını güncelleştirin.

1. *Sshd_conf* dosyasını bir düzenleyici ile açın:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *Passwordaduthentication* satırını *Evet* olarak güncelleştirin:

    ```console
    PasswordAuthentication yes
    ```

    İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *sshd_conf* dosyasını kaydedin ve kapatın `:wq` .

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

1. Aşağıdaki satırı bu dosyaya satır altına ekleyin `session optional pam_sss.so` :

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *ortak oturum* dosyasını kaydedin ve kapatın `:wq` .

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>' AAD DC yöneticileri ' Grup sudo ayrıcalıklarına izin verme

Ubuntu VM 'de *AAD DC yöneticileri* grubu yönetici ayrıcalıklarının üyelerine izin vermek için, */etc/suoners*'a bir giriş eklersiniz. Eklendikten sonra, *AAD DC yöneticileri* grubunun üyeleri `sudo` Ubuntu VM üzerinde komutunu kullanabilir.

1. Şu şekilde düzenlenecek *sudoers* dosyasını açın:

    ```console
    sudo visudo
    ```

1. */Etc/sudoers* dosyasının sonuna aşağıdaki girişi ekleyin:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    İşiniz bittiğinde, komutunu kullanarak düzenleyiciyi kaydedin ve kapatın `Ctrl-X` .

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bir etki alanı hesabı kullanarak VM 'de oturum açma

VM 'nin yönetilen etki alanına başarıyla katıldığını doğrulamak için, bir etki alanı kullanıcı hesabı kullanarak yeni bir SSH bağlantısı başlatın. Bir giriş dizininin oluşturulduğunu ve etki alanındaki grup üyeliğinin uygulandığını doğrulayın.

1. Konsolınızdan yeni bir SSH bağlantısı oluşturun. Komutunu kullanarak yönetilen etki alanına ait bir etki alanı hesabı kullanın, örneğin, `ssh -l` `contosoadmin@aaddscontoso.com` *Ubuntu.aaddscontoso.com* gibi sanal makinenizin adresini girin. Azure Cloud Shell kullanıyorsanız, iç DNS adı yerine VM 'nin genel IP adresini kullanın.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
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

    Grup üyeliklerinizi yönetilen etki alanından görmeniz gerekir.

1. VM 'de *AAD DC Administrators* grubunun bir üyesi olarak oturum açtıysanız, komutunu doğru şekilde kullanıp kullandığınıza bakın `sudo` :

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Sonraki adımlar

VM 'yi yönetilen etki alanına bağlama veya bir etki alanı hesabıyla oturum açma sorunları yaşıyorsanız, bkz. [etki alanına ekleme sorunlarını giderme](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
