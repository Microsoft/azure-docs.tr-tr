---
title: Azure İşlevleri’ni ölçeklendirme ve barındırma
description: Azure Işlevleri tüketim planı ve Premium plan arasında seçim yapma hakkında bilgi edinin.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 079ac41f8b138bccbe4d435a79836d3acee71b7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728632"
---
# <a name="azure-functions-hosting-options"></a>Azure Işlevleri barındırma seçenekleri

Azure 'da bir işlev uygulaması oluşturduğunuzda, uygulamanız için bir barındırma planı seçmeniz gerekir. Azure Işlevleri için kullanılabilen üç temel barındırma planı vardır: [Tüketim planı](consumption-plan.md), [Premium plan](functions-premium-plan.md)ve [adanmış (App Service) plan](dedicated-plan.md). Tüm barındırma planları, hem Linux hem de Windows sanal makinelerinde genel kullanıma sunulmuştur (GA).

Seçtiğiniz barındırma planı aşağıdaki davranışları belirler:

* İşlev uygulamanız nasıl ölçeklendirilir.
* Her işlev uygulaması örneği için kullanılabilir kaynaklar.
* Azure sanal ağ bağlantısı gibi gelişmiş işlevsellik için destek.

Bu makalede, Kubernetes tabanlı barındırma ile birlikte çeşitli barındırma planları arasında ayrıntılı bir karşılaştırma sağlanır.

## <a name="overview-of-plans"></a>Planlara genel bakış

Aşağıda, Işlevleri için üç ana barındırma planının avantajlarından oluşan bir Özet verilmiştir:

