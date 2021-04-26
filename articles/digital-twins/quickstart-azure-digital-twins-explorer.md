---
title: Hızlı Başlangıç-örnek senaryoyu araştırma
titleSuffix: Azure Digital Twins
description: Hızlı başlangıç-önceden oluşturulmuş bir senaryoyu görselleştirmek ve araştırmak için Azure Digital TWINS gezgin örneğini kullanın.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: adafbdb903c9ba827e8ccf56890c69ee2ce4eaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495273"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>Hızlı başlangıç-Azure Digital TWINS gezginini kullanarak örnek bir Azure dijital TWINS senaryosunu araştırma

Azure dijital TWINS sayesinde gerçek ortamlarınızın canlı modellerini oluşturabilir ve bunlarla etkileşim kurabilirsiniz. İlk olarak, tek tek öğeleri **dijital TWINS** olarak modelleyebilirsiniz. Ardından, canlı olaylara yanıt verebilecek ve bilgi için sorgulanabilen bir bilgi **grafiğine** bağlanırsınız.

Bu hızlı başlangıçta, [Azure Digital TWINS Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)adlı örnek bir uygulamanın yardımıyla önceden oluşturulmuş bir Azure dijital TWINS grafiğini araştıracağız. Azure dijital TWINS gezginini kullanarak şunları yapabilirsiniz:

- Bir ortamın dijital temsilini karşıya yükleyin.
- Azure dijital TWINS 'de ortamı temsil etmek üzere oluşturulan TWINS ve grafiğin görsel görüntülerini görüntüleyin.
- Tarayıcı tabanlı, görsel deneyimle diğer yönetim etkinliklerini gerçekleştirin.

Hızlı başlangıç aşağıdaki önemli adımları içerir:

1. Azure dijital TWINS örneği ve Azure dijital TWINS Gezginini ayarlayın.
1. Örnek senaryoyu oluşturmak için önceden oluşturulmuş modelleri ve grafik verilerini karşıya yükleyin.
1. Oluşturulan senaryo grafiğini gezin.
1. Grafikte değişiklikler yapın.

