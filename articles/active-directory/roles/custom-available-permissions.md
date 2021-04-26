---
title: Uygulama kaydı için özel rol izinleri-Azure AD | Microsoft Docs
description: Uygulama kayıtlarını yönetmek için özel yönetici rolü izinleri verme.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af2c1b912afbcf44cefbfb021c592836dbde5b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466295"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory özel roller için uygulama kaydı izinleri

Bu makale, Azure Active Directory (Azure AD) içindeki özel rol tanımları için şu anda kullanılabilir olan uygulama kayıt izinlerini içerir.

## <a name="permissions-for-managing-single-tenant-applications"></a>Tek kiracılı uygulamaları yönetme izinleri

Özel rolünüzün izinlerini seçerken, yalnızca tek kiracılı uygulamaları yönetmek için erişim verme seçeneğiniz vardır. tek kiracılı uygulamalar yalnızca uygulamanın kaydedildiği Azure AD kuruluşundaki kullanıcılar tarafından kullanılabilir. tek kiracılı uygulamalar **Desteklenen hesap türleri** "yalnızca bu kuruluş dizininde hesaplar" olarak ayarlanmış şekilde tanımlanır. Graph API, tek kiracılı uygulamalarda Signınaudience özelliği "AzureADMyOrg" olarak ayarlanmıştır.

Yalnızca tek kiracılı uygulamaları yönetmek üzere erişim vermek için, aşağıdaki izinleri **uygulamalar. Myorgantors** alt türü ile kullanın. Örneğin, Microsoft. Directory/Applications. Myorganleştirme/temel/güncelleştirme.

