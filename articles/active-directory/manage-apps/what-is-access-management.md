---
title: Azure AD kullanarak uygulamalara erişimi yönetme | Microsoft Docs
description: Azure Active Directory kuruluşların her kullanıcının erişimi olan uygulamaları nasıl belirtmesini sağladığını açıklar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99c7947b6469f64f2ea05b2290305710db1ee796
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477103"
---
# <a name="managing-access-to-apps"></a>Uygulamalara erişimi yönetme
Devam eden erişim yönetimi, kullanım değerlendirmesi ve raporlama, bir uygulama kuruluşunuzun kimlik sistemiyle tümleştirildiğinde bir zorluk yapmaya devam eder. Çoğu durumda BT yöneticileri veya yardım masası, uygulamalarınıza erişimi yönetirken devam eden bir etkin rol almak zorunda olur. Bazen, atama genel veya bir BT ekibi tarafından gerçekleştirilir. Genellikle, atama kararı iş kararı Oluşturucu için temsilci olarak atanmak üzere tasarlanmıştır ve atamayı yapmadan önce onayını gerekli hale getirir.  Diğer kuruluşlar, rol tabanlı Access Control (RBAC) veya öznitelik tabanlı Access Control (ATZM) gibi mevcut bir otomatik kimlik ve erişim yönetimi sistemiyle tümleştirmeyi yatırım yapar. Tümleştirme ve kural geliştirmenin her ikisi de özel ve pahalı olma eğilimindedir. Yönetim yaklaşımına göre izleme veya raporlama kendi ayrı, maliyetli ve karmaşık yatırımdır.

## <a name="how-does-azure-active-directory-help"></a>Nasıl yardım Azure Active Directory?
 Azure AD, yapılandırılan uygulamalar için kapsamlı erişim yönetimini desteklediğinden, kuruluşların otomatik, öznitelik tabanlı atama (ATZYA da RBAC senaryolarından), temsilciyle ve dahil olmak üzere doğru erişim ilkelerini kolayca elde etmelerini sağlar Yönetici Yönetimi. Azure AD ile kolayca karmaşık ilkeler elde edebilir, tek bir uygulama için birden çok yönetim modeli birleştirerek aynı hedef kitlelere sahip uygulamalarda yönetim kurallarını da yeniden kullanabilirsiniz.

* [Yeni veya mevcut uygulamalar ekleme](add-gallery-app.md)

  Azure AD 'nin uygulama ataması iki birincil atama moduna odaklanır:

* **Bireysel atama** Dizin genel Yöneticisi izinlerine sahip bir BT Yöneticisi, bireysel kullanıcı hesapları seçebilir ve uygulamaya erişim izni verebilir.
* **Grup tabanlı atama (yalnızca ücretli Azure AD)** Dizin genel Yöneticisi izinlerine sahip bir BT Yöneticisi, uygulamaya bir grup atayabilir. Belirli kullanıcıların erişimi, uygulamaya erişmeye çalıştıkları sırada grubun üyesi olup olmadıkları belirlenir. Diğer bir deyişle, yönetici etkin bir şekilde "atanmış grubun herhangi bir üyesinin uygulamaya erişimi vardır" belirten bir atama kuralı oluşturabilir. Yöneticiler, bu atama seçeneğini kullanarak, [öznitelik tabanlı dinamik gruplar](../fundamentals/active-directory-groups-create-azure-portal.md), dış sistem grupları (örneğin, şirket içi Active Directory veya Workday) dahil olmak üzere Azure AD Grup Yönetimi seçeneklerinden yararlanabilir veya Yönetici tarafından yönetilen veya self servis tarafından yönetilen gruplar. Tek bir grup birden çok uygulamaya kolayca atanabilir, böylece atama benzeşimine sahip uygulamaların atama kurallarını paylaştığından emin olabilir ve bu da genel yönetim karmaşıklığını azaltır. İç içe geçmiş grup üyelikleri Şu anda uygulamalara grup tabanlı atama için desteklenmediğini unutmayın.

Yöneticiler, bu iki atama modunu kullanarak, istenen herhangi bir atama yönetimi yaklaşımını elde edebilir.

