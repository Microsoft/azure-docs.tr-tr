---
title: Azure Active Directory kimlik yaşam döngüsü yönetimi nedir? | Microsoft Belgeleri
description: Kimlik yaşam döngüsü yönetimine genel bakış açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65e1217041d85b66664792d9475cdfcb517559b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172444"
---
# <a name="what-is-identity-lifecycle-management"></a>Kimlik yaşam döngüsü yönetimi nedir?

Kimlik yönetimi, kuruluşların üretkenlik arasında bir denge elde etmesine yardımcı olur ve kuruluşa ne zaman katılabilecekleri gibi bir kişinin ihtiyacı olan kaynaklara ne kadar hızlı bir şekilde erişmesini sağlayabilir? Ve güvenlik-bu kişinin iş durumundaki değişiklikler nedeniyle, zaman içinde erişimin ne zaman içinde değiştirilmesi gerekir?

**Kimlik yaşam döngüsü yönetimi** , kimlik yönetimi için temel ve ölçekteki etkili idare, uygulamalar için kimlik yaşam döngüsü yönetim altyapısını modernleştirmeyi gerektirir. Kimlik yaşam döngüsü yönetimi amaçlar, tüm dijital kimlik yaşam döngüsü sürecini otomatikleştirin ve yönetebilir. 

![bulut sağlama](media/what-is-provisioning/cloud-1.png)

## <a name="what-is-a-digital-identity"></a>Dijital kimlik nedir?

Dijital kimlik, işletim sistemleri veya uygulamalar gibi bir veya daha fazla bilgi işlem kaynağı tarafından kullanılan bir varlıkla ilgili bilgiler. Bu varlıklar kişileri, kuruluşları, uygulamaları veya cihazları temsil edebilir.  Kimlik genellikle onunla ilişkili Öznitelikler (ad, tanımlayıcılar ve erişim yönetimi için kullanılan roller gibi özellikler) tarafından tanımlanır.  Bu öznitelikler yardım sistemleri, bu veya bu sistemi kullanmasına izin verilen ve kimlerin erişebileceği hakkında belirleyici bir şekilde sahip olur.  

## <a name="managing-the-lifecycle-of-digital-identities"></a>Dijital kimliklerin yaşam döngüsünü yönetme

Dijital kimlikleri yönetmek, özellikle de bir kişi gibi gerçek dünyadaki nesnelerin yanı sıra bu kuruluşun bir çalışanı olarak bir organizasyon ile ilişkilerini, dijital bir gösterimle ilişkili olduğu için karmaşık bir görevdir.    Küçük kuruluşlarda, kimlik gerektiren kişilerin dijital gösteriminin tutulması el ile gerçekleştirilen bir işlem olabilir. birisi işe alındığında veya bir yüklenici geldiğinde, BT uzmanı bir dizinde hesap oluşturabilir ve bunlara ihtiyaç duydukları erişimi atayabilir.  Bununla birlikte, orta ölçekli ve büyük kuruluşlarda Otomasyon, kuruluşun daha etkili bir şekilde ölçeklenebilmesini ve kimlikleri doğru tutabilmesini sağlayabilir.

Bir kuruluşta kimlik yaşam döngüsü yönetimi oluşturmak için tipik işlem aşağıdaki adımları izler:

1. Zaten bir kayıt sistemi olup olmadığını belirleme: kuruluşun yetkili olarak davrandığı veri kaynakları.  Örneğin, kuruluşun bir ık sistem iş günü olabilir ve bu sistem, geçerli çalışanların listesini ve çalışanın adı ya da departmanı gibi bazı özelliklerini sağlamak için yetkilidir.  Veya Exchange Online gibi bir e-posta sistemi, bir çalışanın e-posta adresi için yetkili olabilir.

2. Bu kayıt sistemlerini uygulamalar tarafından kullanılan bir veya daha fazla dizin ve veritabanı ile bağlayın ve dizinler ile kayıt sistemleri arasındaki tutarsızlıkları çözün. Örneğin, bir dizin eski bir çalışana ait bir hesap gibi artık gerekmeyen verileri içerebilir. 

3. Kayıt sisteminin yokluğunda yetkili bilgileri sağlamak için hangi işlemlerin kullanılabileceğini belirleme.  Örneğin, dijital kimlikler ancak ziyaretçi varsa, ancak kuruluşun ziyaretçi için bir veritabanı yoksa, ziyaretçi için bir dijital kimliğin artık gerekli olmadığı belirlenmesi için alternatif bir yol bulmanız gerekebilir.

4. Kayıt veya diğer işlemlerin sistemindeki değişikliklerin, güncelleştirme gerektiren dizinlerin veya veritabanlarının her birine çoğaltılacağı şekilde yapılandırın.

## <a name="identity-lifecycle-management-for-representing-employees-and-other-individuals-with-an-organizational-relationship"></a>Çalışanları ve diğer kişileri bir kurumsal ilişki ile temsil eden kimlik yaşam döngüsü yönetimi

