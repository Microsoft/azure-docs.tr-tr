---
title: Microsoft Cloud App Security ile uygulama görünürlüğü ve denetimi
description: Uygulama risk düzeylerini belirlemeyi, ihlal ve sızıntıları gerçek zamanlı olarak durdurmayı ve uygulama bağlayıcılarını kullanarak görünürlük ve idare için sağlayıcı API 'Lerinden yararlanmanızı sağlayan yollar edinin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9a9f56d70e049200cee0c3655a9998feccfa301
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377845"
---
# <a name="cloud-app-visibility-and-control"></a>Bulut uygulama görünürlüğü ve denetimi

Bulut uygulamalarının ve hizmetlerinin tam avantajlarından yararlanmak için, bir BT ekibinin, kritik verileri korumak için denetimi korurken doğru destekleyici erişim dengesini bulması gerekir. Microsoft Cloud App Security, tüm Microsoft ve üçüncü taraf bulut hizmetlerinize ilişkin zengin görünürlük, veri yolculuğu üzerinde denetim ve gelişmiş analizler sağlar.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Ağınızda gölge BT’yi keşfetme ve yönetme

BT yöneticileri, çalışanlarının ne kadar çok sayıda bulut uygulaması kullandığını sorduğu zaman, gerçekte 30 veya 40 olduğunu düşünürken, ortalama, kuruluşunuzda çalışanlar tarafından kullanılan 1.000 ayrı farklı uygulama. Gölge BT, hangi uygulamaların kullanıldığını ve risk düzeyinin ne olduğunu bilmenize ve belirlemenize yardımcı olur. Çalışanların yüzde 80 ' i gözden geçirilmemiş ve güvenlik ve uyumluluk ilkelerinizle uyumlu olmayan, tasdiksiz uygulamalar kullanıyor. Ve çalışanlarınız, kaynak ve uygulamalarınıza şirket ağınızın dışından erişebildiğinden, artık güvenlik duvarlarınızda kuralların ve ilkelerin olması yeterli değildir.

Hangi uygulamaların kullanıldığını bulmak, bu uygulamaların riskini araştırmak, yeni riskli uygulamaları belirlemek için ilkeleri yapılandırmak ve proxy veya güvenlik duvarı gerecinizi yerel olarak engellemek için bu uygulamaların tasdikini kaldırmak için Microsoft Bulut uygulama bulma (Azure Active Directory Premium P1 özelliği) kullanın.

- Gölge BT’yi keşfetme ve tanımlama
- Değerlendirme ve analiz etme
- Uygulamalarınızı yönetme
- Gelişmiş gölge BT bulma raporlaması
- Tasdikli uygulamaları denetleme
 
### <a name="learn-more"></a>Daha fazla bilgi edinin

- [Ağınızda gölge BT 'yi bulma ve yönetme ](/cloud-app-security/tutorial-shadow-it)
- [Cloud App Security ile bulunan uygulamalar ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Kullanıcı oturumu görünürlüğü ve denetimi 

Bugünün çalışma alanında, aslında bulut ortamınızda ne olduğunu bilmekte olan bu durum genellikle yeterli değildir. Çalışanlar kasıtlı olarak veya farkında olmadan verilerinizi ve kuruluşunuzu riske almadan önce gerçek zamanlı olarak ihlal etmek ve sızıntıları durdurmak istiyorsunuz. Azure Active Directory (Azure AD) ile birlikte Microsoft Cloud App Security, bu özellikleri Koşullu Erişim Uygulama Denetimi bütünsel ve tümleşik bir deneyime sunar. 

Oturum denetimi ters bir ara sunucu mimarisi kullanır ve Azure AD koşullu erişimiyle benzersiz bir şekilde tümleşiktir. Azure AD koşullu erişimi, kuruluşunuzun uygulamalarında belirli koşullara göre erişim denetimleri zorlamanıza olanak sağlar. Koşullar, kimlerin (Kullanıcı veya Kullanıcı grubu) ve ne (bulut uygulamaları) ve koşullu erişim ilkesinin hangi konuma uygulanacağını tanımlar. Koşulları belirledikten sonra, verileri gerçek zamanlı olarak koruyabileceğiniz Cloud App Security kullanıcılara yönlendirebilirsiniz.  

Bu denetimle şunları yapabilirsiniz:  
- Dosya indirmelerini denetle
- B2B senaryolarını izleme  
- Dosyalara erişimi denetleme  
- İndirme sırasında belgeleri koruma  
 
### <a name="learn-more"></a>Daha fazla bilgi edinin

- [Cloud App Security oturum denetimi ile uygulamaları koruma ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Gelişmiş uygulama görünürlüğü ve denetimleri 

Uygulama bağlayıcıları, bağlandığınız uygulamalar üzerinde Microsoft Cloud App Security daha fazla görünürlük ve denetim sağlamak için uygulama sağlayıcılarının API 'Lerini kullanır. Cloud App Security, bulut sağlayıcısı tarafından sunulan API 'lerden yararlanır. Her hizmetin kendi çerçevesi ve daraltma, API sınırları, dinamik süre değiştirme API pencereleri ve diğerleri gibi API sınırlamaları vardır. Cloud App Security ürün ekibi, API 'lerin kullanımını iyileştirmek ve en iyi performansı sağlamak için bu hizmetlerle birlikte çalışmıştır. Farklı sınırlamalar Hizmetleri API 'lerine göre hesaba katılarak, Cloud App Security motorları izin verilen en büyük kapasiteyi kullanır. Kiracıdaki tüm dosyaları tarama gibi bazı işlemler, daha uzun bir süre içinde yayılmaları için çok sayıda API çağrısı gerektirir. Bazı ilkelerin birkaç saat veya gün boyunca çalışmasını bekliyor. 
 
### <a name="learn-more"></a>Daha fazla bilgi edinin  

- [Cloud App Security uygulamaları bağlama ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Sonraki adımlar

- [Ağınızda gölge BT 'yi bulma ve yönetme ](/cloud-app-security/tutorial-shadow-it)
- [Cloud App Security ile bulunan uygulamalar ](/cloud-app-security/discovered-apps)
- [Cloud App Security oturum denetimi ile uygulamaları koruma ](/cloud-app-security/proxy-intro-aad)
- [Cloud App Security uygulamaları bağlama ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)