---
title: Microsoft Azure Cloud Services SSS için dağıtım sorunları | Microsoft Docs
description: Bu makalede Microsoft Azure Cloud Services dağıtımı hakkında sık sorulan sorular listelenmektedir.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2ffa6d7b1cf0550c97a60614f3f00ddc4b955218
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154812"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services dağıtım sorunları: Sık sorulan sorular (SSS)

Bu makalede [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)dağıtım sorunları hakkında sık sorulan sorular yer almaktadır. Boyut bilgileri için [Cloud SERVICES VM boyutu sayfasına](cloud-services-sizes-specs.md) de başvurabilirsiniz.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Üretim yuvasında zaten mevcut bir dağıtım varsa, neden bir bulut hizmetini hazırlama yuvasına dağıtma, bazen bir kaynak ayırma hatası ile başarısız olur mu?
Bir bulut hizmetinin her iki yuvada de bir dağıtımı varsa, tüm bulut hizmeti belirli bir kümeye sabitlenmiştir. Bu, bir dağıtım üretim yuvasında zaten mevcutsa, yeni bir hazırlama dağıtımının yalnızca üretim yuvası ile aynı kümede ayrılabileceği anlamına gelir.

Bulut hizmetinizin bulunduğu kümede dağıtım isteğinizi karşılamak için yeterli fiziksel işlem kaynağı olmadığında ayırma sorunları oluşur.

