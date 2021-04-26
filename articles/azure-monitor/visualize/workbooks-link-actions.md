---
title: Azure Izleyici çalışma kitapları bağlantı eylemleri
description: Azure Izleyici çalışma kitaplarında bağlantı eylemlerini kullanma
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100622879"
---
# <a name="link-actions"></a>Bağlantı eylemleri

Bağlantı eylemlerine, çalışma kitabı bağlantı adımları veya [ızgaralar](../visualize/workbooks-grid-visualizations.md), [başlıklar](../visualize/workbooks-tile-visualizations.md)ya da [grafiklerin](../visualize/workbooks-graph-visualizations.md)sütun ayarları aracılığıyla erişilebilir.

## <a name="general-link-actions"></a>Genel bağlantı eylemleri

| Bağlantı eylemi | Tıklama eylemi |
|:------------- |:-------------|
| `Generic Details` | Bir özellik Kılavuzu bağlam görünümündeki satır değerlerini gösterir. |
| `Cell Details` | Bir özellik Kılavuzu bağlam görünümündeki hücre değerini gösterir. Hücre (örneğin, konum, rol örneği vs. gibi istek özelliklerine sahip JSON) içeren dinamik bir tür içerdiğinde yararlı olur. |
| `Url` | Hücrenin değerinin geçerli bir http url 'si olması beklenir ve hücre, bu URL 'yi yeni bir sekmede açan bir bağlantı olacaktır.|

## <a name="application-insights"></a>Application Insights

| Bağlantı eylemi | Tıklama eylemi |
|:------------- |:-------------|
| `Custom Event Details` | Application Insights arama ayrıntılarını hücrede özel olay KIMLIĞI () ile açar `itemId` . |
| `* Details` | Bağımlılıklar, özel durumlar, sayfa görünümleri, istekler ve izlemeler dışında özel olay ayrıntılarına benzer. |
| `Custom Event User Flows` | Hücredeki özel olay adında özetlenen Application Insights Kullanıcı Akışları deneyimini açar. |
| `* User Flows` | Özel durum, sayfa görünümleri ve istekler hariç özel olay Kullanıcı Akışları benzer. |
| `User Timeline` | Kullanıcı zaman çizelgesini hücrede Kullanıcı KIMLIĞI () ile açar `user_Id` . |
| `Session Timeline` | Hücredeki değer için Application Insights arama deneyimini açar (örneğin, ABC hücresindeki değer olan ' abc ' metnini arayın). |

`*` Yukarıdaki tablo için bir joker karakter belirtir

## <a name="azure-resource"></a>Azure kaynağı

