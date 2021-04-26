---
title: Azure AD Connect bulut eşitleme ayrıntılı bakış-nasıl çalıştığı
description: Bu konu, bulut eşitlemenin nasıl çalıştığı hakkında ayrıntılı bilgi sağlar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614320"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Bulut eşitleme ayrıntılı bakış-nasıl çalıştığı

## <a name="overview-of-components"></a>Bileşenlere genel bakış

![Nasıl çalışır?](media/concept-how-it-works/how-1.png)

Bulut eşitleme, Azure AD hizmetlerinin üzerine kurulmuştur ve 2 temel bileşene sahiptir:

- **Sağlama Aracısı**: Azure AD Connect bulut sağlama Aracısı, Workday ile aynı aracıdır ve uygulama proxy 'si ile aynı sunucu tarafı teknolojisinden oluşturulmuştur ve kimlik doğrulamasından geçer. Yalnızca giden bağlantı gerektirir ve aracılar otomatik olarak güncelleştirilir. 
- **Sağlama hizmeti**: Zamanlayıcı tabanlı model kullanan, çıkış sağlama ve iş günü gelen sağlama olarak aynı sağlama hizmeti. Bulut eşitlemesi söz konusu olduğunda, değişiklikler her 2 dakikada bir sağlanır.


## <a name="initial-setup"></a>İlk kurulum
İlk kurulum sırasında, bulut eşitlemesini meydana getiren birkaç şey yapılır.  Bunlar: 

- **Aracı yüklemesi sırasında**: sağlamak istediğiniz ad etki alanları için aracıyı yapılandırırsınız.  Bu yapılandırma etki alanlarını karma kimlik hizmetine kaydeder ve istekleri dinleyen Service Bus 'a giden bir bağlantı kurar.
- **Sağlamayı etkinleştirdiğinizde**: ad etki alanını seçin ve sağlama işlemini her 2 dakikada bir çalışacak şekilde etkinleştirebilirsiniz. İsteğe bağlı olarak parola karması eşitleme ve bildirim e-postasını tanımlama ' i seçebilirsiniz. Ayrıca, Microsoft Graph API 'Leri kullanarak öznitelik dönüştürmeyi yönetebilirsiniz.


## <a name="agent-installation"></a>Aracı yüklemesi
Aşağıdakiler, bulut sağlama Aracısı yüklendiğinde ne olduğunu gösterir.

- İlk olarak, yükleyici, aracı ikili dosyalarını ve sanal hizmet hesabı (ağ hizmeti \ aadprovisioningagent) altında çalışan aracı hizmetini yükleyecek.  Bir sanal hizmet hesabı, parolası olmayan ve Windows tarafından yönetilen özel bir hesap türüdür.
- Yükleyici daha sonra Sihirbazı başlatır.
- Sihirbaz, Azure AD kimlik bilgilerini isteyecek, daha sonra kimlik doğrulamasından ve bir belirteç almaya sunulacaktır.
- Sihirbaz daha sonra geçerli makine etki alanı yöneticileri kimlik bilgilerini ister.
- Bu kimlik bilgilerini kullanarak, bu etki alanı için aracı genel yönetilen hizmet hesabı (GMSA) oluşturulur veya bulunur ve zaten varsa yeniden kullanılır.
- Aracı hizmeti şimdi GMSA altında çalışacak şekilde yeniden yapılandırıldı.
- Sihirbaz artık, aracının hizmet istediğini her etki alanı için Kuruluş Yöneticisi (EA)/Domain yönetici (DA) hesabıyla birlikte etki alanı yapılandırmasını ister.
- GMSA hesabı daha sonra, Yukarıda girilen her etki alanına erişimi etkinleştiren izinlerle güncellenir.
- Sonra, sihirbaz aracı kaydını tetikler
- Aracı bir sertifika oluşturur ve Azure AD belirtecini kullanarak kendisini ve sertifikayı karma kimlik hizmeti (kendı) kayıt hizmeti ile kaydeder
- Sihirbaz bir AgentResourceGrouping çağrısını tetikler. Yönetici hizmetine yapılan bu çağrı, aracının yapılandırmasındaki bir veya daha fazla AD etki alanına aracıyı atamasıdır.
- Sihirbaz artık aracı hizmetini yeniden başlatır.
- Aracı, yapılandırma güncelleştirmelerini denetlemek için yeniden başlatma sırasında (ve her 10 dakikada bir) önyükleme hizmetini çağırır.  Önyükleme hizmeti, aracı kimliğini doğrular.  Ayrıca son önyükleme zamanını da güncelleştirir.  Bu durum, aracıların önbaşlatılmaması, güncel Service Bus uç noktaları almamaları ve istekleri alamamaları nedeniyle önemlidir. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Etki alanları arası kimlik yönetimi (SCıM) sistemi nedir?

