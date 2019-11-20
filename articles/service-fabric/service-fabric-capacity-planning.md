---
title: Service Fabric uygulamalar için kapasite planlaması | Microsoft Docs
description: Bir Service Fabric uygulaması için gereken işlem düğümlerinin sayısını belirlemeyi açıklar
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: ''
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: cae701e34c3934e8ba8a289e7804e8852f6b5288
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167383"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Service Fabric uygulamalar için kapasite planlaması
Bu belge, Azure Service Fabric uygulamalarınızı çalıştırmanız için gereken kaynak miktarını (CPU, RAM, disk depolama) nasıl tahmin edecağınızı öğretir. Kaynak gereksinimlerinizin zaman içinde değiştirilmesi yaygındır. Genellikle hizmetinizi geliştirirken/test ettiğiniz ve daha fazla kaynak oluşturmanız durumunda daha fazla kaynak yapmanız gerekir. Uygulamanızı tasarlarken, uzun süreli gereksinimleri göz önünde bulundurun ve hizmetinizin yüksek müşteri taleplerini karşılayacak şekilde ölçeklendirilmesine izin veren seçimler yapın.

 Bir Service Fabric kümesi oluşturduğunuzda, kümeyi oluşturan sanal makinelerin (VM 'Ler) çeşitlere karar verirsiniz. Her VM CPU (çekirdek ve hız), ağ bant genişliği, RAM ve disk depolama biçiminde sınırlı miktarda kaynakla gelir. Hizmetiniz zaman içinde büyüdükçe, daha fazla kaynak sunan VM 'lere yükseltebilir ve/veya kümenize daha fazla VM ekleyebilirsiniz. İkincisini yapmak için, hizmetinize dinamik olarak eklenen yeni VM 'lerden yararlanabilmesi için hizmetinizi başlangıçta mimarmanız gerekir.

Bazı hizmetler, VM 'lerde hiç veri bulundurmaz. Bu nedenle, bu hizmetler için kapasite planlaması öncelikle VM 'lerin uygun CPU 'Ları (çekirdek ve hız) seçen performansa odaklanmalıdır. Ayrıca, ağ aktarımlarının ne sıklıkla oluştuğunu ve ne kadar veri aktarıldığını de kapsayan ağ bant genişliğini göz önünde bulundurmanız gerekir. Hizmet kullanımı arttıkça hizmetinizin iyi gerçekleştirmesi gerekiyorsa, kümeye daha fazla sanal makine ekleyebilir ve ağ isteklerinin tüm VM 'lerde yükünü dengeleyebilirsiniz.

VM 'lerde büyük miktarlarda veri yöneten hizmetler için kapasite planlaması öncelikle boyuta odaklanmalıdır. Bu nedenle, VM 'nin RAM ve disk depolamanın kapasitesini dikkatle düşünmeniz gerekir. Windows 'daki sanal bellek yönetimi sistemi, disk alanını uygulama koduna RAM gibi görünür hale getirir. Ayrıca, Service Fabric çalışma zamanı, akıllı disk belleği yalnızca bellekte etkin verileri koruyarak ve soğuk verileri diske taşıyarak akıllı sayfalama sağlar. Uygulamalar bu nedenle VM üzerinde fiziksel olarak kullanılabilir olandan daha fazla bellek kullanabilir. VM 'nin RAM 'de daha fazla disk depolaması saklayabileceğinizden, daha fazla RAM bulunması performansı artırır. Seçtiğiniz VM, sanal makinede istediğiniz verileri depolamak için yeterli büyüklükte bir disk içermelidir. Benzer şekilde, sanal makine size istediğiniz performansı sağlamak için yeterli RAM içermelidir. Hizmetinizin verileri zaman içinde büyürse, kümeye daha fazla sanal makine ekleyebilir ve verileri tüm VM 'lerde bölümleyebilirsiniz.

## <a name="determine-how-many-nodes-you-need"></a>Kaç tane düğüme ihtiyacınız olduğunu belirleme
Hizmetinizi bölümlemek, hizmetinizin verilerini ölçeklendirmenize olanak tanır. Bölümlendirme hakkında daha fazla bilgi için bkz. [bölümlendirme Service Fabric](service-fabric-concepts-partitioning.md). Her bölüm tek bir VM 'ye sığmalıdır, ancak birden çok (küçük) bölüm tek bir VM 'ye yerleştirilebilir. Bu nedenle, daha fazla küçük bölüm olması, daha büyük bir bölümden daha fazla esneklik elde etmenizi sağlar. Çok sayıda bölüm Service Fabric ek yükü artar ve bu işlemler bölümler arasında işlem gerçekleştiremez. Ayrıca, hizmet kodunuzun farklı bölümlerde bulunan veri parçalarına genellikle erişmesi gerektiğinde daha olası ağ trafiği de vardır. Hizmetinizi tasarlarken, etkili bir bölümleme stratejisine ulaşmak üzere bu uzmanları ve dezavantajları dikkatle düşünmeniz gerekir.

Uygulamanızın bir yılda DB_Size GB 'a büyümesini istediğiniz bir mağaza boyutu olan tek durumlu bir hizmete sahip olduğunu varsayalım. Bu yıldan daha fazla büyümeye çalıştığınız sürece daha fazla uygulama (ve bölüm) eklemek istiyorsunuz.  Hizmetinizin yineleme sayısını belirleyen çoğaltma faktörü (RF) Toplam DB_Size etkiler. Tüm çoğaltmalarda toplam DB_Size, çoğaltma faktörü DB_Size ile çarpılır.  Node_Size, hizmetiniz için kullanmak istediğiniz düğüm başına disk alanını/RAM 'i temsil eder. En iyi performansı elde etmek için DB_Size, küme genelinde belleğe sığması gerekir ve VM 'nin RAM 'i etrafında olan bir Node_Size seçilmelidir. RAM kapasitesinden daha büyük bir Node_Size ayırarak, Service Fabric çalışma zamanı tarafından belirtilen disk belleğine bağlı olursunuz. Bu nedenle, tüm verileriniz etkin olarak kabul edildiğinde (veriler disk belleğine alınmış/giden olduğundan) performans en iyi durumda olmayabilir. Ancak, yalnızca verilerin bir bölümünün sık kullanıldığı birçok hizmet için, daha düşük maliyetli hale gelir.

En yüksek performans için gereken düğüm sayısı şu şekilde hesaplanabilir:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Büyüme hesabı
Hizmetin büyümesini istediğiniz DB_Size göre düğüm sayısını, ile başlayan DB_Size 'e ek olarak hesaplamak isteyebilirsiniz. Daha sonra, düğüm sayısını daha fazla sağlamaktan emin olmak için hizmetinizin büyüdükçe düğüm sayısını büyütün. Ancak bölüm sayısı, hizmetinizi en yüksek büyümeye çalıştırdığınız zaman gereken düğüm sayısını temel almalıdır.

Herhangi bir zamanda, bazı ek makinelerin her zaman kullanılabilir olması iyi bir hale gelir, böylece beklenmedik ani artışlar veya hata (örneğin, birkaç VM 'ye geçer) işleyebilmenizi sağlayabilirsiniz.  Ek kapasitenin beklenen artışlar kullanılarak belirlenmesi gerekir, ancak bir başlangıç noktası birkaç ek VM (yüzde 5-10 ek) ayırabilmelidir.

Önceki bir durum bilgisi olan tek bir hizmeti varsayar. Birden fazla durum bilgisi içeren hizmetiniz varsa, diğer hizmetlerle ilişkili DB_Size denklemi denkleme eklemeniz gerekir. Alternatif olarak, her durum bilgisi olan her hizmet için düğüm sayısını ayrı olarak hesaplamanız gerekir.  Hizmetiniz dengeli olmayan çoğaltmalara veya bölümlere sahip olabilir. Bölümlerin diğerlerinden daha fazla veriye sahip olabileceğini göz önünde bulundurun. Bölümlendirme hakkında daha fazla bilgi için bkz. [en iyi yöntemler üzerinde bölümlendirme makalesi](service-fabric-concepts-partitioning.md). Ancak, önceki Denklem bölüm ve çoğaltma belirsiz bir biçimde olduğundan, Service Fabric çoğaltmaların düğümler arasında iyileştirilmiş bir şekilde yayıldığından emin olunmasını sağlar.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Maliyet hesaplama için bir elektronik tablo kullanma
Şimdi formülde bazı gerçek sayılar koyalım. [Örnek bir elektronik tablo](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) , üç tür veri nesnesi içeren bir uygulama için kapasitenin nasıl planlanacağını göstermektedir. Her nesne için boyutunu ve kaç tane nesneyi beklediğinizi yaklaşık olarak yaptık. Ayrıca, her bir nesne türünün kaç tane yineleme istediğini seçtik. Elektronik tablo, kümede depolanacak toplam bellek miktarını hesaplar.

Ardından, bir VM boyutu ve aylık maliyet girersiniz. Çalışma sayfası, VM boyutuna bağlı olarak, verilerinizi düğümlere fiziksel olarak sığacak şekilde ayırmak için kullanmanız gereken minimum bölüm sayısını size bildirir. Uygulamanızın belirli bir hesaplama ve ağ trafiği ihtiyaçlarına uyum sağlamak için daha fazla sayıda bölüm isteyebilirsiniz. Elektronik tablo, Kullanıcı profili nesnelerini yöneten bölüm sayısını, birinden altıya kadar arttı gösterir.

Artık tüm bu bilgilere bağlı olarak, elektronik tablo, tüm verileri bir 26 düğümlü kümede istenen bölümler ve çoğaltmalar ile fiziksel olarak almanızı gösterir. Ancak, bu küme özellikle paketlenmiş olduğundan, bazı ek düğümlerin düğüm arızalarını ve yükseltmelerini kapsaymasını isteyebilirsiniz. Ayrıca, boş düğümleriniz olduğundan, elektronik tablo 57 'den fazla düğümü olan ek bir değer sunmakta olduğunu gösterir. Yine de, düğüm arızalarına ve yükseltmelere uyum sağlamak için 57 düğümlerinin üzerine gitmek isteyebilirsiniz. Elektronik tabloyu uygulamanızın özel ihtiyaçlarına uyacak şekilde ince ayar edebilirsiniz.   

![Maliyet hesaplama için elektronik tablo][Image1]

## <a name="next-steps"></a>Sonraki adımlar
Hizmetinizi bölümlemek hakkında daha fazla bilgi edinmek için [bölümlendirme Service Fabric Hizmetleri][10] 'ne göz atın.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