Genel terimler alt türünün, izninin ve özellik kümesinin ne anlama geldiğini öğrenmek için [özel rollere genel bakış ' a](custom-overview.md) bakın. Aşağıdaki bilgiler uygulama kayıtlarına özgüdür.

### <a name="create-and-delete"></a>Oluştur ve Sil

Her biri farklı davranışa sahip olan uygulama kayıtları oluşturma özelliği için iki izin mevcuttur:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>Microsoft. Directory/Applications/createAsOwner

Bu iznin atanması, Oluşturucu oluşturulan uygulama kaydının ilk sahibi olarak eklenmekte ve oluşturulan uygulama kaydı, oluşturucunun 250 tarafından oluşturulan nesne kotasına göre sayılır.

#### <a name="microsoftdirectoryapplicationscreate"></a>Microsoft. Directory/uygulamalar/oluşturma

Bu iznin atanması, oluşturan uygulama kaydının ilk sahibi olarak eklenmeyecektir ve oluşturulan uygulama kaydı, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılmaz. Bu izni dikkatle kullanın çünkü dizin düzeyi kotasına ulaşılana kadar atanan uygulama kaydı oluşturulmasını hiçbir şey yok. Her iki izin atanırsa, bu izin önceliklidir.

Her iki izin atanırsa,/Create izni öncelikli olur. Bu,/createAsOwner izni oluşturucuyu ilk sahip olarak otomatik eklemez, ancak grafik API 'Leri veya PowerShell cmdlet 'leri kullanılırken uygulama kaydı oluşturma sırasında sahipler belirtilebilir.

İzin oluştur **Yeni kayıt** komutuna erişim izni verir.

[Bu izinler yeni kayıt portalı komutuna erişim izni verir](./media/custom-available-permissions/new-custom-role.png)

Uygulama kayıtlarını silebilme olanağı vermek için iki izin mevcuttur:

#### <a name="microsoftdirectoryapplicationsdelete"></a>Microsoft. Directory/uygulamalar/Sil

Subtype 'dan bağımsız olarak uygulama kayıtlarını silme yeteneği verir; diğer bir deyişle, tek kiracılı ve çok kiracılı uygulamalar.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. Myorganleştirme/silme

Yalnızca kuruluşunuzdaki hesaplara veya tek kiracılı uygulamalardaki (Myorganın alt türü) erişilebilen uygulama kayıtlarını silme yeteneği verir.

![Bu izinler, uygulama kaydını Sil komutuna erişim verir](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Oluşturma izinleri içeren bir rol atarken, rol atamasının dizin kapsamında yapılması gerekir. Kaynak kapsamında atanan bir oluşturma izni, uygulama kayıtları oluşturma olanağı sağlamaz.

### <a name="read"></a>Okuma

Kuruluştaki tüm üye kullanıcıları, varsayılan olarak uygulama kayıt bilgilerini okuyabilir. Ancak Konuk kullanıcılar ve uygulama hizmeti sorumluları olamaz. Bir Konuk Kullanıcı veya uygulamaya bir rol atamayı planlıyorsanız, uygun okuma izinlerini dahil etmeniz gerekir.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/uygulamalar/allProperties/Read

Kimlik bilgileri gibi herhangi bir durumda okunamayan özellikler dışında tek kiracılı ve çok kiracılı uygulamaların tüm özelliklerini okuma özelliği.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. Myorganleştirme/allProperties/Read

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/Applications/allProperties/Read ile aynı izinleri verir.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/uygulamalar/sahipler/okuma

Tek kiracılı ve çok kiracılı uygulamalardaki sahipler özelliğini okuma yeteneği verir. Uygulama kayıt sahipleri sayfasındaki tüm alanlara erişim izni verir:

![Bu izinler, uygulama kayıt sahipleri sayfasına erişim verir](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft. Directory/uygulamalar/standart/okuma

Standart uygulama kaydı özelliklerini okuma erişimi verir. Bu, uygulama kayıt sayfalarında özellikler içerir.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. Myorganleştirme/standart/okuma

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/Applications/standart/Read gibi aynı izinleri verir.

### <a name="update"></a>Güncelleştir

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/uygulamalar/allProperties/Update

Tek kiracılı ve çok kiracılı uygulamalardaki tüm özellikleri güncelleştirebilme.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. Myorganleştirme/allProperties/Update

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/Applications/allProperties/Update ile aynı izinleri verir.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme

Tek kiracılı ve çok kiracılı uygulamalarda desteklenen hesap türü (Signınaudience) özelliğini güncelleştirme özelliği.

![Bu izin, kimlik doğrulama sayfasında uygulama kaydı desteklenen hesap türü özelliğine erişim verir](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. Myorganleştirme/izleyici/güncelleştirme

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme ile aynı izinleri verir.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme

Tek kiracılı ve çok kiracılı uygulamalarda yanıt URL 'SI, oturum kapatma URL 'SI, örtük akış ve yayımcı etki alanı özelliklerini güncelleştirme özelliği. Desteklenen hesap türleri dışındaki uygulama kaydı kimlik doğrulama sayfasındaki tüm alanlara erişim verir:

![Uygulama kaydı kimlik doğrulamasına erişim izni verir ancak desteklenmez.](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. Myorganleştirme/kimlik doğrulama/güncelleştirme

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/uygulamalar/kimlik doğrulama/güncelleştirme ile aynı izinleri verir.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/uygulamalar/temel/güncelleştirme

Tek kiracılı ve çok kiracılı uygulamalardaki adı, logoyu, ana hat URL 'sini, hizmet URL 'sini ve gizlilik bildirimi URL özelliklerini güncelleştirme özelliği. Uygulama kaydı markalama sayfasındaki tüm alanlara erişim izni verir:

![Bu izin, uygulama kaydı markalama sayfasına erişim verir](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. Myorganleştirme/temel/güncelleştirme

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/Applications/Basic/Update ile aynı izinleri verir.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme

Tek kiracılı ve çok kiracılı uygulamalardaki sertifikaları ve istemci gizli dizilerini güncelleştirme özelliği. Uygulama kayıt sertifikaları & gizlilikler sayfasındaki tüm alanlara erişim verir:

![Bu izin, uygulama kayıt sertifikalarına & gizli dizi sayfasına erişim verir](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. Myorganleştirme/kimlik bilgileri/güncelleştirme

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme ile aynı izinleri verir.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>Microsoft. Directory/uygulamalar/sahipler/güncelleştirme

Tek kiracılı ve çok kiracılı üzerinde Owner özelliğini güncelleştirme özelliği. Uygulama kayıt sahipleri sayfasındaki tüm alanlara erişim izni verir:

![Bu izinler, uygulama kayıt sahipleri sayfasına erişim verir](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. Myorganleştirme/sahipler/güncelleştirme

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/Applications/Owners/Update ile aynı izinleri verir.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/uygulamalar/izinler/güncelleştirme

Temsilci izinleri, uygulama izinleri, yetkilendirilmiş istemci uygulamaları, gerekli izinler ve tek kiracılı ve çok kiracılı uygulamalarda izin özellikleri verme özelliği. İzin verme olanağı sağlamaz. Uygulama kaydı API 'SI izinlerinde tüm alanlara erişim verir ve bir API sayfası sunar:

![Bu izinler, uygulama kaydı API 'SI izinleri sayfasına erişim verir](./media/custom-available-permissions/app-registration-api-permissions.png)

![Bu izinler, bir API sayfasını açığa çıkarmak için uygulama kaydına erişim verir](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. Myorganleştirme/izinler/güncelleştirme

Yalnızca tek kiracılı uygulamalar için Microsoft. Directory/Applications/Permissions/Update ile aynı izinleri verir.

## <a name="required-license-plan"></a>Gerekli lisans planı

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal, Azure AD PowerShell ve Graph API](custom-create.md) kullanarak özel roller oluşturun
- [Rol atamalarını listeleme](view-assignments.md)
