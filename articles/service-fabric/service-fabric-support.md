---
title: Azure Service Fabric destek seçenekleri hakkında bilgi edinin
description: Desteklenen Azure Service Fabric küme sürümleri ve dosya desteği biletleri bağlantıları
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92316492"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric destek seçenekleri

Service Fabric kümelerini ve uygulama iş yüklerinizi yönetmek için gereken çeşitli destek isteği seçenekleri oluşturduk. Gerekli destek aciliyet 'e ve sorunun önem derecesine bağlı olarak, sizin için uygun olan seçeneği belirleyebilirsiniz.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Azure için üretim sorunlarını bildirme veya ücretli destek isteme

Azure üzerinde çalışan Service Fabric kümeniz ile ilgili sorunları raporlamak için, [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) veya [Microsoft destek portalında](https://support.microsoft.com/oas/default.aspx?prid=16146)bir destek bileti açın.

Aşağıdakiler hakkında daha fazla bilgi edinin:
 
- [Azure Için Microsoft desteği](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier desteği](https://support.microsoft.com/en-us/premier).

> [!Note]
> Bir bronz güvenilirlik katmanında veya tek düğümlü kümede çalışan kümeler yalnızca test iş yüklerini çalıştırmanıza izin verir. Bronz güvenilirlik veya tek düğümlü küme üzerinde çalışan bir küme ile ilgili sorunlarla karşılaşırsanız, Microsoft destek ekibi bu sorunu azaltmaya yardımcı olur, ancak bir kök neden analizi gerçekleştirmeyecektir. Daha fazla bilgi için lütfen [kümenin güvenilirlik özelliklerine](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)bakın.
>
> Üretime hazır bir küme için gerekli olanlar hakkında daha fazla bilgi için lütfen [Üretim hazırlığı denetim listesine](./service-fabric-production-readiness-checklist.md)bakın.

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Üretim sorunlarını bildirme veya tek başına Service Fabric kümeleri için ücretli destek isteme

Şirket içinde veya diğer bulutlarda çalışan Service Fabric kümeleriyle ilgili sorunları raporlamak için, [Microsoft destek portalında](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)profesyonel destek bileti açabilirsiniz.

Aşağıdakiler hakkında daha fazla bilgi edinin:

- Şirket [içi Için Microsoft 'Tan profesyonel destek](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier desteği](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric sorunlarını raporla

Service Fabric sorunları bildirmek için bir GitHub deposu ayarladık.  Ayrıca, aşağıdaki forumları etkin bir şekilde izliyoruz.

### <a name="github-repo"></a>GitHub deposu 

[Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues)'da Azure Service Fabric sorunlarını raporla. Bu depo, sorunları raporlama ve izlemeye ve Azure Service Fabric ilgili küçük özellik istekleri yapmaya yöneliktir. **Canlı site sorunlarını bildirmek için bu ortamı kullanmayın**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow ve MSDN forumları

[StackOverflow üzerindeki Service Fabric etiketi][stackoverflow] ve [MSDN 'deki Service Fabric Forumu][msdn-forum] , platformun nasıl çalıştığı ve belirli görevleri gerçekleştirmek için nasıl kullanabileceğiniz hakkında genel sorular sormak için en iyi şekilde kullanılır.

### <a name="azure-feedback-forum"></a>Azure geri bildirim Forumu

[Service Fabric Için Azure geri bildirim Forumu][uservoice-forum] , önemli ürün özelliği fikirleri göndermek için en iyi yerdir. En popüler istekleri gözden geçiririz ve bunları orta ölçekli uzun süreli planlamaya göre çarpanımız. Topluluk içindeki önerileriniz için bir düzeyde destek geçirmenizi öneririz.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric önizleme sürümleri-üretim kullanımı için desteklenmez

Bazen, erken geri bildirimde bulunmak istediğimiz önemli özellik değişiklikleri içeren özel önizleme yayınları sunuyoruz. Yalnızca üretim iş yükleri sunan yalıtılmış test ortamlarında önizleme sürümlerini kullanmanız gerekir. Üretim kümeniz, her zaman desteklenen, kararlı bir Service Fabric sürümü çalıştırıyor olmalıdır. Bu önizleme sürümleri için ücretli destek seçeneği sunmuyoruz.

Önizleme sürümü her zaman büyük ve küçük sürüm 255 ile başlar. Örneğin, bir Service Fabric sürüm 255.255.5703.949 görürseniz, bu sürüm önizlemededir ve yalnızca test kümelerinde kullanılmak üzere tasarlanmıştır. Bu önizleme yayınları Ayrıca [Service Fabric ekip blogundan](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) duyurulur ve dahil edilen özelliklerle ilgili ayrıntıları sağlayacaktır. Soru sormak veya geri bildirim sağlamak için [Azure Service Fabric sorunlarını raporla](#report-azure-service-fabric-issues) altında listelenen seçeneklerden birini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[Desteklenen Service Fabric sürümleri](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform