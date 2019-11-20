---
title: E-posta doğrulanan kullanıcılar için self servis kaydolma-Azure AD | Microsoft Docs
description: Azure Active Directory (Azure AD) kiracısında self servis kaydolma kullanma
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027629"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Azure Active Directory için self servis kaydolma nedir?

Bu makalede, Azure Active Directory (Azure AD) ' de bir kuruluşu doldurmak için self servis kaydolma 'nın nasıl kullanılacağı açıklanmaktadır. Yönetilmeyen bir Azure AD kuruluştan bir etki alanı adı almak istiyorsanız, bkz. [yönetilmeyen bir dizini yönetici olarak alma](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Neden self servis kaydolma kullanmalıyım?
* Müşterileri daha hızlı istedikleri hizmetlere alın
* Hizmet için e-posta tabanlı teklifler oluşturma
* Kullanıcıların kolay anımsanacak iş e-posta diğer adlarını kullanarak kimlik oluşturmalarına hızlı bir şekilde izin veren e-posta tabanlı kaydolma akışları oluşturma
* Self servis tarafından oluşturulan bir Azure AD dizini, diğer hizmetler için kullanılabilecek bir yönetilen dizine açılabilir

## <a name="terms-and-definitions"></a>Hüküm ve tanımlar
* **Self servis kaydolma**: Bu, bir kullanıcının bir bulut hizmetine kaydolup kendi e-posta etki alanına göre Azure AD 'de otomatik olarak oluşturulan bir kimliğe sahip olduğu yöntemdir.
* **Yönetilmeyen Azure ad dizini**: Bu, kimliğin oluşturulduğu dizindir. Yönetilmeyen Dizin, genel yöneticisi olmayan bir dizindir.
* **E-posta doğrulanan kullanıcı**: Bu, Azure AD 'de bir kullanıcı hesabı türüdür. Self servis teklifine kaydolduktan sonra otomatik olarak oluşturulan bir kimliğe sahip bir Kullanıcı, e-posta doğrulanan kullanıcı olarak bilinir. E-posta doğrulanan kullanıcı, creationmethod = Emaildoğrulandı ile etiketlenmiş bir dizinin normal üyesidir.

## <a name="how-do-i-control-self-service-settings"></a>Self Servis ayarlarını denetlemek Nasıl yaparım??
Yöneticiler bugün iki self servis denetimine sahiptir. Şunları kontrol edebilirler:

* Kullanıcılar, bir e-posta ile dizine katılabilir
* Kullanıcılar uygulamalar ve hizmetler için kendilerini lisanslayabilirler

### <a name="how-can-i-control-these-capabilities"></a>Bu özellikleri nasıl denetleyebilirim?
Bir yönetici, aşağıdaki Azure AD cmdlet Set-MsolCompanySettings parametrelerini kullanarak bu özellikleri yapılandırabilir:

* **AllowEmailVerifiedUsers** bir kullanıcının dizin oluşturup oluşturamayacağını veya katılamayacağını denetler. Bu parametreyi $false olarak ayarlarsanız, hiçbir e-posta doğrulanan kullanıcı dizine katılabilir.
* **Allowadhocabonelikleri** , kullanıcıların Self Servis kaydolma işlemini gerçekleştirmesine olanak sağlar. Bu parametreyi $false olarak ayarlarsanız, Kullanıcı self servis kaydolma işlemini gerçekleştiremez.
  
AllowEmailVerifiedUsers ve Allowadhocabonelikleri, yönetilen veya yönetilmeyen bir dizine uygulanabilecek Dizin genelinde ayarlar. İşte şöyle bir örnek:

* Contoso.com gibi doğrulanmış bir etki alanı ile bir dizin yönetebilirsiniz.
* Contoso.com giriş dizininde zaten mevcut olmayan bir kullanıcıyı (userdoesnotexist@contoso.com) davet etmek için farklı bir dizinden B2B işbirliğini kullanırsınız
* Ana dizinde AllowEmailVerifiedUsers açıktır

Yukarıdaki koşullar doğru ise, ana dizinde bir üye Kullanıcı oluşturulur ve davet edilen dizinde bir B2B Konuk Kullanıcı oluşturulur.

Flow ve PowerApps deneme oturum açma işlemleri, **Allowadhocabonelikler** ayarı tarafından denetlenmez. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Var olan kullanıcılarınızın Power BI kullanmaya başlamasını nasıl önleyebilirim?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Kuruluşunuzda akış Q &](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Denetimler birlikte nasıl çalışır?
Bu iki parametre, self servis kaydolma üzerinde daha kesin denetim tanımlamak için birlikte kullanılabilir. Örneğin, aşağıdaki komut, kullanıcıların Self Servis kaydolma gerçekleştirmesine izin verir, ancak yalnızca bu kullanıcıların Azure AD 'de zaten bir hesabı varsa (diğer bir deyişle, bir e-posta doğrulanan hesabı olması gereken kullanıcılar self servis kaydolma işlemini gerçekleştiremez):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Aşağıdaki akış çizelgesi, bu parametrelerin farklı birleşimlerini ve dizin ve self servis kaydolma koşullarını açıklamaktadır.

![Self Servis kaydolma denetimlerinin akış çizelgesi](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Bu parametrelerin nasıl kullanılacağına ilişkin daha fazla bilgi ve örnekler için bkz. [set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD 'ye özel etki alanı adı ekleme](../fundamentals/add-custom-domain.md)
* [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet Başvurusu](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [İş veya okul hesabınızı yönetilmeyen bir dizinde kapatma](users-close-account.md)
