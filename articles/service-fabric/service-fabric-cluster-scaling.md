---
title: Azure Service Fabric kümesi ölçeklendirme
description: Azure Service Fabric kümelerini içinde veya dışarı ve yukarı veya aşağı ölçeklendirmeyle ilgili bilgi edinin. Uygulama gereksinimleri değiştikçe kümeler Service Fabric olabilir.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 610c43f64f9073aefe8008473209039122cf36d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591795"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Azure Service Fabric kümelerini ölçeklendirme
Service Fabric küme, mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Bir kümenin parçası olan makine veya VM, düğüm olarak adlandırılır. Kümeler potansiyel binlerce düğüm içerebilir. Service Fabric kümesi oluşturduktan sonra, kümeyi yatay olarak ölçeklendirebilirsiniz (düğüm sayısını değiştirebilir) veya dikey (düğümlerin kaynaklarını değiştirebilirsiniz).  Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz.  Küme ölçeklenirken uygulamalarınız da otomatik olarak ölçeklendirilir.

Küme neden ölçeklendirmeliyim? Uygulama taleplerine zaman içinde değişiklik yapılır.  Daha fazla uygulama iş yükünü veya ağ trafiğini karşılamak için küme kaynaklarını artırmanız veya talep düştüğünde küme kaynaklarını azaltmanız gerekebilir.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Ölçek Genişletme ve genişletme veya yatay ölçeklendirme
Kümedeki düğümlerin sayısını değiştirir.  Yeni düğümler kümeye katılıyorsa, [küme kaynak yöneticisi](service-fabric-cluster-resource-manager-introduction.md) Hizmetleri bunlara, var olan düğümlerde yükü azaltacak şekilde gider.  Kümenin kaynakları verimli bir şekilde kullanılmıyorsa düğüm sayısını da azaltabilirsiniz.  Düğüm kümeden ayrıldığında, hizmetler bu düğümleri kapatır ve kalan düğümlerde yük artar.  Azure 'da çalışan bir kümedeki düğümlerin sayısını azaltmak, bu VM 'lerde iş yükünü değil kullandığınız VM sayısı için ödeme yaptığınız için tasarruf etmenizi sağlayabilir.  

- Avantajlar: sonsuz ölçek, teorik olarak.  Uygulamanız ölçeklenebilirlik için tasarlandıysa daha fazla düğüm ekleyerek sınırsız büyümeyi etkinleştirebilirsiniz.  Bulut ortamlarındaki araçlar, düğüm eklemeyi veya kaldırmayı kolaylaştırır, böylece kapasiteyi kolayca ayarlayabilir ve yalnızca kullandığınız kaynaklar için ödeme yaparsınız.  
- Dezavantajları: uygulamalar [ölçeklenebilirlik için tasarlanmalıdır](service-fabric-concepts-scalability.md).  Uygulama veritabanları ve kalıcılık, daha fazla mimari çalışmanın de ölçeğini gerektirebilir.  Service Fabric durum bilgisi olan hizmetlerde [güvenilir koleksiyonlar](service-fabric-reliable-services-reliable-collections.md) , uygulama verilerinizi ölçeklendirmenizi çok daha kolay hale getirir.

Sanal Makine Ölçek Kümeleri, bir sanal makine koleksiyonunu bir küme olarak dağıtmak ve yönetmek için kullanabileceğiniz bir Azure işlem kaynağıdır. Bir Azure kümesinde tanımlanan her düğüm türü [ayrı bir ölçek kümesi olarak ayarlanır](service-fabric-cluster-nodetypes.md). Her düğüm türü daha sonra bağımsız olarak veya dışarı ölçeklenebilen, farklı bağlantı noktası kümelerine açık olabilir ve farklı kapasite ölçümleri olabilir. 

Bir Azure kümesini ölçeklendirirken aşağıdaki yönergeleri göz önünde bulundurun:
- üretim iş yüklerini çalıştıran birincil düğüm türleri her zaman beş veya daha fazla düğüme sahip olmalıdır.
- durum bilgisi olmayan üretim iş yükleri çalıştıran birincil düğüm türleri her zaman beş veya daha fazla düğüme sahip olmalıdır.
- durum bilgisi olmayan üretim iş yükleri çalıştıran birincil düğüm türleri her zaman iki veya daha fazla düğüme sahip olmalıdır.
- Altın veya gümüş herhangi bir düğüm [türü, her](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) zaman beş veya daha fazla düğüme sahip olmalıdır.
- Bir düğüm türünden rastgele VM örnekleri/düğümleri kaldırmayın, her zaman özelliğindeki sanal makine ölçek kümesi ölçeğini kullanın. Rastgele sanal makine örneklerinin silinmesi, sistemin doğru şekilde yük dengeleyebilme yeteneğini olumsuz etkileyebilir.
- Otomatik ölçeklendirme kuralları kullanıyorsanız, kuralları ölçeklendirmek için (sanal makine örneklerini kaldırma) tek seferde bir düğüm yapılır. Aynı anda birden fazla örneğin ölçeğini azaltma güvenli değildir.