Çalışanlar için kimlik yaşam döngüsü yönetimini veya yüklenici ya da öğrenci gibi bir kurumsal ilişkiye sahip diğer kişileri planlarken, birçok kuruluş "JOIN, Move ve Leave" sürecini modelleyebilir.  Bunlar:
    
   - Birleştir-bir kişi erişim gerektiren kapsama geldiğinde, bu uygulamalar için bir kimlik gerekir, bu nedenle henüz bir tane yoksa yeni bir dijital kimliğin oluşturulması gerekebilir
   - Taşı-bir kişi, ek erişim yetkilendirmelerinin dijital kimliğe eklenmesini veya kaldırılmasını gerektiren sınırlar arasında hareket ettirildiğinde
   - Bırak-bir kişi, erişim gerektiren kapsam dışında bırakılırsa, erişimin kaldırılması gerekebilir ve daha sonra kimlik denetimi veya adli amaçları dışındaki uygulamalar için gerekli olmayabilir.

Örneğin, yeni bir çalışan kuruluşunuzda daha önce hiç bağlantılı olmayan, kuruluşunuza katılıyordu. bu çalışan, Azure AD 'de Kullanıcı hesabı olarak temsil edilen yeni bir dijital kimlik gerektirir.  Bu hesabın oluşturulması bir "Bitiştiricisi" işlemine denk gelir. Bu, iş günü gibi yeni çalışan işe başladığında gösterebilecek bir kayıt sistemi varsa otomatikleştirilebilir.  Daha sonra, kuruluşunuzda bir çalışan, Sales 'ten pazarlama 'ya geçtiğinde, bir "taşıyıcısı" işlemine girer.  Bu, artık gerek duyulmayacak olan satış kuruluşunda bulunan erişim haklarının kaldırılmasını ve bunların yeni ihtiyaç duydukları pazarlama kuruluşunda hakları verilmesini gerektirir.

## <a name="identity-lifecycle-management-for-guests"></a>Konuklar için kimlik yaşam döngüsü yönetimi

Ayrıca, konuklar ve diğer kullanıcılar için de benzer süreçler gerekir.  Azure AD Yetkilendirme Yönetimi, kuruluşunuzun dışındaki kişilerle, kuruluşunuzun kaynaklarına erişmesi gereken kişilerle işbirliği yapmak için gereken yaşam döngüsü denetimlerini sağlamak üzere Azure AD işletmeden işletmeye (B2B) kullanır. Azure AD B2B ile dış kullanıcılar kendi giriş dizinlerinde kimlik doğrular, ancak dizininizde bir temsili vardır. Dizininizdeki Gösterim, kullanıcıya kaynaklarınıza erişim atanmasına olanak sağlar.  Yetkilendirme Yönetimi, kuruluşunuzun dışındaki kişilerin, gerektiğinde bunlara yönelik bir dijital kimlik oluşturarak erişim istemesine olanak sağlar. Bu dijital kimlikler Kullanıcı erişimi kaybettiğinde otomatik olarak kaldırılır.  

## <a name="how-does-azure-ad-automate-identity-lifecycle-management"></a>Azure AD kimlik yaşam döngüsü yönetimini nasıl otomatikleştirebilirim?

Azure AD Şu anda şu özellikleri sunmaktadır:

* Çalışanları temsil eden kullanıcılar, Azure AD 'de otomatik olarak oluşturulup güncelleştirilemeyebilir ve [HR odaklı sağlama](what-is-hr-driven-provisioning.md) kullanılarak Active Directory
* Active Directory zaten mevcut olan kullanıcılar, Azure AD 'de [Dizin içi sağlama](what-is-inter-directory-provisioning.md) kullanılarak otomatik olarak oluşturulup korunabilir
* Kullanıcılar, özelliklerine göre otomatik olarak gruplara atanabilir, [dinamik grupları](../external-identities/use-dynamic-groups.md#what-are-dynamic-groups) kullanabilir ve isteğe bağlı olarak gruplara, takımlara, Azure AD rollerine, Azure Kaynak rollerine ve SharePoint Online sitelerine, [yetkilendirme yönetimi](entitlement-management-scenarios.md) ve [Privileged Identity Management](../privileged-identity-management/pim-configure.md) atanabilir
* Kullanıcılar için güncelleştirmeler, [uygulama sağlama](what-is-app-provisioning.md) kullanılarak daha fazla uygulamaya otomatik olarak gönderilebilir

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD yetkilendirme yönetiminde dış kullanıcılara erişimi yönetir](./entitlement-management-external-users.md)
- [HR odaklı sağlama nedir?](what-is-hr-driven-provisioning.md)
- [Uygulama sağlama nedir?](what-is-app-provisioning.md)
- [Dizinler arası sağlama nedir?](what-is-inter-directory-provisioning.md)