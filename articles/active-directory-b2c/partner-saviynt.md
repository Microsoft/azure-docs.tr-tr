---
title: Azure Active Directory B2C ile Saviynt yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Daha iyi güvenlik, idare ve uyumluluğu KOLAYLAŞTıRMAYA ve yükseltmeye yönelik uygulamalar arası tümleştirme için Saviynt ile Azure Active Directory B2C yapılandırma öğreticisi. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fe5a2114a99b7867ac5e51b6bf9ec542ad47ca13
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257900"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile Saviynt yapılandırma öğreticisi

Bu örnek öğreticide, Azure Active Directory (AD) B2C 'yi [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/)ile tümleştirme hakkında rehberlik sağlıyoruz. Saviynt 'ın Güvenlik Yöneticisi platformu, tek bir birleştirilmiş platformda görünürlük, güvenlik ve BT 'nin işletmelerini sağlar. Saviynt, uygulama riskini ve idare, altyapı yönetimi, ayrıcalıklı hesap yönetimi ve müşteri risk analizini içerir.

Bu örnek öğreticide, Azure AD B2C kullanıcılara yönelik ayrıntılı erişim denetimi tabanlı yönetim sağlamak için Saviynt ayarlayacaksınız. Saviynt, bir kullanıcının Azure AD B2C kullanıcıları yönetme yetkisine sahip olup olmadığını anlamak için aşağıdaki denetimleri yapar.

- Bir kullanıcının belirli bir işlemi gerçekleştirip gerçekleştiremediğine yönelik özellik düzeyi güvenlik. Örneğin, kullanıcı oluşturun, Kullanıcı güncelleştirin, Kullanıcı parolasını sıfırlayın vb.

- Alan düzeyi güvenliği Kullanıcı yönetim işlemleri sırasında bir kullanıcının başka bir kullanıcının belirli bir özniteliğini okuyup yazmadığını belirleme. Örneğin, Yardım Masası Aracısı yalnızca telefon numarasını güncelleştirebilir, diğer tüm öznitelikler salt okunurdur.

- Bir kullanıcının belirli bir kullanıcı için belirli bir işlem gerçekleştirip gerçekleştirmediğine yönelik veri düzeyi güvenliği. Örneğin, UK bölgesi için Yardım Masası Yöneticisi yalnızca UK kullanıcılarını yönetebilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- [Azure AD B2C kiracısı](./tutorial-create-tenant.md). Kiracı, Azure aboneliğinize bağlı.

- Bir Saviynt [aboneliği](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>Senaryo açıklaması

Saviynt tümleştirmesi aşağıdaki bileşenleri içerir:

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) : müşterilerinizin, şirket içinde oturum açma, oturum açma ve profillerini yönetme konusunda özel denetim sağlayan bir hizmet olarak iş-müşteri kimliği.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) : Kullanıcı yaşam döngüsü yönetimi ve Azure AD B2C kullanıcıların yönetimine yönelik ayrıntılı bir yönetim sağlayan kimlik idare platformu.  

- [MICROSOFT Graph API](/graph/use-the-api) – bu apı, Azure AD B2C kullanıcıları ve Azure AD B2C erişimini yönetmek için Saviynt arabirimlerini sağlar.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![Saviynt mimari diyagramını gösteren resim](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Yetkilendirilmiş bir yönetici, Saviynt aracılığıyla Azure AD B2C Kullanıcı yönetme işlemini başlatır.
| 2. | Saviynt, yetkilendirilmiş yönetici belirli bir işlemi yapabiliyorsanız yetkilendirme altyapısını doğrular.
| 3. | Saviynt 'in yetkilendirme altyapısı bir yetkilendirme başarısı/hata yanıtı gönderir.
| 4. | Saviynt, yönetici temsilcisi tarafından gerekli işlemi yapmasına izin verir.
| 5. | Saviynt, kullanıcıyı Azure AD B2C yönetmek için Kullanıcı öznitelikleriyle birlikte Microsoft Graph API çağırır
| 6. | Microsoft Graph API 'SI, Azure AD B2C Kullanıcı oluşturma/güncelleştirme/silme olacaktır.
| 7. | Azure AD B2C, başarı/başarısızlık yanıtı gönderecek.
| 8. | Microsoft Graph API 'SI, yanıtı Saviynt döndürür.

## <a name="onboard-with-saviynt"></a>Saviynt ile ekleme

1. Saviynt hesabı oluşturmak için [Saviynt](https://saviynt.com/contact-us/) başvurun

2. Temsilcili yönetim ilkeleri oluşturun ve kullanıcıları farklı rollerle yönetici temsilcileri olarak atayın.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Saviynt ile Azure AD B2C yapılandırma

### <a name="create-an-azure-ad-application-for-saviynt"></a>Saviynt için bir Azure AD uygulaması oluşturma

1. [Azure portalında](https://portal.azure.com/#home) oturum açın.

2. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.

3. Azure portal arama yapın ve **Azure AD B2C** seçin.

4. Yeni **uygulama kayıtları**  >  **kayıt**' ı seçin.

5. Uygulama için bir ad girin. Örneğin, Saviynt ve **Oluştur**' u seçin.

6. **API izinleri** ' ne gidin ve **+ izin Ekle** ' yi seçin.

7. Istek API 'SI izinleri sayfası görüntülenir. **Microsoft API 'leri** sekmesini seçin ve yaygın olarak kullanılan Microsoft apı 'leri **Microsoft Graph** seçin.

8. Sonraki sayfaya gidin ve **Uygulama izinleri**' ni seçin.

9. **Dizin**' i seçin ve **Dizin. Read. All** ve **Directory. ReadWrite. All** onay kutularını seçin.

10. **Izin Ekle**' yi seçin. Eklenen izinleri gözden geçirin.

11. **Varsayılan dizin kaydetme için yönetici izni ver**' i seçin  >  .

12. **Sertifikalar ve gizlilikler** ' a gidip **+ istemci parolası Ekle**' yi seçin. İstemci gizli anahtarını girin, süre sonu seçeneğini belirleyin ve **Ekle**' yi seçin.

13. Gizli anahtar, Istemci gizli bölümünde oluşturulur ve görüntülenir.

    >[!NOTE]
    > Daha sonra istemci parolası gerekir.

14. **Genel Bakış ' a** gidin ve **Istemci KIMLIĞINI** ve **Kiracı kimliğini** alın.

15. Saviynt 'de kurulumu tamamlaması için kiracı KIMLIĞI, istemci KIMLIĞI ve istemci gizli anahtarı gerekir.

### <a name="enable-saviynt-to-delete-users"></a>Kullanıcıları silmek için Saviynt etkinleştir

Aşağıdaki adımlarda, Azure AD B2C içinde Kullanıcı silme işlemlerini gerçekleştirmek için Saviynt nasıl etkinleştirileceği açıklanmaktadır.

>[!NOTE]
>[Yönetici rollerine hizmet sorumlusu erişimi vermeden önce riski değerlendirin.](../active-directory/develop/app-objects-and-service-principals.md)

1. MSOnline PowerShell modülünün en son sürümünü bir Windows iş istasyonuna/sunucusuna yükler.

2. AzureAD PowerShell modülüne bağlanın ve aşağıdaki komutları yürütün:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>Çözümü test etme

Saviynt uygulama kiracınıza gidin ve Kullanıcı yaşam döngüsü yönetimini test edin ve idare kullanım örneğine erişin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [Web API uygulaması oluşturma](./add-web-api-application.md)