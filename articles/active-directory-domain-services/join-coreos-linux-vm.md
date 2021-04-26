---
title: Azure AD Domain Services bir CoreOS VM 'sine ekleme | Microsoft Docs
description: Bir CoreOS sanal makinesini Azure AD Domain Services yönetilen bir etki alanına nasıl yapılandıracağınızı ve katılacağınızı öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 1e98f32bd6fe7d5373d5ab6621ffdce5e79abc08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619598"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Bir CoreOS sanal makinesini Azure Active Directory Domain Services yönetilen bir etki alanına katma

Kullanıcıların Azure 'da tek bir kimlik bilgileri kümesi kullanarak sanal makinelerde (VM) oturum açmalarına izin vermek için VM 'Leri Azure Active Directory Domain Services (Azure AD DS) yönetilen bir etki alanına katabilirsiniz. Bir VM 'yi Azure AD DS yönetilen bir etki alanına katdığınızda, etki alanındaki Kullanıcı hesapları ve kimlik bilgileri, sunucuları oturum açmak ve yönetmek için kullanılabilir. Yönetilen etki alanındaki grup üyelikleri, VM 'deki dosyalara veya hizmetlere erişimi denetlemenize olanak sağlamak için de uygulanır.

Bu makalede, bir CoreOS VM 'sini yönetilen bir etki alanına nasıl katmak gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Yönetilen etki alanının bir parçası olan bir kullanıcı hesabı.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Bir CoreOS Linux VM 'si oluşturma ve bu makineye bağlanma

Azure 'da mevcut bir CoreOS Linux sanal makinesi varsa, SSH kullanarak buna bağlanın ve sonra [VM 'yi yapılandırmaya başlamak](#configure-the-hosts-file)için sonraki adımla devam edin.

Bir CoreOS Linux sanal makinesi oluşturmanız veya bu makaleyle kullanmak üzere bir test sanal makinesi oluşturmak istiyorsanız aşağıdaki yöntemlerden birini kullanabilirsiniz:

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
* *CoreOS* , yönetilen etki alanına katıldığınız COREOS VM 'nizin ana bilgisayar adıdır.

Bu adları kendi değerlerinizle güncelleştirin:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *konaklar* dosyasını kaydedin ve kapatın `:wq` .

## <a name="configure-the-sssd-service"></a>SSSD hizmetini yapılandırma

*/Etc/sssd/sssd.exe* sssd yapılandırmasını güncelleştirin.

```console
sudo vi /etc/sssd/sssd.conf
```

Aşağıdaki parametreler için kendi yönetilen etki alanı adınızı belirtin:

* Tüm büyük durumlarda *etki alanları*
* *[Domain/aaddscontoso]* burada AADDSCONTOSO her büyük durumda
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* Tüm büyük durumlarda *krb5_realm*

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>VM 'yi yönetilen etki alanına katma

SSSD yapılandırma dosyası güncelleştirildiğinden, artık sanal makineyi yönetilen etki alanına katın.

1. İlk olarak, `adcli info` yönetilen etki alanı hakkında bilgi görebildiğinizi doğrulamak için komutunu kullanın. Aşağıdaki örnek, *AADDSCONTOSO.com* etki alanı için bilgileri alır. Tüm büyük harfle yönetilen etki alanı adınızı belirtin:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   `adcli info`Komut yönetilen etki alanınızı bulamazsa, aşağıdaki sorun giderme adımlarını gözden geçirin:

    * Etki alanına VM 'den erişilebildiğinden emin olun. `ping aaddscontoso.com`Olumlu bir yanıtın döndürülüp döndürülmediğini görmeyi deneyin.
    * VM 'nin, yönetilen etki alanının kullanılabildiği aynı veya eşlenmiş bir sanal ağa dağıtılmış olduğunu denetleyin.
    * Sanal ağ için DNS sunucu ayarlarının, yönetilen etki alanının etki alanı denetleyicilerini işaret etmek üzere güncelleştirildiğinden emin olun.

1. Şimdi komutunu kullanarak VM 'yi yönetilen etki alanına katın `adcli join` . Yönetilen etki alanının bir parçası olan bir kullanıcı belirtin. Gerekirse, [Azure AD 'de bir gruba bir kullanıcı hesabı ekleyin](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Daha sonra, yönetilen etki alanı adının tümü büyük harfle girilmelidir. Aşağıdaki örnekte, adlı hesap, `contosoadmin@aaddscontoso.com` Kerberos 'u başlatmak için kullanılır. Yönetilen etki alanının bir parçası olan kendi kullanıcı hesabınızı girin.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    `adcli join`VM yönetilen etki alanına başarıyla katıldığında, komut herhangi bir bilgi döndürmez.

1. Etki alanına katılacak yapılandırmayı uygulamak için SSSD hizmetini başlatın:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bir etki alanı hesabı kullanarak VM 'de oturum açma

VM 'nin yönetilen etki alanına başarıyla katıldığını doğrulamak için, bir etki alanı kullanıcı hesabı kullanarak yeni bir SSH bağlantısı başlatın. Bir giriş dizininin oluşturulduğunu ve etki alanındaki grup üyeliğinin uygulandığını doğrulayın.

1. Konsolınızdan yeni bir SSH bağlantısı oluşturun. Komutunu kullanarak yönetilen etki alanına ait bir etki alanı hesabı kullanın, örneğin, `ssh -l` `contosoadmin@aaddscontoso.com` *CoreOS.aaddscontoso.com* gibi sanal makinenizin adresini girin. Azure Cloud Shell kullanıyorsanız, iç DNS adı yerine VM 'nin genel IP adresini kullanın.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Şimdi grup üyeliklerinin doğru çözümlendiğini kontrol edin:

    ```console
    id
    ```

    Grup üyeliklerinizi yönetilen etki alanından görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

VM 'yi yönetilen etki alanına bağlama veya bir etki alanı hesabıyla oturum açma sorunları yaşıyorsanız, bkz. [etki alanına ekleme sorunlarını giderme](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
