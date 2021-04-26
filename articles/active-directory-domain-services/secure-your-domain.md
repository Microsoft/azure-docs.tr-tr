---
title: Güvenli Azure AD Domain Services | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanı için zayıf şifrelemeleri, eski protokolleri ve NTLM parola karması eşitlemesini devre dışı bırakmayı öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: 5fa19e23767af0e121d07872970199a2a1705ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951949"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanının güvenliğini sağlamak için zayıf şifre ve parola karma eşitlemesini devre dışı bırakın

Varsayılan olarak, Azure Active Directory Domain Services (Azure AD DS), NTLM v1 ve TLS v1 gibi şifrelemelerin kullanımını mümkün değildir. Bu şifrelemeler bazı eski uygulamalar için gerekli olabilir, ancak zayıf kabul edilir ve gerek duymuyorsanız devre dışı bırakılabilir. Azure AD Connect kullanarak şirket içi karma bağlantınız varsa, NTLM parola karmalarının eşitlenmesini de devre dışı bırakabilirsiniz.

Bu makalede, NTLM v1 ve TLS v1 şifrelemelerini devre dışı bırakma ve NTLM parola karma eşitlemesini devre dışı bırakma gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturun ve yapılandırın][create-azure-ad-ds-instance].

## <a name="use-security-settings-to-disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Zayıf şifrelemeleri ve NTLM parola karması eşitlemesini devre dışı bırakmak için güvenlik ayarlarını kullanın

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Arama yapın ve **Azure AD Domain Services** seçin.
1. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Sol taraftaki **güvenlik ayarları**' nı seçin.
1. Aşağıdaki ayarlar için **devre dışı bırak** 'a tıklayın:
   - **Yalnızca TLS 1,2 modu**
   - **NTLM kimlik doğrulaması**
   - **Şirket içinden NTLM parola eşitleme**

   ![Zayıf şifrelemeleri ve NTLM parola karması eşitlemesini devre dışı bırakma güvenlik ayarlarının ekran görüntüsü](media/secure-your-domain/security-settings.png)

## <a name="use-powershell-to-disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Zayıf şifrelemeleri ve NTLM parola karması eşitlemesini devre dışı bırakmak için PowerShell 'i kullanma

Gerekirse, [Azure PowerShell yükleyip yapılandırın](/powershell/azure/install-az-ps). [Connect-AzAccount][Connect-AzAccount] cmdlet 'Ini kullanarak Azure aboneliğinizde oturum açın. 

Ayrıca gerekirse, [Azure AD PowerShell 'i yükleyip yapılandırın](/powershell/azure/active-directory/install-adv2). [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda oturum açarak emin olun.

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

Son olarak, tanımlı güvenlik ayarlarını [set-AzResource][Set-AzResource] cmdlet 'ini kullanarak yönetilen etki alanına uygulayın. İlk adımdan Azure AD DS kaynağını ve önceki adımdaki güvenlik ayarlarını belirtin.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Yönetilen etki alanına güvenlik ayarlarının uygulanması birkaç dakika sürer.

> [!IMPORTANT]
> NTLM 'yi devre dışı bıraktıktan sonra, yönetilen etki alanındaki tüm parola karmalarını kaldırmak için Azure AD Connect ' de tam Parola karması eşitlemesi gerçekleştirin. NTLM 'yi devre dışı bırakırsanız ancak parola karması eşitlemesine zorlamazsanız, bir kullanıcı hesabı için NTLM parola karmaları yalnızca bir sonraki parola değişikliğine kaldırılır. Bu davranış, bir kullanıcının kimlik doğrulama yöntemi olarak NTLM 'nin kullanıldığı bir sistemde önbelleğe alınmış kimlik bilgileri varsa oturum açmaya devam etmesine izin verebilir.
>
> NTLM parola karması Kerberos parola karmasından farklıysa, NTLM 'ye geri dönüş çalışmaz. VM 'nin yönetilen etki alanı denetleyicisiyle bağlantısı varsa, önbelleğe alınmış kimlik bilgileri de artık çalışmaz.  

## <a name="next-steps"></a>Sonraki adımlar

Eşitleme işlemi hakkında daha fazla bilgi edinmek için bkz. [nesneleri ve kimlik bilgilerini yönetilen bir etki alanında eşitleme][synchronization].

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
