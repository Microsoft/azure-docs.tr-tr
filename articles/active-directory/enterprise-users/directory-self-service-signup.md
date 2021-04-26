---
title: E-posta doğrulanan kullanıcılar için self servis kaydolma-Azure AD | Microsoft Docs
description: Azure Active Directory (Azure AD) kuruluşunda self servis kaydolma kullanma
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95625886ed11256a40e5993540d7e545134d6dd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860873"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Azure Active Directory için self servis kaydolma nedir?

Bu makalede, Azure Active Directory (Azure AD) ' de bir kuruluşu doldurmak için self servis kaydolma 'nın nasıl kullanılacağı açıklanmaktadır. Yönetilmeyen bir Azure AD kuruluştan bir etki alanı adı almak istiyorsanız, bkz. [yönetilmeyen bir dizini yönetici olarak alma](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Neden self servis kaydolma kullanmalıyım?

* Müşterileri daha hızlı istedikleri hizmetlere alın
* Hizmet için e-posta tabanlı teklifler oluşturma
* Kullanıcıların kolay anımsanacak iş e-posta diğer adlarını kullanarak kimlik oluşturmalarına hızlı bir şekilde izin veren e-posta tabanlı kaydolma akışları oluşturma
* Self servis tarafından oluşturulan bir Azure AD dizini, diğer hizmetler için kullanılabilecek bir yönetilen dizine açılabilir

## <a name="terms-and-definitions"></a>Terimler ve tanımlar

* **Self servis kaydolma**: Bu, bir kullanıcının bir bulut hizmetine kaydolup kendi e-posta etki alanına göre Azure AD 'de otomatik olarak oluşturulan bir kimliğe sahip olduğu yöntemdir.
* **Yönetilmeyen Azure ad dizini**: Bu, kimliğin oluşturulduğu dizindir. Yönetilmeyen Dizin, genel yöneticisi olmayan bir dizindir.
* **E-posta doğrulanan kullanıcı**: Bu, Azure AD 'de bir kullanıcı hesabı türüdür. Self servis teklifine kaydolduktan sonra otomatik olarak oluşturulan bir kimliğe sahip bir Kullanıcı, e-posta doğrulanan kullanıcı olarak bilinir. E-posta doğrulanan kullanıcı, creationmethod = Emaildoğrulandı ile etiketlenmiş bir dizinin normal üyesidir.

## <a name="how-do-i-control-self-service-settings"></a>Self Servis ayarlarını denetlemek Nasıl yaparım??

Yöneticiler bugün iki self servis denetimine sahiptir. Şunları kontrol edebilirler:

* Kullanıcılar, bir e-posta ile dizine katılabilir
* Kullanıcılar uygulamalar ve hizmetler için kendilerini lisanslayabilirler

### <a name="how-can-i-control-these-capabilities"></a>Bu özellikleri nasıl denetleyebilirim?

Yönetici, aşağıdaki Azure AD cmdlet Set-MsolCompanySettings parametrelerini kullanarak bu özellikleri yapılandırabilir:

* **AllowEmailVerifiedUsers** bir kullanıcının dizin oluşturup oluşturamayacağını veya katılamayacağını denetler. Bu parametreyi $false olarak ayarlarsanız, hiçbir e-posta doğrulanan kullanıcı dizine katılabilir.
* **Allowadhocabonelikleri** , kullanıcıların Self Servis kaydolma işlemini gerçekleştirmesine olanak sağlar. Bu parametreyi $false olarak ayarlarsanız, Kullanıcı self servis kaydolma işlemini gerçekleştiremez.
  
AllowEmailVerifiedUsers ve Allowadhocabonelikleri, yönetilen veya yönetilmeyen bir dizine uygulanabilecek Dizin genelinde ayarlar. İşte şöyle bir örnek:

* Contoso.com gibi doğrulanmış bir etki alanı ile bir dizin yönetebilirsiniz.
* Contoso.com giriş dizininde zaten mevcut olmayan () bir kullanıcıyı davet etmek için farklı bir dizinden B2B işbirliği kullanıyorsunuz userdoesnotexist@contoso.com
* Ana dizinde AllowEmailVerifiedUsers açıktır

Yukarıdaki koşullar doğru ise, ana dizinde bir üye Kullanıcı oluşturulur ve davet edilen dizinde bir B2B Konuk Kullanıcı oluşturulur.

Flow ve PowerApps deneme sürümü oturum açma bilgileri hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Mevcut kullanıcılarımın Power BI'ı kullanmaya başlamasını nasıl önleyebilirim?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Kuruluşunuzda Flow ile ilgili soru-cevap](/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Denetimler birlikte nasıl çalışır?
Bu iki parametre, self servis kaydolma üzerinde daha kesin denetim tanımlamak için birlikte kullanılabilir. Örneğin, aşağıdaki komut, kullanıcıların Self Servis kaydolma gerçekleştirmesine izin verir, ancak yalnızca bu kullanıcıların Azure AD 'de zaten bir hesabı varsa (diğer bir deyişle, bir e-posta doğrulanan hesabı olması gereken kullanıcılar self servis kaydolma işlemini gerçekleştiremez):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Aşağıdaki akış çizelgesi, bu parametrelerin farklı birleşimlerini ve dizin ve self servis kaydolma koşullarını açıklamaktadır.

![Self Servis kaydolma denetimlerinin akış çizelgesi](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Bu ayarın ayrıntıları, Get-Msolcompanyınformation PowerShell cmdlet 'i tarafından alınabilir. Bu konuda daha fazla bilgi edinmek için bkz. [Get-Msolcompanyınformation](/powershell/module/msonline/get-msolcompanyinformation).

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

Bu parametrelerin nasıl kullanılacağına ilişkin daha fazla bilgi ve örnekler için bkz. [set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD’ye özel etki alanı adı ekleme](../fundamentals/add-custom-domain.md)
* [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet Başvurusu](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)
* [İş veya okul hesabınızı yönetilmeyen bir dizinde kapatma](users-close-account.md)