Kümenizdeki Service Fabric düğüm türleri arka uçta sanal makine ölçek kümelerinden yapıldığından, [Otomatik ölçeklendirme kuralları ayarlayabilir veya](service-fabric-cluster-scale-in-out.md) her düğüm türü/sanal makine ölçek kümesini el ile ölçeklendirdirebilirsiniz.

### <a name="programmatic-scaling"></a>Programlı ölçekleme
Birçok senaryoda, [bir kümeyi el ile veya otomatik ölçeklendirme kurallarıyla ölçeklendirmeniz iyi bir](service-fabric-cluster-scale-in-out.md) çözümdür. Ancak, daha gelişmiş senaryolar için, bu, doğru olmayabilir. Bu yaklaşımların olası dezavantajları şunlardır:

- El ile ölçekleme, oturum açmanızı ve açıkça ölçekleme işlemlerine istek yapmanızı gerektirir. Ölçeklendirme işlemleri sıklıkla veya öngörülemeyen zamanlarda gerekliyse, bu yaklaşım iyi bir çözüm olmayabilir.
- Otomatik ölçeklendirme kuralları bir sanal makine ölçek kümesinden bir örneği kaldırdıklarında, düğüm türünün gümüş veya altın bir dayanıklılık düzeyi yoksa, ilgili Service Fabric kümesinden o düğümün bilgisini otomatik olarak kaldırmaz. Otomatik ölçeklendirme kuralları ölçek kümesi düzeyinde çalıştığından (Service Fabric düzeyi yerine), otomatik ölçeklendirme kuralları Service Fabric düğümleri düzgün şekilde kapatmadan kaldırabilir. Bu işlenmemiş düğüm kaldırma işlemi, ölçeklendirme işlemlerinden sonra ' hayalet ' Service Fabric düğüm durumunun arkasına bırakılır. Bir bireyin (veya bir hizmetin) Service Fabric kümesinde kaldırılan düğüm durumunu düzenli aralıklarla temizlemesi gerekir.
- Dayanıklılık düzeyi Gold veya gümüş olan bir düğüm türü kaldırılan düğümleri otomatik olarak temizler, bu nedenle ek temizleme gerekmez.
- Otomatik ölçeklendirme kuralları tarafından desteklenen [çok sayıda ölçüm](../azure-monitor/autoscale/autoscale-common-metrics.md) olsa da, hala sınırlı bir küme vardır. Senaryonuz, bu küme kapsamında olmayan bazı metriyi temel alarak ölçeklendirmeyi çağırırsa, otomatik ölçeklendirme kuralları iyi bir seçenek olmayabilir.

Service Fabric ölçeklendirmeye nasıl yaklaşımınız, senaryonuza bağlıdır. Ölçeklendirmenin bir yaygın olması durumunda, el ile düğüm ekleme veya kaldırma olanağı muhtemelen yeterlidir. Daha karmaşık senaryolar için, programlama yoluyla ölçeklendirme olanağı sunan otomatik ölçeklendirme kuralları ve SDK 'Ları, güçlü alternatifler sunmaktadır.

Azure API 'Leri, uygulamaların sanal makine ölçek kümeleri ve Service Fabric kümeleriyle programlı bir şekilde çalışmasına olanak tanır. Mevcut otomatik ölçeklendirme seçenekleri senaryonuz için çalışmazsa, bu API 'Ler özel ölçeklendirme mantığı uygulamayı mümkün kılar. 

Bu ' giriş haline getirilen ' otomatik ölçeklendirme işlevlerini uygulamaya yönelik bir yaklaşım, ölçeklendirme işlemlerini yönetmek için Service Fabric uygulamasına yeni bir durum bilgisi olmayan hizmet eklemektir. Kendi ölçeklendirme hizmetinizi oluşturmak, uygulamanızın ölçeklendirme davranışı üzerinde en yüksek düzeyde denetim ve özelleştirme sağlar. Bu, bir uygulamanın ne zaman veya nasıl ölçeklendirilen üzerinde kesin denetim gerektiren senaryolar için yararlı olabilir. Ancak, bu denetim bir zorunluluğunu getirir kod karmaşıklığı ile birlikte gelir. Bu yaklaşımın kullanılması, basit olmayan bir ölçeklendirme koduna sahip olmanız gerektiği anlamına gelir. Hizmetin `RunAsync` yönteminde, bir tetikleyici kümesi, ölçeklendirmenin gerekip gerekmediğini (en fazla küme boyutu ve coolazaltmalar gibi parametreleri denetleme dahil) tespit edebilir.   

Sanal makine ölçek kümesi etkileşimleri için kullanılan API (her ikisi de sanal makine örneklerinin geçerli sayısını kontrol etmek ve değiştirmek için), [akıcı Azure Yönetim işlem kitaplığıdır](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Akıcı işlem kitaplığı, sanal makine ölçek kümeleriyle etkileşim kurmak için kullanımı kolay bir API sağlar.  Service Fabric kümeyle etkileşim kurmak için [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient)kullanın.

Ölçeklendirme kodunun ölçeklenmesi için kümede bir hizmet olarak çalıştırılması gerekmez, ancak. Hem hem de `IAzure` `FabricClient` ilişkili Azure kaynaklarına uzaktan bağlanıp, ölçeklendirme hizmeti kolayca bir konsol uygulaması veya Service Fabric uygulamasının dışından çalışan bir Windows hizmeti olabilir.

Bu sınırlamalara göre, [daha özelleştirilmiş otomatik ölçeklendirme modellerini uygulamak](service-fabric-cluster-programmatic-scaling.md)isteyebilirsiniz.

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Ölçeği artırma ve azaltma ya da dikey ölçekleme 
Kümedeki düğümlerin kaynaklarını (CPU, bellek veya depolama) değiştirir.
- Avantajlar: yazılım ve uygulama mimarisi aynı kalır.
- Dezavantajları: bağımsız düğümlerde kaynakları ne kadar artırabileceğiniz için bir sınır olduğundan, sınırlı ölçek. Kapalı kalma süresi, kaynak eklemek veya kaldırmak için fiziksel veya sanal makineleri çevrimdışına almanız gerekir.

Sanal Makine Ölçek Kümeleri, bir sanal makine koleksiyonunu bir küme olarak dağıtmak ve yönetmek için kullanabileceğiniz bir Azure işlem kaynağıdır. Bir Azure kümesinde tanımlanan her düğüm türü [ayrı bir ölçek kümesi olarak ayarlanır](service-fabric-cluster-nodetypes.md). Her düğüm türü ayrıca yönetilebilir.  Düğüm türünü yukarı veya aşağı ölçeklendirmek, yeni bir düğüm türünün (güncelleştirilmiş VM SKU 'SU ile) eklenmesini ve eski düğüm türünün kaldırılmasını içerir.

Bir Azure kümesini ölçeklendirirken aşağıdaki kılavuzu aklınızda bulundurun:
- Birincil düğüm türünü ölçeklendirirseniz, [güvenilirlik katmanının](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) izin verdiği sayıdan daha fazla ölçeklenmez.

Bir düğüm türünü yukarı veya aşağı ölçeklendirmeye yönelik işlem, birincil olmayan veya birincil düğüm türü olmasına bağlı olarak farklılık belirtir.

### <a name="scaling-non-primary-node-types"></a>Birincil olmayan düğüm türlerini ölçekleme
İhtiyacınız olan kaynaklarla yeni bir düğüm türü oluşturun.  Çalışan hizmetlerin yerleştirme kısıtlamalarını yeni düğüm türünü içerecek şekilde güncelleştirin.  Yavaş yavaş (tek seferde), kümenin güvenilirliği etkilenmemesi için eski düğüm türü örnek sayısının örnek sayısını sıfıra küçültün.  Eski düğüm türü kullanımdan kaldırılan için hizmetler, aşamalı olarak yeni düğüm türüne geçirilir.

### <a name="scaling-the-primary-node-type"></a>Birincil düğüm türünü ölçekleme
Güncelleştirilmiş VM SKU 'SU ile yeni bir birincil düğüm türü dağıtın, sonra sistem hizmetleri yeni ölçek kümesine geçiş yapmak için özgün birincil düğüm türü örneklerini birer birer devre dışı bırakın. Kümenin ve yeni düğümlerin sağlıklı olduğunu doğrulayın, sonra silinen düğümlerin orijinal ölçek kümesini ve düğüm durumunu kaldırın.

Bu mümkün değilse, yeni bir küme oluşturabilir ve eski kümenizdeki uygulama durumunu (varsa) [geri yükleyebilirsiniz](service-fabric-reliable-services-backup-restore.md) . Her türlü sistem hizmeti durumunu geri yüklemeniz gerekmez, uygulamalarınızı yeni kümenize dağıttığınızda yeniden oluşturulur. Kümenizde yalnızca durum bilgisi olmayan uygulamalar çalıştırıyorsanız, uygulamalarınızı yeni kümeye dağıtırsınız, geri yükleme yapamazsınız.

## <a name="next-steps"></a>Sonraki adımlar
* [Uygulama ölçeklenebilirliği](service-fabric-concepts-scalability.md)hakkında bilgi edinin.
* [Bir Azure kümesini içinde veya dışarı ölçeklendirin](service-fabric-tutorial-scale-cluster.md).
* Akıcı Azure işlem SDK 'sını kullanarak [bir Azure kümesini programlı bir şekilde ölçeklendirin](service-fabric-cluster-programmatic-scaling.md) .
* [Tek başına kümeyi içinde veya dışarı ölçeklendirin](service-fabric-cluster-windows-server-add-remove-nodes.md).