| Bağlantı eylemi | Tıklama eylemi |
|:------------- |:-------------|
| `ARM Deployment` | Azure Resource Manager şablonu dağıtın.  Bu öğe seçildiğinde, yazarın hangi Azure Resource Manager şablonu açmasını, şablon parametrelerini, vb. [Azure Resource Manager dağıtım bağlantı ayarlarını görmesini](#azure-resource-manager-deployment-link-settings)sağlamak için ek alanlar görüntülenir. |
| `Create Alert Rule` | Bir kaynak için bir uyarı kuralı oluşturur.  |
| `Custom View` | Özel bir görünüm açar. Bu öğe seçildiğinde, yazarın görünüm uzantısını, görünüm adını ve görünümü açmak için kullanılan parametreleri yapılandırmasına izin vermek için ek alanlar görüntülenir. [Bkz. özel görünüm](#custom-view-link-settings). |
| `Metrics` | Ölçüm görünümünü açar.  |
| `Resource overview` | Kaynaktaki kaynak KIMLIĞI değerine bağlı olarak, portalda kaynağın görünümünü açın. Yazar ayrıca, isteğe bağlı olarak `submenu` kaynak görünümünde belirli bir menü öğesini açacak bir değer ayarlayabilir. |
| `Workbook (template)` | Çalışma kitabı şablonu açın.  Bu öğe seçildiğinde, yazarın açılacak şablonu, vb. yapılandırmasına izin vermek için ek alanlar görüntülenir.  |

## <a name="link-settings"></a>Bağlantı ayarları

Bağlantı oluşturucuyu kullanırken aşağıdaki ayarlar kullanılabilir:

![Bağlantı ayarlarının ekran görüntüsü](./media/workbooks-link-actions/link-settings.png)

| Ayar | Açıklama |
|:------------- |:-------------|
| `View to open` | Yazarın yukarıda numaralandırılan eylemlerden birini seçmesine izin verir. |
| `Menu item` | "Kaynağa genel bakış" seçilirse, bu, kaynağın genel bakış alanındaki menü öğesidir. Bu, kaynak için "genel bakış" yerine uyarıları veya etkinlik günlüklerini açmak için kullanılabilir. Menü öğesi değerleri her Azure için farklıdır `Resourcetype` .|
| `Link label` | Belirtilmişse, bu değer kılavuz sütununda görüntülenir. Bu değer belirtilmemişse, hücrenin değeri görüntülenecektir. Bir ısı haritasını veya Icon gibi başka bir değer görüntülenmesini istiyorsanız, `Link` oluşturucuyu kullanmayın, bunun yerine uygun oluşturucuyu kullanın ve `Make this item a link` seçeneğini belirleyin. |
| `Open link in Context Blade` | Belirtilmişse, bağlantı tam görünüm olarak açmak yerine pencerenin sağ tarafında bir açılan pencere "bağlam" görünümü olarak açılır. |

`Make this item a link`Seçeneği kullanılırken, aşağıdaki ayarlar kullanılabilir:

| Ayar | Açıklama |
|:------------- |:-------------|
| `Link value comes from` | Bir hücreyi bağlantı içeren bir Oluşturucu olarak görüntülerken, bu alan, bağlantıda kullanılan "bağlantı" değerinin nereden geldiğini belirtir, yazarın kılavuzdaki diğer sütunların bir açılan listesinden seçim yapmasına izin verir. Örneğin, hücre bir ısı haritasını değeri olabilir, ancak bağlantının satırdaki kaynak kimliği için kaynak genel bakışını açmasını istiyorsunuz. Bu durumda, alandan gelmesi için bağlantı değerini ayarlarsınız `Resource Id` .
| `View to open` | Yukarıdaki gibi. |
| `Menu item` | Yukarıdaki gibi. |
| `Open link in Context Blade` | Yukarıdaki gibi. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure Resource Manager dağıtım bağlantısı ayarları

Seçili bağlantı türü ise `ARM Deployment` yazarın bir Azure Resource Manager dağıtımı açmak için ek ayarlar belirtmesi gerekir. Yapılandırmalara yönelik iki ana sekme vardır.

### <a name="template-settings"></a>Şablon ayarları

Bu bölüm, şablonun nereden gelmesi gerektiğini ve Azure Resource Manager dağıtımını çalıştırmak için kullanılan parametreleri tanımlar.

| Kaynak | Açıklama |
|:------------- |:-------------|
|`Resource group id comes from` | Dağıtılan kaynakları yönetmek için kaynak KIMLIĞI kullanılır. Abonelik, dağıtılan kaynakları ve maliyetleri yönetmek için kullanılır. Kaynak grupları, tüm kaynaklarınızı düzenlemek ve yönetmek için klasörler gibi kullanılır. Bu değer belirtilmezse, dağıtım başarısız olur. Bağlantı kaynaklarında,, veya arasından seçim `Cell` yapın `Column` `Static Value` `Parameter` . [](#link-sources)|
|`ARM template URI from` | Azure Resource Manager şablonun kendisi için URI. Şablonu dağıtan kullanıcılar için şablon URI 'sine erişilebilir olması gerekir. Bağlantı kaynaklarında,, veya arasından seçim `Cell` yapın `Column` `Parameter` `Static Value` . [](#link-sources) Başlangıçlar için [hızlı başlangıç Şablonlarımıza](https://azure.microsoft.com/resources/templates/)göz atın.|
|`ARM Template Parameters` | Bu bölüm, yukarıda tanımlanan şablon URI 'SI için kullanılan şablon parametrelerini tanımlar. Bu parametreler, çalışma sayfasında şablonu dağıtmak için kullanılacaktır. Kılavuz, parametreleri şablon URI 'sinde tanımlanan adları kullanarak doldurmaya ve statik boş değerlere ayarlamaya yardımcı olması için bir genişletme araç çubuğu düğmesi içerir. Bu seçenek yalnızca kılavuzda parametre olmadığında ve şablon URI 'SI ayarlanmışsa kullanılabilir. Alt bölüm, parametre çıktısının nasıl görüneceğine ilişkin bir önizlemedir. Önizlemeyi geçerli değişikliklerle güncelleştirmek için Yenile ' yi seçin. Parametreler genellikle değerlerdir, ancak başvurular kullanıcının erişimi olan Anahtar Kasası gizli dizilerini işaret edebilen bir şeydir. <br/><br/> **Şablon Görüntüleyicisi dikey pencere sınırlaması** -başvuru parametrelerini doğru işlemez ve null/değer olarak görünür, böylece kullanıcılar şablon Görüntüleyicisi sekmesinden başvuru parametrelerini doğru şekilde dağıtamaz.|

![Azure Resource Manager şablonu ayarlarının ekran görüntüsü](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX ayarları

Bu bölüm, kullanıcıların Azure Resource Manager dağıtımını çalıştırmadan önce neleri görebileceğini yapılandırır.

| Kaynak | Açıklama |
|:------------- |:-------------|
|`Title from` | Çalışma görünümünde kullanılan başlık. Bağlantı kaynaklarında,, veya arasından seçim `Cell` yapın `Column` `Parameter` `Static Value` . [](#link-sources)|
|`Description from` | Bu, kullanıcılara şablonu dağıtmak istediklerinde faydalı bir açıklama sağlamak için kullanılan markın metindir. Bağlantı kaynaklarında,, veya arasından seçim `Cell` yapın `Column` `Parameter` `Static Value` . [](#link-sources) <br/><br/> **Note:** `Static Value` Seçilirse, çok satırlı bir metin kutusu görünür. Bu metin kutusunda, kullanarak parametreleri çözebilirsiniz `{paramName}` . Ayrıca, sütun adından sonra da ekleyerek sütunları parametre olarak ele alabilirsiniz `_column` `{columnName_column}` . Aşağıdaki örnek görüntüde yazarak sütuna başvuracağız `VMName` `{VMName_column}` . İki nokta üst üste işaretinden sonraki değer [parametre biçimlendiricidir](../visualize/workbooks-parameters.md#parameter-options), bu durumda `value` .|
|`Run button text from` | Azure Resource Manager şablonunu dağıtmak için Çalıştır (Yürüt) düğmesinde kullanılan etiket. Bu, kullanıcıların Azure Resource Manager şablonunu dağıtmaya başlamak için seçim yapabilecekleri şeydir.|

![Azure Resource Manager UX ayarlarının ekran görüntüsü](./media/workbooks-link-actions/ux-settings.png)

Bu yapılandırmaların ayarlanmalarındaki kullanıcılar bağlantıyı seçtiğinden, UX ayarlarında açıklanan UX ile görünümü açar. Kullanıcı seçerse, `Run button text from` [şablon ayarlarından](#template-settings)değerleri kullanarak bir Azure Resource Manager şablonu dağıtır. Görünüm şablonu, Kullanıcı tarafından dağıtılmadan önce şablonu ve parametreleri incelemek için şablon Görüntüleyici sekmesini açar.

![Çalışma Azure Resource Manager görünümünün ekran görüntüsü](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Özel Görünüm bağlantısı ayarları

Azure portal özel görünümleri açmak için bunu kullanın. Tüm yapılandırma ve ayarları doğrulayın. Hatalı değerler portalda hatalara neden olur veya görünümleri doğru bir şekilde açamaz. Veya kullanarak ayarları yapılandırmanın iki yolu vardır `Form` `URL` .

> [!NOTE]
> Bir menü içeren görünümler bağlam sekmesinde açılamaz. Bir menü içeren bir görünüm bağlam sekmesinde açılacak şekilde yapılandırıldıysa, bağlantı seçildiğinde bağlam sekmesi gösterilmez.

### <a name="form"></a>Form

| Kaynak | Açıklama |
|:------------- |:-------------|
|`Extension name` | Görünümün adını barındıran uzantının adı.|
|`View name` | Açılacak görünümün adı.|

#### <a name="view-inputs"></a>Girişleri görüntüle

İki tür giriş, kılavuz ve JSON vardır. `Grid`Basit anahtar ve değer sekmesi girişleri için kullanın veya `JSON` iç içe geçmış bir JSON girişi belirtmeyi seçin.

- Kılavuz
    - `Parameter Name`: View Input parametresinin adı.
    - `Parameter Comes From`: Görünüm parametresinin değeri nereden gelmelidir. Bağlantı kaynaklarında,, veya arasından seçim `Cell` yapın `Column` `Parameter` `Static Value` . [](#link-sources)
    > [!NOTE]
    > `Static Value`Seçilirse, parametreler metin kutusunda parantez bağlantısı kullanılarak çözülebilir `{paramName}` . Sütunlar `_column` , gibi sütun adından sonra eklenerek parametre sütunları olarak kabul edilebilir `{columnName_column}` .

    - `Parameter Value`: öğesine bağlı olarak `Parameter Comes From` , kullanılabilir parametrelerin, sütunların veya statik bir değerin bir açılan listesi olacaktır.

    ![Sütun düzenleme ayarının ekran görüntüsü, formdan özel görünüm ayarlarını gösterir.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Sekme girişinizi düzenleyicide JSON biçiminde belirtin. `Grid`Mod, parametreler ve sütunlara benzer şekilde `{paramName}` Parametreler için ve sütunlar için kullanılarak başvurulabilir `{columnName_column}` . Seçilirse `Show JSON Sample` , görüntüleme girişi için tüm çözümlenen parametrelerin ve sütunların Kullanıcı tarafından beklenen çıktıyı gösterir.

    ![JSON üzerinde görünüm girişi ile açık özel görünüm ayarlarını gösteren ekran görüntüsü.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Uzantıyı, görünümün adını ve görünümü açmak için gereken tüm girdileri içeren bir portal URL 'SI yapıştırın. ' İ seçtikten sonra, `Initialize Settings` `Form` Görünüm girdilerini ekleme/değiştirme/kaldırma için yazar için ' i dolduracaktır.

![Sütun ayarını gösteren ekran görüntüsü URL 'den özel görünüm ayarlarını gösterir. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Çalışma kitabı (şablon) bağlantısı ayarları

Seçili bağlantı türü ise `Workbook (Template)` , yazarın doğru çalışma kitabı şablonunu açmak için ek ayarlar belirtmesi gerekir. Aşağıdaki ayarlar, kılavuzun her bir ayar için uygun değeri nasıl bulabileceğinizi gösteren seçeneklere sahiptir.

| Ayar | Açıklama |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Bu, çalışma kitabına "sahip" olan Azure kaynağının kaynak KIMLIĞIDIR. Genellikle bir Application Insights kaynağıdır veya bir Log Analytics çalışma alanıdır. Azure Izleyici içinde bu aynı zamanda sabit dize olabilir `"Azure Monitor"` . Çalışma kitabı kaydedildiğinde, bu çalışma kitabının bağlanacağı şeydir. |
| `Workbook resources` | Çalışma kitabında kullanılan varsayılan kaynağı belirten bir dizi Azure kaynak kimliği. Örneğin, açılan şablon sanal makine ölçümlerini gösteriyorsa, buradaki değerler sanal makine kaynak kimlikleri olacaktır.  Birçok kez, sahip ve kaynakları aynı ayarlara ayarlanır. |
| `Template Id` | Açılacak şablonun KIMLIĞINI belirtin. Bu, galerideki bir topluluk Şablonum (en yaygın durum) ise, şablon için olduğu gibi, ile şablonun yolunu önek olarak ekleyin `Community-` `Community-Workbooks/Performance/Apdex` `Workbooks/Performance/Apdex` . Bu, kaydedilmiş bir çalışma kitabına/şablona yönelik bir bağlantıdır, bu öğenin tam Azure Kaynak KIMLIĞIDIR. |
| `Workbook Type` | Açılacak çalışma kitabı şablonu türünü belirtin. En yaygın durumlar, `Default` `Workbook` geçerli çalışma kitabındaki değeri kullanmak için veya seçeneğini kullanır. |
| `Gallery Type` | Bu, açılan şablonun "Galeri" görünümünde görüntülenecek Galeri türünü belirtir. En yaygın durumlar, `Default` `Workbook` geçerli çalışma kitabındaki değeri kullanmak için veya seçeneğini kullanır. |
|`Location comes from` | Belirli bir çalışma kitabı kaynağını açarsanız konum alanı belirtilmelidir. Konum belirtilmezse, çalışma kitabı içeriğini bulma çok daha yavaştır. Konumu biliyorsanız, belirtin. Konumu bilmiyor veya belirli bir konum olmadan bir şablonu açmadıysanız, bu alanı "varsayılan" olarak bırakın.|
|`Pass specific parameters to template` | Şablona belirli parametreleri geçirmek için seçin. Seçilirse, yalnızca belirtilen parametreler şablona geçirilir, geçerli çalışma kitabındaki tüm parametreler şablona geçirilir ve bu durumda, bu parametre değerinin çalışması için her iki çalışma kitabında parametre *adlarının* aynı olması gerekir.|
|`Workbook Template Parameters` | Bu bölüm, hedef şablona geçirilen parametreleri tanımlar. Ad, hedef şablondaki parametrenin adıyla eşleşmelidir. `Cell`,, `Column` `Parameter` , Ve değerlerini seçin `Static Value` . Bu parametreyi hedef şablona geçirmek için ad ve değer boş olmamalıdır.|

Yukarıdaki ayarların her biri için, yazarın bağlantılı çalışma kitabındaki değerin nereden geldiği ' i seçmesini gerekir. Bkz. [bağlantı kaynakları](#link-sources)

Çalışma kitabı bağlantısı açıldığında, yeni çalışma kitabı görünümü yukarıdaki ayarlardan yapılandırılan tüm değerleri geçirilir.

![Çalışma kitabı bağlantı ayarlarının ekran görüntüsü](./media/workbooks-link-actions/workbook-link-settings.png)

![Çalışma kitabı şablon parametreleri ayarlarının ekran görüntüsü](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Kaynakları bağla

| Kaynak | Açıklama |
|:------------- |:-------------|
| `Cell` | Bu, bağlantı değeri olarak kılavuzdaki Bu hücredeki değeri kullanır |
| `Column` | Seçildiğinde, yazarın kılavuzda başka bir sütun seçmesini sağlamak için başka bir alan görüntülenir.  Satır için bu sütunun değeri bağlantı değerinde kullanılacaktır. Bu, bir kılavuzun her satırının farklı bir şablon açmasını, alanın `Template Id` olarak ayarlanmasına `column` veya `Workbook resources` BIR Azure kaynak kimliği içeren bir sütuna ayarlandıysa farklı kaynaklar için aynı çalışma kitabı şablonunu açmasına olanak tanımak için kullanılır. |
| `Parameter` | Seçildiğinde, yazarın bir parametre seçmesini sağlamak için başka bir alan görüntülenir. Bu parametrenin değeri, bağlantı tıklandığında değer için kullanılacaktır |
| `Static value` | Seçildiğinde, yazarın bağlantılı çalışma kitabında kullanılacak statik bir değer içinde olmasını sağlamak için başka bir alan görüntülenir. Bu, kılavuzdaki tüm satırların bir alan için aynı değeri kullanacağı zaman yaygın olarak kullanılır. |
| `Step` | Çalışma kitabının geçerli adımında ayarlanan değeri kullanın. Bu, bağlantılı çalışma kitabındaki çalışma kitabı kaynaklarını geçerli çalışma kitabında değil *sorgu/ölçümler adımında* kullanılanlarla ayarlamaya yönelik sorgu ve ölçüm adımlarında yaygındır |
| `Workbook` | Geçerli çalışma kitabında ayarlanan değeri kullanın. |
| `Default` | Hiçbir değer belirtilmemişse kullanılacak varsayılan değeri kullanın. Bu, varsayılan galerinin sahip kaynağın türüne göre ayarlandığı Galeri türü için ortaktır |

## <a name="next-steps"></a>Sonraki adımlar

- Çalışma kitabı kaynaklarınıza erişimi [denetleme](../visualize/workbooks-access-control.md) ve paylaşma.
- [Çalışma kitaplarında grupları](../visualize/workbooks-groups.md)nasıl kullanacağınızı öğrenin.