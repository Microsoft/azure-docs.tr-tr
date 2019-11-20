---
title: Azure AD Domain Services bir CoreOS VM 'sine ekleme | Microsoft Docs
description: Bir CoreOS sanal makinesini Azure AD Domain Services yönetilen bir etki alanına nasıl yapılandıracağınızı ve katılacağınızı öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/14/2019
ms.author: iainfou
ms.openlocfilehash: 4cdc2fff05270a296d9c4c9151f73cadeb2a1cfc
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754384"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Bir CoreOS sanal makinesini Azure AD Domain Services yönetilen bir etki alanına katma

Kullanıcıların Azure 'da tek bir kimlik bilgileri kümesi kullanarak sanal makinelerde (VM) oturum açmalarına izin vermek için, VM 'Leri Azure Active Directory Domain Services (AD DS) yönetilen bir etki alanına katabilirsiniz. Bir VM 'yi Azure AD DS yönetilen bir etki alanına katdığınızda, etki alanındaki Kullanıcı hesapları ve kimlik bilgileri, sunucuları oturum açmak ve yönetmek için kullanılabilir. Azure AD DS yönetilen etki alanındaki grup üyelikleri, VM 'deki dosya ve hizmetlere erişimi denetlemenize olanak sağlamak için de uygulanır.

Bu makalede, bir CoreOS VM 'sini Azure AD DS yönetilen bir etki alanına nasıl katılabilmeniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [bir Azure Active Directory Domain Services örneği oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Bir CoreOS Linux VM 'si oluşturma ve bu makineye bağlanma

Azure 'da mevcut bir CoreOS Linux sanal makinesi varsa, SSH kullanarak buna bağlanın ve sonra [VM 'yi yapılandırmaya başlamak](#configure-the-hosts-file)için sonraki adımla devam edin.

Bir CoreOS Linux sanal makinesi oluşturmanız veya bu makaleyle kullanmak üzere bir test sanal makinesi oluşturmak istiyorsanız aşağıdaki yöntemlerden birini kullanabilirsiniz:

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
* *CoreOS* , yönetilen etki alanına katıldığınız COREOS VM 'nizin ana bilgisayar adıdır.

Bu adları kendi değerlerinizle güncelleştirin:

```console
127.0.0.1 coreos coreos.contoso.com
```

İşiniz bittiğinde, düzenleyicinin `:wq` komutunu kullanarak *konaklar* dosyasını kaydedin ve kapatın.

## <a name="configure-the-sssd-service"></a>SSSD hizmetini yapılandırma

*/Etc/sssd/sssd.exe* sssd yapılandırmasını güncelleştirin.

```console
sudo vi /etc/sssd/sssd.conf
```

Aşağıdaki parametreler için kendi Azure AD DS yönetilen etki alanı adınızı belirtin:

* Tüm büyük durumlarda *etki alanları*
* *[etkialanı/contoso]* burada contoso her büyük durumda
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* Tüm büyük durumlarda *krb5_realm*

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = CONTOSO.COM

[domain/CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>VM 'yi yönetilen etki alanına katma

SSSD yapılandırma dosyası güncelleştirildiğinden, artık sanal makineyi yönetilen etki alanına katın.

1. İlk olarak, Azure AD DS yönetilen etki alanı hakkında bilgi görebildiğinizi doğrulamak için `adcli info` komutunu kullanın. Aşağıdaki örnek, *contoso.com*etki alanı için bilgileri alır. Azure AD DS yönetilen etki alanı adınızı tüm büyük harfle belirtin:

    ```console
    sudo adcli info CONTOSO.COM
    ```

   @No__t_0 komutu Azure AD DS yönetilen etki alanınızı bulamazsa, aşağıdaki sorun giderme adımlarını gözden geçirin:

    * Etki alanına VM 'den erişilebildiğinden emin olun. Pozitif bir yanıtın döndürülüp döndürülmediğini görmek için `ping contoso.com` deneyin.
    * VM 'nin aynı veya Azure AD DS yönetilen etki alanının kullanılabildiği eşlenmiş bir sanal ağa dağıtıldığını denetleyin.
    * Sanal ağ için DNS sunucu ayarlarının, Azure AD DS yönetilen etki alanının etki alanı denetleyicilerini işaret etmek üzere güncelleştirildiğinden emin olun.

1. Şimdi `adcli join` komutunu kullanarak VM 'yi Azure AD DS tarafından yönetilen etki alanına katın. *AAD DC Administrators* grubuna ait olan bir kullanıcı belirtin. Gerekirse, [Azure AD 'de bir gruba bir kullanıcı hesabı ekleyin](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Azure AD DS yönetilen etki alanı adının tümü büyük harfle girilmelidir. Aşağıdaki örnekte, `contosoadmin@contoso.com` adlı hesap, Kerberos 'u başlatmak için kullanılır. *AAD DC Administrators* grubunun üyesi olan kendi kullanıcı hesabınızı girin.

    ```console
    sudo adcli join -D CONTOSO.COM -U contosoadmin@CONTOSO.COM -K /etc/krb5.keytab -H coreos.contoso.com -N coreos
    ```

    @No__t_0 komutu, sanal makine Azure AD DS tarafından yönetilen etki alanına başarıyla katıldığında hiçbir bilgi döndürmez.

1. Etki alanına katılacak yapılandırmayı uygulamak için SSSD hizmetini başlatın:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bir etki alanı hesabı kullanarak VM 'de oturum açma

VM 'nin Azure AD DS tarafından yönetilen etki alanına başarıyla katıldığını doğrulamak için, bir etki alanı kullanıcı hesabı kullanarak yeni bir SSH bağlantısı başlatın. Bir giriş dizininin oluşturulduğunu ve etki alanındaki grup üyeliğinin uygulandığını doğrulayın.

1. Konsolınızdan yeni bir SSH bağlantısı oluşturun. @No__t_1 gibi `ssh -l` komutunu kullanarak yönetilen etki alanına ait bir etki alanı hesabı kullanın ve ardından sanal makinenizin adresini girin (örneğin, *CoreOS.contoso.com*). Azure Cloud Shell kullanıyorsanız, iç DNS adı yerine VM 'nin genel IP adresini kullanın.

    ```console
    ssh -l contosoadmin@CONTOSO.com coreos.contoso.com
    ```

1. Şimdi grup üyeliklerinin doğru çözümlendiğini kontrol edin:

    ```console
    id
    ```

    Grup üyeliklerinizi Azure AD DS yönetilen etki alanından görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

VM 'yi Azure AD DS tarafından yönetilen etki alanına bağlama veya bir etki alanı hesabıyla oturum açma sorunları yaşıyorsanız, bkz. [etki alanına ekleme sorunlarını giderme](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