[SCIM belirtimi](https://tools.ietf.org/html/draft-scim-core-schema-01) , Azure AD gibi kimlik alanları arasında Kullanıcı veya grup kimlik bilgilerinin değişimini otomatik hale getirmek için kullanılan bir standarttır. SCıM, sağlama için standart bir standart haline geliyor ve SAML veya OpenID Connect gibi Federasyon standartlarıyla birlikte kullanıldığında, yöneticilere erişim yönetimi için uçtan uca standartlara dayalı bir çözüm sunar.

Azure AD Connect bulut sağlama Aracısı, kullanıcıları ve grupları sağlamak ve sağlamak için Azure AD ile SCıM 'i kullanır.

## <a name="synchronization-flow"></a>Eşitleme akışı
![sağlama ](media/concept-how-it-works/provisioning-4.png) Aracı 'nı yükledikten ve sağlamayı etkinleştirmişse, aşağıdaki akış gerçekleşir.

1.  Bir kez yapılandırıldıktan sonra Azure AD sağlama hizmeti, Service Bus 'a bir istek eklemek için Azure AD karma hizmetini çağırır. Aracı sürekli olarak istekleri dinleyen Service Bus giden bir bağlantı sağlar ve sistemi etki alanları arası kimlik yönetimi (SCıM) isteği için hemen seçer. 
2.  Aracı, isteği nesne türüne göre ayrı sorgulara ayırır. 
3.  AD, aracıya sonucu döndürür ve aracı bu verileri Azure AD 'ye göndermeden önce filtreler.  
4.  Aracı, Azure AD 'ye yönelik SCıM yanıtını döndürür.  Bu yanıtlar, aracıda gerçekleşen filtrelemeye bağlıdır.  Aracı, sonuçları filtrelemek için kapsam kullanır. 
5.  Sağlama Hizmeti, değişiklikleri Azure AD 'ye yazar.
6. Bu, tam eşitlemenin aksine bir Delta Eşitlemi olursa tanımlama bilgisi/filigran kullanılır. Yeni sorgular, bu tanımlama bilgisinden/filigranlarındaki değişiklikleri alır.

## <a name="supported-scenarios"></a>Desteklenen senaryolar:
Bulut eşitlemesi için aşağıdaki senaryolar desteklenir.


- **Yeni bir ormana sahip mevcut karma müşteri**: Azure AD Connect eşitleme, birincil ormanlar için kullanılır. Bulut eşitleme, bir AD ormanından sağlama için kullanılır (bağlantısı kesik). Daha fazla bilgi için [buradaki](tutorial-existing-forest.md)öğreticiye bakın.

 ![Mevcut karma](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Yeni karma müşteri**: Azure AD Connect eşitleme kullanılmıyor. Bulut eşitleme, bir AD ormanından sağlama için kullanılır.  Daha fazla bilgi için [buradaki](tutorial-single-forest.md)öğreticiye bakın.
 
 ![Yeni müşteriler](media/tutorial-single-forest/diagram-2.png)

- **Mevcut karma müşteri**: Azure AD Connect eşitleme, birincil ormanlar için kullanılır. Bulut eşitleme, birincil ormanlarda bulunan küçük bir kullanıcı kümesi için şu [şekildedir](tutorial-existing-forest.md).

 ![Mevcut pilot](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Daha fazla bilgi için bkz. [desteklenen topolojiler](plan-cloud-sync-topologies.md).



## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)
