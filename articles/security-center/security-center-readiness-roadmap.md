---
title: Azure Güvenlik Merkezi Hazırlığı Yol Haritası | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi'nde kullanılacak bir hazırlık yol haritası sağlar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: ea029e62ac1273d9796e52edc3f7a46868964d2a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520749"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure Güvenlik Merkezi Hazırlığı Yol Haritası
Bu belge, Azure Güvenlik Merkezi ile çalışmaya başlamanıza yardımcı olacak bir hazırlık yol haritası sağlar.

## <a name="understanding-security-center"></a>Güvenlik Merkezi’ni anlama
Azure Güvenlik Merkezi, Azure’da, şirket içinde ve diğer bulutlarda çalışan iş yükleri için birleşik güvenlik yönetimi ve gelişmiş tehdit koruması sağlar. 

Güvenlik Merkezi ile çalışmaya başlamak için aşağıdaki kaynakları kullanın.

Makaleler
* [Azure Güvenlik Merkezi'ne Giriş](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Azure Güvenlik Merkezi hızlı başlangıç kılavuzu](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Videolar
* [Hızlı Tanıtım Videosu](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Güvenlik Merkezi Önleme, Algılama ve Yanıt Özelliklerine Genel Bakış](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planlama ve işlemler
Güvenlik Merkezi'nin tüm avantajlarından yararlanabilmek için kurumunuzdaki farklı kişilerin veya ekiplerin güvenli çalışma, izleme, yönetim ve olay yanıtı gereksinimlerini karşılamak amacıyla hizmeti nasıl kullandığının anlaşılması oldukça önemlidir.

Planlama ve çalışma işlemleri sırasında size yardımcı olması için aşağıdaki kaynakları kullanın.


Makale
* [Azure Güvenlik Merkezi planlama ve işlemler kılavuzu](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

Video
* [Güvenlik Merkezi ile karma bulut iş yükü koruması](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965)

### <a name="onboarding-computers-to-security-center"></a>Güvenlik Merkezi’ne bilgisayar ekleme
Güvenlik Merkezi, Güvenlik Merkezi Standart sürümü için etkinleştirilmemiş herhangi bir Azure aboneliğini veya çalışma alanını otomatik olarak algılar. Buna, Güvenlik Merkezi Ücretsiz sürümünü kullanan Azure abonelikleri ve etkin bir Güvenlik çözümü olmayan çalışma alanları dahildir.

Ekleme işlemleri sırasında size yardımcı olması için aşağıdaki kaynakları kullanın.

Makale
* [Gelişmiş güvenlik için Azure Güvenlik Merkezi Standart sürümüne ekleme](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Video
* [Azure Güvenlik Merkezi Karma - Genel Bakış](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Güvenlik Merkezi'ni kullanarak güvenlik sorunlarını azaltma
Güvenlik Merkezi, gerçek tehditleri algılamak ve hatalı pozitif sonuçları azaltmak için Azure kaynaklarınızdan, ağınızdan ve güvenlik duvarı ve uç nokta koruma çözümleri gibi bağlı iş ortağı çözümlerinden günlük verilerini otomatik olarak toplar, çözümler ve tümleştirir.

Güvenlik uyarılarını yönetmenize ve kaynaklarınızı korumanıza yardımcı olması için aşağıdaki kaynakları kullanın.

Makaleler    
* [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Azure Güvenlik Merkezi'nde makinelerinizi ve uygulamalarınızı koruma](security-center-virtual-machine-protection.md)
* [Azure Güvenlik Merkezi'nde ağınızı koruma](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Azure Güvenlik Merkezi'nde Azure SQL hizmetini ve verilerini koruma](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Video   
* [Güvenlik Merkezi'ni Kullanarak Güvenlik Sorunlarını Azaltma](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Olay yanıtlama için Güvenlik Merkezi
Maliyetini ve zararını azaltmak için bir saldırı gerçekleşmeden önce olay yanıtı planınızın olması önemlidir. Bir olay yanıtının farklı aşamalarında Azure Güvenlik Merkezi’ni kullanabilirsiniz.

Güvenlik Merkezi’nin olay yanıtlama işleminize nasıl dahil edilebileceğini anlamak için aşağıdaki kaynakları kullanın.

Videolar  
* [Olay Yanıtlamada Azure Güvenlik Merkezi](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Yeni nesil güvenlik işlemi ve araştırma ile tehditlere hızlı yanıt verme](https://youtu.be/e8iFCz5RM4g)

Makaleler    
* [Olay yanıtı için Azure Güvenlik Merkezi’ni kullanma](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Güvenlik playbook’u ile yanıtı otomatikleştirme](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>Gelişmiş bulut savunması

Azure VM'ler, Güvenlik Merkezi’ndeki gelişmiş bulut savunma özelliklerinden yararlanabilir. Bu yetenekler, tam zamanında sanal makine (VM) erişimi ve Uyarlamalı uygulama denetimleri içerir.

Bu özelliklerin Güvenlik Merkezi’nde nasıl kullanılacağını öğrenmek için aşağıdaki kaynakları kullanın.

Videolar  
* [Azure Güvenlik Merkezi – tam zamanında VM erişimi](https://youtu.be/UOQb2FcdQnU)
* [Azure Güvenlik Merkezi - Uyarlamalı Uygulama Denetimleri](https://youtu.be/wWWekI1Y9ck)

Makaleler    
* [Tam zamanında sanal makine erişimini yönetme](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Azure Güvenlik Merkezi'ndeki Uyarlamalı Uygulama Denetimleri](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Uygulamalı etkinlikler

* [Güvenlik Merkezi uygulamalı laboratuvarı](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Güvenlik Merkezi'nde Web Uygulaması Güvenlik Duvarı (WAF) öneri playbook’u](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Güvenlik Merkezi Playbook: Güvenlik Uyarıları](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Ek kaynaklar
* [Güvenlik Merkezi Belgeleri Sayfası](https://docs.microsoft.com/azure/security-center/)
* [Güvenlik Merkezi REST API’si Belgeleri Sayfası](https://msdn.microsoft.com/library/mt704034.aspx)
* [Azure Güvenlik Merkezi hakkında sık sorulan sorular (SSS)](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Güvenlik Merkezi Fiyatlandırma Sayfası](https://azure.microsoft.com/pricing/details/security-center/)
* [Kimlik güvenliği için en iyi uygulamalar](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Ağ güvenliği için en iyi uygulamalar](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [PaaS önerileri](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Uyumluluk](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics müşterileri artık, karma bulut iş yüklerini korumak için Azure Güvenlik Merkezi 'ni kullanabilir](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Topluluk Kaynakları

* [Güvenlik Merkezi UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Güvenlik Merkezi topluluk forumu](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



