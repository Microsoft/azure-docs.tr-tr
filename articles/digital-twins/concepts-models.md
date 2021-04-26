---
title: DTDL modelleri
titleSuffix: Azure Digital Twins
description: Azure Digital TWINS 'in ortamınızdaki varlıkları anlatmak için özel modeller nasıl kullandığını anlayın.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b3f0dd599f982e19fee7febc3b85d46f91a55b35
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589304"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Azure Digital Twins'deki ikiz modellerini anlama

Azure dijital TWINS 'in temel özellikleri, kendi sözlüğünüzü tanımlayabilir ve ikizi grafınızı işletmenizin otomatik olarak tanımlanan koşullarında oluşturabilir. Bu yetenek, Kullanıcı tarafından belirtilen **modeller** aracılığıyla sağlanır. Modellerinizi, dünyanın bir açıklamasında adlar olarak düşünebilirsiniz. 

Model, nesne odaklı programlama dilindeki bir **sınıfa** benzer ve gerçek çalışma ortamınızdaki belirli bir kavram için veri şekli tanımlar. Modeller, adlara sahiptir ( *Oda* veya *sıcaklık algılayıcısı* gibi) ve ortamınızdaki bu varlık türünün neler yapabileceğini tanımlayan özellikler, telemetri/olaylar ve komutlar gibi öğeleri içerir. Daha sonra bu modelleri, bu tür açıklamasını karşılayan belirli varlıkları temsil eden [**dijital TWINS**](concepts-twins-graph.md) oluşturmak için kullanacaksınız.

Azure dijital TWINS modelleri, JSON-LD tabanlı **dijital Ikizi tanım dili (DTDL)** içinde temsil edilir.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Modeller için dijital Ikizi tanım dili (DTDL)

Azure dijital TWINS modelleri, dijital TWINS tanım dili (DTDL) kullanılarak tanımlanmıştır. 

GitHub: [**dijital TWINS tanım dili (dtdl)-sürüm 2**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)' de dtdl için tam dil özelliklerini görüntüleyebilirsiniz.

JSON-LD tabanlı bir dil olan DTDL, programlama dilinden bağımsız olarak çalışır. DTDL, Azure dijital TWINS 'e özel değildir, ancak [ıot Tak ve kullan](../iot-pnp/overview-iot-plug-and-play.md)gibi diğer IoT hizmetlerindeki cihaz verilerini göstermek için de kullanılır. Azure dijital TWINS, DTDL **sürüm 2** kullanır (Azure dijital TWINS Ile dtdl sürüm 1 kullanımı artık kullanım dışıdır). 

Bu makalenin geri kalanı, dilin Azure dijital TWINS 'de nasıl kullanıldığını özetler.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure dijital TWINS DTDL uygulama özellikleri

DTDL kullanan hizmetlerin hepsi aynı DTDL özelliklerinin aynısını uygulamaz. Örneğin, IoT Tak ve Kullan grafik için olan DTDL özelliklerini kullanmaz, Azure dijital TWINS Şu anda DTDL komutlarını uygulamıyor. 

Bir DTDL modelinin Azure dijital TWINS ile uyumlu olması için, bu gereksinimleri karşılaması gerekir:

* Bir modeldeki tüm üst düzey DTDL öğelerinin *Interface* türünde olması gerekir. Bunun nedeni, Azure Digital TWINS model API 'Lerinin bir arabirimi ya da arabirim dizisini temsil eden JSON nesnelerini almasına yönelik olması olabilir. Sonuç olarak, en üst düzeyde başka bir DTDL öğesi türüne izin verilmez.
* Azure dijital TWINS için DTDL herhangi bir *komut* tanımlamamalıdır.
* Azure dijital TWINS yalnızca tek bir bileşen iç içe geçme düzeyine izin verir. Bu, bileşen olarak kullanılan bir arabirimin hiçbir bileşene sahip olamayacağı anlamına gelir. 
* Arabirimler, diğer DTDL arabirimleri içinde satır içi olarak tanımlanamaz; kendi kimlikleri olan ayrı en üst düzey varlıklar olarak tanımlanmalıdır. Daha sonra, başka bir arabirim bu arabirimi bir bileşen olarak veya devralma yoluyla eklemek istediğinde, KIMLIĞINE başvurabilir.