Azure AD ile kullanım ve atama raporlaması, yöneticilerin atama durumu, atama hataları ve hatta kullanımı kolayca rapor vermesine olanak tanıyan tam olarak tümleşiktir.

## <a name="complex-application-assignment-with-azure-ad"></a>Azure AD ile karmaşık uygulama ataması
Salesforce gibi bir uygulamayı düşünün. Birçok kuruluşta Salesforce öncelikle pazarlama ve satış ekipleri tarafından kullanılır. Genellikle, pazarlama ekibinin üyelerinin Salesforce erişimine son derece ayrıcalıklı erişimi vardır, ancak satış ekibinin üyeleri sınırlı erişime sahip olur. Birçok durumda, büyük bir bilgi çalışanı popülasyonu uygulamaya erişimi kısıtlamıştır. Bu kuralların özel durumları önemli bir şekilde karmaşıklaştırır. Genellikle pazarlama veya satış liderlik ekiplerinin, Kullanıcı erişimi vermek veya rollerini bu genel kurallardan bağımsız olarak değiştirmesi için Prerogative.

Azure AD ile, Salesforce gibi uygulamalar çoklu oturum açma (SSO) ve otomatik sağlama için önceden yapılandırılmış olabilir. Uygulama yapılandırıldıktan sonra, yönetici uygun grupları oluşturup atamak için tek seferlik eylemi gerçekleştirebilir. Bu örnekte, bir yönetici aşağıdaki atamaları yürütebilir:

* [Dinamik Gruplar](../fundamentals/active-directory-groups-create-azure-portal.md) , departman veya rol gibi öznitelikleri kullanarak pazarlama ve satış ekiplerinin tüm üyelerini otomatik olarak temsil edecek şekilde tanımlanabilir:
  
  * Pazarlama gruplarının tüm üyeleri Salesforce 'ta "pazarlama" rolüne atanır
  * Satış ekibi gruplarının tüm üyeleri Salesforce 'ta "Sales" rolüne atanır. Daha fazla bir iyileştirme, farklı Salesforce rollerine atanan bölgesel satış ekiplerini temsil eden birden çok grup kullanabilir.
* Özel durum mekanizmasını etkinleştirmek için, her rol için bir self servis grubu oluşturulabilir. Örneğin, "Salesforce Marketing Exception" grubu bir self servis grubu olarak oluşturulabilir. Grup Salesforce pazarlama rolüne atanabilir ve pazarlama liderliği ekibi sahip olabilir. Pazarlama liderliği ekibinin üyeleri, kullanıcı ekleyebilir veya kaldırabilir, bir JOIN ilkesi ayarlayabilir veya hatta bireysel kullanıcıların katılması isteklerini onaylayabilir veya reddedebilir. Bu mekanizma, sahipler veya Üyeler için uzman eğitim gerektirmeyen bir bilgi çalışanı uygun deneyimi aracılığıyla desteklenir.

Bu durumda, atanan tüm kullanıcılar otomatik olarak Salesforce olarak sağlanır, çünkü farklı gruplara eklendikçe rol atamaları Salesforce 'ta güncelleştirilir. Kullanıcılar, Microsoft uygulama erişim paneli, Office Web istemcileri ve hatta kurumsal Salesforce oturum açma sayfasına giderek Salesforce 'ı bulabilir ve bunlara erişebilir. Yöneticiler, Azure AD raporlama kullanarak kullanımı ve atama durumunu kolayca görüntüleyebilir.

Yöneticiler, belirli roller için erişim ilkeleri ayarlamak üzere [Azure AD koşullu erişimi](../active-directory-conditional-access-azure-portal.md) kullanabilir. Bu ilkeler, şirket ortamının dışında erişime izin verilip verilmeyeceğini ve hatta çok faktörlü kimlik doğrulaması veya cihaz gereksinimlerinin çeşitli durumlarda erişim elde etmelerini içerebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Koşullu erişimle uygulamaları koruma](../active-directory-conditional-access-azure-portal.md)
* [Self Servis Grup Yönetimi/SSAA](../users-groups-roles/groups-self-service-management.md)
