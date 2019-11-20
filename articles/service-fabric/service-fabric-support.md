---
title: Azure Service Fabric destek seçenekleri hakkında bilgi edinin | Microsoft Docs
description: Desteklenen Azure Service Fabric küme sürümleri ve dosya desteği biletleri bağlantıları
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: ac36b1a538d5899613e19f157695d0bc333d9ece
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679053"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric destek seçenekleri

Uygulama iş yüklerinizi çalıştırdığınız Service Fabric kümeleriniz için uygun desteği sunmak üzere, sizin için çeşitli seçenekler ayarladık. Gerekli destek düzeyine ve sorunun önem derecesine bağlı olarak doğru seçenekleri seçmeniz gerekir. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Azure için üretim sorunlarını bildirme veya ücretli destek isteme

Azure üzerinde dağıtılan Service Fabric kümenizdeki sorunları raporlamak için, Azure portal veya [Microsoft Destek Portalı](https://support.microsoft.com/oas/default.aspx?prid=16146) ['nda](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) destek için bir bilet açın.

Daha fazla bilgi:
 
- [Azure Için Microsoft desteği](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier desteği](https://support.microsoft.com/en-us/premier).

> [!Note]
> Bir bronz güvenilirlik katmanında veya tek düğümlü kümede çalışan kümeler yalnızca test iş yüklerini çalıştırmanıza izin verir. Bronz güvenilirlik veya tek düğümlü küme üzerinde çalışan bir küme ile ilgili sorunlarla karşılaşırsanız, Microsoft destek ekibi bu sorunu azaltmaya yardımcı olur, ancak bir kök neden analizi gerçekleştirmeyecektir. Daha fazla ayrıntı için lütfen [kümenin güvenilirlik özelliklerine](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) bakın.
>
> Üretime hazır bir küme için gerekli olanlar hakkında daha fazla bilgi için lütfen [Üretim hazırlığı denetim listesine](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)bakın.

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Üretim sorunlarını bildirme veya tek başına Service Fabric kümeleri için ücretli destek isteme

Şirket içinde veya diğer bulutlarda dağıtılan Service Fabric kümenizdeki sorunları raporlamak için, [Microsoft Destek Portalı](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)'nda profesyonel destek için bir bilet açın.

Daha fazla bilgi:

- Şirket [içi Için Microsoft 'Tan profesyonel destek](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier desteği](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric sorunlarını raporla

Service Fabric sorunları bildirmek için bir GitHub deposu ayarladık.  Ayrıca, aşağıdaki forumları etkin bir şekilde izliyoruz.

### <a name="github-repo"></a>GitHub deposu 

[Service Fabric sorunları git](https://github.com/Azure/service-fabric-issues)deposunda Azure Service Fabric sorunlarını raporla. Bu depo, Azure Service Fabric ile ilgili sorunları raporlama ve izleme ve küçük özellik istekleri yapma amaçlıdır. **Canlı site sorunlarını bildirmek için bunu kullanmayın**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow ve MSDN forumları

[StackOverflow üzerindeki Service Fabric etiketi][stackoverflow] ve [MSDN 'deki Service Fabric Forumu][msdn-forum] , platformun nasıl çalıştığı ve bununla ilgili görevleri nasıl gerçekleştirebileceğiniz hakkında sorular sormak için en iyi seçenektir.

### <a name="azure-feedback-forum"></a>Azure geri bildirim Forumu

[Service Fabric Için Azure geri bildirim Forumu][uservoice-forum] , en popüler istekleri bizim merkezimizin uzun dönem planlamasına bir parçası olduğu için, ürün için sahip olduğunuz büyük özellik fikirlerini göndermek için en iyi yerdir. Topluluk içindeki önerileriniz için bir düzeyde destek geçirmenizi öneririz.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric önizleme sürümleri-üretim kullanımı için desteklenmez

Zaman zaman, önizleme olarak yayınlanan, geri bildirimde bulunmak istediğimiz önemli özelliklere sahip sürümleri yayınlarız. Bu önizleme sürümleri yalnızca test amacıyla kullanılmalıdır. Üretim kümeniz, her zaman desteklenen, kararlı bir Service Fabric sürümü çalıştırıyor olmalıdır. Önizleme sürümü her zaman büyük ve küçük sürüm 255 ile başlar. Örneğin, bir Service Fabric sürüm 255.255.5703.949 görürseniz, bu yayın sürümü yalnızca test kümelerinde kullanılır ve önizlemededir. Bu önizleme yayınları Ayrıca [Service Fabric ekip blogundan](https://blogs.msdn.microsoft.com/azureservicefabric) duyurulur ve dahil edilen özelliklerle ilgili ayrıntıları sağlayacaktır.
Bu önizleme sürümleri için ücretli destek seçeneği yoktur. Soru sormak veya geri bildirim sağlamak için [Azure Service Fabric sorunlarını raporla](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) altında listelenen seçeneklerden birini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[Desteklenen Service Fabric sürümleri](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