İle çalıştığınız örnek grafik, iki kattan ve iki odaya sahip bir bina temsil eder. Grafik şu resme benzer şekilde görünür:

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Oklar ile bağlanmış dört dairesel düğüm tarafından oluşturulan grafiğin görünümü. ' Floor1 ' etiketli bir daire, ' Contains ' etiketli bir okla ' room1 ' etiketli bir daireye bağlanır. ' Floor0 ' etiketli bir daire, ' Contains ' etiketli bir okla ' Room0 ' etiketli bir daireye bağlanır. ' Floor1 ' ve ' Floor0 ' bağlı değil.":::

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlayabilmeniz için bir Azure aboneliğine sahip olmanız gerekir. Henüz bir tane yoksa, şimdi [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Ayrıca makinenizde **Node.js** gerekir. En son sürümü almak için bkz. [Node.js](https://nodejs.org/).

Son olarak, hızlı başlangıç sırasında kullanmak üzere örneği de indirmeniz gerekir. Örnek uygulama, **Azure dijital TWINS Gezgini**' dir. Bu örnek, hızlı başlangıçta bir Azure dijital TWINS senaryosu yüklemek ve araştırmak için kullandığınız uygulamayı içerir. Örnek senaryo dosyalarını da içerir. Örneği almak için [Azure Digital Twins Gezgini](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)' ne gidin. Başlığın altındaki **kodu görüntüle** düğmesini seçin, bu, sizi örnek için GitHub deposuna götürür. Örnek olarak indirmek için **kod** düğmesini SEÇIN ve **ZIP 'i indirin** *. ZIP* dosyası. 

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png" alt-text="GitHub 'da dijital-TWINS-Explorer deposunun ekran görüntüsü. Kod düğmesi seçilidir ve posta Indirme düğmesinin vurgulandığı küçük bir iletişim kutusu oluşturulur." lightbox="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png":::

**digital-twins-explorer-main.zip** klasörünü sıkıştırmayı açın ve dosyaları ayıklayın.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Azure dijital TWINS ve Azure dijital TWINS Gezginini ayarlama

Azure dijital TWINS ile çalışmanın ilk adımı, bir Azure dijital TWINS örneği kurmak için kullanılır. Hizmetin bir örneğini oluşturduktan ve kimlik bilgilerinizi Azure Digital TWINS Explorer ile kimlik doğrulaması yapacak şekilde ayarladıktan sonra, Azure Digital TWINS Explorer 'da örneğe bağlanabilir ve hızlı başlangıçta örnek verilerle doldurabilirsiniz.

Bu bölümün geri kalanında bu adımlarda adım adım gösterilmektedir.

### <a name="set-up-an-azure-digital-twins-instance"></a>Azure dijital TWINS örneği ayarlama

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Yerel Azure kimlik bilgilerini ayarlama

Azure dijital TWINS Explorer uygulaması, [](/dotnet/api/azure.identity.defaultazurecredential) `Azure.Identity` Yerel makinenizde çalıştırdığınızda Azure dijital TWINS örneğiyle kullanıcıların kimliğini doğrulamak için DefaultAzureCredential (kitaplığın bir parçası) kullanır. İstemci uygulamasının Azure dijital TWINS ile kimlik doğrulaması yapabilme yolları hakkında daha fazla bilgi için bkz. [uygulama kimlik doğrulama kodu yazma](how-to-authenticate-client.md).

Bu tür bir kimlik doğrulamasıyla Azure dijital TWINS Gezgini, yerel ortamınızda, yerel bir [Azure CLI](/cli/azure/install-azure-cli) veya Visual Studio veya Visual Studio Code Içindeki bir Azure oturum açma gibi kimlik bilgilerini arar. Bu nedenle, Azure Digital TWINS gezgin uygulamasının kimlik bilgilerini ayarlamak için bu mekanizmalardan biri aracılığıyla **yerel olarak Azure 'da oturum açmanız** gerekir.

Bu yollarla Azure 'da zaten oturum açtıysanız bir [sonraki bölüme](#run-and-configure-azure-digital-twins-explorer)atlayabilirsiniz.

Aksi takdirde, yerel Azure CLı 'yi şu adımlarla yükleyebilirsiniz:

1. İşletim sistemi ile eşleşen yüklemeyi tamamlamaya yönelik [Bu yükleme bağlantısındaki](/cli/azure/install-azure-cli) işlemi izleyin.
1. Makinenizde bir konsol penceresi açın.
1. `az login`' İ çalıştırın ve Azure hesabınızda oturum açmak için kimlik doğrulama istemlerini izleyin.
1. Olası son adım: Bu hesap altında birden çok Azure aboneliği kullanıyorsanız, kimlik doğrulama bağlamını, çalıştırarak Azure dijital TWINS örneğinizi içeren Azure aboneliğine ayarlayın `az account set --subscription "<your-subscription-name-or-ID>"` (aboneliğin adı veya kimlik değeri çalışacaktır).

Oturum açtıktan sonra, Azure Digital TWINS Explorer, sonraki bölümde çalıştırdığınızda Azure kimlik bilgilerinizi otomatik olarak göstermelidir.

İsterseniz, kimlik doğrulama konsolu penceresini kapatabilirsiniz. Ya da bir sonraki adımda kullanmak üzere açık tutabilirsiniz.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Azure dijital TWINS Gezginini çalıştırma ve yapılandırma

Ardından, Azure Digital TWINS gezgin uygulamasını çalıştırın ve Azure dijital TWINS örneğiniz için yapılandırın.

1. İndirilen ve zip **dışı dijital-TWINS-Explorer-ana** klasörüne gidin.
Bir konsol penceresini, **dijital-TWINS-Explorer-Main/Client/src** klasör konumu üzerinde açın.

1. `npm install`Tüm gerekli bağımlılıkları indirmek için ' i çalıştırın.

1. Uygulamayı çalıştırarak başlatın `npm run start` .

   Birkaç saniye sonra, bir tarayıcı penceresi açılır ve uygulama tarayıcıda görüntülenir.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Localhost: 3000 konumunda çalışan bir uygulamayı gösteren tarayıcı penceresi. Uygulamaya Azure dijital TWINS Gezgini adı verilir ve sorgu Gezgini, model görünümü, grafik görünümü ve Özellik Gezgini için kutular bulunur. Henüz ekran verisi yok." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

1. Aşağıdaki görüntüde gösterildiği gibi pencerenin sağ üst köşesindeki **oturum aç** düğmesini seçin ve Azure Digital TWINS Gezginini ayarladığınız örnekle çalışacak şekilde yapılandırın.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/sign-in.png" alt-text="Pencerenin üst kısmındaki oturum açma simgesini vurgulayan Azure dijital TWINS Gezgini. Simge, bir anahtarın silueti içeren bir kişinin basit silueti gösterir." lightbox="media/quickstart-azure-digital-twins-explorer/sign-in.png":::

1. [Azure dijital TWINS örneği ayarlama](#set-up-an-azure-digital-twins-instance) bölümünde daha önce topladığınız Azure dijital TWINS örneği URL 'sini, *https://<Instance-host-Name>* biçiminde girin.

> [!TIP]
> Bağlandığınızda bir `SignalRService.subscribe` hata iletisi görünürse, Azure Digital TWINS URL 'nizin *https://* ile başladığından emin olun.
>
> Bir kimlik doğrulama hatası görünürse, Azure dijital TWINS için geçerli olduğundan emin olmak üzere **ortam değişkenlerinizi** kontrol etmek isteyebilirsiniz. `DefaultAzureCredential`Kimlik bilgileri türlerinde [belirli bir sırada](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)kimlik doğrulaması yapmaya çalışır ve ortam değişkenleri önce değerlendirilir.

Microsoft 'tan bir **izin** iste açılan pencere görürseniz, bu uygulamaya izin verin ve devam etmek için kabul edin.

>[!NOTE]
> Aynı simgeyi seçerek, **oturum açma** kutusunu yeniden açmak için bu bilgileri dilediğiniz zaman tekrar ziyaret edebilir veya düzenleyebilirsiniz. Bu, geçirilen değerleri tutacak.

## <a name="add-the-sample-data"></a>Örnek verileri ekleme

Ardından, örnek senaryoyu ve grafiği Azure Digital TWINS Explorer 'a aktarırsınız. Örnek senaryo, daha önce indirdiğiniz **Digital-Twins-Explorer-ana** klasöründe de bulunur.

### <a name="models"></a>Modeller

Bir Azure dijital TWINS çözümünün ilk adımı, ortamınız için sözlük tanımlamaktır. Ortamınızda var olan varlık türlerini tanımlayan özel [modeller](concepts-models.md) oluşturacaksınız.

Her model JSON-LD gibi dijital Ikizi tanım dili (DTDL) olarak adlandırılan bir dilde yazılır. Her model, **özellikleri**, **telemetri**, **ilişkiler** ve **bileşenleri** bakımından tek bir varlık türünü tanımlar. Daha sonra bu modelleri, bu türlerin belirli örneklerini temsil eden dijital TWINS 'in temeli olarak kullanacaksınız.

Genellikle, bir model oluşturduğunuzda üç adımı tamamlayacaksınız:

1. Model tanımını yazın. Hızlı başlangıçta, bu adım örnek çözümün bir parçası olarak zaten yapılır.
1. Sözdiziminin doğru olduğundan emin olmak için bunu doğrulayın. Hızlı başlangıçta, bu adım örnek çözümün bir parçası olarak zaten yapılır.
1. Azure dijital TWINS örneğinizi karşıya yükleyin.
 
Bu hızlı başlangıç için model dosyaları zaten yazıldığı ve sizin için doğrulanan. İndirdiğiniz çözüme dahil edilmiştir. Bu bölümde, derleme ortamının bu bileşenlerini tanımlamak için örneğinizle iki adet önceden yazılmış modeli yükleyeceksiniz:

* Floor
* Yı

#### <a name="upload-models"></a>Modelleri karşıya yükle

Modelleri karşıya yüklemek için bu adımları izleyin.

1. **Model görünümü** kutusunda **bir model yükle** simgesini seçin.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="Model görünümü kutusunda ortadaki simge vurgulanır. Bir buluta işaret eden bir ok gösterir." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. Görüntülenen dosya Seçicisi kutusunda, indirilen depodaki **Digital-TWINS-Explorer-Main/Client/örnekler** klasörüne gidin.
1. **ÜzerindeRoom.js** seçin ve **üzerindeFloor.js** ve **Tamam**' ı seçin. İsterseniz ek modeller yükleyebilirsiniz, ancak bu hızlı başlangıçta kullanılmaz.
1. Azure hesabınızda oturum açmanızı isteyen açılır iletişim kutusunu izleyin.

>[!NOTE]
>Şu hata iletisini görürseniz: :::image type="content" source="media/quickstart-azure-digital-twins-explorer/error-models-popup.png" alt-text="bir açılan kutu okuma ' hatası: modeller getirilirken hata: ClientAuthError: açılır pencere açılırken hata oluştu. Bu, IE kullanıyorsanız veya tarayıcıda açılan pencereler engelleniyorsa gerçekleşebilir. ' alt kısımdaki bir kapatma düğmesi." border="false"::: 
> Açılır pencere engelleyicinizi devre dışı bırakmayı veya farklı bir tarayıcı kullanmayı deneyin.

Azure dijital TWINS Gezgini artık bu model dosyalarını Azure dijital TWINS örneğinize yükler. **Model görünümü** kutusunda gösterilmesi ve kolay adlarını ve tam model kimliklerini görüntülemesi gerekir. Modeldeki DTDL kodunu görmek için **model bilgilerini görüntüle** simgelerini seçebilirsiniz.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="Model görünümü kutusunun, Floor (dtmı: örnek: Floor; 1) ve Oda (dtmı: örnek: Oda; 1) içinde listelendiği iki model tanımına sahip bir görünümü. Bir daire içinde bir harf ' ı ' gösteren model bilgilerini görüntüle simgesi her model için vurgulanır." lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>TWINS ve ikizi grafiği

Artık bazı modeller Azure dijital TWINS örneğinizi karşıya yüklediğinize göre, model tanımlarını izleyen [dijital TWINS](concepts-twins-graph.md) 'leri ekleyebilirsiniz.

Dijital TWINS, iş ortamınızdaki gerçek varlıkları temsil eder. Bu kişiler, bir gruptaki sensörler, bir otomobil ışıkları ya da bu hızlı başlangıçta bir yapı katında odalar gibi şeyler olabilir. Herhangi bir belirli model türünün, tüm *Oda* modelini kullanan birden fazla oda gibi birçok sayıda TWINS oluşturabilirsiniz. Bu ilişkileri, tam ortamı temsil eden bir **ikizi grafiğine** bağlayabilirsiniz.

Bu bölümde, önceden düzenlenen bir grafiğe bağlı olan önceden oluşturulan TWINS 'i karşıya yükleyeceksiniz. Grafik iki kat ve iki oda içerir ve aşağıdaki düzende bağlanır:

* Floor0
    - Room0 içerir
* Floor1
    - Room1 içerir

#### <a name="import-the-graph"></a>Grafiği içeri aktarma

Grafiği içeri aktarmak için aşağıdaki adımları izleyin.

1. **GRAFIK görünümü** kutusunda, **grafiği içeri aktar** simgesini seçin.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="Grafik görünümü kutusunda bir simge vurgulanır. Bir buluta işaret eden bir ok gösterir." lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. Dosya Seçicisi kutusunda, **Digital-TWINS-Explorer-Main/Client/örnekler** klasörüne gidin ve **buildingScenario.xlsx** elektronik tablo dosyasını seçin. Bu dosya, örnek grafiğin bir açıklamasını içerir. **Tamam**’ı seçin.

   Birkaç saniye sonra Azure Digital TWINS Explorer, yüklenecek Grafiğin önizlemesini gösteren bir **Içeri aktarma** görünümü açar.

3. Grafik karşıya yüklemeyi onaylamak için **GRAFIK görünümü** kutusunun sağ üst köşesindeki **Kaydet** simgesini seçin.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="Grafik Önizleme bölmesindeki Kaydet simgesini vurgulama." lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure dijital TWINS Gezgini artık, istenen TWINS ve aralarında ilişkiler oluşturmak için karşıya yüklenen dosyayı kullanır. Bittiğinde bir iletişim kutusu görüntülenir. **Kapat**’ı seçin.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="Graph içeri aktarma başarısını belirten bir iletişim kutusu. ' Içeri aktarma başarılı ' i okur. 4 TWINS içeri aktarıldı. 2 ilişki içeri aktarıldı. '" lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Grafik artık Azure Digital TWINS Explorer 'a yüklendi. Grafiği görmek için, Azure Digital TWINS gezgin penceresinin üst kısmındaki **GRAFIK Gezgini** kutusunda **Sorguyu Çalıştır** düğmesini seçin.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="Pencerenin sağ üst köşesindeki Sorguyu Çalıştır düğmesi vurgulanır." lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

Bu eylem, tüm dijital TWINS 'i seçmek ve göstermek için varsayılan sorguyu çalıştırır. Azure dijital TWINS Gezgini, tüm TWINS ve ilişkileri hizmetten alır. Grafik **görünümü** kutusunda kendileri tarafından tanımlanan grafiği çizer.

## <a name="explore-the-graph"></a>Grafiği keşfet

Şimdi örnek senaryonun karşıya yüklenen grafiğini görebilirsiniz.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="İçindeki bir ikizi Graf ile grafik görünümü kutusunun görünümü. ' Floor1 ' etiketli bir daire, ' Contains ' etiketli bir okla ' room1. ' etiketli bir daireye bağlı. ' Floor0 ' etiketli bir daire, ' Contains ' etiketli bir okla ' room0. ' etiketli bir daireye bağlı.":::

Daireler (Graf "düğümler") dijital TWINS 'i temsil eder. Satırlar ilişkileri temsil eder. **Floor0** Ikizi, **Room0** Içerir ve **Floor1** ikizi **room1** içerir.

Bir fare kullanıyorsanız, grafiğin parçalarını etrafında taşımak için sürükleyebilirsiniz.

### <a name="view-twin-properties"></a>İkizi özelliklerini görüntüle

**ÖZELLIK Gezgini** kutusunda, özelliklerinin ve bunların değerlerinin listesini görmek için bir ikizi seçebilirsiniz.

Room0 öğesinin özellikleri şunlardır:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="$DtId bir Room0 alanı, 70 sıcaklık alanı ve 30 ' a bir nem alanı dahil olmak üzere Room0 özelliklerini gösteren Özellik Gezgini kutusunun etrafında vurgulayın." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 70 sıcaklığını içerir.

Room1 öğesinin özellikleri şunlardır:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="$DtId bir room1 alanı, 80 sıcaklık alanı ve 60 nem alanı dahil olmak üzere room1 özelliklerini gösteren Özellik Gezgini kutusunun etrafında vurgulayın." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 80 sıcaklığını içerir.

### <a name="query-the-graph"></a>Grafiği sorgulama

Azure dijital TWINS 'in ana özelliği, ortamınız hakkında soruları yanıtlamak için ikizi grafınızı kolayca ve verimli bir şekilde [sorgulayabilir](concepts-query-language.md) .

Grafikteki TWINS 'i sorgulamak için bir yol, **özelliklerinden** biridir. Özelliklere göre sorgulama, çeşitli soruların yanıtlanmasına yardımcı olabilir. Örneğin, ortamınızda ilgilenilmesi gerekebilecek aykırı ler bulabilirsiniz.

Bu bölümde, ortamınızdaki kaç TWINS 'in 75 üzerinde sıcaklık olduğunu öğrenmek için bir sorgu çalıştıracaksınız.

Yanıtı görmek için **sorgu Gezgini** kutusunda aşağıdaki sorguyu çalıştırın.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Room0 'ın 70 sıcaklığını ve room1 80 sıcaklığını içeren ikizi özelliklerini görüntülemeyi geri çekin. Bu nedenle, sonuçlarda yalnızca room1 gösterilir.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="Yalnızca room1 gösteren özellik sorgusunun sonuçları." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> Diğer karşılaştırma işleçleri (<, >, =, veya! =) önceki sorgu içinde de desteklenir. Kendi sorularınızı yanıtlamayı denemek için bu işleçleri, farklı değerleri veya farklı ikizi özelliklerini sorguya takmayı deneyebilirsiniz.

## <a name="edit-data-in-the-graph"></a>Grafikteki verileri düzenleme

Grafiğinizde temsil edilen TWINS özelliklerini düzenlemek için Azure Digital TWINS Gezginini kullanabilirsiniz. Bu bölümde Room0 'in sıcaklığını 76 olarak tetikliyoruz.

Başlamak için, **ÖZELLIK Gezgini** kutusunda Özellik listesini açmak üzere **Room0** ' ı seçin.

Bu listedeki Özellikler düzenlenebilir. Yeni bir değer girmeyi etkinleştirmek için **70** sıcaklık değerini seçin. **76** girin ve sıcaklığın **76** olarak güncelleştirilmesi için **Kaydet** simgesini seçin.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Room0 özelliklerini gösteren Özellik Gezgini kutusu. Sıcaklık değeri, 76 gösteren düzenlenebilir bir kutusudur ve Kaydet simgesinin etrafında bir vurgulayıcıdır." lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Şimdi, güncelleştirmeyi yapmak için Azure Digital TWINS [API 'leri](how-to-use-apis-sdks.md) ile arka planda kullanılan düzeltme eki kodunun göründüğü bir **yama bilgileri** penceresi görürsünüz. **Kapat**’ı seçin.

### <a name="query-to-see-the-result"></a>Sonucu görmek için sorgu

Grafiğin, Room0 için güncelleştirme sıcaklığını başarıyla kaydetdiğini doğrulamak için, daha önce sorguyu yeniden çalıştırın. Bu, ortamdaki tüm TWINS sürümlerini 75 ' ün üzerinde bir sıcaklık ile alır.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Room0 'ın sıcaklığı 70 olarak 76 ' e değiştiği için, her iki TWINS de sonuçta görünür.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="Hem Room0 hem de room1 gösteren özellik sorgusunun sonuçları." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>İnceleme ve contextulaştır dersleri

Bu hızlı başlangıçta bir Azure dijital TWINS örneği oluşturdunuz, Azure dijital TWINS Gezgini 'ne bağladınız ve bunu örnek bir senaryoyla doldurmuş olursunuz.

Daha sonra grafiği şu şekilde araştırın:

* Senaryo hakkında bir soruyu yanıtlamak için sorgu kullanma.
* Dijital ikizi bir özelliği Düzenle.
* Güncelleştirmenin bir sonucu olarak yanıtın nasıl değiştiğini görmek için sorguyu yeniden çalıştırın.

Bu alıştırmanın amacı, ortam değişmeye devam ettiği halde ortamınıza ilişkin soruları yanıtlamak için Azure dijital TWINS grafiğini nasıl kullanabileceğinizi gösterir.

Bu hızlı başlangıçta, sıcaklık güncelleştirmesini el ile yaptınız. Azure dijital TWINS 'te, dijital TWINS 'i gerçek IoT cihazlarına bağlamak, böylelikle telemetri verilerine bağlı olarak güncelleştirmeleri otomatik olarak almaları gerekir. Bu şekilde, ortamınızın gerçek durumunu her zaman yansıtan canlı bir grafik oluşturabilirsiniz. Ortamınızda gerçek zamanlı olarak neler olduğunu öğrenmek için sorguları kullanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta çalışmayı kaydırmak için, önce çalışan konsol uygulamasını sonlandırın. Bu eylem, tarayıcıda Azure Digital TWINS gezgin uygulaması bağlantısını kapatır. Artık canlı verileri tarayıcıda görüntüleyemeyeceksiniz. Tarayıcı sekmesini kapatabilirsiniz.

Ardından, daha sonra yapmak istediğiniz seçeneğe bağlı olarak kaldırmak istediğiniz kaynakları seçebilirsiniz.

* **Azure dijital TWINS öğreticilerine devam etmeyi planlıyorsanız**, söz konusu makaleler için bu hızlı başlangıçta örneği yeniden kullanabilirsiniz ve bunu kaldırmanız gerekmez.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Yerel makinenizden proje klasörünü de silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, Azure dijital TWINS öğreticilerine devam ederek kendi Azure dijital TWINS senaryonuz ve etkileşim araçlarınızı oluşturun.

> [!div class="nextstepaction"]
> [Öğretici: istemci uygulamasını kodlayın](tutorial-code.md)
