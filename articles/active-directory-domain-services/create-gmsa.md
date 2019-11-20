---
title: Azure AD Domain Services için yönetilen hizmet hesaplarını Gruplandır | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen etki alanları ile kullanmak üzere bir grup yönetilen hizmet hesabı (gMSA) oluşturmayı öğrenin
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: iainfou
ms.openlocfilehash: 1cfddf14d60b7d73bae283a18732c7c99ae22b4d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898222"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Azure AD Domain Services içinde bir grup yönetilen hizmet hesabı (gMSA) oluşturun

Uygulamalar ve hizmetler genellikle diğer kaynaklarla kimlik doğrulaması yapmak için bir kimliğe sahip olmalıdır. Örneğin, bir Web hizmetinin bir veritabanı hizmeti ile kimlik doğrulaması yapması gerekebilir. Bir uygulama veya hizmette, Web sunucusu grubu gibi birden çok örnek varsa, bu kaynakların kimliklerini el ile oluşturma ve yapılandırma zaman alıcı alır. Bunun yerine, Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanında bir grup yönetilen hizmet hesabı (gMSA) oluşturulabilir. Windows işletim sistemi, büyük kaynak gruplarının yönetimini kolaylaştıran bir gMSA 'nın kimlik bilgilerini otomatik olarak yönetir.

Bu makalede, Azure AD DS yönetilen bir etki alanında gMSA 'Nın nasıl oluşturulacağı gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services bir örnek oluşturmak ve yapılandırmak][create-azure-ad-ds-instance]için öğreticiyi doldurun.
* Azure AD DS yönetilen etki alanına katılmış bir Windows Server Yönetim sanal makinesi.
    * Gerekirse, [bir yönetim sanal makinesi oluşturmak][tutorial-create-management-vm]için öğreticiyi izleyin.

## <a name="managed-service-accounts-overview"></a>Yönetilen hizmet hesaplarına genel bakış

Tek başına yönetilen hizmet hesabı (sMSA), parolası otomatik olarak yönetilen bir etki alanı hesabıdır. Bu yaklaşım hizmet asıl adı (SPN) yönetimini basitleştirir ve diğer yöneticilere Temsilcili yönetim sağlar. Hesap için kimlik bilgilerini el ile oluşturmanız ve döndürmeniz gerekmez.

Grup tarafından yönetilen hizmet hesabı (gMSA), aynı yönetim basitleştirmesine, ancak etki alanındaki birden çok sunucuya yönelik olarak sahiptir. Bir gMSA, bir sunucu grubunda barındırılan tüm hizmet örneklerinin, karşılıklı kimlik doğrulama protokollerinin çalışması için aynı hizmet sorumlusunu kullanmasına izin verir. Bir gMSA hizmet sorumlusu olarak kullanıldığında, Windows işletim sistemi, yöneticinin güvenmek yerine hesabın parolasını yönetir.

Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesapları (gMSA) genel bakış][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Azure AD DS hizmet hesaplarını kullanma

Azure AD DS yönetilen etki alanları Microsoft tarafından kilitlendiğinden ve yönetiliyorsa, hizmet hesapları kullanılırken bazı önemli noktalar vardır:

* Yönetilen etki alanındaki özel kuruluş birimlerinde (OU) hizmet hesapları oluşturun.
    * Yerleşik *Aaddc kullanıcıları* veya *Aaddc bilgisayarları* OU 'larda bir hizmet hesabı oluşturamazsınız.
    * Bunun yerine, Azure AD DS yönetilen etki alanında [Özel BIR OU oluşturun][create-custom-ou] ve ardından bu özel OU 'da hizmet hesapları oluşturun.
* Anahtar Dağıtım Hizmetleri (KDS) kök anahtarı önceden oluşturulmuştur.
    * KDS kök anahtarı, gMSAs için parola oluşturmak ve almak için kullanılır. Azure AD DS 'de, KDS kökü sizin için oluşturulur.
    * Başka bir oluşturma ayrıcalığınız yok veya varsayılan, KDS kök anahtarını görüntüle.

## <a name="create-a-gmsa"></a>GMSA oluşturma

İlk olarak, [New-ADOrganizationalUnit][New-AdOrganizationalUnit] cmdlet 'ini kullanarak özel bir OU oluşturun. Özel OU 'Lar oluşturma ve yönetme hakkında daha fazla bilgi için bkz. [Azure AD DS 'Daki özel OU 'lar][create-custom-ou].

Aşağıdaki örnekte, *contoso.com*adlı Azure AD DS yönetilen etki alanında *Mynewou* adlı özel bir OU oluşturulur. Kendi OU ve yönetilen etki alanı adınızı kullanın:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=contoso,DC=COM"
```

Şimdi [New-ADServiceAccount][New-ADServiceAccount] cmdlet 'ini kullanarak bir gMSA oluşturun. Aşağıdaki örnek parametreler tanımlanmıştır:

* **-Ad** *WebFarmSvc* olarak ayarlandı
* **-Path** parametresi, önceki adımda oluşturulan gMSA için özel OU 'yu belirtir.
* DNS girdileri ve hizmet sorumlusu adları *WebFarmSvc.contoso.com* için ayarlanır
* *Contoso-sunucu $* ' daki sorumlular, parolayı almasına izin verilir.

Kendi adlarınızı ve etki alanı adlarınızı belirtin.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.contoso.com `
    -Path "OU=MYNEWOU,DC=contoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
        http/WebFarmSvc.contoso.com/contoso, `
        http/WebFarmSvc/contoso.com, `
        http/WebFarmSvc/contoso `
    -PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

Uygulamalar ve hizmetler artık gerektiğinde gMSA 'Yı kullanacak şekilde yapılandırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

GMSAs hakkında daha fazla bilgi için bkz. [Grup tarafından yönetilen hizmet hesapları ile çalışmaya][gmsa-start]başlama.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
