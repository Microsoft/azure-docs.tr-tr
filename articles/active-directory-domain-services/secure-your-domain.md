---
title: Güvenli Azure AD Domain Services | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanı için zayıf şifrelemeleri, eski protokolleri ve NTLM parola karması eşitlemesini devre dışı bırakmayı öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/09/2019
ms.author: iainfou
ms.openlocfilehash: 6d60663e345f297246a423e1b4e7a4fb45f352a5
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754327"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanının güvenliğini sağlamak için zayıf şifre ve parola karma eşitlemesini devre dışı bırakın

Varsayılan olarak, Azure Active Directory Domain Services (Azure AD DS), NTLM v1 ve TLS v1 gibi şifrelemelerin kullanımını mümkün değildir. Bu şifrelemeler bazı eski uygulamalar için gerekli olabilir, ancak zayıf kabul edilir ve gerek duymuyorsanız devre dışı bırakılabilir. Azure AD Connect kullanarak şirket içi karma bağlantınız varsa, NTLM parola karmalarının eşitlenmesini de devre dışı bırakabilirsiniz.

Bu makalede, NTLM v1 ve TLS v1 şifrelemelerini devre dışı bırakma ve NTLM parola karma eşitlemesini devre dışı bırakma gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [bir Azure Active Directory Domain Services örneği oluşturun ve yapılandırın][create-azure-ad-ds-instance].
* Azure PowerShell'i yükleyip yapılandırın.
    * Gerekirse, [Azure PowerShell modülünü yüklemek ve Azure aboneliğinize bağlanmak](/powershell/azure/install-az-ps)için yönergeleri izleyin.
    * [Connect-AzAccount][Connect-AzAccount] cmdlet 'Ini kullanarak Azure aboneliğinizde oturum açın.
* Azure AD PowerShell 'i yükleyip yapılandırın.
    * Gerekirse, [Azure AD PowerShell modülünü yüklemek ve Azure AD 'ye bağlanmak](/powershell/azure/active-directory/install-adv2)için yönergeleri izleyin.
    * [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda oturum açarak emin olun.

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Zayıf şifre ve NTLM parola karma eşitlemesini devre dışı bırak

Zayıf şifre paketlerini ve NTLM kimlik bilgisi karma eşitlemesini devre dışı bırakmak için Azure hesabınızda oturum açın, sonra [Get-AzResource][Get-AzResource] cmdlet 'Ini kullanarak Azure AD DS kaynağını alın:

> [!TIP]
> *Microsoft. AAD/DomainServices* kaynağının mevcut olmadığı [Get-azresource][Get-AzResource] komutunu kullanarak bir hata alırsanız, [tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek için erişiminizi][global-admin]yükseltin.

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Ardından, aşağıdaki güvenlik seçeneklerini yapılandırmak için *Domainsecuritysettings* tanımlayın:

1. NTLM v1 desteğini devre dışı bırakın.
2. Şirket içi AD 'nizden NTLM parola karmalarının eşitlemesini devre dışı bırakın.
3. TLS v1 'yi devre dışı bırakın.

> [!IMPORTANT]
> Azure AD DS yönetilen etki alanında NTLM parola karması eşitlemesini devre dışı bıraktığınızda kullanıcılar ve hizmet hesapları LDAP basit bağlamalar gerçekleştiremez. LDAP basit bağlamalar gerçekleştirmeniz gerekirse, aşağıdaki komutta *"Syncntlmpasswords" = "devre dışı";* Güvenlik Yapılandırması seçeneğini ayarlayın.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Son olarak, [set-AzResource][Set-AzResource] cmdlet 'ini kullanarak tanımlı güvenlik ayarlarını Azure AD DS tarafından yönetilen etki alanına uygulayın. İlk adımdan Azure AD DS kaynağını ve önceki adımdaki güvenlik ayarlarını belirtin.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Güvenlik ayarlarının Azure AD DS yönetilen etki alanına uygulanması birkaç dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

Eşitleme işlemi hakkında daha fazla bilgi edinmek için bkz. [Azure AD DS yönetilen bir etki alanında nesnelerin ve kimlik bilgilerinin nasıl eşitleneceğini][synchronization]öğrenin.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD