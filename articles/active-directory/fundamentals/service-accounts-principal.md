---
title: Azure Active Directory 'de hizmet sorumlularının güvenliğini sağlama
description: Hizmet sorumlularını bulun, değerlendirin ve güvenli hale getirin.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 297c0a53fb2ab4ee0b2c5291cabf5a63c8841664
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604711"
---
# <a name="securing-service-principals"></a>Hizmet sorumlularının güvenliğini sağlama

Azure Active Directory (Azure AD) [hizmet sorumlusu](../develop/app-objects-and-service-principals.md) , tek bir Kiracıdaki veya dizindeki bir uygulama nesnesinin yerel gösterimidir.  Uygulama örneğinin kimliği olarak çalışır. Hizmet sorumluları, uygulamaya kimlerin erişebileceğini ve uygulamanın erişebileceği kaynakları tanımlar. Bir hizmet sorumlusu, uygulamanın kullanıldığı her bir kiracıda oluşturulur ve genel olarak benzersiz uygulama nesnesine başvurur. Kiracı, hizmet sorumlusunun oturum açma ve kaynaklara erişme güvenliğini sağlar.  

### <a name="tenant-service-principal-relationships"></a>Kiracı hizmeti sorumlusu ilişkileri
Tek kiracılı bir uygulamanın, kendi ana kiracısında yalnızca bir hizmet sorumlusu vardır. Çok kiracılı bir Web uygulaması veya API, her kiracıda bir hizmet sorumlusu gerektirir. Bir hizmet sorumlusu, bu kiracıya ait bir kullanıcı uygulamanın veya API 'sinin kullanımına yeniden geldiğinde oluşturulur. Bu izin, çok kiracılı uygulama ile ilişkili hizmet sorumluları arasında bire çok bir ilişki oluşturur.

Çok kiracılı bir uygulama tek bir kiracıda bulunur ve diğer kiracılarda örnekleri olacak şekilde tasarlanmıştır. Hizmet olarak yazılım (SaaS) uygulamaları, çok kiracılı için tasarlanmıştır. Uygulama ve kullanıcıları için hem tek kiracı hem de çok kiracılı kullanım durumlarında doğru güvenlik duruşunu sağlamak için hizmet sorumlularını kullanın.

## <a name="applicationid-and-objectid"></a>ApplicationId ve objectID

Belirli bir uygulama örneğinin iki farklı özelliği vardır: ApplicationId (ClientID olarak da bilinir) ve objectID.

> [!NOTE] 
> Uygulama ve hizmet sorumlusu, kimlik doğrulaması ile ilgili görevler bağlamında bir uygulamaya gevşek bir şekilde başvurulurken birbirinin yerine kullanılacağını fark edebilirsiniz. Ancak, Azure AD 'de uygulamaların iki farklı temsilleridir.
 

ApplicationId, genel uygulamayı temsil eder ve kiracılar genelinde tüm uygulama örnekleri için aynıdır. ObjectID, bir uygulama nesnesi için benzersiz bir değerdir ve hizmet sorumlusunu temsil eder. Kullanıcılar, gruplar ve diğer kaynaklarda olduğu gibi objectID, Azure AD 'de bir uygulama örneğini benzersiz bir şekilde belirlemesine yardımcı olur.

Bu konuyla ilgili daha ayrıntılı bilgi için bkz. [uygulama ve hizmet sorumlusu ilişkisi](../develop/app-objects-and-service-principals.md).

Azure PowerShell, Azure CLı, Microsoft Graph, Azure portal ve diğer araçları kullanarak bir kiracıda bir uygulama ve hizmet sorumlusu nesnesi (ObjectID) oluşturabilirsiniz. 

