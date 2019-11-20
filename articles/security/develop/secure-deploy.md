---
title: Microsoft Azure güvenli uygulamalar dağıtma
description: Bu makalede, Web uygulaması projenizin sürümü ve yanıt aşamaları sırasında göz önünde bulundurmanız gereken en iyi yöntemler açıklanmaktadır.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934881"
---
# <a name="deploy-secure-applications-on-azure"></a>Azure 'da güvenli uygulamalar dağıtma
Bu makalede, bulut için uygulama dağıtırken göz önünde bulundurmanız gereken güvenlik etkinlikleri ve denetimler sunuyoruz. Microsoft [güvenlik geliştirme yaşam döngüsü 'nin (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) yayın ve yanıt aşamaları sırasında göz önünde bulundurmanız gereken güvenlik soruları ve kavramlar ele alınmıştır. Amaç, daha güvenli bir uygulama dağıtmak için kullanabileceğiniz etkinlikleri ve Azure hizmetlerini tanımlamanıza yardımcı olmaktır.

Aşağıdaki SDL aşamaları bu makalede ele alınmıştır:

- Yayınla
- Yanıt

## <a name="release"></a>Yayınla
Yayın aşamasının odağı, bir projeyi ortak yayın için Read.
Bu, daha sonra gerçekleşebilecek yayın sonrası bakım görevlerini ve adres güvenliği güvenlik açıklarını etkili bir şekilde gerçekleştirmenin planlama yollarını içerir.

### <a name="check-your-applications-performance-before-you-launch"></a>Başlamadan önce uygulamanızın performansını denetleyin

Uygulamayı başlatıp üretim için güncelleştirmeleri dağıtmadan önce uygulamanızın performansını denetleyin. Uygulamanızdaki performans sorunlarını bulmak için Visual Studio kullanarak bulut tabanlı [yük testlerini](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) çalıştırın, dağıtım kalitesini geliştirebilirsiniz, uygulamanızın her zaman açık veya kullanılabilir olduğundan ve uygulamanızın başlatma için trafiği işleyebileceği şekilde emin olun.

### <a name="install-a-web-application-firewall"></a>Web uygulaması güvenlik duvarını yükler

Web uygulamaları, bilinen yaygın güvenlik açıklarından yararlanan kötü amaçlı saldırıların giderek daha fazla hedefi olmaktadır. Bu güvenlik açıkları arasında genel olarak SQL ekleme saldırıları ve siteler arası komut dosyası saldırıları bulunur. Uygulama kodundaki bu saldırıların önlenmesi zor olabilir. Uygulama topolojisinin birçok katmanında kapsamlı bakım, düzeltme eki uygulama ve izleme gerektirebilir. Merkezi bir WAF, güvenlik yönetiminin daha basit olmasına yardımcı olur. Bir WAF çözümü aynı zamanda, her bir Web uygulamasının güvenliğini sağlamak üzere merkezi bir konumdaki bilinen bir güvenlik açığına yama yaparak güvenlik tehditlerine da yanıt verebilir.

[Azure Application Gateway WAF](../../application-gateway/waf-overview.md) , Web uygulamalarınızın yaygın güvenlik açıklarından ve güvenlik açıklarından merkezi bir şekilde korunmasını sağlar. WAF, [OWASP çekirdek kural kümelerinden](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 veya 2.2.9 göre kuralları temel alır.

### <a name="create-an-incident-response-plan"></a>Olay yanıt planı oluşturma

Bir olay yanıt planının hazırlanması, zaman içinde ortaya çıkabilir yeni tehditleri ele almanıza yardımcı olmak için önemlidir. Bir olay yanıt planının hazırlanması, uygun güvenlik acil durum kişilerini tanımlamayı ve kuruluştaki diğer gruplardan devralınan ve lisanslı üçüncü taraf kodu için güvenlik bakım planlarını oluşturmayı içerir.

### <a name="conduct-a-final-security-review"></a>Son güvenlik incelemesi gerçekleştirin

Gerçekleştirilen tüm güvenlik etkinliklerini kasıtlı olarak gözden geçirmek, yazılım sürümü veya uygulamanız için hazırlık sağlanmasına yardımcı olur. Son güvenlik incelemesi (FSR) genellikle gereksinimler aşamasında tanımlanan kalite kapıları ve hata çubuklarına karşı tehdit modellerini, araç çıktılarını ve performansı incelemeyi içerir.

### <a name="certify-release-and-archive"></a>Yayın ve arşivi Onayla

Bir sürümden önce yazılım, güvenlik ve gizlilik gereksinimlerinin karşılanmasını sağlamaya yardımcı olur. Tüm ilgili veriler arşivlenmek, yayın sonrası bakım görevlerini gerçekleştirmek için gereklidir. Arşivleme, sürekli yazılım mühendisliğinde ilişkili uzun süreli maliyetlerin düşürülmesine de yardımcı olur.

## <a name="response"></a>Yanıt
Geliştirme ekibinin yanıt sonrası, sunulan yazılım tehditleri ve güvenlik açıklarına yönelik tüm raporlara uygun şekilde yanıt verebilmesini sağlar.

### <a name="execute-the-incident-response-plan"></a>Olay yanıtı planını yürütme

Yayın aşamasında kurumsal olarak bulunan olay yanıtı planını uygulayabilmeniz, müşterilerin ortaya çıkmakta olan yazılım güvenliği veya gizlilik açıklarına karşı korunmasına yardımcı olmak için gereklidir.

### <a name="monitor-application-performance"></a>Uygulama performansını izleme

Uygulamanızı dağıtıldıktan sonra devam eden izleme, performans sorunlarını ve güvenlik açıklarını da algılamanıza yardımcı olur.
Uygulama izlemeye yardımcı olan Azure hizmetleri şunlardır:

  - Azure Application Insights
  - Azure Güvenlik Merkezi

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) , birden çok platformda Web geliştiricileri için genişletilebilir bir uygulama performans YÖNETIMI (APM) hizmetidir. Canlı web uygulamanızı izlemek için kullanabilirsiniz. Application Insights, performans bozuklularını otomatik olarak algılar. Sorunları tanılamanıza ve hangi kullanıcıların uygulamanızla gerçekten ne yaptığını anlamanıza yardımcı olacak güçlü analiz araçları içerir. Performansı ve kullanılabilirliği sürekli geliştirmenize yardımcı olmak amacıyla tasarlanmıştır.

#### <a name="azure-security-center"></a>Azure Güvenlik Merkezi

[Azure Güvenlik Merkezi](../../security-center/security-center-intro.md) , Web uygulamaları dahil olmak üzere Azure kaynaklarınızın güvenliğine yönelik artırılmış görünürlük (ve üzerinde denetim) ile tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Azure Güvenlik Merkezi, aksi takdirde açıklanabilecek tehditleri algılamaya yardımcı olur. Çeşitli güvenlik çözümleriyle birlikte çalışmaktadır.

Güvenlik Merkezi 'nin ücretsiz katmanı yalnızca Azure kaynaklarınız için sınırlı güvenlik sunar. [Güvenlik Merkezi Standart katmanı](../../security-center/security-center-onboarding.md) , bu özellikleri şirket içi kaynaklara ve diğer bulutlara genişletir.
Güvenlik Merkezi Standart şunları yapmanıza yardımcı olur:

  - Güvenlik açıklarını bulun ve onarın.
  - Kötü amaçlı etkinlikleri engellemek için erişim ve uygulama denetimleri uygulayın.
  - Analiz ve zekası kullanarak tehditleri algılayın.
  - Saldırı altında hızlı bir şekilde yanıt verin.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelerde, güvenli uygulamalar tasarlamanıza ve geliştirmenize yardımcı olabilecek güvenlik denetimleri ve etkinlikleri önerilir.

- [Güvenli uygulamalar tasarlama](secure-design.md)
- [Güvenli uygulamalar geliştirme](secure-develop.md)
