---
title: Azure Container Instances-sık sorulan sorular
description: Azure Container Instances hizmetiyle ilgili sık sorulan soruların yanıtları
services: container-instances
author: dkkapur
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 29d31e2076e0ff5ddf4f84df13ac2eede482c052
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326006"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Azure Container Instances hakkında sık sorulan sorular

Bu makalede Azure Container Instances hakkında sık sorulan sorular ele alınmaktadır.

## <a name="deployment"></a>Dağıtım

### <a name="how-large-can-my-container-image-be"></a>Kapsayıcı resminiz ne kadar büyük olabilir?

Azure Container Instances üzerinde dağıtılabilir bir kapsayıcı görüntüsünün en büyük boyutu 15 GB 'tır. Dağıttığınız sırada tam kullanılabilirliğe göre daha büyük görüntüleri dağıtabilirsiniz, ancak bu garanti edilmez.

Kapsayıcı resminizin boyutu, ne kadar süreyle dağıtılması gerektiğini etkiler, bu nedenle genellikle kapsayıcı görüntülerinizi mümkün olduğunca küçük tutmak istiyor.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Kapsayıcımın dağıtımını nasıl hızlandırabilirim?

Dağıtım sürelerinin ana öğelerinden biri görüntü boyutu olduğundan, boyutu azaltmanın yollarını arayın. İhtiyacınız olmayan katmanları kaldırın veya görüntüdeki katmanların boyutunu küçültün (daha hafif bir temel işletim sistemi görüntüsü seçerek). Örneğin, Linux kapsayıcıları çalıştırıyorsanız, tam Ubuntu sunucusu yerine alp 'yi temel görüntü olarak kullanmayı düşünün. Benzer şekilde, Windows kapsayıcıları için mümkünse nano sunucu temel görüntüsünü kullanın. 

Ayrıca, [önbelleğe](/rest/api/container-instances/listcachedimages) alınmış görüntüler API 'si aracılığıyla kullanılabilen Azure Container görüntülerinde önceden önbelleğe alınmış görüntülerin listesini de denetlemeniz gerekir. Önceden önbelleğe alınmış görüntülerden biri için bir görüntü katmanını geçirebilirsiniz. 

Kapsayıcı başlatma süresini azaltmak için daha [ayrıntılı rehberlik](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) bölümüne bakın.

### <a name="what-windows-base-os-images-are-supported"></a>Hangi Windows temel işletim sistemi görüntüleri destekleniyor?

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 temel görüntüler

