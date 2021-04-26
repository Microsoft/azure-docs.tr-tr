---
title: Azure AD Domain Services bir CentOS VM 'sine ekleme | Microsoft Docs
description: Bir CentOS Linux sanal makinesini Azure Active Directory Domain Services yönetilen bir etki alanına nasıl yapılandıracağınızı ve katılacağınızı öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 76fc11384b55337f581a74239d4a40b90b284f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619666"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Bir CentOS Linux sanal makinesini Azure Active Directory Domain Services yönetilen bir etki alanına katma

Kullanıcıların Azure 'da tek bir kimlik bilgileri kümesi kullanarak sanal makinelerde (VM) oturum açmalarına izin vermek için VM 'Leri Azure Active Directory Domain Services (Azure AD DS) yönetilen bir etki alanına katabilirsiniz. Bir VM 'yi Azure AD DS yönetilen bir etki alanına katdığınızda, etki alanındaki Kullanıcı hesapları ve kimlik bilgileri, sunucuları oturum açmak ve yönetmek için kullanılabilir. Yönetilen etki alanındaki grup üyelikleri, VM 'deki dosyalara veya hizmetlere erişimi denetlemenize olanak sağlamak için de uygulanır.

Bu makalede, bir CentOS Linux sanal makinesini yönetilen bir etki alanına nasıl katılabilmeniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Yönetilen etki alanının parçası olan bir kullanıcı hesabı.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>CentOS Linux VM 'si oluşturma ve bu makineye bağlanma

Azure 'da var olan bir CentOS Linux sanal makinesi varsa, SSH kullanarak bu makineye bağlanın ve ardından [VM 'yi yapılandırmaya başlamak](#configure-the-hosts-file)için sonraki adıma geçin.

Bir CentOS Linux sanal makinesi oluşturmanız veya bu makaleyle kullanmak üzere bir test sanal makinesi oluşturmak istiyorsanız aşağıdaki yöntemlerden birini kullanabilirsiniz:

* [Azure portalı](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM 'yi oluştururken sanal ağ ayarlarına dikkat ederek VM 'nin yönetilen etki alanıyla iletişim kurabildiğinden emin olun:

* Sanal makineyi aynı veya Azure AD Domain Services etkinleştirdiğiniz bir eşlenen sanal ağa dağıtın.
* VM 'yi yönetilen etki alanından farklı bir alt ağa dağıtın.

VM dağıtıldıktan sonra, SSH kullanarak VM 'ye bağlanma adımlarını izleyin.

## <a name="configure-the-hosts-file"></a>Hosts dosyasını yapılandırma

VM ana bilgisayar adının yönetilen etki alanı için doğru yapılandırıldığından emin olmak için, */etc/hosts* dosyasını düzenleyin ve ana bilgisayar adını ayarlayın:

```console
sudo vi /etc/hosts
```

*Konaklar* dosyasında, *localhost* adresini güncelleştirin. Aşağıdaki örnekte:

* *aaddscontoso.com* , yönetilen etkı alanının DNS etki alanı adıdır.
* *CentOS* , yönetilen etki alanına katıldığınız CENTOS sanal makinenizin ana bilgisayar adıdır.

Bu adları kendi değerlerinizle güncelleştirin:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *konaklar* dosyasını kaydedin ve kapatın `:wq` .

## <a name="install-required-packages"></a>Gerekli paketleri yükleme

VM 'nin yönetilen etki alanına sanal makineye katılması için bazı ek paketlere ihtiyacı vardır. Bu paketleri yüklemek ve yapılandırmak için, kullanarak etki alanına ekleme araçlarını güncelleştirin ve yapılandırın `yum` :

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>VM 'yi yönetilen etki alanına katma

Gerekli paketler VM 'ye yüklendikten sonra VM 'yi yönetilen etki alanına katın.

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
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Son olarak, komutunu kullanarak VM 'yi yönetilen etki alanına katın `realm join` . Önceki komutta belirttiğiniz yönetilen etki alanının bir parçası olan kullanıcı hesabını kullanın `kinit` , örneğin `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

VM 'nin yönetilen etki alanına katılması birkaç dakika sürer. Aşağıdaki örnek çıktıda VM 'nin yönetilen etki alanına başarıyla katıldığını gösterilmektedir:

```output
Successfully enrolled machine in realm
```

VM 'niz etki alanına ekleme işlemini başarıyla tamamlayamadıysanız, VM 'nin ağ güvenlik grubunun, TCP + UDP bağlantı noktası 464 üzerinde giden Kerberos trafiğine, yönetilen etki alanınıza ait sanal ağ alt ağına izin verdiğinden emin olun.

## <a name="allow-password-authentication-for-ssh"></a>SSH için parola kimlik doğrulamasına izin ver

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
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>' AAD DC yöneticileri ' Grup sudo ayrıcalıklarına izin verme

CentOS sanal makinesinde *AAD DC Administrators* grubu yönetici ayrıcalıklarının üyelerine izin vermek için, */etc/sudoers*'a bir giriş eklersiniz. Eklendikten sonra *AAD DC yöneticileri* grubunun üyeleri, `sudo` CentOS sanal makinesinde komutunu kullanabilir.

1. Şu şekilde düzenlenecek *sudoers* dosyasını açın:

    ```console
    sudo visudo
    ```

1. */Etc/sudoers* dosyasının sonuna aşağıdaki girişi ekleyin. *AAD DC Administrators* grubu adında boşluk içerir, bu nedenle Grup adına ters eğik çizgi kaçış karakteri ekleyin. *Aaddscontoso.com* gibi kendi etki alanı adınızı ekleyin:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    İşiniz bittiğinde düzenleyicinin komutunu kullanarak düzenleyiciyi kaydedin ve kapatın `:wq` .

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bir etki alanı hesabı kullanarak VM 'de oturum açma

VM 'nin yönetilen etki alanına başarıyla katıldığını doğrulamak için, bir etki alanı kullanıcı hesabı kullanarak yeni bir SSH bağlantısı başlatın. Bir giriş dizininin oluşturulduğunu ve etki alanındaki grup üyeliğinin uygulandığını doğrulayın.

1. Konsolınızdan yeni bir SSH bağlantısı oluşturun. Komutunu kullanarak yönetilen etki alanına ait bir etki alanı hesabı kullanın, örneğin, `ssh -l` `contosoadmin@aaddscontoso.com` *CentOS.aaddscontoso.com* gibi sanal makinenizin adresini girin. Azure Cloud Shell kullanıyorsanız, iç DNS adı yerine VM 'nin genel IP adresini kullanın.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
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
    sudo yum update
    ```

## <a name="next-steps"></a>Sonraki adımlar

VM 'yi yönetilen etki alanına bağlama veya bir etki alanı hesabıyla oturum açma sorunları yaşıyorsanız, bkz. [etki alanına ekleme sorunlarını giderme](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
