---
title: Bağlayıcı bulma stratejisi
description: Bulma API 'sini çağırırken farklı stratejiler hakkında bilgi edinin
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048485"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>AnchorLocateCriteria sınıfını anlama
Bu makalede, bir bağlayıcıyı sorgularken kullanabileceğiniz farklı seçenekleri öğreneceksiniz. AnchorLocateCriteria sınıfının, seçeneklerinin ve geçerli seçenek birleşimlerinin üzerine gidecağız.

## <a name="anchor-locate-criteria"></a>Bağlayıcı bulma ölçütü
[Anchorlocatecriteria sınıfı](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) , daha önce oluşturulan bağlantılar için hizmeti sorgulamanızı sağlar. Tek bir AnchorLocateCriteria nesnesi, her zaman izleyici başına kullanılabilir. Her AnchorLocateCriteria nesnesi şu özelliklerden **tam olarak birini** Içermelidir: [tanımlayıcılar](#identifiers), [NearAnchor](#nearanchor)veya daha fazla bir [cihaz](#neardevice). [Strateji](#strategy), [BypassCache](#bypasscache)ve [requestedcategories](#requestedcategories) gibi ek özellikler isteniyorsa ayarlanabilir. 

### <a name="properties"></a>Özellikler
İzleyicinizdeki aşağıdaki özelliklerden **tam olarak birini** tanımlayın:
#### <a name="identifiers"></a>Tanımlayıcılar
*Varsayılan değer: boş dize dizisi*

Tanımlayıcıları kullanarak, bulmak istediğiniz çıpaların bağlantı kimliklerinin bir listesini tanımlayabilirsiniz. İlk olarak, bağlayıcı oluşturulduktan sonra, bağlantı noktası kimlikleri size döndürülür. Tanımlayıcılarla, AnchorLocateCriteria, istenen Tutturucular kümesini eşleşen bağlayıcı kimlikleri olan bağlayıcılara kısıtlar. Bu özellik bir dize dizisi kullanılarak belirtilir. 

#### <a name="nearanchor"></a>NearAnchor
*Varsayılan değer: ayarlanmadı*

NearAnchor kullanarak, AnchorLocateCriteria, istenen bir uzaklıkta yer alan seçili bir mesafede yer alan Tutturucuların kümesini kısıtladığını belirtebilirsiniz. Bu Seçili bağlayıcıyı kaynak bağlantısı olarak sağlamalısınız. Ayrıca, aramayı daha fazla sınırlandırmak için, istenen mesafeyi kaynak bağlayıcıdan ve döndürülen en fazla bağlantı sayısı olarak ayarlayabilirsiniz.
Bu özellik bir NearAnchorCriteria nesnesi kullanılarak belirtilir.

#### <a name="neardevice"></a>Yakın cihaz
*Varsayılan değer: ayarlanmadı*

Daha önce, bir AnchorLocateCriteria, istenen Tutturucular kümesini cihazın fiziksel konumuna yakın olarak kısıtladığını belirtebilirsiniz. Etkin sensörler, cihazınızın çevresindeki bağlantıları bulmaya yardımcı olmak için kullanılır. Bağlantıları bulmanın en iyi ihtimaline sahip olmak için, tüm uygun sensörler için oturum erişimi sağlamak üzere SensorCapabilities özelliğini yapılandırmanız gerekir. Bu özelliği ayarlama ve kullanma hakkında daha fazla bilgi için bkz. [kaba Reyerelleştirme-Azure uzamsal bağlantıları | Microsoft docs](./coarse-reloc.md) ve [C#](../how-tos/set-up-coarse-reloc-unity.md), [Amaç-C](../how-tos/set-up-coarse-reloc-unity.md), [Swift](../how-tos/set-up-coarse-reloc-swift.md), [Java](../how-tos/set-up-coarse-reloc-java.md), [c++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md), [c++/wınrt](../how-tos/set-up-coarse-reloc-cpp-winrt.md)'de *kaba yeniden yerelleştirme kullanarak Tutturucular oluşturma ve bulma* .
Bu özellik, bir bir bir bir bir, bir bir bir, bir, bir bir bir

### <a name="additional-properties"></a>Ek özellikler
#### <a name="bypasscache"></a>BypassCache
*Varsayılan değer: false*

Bir bağlantı oluşturulduğunda veya bir oturumda bulunduğunda, bu da önbellekte depolanır.  Bu özellik false olarak ayarlandığında, aynı oturumdaki sonraki tüm sorgular önbelleğe alınmış değeri döndürür. ASA hizmetine istek yapılmaz.

#### <a name="requestedcategories"></a>RequestedCategories
*Varsayılan değer: Özellikler | Uzay*

Bu özellik, bir sorgudan AnchorLocateCriteria kullanılarak döndürülen verileri belirlemek için kullanılır. Varsayılan değer hem özellikleri hem de uzamsal verileri döndürür, Özellikler ve uzamsal veriler her ikisi de isteniyorsa bu değiştirilmemelidir. Bu özellik AnchorDataCategory numaralandırması kullanılarak belirtilebilir.

AnchorDataCategory Enum değeri | Döndürülen Veri
-----|------------
Yok | Hiçbir veri döndürülmüyor
Özellikler| AppProperties dahil olmak üzere bağlayıcı özellikleri döndürülür.
Uzamsal| Bir tutturucu hakkındaki uzamsal bilgiler döndürülür.

#### <a name="strategy"></a>Strateji
*Varsayılan değer: Anystrateji*

Strateji, Tutturucuların nasıl yer alınacağını tanımlar. Strateji özelliği bir LocateStrategy enum kullanılarak belirtilebilir.

LocateStrategy Enum değeri | Description
---------------|------------
Anystrateji | Bu strateji, sistemin çıpası bulmak için Visualınformation ve Ilişki stratejileri birleşimlerini kullanmasına izin verir. 
Visualınformation|Bu strateji, geçerli nesnelerin, bağlayıcının görsel parmak izine ait olduğu görsel bilgilerle eşleşen bağlantıları bulmaya çalışır. Bir bağlayıcının görsel ayak izi, şu anda yer alan ve bağlayıcı ile ilişkili görsel bilgilere başvurur. Bu görsel bilgiler genellikle, bağlayıcı oluşturma sırasında özel olarak toplanmamıştır. Şu anda bu stratejiye yalnızca, kabul eden cihaz veya tanımlayıcı özellikleriyle birlikte izin verilir.
İlişki|Bu strateji, var olan [bağlantı bağlayıcılarını](./anchor-relationships-way-finding.md#connect-anchors)kullanarak bağlantıları bulmaya çalışır. Şu anda, bu stratejiye yalnızca NearAnchor veya tanımlayıcılar özellikleriyle birlikte izin verilir. Tanımlayıcılar özelliği ile birlikte kullanıldığında, bu, kullanıcının, kimlikleri tanımlayıcı dizisinde belirtilen tutturucularla önceden oluşturulmuş bağlama ilişkilerine sahip bir çapa (ler) daha önce konumlandırmış olması gerekir. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>LocateStrategy ve AnchorLocateCriteria özelliklerinin geçerli birleşimleri 

Strateji ve AnchorLocateCriteria özelliklerinin tüm birleşimlerine Şu anda sistem tarafından izin verilmiyor. Aşağıdaki tabloda izin verilen birleşimler gösterilmektedir:



Özellik | Anystrateji | İlişki | Visualınformation
-------- | ------------|--------------|-------------------
Tanımlayıcılar | &check;    | &check;     | &check;
NearAnchor  | &check;   (varsayılan Ilişkiye göre değişir) | &check;    | 
Yakın cihaz  | &check;    |   | &check;




## <a name="next-steps"></a>Sonraki adımlar

AnchorLocateCriteria sınıfını kullanarak daha fazla örnek için [Azure uzamsal bağlayıcılarını kullanarak bağlantıları oluşturma ve bulma](../create-locate-anchors-overview.md) bölümüne bakın.