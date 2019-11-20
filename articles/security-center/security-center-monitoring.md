---
title: Azure Güvenlik Merkezi ile güvenlik duruşunuzu güçlendirin | Microsoft Docs
description: Bu makale, Azure Güvenlik Merkezi 'nde kaynaklarınızı izleyerek güvenlik duruşunuzu güçlendirmenize yardımcı olur.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 57d47712fe20fac0bf370a9a100a07a37d7aa753
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996673"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Azure Güvenlik Merkezi ile güvenlik duruşunuzu güçlendirin
Bu makale, güvenlik duruşunuzu güçlendirmenize yardımcı olur. Azure Güvenlik Merkezi 'ndeki izleme yeteneklerini kullanarak kaynak güvenliği mümkün olduğunca sıkı olduğundan ve ilkelerle uyumluluğu izlediğinizden emin olun.

## <a name="how-do-you-strengthen-your-security-posture"></a>Güvenlik duruşunuzu nasıl güçlendirin?
Genellikle izleme olarak izlemeyi düşündük ve duruma yanıt verebilmesi için bir olayın gerçekleşmesini bekliyor. Güvenlik duruşunuzu güçleşerek, kurumsal standartları veya en iyi yöntemleri karşılamayan sistemleri belirlemek için kaynaklarınızı denetleyen öngörülü bir stratejiye sahip olunmaktadır.

Bir aboneliğin kaynakları için [güvenlik ilkelerini](tutorial-security-policy.md) etkinleştirdikten sonra, güvenlik merkezi olası güvenlik açıklarını belirlemek için kaynaklarınızın güvenliğini analiz eder. Ağ yapılandırmanızla ilgili bilgiler anında kullanılabilir. Aracının yüklü olduğu VM 'lerin ve bilgisayarların sayısına bağlı olarak, güvenlik güncelleştirmesi durumu ve işletim sistemi yapılandırması gibi VM 'Ler ve bilgisayar yapılandırması hakkında bilgi toplamak bir saat veya daha fazla zaman alabilir. Sorunların tam listesini ve ağınızı bir bütün olarak görmek ve **öneriler** kutucuğunda riski düzeltmek için kullanabileceğiniz yolları görüntüleyebilirsiniz.

Kaynaklarınızın güvenlik durumunu ve kaynak türü başına tüm sorunları görüntüleyebilirsiniz:

- Bilgisayar kaynaklarınızın ve uygulamalarınızın durumunu izlemek ve güvenliğini iyileştirmeye yönelik öneriler almak için bkz. [Azure Güvenlik Merkezi 'nde makinelerinizi ve uygulamalarınızı koruma](security-center-virtual-machine-protection.md)
- Sanal makineler, ağ güvenlik grupları ve uç noktaları gibi ağ kaynaklarınızı izlemek ve güvenliğini iyileştirmeye yönelik öneriler almak için bkz. daha fazla bilgi için [Azure Güvenlik Merkezi 'nde ağınızı koruma](security-center-network-recommendations.md) . 
- Verilerinizi ve depolama kaynaklarınızı SQL Server 'lar ve depolama hesapları gibi izlemek ve bunların güvenliğini iyileştirmeye yönelik öneriler almak için bkz. daha fazla bilgi için bkz. Azure [Güvenlik Merkezi 'Nde Azure SQL hizmetini ve verilerini koruma](security-center-sql-service-recommendations.md) . 
- MFA ve hesap izinleri dahil olmak üzere kimliğinizi ve erişim kaynaklarını izlemek ve güvenliğini iyileştirmeye yönelik öneriler almak için bkz. daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](security-center-identity-access.md) . 
- Kaynaklarınıza tam zamanında erişimi izlemek için bkz. daha fazla bilgi için [tam zamanında sanal makine erişimini yönetme](security-center-just-in-time.md) . 


Önerilerin nasıl uygulanacağı hakkında daha fazla bilgi için [Azure Güvenlik Merkezi 'nde güvenlik önerilerini uygulama](security-center-recommendations.md)makalesini okuyun.



![Kaynak güvenlik durumu kutucuğu](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Ayrıca bkz.
Bu makalede, Azure Güvenlik Merkezi 'nde izleme yeteneklerini kullanmayı öğrendiniz. Azure Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdakilere bakın:

* [Azure Güvenlik Merkezi 'nde güvenlik Ilkelerini ayarlama](tutorial-security-policy.md): Azure Güvenlik Merkezi 'nde güvenlik ayarlarını yapılandırmayı öğrenin.
* [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md): güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile iş ortağı çözümlerini izleme](security-center-partner-solutions.md): iş ortağı çözümlerinizin sistem durumunu izlemeyi öğrenin.
* [Azure Güvenlik MERKEZI SSS](security-center-faq.md): hizmeti kullanma hakkında sık sorulan soruları bulun.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/): Azure güvenliği ve uyumluluğu ile ilgili blog gönderilerini bulun.
