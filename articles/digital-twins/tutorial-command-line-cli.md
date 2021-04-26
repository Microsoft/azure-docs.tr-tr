---
title: "Öğretici: Azure dijital TWINS 'te bir grafik oluşturma (CLı)"
titleSuffix: Azure Digital Twins
description: Azure CLı kullanarak Azure dijital TWINS senaryosu oluşturma öğreticisi
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 578befe3e26ebb42fa2172976e07d0a5836e3743
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107168"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Öğretici: Azure CLı kullanarak Azure dijital TWINS grafiği oluşturma

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Bu öğreticide, modeller, TWINS ve ilişkiler kullanarak Azure dijital TWINS 'de bir grafik oluşturacaksınız. Bu öğreticide, [ **Azure CLI** Için Azure dijital TWINS komutu ayarlanmış](how-to-use-cli.md)olan araç. 

CLı komutlarını, model yükleme, TWINS oluşturma ve değiştirme ve ilişki oluşturma gibi temel Azure dijital TWINS eylemlerini gerçekleştirmek için kullanabilirsiniz. CLı komutlarının tam kümesini görmek için [ *az DT* komut kümesi için başvuru belgelerine](/cli/azure/dt) de bakabilirsiniz.

Bu öğreticide,...
> [!div class="checklist"]
> * Ortam modelleme
> * Dijital ikizleri oluşturma
> * Grafik oluşturacak ilişkiler ekleme
> * Soruları yanıtlamak için grafiği sorgulama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticideki adımları tamamlayabilmeniz için öncelikle aşağıdaki önkoşulları gerçekleştirmeniz gerekir.