![Uygulama KIMLIĞI ve nesne KIMLIĞI alanları vurgulanmış şekilde yeni bir uygulama kaydı gösteren ekran görüntüsü.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulaması

Hizmet sorumlularını kullanarak kimlik doğrulama için iki mekanizma vardır: istemci sertifikaları ve istemci gizli dizileri. 

![ Yeni uygulama sayfasının, vurgulanan sertifikaları ve istemci gizli dizileri alanını gösteren ekran görüntüsü.](./media/securing-service-accounts/secure-principal-certificates.png)

Sertifikalar daha güvenlidir: mümkünse istemci sertifikalarını kullanın. İstemci gizliliklerinden farklı olarak, istemci sertifikaları yanlışlıkla koda Katıştırılamaz. Donanım güvenlik modülleri tarafından korunan anahtarları kullanarak aşağıdaki varlıkları şifrelemek mümkün olduğunda sertifika ve gizli dizi yönetimi için Azure Key Vault kullanın:

* kimlik doğrulama anahtarları

* depolama hesabı anahtarları

* veri şifreleme anahtarları

* . pfx dosyaları

* Parolanız 

Azure Key Vault hakkında daha fazla bilgi ve sertifika ve gizli yönetim için nasıl kullanılacağı hakkında daha fazla bilgi için, bkz. [Azure Key Vault](../../key-vault/general/overview.md) ve [Azure Portal kullanarak Key Vault erişim ilkesi atama](../../key-vault/general/assign-access-policy-portal.md). 

 ### <a name="challenges-and-mitigations"></a>Güçlükler ve azaltmaları
Aşağıdaki tabloda, hizmet sorumlularını kullanırken karşılaşabileceğiniz güçlüklere yönelik azaltmaları sunulmaktadır.


| Zorluklar| Risk Azaltıcı Etkenler |
| - | - |
| Ayrıcalıklı rollere atanan hizmet sorumluları için erişim gözden geçirmeleri.| Bu işlevsellik önizleme aşamasındadır ve henüz yaygın olarak kullanılamaz. |
| Hizmet sorumlularının erişimini inceler| Azure portal kullanarak kaynağın erişim denetim listesine el ile denetim. |
| İle permissioned hizmet sorumlusu| Otomasyon Hizmeti hesapları veya hizmet sorumlularını oluştururken, yalnızca görev için gereken izinleri sağlayın. Ayrıcalıkların azaltılmasına izin olup olmadığını görmek için mevcut hizmet sorumlularını değerlendirin. |
|Hizmet sorumlularının kimlik bilgileri veya kimlik doğrulama yöntemlerinde yapılan değişiklikleri tanımla |Bu sorunu azaltmaya yardımcı olabilecek hassas Işlemler rapor çalışma kitabını kullanın. [Bu blog gönderisine bakın](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>Hizmet sorumlularını kullanarak hesap bulma
Hizmet sorumlularını kullanarak hesapları bulmak için aşağıdaki komutları çalıştırın.

Azure CLI’yı kullanma


`az ad sp list`

PowerShell’i kullanma

`Get-AzureADServicePrincipal -All:$true` 


Daha fazla bilgi için bkz. [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="assess-service-principal-security"></a>Hizmet sorumlusu güvenliğini değerlendir

Hizmet sorumlularınızın güvenliğini değerlendirmek için, ayrıcalıkları ve kimlik bilgisi depolamayı değerlendirdiğinizden emin olun.

Aşağıdaki bilgileri kullanarak olası zorlukları azaltabilirsiniz.

|Zorluklar | Risk Azaltıcı Etkenler|
| - | - |
| Çok kiracılı bir uygulamaya onay veren kullanıcıyı algılayın ve çok kiracılı bir uygulamaya izin vermesini tespit edin | Çok kiracılı uygulamaları bulmak için aşağıdaki PowerShell 'i çalıştırın.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Kullanıcı onayını devre dışı bırakın. <br>Seçili izinler için doğrulanan yayımcıların Kullanıcı onaylamasına izin ver (önerilir) <br> Güvenilmeyen konumlardan gelen hizmet sorumlularını engellemek için koşullu erişimi kullanın. Bunları Kullanıcı bağlamı altında yapılandırın ve bunların belirteçleri hizmet sorumlusunu tetiklemek için kullanılmalıdır.|
|Hizmet sorumlusu kullanarak bir betikte sabit kodlanmış paylaşılan gizli dizi kullanımı.|Bir sertifika veya Azure Key Vault kullanın.|
|Sertifikayı veya gizli anahtarı kullanan izleme| Azure AD oturum açma günlüklerini kullanarak hizmet sorumlusunun oturum açma işlemlerini izleyin.|
Hizmet sorumlusu oturum açma, koşullu erişimle yönetiyoruz.| Azure AD oturum açma günlüklerini kullanarak oturum açma işlemlerini izleme
| Varsayılan Azure RBAC rolü katkıda bulunur. |İhtiyaçları değerlendirin ve bu gereksinimi karşılamak için en az olası izinlerle rolü uygulayın.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Bir kullanıcı hesabından hizmet sorumlusuna gitme  
Hizmet sorumlusu olarak bir Azure Kullanıcı hesabı kullanıyorsanız, [yönetilen bir kimliğe](../../app-service/overview-managed-identity.md?tabs=dotnet) veya hizmet sorumlusuna taşıyıp taşıyabileceğinizi değerlendirin. Yönetilen bir kimlik kullanmazsak, gerekli görevleri çalıştırmak için yeterli izinlere ve kapsama sahip bir hizmet sorumlusu sağlayın. Bir uygulamayı veya [PowerShell](../develop/howto-authenticate-service-principal-powershell.md)'i [kaydederek](../develop/howto-create-service-principal-portal.md)bir hizmet sorumlusu oluşturabilirsiniz.

Microsoft Graph kullanırken, [Bu örnekte olduğu gibi](/powershell/azure/create-azure-service-principal-azureps), belirli API 'nin belgelerini denetleyin ve uygulama için izin türünün desteklendiğinden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

**Hizmet sorumluları hakkında daha fazla bilgi için:**

[Hizmet sorumlusu oluşturma](../develop/howto-create-service-principal-portal.md)

 [Hizmet sorumlusu oturum açma işlemlerini izleme](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**Hizmet hesaplarının güvenliğini sağlama hakkında daha fazla bilgi için:**

[Azure hizmet hesaplarına giriş](service-accounts-introduction-azure.md)

[Yönetilen kimliklerin güvenliğini sağlama](service-accounts-managed-identities.md)

[Azure hizmet hesaplarını yöneten](service-accounts-governing-azure.md)

[Şirket içi hizmet hesaplarına giriş](service-accounts-on-premises.md)
