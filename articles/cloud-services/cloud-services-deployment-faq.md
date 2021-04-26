---
title: Microsoft Azure Cloud Services SSS için dağıtım sorunları | Microsoft Docs
description: Bu makalede Microsoft Azure Cloud Services dağıtımı hakkında sık sorulan sorular listelenmektedir.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896486"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Azure Cloud Services için dağıtım sorunları (klasik): sık sorulan sorular (SSS)

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).
Bu makalede [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)dağıtım sorunları hakkında sık sorulan sorular yer almaktadır. Boyut bilgileri için [Cloud SERVICES VM boyutu sayfasına](cloud-services-sizes-specs.md) de başvurabilirsiniz.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Üretim yuvasında zaten mevcut bir dağıtım varsa, neden bir bulut hizmetini hazırlama yuvasına dağıtma, bazen bir kaynak ayırma hatası ile başarısız olur mu?
Bir bulut hizmetinin her iki yuvada de bir dağıtımı varsa, tüm bulut hizmeti belirli bir kümeye sabitlenmiştir. Bu, bir dağıtım üretim yuvasında zaten mevcutsa, yeni bir hazırlama dağıtımının yalnızca üretim yuvası ile aynı kümede ayrılabileceği anlamına gelir.

Bulut hizmetinizin bulunduğu kümede dağıtım isteğinizi karşılamak için yeterli fiziksel işlem kaynağı olmadığında ayırma sorunları oluşur.

Bu tür ayırma hatalarını azaltmaya yönelik yardım için bkz. [bulut hizmeti ayırma hatası: çözümler](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Bir bulut hizmeti dağıtımının ölçeklendirilmesi veya ölçeklendirilmesi neden bazen ayırma hatasına neden olabilir?
Bir bulut hizmeti dağıtıldığında, genellikle belirli bir kümeye sabitlenmiştir. Bu, var olan bir bulut hizmetinin ölçeği artırma/genişletme aynı kümede yeni örnekler ayırmalıdır. Küme kapasiteye yaklaştığı takdirde veya istenen VM boyutu/türü yoksa, istek başarısız olabilir.

Bu tür ayırma hatalarını azaltmaya yönelik yardım için bkz. [bulut hizmeti ayırma hatası: çözümler](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Neden bir bulut hizmetini bir benzeşim grubuna dağıtma bazen ayırma hatasına neden olabilir mi?
Bulut hizmeti bir benzeşim grubuna sabitlenmemişse, boş bir bulut hizmetine yeni bir dağıtım, söz konusu bölgedeki herhangi bir kümede yapı tarafından ayrılabilir. Aynı benzeşim grubuna yönelik dağıtımlar aynı kümede denenmeyecektir. Küme kapasiteye yaklaştığı takdirde istek başarısız olabilir.

Bu tür ayırma hatalarını azaltmaya yönelik yardım için bkz. [bulut hizmeti ayırma hatası: çözümler](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>VM boyutunu değiştirme veya var olan bir bulut hizmetine yeni bir VM ekleme nedenleri bazen ayırma hatasına neden olabilir mi?
Bir veri merkezindeki kümeler farklı makine türleri yapılandırmasına sahip olabilir (örneğin, bir seri, AV2 serisi, D serisi, Dv2 serisi, G serisi, H serisi vb.). Ancak tüm kümelerin tüm VM türleri olması gerekmez. Örneğin, zaten serinin bir kümesinde dağıtılmış olan bir bulut hizmetine D serisi VM eklemeye çalışırsanız, bir ayırma hatası yaşarsınız. Bu durum, VM SKU boyutlarını değiştirmeye çalışırsanız (örneğin, bir serinin D serisine geçiş yapmak için) de gerçekleşir.

Bu tür ayırma hatalarını azaltmaya yönelik yardım için bkz. [bulut hizmeti ayırma hatası: çözümler](cloud-services-allocation-failures.md#solutions).

Bölgenizdeki kullanılabilir boyutları denetlemek için bkz. [Microsoft Azure: bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Aboneliğimde veya hizmetmdeki sınırlar/kotalar/kısıtlamalar nedeniyle neden bir bulut hizmeti dağıtıyor?
Ayrılan kaynaklar bölge/veri merkezi düzeyinde hizmetiniz için izin verilen varsayılan veya en yüksek kotayı aşarsa, bulut hizmeti dağıtımı başarısız olabilir. Daha fazla bilgi için bkz. [Cloud Services sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Ayrıca, portalda aboneliğiniz için geçerli kullanımı/kotayı da izleyebilirsiniz: Azure portal => abonelikler => \<appropriate subscription>   => "kullanım + kota".

Kaynak kullanımı/tüketim ile ilgili bilgiler de Azure Faturalama API'leri aracılığıyla alınabilir. Bkz. [Azure tüketim API 'sine genel bakış](../cost-management-billing/manage/consumption-api-overview.md).

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
