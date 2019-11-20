---
title: Azure AD Domain Services hizmet sorumlusu uyarılarını çözümle | Microsoft Docs
description: Azure Active Directory Domain Services için hizmet sorumlusu yapılandırma uyarılarını nasıl giderebileceğinizi öğrenin
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257956"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Bilinen sorunlar: Azure Active Directory Domain Services 'de hizmet sorumlusu uyarıları

[Hizmet sorumluları](../active-directory/develop/app-objects-and-service-principals.md) , Azure platformunun Azure AD DS yönetilen bir etki alanını yönetmek, güncelleştirmek ve sürdürmek için kullandığı uygulamalardır. Bir hizmet sorumlusu silinirse, Azure AD DS yönetilen etki alanındaki işlevler etkilenir.

Bu makale, hizmet sorumlusu ile ilgili yapılandırma uyarılarını gidermenize ve çözmenize yardımcı olur.

## <a name="alert-aadds102-service-principal-not-found"></a>Uyarı AADDS102: Hizmet sorumlusu bulunamadı

### <a name="alert-message"></a>Uyarı iletisi

*Azure AD Domain Services çalışması için gereken bir hizmet sorumlusu Azure AD dizininden silindi. Bu yapılandırma, Microsoft 'un yönetilen etki alanınızı izleme, yönetme, düzeltme eki uygulama ve eşitlemeye yönelik yeteneğini etkiler.*

Gerekli bir hizmet sorumlusu silinirse, Azure platformu otomatik yönetim görevleri gerçekleştiremez. Azure AD DS yönetilen etki alanı güncelleştirmeleri doğru bir şekilde uygulayamaz veya yedeklemeleri alamaz.

### <a name="check-for-missing-service-principals"></a>Eksik hizmet sorumlularını denetle

Hangi hizmet sorumlusunun eksik olduğunu ve yeniden oluşturulması gerektiğini denetlemek için aşağıdaki adımları izleyin:

1. Azure portal sol taraftaki gezinti menüsünden **Azure Active Directory** ' i seçin.
1. **Kurumsal uygulamalar**' ı seçin. **Uygulama türü** açılan menüsünden *tüm uygulamalar* ' ı seçin ve ardından **Uygula**' yı seçin.
1. Uygulama kimliklerinin her birini arayın. Mevcut bir uygulama bulunamazsa, hizmet sorumlusunu oluşturmak veya ad alanını yeniden kaydettirmek için *çözüm* adımlarını izleyin.

    | Uygulama Kimliği | Çözüm |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Eksik hizmet sorumlusunu yeniden oluşturma](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Microsoft. AAD ad alanını yeniden kaydedin](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Microsoft. AAD ad alanını yeniden kaydedin](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Microsoft. AAD ad alanını yeniden kaydedin](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Eksik hizmet sorumlusunu yeniden oluşturma

Uygulama KIMLIĞI *2565bd9d-dad50-47d4-8B85-4c97f669dc36* Azure AD dizininizde yoksa, aşağıdaki adımları gerçekleştirmek IÇIN Azure AD PowerShell kullanın. Daha fazla bilgi için bkz. [Azure AD PowerShell 'i Install](/powershell/azure/active-directory/install-adv2).

1. Azure AD PowerShell modülünü yükleyip aşağıdaki gibi içeri aktarın:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Şimdi [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal] cmdlet 'ini kullanarak hizmet sorumlusunu yeniden oluşturun:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Azure AD DS yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

### <a name="re-register-the-microsoft-aad-namespace"></a>Microsoft AAD ad alanını yeniden kaydetme

Uygulama KIMLIĞI *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022*veya *d87dcbc6-a371-462e-88e3-28ad15ec4e64* , Azure AD dizininizde yoksa, aşağıdaki adımları gerçekleştirmek için *Microsoft. AAD* kaynak sağlayıcısını yeniden kaydedin:

1. Azure portal, **abonelikleri**arayıp seçin.
1. Azure AD DS yönetilen etki alanınız ile ilişkili aboneliği seçin.
1. Sol taraftaki gezinmede **kaynak sağlayıcıları**' nı seçin.
1. *Microsoft. AAD*araması yapın ve ardından **yeniden kaydet**' i seçin.

Azure AD DS yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Uyarı AADDS105: Parola eşitleme uygulaması güncel değil

### <a name="alert-message"></a>Uyarı iletisi

*"D87dcbc6-a371-462e-88e3-28ad15ec4e64" uygulama KIMLIĞINE sahip hizmet sorumlusu silindi ve sonra yeniden oluşturuldu. Yeniden oluşturma, yönetilen etki alanınızı hizmet etmek için gereken Azure AD Domain Services kaynaklardaki tutarsız izinlerin arkasında kalır. Yönetilen etki alanındaki parolaların eşitlenmesi etkilenebilir.*

Azure AD DS, Kullanıcı hesaplarını ve kimlik bilgilerini Azure AD 'den otomatik olarak eşitler. Bu işlem için kullanılan Azure AD uygulamasıyla ilgili bir sorun varsa Azure AD DS ile Azure AD arasında kimlik bilgileri eşitlemesi başarısız olur.

### <a name="resolution"></a>Çözüm

Kimlik bilgileri eşitleme için kullanılan Azure AD uygulamasını yeniden oluşturmak için Azure AD PowerShell 'i kullanarak aşağıdaki adımları uygulayın. Daha fazla bilgi için bkz. [Azure AD PowerShell 'i Install](/powershell/azure/active-directory/install-adv2).

1. Azure AD PowerShell modülünü yükleyip aşağıdaki gibi içeri aktarın:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Şimdi aşağıdaki PowerShell cmdlet 'lerini kullanarak eski uygulamayı ve nesneyi silin:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Her iki uygulamayı da sildikten sonra, Azure platformu onları otomatik olarak yeniden oluşturur ve parola eşitlemesini sürdürmeye çalışır. Azure AD DS yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

## <a name="next-steps"></a>Sonraki adımlar

Hala sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
