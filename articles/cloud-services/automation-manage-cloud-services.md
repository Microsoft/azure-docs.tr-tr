---
title: Azure Otomasyonu 'Nu kullanarak Azure Cloud Services (klasik) yönetme | Microsoft Docs
description: Azure Otomasyonu hizmetinin Azure Cloud Services 'ı ölçekte yönetmek için nasıl kullanılabileceği hakkında bilgi edinin.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 530efd09f3632637c6a12648495dcff0e7bf0e6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743500"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Azure Otomasyonu 'Nu kullanarak Azure Cloud Services 'yi (klasik) yönetme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).
Bu kılavuz size Azure Otomasyonu hizmetini ve Azure bulut hizmetlerinizin yönetimini basitleştirmek için nasıl kullanılabileceğini tanıtacaktır.

## <a name="what-is-azure-automation"></a>Azure Otomasyonu Nedir?
[Azure Otomasyonu](https://azure.microsoft.com/services/automation/) , işlem otomasyonu aracılığıyla bulut yönetimini basitleştirecek bir Azure hizmetidir. Azure Otomasyonu, uzun süre çalışan, el ile, hata açısından açık ve sık tekrarlanan görevler kullanmak, kuruluşunuzun güvenilirliğini, verimliliğini ve değer süresini artırmak için otomatikleştirilebilir.

Azure Otomasyonu, kuruluşunuz büyüdükçe gereksinimlerinizi karşılayacak şekilde ölçeklendirilebilen, yüksek düzeyde güvenilir ve yüksek oranda kullanılabilir bir iş akışı yürütme altyapısı sağlar. Azure Otomasyonu 'nda, süreçler el ile, 3. taraf sistemleri tarafından ya da görevlerin gerektiği zaman tam olarak gerçekleşmesi için zamanlanmış aralıklarla kapatılabilir.

İşlem yükünü düşürün ve BT/DevOps personelini, bulut yönetim görevlerinizi Azure Otomasyonu tarafından otomatik olarak çalışacak şekilde taşıyarak iş değeri ekleyen işlere odaklanmak üzere boşaltın.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Azure Otomasyonu, Azure bulut hizmetlerini nasıl yönetmenize yardımcı olabilir?
Azure Cloud Services, Azure Otomasyonu 'nda [Azure PowerShell araçlarında](/powershell/)kullanılabilen PowerShell cmdlet 'leri kullanılarak yönetilebilir. Azure Otomasyonu, hizmet içinde tüm bulut hizmeti yönetim görevlerinizi gerçekleştirebilmeniz için bu bulut hizmeti PowerShell cmdlet 'lerini kullanıma hazır olarak kullanabilir. Ayrıca, Azure hizmetleri ve üçüncü taraf sistemler genelinde karmaşık görevleri otomatikleştirmek için bu cmdlet 'leri diğer Azure hizmetleri cmdlet 'leriyle da eşleştirin.

Azure Cloud Services 'yi yönetmek için Azure Otomasyonu 'nun bazı örnekleri şunlardır:

* [Azure Blob depolama alanında cscfg veya cspkg güncelleştirildiğinde bir bulut hizmetinin sürekli dağıtımı](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Bulut hizmeti örneklerini tek seferde bir yükseltme etki alanında paralel olarak yeniden başlatma](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Sonraki Adımlar
Azure Otomasyonu ile ilgili temel bilgileri ve Azure bulut hizmetlerini yönetmek için nasıl kullanılabileceğinizi öğrendiğinize göre, Azure Otomasyonu hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin.

* [Azure Otomasyonu genel bakış](../automation/automation-intro.md)
* [İlk runbook’um](../automation/learn/automation-tutorial-runbook-graphical.md)