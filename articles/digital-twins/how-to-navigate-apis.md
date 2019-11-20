---
title: API 'Lerde gezinme-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS yönetim API 'Lerini sorgulama hakkında sık kullanılan desenleri öğrenin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: 135c760919f094f7b2d242ad37cc1cadb614e36e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010002"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Azure Digital TWINS yönetim API 'Lerini kullanma

Azure Digital TWINS yönetim API 'Leri, IoT uygulamalarınız için güçlü işlevler sağlar. Bu makalede, API yapısına nasıl gidebileceğiniz gösterilmektedir.  

## <a name="api-summary"></a>API özeti

Aşağıdaki listede, dijital TWINS API 'Lerinin bileşenleri gösterilmektedir.

* [/SPACES](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Bu API 'ler, kuruluminizdeki fiziksel konumlarla etkileşim kurar. Bu, fiziksel konumlarınızın dijital eşlemelerini [uzamsal bir grafik](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)biçiminde oluşturmanıza, silmesine ve yönetmenize yardımcı olur.

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Bu API 'ler, kurulumlarınızdaki cihazlarla etkileşime geçin. Bu cihazlar, bir veya daha fazla sensöri yönetebilir. Örneğin, bir cihaz telefonunuz veya bir Raspberry PI algılayıcısı Pod veya bir Lora ağ geçidi olabilir.

* [/Algılayıcılar](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Bu API 'ler, cihazlarınızla ve fiziksel konumlarınızla ilişkili sensörlerle iletişim kurmanıza yardımcı olur. Algılayıcı, daha sonra uzamsal ortamınızı işlemek için kullanılabilecek ortam değerlerini kaydeder ve gönderir.  

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Bu API 'Ler, dijital TWINS örneğiniz Için bir IoT Hub gibi kaynakları ayarlamanıza yardımcı olur.

* [/Types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Bu API 'ler, bu nesnelere belirli özellikler eklemek için genişletilmiş türleri dijital TWINS nesnelerinizle ilişkilendirmenize olanak tanır. Bu türler Kullanıcı arabirimindeki nesnelerin ve telemetri verilerinizi işleyen özel işlevlerin kolayca filtrelenmesini ve gruplanmasına olanak tanır. Genişletilmiş türlerin örnekleri *DeviceType*, *sensortype*, *sensordatatype*, *spacetype*, *spacesubtype*, *spaceblobtype*, *SpaceResourceType*ve benzeri bir örnektir.

* [/ontolobir](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Bu API 'ler, genişletilmiş türdeki koleksiyonlar olan ontolo, yönetmenize yardımcı olur. Ontololarca, temsil ettikleri fiziksel alana göre nesne türleri için adlar sağlar. Örneğin, *BACnet* ontology, *algılayıcı türleri*, *veri türleri* *, veri alt türleri ve* *dataunittypes*için özel adlar sağlar. Ontolo, hizmet tarafından yönetilir ve oluşturulur. Kullanıcılar ontoloda yükleyip kaldırabilirler. Bir ontology yüklendiğinde, tüm ilişkili tür adları etkinleştirilir ve uzamsal grafiğinizde sağlanmaya hazır olur. 

* [/PropertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Bu API 'leri, *boşluklarınız*, *cihazlarınız*, *kullanıcılarınız*ve *sensörleriniz*için özel özellikler oluşturmak üzere kullanabilirsiniz. Bu özellikler anahtar/değer çiftleri olarak oluşturulur. Bu özellikler için veri türünü, *Primitivedatatype*ayarlayarak tanımlayabilirsiniz. Örneğin, sensörlerinizin *BasicTemperatureDeltaProcessingRefreshTime* adlı bir özelliği tanımlayabilir ve sonra sensörlerinizin her *biri için bu* özellik için bir değer atayabilirsiniz. Ayrıca, özelliği oluştururken, *Min* ve *Max* aralıklarının yanı sıra *validationdata*olarak izin verilen değerleri de ekleyerek bu değerler için kısıtlamalar da ekleyebilirsiniz.

* [/Matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Bu API 'ler, gelen cihaz verilerinize değerlendirmek istediğiniz koşulları belirtmenize olanak tanır. Daha fazla bilgi için [Bu makaleye](concepts-user-defined-functions.md#matchers) bakın. 

* [/UserDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Bu API 'ler, kurulumdan gelen verileri işlemek için, *eşleştiriciler* tarafından tanımlanan koşullar gerçekleştiğinde yürütülecek özel bir işlev oluşturmanıza, silmesine veya güncelleştirmenize olanak tanır. *Kullanıcı tanımlı işlevler*olarak da adlandırılan bu özel işlevler hakkında daha fazla bilgi için [Bu makaleye](concepts-user-defined-functions.md#user-defined-functions) bakın. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Bu API 'Ler, dijital TWINS çözümünüzün veri depolama ve analiz Için diğer Azure hizmetleriyle iletişim kurabilmesi için uç noktalar oluşturmanızı sağlar. Daha fazla bilgi için [Bu makaleyi](concepts-events-routing.md) okuyun. 

* [/Keymağazaların](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Bu API 'ler, boşluklarınız için güvenlik anahtarı depolarını yönetmenizi sağlar. Bu mağazalar bir güvenlik anahtarları koleksiyonu tutabilir ve en son geçerli anahtarları kolayca almanızı sağlar.

* [/Users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Bu API 'ler, gerektiğinde bu bireyleri bulmak için kullanıcıları sizin boşluklarınızla ilişkilendirmenize olanak tanır. 

* [/sistem](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Bu API 'ler, varsayılan boşluk ve algılayıcı türleri gibi sistem genelindeki ayarları yönetmenizi sağlar. 

* [/Roleatamalar](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Bu API 'ler, ROLLERI Kullanıcı kimliği, Kullanıcı TANıMLı işlev kimliği vb. gibi varlıklarla ilişkilendirmenize olanak tanır. Her rol ataması, ilişkilendirilecek varlığın KIMLIĞINI, varlık türünü, ilişkilendirilecek rolün KIMLIĞINI, kiracı KIMLIĞINI ve varlığın bu ilişkiyle erişebileceği kaynağın üst sınırını tanımlayan bir yolu içerir. Daha fazla bilgi için [Bu makaleyi](security-role-based-access-control.md) okuyun.


## <a name="api-navigation"></a>API gezintisi

Dijital TWINS API 'Leri, aşağıdaki parametreleri kullanarak uzamsal grafikleriniz boyunca filtreleme ve gezinmeyi destekler:

- **spaceıd**: API, sonuçları VERILEN alan kimliğine göre filtreleyecek. Ayrıca, **Useparentspace** Boole bayrağı [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API 'leri için geçerlidir. Bu, verilen alan kimliğinin geçerli alan yerine üst alana başvurduğunu gösterir. 

- **MinLevel** ve **maxlevel**: kök alanları 1 düzeyinde olduğu kabul edilir. *N* düzeyinde üst boşluk olan boşluklar *n + 1*düzeyindedir. Bu değerler ayarlandığında, sonuçları belirli düzeylerde filtreleyebilirsiniz. Bunlar, ayarlandığında dahil değerlerdir. Cihazlar, algılayıcılar ve diğer nesneler, en yakın alanla aynı düzeyde kabul edilir. Tüm nesneleri belirli bir düzeyde almak için, hem **MinLevel** hem de **maxlevel** değerlerini aynı değere ayarlayın.

- **mingöreli** ve **maxgöreli**: Bu filtreler verildiğinde, karşılık gelen düzey verilen alan kimliği düzeyine göre belirlenir:
   - Göreli düzey *0* , VERILEN alan kimliğiyle aynı düzeydir.
   - Göreli düzey *1* , BELIRTILEN alan kimliği alt öğeleri ile aynı düzeydeki boşlukları temsil eder. Göreli düzey *n* , belirtilen alandan *n* düzeyle daha düşük olan boşlukları temsil eder.
   - Göreli düzey *-1* , belirtilen alanın üst alanı ile aynı düzeydeki boşlukları temsil eder.

- **çapraz geçiş**: aşağıdaki değerler tarafından belirtilen şekilde, belirli BIR alan kimliğinden her iki yönde de çapraz geçiş yapmanıza olanak sağlar.
   - **Hiçbiri**: Bu varsayılan değer VERILEN alan kimliğine filtre uygular.
   - **Aşağı**: Bu, VERILEN alan kimliği ve alt öğeleri tarafından filtreliyor. 
   - Yukarı: Bu, belirtilen alan KIMLIĞI ve üst öğelerinden göre **filtreliyor**. 
   - **Span**: Bu, uzamsal grafiğin yatay bir kısmına, BELIRTILEN alan kimliğiyle aynı düzeyde filtre uygular. Bu, **mingöreli** veya **maxgöreli** 'nin true olarak ayarlanması gerekir. 


### <a name="examples"></a>Örnekler

Aşağıdaki listede [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API 'leri aracılığıyla bazı gezintinin örnekleri gösterilmektedir. Yer tutucunun `YOUR_MANAGEMENT_API_URL`, `YOUR_INSTANCE_NAME` Azure dijital TWINS örneğinizin adı olduğu ve `YOUR_LOCATION` örneğinizin barındırıldığı bölge olan `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`biçimindeki dijital TWINS API 'Lerinin URI 'sine başvurduğunu unutmayın.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`, kök alanlara bağlı tüm cihazları döndürür.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`, 2, 3 veya 4. düzey alanlara eklenmiş tüm cihazları döndürür.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`, Myspaceıd 'ye doğrudan bağlı tüm cihazları döndürür.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` Myspaceıd veya alt öğelerinden birine bağlı tüm cihazları döndürür.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` myspaceıd hariç, Myspaceıd 'nin alt öğelerinin ekli tüm cihazları döndürür.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` Myspaceıd 'nin hemen alt öğelerine ekli tüm cihazları döndürür.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`, Myspaceıd 'nin üst öğelerinden birine bağlı tüm cihazları döndürür.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`, en az 5 ' e eşit veya daha küçük olan Myspaceıd 'nin alt öğeleri eklenmiş tüm cihazları döndürür.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`, Myspaceıd ile aynı düzeydeki boşluklara ekli tüm cihazları döndürür.


## <a name="odata-support"></a>OData desteği

/Spaces üzerinde GET çağrısı gibi koleksiyonları döndüren API 'lerin çoğu, genel [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) sistem sorgu seçeneklerinin aşağıdaki alt kümesini destekler:  

* **$filter**
* **$orderby** 
* **$top**
* **$Skip** -tüm koleksiyonu görüntülemeyi düşünüyorsanız, tek bir çağrıda onu bir bütün olarak bir küme olarak istemeniz ve sonra uygulamanızda sayfalama yapmanız gerekir. 

> [!NOTE]
> Bazı OData seçenekleri (örneğin, sorgu seçenekleri **$Count**, **$Expand**ve **$Search**) Şu anda desteklenmemektedir.

### <a name="examples"></a>Örnekler

Aşağıdaki listede geçerli OData sözdizimi olan birkaç sorgu gösterilmektedir:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Sonraki adımlar

Bazı ortak API sorgu desenleri hakkında daha fazla bilgi edinmek için [Azure dijital TWINS API 'lerini ortak görevler için sorgulama](./how-to-query-common-apis.md)konusunu okuyun.

API uç noktalarınız hakkında daha fazla bilgi edinmek için [dijital TWINS Swagger nasıl kullanacağınızı](./how-to-use-swagger.md)okuyun.

OData söz dizimini ve kullanılabilir karşılaştırma işleçlerini gözden geçirmek için [Azure bilişsel arama 'Da OData karşılaştırma işleçlerini](../search/search-query-odata-comparison-operators.md)okuyun.