Azure aboneliğiniz yoksa başlamadan önce **[ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun** .

### <a name="download-the-sample-models"></a>Örnek modelleri indirin

Öğretici, Azure dijital TWINS için C# [uçtan uca örnek projenin](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) bir parçası olan önceden yazılmış iki model kullanır. Model dosyaları şurada bulunur: 
* [*ÜzerindeRoom.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*ÜzerindeFloor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Makinenizde dosyaları almak için yukarıdaki gezinti bağlantılarını kullanın ve dosya gövdelerini aynı adlarla (*Room.jsüzerinde* ve *Floor.jsaçık*) makinenizde yerel dosyalara kopyalayın.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell oturum ayarlama
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure dijital TWINS örneği hazırlama

Bu makalede Azure Digital TWINS ile çalışmak için öncelikle **bir Azure dijital TWINS örneği** ve bu uygulamayı kullanmak için gerekli izinleri ayarlamanız gerekir. Önceki çalışmalardan daha önceden ayarlanmış bir Azure dijital TWINS örneğiniz varsa, bu örneği kullanabilirsiniz.

Aksi takdirde, [*nasıl yapılır: örnek ve kimlik doğrulaması ayarlama*](how-to-set-up-instance-cli.md)bölümündeki yönergeleri izleyin. Yönergeler Ayrıca, her adımı başarıyla tamamlayıp tamamladığınızdan ve yeni örneğinizi kullanmaya başlamaya hazırlamış olduğunuzu doğrulamaya yönelik adımları da içerir.

Azure dijital TWINS örneğinizi ayarladıktan sonra, örneğe daha sonra bağlanmanız için gereken aşağıdaki değerleri unutmayın:
* Örneğin **_ana bilgisayar adı_**
* örneği oluşturmak için kullandığınız **Azure aboneliği** . 

Aşağıdaki Azure CLı komutunun çıkışında örneğiniz için bu değerlerin her ikisini de alabilirsiniz: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Az DT Show komutunun çıkışını gösteren Cloud Shell tarayıcı penceresinin ekran görüntüsü. Ana bilgisayar adı alanı ve abonelik KIMLIĞI (kimlik alanının parçası) vurgulanır.":::

## <a name="model-a-physical-environment-with-dtdl"></a>DTDL ile fiziksel ortam modelleme

CLı ve Azure dijital TWINS örneği kuruldığına göre, bir senaryonun grafiğini oluşturmaya başlayabilirsiniz. 

Azure dijital TWINS çözümü oluşturmanın ilk adımı, ortamınız için ikizi [**modellerini**](concepts-models.md) tanımlıyor. 

Modeller, nesne odaklı programlama dillerinde sınıflarla benzerdir; Bunlar, daha sonra izlemek ve örneklendirilecek [dijital TWINS](concepts-twins-graph.md) için Kullanıcı tanımlı şablonlar sağlar. Bunlar, **dijital TWINS tanım dili (DTDL)** adlı JSON benzeri bir dilde yazılır ve bir ikizi *özellikleri*, *telemetri*, *ilişki* ve *bileşen* tanımlayabilir.

> [!NOTE]
> DTDL Ayrıca dijital TWINS 'teki *komutların* tanımına izin verir. Ancak, şu anda Azure Digital TWINS hizmetinde komutlar desteklenmemektedir.

Makinenizde, [Önkoşullar](#prerequisites) bölümünde oluşturduğunuz dosyadaki *Room.js* gidin. Kodu bir kod düzenleyicisinde açın ve aşağıdaki yollarla değiştirin:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Azure dijital TWINS 'e modelleri yükleme

Modelleri tasarladıktan sonra Azure dijital TWINS örneğinizi yüklemeniz gerekir. Bu, Azure dijital TWINS hizmeti örneğinizi kendi özel etki alanı sözlüğinizle yapılandırır. Modelleri karşıya yükledikten sonra, bunları kullanan ikizi örnekleri oluşturabilirsiniz.

1. Cloud Shell kullanarak modeller eklemek için, dosyaları kullanan Cloud Shell komutunu çalıştırdığınızda dosyaların kullanılabilmesi için model dosyalarınızı Cloud Shell depolamasına yüklemeniz gerekir. Bunu yapmak için "dosyaları karşıya yükle/Indir" simgesini seçin ve "karşıya yükle" yi seçin.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Karşıya yükleme simgesinin seçimini gösteren Cloud Shell tarayıcı penceresinin ekran görüntüsü.":::
    
    Makinenizde dosya *Room.js* gidin ve "Aç" ı seçin. Daha sonra *Floor.js* için bu adımı tekrarlayın.

1. Ardından, güncelleştirilmiş *Oda* modelinizi Azure dijital TWINS örneğinize yüklemek için aşağıda gösterildiği gibi [**az DT model Create**](/cli/azure/dt/model#az_dt_model_create) komutunu kullanın. İkinci komut, farklı türlerde TWINS oluşturmak için de bir sonraki bölümde kullanacağınız bir başka model, *kat* yükler.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    Her komutun çıktısı başarıyla karşıya yüklenen model hakkında bilgi gösterir.

    >[!TIP]
    >Ayrıca, `--from-directory` model Oluştur komutu seçeneğini kullanarak bir dizin içindeki tüm modelleri aynı anda karşıya yükleyebilirsiniz. Daha fazla bilgi için bkz. [ *az DT model Create* için isteğe bağlı parametreler](/cli/azure/dt/model#az_dt_model_create-optional-parameters).

1. Modellerin aşağıda gösterildiği gibi [**az DT model List**](/cli/azure/dt/model#az_dt_model_list) komutuyla oluşturulduğunu doğrulayın. Bu işlem, Azure dijital TWINS örneğine yüklenmiş tüm modellerin listesini tam bilgileriyle yazdırır. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Sonuçlarda düzenlenmiş *Oda* modelini arayın:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Güncelleştirilmiş Oda modelini içeren model listesi komutunun sonucunu gösteren Cloud Shell ekran görüntüsü." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Hatalar

CLı Ayrıca hizmetten gelen hataları da işler. 

`az dt model create`İkinci bir kez karşıya yüklediğiniz modellerden birini yeniden karşıya yüklemeyi denemek için komutu yeniden çalıştırın:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Modellerin üzerine yazılamaz, bu işlem şimdi hata kodu döndürür `ModelIdAlreadyExists` .

## <a name="create-digital-twins"></a>Dijital ikizleri oluşturma

Artık bazı modeller Azure dijital TWINS örneğinizi karşıya yüklediğinize göre, model tanımlarına göre [**dijital TWINS**](concepts-twins-graph.md) oluşturabilirsiniz. Dijital TWINS, iş ortamınızdaki varlıkları, bir gruptaki algılayıcılar gibi şeyler, bir bina içindeki odalar veya bir otomobilde ışıklar temsil eder. 

Dijital bir ikizi oluşturmak için [**az DT ikizi Create**](/cli/azure/dt/twin#az_dt_twin_create) komutunu kullanın. İkizi 'in temel aldığı modele başvurmanız gerekir ve isteğe bağlı olarak modeldeki tüm özellikler için başlangıç değerlerini tanımlayabilir. Bu aşamada herhangi bir ilişki bilgisi iletmeniz gerekmez.

1. Daha önce güncelleştirdiğiniz *Oda* modeline ve başka bir model, *kata* göre birkaç twıns oluşturmak için Cloud Shell bu kodu çalıştırın. *Odanın* üç özelliği olduğunu anımsayın, bu nedenle bunlar için başlangıç değerleriyle bağımsız değişken sağlayabilirsiniz. (Özellik değerlerini başlatmak genel olarak isteğe bağlıdır, ancak bu öğretici için gereklidir.)

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > PowerShell ortamında Cloud Shell kullanıyorsanız, `--properties` JSON değerinin doğru ayrıştırılabilmesi için tırnak işareti karakterlerini atlamanız gerekebilir. Bu Düzenle, Oda TWINS oluşturma komutları şuna benzer:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Bu, aşağıdaki ekran görüntüsünde yansıtılır.
    
    Her komutun çıktısı, başarıyla oluşturulan ikizi (bunlarla başlatılan Oda TWINS özellikleri dahil) hakkındaki bilgileri gösterir.

1. TWINS 'nin aşağıda gösterildiği gibi [**az DT ikizi Query**](/cli/azure/dt/twin#az_dt_twin_query) komutuyla oluşturulduğunu doğrulayabilirsiniz. Gösterilen sorgu, Azure dijital TWINS örneğinizin tüm dijital TWINS değerlerini bulur.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    Sonuçlarda *room0*, *room1*, *floor0* ve *Floor1* TWINS ' i arayın. Bu sorgunun sonucunun bir kısmını gösteren bir alıntı aşağıda verilmiştir.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Room0 ve room1 dahil olmak üzere ikizi sorgusunun kısmi sonucunu gösteren Cloud Shell ekran görüntüsü." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Dijital ikizi değiştirme

Ayrıca, oluşturduğunuz bir ikizi özelliklerini de değiştirebilirsiniz. 

1. *Room0*'ın RoomName öğesini *room0* iken *PresidentialSuite* olarak değiştirmek için bu [**az DT ikizi Update**](/cli/azure/dt/twin#az_dt_twin_update) komutunu çalıştırın:

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > PowerShell ortamında Cloud Shell kullanıyorsanız, `--json-patch` JSON değerinin doğru ayrıştırılabilmesi için tırnak işareti karakterlerini atlamanız gerekebilir. Bu Düzenle ile ikizi 'yi güncelleştirme komutu şöyle görünür:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Bu, aşağıdaki ekran görüntüsünde yansıtılır.
    
    Bu komutun çıktısı, ikizi 'in geçerli bilgilerini gösterir ve sonucunda için yeni değeri görmeniz gerekir `RoomName` .

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="PresidentialSuite mname öğesinin bir RoomName içeren Update komutunun sonucunu gösteren Cloud Shell ekran görüntüsü." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. *Room0*'ın bilgilerini görmek için [**az DT ikizi Show**](/cli/azure/dt/twin#az_dt_twin_show) komutunu çalıştırarak güncelleştirmeyi başarılı bir şekilde doğrulayabilirsiniz:

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    Çıktı, güncelleştirilmiş adı yansıtmalıdır.

## <a name="create-a-graph-by-adding-relationships"></a>İlişkiler ekleyerek bir grafik oluşturma

Daha sonra, bu TWINS arasında bir [**ikizi grafiğine**](concepts-twins-graph.md)bağlanmak için bazı **ilişkiler** oluşturabilirsiniz. İkizi grafikleri tüm ortamı temsil etmek için kullanılır. 

Bir ikizi arasında oluşturabileceğiniz ilişki türleri, daha önce yüklediğiniz [modeller](#model-a-physical-environment-with-dtdl) içinde tanımlanır. [ *Taban* için model tanımı](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) , katörlerin, *Contains* adlı bir ilişki türüne sahip olduğunu belirtir. Bu, her bir *tabandan* içerdiği ilgili odaya bir *Contains*-Type ilişkisi oluşturulmasını mümkün kılar.

Bir ilişki eklemek için [**az DT ikizi Relationship Create**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_create) komutunu kullanın. İlişkinin geldiği ikizi, ilişki türü ve ilişkinin bağlandığı ikizi belirtin. Son olarak, ilişkiye benzersiz bir KIMLIK verin. Bir ilişki özelliklerine sahip olacak şekilde tanımlanmışsa, bu komutta ilişki özelliklerini de başlatabilirsiniz.

1. Daha önce oluşturduğunuz her bir *taban* - *tür* ilişkisini karşılık gelen *odaya* ikizi eklemek için aşağıdaki kodu çalıştırın. İlişkiler *relationship0* ve *relationship1* olarak adlandırılır.

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >Ayrıca, [ *taban* modeldeki](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) *bir ilişki de* iki özellik ile tanımlanmıştır `ownershipUser` ve `ownershipDepartment` Bu sayede, ilişkileri oluştururken bunların başlangıç değerleriyle birlikte bağımsız değişkenler de sağlayabilirsiniz.
    > Bu özelliklerle başlatılan bir ilişki oluşturmak için aşağıdaki `--properties` komutlardan birine, aşağıdaki gibi seçeneği ekleyin:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > PowerShell ortamında Cloud Shell kullanıyorsanız, `--properties` JSON değerinin doğru ayrıştırılabilmesi için tırnak işareti karakterlerini atlamanız gerekebilir.
    
    Her komutun çıktısı başarıyla oluşturulan ilişki hakkındaki bilgileri gösterir.

1. Azure dijital TWINS örneğinizdeki ilişkileri sorgulayan aşağıdaki komutlardan herhangi biriyle ilişkileri doğrulayabilirsiniz.
    * Her bir tabandan gelen tüm ilişkileri görmek için (ilişkiyi bir taraftan görüntüleme):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * Her odaya ulaşan tüm ilişkileri görmek için ("diğer" taraftan ilişkiyi görüntüleme):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Bu ilişkileri tek tek aramak için KIMLIĞE göre:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

Bu öğreticide ayarladığınız TWINS ve ilişkiler aşağıdaki kavramsal grafiği oluşturur:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Kavramsal grafiği gösteren bir diyagram. floor0, relationship0 ile room0 arasında bağlanır ve Floor1 relationship1 ile room1 arasında bağlanır." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Ortam sorularını yanıtlamak için ikizi grafiğini sorgulama

Azure dijital TWINS 'in ana özelliği, ortamınız hakkında soruları yanıtlamak için ikizi grafınızı kolayca ve verimli bir şekilde [sorgulayabilir](concepts-query-language.md) . Azure CLı 'da bu, [**az DT ikizi Query**](/cli/azure/dt/twin#az_dt_twin_query) komutuyla yapılır.

Örnek ortamla ilgili bazı sorulara yanıt vermek için Cloud Shell aşağıdaki sorguları çalıştırın.

1. **Ortamınızdaki Azure dijital TWINS 'te gösterilen tüm varlıklar nelerdir?** (tümünü sorgula)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    Bu, ortamınızı bir bakışta almanıza olanak sağlar ve her şeyin Azure dijital TWINS 'in içinde olmasını istediğiniz şekilde gösterildiğinden emin olun. Bunun sonucu, her dijital ikizi ayrıntılarını içeren bir çıktıdır. Aşağıda bir alıntı verilmiştir:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Room0 ve room1 dahil olmak üzere ikizi sorgusunun kısmi sonucunu gösteren Cloud Shell ekran görüntüsü." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Bu, örnekte [*dijital TWINS oluştur*](#create-digital-twins) bölümünde kullandığınız komutun aynısını, örnekteki tüm Azure dijital TWINS sürümlerini bulmak için kullanabilirsiniz.

1. **Ortammdaki tüm odalar nelerdir?** (modele göre sorgu)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    Hangi bilgilerin temsil edildiği hakkında daha ayrıntılı bilgi edinmek için sorgunuzu belirli bir türden TWINS ile kısıtlayabilirsiniz. Bunun sonucu, *room0* ve *room1* gösterir, ancak *floor0* veya *Floor1* **göstermez (** Odalar, odaların değil, bu nedenle).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Yalnızca room0 ve room1 içeren model sorgusunun sonucunu gösteren Cloud Shell ekran görüntüsü." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. ***Floor0* üzerindeki tüm odalar nelerdir?** (ilişkiye göre sorgulama)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    TWINS 'in nasıl bağlı olduğu hakkında bilgi edinmek veya sorgunuzu belirli bir alanla kısıtlamak için grafiğinizde ilişkiler temelinde sorgulama yapabilirsiniz. Yalnızca *room0* *floor0*, bu nedenle sonuçtaki tek yer.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Room0 içeren ilişki sorgusunun sonucunu gösteren Cloud Shell ekran görüntüsü." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > İkizi 'ın KIMLIĞI (Yukarıdaki sorguda *floor0* gibi), meta veri alanı kullanılarak sorgulandığına dikkat edin `$dtId` . 
    >
    >İle başlayan bu gibi meta veri alanlarıyla bir sorgu çalıştırmak için Cloud Shell kullanırken, `$` `$` bir değişken olmadığını ve sorgu metninde bir sabit değer olarak kullanılması gerektiğini bildirmek Cloud Shell için geri alma ile kaçış yapmalısınız. Bu, yukarıdaki ekran görüntüsüne yansıtılır.

1. **75 üzerinde sıcaklık olan ortammdaki tüm TWINS nedir?** (özelliğe göre sorgu)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    Ortamınızda dikkat etmeniz gerekebilecek aykırı değerleri bulma dahil olmak üzere çeşitli soruları yanıtlamak için, özellikleri temel alarak grafik sorgulama yapabilirsiniz. Diğer karşılaştırma işleçleri ( *<* , *>* , *=* veya *! =*) de desteklenir. *room1* , 80 sıcaklığını içerdiğinden burada sonuçlarda görüntülenir.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Yalnızca room1 içeren özellik sorgusunun sonucunu gösteren Cloud Shell ekran görüntüsü." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **75 üzerindeki sıcaklığa sahip *floor0* üzerindeki tüm odalar nelerdir?** (bileşik sorgu)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    SQL 'de olduğu gibi önceki sorguları,, gibi Birleşik işleçler kullanarak da birleştirebilirsiniz `AND` `OR` `NOT` . Bu sorgu `AND` , önceki ikizi sıcaklıklar hakkında daha belirgin bir sorgu oluşturmak için kullanır. Sonuç artık yalnızca, *floor0* üzerinde olan 75 üzerinde sıcaklık olan odaları (Bu durumda, bunlardan hiçbiri) içerir. Sonuç kümesi boş.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Hiçbir öğe içermeyen bileşik sorgunun sonucunu gösteren Cloud Shell ekran görüntüsü." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu Öğreticiyi tamamladıktan sonra, ne yapmak istediğinize bağlı olarak kaldırmak istediğiniz kaynakları seçebilirsiniz.

* **Sonraki öğreticiye devam etmeyi planlıyorsanız**, burada ayarladığınız kaynakları tutabilir ve arasında herhangi bir şeyi temizlemeden Azure dijital TWINS örneğini yeniden kullanabilirsiniz.

* **Azure dijital TWINS örneğini kullanmaya devam etmek, ancak tüm modellerini, TWINS ve ilişkilerini temizlemek** istiyorsanız, örneğinizdeki ilişkileri, TWINS 'leri ve modelleri temizlemek için [**az DT ikizi Relationship Delete**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_delete), [**az DT ikizi Delete**](/cli/azure/dt/twin#az_dt_twin_delete)ve [**az DT model Delete**](/cli/azure/dt/model#az_dt_model_delete) komutlarını kullanabilirsiniz.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Ayrıca, yerel makinenizde oluşturduğunuz model dosyalarını da silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar 

Bu öğreticide, Azure CLı kullanarak Örneğinizde bir grafik oluşturarak Azure dijital TWINS ile çalışmaya başladım. Bir grafik oluşturmak için modeller, dijital TWINS ve ilişkiler oluşturdunuz. Ayrıca, Azure dijital TWINS 'in bir ortam hakkında ne tür sorular yanıtlayabileceklerini öğrenmek için grafikte bazı sorgular da çalıştırdınız.

Azure dijital TWINS 'i diğer Azure hizmetleriyle birleştirerek veri odaklı, uçtan uca bir senaryoyu tamamlamaya yönelik sonraki öğreticiye geçin:
> [!div class="nextstepaction"]
> [*Öğretici: uçtan uca bir çözümü bağlama*](tutorial-end-to-end.md)