Bu tür ayırma hatalarını azaltmaya yönelik yardım için bkz [. bulut hizmeti ayırma hatası: Çözümler](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Bir bulut hizmeti dağıtımının ölçeklendirilmesi veya ölçeklendirilmesi neden bazen ayırma hatasına neden olabilir?
Bir bulut hizmeti dağıtıldığında, genellikle belirli bir kümeye sabitlenmiştir. Bu, var olan bir bulut hizmetinin ölçeği artırma/genişletme aynı kümede yeni örnekler ayırmalıdır. Küme kapasiteye yaklaştığı takdirde veya istenen VM boyutu/türü yoksa, istek başarısız olabilir.

Bu tür ayırma hatalarını azaltmaya yönelik yardım için bkz [. bulut hizmeti ayırma hatası: Çözümler](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Neden bir bulut hizmetini bir benzeşim grubuna dağıtma bazen ayırma hatasına neden olabilir mi?
Bulut hizmeti bir benzeşim grubuna sabitlenmemişse, boş bir bulut hizmetine yeni bir dağıtım, söz konusu bölgedeki herhangi bir kümede yapı tarafından ayrılabilir. Aynı benzeşim grubuna yönelik dağıtımlar aynı kümede denenmeyecektir. Küme kapasiteye yaklaştığı takdirde istek başarısız olabilir.

Bu tür ayırma hatalarını azaltmaya yönelik yardım için bkz [. bulut hizmeti ayırma hatası: Çözümler](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>VM boyutunu değiştirme veya var olan bir bulut hizmetine yeni bir VM ekleme nedenleri bazen ayırma hatasına neden olabilir mi?
Bir veri merkezindeki kümeler farklı makine türleri yapılandırmasına sahip olabilir (örneğin, bir seri, AV2 serisi, D serisi, Dv2 serisi, G serisi, H serisi vb.). Ancak tüm kümelerin tüm VM türleri olması gerekmez. Örneğin, zaten serinin bir kümesinde dağıtılmış olan bir bulut hizmetine D serisi VM eklemeye çalışırsanız, bir ayırma hatası yaşarsınız. Bu durum, VM SKU boyutlarını değiştirmeye çalışırsanız (örneğin, bir serinin D serisine geçiş yapmak için) de gerçekleşir.

Bu tür ayırma hatalarını azaltmaya yönelik yardım için bkz [. bulut hizmeti ayırma hatası: Çözümler](cloud-services-allocation-failures.md#solutions).

Bölgenizdeki kullanılabilir boyutları denetlemek için bkz [. Microsoft Azure: Bölgeye](https://azure.microsoft.com/regions/services)göre kullanılabilir ürünler.

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Aboneliğimde veya hizmetmdeki sınırlar/kotalar/kısıtlamalar nedeniyle neden bir bulut hizmeti dağıtıyor?
Ayrılan kaynaklar bölge/veri merkezi düzeyinde hizmetiniz için izin verilen varsayılan veya en yüksek kotayı aşarsa, bulut hizmeti dağıtımı başarısız olabilir. Daha fazla bilgi için bkz. [Cloud Services sınırları](../azure-subscription-service-limits.md#azure-cloud-services-limits).

Ayrıca, portalda aboneliğiniz için geçerli kullanımı/kotayı da izleyebilirsiniz: Azure Portal = > abonelikler = > \<uygun abonelik > = > "kullanım + kota".

Kaynak kullanımı/tüketim ile ilgili bilgiler de Azure Faturalama API'leri aracılığıyla alınabilir. Bkz. [Azure Kaynak kullanımı API 'si (Önizleme)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Dağıtılmış bir bulut hizmeti VM 'sinin boyutunu yeniden dağıtmaya gerek kalmadan nasıl değiştirebilirim?
Dağıtılmış bir bulut hizmetinin VM boyutunu yeniden dağıtmaya gerek kalmadan değiştiremezsiniz. VM boyutu, yalnızca yeniden dağıtım ile güncelleştirilebilen CSDEF içinde yerleşiktir.

Daha fazla bilgi için bkz. [bulut hizmetini güncelleştirme](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Azure Resource Manager depolama hesabı kullanırken hizmet yönetimi API 'Leri veya PowerShell aracılığıyla Cloud Services neden dağımdayım? 

Bulut hizmeti Azure Resource Manager modeliyle doğrudan uyumlu olmayan klasik bir kaynak olduğundan, bunu Azure Resource Manager depolama hesaplarıyla ilişkilendiremezsiniz. Birkaç seçenek aşağıda verilmiştir: 
 
- REST API üzerinden dağıtma.

    Service Management REST API aracılığıyla dağıtırken, blob depolamaya yönelik bir SAS URL 'SI belirterek, hem klasik hem de Azure Resource Manager depolama hesabıyla çalışacak şekilde sınırlamayı aşabilirsiniz. [Burada](/previous-versions/azure/reference/ee460813(v=azure.100))' packageurl ' özelliği hakkında daha fazla bilgi edinin.
  
- [Azure Portal](https://portal.azure.com)üzerinden dağıtma.

    Bu, arama Azure Resource Manager ve klasik kaynaklar arasında iletişime izin veren bir ara sunucu/dolgusunda gezinilerek [Azure Portal](https://portal.azure.com) çalışacaktır. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Neden Azure portal dağıtım için bir depolama hesabı sağlamamızı gerektiriyor mu? 

Klasik portalda, paket doğrudan yönetim API 'sine yüklendi ve ardından API katmanı, paketi geçici olarak dahili bir depolama hesabına yerleştirdi.  Bu işlem, API katmanı bir dosya karşıya yükleme hizmeti olarak tasarlanmadığı için performans ve ölçeklenebilirlik sorunlarına neden olur.  Azure portal (Kaynak Yöneticisi dağıtım modelinde), API katmanına ilk kez yükleme sırasında, daha hızlı ve daha güvenilir dağıtımlar elde eden geçici adım atlandık. 

Maliyetten itibaren bu çok küçüktür ve tüm dağıtımlarda aynı depolama hesabını yeniden kullanabilirsiniz. Hizmet paketini (CSPKG) karşıya yükleme maliyetini öğrenmek için [depolama maliyeti Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/#storage1) ' ı kullanabilir, cspkg 'yı indirebilir ve ardından cspkg 'yi silebilirsiniz. 
