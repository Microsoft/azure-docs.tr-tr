---
title: "Öğretici: Azure dijital TWINS 'te bir grafik oluşturma (istemci uygulaması)"
titleSuffix: Azure Digital Twins
description: Örnek komut satırı uygulaması kullanarak Azure dijital TWINS senaryosu oluşturma öğreticisi
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493722"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Öğretici: örnek bir istemci uygulaması kullanarak Azure dijital TWINS grafiği oluşturma

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Bu öğreticide, modeller, TWINS ve ilişkiler kullanarak Azure dijital TWINS 'de bir grafik oluşturacaksınız. Bu öğretici için araç, bir Azure dijital TWINS örneğiyle etkileşime yönelik **örnek bir komut satırı istemci uygulamasıdır** . İstemci uygulaması, [*öğretici: istemci uygulaması kodu*](tutorial-code.md)olarak yazılmış birine benzer.

Bu örneği, model yükleme, TWINS oluşturma ve değiştirme ve ilişki oluşturma gibi temel Azure dijital TWINS eylemlerini gerçekleştirmek için kullanabilirsiniz. Ayrıca, Azure dijital TWINS API 'Leri hakkında bilgi edinmek ve örnek projeyi istediğiniz gibi değiştirerek kendi komutlarınızı uygulamayı yapmak için [Örneğin koduna](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) bakabilirsiniz.

Bu öğreticide,...
> [!div class="checklist"]
> * Ortam modelleme
> * Dijital ikizleri oluşturma
> * Grafik oluşturacak ilişkiler ekleme
> * Soruları yanıtlamak için grafiği sorgulama

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Örnek projeyi çalıştırma

Artık uygulama ve kimlik doğrulaması ayarlanmış olduğuna göre, araç çubuğunda bu düğmeyi kullanarak projeyi çalıştırın:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Visual Studio başlangıç düğmesinin (SampleClientApp Projesi) ekran görüntüsü." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Bir konsol penceresi açılır, kimlik doğrulama işlemi gerçekleştirebilir ve bir komut için bekler. 
* Kimlik doğrulaması tarayıcı aracılığıyla gerçekleştirilir: varsayılan Web tarayıcınız, bir kimlik doğrulama istemiyle açılır. Azure kimlik bilgilerinizle oturum açmak için bu istemi kullanın. Ardından tarayıcı sekmesini veya penceresini kapatabilirsiniz.

Proje konsolunun neye benzediğinin ekran görüntüsü aşağıda verilmiştir:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Komut satırı uygulamasındaki hoş geldiniz iletisinin ekran görüntüsü." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Bu projeyle kullanabileceğiniz tüm olası komutların listesi için, `help` Proje konsoluna girip Return tuşuna basın.

Bu öğreticideki adımların geri kalanında proje konsolunu çalışır durumda tutun.

## <a name="model-a-physical-environment-with-dtdl"></a>DTDL ile fiziksel ortam modelleme

Artık Azure Digital TWINS örneği ve örnek uygulama ayarlanmış olduğuna göre, senaryonun bir grafiğini oluşturmaya başlayabilirsiniz. 

Azure dijital TWINS çözümü oluşturmanın ilk adımı, ortamınız için ikizi [**modellerini**](concepts-models.md) tanımlıyor. 

Modeller, nesne odaklı programlama dillerinde sınıflarla benzerdir; Bunlar, daha sonra izlemek ve örneklendirilecek [dijital TWINS](concepts-twins-graph.md) için Kullanıcı tanımlı şablonlar sağlar. Bunlar, **dijital TWINS tanım dili (DTDL)** adlı JSON benzeri bir dilde yazılır ve bir ikizi *özellikleri*, *telemetri*, *ilişki* ve *bileşen* tanımlayabilir.

> [!NOTE]
> DTDL Ayrıca dijital TWINS 'teki *komutların* tanımına izin verir. Ancak, şu anda Azure Digital TWINS hizmetinde komutlar desteklenmemektedir.

_**AdtE2ESample**_ projesinin açık olduğu Visual Studio pencerenizde, *AdtSampleApp\SampleClientApp\Models klasörüne* gitmek için *Çözüm Gezgini* bölmesini kullanın. Bu klasör örnek modeller içerir.

Düzenle penceresinde açmak için *Room.js* seçin ve aşağıdaki yollarla değiştirin:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Azure dijital TWINS 'e modelleri yükleme