* [Nano sunucu](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`,`sac2016`
* [Windows Server çekirdeği](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,`10.0.14393.x`

> [!NOTE]
> Yarı yıllık kanal sürümü 1709 veya 1803 tabanlı Windows görüntüleri desteklenmez.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 ve istemci temel görüntüleri (Önizleme)

* [Nano sunucu](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`,`10.0.17763.x`
* [Windows Server çekirdeği](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`,`10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`,`10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Kapsayıcımda hangi .NET veya .NET Core görüntü katmanını kullanmalıyım? 

Gereksinimlerinizi karşılayan en küçük görüntüyü kullanın. Linux için .NET Core 2,1 sürümünden bu yana desteklenen bir *Runtime-alçam* .NET Core görüntüsü kullanabilirsiniz. Windows için, tam .NET Framework kullanıyorsanız, bir Windows Server çekirdek görüntüsü (yalnızca çalışma zamanı görüntüsü, örneğin *4.7.2-windowsservercore-ltsc2016*) kullanmanız gerekir. Yalnızca çalışma zamanı görüntüleri küçüktür, ancak .NET SDK gerektiren iş yüklerini desteklemez.

## <a name="availability-and-quotas"></a>Kullanılabilirlik ve Kotalar

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Kapsayıcılarım veya kapsayıcı grubu için kaç çekirdek ve bellek ayıramalıyım?

Bu aslında iş yükünüze bağlıdır. Kapsayıcılarınızın nasıl çalıştığını görmek için küçük ve test performansı ' nı başlatın. [CPU ve bellek kaynak kullanımını izleyin](container-instances-monitor.md)ve ardından kapsayıcıda dağıttığınız işlem türüne göre çekirdek veya bellek ekleyin. 

CPU çekirdekleri üzerindeki üst sınırlar ve kapsayıcı grubu başına kullanılabilir bellek için [kaynak kullanılabilirliğini](container-instances-region-availability.md#availability---general) kontrol ettiğinizden emin olun. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Hangi temel altyapıyı üzerinde çalışır?

Amaçlar 'nin, daha az bir isteğe bağlı kapsayıcı hizmeti olarak Azure Container Instances, bu nedenle Kapsayıcınız geliştirmeye odaklanmanız ve altyapıya ilişkin endişelenmeniz istiyoruz! Performans üzerinde karşılaştırmalar yapmak için merak eden veya andırmaya yönelik olarak, farklı SKU 'ların Azure VM 'lerinde, öncelikle F ve D serisinden çalışır. Bu, hizmeti geliştirmeye ve iyileştirmeye devam ettiğimiz için gelecekte değiştirilmesini bekleiyoruz. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>ACI 'de binlerce çekirdek dağıtmak istiyorum-Kotam arttı mi?
 
Evet (bazen). Geçerli kotalar için [Kotalar ve sınırlar](container-instances-quotas.md) makalesine ve isteğe göre hangi limitlerin arttırılabileceği konusuna bakın.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>4 ' ten fazla çekirdekte ve 16 GB RAM ile dağıtım yapabilir miyim?

Henüz değil. Şu anda bunlar bir kapsayıcı grubu için en yüksek UMS. Belirli gereksinimler veya isteklerle Azure desteği ile iletişim kurun. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Belirli bir bölgede ne zaman bir ACI olur?

Geçerli bölge kullanılabilirliği [burada](container-instances-region-availability.md#availability---general)yayımlanır. Belirli bir bölge için bir gereksiniminize sahipseniz Azure desteği 'ne başvurun.

## <a name="features-and-scenarios"></a>Özellikler ve senaryolar

### <a name="how-do-i-scale-a-container-group"></a>Bir kapsayıcı grubunu ölçeklendirmek Nasıl yaparım??

Şu anda, kapsayıcılar veya kapsayıcı grupları için ölçekleme kullanılamaz. Daha fazla örnek çalıştırmanız gerekirse, API 'lerimizi kullanarak hizmet için kapsayıcı grubu oluşturmaya yönelik daha fazla istek oluşturun ve daha fazla istek oluşturun. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Özel VNet 'te çalışan örneklere hangi özellikler sağlanıyor?

Seçtiğiniz bir Azure sanal ağında kapsayıcı grupları dağıtabilir ve VNet içindeki trafiği Azure kaynaklarınızın tamamında yönlendirmek için kapsayıcı gruplarına özel IP 'Ler atayabilirsiniz. Bir kapsayıcı grubunun bir sanal ağa dağıtılması Şu anda önizleme aşamasındadır ve bu özelliğin bazı yönleri genel kullanıma (GA) önce değişebilir. Güncelleştirilmiş bilgiler için [Önizleme sınırlamalarına](container-instances-vnet.md#preview-limitations) bakın.

## <a name="pricing"></a>Fiyatlandırma

### <a name="when-does-the-meter-start-running"></a>Ölçüm ne zaman çalışmaya başlar?

Kapsayıcı grubu süresi, ilk kapsayıcının görüntüsünü çekmeye başladığımız zamandan (yeni bir dağıtım için) veya kapsayıcı grubunuz yeniden başlatıldığında (zaten dağıtılmışsa), kapsayıcı grubu durduruluncaya kadar hesaplanır. [Container Instances fiyatlandırmayla](https://azure.microsoft.com/pricing/details/container-instances/)ayrıntılara bakın.

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Kapsayıcılarım durdurulduğunda ücretlendiriyorum mı?

Tüm kapsayıcı grubunuz durdurulduktan sonra ölçümler çalışmayı durdurur. Kapsayıcı grubunuzdaki bir kapsayıcı çalıştığı sürece, kapsayıcıları yeniden başlatmak istemeniz durumunda kaynakları tutuyoruz. 

## <a name="next-steps"></a>Sonraki adımlar

* Azure Container Instances hakkında [daha fazla bilgi edinin](container-instances-overview.md) .
* Azure Container Instances 'daki [yaygın sorunları giderin](container-instances-troubleshooting.md) .