Azure dijital TWINS, `writable` Özellikler veya ilişkilerdeki özniteliği de gözlemlemez. Bu, DTDL belirtimlerine göre ayarlanabilir, ancak değer Azure dijital TWINS tarafından kullanılmaz. Bunun yerine, bunlar her zaman Azure dijital TWINS hizmeti için genel yazma izinlerine sahip dış istemciler tarafından yazılabilir olarak değerlendirilir.

## <a name="elements-of-a-model"></a>Bir modelin öğeleri

Bir model tanımı içinde, üst düzey kod öğesi bir **arabirimdir**. Bu tür modelin tamamını kapsüller ve modelin geri kalanı arabirim içinde tanımlanır. 

Bir DTDL model arabirimi, aşağıdaki alanlardan her birinin sıfır, bir veya bir çoğunu içerebilir:
* **Özellik** özellikleri, bir varlığın durumunu temsil eden veri alanlarıdır (birçok nesne odaklı programlama dilinde özellikler gibi). Özellikler depolamayı yedekliyor ve herhangi bir zamanda okunabilir.
* **Telemetri** -telemetri alanları ölçümleri ve olayları temsil eder ve genellikle cihaz algılayıcı ayarlarını göstermek için kullanılır. Özelliklerden farklı olarak, telemetri dijital bir ikizi depolanmaz; Bu, gerçekleşdikleri sırada işlenmesi gereken bir dizi zamana bağlanan veri olaydır. Özellik ve telemetri arasındaki farklılıklar hakkında daha fazla bilgi için aşağıdaki [*Özellikler vs. telemetri*](#properties-vs-telemetry) bölümüne bakın.
* **Bileşen** -bileşenler, model arabiriminizi başka arabirimlerin bir derlemesi olarak oluşturmanıza olanak tanır. Bir bileşene örnek olarak, bir *Telefon* için model tanımlarken kullanılan, önde gelen bir *Kamera* arabirimi (ve başka bir bileşen arabirimi *arkakamerası*) bulunur. İlk olarak, kendi modeli gibi *Frontcamera* için bir arabirim tanımlamanız gerekir ve ardından *Telefon* tanımlarken buna başvurabilirsiniz.

    Çözümünüzün integral bir parçası olan, ancak ayrı bir kimlik gerektirmeyen ve ikizi grafının bağımsız olarak oluşturulması, silinmesi veya yeniden düzenlenmesinin gerekli olmadığı bir şeyi betimleyen bir bileşen kullanın. Varlıkların ikizi grafiğinde bağımsız olarak var olmasını istiyorsanız, bunları farklı modellerdeki ayrı dijital ikgörüleri olarak temsil edin, *ilişkilerin* bağlanır (bkz. sonraki madde işareti).
    
    >[!TIP] 
    >Bileşenler, bir model arabirimi içindeki ilgili özelliklerin kümelerini gruplamak için de kullanılabilir. Bu durumda, her bir bileşeni arabirim içinde bir ad alanı veya "klasör" olarak düşünebilirsiniz.
* **İlişki** ilişkileri, dijital bir ikizi diğer dijital TWINS ile nasıl dahil edileceğini temsil etmenize olanak tanır. İlişkiler, *Contains* ("kat yer alan"), *cozı* ("HVAC Cozı"), *isbilledto* ("sıkıştırıcı kullanıcıya faturalandırılır") vb. gibi farklı anlam anlamlarını temsil edebilir. İlişkiler çözümün birbiriyle ilişkili varlıkların bir grafiğini sağlamasına izin verir. İlişkiler Ayrıca kendi [özelliklerine](#properties-of-relationships) sahip olabilir.

> [!NOTE]
> [DTDL özelliği](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) Ayrıca, dijital bir ikizi (sıfırlama komutu gibi) veya bir fanı açık veya kapalı bir komut olarak yürütülebilecek Yöntemler olan **komutları** tanımlar. Ancak, *Komutlar Şu anda Azure dijital TWINS 'te desteklenmemektedir.*

### <a name="properties-vs-telemetry"></a>Özellikler ve telemetri karşılaştırması

Azure dijital TWINS 'te DTDL **özelliği** ve **telemetri** alanları arasında ayrım yapmak için bazı ek yönergeler aşağıda verilmiştir.

Azure dijital TWINS modelleriyle ilgili özellikler ve telemetri arasındaki fark aşağıdaki gibidir:
* Depolama alanının yedeklenmemesi için **Özellikler** bekleniyor. Bu, herhangi bir zamanda bir özelliği okuyabilmeniz ve değerini alabilmek anlamına gelir. Özellik yazılabilir ise, özelliğinde bir değeri de saklayabilirsiniz.  
* **Telemetri** bir olay akışı gibidir; Bu, kısa ömrü olan bir veri iletisi kümesidir. Olay ve işlem sırasında gerçekleştirilecek eylemler için dinlemeyi ayarlamazsanız, olayın daha sonra izlenmesi gerekmez. Buraya geri dönüp daha sonra okuyamıyorum. 
  - C# koşullarında telemetri bir C# olayı gibidir. 
  - IoT koşullarında telemetri genellikle bir cihaz tarafından gönderilen tek bir ölçümdür.

**Telemetri** genellikle IoT cihazlarıyla kullanılır, çünkü birçok cihaz, oluşturdukları ölçüm değerlerini depoladığını veya bunlarla ilgilenmez. Bunlar yalnızca "Telemetri" olaylarının akışı olarak gönderilir. Bu durumda, telemetri alanının en son değeri için cihazı istediğiniz zaman sorgulayın. Bunun yerine, cihazdaki iletileri dinlemek ve iletiler geldikçe işlem yapmanız gerekir. 

Sonuç olarak, Azure dijital TWINS 'te bir model tasarlarken, büyük olasılıkla TWINS 'nizi modellemek için çoğu durumda **özellikleri** kullanırsınız. Bu sayede depolama alanı yedekleme ve veri alanlarını okuma ve sorgulama imkanına sahip olursunuz.

Telemetri ve özellikler genellikle cihazlardan veri girişini işlemek için birlikte çalışır. Azure dijital TWINS 'e yönelik tüm giriş [API 'ler](how-to-use-apis-sdks.md)aracılığıyla olduğundan, genellikle giriş işlevinizi kullanarak cihazlardan telemetri veya özellik olaylarını okuyabilir ve yanıt olarak Azure dijital TWINS 'te bir özellik ayarlarsınız. 

Ayrıca Azure dijital TWINS API 'sinden bir telemetri olayı yayımlayabilirsiniz. Diğer telemetride olduğu gibi, işlemek için bir dinleyici gerektiren kısa süreli bir olaydır.

#### <a name="properties-of-relationships"></a>İlişkilerin özellikleri

DTDL Ayrıca **ilişkilerin** kendi özelliklerine sahip olmasını sağlar. Bir DTDL modeli içinde bir ilişki tanımlarken, ilişki, `properties` ilişkiye özgü durumu tanımlamak için özel özellikler tanımlayabileceğiniz kendi alanına sahip olabilir.

## <a name="model-inheritance"></a>Model devralma

Bazen bir modeli daha fazla özelleştirmek isteyebilirsiniz. Örneğin, genel bir model *odası* ve özelleştirilmiş çeşitler *conferenceroom* ve *Gym* olmak yararlı olabilir. Hızlı özelleşmede DTDL devralmayı destekler: arabirimler, bir veya daha fazla arabirimden devralınabilir. 

Aşağıdaki örnek, önceki DTDL örneğinde bulunan *Planet* modelini daha büyük bir *ünalbody* modelinin alt türü olarak yeniden görüntüle. "Üst" model önce tanımlanmıştır ve ardından "alt" modeli alanını kullanarak bunu oluşturur `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

Bu örnekte, bir ad, toplu ve sıcaklığın bir adı, bir kütle ve sıcaklık katkısında olduğunu *Ttıalgövdesi* . `extends`Bölüm bir arabirim adıdır veya arabirim adları dizisidir (uzatma arabirimine isterseniz birden çok üst modelden devralma olanağı sağlar).

Devralma uygulandıktan sonra, genişletme arabirimi tüm devralma zincirinden tüm özellikleri sunar.

Genişletme arabirimi üst arabirimlerin tanımlarından hiçbirini değiştiremez; yalnızca bunlara eklenebilir. Ayrıca, kendi üst arabirimlerinde tanımlanmış bir özelliği (özellikler aynı olarak tanımlanmış olsa bile) yeniden tanımlayamazsınız. Örneğin, bir üst arabirim bir `double` özellik *kütle* tanımlıyorsa, genişletme arabirimi de olsa bile bir *yığın* Bildirimi içeremez `double` .

## <a name="model-code"></a>Model kodu

İkizi tür modelleri, herhangi bir metin düzenleyicisinde yazılabilir. DTDL dili JSON söz dizimini izler, bu nedenle modelleri *. JSON* uzantısıyla depomalısınız. JSON uzantısının kullanılması, DTDL belgeleriniz için temel sözdizimi denetimi ve vurgulaması sağlamak üzere birçok programlama metin düzenleyicilerini etkinleştirir. Ayrıca, [Visual Studio Code](https://code.visualstudio.com/)Için bir [dtdl uzantısı](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) da mevcuttur.

### <a name="possible-schemas"></a>Olası şemalar

Dtdl başına, *özellik* ve *telemetri* özniteliklerinin şeması standart temel türler,,, `integer` `double` `string` , ve gibi `Boolean` diğer türler olabilir `DateTime` `Duration` . 

İlkel türlere ek olarak, *özellik* ve *telemetri* alanları bu karmaşık türlere sahip olabilir:
* `Object`
* `Map`
* `Enum`

*Telemetri* alanları da desteklenir `Array` .

### <a name="example-model"></a>Örnek model

Bu bölüm, DTDL arabirimi olarak yazılmış tipik bir modele örnek içerir. Model, her biri bir ad, yığın ve sıcaklığa sahip olan **plananları** açıklar.
 
Gezegenlerin kendi uyduları olan **Moons** ile de etkileşime girebileceği göz önünde **bulundurun.** Aşağıdaki örnekte, `Planet` model iki harici modele (ve) başvurarak bu diğer varlıklara bağlantıları ifade eder `Moon` `Crater` . Bu modeller ayrıca aşağıdaki örnek kodda tanımlanmıştır, ancak birincil örnekte durmaması için çok basittir `Planet` .

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Modelin alanları şunlardır:

| Alan | Açıklama |
| --- | --- |
| `@id` | Model için bir tanımlayıcı. Biçiminde olmalıdır `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Açıklanmakta olan bilgi türünü tanımlar. Bir arabirim için tür *arabirimdir*. |
| `@context` | JSON belgesi [bağlamını](https://niem.github.io/json/reference/json-ld/context/) ayarlar. Modeller kullanmalıdır `dtmi:dtdl:context;2` . |
| `displayName` | seçim İsterseniz modele kolay bir ad vermenizi sağlar. |
| `contents` | Kalan tüm arabirim verileri, öznitelik tanımlarının bir dizisi olarak buraya yerleştirilir. Her öznitelik `@type` , açıkladığı arabirim bilgilerinin sıralamasını belirlemek için bir (*özellik*, *telemetri*, *komut*, *ilişki* veya *bileşen*) ve ardından gerçek özniteliği tanımlayan bir özellikler kümesi (örneğin, `name` ve `schema` bir *özelliği* tanımlamak) sağlamalıdır. |

> [!NOTE]
> Bileşen *arabiriminin (Bu* örnekteki), kendisini kullanan arabirimle (*Planet*) aynı dizide tanımlandığını unutmayın. Bu şekilde, arabirimin bulunması için API çağrılarında bu şekilde tanımlanması gerekir.

## <a name="best-practices-for-designing-models"></a>Model tasarlamak için en iyi uygulamalar

Ortamınızdaki varlıkları yansıtmak için modeller tasarlarken, geleceğe bakmak ve tasarımınızın [sorgu](concepts-query-language.md) etkilerine ilişkin etkilerini göz önünde bulundurmanız yararlı olabilir. Grafik geçiş işleminden büyük sonuç kümelerinden kaçınacak şekilde Özellikler tasarlamak isteyebilirsiniz. Tek bir sorguda tek düzeyli ilişkiler olarak yanıtlanacak ilişkileri modellemek de isteyebilirsiniz.

### <a name="validating-models"></a>Modelleri doğrulama

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Modeller için Araçlar 

Modellerle ve ontoloetlerden daha da kolay hale getirmek için birkaç örnek mevcuttur. Bunlar bu depoda bulunur: [dijital TWINS tanım dili (DTDL) araçları](https://github.com/Azure/opendigitaltwins-tools).

Bu bölümde, geçerli örnek kümesi daha ayrıntılı olarak açıklanmaktadır.

### <a name="model-uploader"></a>Model Uploader 

_**Azure dijital TWINS 'e model yükleme**_

Modellerinizi oluşturmayı, genişletmeyi veya seçmeyi tamamladıktan sonra, çözümünüzde kullanılabilir hale getirmek için bunları Azure dijital TWINS örneğinizle karşıya yükleyebilirsiniz. Bu işlem, [*nasıl yapılır: DTDL modellerini yönetme*](how-to-manage-model.md#upload-models)bölümünde açıklandığı gibi [Azure dijital TWINS API 'leri](how-to-use-apis-sdks.md)kullanılarak yapılır.

Ancak, karşıya yüklenecek çok modelleriniz varsa veya tek tek karşıya yükleme işlemlerini izleyen çok sayıda bağımlılıklar varsa, aynı anda birçok modeli karşıya yüklemek için bu örneği kullanabilirsiniz: [**Azure Digital TWINS model yükleyicisi**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Bu projeyi yapılandırmak ve kullanmak için örnek ile birlikte sunulan yönergeleri izleyin.

### <a name="model-visualizer"></a>Model görselleştiricisi 

_**Modelleri görselleştirme için**_

Azure dijital TWINS örneğinizdeki modelleri karşıya yükledikten sonra, Azure Digital TWINS [**model görselleştiricisi**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)' nı kullanarak tüm devralma ve model ilişkileri dahil olmak üzere Azure dijital TWINS örneğiniz içindeki modelleri görüntüleyebilirsiniz. Bu örnek şu anda bir taslak durumundadır. Dijital TWINS geliştirme Community 'nin örneğe genişletmesini ve katkıda bulunmasını öneririz. 

## <a name="next-steps"></a>Sonraki adımlar

* Endüstri standardı ontolotem 'lar temelinde modeller oluşturma hakkında bilgi edinin: [ *ontology nedir?*](concepts-ontologies.md)

* API işlemlerinde modelleri yönetmeye daha derin bakış: [ *nasıl yapılır: dtdl modellerini yönetme*](how-to-manage-model.md)

* Modellerin dijital TWINS oluşturmak için nasıl kullanıldığı hakkında bilgi edinin: [ *Kavramlar: dijital TWINS ve ikizi Graf*](concepts-twins-graph.md)