Modelleri tasarladıktan sonra Azure dijital TWINS örneğinizi yüklemeniz gerekir. Bu, Azure dijital TWINS hizmeti örneğinizi kendi özel etki alanı sözlüğinizle yapılandırır. Modelleri karşıya yükledikten sonra, bunları kullanan ikizi örnekleri oluşturabilirsiniz.

1. Proje konsolu penceresinde, güncelleştirilmiş *Oda* modelinizi karşıya yüklemek için aşağıdaki komutu çalıştırın ve farklı türlerde TWINS oluşturmak için bir sonraki bölümde kullanacağınız bir *kat* modeli de vardır.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    Çıktı, modellerin başarıyla oluşturulduğunu göstermelidir.

1. Komutu çalıştırarak modellerin oluşturulduğunu doğrulayın `GetModels true` . Bu, karşıya yüklenen tüm modeller için Azure dijital TWINS örneğini sorgular ve tam bilgilerini yazdırır. Sonuçlarda düzenlenmiş *Oda* modelini arayın:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Güncelleştirilmiş Oda modelini gösteren Getmodellerden elde edilen sonucun ekran görüntüsü." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Hatalar

Örnek uygulama, hizmetten gelen hataları da işler. 

`CreateModels`İkinci bir kez karşıya yüklediğiniz modellerden birini yeniden karşıya yüklemeyi denemek için komutu yeniden çalıştırın:

```cmd/sh
CreateModels Room
```

