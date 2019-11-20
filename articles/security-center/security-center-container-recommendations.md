---
title: Azure Güvenlik Merkezi 'nde kapsayıcı önerileri | Microsoft Docs
description: Bu belgede, kapsayıcılarınızın korunmasına yardımcı olacak Azure Güvenlik Merkezi önerileri açıklanmaktadır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: 96efe5271b91f87e30ba26aabe69a0de76e1a791
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202403"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Azure Güvenlik Merkezi kapsayıcısı önerilerini anlama

Tek bir uygulamalarınızı üretimde iş açısından kritik, Kapsayıcılı bulutta yerel uygulamalar çalıştıracak şekilde geçirdiğinizde, kolay ve hızlı dağıtım ve güncelleştirme dahil olmak üzere kapsayıcıların özelliklerinden yararlanabilirsiniz. Dağıtılan kapsayıcıların sayısı artmaya devam ettiğinden, kapsayıcılarınızın güvenlik durumuna ilişkin görünürlük sağlamak ve tehditlere karşı korumaya yardımcı olmak için güvenlik çözümlerinin yerinde olması gerekir.

Azure Güvenlik Merkezi, kapsayıcılarınızın güvenliğini sağlamanıza yardımcı olmak için aşağıdaki özellikleri sağlar:

- **IaaS Linux makinelerinde barındırılan kapsayıcıların görünürlüğü**<br>Azure Güvenlik Merkezi 'nde kapsayıcılar sekmesi, Docker ile dağıtılan tüm sanal makineleri görüntüler. Güvenlik Merkezi, bir sanal makinede güvenlik sorunlarını araştırırken, Docker sürümü ve konakta çalışan görüntülerin sayısı gibi, makinedeki kapsayıcılarla ilgili ek bilgiler sağlar.

    ![kapsayıcı sekmesi](./media/security-center-container-recommendations/docker-recommendation.png)


- **Docker için CIS kıyaslaması tabanlı güvenlik önerileri**<br>Güvenlik Merkezi, Docker yapılandırmalarınızı tarar ve değerlendirilen başarısız kuralların listesini sunarak hatalı yapılandırmalar konusunda görünürlük sağlar. Güvenlik Merkezi, bu sorunları hızlı bir şekilde çözmenize ve zamandan tasarruf etmenize yardımcı olacak yönergeler sağlar. Güvenlik Merkezi, Docker yapılandırmalarını sürekli değerlendirir ve son durumlar hakkında bilgi verir.

    ![kapsayıcı sekmesi](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Gerçek zamanlı kapsayıcı tehdit algılama**<br> Güvenlik Merkezi, AuditD bileşeniyle Linux makinelerde yer alan kapsayıcılarınız için gerçek zamanlı tehdit algılama sağlar. Uyarılar, konakta ayrıcalıklı bir kapsayıcı oluşturma, bir Docker kapsayıcısı içinde çalışan Secure Shell (SSH) sunucusu veya şifre Miners kullanımı gibi birçok şüpheli Docker etkinliğini belirler. Bu bilgileri kullanarak güvenlik sorunlarını hızlı bir şekilde çözebilir ve kapsayıcılarınızın güvenlik düzeyini artırabilirsiniz.

    ![kapsayıcı sekmesi](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Öneriler
IaaS Linux makinelerinde barındırılan mevcut kapsayıcıları ve Docker yapılandırmalarının güvenlik değerlendirmesini anlamanıza yardımcı olması için aşağıdaki tabloları kullanın.

| Öneri | Açıklama | Düzeltme |
| --- | --- | --- |
|Kapsayıcı güvenlik yapılandırmalarında güvenlik açıklarını düzeltin |En iyi yapılandırma uygulamalarına göre kapsayıcı güvenlik yapılandırmalarında güvenlik açıklarını düzeltin.| Kapsayıcı güvenlik yapılandırmalarında güvenlik açıklarını düzeltmek için:<br>1. Başarısız kuralların listesini gözden geçirin.<br>2. Her kuralı belirtilen yönergelere göre düzeltir.|


## <a name="next-steps"></a>Sonraki adımlar
Diğer Azure kaynak türü için geçerli öneriler hakkında daha fazla bilgi için aşağıdakilere bakın:

* [Kimlik ve erişim Azure Güvenlik Merkezi'nde izleme](security-center-identity-access.md)
* [Azure Güvenlik Merkezi'nde ağınızı koruma](security-center-network-recommendations.md)
* [Azure Güvenlik Merkezi'nde Azure SQL hizmetinizi koruma](security-center-sql-service-recommendations.md)

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi'nde makinelerinizi ve uygulamalarınızı koruma](security-center-virtual-machine-protection.md)
* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md) -- Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.