| Planlama | Avantajlar |
| --- | --- |  
|**[Tüketim planı](consumption-plan.md)**| Otomatik olarak ölçeklendirin ve işlevleriniz çalışırken yalnızca işlem kaynakları için ödeme yapın.<br/><br/>Tüketim planında, Işlevler ana bilgisayarının örnekleri, gelen olayların sayısına göre dinamik olarak eklenir ve kaldırılır.<br/><br/> ✔ Varsayılan barındırma planı.<br/>✔ Yalnızca işlevleriniz çalışırken ödeyin.<br/>✔, Yüksek yük dönemlerinde bile otomatik olarak ölçeklendirilir.|  
|**[Premium planı](functions-premium-plan.md)**|, Boşta kaldıktan sonra gecikme olmadan uygulamaları çalıştıran, daha güçlü örneklerde çalışan ve sanal ağlara bağlanan, önceden çarpımış çalışanları kullanarak isteğe bağlı olarak otomatik olarak ölçeklendirin. <br/><br/>Aşağıdaki durumlarda Azure Işlevleri Premium planını göz önünde bulundurun: <br/><br/>İşlev uygulamalarınızın sürekli veya neredeyse sürekli çalışmasını ✔.<br/>✔ Yüksek sayıda küçük yürütmeler ve yüksek bir yürütme faturanız, ancak tüketim planında düşük GB saniyedir.<br/>✔ Tüketim planı tarafından sağlandıkından daha fazla CPU veya bellek seçeneği gerekir.<br/>Kodunuzun, tüketim planında izin verilen en uzun yürütme süresinden daha uzun süre çalışmasını ✔.<br/>✔, Sanal ağ bağlantısı gibi tüketim planında kullanılamayan özellikler gerektirir.|  
|**[Ayrılmış plan](dedicated-plan.md)** |İşlevlerinizi düzenli [App Service plan ücretlerindeki](https://azure.microsoft.com/pricing/details/app-service/windows/)bir App Service planı içinde çalıştırın.<br/><br/>[Dayanıklı işlevler](durable/durable-functions-overview.md) kullanılamayacak uzun süre çalışan senaryolar için idealdir. Aşağıdaki durumlarda bir App Service planı düşünün:<br/><br/>Daha önce başka App Service örnekleri çalıştıran mevcut, az kullanılan VM 'Ler ✔.<br/>İşlevlerinizin çalıştırılacağı özel bir görüntü sağlamak ✔. <br/>Tahmine dayalı ölçekleme ve maliyetler ✔ gereklidir.|  

Bu makaledeki karşılaştırma tabloları, işlev uygulamalarınızın çalıştırılacağı en yüksek denetim ve yalıtım miktarını sağlayan aşağıdaki barındırma seçeneklerini de içerir.  

| Barındırma seçeneği | Ayrıntılar |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service Ortamı (ASE), yüksek ölçekte App Service uygulamaları güvenli bir şekilde çalıştırmak için tam olarak yalıtılmış ve ayrılmış bir ortam sağlayan bir App Service özelliğidir.<br/><br/>ASEs, gereken uygulama iş yükleri için uygundur: <br/><br/>Çok yüksek ölçekli ✔.<br/>Tam işlem yalıtımı ve güvenli ağ erişimi ✔.<br/>✔ Yüksek bellek kullanımı.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes, Kubernetes platformunun üstünde çalışan tam yalıtılmış ve ayrılmış bir ortam sağlar.<br/><br/> Kubernetes şunları gerektiren uygulama iş yükleri için uygundur: <br/>Özel donanım gereksinimlerini ✔.<br/>Yalıtım ve güvenli ağ erişimi ✔.<br/>Karma veya çok bulut ortamında çalıştırmak ✔.<br/>✔, Mevcut Kubernetes uygulamaları ve hizmetleriyle birlikte çalışır.|  

Bu makaledeki geri kalan tablolar, çeşitli özellikler ve davranışlar için planları karşılaştırın. Dinamik barındırma planları (tüketim ve Premium) arasındaki maliyet karşılaştırması için bkz. [Azure işlevleri fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/functions/). Çeşitli adanmış plan seçeneklerinin fiyatlandırması için [App Service fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/app-service/windows/)bakın. 

## <a name="operating-systemruntime"></a>İşletim sistemi/çalışma zamanı

Aşağıdaki tabloda barındırma planları için desteklenen işletim sistemi ve dil çalışma zamanı desteği gösterilmektedir.

| | Linux<sup>1</sup><br/>Yalnızca kod | Windows<sup>2</sup><br/>Yalnızca kod | Linux<sup>1, 3</sup><br/>Docker kapsayıcısı |
| --- | --- | --- | --- |
| **[Tüketim planı](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Destek yok  |
| **[Premium planı](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Ayrılmış plan](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | yok | yok |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux, Python çalışma zamanı yığını için desteklenen tek işletim sistemidir. <br/>
<sup>2</sup> Windows, PowerShell çalışma zamanı yığını için desteklenen tek işletim sistemidir.<br/>
<sup>3</sup> Linux, Docker kapsayıcıları için desteklenen tek işletim sistemidir.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Ölçek

Aşağıdaki tabloda, çeşitli barındırma planlarının ölçeklendirme davranışları karşılaştırılmaktadır.

| Planlama | Ölçeği genişletme | En fazla # örnek |
| --- | --- | --- |
| **[Tüketim planı](consumption-plan.md)** | [Olay odaklı](event-driven-scaling.md). Yüksek yük dönemlerinde bile otomatik olarak ölçeklendirin. Azure Işlevleri altyapısı, gelen tetikleyici olaylarının sayısına bağlı olarak Işlevler ana bilgisayarının ek örneklerini ekleyerek CPU ve bellek kaynaklarını ölçeklendirir. | 200 |
| **[Premium planı](functions-premium-plan.md)** | [Olay odaklı](event-driven-scaling.md). Yüksek yük dönemlerinde bile otomatik olarak ölçeklendirin. Azure Işlevleri altyapısı, işlevlerinin tetiklendiği olay sayısına göre Işlevler ana bilgisayarının ek örneklerini ekleyerek CPU ve bellek kaynaklarını ölçeklendirir. |100|
| **[Adanmış plan](dedicated-plan.md)**<sup>1</sup> | El ile/otomatik ölçeklendirme |10-20|
| **[Ao](dedicated-plan.md)**<sup>1</sup> | El ile/otomatik ölçeklendirme |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | [Keda](https://keda.sh)kullanarak Kubernetes kümeleri için olay odaklı otomatik ölçeklendirme. | &nbsp;Kümeye göre &nbsp; değişir&nbsp;&nbsp;|

<sup>1</sup> çeşitli App Service planı seçeneklerine yönelik belirli sınırlar için, [App Service plan sınırlarına](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)bakın.

## <a name="cold-start-behavior"></a>Soğuk başlangıç davranışı

| Planlama | Ayrıntılar | 
| -- | -- |
| **[Tüketim &nbsp; planı](consumption-plan.md)** | Boşta kaldığında uygulamalar sıfıra ölçeklendirebilir, ancak bazı isteklerin başlangıçta ek gecikmesi olabilir.  Tüketim planı, işlev Konağı ve dil işlemlerini çalıştıran önceden çarpımış yer tutucu işlevlerden çekme dahil olmak üzere soğuk başlangıç süresini azaltmanıza yardımcı olmak için bazı iyileştirmeler içerir. |
| **[Premium planı](functions-premium-plan.md)** | Tüm soğuk başlarını önlemek için sıcak örnekler adet sürekli. |
| **[Ayrılmış plan](dedicated-plan.md)** | Adanmış bir planda çalışırken, Işlevler ana bilgisayarı sürekli çalışabilir ve bu da soğuk başlatması aslında bir sorun değildir. |
| **[ASE](dedicated-plan.md)** | Adanmış bir planda çalışırken, Işlevler ana bilgisayarı sürekli çalışabilir ve bu da soğuk başlatması aslında bir sorun değildir. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | KEDA yapılandırmasına bağlı olarak, uygulamalar soğuk başlangıçtan kaçınmak için yapılandırılabilir. Sıfıra ölçeklendirilmesi için yapılandırıldıysa, yeni olaylar için soğuk başlatmaya bir deneyim yaşanır. 

## <a name="service-limits"></a>Hizmet sınırlamaları

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Ağ özellikleri

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Faturalandırma

| Planlama | Ayrıntılar |
| --- | --- |
| **[Tüketim planı](consumption-plan.md)** | Yalnızca işlevlerinizin çalıştığı süre için ödeme yapın. Fatura oluşturulurken yürütme sayısı, yürütme süresi ve kullanılan bellek temel alınır. |
| **[Premium planı](functions-premium-plan.md)** | Premium plan, gerekli ve önceden çarpımış örnekler genelinde kullanılan çekirdek saniye ve bellek sayısını temel alır. Plan başına en az bir örnek her zaman sıcak tutulmalıdır. Bu plan, en öngörülebilir fiyatlandırmayı sağlar. |
| **[Adanmış plan](dedicated-plan.md)* | Aynı şekilde, Web Apps gibi diğer App Service kaynaklarda yaptığınız gibi App Service planındaki işlev uygulamaları için de aynı ödeme yaparsınız.|
| **[App Service Ortamı (Ao)](dedicated-plan.md)** | Altyapı için ödeme yapan ve asa 'nın boyutuyla değişmeyen bir asa için sabit bir aylık ücret vardır. Ayrıca, App Service plan vCPU 'Su başına bir ücret de vardır. ASE'de barındırılan tüm uygulamalar, Yalıtılmış fiyatlandırma SKU’su içindedir. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Yalnızca Kubernetes kümenizin maliyetlerini ödeyin; Işlevler için ek faturalandırma yok. İşlev uygulamanız, kümenizin üstünde, normal bir uygulama gibi bir uygulama iş yükü olarak çalışır. |

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Işlevlerinde dağıtım teknolojileri](functions-deployment-technologies.md) 
+ [Azure İşlevleri geliştirici kılavuzu](functions-reference.md)