Modellerin üzerine yazılamaz, bu işlem artık bir hizmet hatası döndürüyor.
Mevcut modellerin nasıl silineceği hakkındaki ayrıntılar için bkz. [*nasıl yapılır: DTDL modellerini yönetme*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>Dijital ikizleri oluşturma

Artık bazı modeller Azure dijital TWINS örneğinizi karşıya yüklediğinize göre, model tanımlarına göre [**dijital TWINS**](concepts-twins-graph.md) oluşturabilirsiniz. Dijital TWINS, iş ortamınızdaki varlıkları, bir gruptaki algılayıcılar gibi şeyler, bir bina içindeki odalar veya bir otomobilde ışıklar temsil eder. 

Dijital bir ikizi oluşturmak için `CreateDigitalTwin` komutunu kullanın. İkizi 'in temel aldığı modele başvurmanız gerekir ve isteğe bağlı olarak modeldeki tüm özellikler için başlangıç değerlerini tanımlayabilir. Bu aşamada herhangi bir ilişki bilgisi iletmeniz gerekmez.

1. Daha önce güncelleştirdiğiniz *Oda* modeline ve başka bir model, *kata* göre birkaç TWINS oluşturmak için bu kodu çalışan proje konsolunda çalıştırın. *Odanın* üç özelliği olduğunu anımsayın, bu nedenle bunlar için başlangıç değerleriyle bağımsız değişken sağlayabilirsiniz. (Özellik değerlerini başlatmak genel olarak isteğe bağlıdır, ancak bu öğretici için gereklidir.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    Bu komutların çıktısı, TWINS 'nin başarıyla oluşturulduğunu göstermelidir. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Floor0, Floor1, room0 ve room1 içeren CreateDigitalTwin komutlarının sonuçlarından bir alıntı gösteren ekran görüntüsü." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. Komutu çalıştırarak TWINS 'nin oluşturulduğunu doğrulayabilirsiniz `Query` . Bu komut, içerdiği tüm dijital TWINS için Azure dijital TWINS örneğinizi sorgular. Sonuçlarda *room0*, *room1*, *floor0* ve *Floor1* TWINS ' i arayın.

### <a name="modify-a-digital-twin"></a>Dijital ikizi değiştirme

Ayrıca, oluşturduğunuz bir ikizi özelliklerini de değiştirebilirsiniz. 

> [!NOTE]
> Temel REST API, bir ikizi güncelleştirmelerini tanımlamak için [JSON Patch](http://jsonpatch.com/) biçimini kullanır. Komut satırı uygulaması, temel API 'Lerin beklediği verilerle bir truer deneyimi sağlamak için de bu biçimi kullanır.

1. *Room0*'In RoomName öğesini *Room0* ' den *PresidentialSuite*' ye değiştirmek için bu komutu çalıştırın:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    Çıktı, ikizi başarıyla güncelleştirildiğini göstermelidir.

1. *Room0*'un bilgilerini görmek için bu komutu çalıştırarak güncelleştirmeyi başarılı bir şekilde doğrulayabilirsiniz:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    Çıktı, güncelleştirilmiş adı yansıtmalıdır.


## <a name="create-a-graph-by-adding-relationships"></a>İlişkiler ekleyerek bir grafik oluşturma

Daha sonra, bu TWINS arasında bir [**ikizi grafiğine**](concepts-twins-graph.md)bağlanmak için bazı **ilişkiler** oluşturabilirsiniz. İkizi grafikleri tüm ortamı temsil etmek için kullanılır. 

Bir ikizi arasında oluşturabileceğiniz ilişki türleri, daha önce yüklediğiniz [modeller](#model-a-physical-environment-with-dtdl) içinde tanımlanır. [ *Taban* için model tanımı](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) , katörlerin, *Contains* adlı bir ilişki türüne sahip olduğunu belirtir. Bu, her bir *tabandan* içerdiği ilgili odaya bir *Contains*-Type ilişkisi oluşturulmasını mümkün kılar.

Bir ilişki eklemek için `CreateRelationship` komutunu kullanın. İlişkinin geldiği ikizi, ilişki türü ve ilişkinin bağlandığı ikizi belirtin. Son olarak, ilişkiye benzersiz bir KIMLIK verin.

1. Daha önce oluşturduğunuz her bir *tabandan* bir "Contains" ilişkisini karşılık gelen *odaya* ikizi eklemek için aşağıdaki kodu çalıştırın. İlişkiler *relationship0* ve *relationship1* olarak adlandırılır.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >Ayrıca *,* [ *taban* modeldeki](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) ilişki, iki dize özellikleriyle da tanımlanmıştır `ownershipUser` ve `ownershipDepartment` Bu sayede, ilişkileri oluştururken bunların başlangıç değerleriyle birlikte bağımsız değişkenler de sağlayabilirsiniz.
    > Bu özelliklerin başlangıç değerlerini de belirten *relationship0* oluşturmak için yukarıdaki komutun alternatif bir sürümü aşağıda verilmiştir:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    Bu komutların çıktısı, ilişkilerin başarıyla oluşturulduğunu onaylar:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Relationship0 ve relationship1 içeren CreateRelationship komutlarının sonuçlarından elde edilen bir alıntı ekran görüntüsü." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Azure dijital TWINS örneğinizdeki ilişkileri sorgulayan aşağıdaki komutlardan herhangi biriyle ilişkileri doğrulayabilirsiniz.
    * Her bir tabandan gelen tüm ilişkileri görmek için (ilişkiyi bir taraftan görüntüleme):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Her odaya ulaşan tüm ilişkileri görmek için ("diğer" taraftan ilişkiyi görüntüleme):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Bu ilişkileri tek tek aramak için KIMLIĞE göre:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

Bu öğreticide ayarladığınız TWINS ve ilişkiler aşağıdaki kavramsal grafiği oluşturur:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Kavramsal grafiği gösteren bir diyagram. floor0, relationship0 ile room0 arasında bağlanır ve Floor1 relationship1 ile room1 arasında bağlanır." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Ortam sorularını yanıtlamak için ikizi grafiğini sorgulama

Azure dijital TWINS 'in ana özelliği, ortamınız hakkında soruları yanıtlamak için ikizi grafınızı kolayca ve verimli bir şekilde [sorgulayabilir](concepts-query-language.md) . 

Örnek ortamla ilgili bazı sorulara yanıt vermek için çalışan proje konsolunda aşağıdaki komutları çalıştırın.

1. **Ortamınızdaki Azure dijital TWINS 'te gösterilen tüm varlıklar nelerdir?** (tümünü sorgula)

    ```cmd/sh
    Query
    ```

    Bu, ortamınızı bir bakışta almanıza olanak sağlar ve her şeyin Azure dijital TWINS 'in içinde olmasını istediğiniz şekilde gösterildiğinden emin olun. Bunun sonucu, her dijital ikizi ayrıntılarını içeren bir çıktıdır. Aşağıda bir alıntı verilmiştir:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Room0 ve Floor1 de dahil olmak üzere ikizi sorgusundan kısmi bir sonuç gösteren ekran görüntüsü.":::

    >[!NOTE]
    >Örnek projede, `Query` ek bağımsız değişken içermeyen komutu ' nin eşdeğeridir `Query SELECT * FROM DIGITALTWINS` . [Sorgu API 'lerini](/rest/api/digital-twins/dataplane/query) veya [CLI komutlarını](how-to-use-cli.md)kullanarak örneğinizdeki tüm TWINS 'leri sorgulamak için, daha uzun (tamamlanmış) sorguyu kullanın.

1. **Ortammdaki tüm odalar nelerdir?** (modele göre sorgu)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Hangi bilgilerin temsil edildiği hakkında daha ayrıntılı bilgi edinmek için sorgunuzu belirli bir türden TWINS ile kısıtlayabilirsiniz. Bunun sonucu, *room0* ve *room1* gösterir, ancak *floor0* veya *Floor1* **göstermez (** Odalar, odaların değil, bu nedenle).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Yalnızca room0 ve room1 gösteren model sorgusundan elde edilen sonucun ekran görüntüsü.":::

1. ***Floor0* üzerindeki tüm odalar nelerdir?** (ilişkiye göre sorgulama)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    TWINS 'in nasıl bağlı olduğu hakkında bilgi edinmek veya sorgunuzu belirli bir alanla kısıtlamak için grafiğinizde ilişkiler temelinde sorgulama yapabilirsiniz. Yalnızca *room0* *floor0*, bu nedenle sonuçtaki tek yer.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="İlişki sorgusundan elde edilen room0 gösteren sonucun ekran görüntüsü.":::

1. **75 üzerinde sıcaklık olan ortammdaki tüm TWINS nedir?** (özelliğe göre sorgu)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Ortamınızda dikkat etmeniz gerekebilecek aykırı değerleri bulma dahil olmak üzere çeşitli soruları yanıtlamak için, özellikleri temel alarak grafik sorgulama yapabilirsiniz. Diğer karşılaştırma işleçleri ( *<* , *>* , *=* veya *! =*) de desteklenir. *room1* , 80 sıcaklığını içerdiğinden burada sonuçlarda görüntülenir.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Yalnızca room1 gösteren özellik sorgusundan elde edilen sonucun ekran görüntüsü.":::

1. **75 üzerindeki sıcaklığa sahip *floor0* üzerindeki tüm odalar nelerdir?** (bileşik sorgu)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    SQL 'de olduğu gibi önceki sorguları,, gibi Birleşik işleçler kullanarak da birleştirebilirsiniz `AND` `OR` `NOT` . Bu sorgu `AND` , önceki ikizi sıcaklıklar hakkında daha belirgin bir sorgu oluşturmak için kullanır. Sonuç artık yalnızca, *floor0* üzerinde olan 75 üzerinde sıcaklık olan odaları (Bu durumda, bunlardan hiçbiri) içerir. Sonuç kümesi boş.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Bileşik sorgudan elde edilen sonuç gösteren sonucun ekran görüntüsü." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu Öğreticiyi tamamladıktan sonra, ne yapmak istediğinize bağlı olarak kaldırmak istediğiniz kaynakları seçebilirsiniz.

* **Sonraki öğreticiye devam etmeyi planlıyorsanız**, bu Azure dijital TWINS örneğini ve yapılandırılmış örnek uygulamayı bir sonraki öğreticide kullanmaya devam etmek için burada ayarladığınız kaynakları koruyabilirsiniz

* **Azure dijital TWINS örneğini kullanmaya devam etmek, ancak tüm modellerini, TWINS ve ilişkilerini temizlemek** istiyorsanız, örnek uygulama `DeleteAllTwins` ve komutlarını kullanarak, `DeleteAllModels` örneğiniz içindeki TWINS ve modelleri temizleyebilirsiniz.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Yerel makinenizden proje klasörünü de silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar 

Bu öğreticide, örnek bir istemci uygulaması kullanarak Örneğinizde bir grafik oluşturarak Azure Digital TWINS ile çalışmaya başladıysanız. Bir grafik oluşturmak için modeller, dijital TWINS ve ilişkiler oluşturdunuz. Ayrıca, Azure dijital TWINS 'in bir ortam hakkında ne tür sorular yanıtlayabileceklerini öğrenmek için grafikte bazı sorgular da çalıştırdınız.

Azure dijital TWINS 'i diğer Azure hizmetleriyle birleştirerek veri odaklı, uçtan uca bir senaryoyu tamamlamaya yönelik sonraki öğreticiye geçin:
> [!div class="nextstepaction"]
> [*Öğretici: uçtan uca bir çözümü bağlama*](tutorial-end-to-end.md)