---
title: 'Öğretici: istemci uygulamasını kodlayın'
titleSuffix: Azure Digital Twins
description: .NET (C#) SDK 'SıNı kullanarak bir istemci uygulaması için en düşük kodu yazma öğreticisi.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: bd3ba88650161bd11a24697b4ff8575d307120e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102424470"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Öğretici: Azure dijital TWINS API 'Leri ile kodlama

Azure dijital TWINS ile çalışan geliştiriciler, Azure dijital TWINS hizmeti örneğiyle etkileşim kurmak üzere bir istemci uygulaması yazmak için yaygındır. Bu geliştirici odaklı öğreticide, [.net Için Azure dijital TWINS SDK 'sını (C#)](/dotnet/api/overview/azure/digitaltwins/client)kullanarak Azure dijital TWINS hizmetinde programlamaya yönelik bir giriş sunulmaktadır. Bir C# Konsolu istemci uygulaması adımını sıfırdan başlayarak adım adım yazma konusunda size kılavuzluk eder.

> [!div class="checklist"]
> * Projeyi ayarla
> * Proje kodu ile çalışmaya başlama   
> * Kod örneğini doldurun
> * Kaynakları temizleme
> * Sonraki adımlar

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici, kurulum ve proje çalışması için komut satırını kullanır. Bu nedenle, alýþtýrmalar üzerinde gezinmek için herhangi bir kod düzenleyicisini kullanabilirsiniz.

Başlamak için gerekenler:
* Herhangi bir kod Düzenleyicisi
* Geliştirme makinenizde **.NET Core 3,1** . Birden çok platform için .NET Core SDK bu sürümünü [.NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1)' i indirin.

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure dijital TWINS örneği hazırlama

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Projeyi ayarla

Azure dijital TWINS örneğiniz ile çalışmaya hazırsanız, istemci uygulama projesini ayarlamaya başlayın. 

Makinenizde bir komut istemi veya başka bir konsol penceresi açın ve bu öğreticide işlerinizi depolamak istediğiniz boş bir proje dizini oluşturun. Dizine istediğiniz her şeyi adlandırın (örneğin, *Digitaltwınscodeöğreticisi*).

Yeni dizine gidin.

Proje dizininde bir kez, **boş bir .NET konsol uygulaması projesi oluşturun**. Komut penceresinde, konsol için en az C# projesi oluşturmak üzere aşağıdaki komutu çalıştırabilirsiniz:

```cmd/sh
dotnet new console
```

Bu işlem, dizininizin içinde, kodunuzun büyük bir kısmını yazacağınız *program. cs* adlı birkaç dosya oluşturur.

Öğreticinin tamamında kullanmaya devam edebileceksiniz, komut penceresini açık tutun.

Ardından, projenize Azure dijital TWINS ile çalışmak için gerekli olacak **iki bağımlılık ekleyin** . Birincisi [.net Için Azure Digital TWINS SDK 'sının](/dotnet/api/overview/azure/digitaltwins/client)paketidir, Ikincisi ise Azure 'da kimlik doğrulamaya yardımcı olacak araçlar sağlar.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Proje kodu ile çalışmaya başlama

Bu bölümde, Azure dijital TWINS ile çalışmak üzere yeni uygulama projeniz için kod yazmaya başlayacaksınız. Kapsanan eylemler şunları içerir:
* Hizmette kimlik doğrulaması yapılıyor
* Model karşıya yükleme
* Yakalama hataları
* Dijital TWINS oluşturma
* İlişki oluşturma
* Dijital TWINS sorgulama

Öğreticinin sonundaki kodun tamamını gösteren bir bölüm de vardır. Bunu, programınızı hareket halindeyken denetlemek için bir başvuru olarak kullanabilirsiniz.

Başlamak için, herhangi bir kod düzenleyicisinde *program. cs* dosyasını açın. Aşağıdakine benzer bir kod şablonu görürsünüz:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Örnek kod parçacığı. Digitaltwınscodeöğretici adlı bir ad alanı olan bir ' using System; ' deyimidir. ad alanındaki program adlı bir sınıf; ve ' static void Main (String [] args) ' standart imzasına sahip sınıfta bir Main yöntemi. Main yöntemi bir Merhaba Dünya Print ifadesini içerir." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

İlk olarak, `using` gerekli bağımlılıkları çekmek için kodun üst kısmına bazı satırlar ekleyin.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Daha sonra, bazı işlevleri doldurmanız için bu dosyaya kod ekleyeceksiniz. 

### <a name="authenticate-against-the-service"></a>Hizmette kimlik doğrulama

Uygulamanızın yapması gereken ilk şey, Azure dijital TWINS hizmetinde kimlik doğrulaması gerçekleştirir. Ardından, SDK işlevlerine erişmek için bir hizmet istemci sınıfı oluşturabilirsiniz.

Kimlik doğrulamak için, Azure dijital TWINS örneğinizin *ana bilgisayar adına* ihtiyacınız vardır.

*Program. cs*' de, aşağıdaki kodu "Hello, World!" altına yapıştırın yöntemdeki çıktı satırı `Main` . Değerini `adtInstanceUrl` Azure Digital TWINS örneğiniz *ana bilgisayar adına* ayarlayın.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

Dosyayı kaydedin. 

Komut pencerenizde şu komutla kodu çalıştırın: 

```cmd/sh
dotnet run
```

Bu işlem, ilk çalıştırmada bağımlılıkları geri yükler ve ardından programı yürütür. 
* Bir hata oluşursa, program *oluşturulan hizmet istemcisini Yazdır-başlamaya hazırlanın*.
* Bu projede henüz bir hata işleme olmadığından, herhangi bir sorun oluşursa, kod tarafından oluşturulan bir özel durum görürsünüz.

### <a name="upload-a-model"></a>Bir modeli karşıya yükleyin

Azure dijital TWINS 'in hiç iç etki alanı sözlüğü yok. Ortamınızdaki Azure dijital TWINS 'de temsil ettiğiniz öğelerin türleri, **modeller** kullanılarak sizin tarafınızdan tanımlanır. [Modeller](concepts-models.md) , nesne odaklı programlama dillerinde sınıflarla benzerdir; Bunlar, daha sonra izlemek ve örneklendirilecek [dijital TWINS](concepts-twins-graph.md) için Kullanıcı tanımlı şablonlar sağlar. Bunlar, **dijital TWINS tanım dili (DTDL)** adlı JSON benzeri bir dilde yazılır.

Azure dijital TWINS çözümü oluşturmanın ilk adımı, bir DTDL dosyasında en az bir model tanımlıyor.

Projenizi oluşturduğunuz dizinde, *üzerindeSampleModel.js* adlı yeni bir *. JSON* dosyası oluşturun. Aşağıdaki dosya gövdesine yapıştırın: 

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Bu öğretici için Visual Studio kullanıyorsanız, yeni oluşturulan JSON dosyasını seçmek ve Özellik denetçisindeki *Çıkış Dizinine Kopyala* özelliğini, *daha yeniyse* veya *her zaman* kopyalamak üzere ayarlamak isteyebilirsiniz. Bu, öğreticinin geri kalanı sırasında program 'yu **F5** ile çalıştırdığınızda, Visual Studio 'nun varsayılan yol ile json dosyasını bulmasını sağlayacaktır.

> [!TIP] 
> DTDL 'nin geçerli olduğundan emin olmak için model belgelerini denetlemek için kullanabileceğiniz dilden bağımsız bir [Dtdl doğrulayıcısı örneği](/samples/azure-samples/dtdl-validator/dtdl-validator) vardır. Bu, [*nasıl yapılır: modellerinizi ayrıştırma ve doğrulama*](how-to-parse-models.md)hakkında daha fazla bilgi edinmek için dtdl ayrıştırıcısı kitaplığı üzerine kurulmuştur.

Daha sonra, yeni oluşturduğunuz modeli Azure dijital TWINS Örneğinizde karşıya yüklemek için *program. cs* dosyasına daha fazla kod ekleyin.

İlk olarak, `using` dosyanın en üstüne birkaç deyim ekleyin:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Daha sonra, `Main` yöntem imzasını zaman uyumsuz yürütmeye izin verecek şekilde değiştirerek C# hizmet SDK 'sindeki zaman uyumsuz yöntemleri kullanmaya hazırlanın. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> `async`SDK, tüm çağrıların zaman uyumlu sürümlerini de sağladığından, kullanılması kesinlikle gerekli değildir. Bu öğreticiyi kullanma yöntemleri `async` .

Daha sonra, Azure dijital TWINS hizmetiyle etkileşim kuran kodun ilk biti gelir. Bu kod, diskinizde oluşturduğunuz DTDL dosyasını yükler ve Azure dijital TWINS hizmet örneğinize yükler. 

Daha önce eklediğiniz yetkilendirme kodu altına aşağıdaki kodu yapıştırın.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

Komut pencerenizde, programı şu komutla çalıştırın: 

```cmd/sh
dotnet run
```
"Bir modeli karşıya yükle", bu koda ulaşıldığını belirten çıktıda yazdırılır, ancak henüz karşıya yüklemenin başarılı olup olmadığını belirtmek için bir çıktı yok.

Örneğe başarıyla yüklenmiş tüm modelleri gösteren bir Print ifadesini eklemek için, önceki bölümden hemen sonra aşağıdaki kodu ekleyin:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Bu yeni kodu test etmek için programı yeniden çalıştırmadan önce**, programı son kez çalıştırdığınızda modelinizi zaten karşıya yüklediğinize geri çekin. Azure dijital TWINS aynı modeli iki kez karşıya yüklemeye izin vermeyecektir. bu nedenle, aynı modeli yeniden yüklemeye çalışırsanız programın bir özel durum oluşturması gerekir.

Bu göz önünde bulundurularak, komut pencerenizde bu komutla programı yeniden çalıştırın:

```cmd/sh
dotnet run
```

Program bir özel durum oluşturması gerekir. Zaten karşıya yüklenmiş bir modeli karşıya yüklemeye çalıştığınızda hizmet, REST API aracılığıyla bir "Hatalı istek" hatası döndürür. Sonuç olarak, Azure Digital TWINS istemci SDK 'Sı, başarılı dışında her hizmet dönüş kodu için bir özel durum oluşturur. 

Sonraki bölümde, bu gibi özel durumlar ve kodunuzda nasıl ele alınacağını görülüyor.

### <a name="catch-errors"></a>Hataları yakala

Programın çökmesini önlemek için, model karşıya yükleme kodunun etrafına özel durum kodu ekleyebilirsiniz. Mevcut istemci çağrısını `await client.CreateModelsAsync(typeList)` bir Try/Catch işleyicisine kaydırın, örneğin:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Şimdi komut pencerenizde programını ile çalıştırırsanız `dotnet run` , bir hata kodu geri almanızı göreceksiniz. Model oluşturma kodundaki çıkış şu hatayı gösterir:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Program çıktısı, ' 409: hizmet isteği başarısız oldu iletisini gösterir. Durum: 409 (çakışma). ', ardından dtmı: örnek: SampleModel; 1 olduğunu belirten bir hata çıktısı var":::

Bu noktadan itibaren öğreticide, try/catch işleyicilerindeki hizmet yöntemlerine yapılan tüm çağrılar kaydırılır.

### <a name="create-digital-twins"></a>Dijital ikizleri oluşturma

Azure dijital TWINS 'e bir model yüklediğinize göre, bu model tanımını **dijital TWINS** oluşturmak için kullanabilirsiniz. [Dijital TWINS](concepts-twins-graph.md) , bir modelin örnekleridir ve iş ortamınızdaki varlıkları, bir gruptaki sensörlerden, binadaki odaların veya bir otomobilde ışıkların bulunduğu şeyleri temsil eder. Bu bölümde, daha önce karşıya yüklediğiniz modele göre birkaç dijital TWINS oluşturulur.

`Main`Bu modele göre üç dijital TWINS oluşturmak ve başlatmak için yönteminin sonuna aşağıdaki kodu ekleyin.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

Komut pencerenizde, programını ile çalıştırın `dotnet run` . Çıktıda, *sampleTwin-0*, *sampleTwin-1* ve *sampleTwin-2* ' nin oluşturulduğu yazdırma iletilerini arayın. 

Sonra, programı yeniden çalıştırın. 

TWINS, ilk çalıştırmadan sonra zaten mevcut olsa bile, ikinci kez oluşturulan bir hata olmadığını unutmayın. Model oluşturma işleminden farklı olarak, ikizi oluşturma, *upsert* semantik bir *PUT* çağrısı olan Rest düzeyinde olur. Yani, bir ikizi zaten varsa, aynı ikizi yeniden oluşturma girişimi yalnızca özgün ikizi yerine geçecek. Hiçbir hata oluşturulmaz.

### <a name="create-relationships"></a>İlişki oluşturma

Daha sonra, oluşturduğunuz TWINS arasında **ilişkiler** oluşturarak bunları bir **ikizi grafiğine** bağlayabilirsiniz. [İkizi grafikleri](concepts-twins-graph.md) , tüm ortamınızı temsil etmek için kullanılır.

Yöntemine, yönteminin altına **Yeni bir static yöntem** ekleyin `Program` `Main` (şimdi kodda iki yöntem vardır):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Sonra, yöntemi `Main` çağırmak `CreateRelationship` ve yeni yazdığınız kodu kullanmak için yönteminin sonuna aşağıdaki kodu ekleyin:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

Komut pencerenizde, programını ile çalıştırın `dotnet run` . Çıktıda, iki ilişkinin başarıyla oluşturulduğunu belirten Print deyimlerini arayın.

Aynı KIMLIĞE sahip başka bir ilişki zaten varsa Azure Digital TWINS 'in bir ilişki oluşturmanıza izin vermediğini unutmayın. programı birden çok kez çalıştırırsanız, ilişki oluşturma konusunda özel durumlar görürsünüz. Bu kod özel durumları yakalar ve bunları yoksayar. 

### <a name="list-relationships"></a>Liste ilişkileri

Ekleyeceğiniz bir sonraki kod, oluşturduğunuz ilişkilerin listesini görmenizi sağlar.

Sınıfına aşağıdaki **yeni yöntemi** ekleyin `Program` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Daha sonra, `Main` kodu çağırmak için yönteminin sonuna aşağıdaki kodu ekleyin `ListRelationships` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

Komut pencerenizde, programını ile çalıştırın `dotnet run` . Aşağıdaki gibi görünen bir çıktı bildiriminde oluşturduğunuz tüm ilişkilerin bir listesini görmeniz gerekir:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="' Ikizi sampleTwin-0 ' a bağlı olduğunu belirten bir ileti gösteren program çıktısı: Contains->sampleTwin-1,-Contains->sampleTwin-2 '" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Dijital TWINS sorgula

Azure dijital TWINS 'in ana özelliği, ortamınız hakkında soruları yanıtlamak için ikizi grafınızı kolayca ve verimli bir şekilde [sorgulayabilir](concepts-query-language.md) .

Bu öğreticide eklenecek kodun son bölümü Azure dijital TWINS örneğinde bir sorgu çalıştırır. Bu örnekte kullanılan sorgu, örnekteki tüm dijital TWINS 'i döndürür.

Bu `using` ifadeyi, `JsonSerializer` dijital ikizi bilgilerinin sunulmasını sağlamak üzere sınıfının kullanımını etkinleştirmek için ekleyin:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Ardından, yönteminin sonuna aşağıdaki kodu ekleyin `Main` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

Komut pencerenizde, programını ile çalıştırın `dotnet run` . Çıktıda bu örnekteki tüm dijital TWINS ' i görmeniz gerekir.

## <a name="complete-code-example"></a>Kod örneğini doldurun

Öğreticide bu noktada, Azure dijital TWINS 'e yönelik temel eylemleri gerçekleştirmenize yönelik, tüm istemci uygulamanız vardır. Başvuru için, program *. cs* dosyasındaki programın tam kodu aşağıda listelenmiştir:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu Öğreticiyi tamamladıktan sonra, ne yapmak istediğinize bağlı olarak kaldırmak istediğiniz kaynakları seçebilirsiniz.

* **Sonraki öğreticiye devam etmeyi planlıyorsanız**, bu öğreticide kullanılan örnek bir sonraki adımda yeniden kullanılabilir. Burada ayarladığınız Azure dijital TWINS kaynaklarını tutabilir ve bu bölümün geri kalanını atlayabilirsiniz.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Yerel makinenizden proje klasörünü de silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, sıfırdan bir .NET konsol istemci uygulaması oluşturdunuz. Bu istemci uygulamanın kodunu bir Azure dijital TWINS örneği üzerinde temel eylemleri gerçekleştirecek şekilde yazmış olursunuz.

Örnek bir istemci uygulamasıyla yapabileceklerinizden birini araştırmak için sonraki öğreticiye geçin: 

> [!div class="nextstepaction"]
> [*Öğretici: örnek bir istemci uygulamasıyla ilgili temel bilgileri araştırma*](tutorial-command-line-app.md)
