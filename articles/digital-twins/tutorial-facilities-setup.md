---
title: 'Öğretici: Azure dijital TWINS dağıtma'
description: Azure dijital İkizlerini örneğinizi dağıtma ve bu öğreticideki adımları kullanarak uzamsal kaynaklarınızı yapılandırmak hakkında bilgi edinin.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 4d4e7e47b82c46cca53fab0540a4867031eaab85
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107749"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Öğretici: Azure dijital TWINS önizlemesi dağıtma ve uzamsal bir grafik yapılandırma

Azure Digital TWINS önizleme hizmetini kullanarak, kişileri, yerleri ve cihazları tutarlı bir uzamsal sisteme bir araya getirebilirsiniz. Bu öğretici serisinde, Azure dijital İkizlerini odası doluluk sıcaklık ve Uzaktan kalite en uygun koşullarla algılamak için nasıl kullanılacağını gösterir. 

Bu öğreticiler bir ofis binasındaki bir senaryo oluşturmak için bir .NET konsol uygulaması size yol gösterir. Yapı, her zemin içinde birden çok Katlar ve odaları sahiptir. Odaları ile ortam sıcaklığı, hareket algılayan ve kalite hava, bağlı sensörlerden cihazları içerir. 

Azure dijital İkizlerini hizmetini kullanarak varlıkları dijital nesneleri oluşturma ve fiziksel alanları çoğaltmak öğreneceksiniz. Başka bir konsol uygulaması kullanarak cihaz olaylarının benzetimini yapma. Ardından, bu fiziksel alanları ve varlıkları neredeyse gerçek zamanlı olarak gelen olayları izlemek nasıl öğreneceksiniz. 

Ofis yöneticileri bu tür bilgileri kullanarak bu binada görevli bir çalışanın en uygun koşullara sahip toplantı odalarını ayırması konusunda yardımcı olabilir. Bir office tesis Yöneticisi kurulumunuzu odaları kullanım eğilimlerini almak ve bakım amacıyla çalışma koşullarına izlemek için kullanabilirsiniz.

Bu serinin ilk öğreticisinde aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Dijital İkizlerini dağıtın.
> * Uygulamanıza izinler verir.
> * Dijital İkizlerini örnek bir uygulama değiştirin.
> * Yapı sağlayın.

Bu öğreticilerde [uygun odaları bulma hızlı başlangıcındaki](quickstart-view-occupancy-dotnet.md) örnekler kullanılmakta, kavramlar daha ayrıntılı bir şekilde ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure hesabınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- .NET Core SDK. Aşağıdaki öğreticilerde kullanılan dijital İkizlerini Azure örnekleri yazılan C#. Yüklediğinizden emin olun [.NET Core SDK'sı sürüm 2.1.403 veya üzeri](https://www.microsoft.com/net/download) geliştirme makinenizde derlemek ve örnek çalıştırmak için. Çalıştırarak doğru sürümü, makinenizde yüklü olduğunu denetlemek `dotnet --version` komut penceresinde.

- Örnek kodu incelemek için [Visual Studio Code](https://code.visualstudio.com/). 

## <a name="deploy-digital-twins"></a>Digital Twins'i dağıtma

Azure dijital İkizlerini hizmetinin yeni bir örneğini oluşturmak için bu bölümdeki adımları kullanın. Abonelik başına yalnızca bir örneği oluşturulabilir. Bir çalıştırma zaten varsa, sonraki bölüme atlayın. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Uygulamanıza izin verme

Dijital İkizlerini kullanır [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (denetlemek için Azure AD) [okuma/yazma erişimi](../active-directory/develop/v1-permissions-and-consent.md) hizmeti. Dijital İkizlerini Örneğinize bağlanmak için gereken herhangi bir uygulamadan Azure AD'ye kayıtlı olması gerekir. Bu bölümde örnek uygulamanızı kaydetme adımları gösterilmektedir.

Bir uygulama kaydı zaten varsa, Örneğiniz için yeniden kullanabilirsiniz. Ancak bu bölümdeki adımları izleyerek uygulama kaydınızın doğru yapılandırmaya sahip olduğundan emin olun.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Dijital İkizlerini örnek yapılandırma

Bu bölümde, iletişim kuran Azure dijital İkizlerini uygulamanın size [dijital İkizlerini REST API'leri](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Örneği indirme

[Uygun odaları bulma hızlı başlangıcı](quickstart-view-occupancy-dotnet.md) için bu örnekleri daha önce indirdiyseniz bu adımları atlayabilirsiniz.

1. İndirme [dijital İkizlerini .NET örnekleri](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Makinenizde posta klasörünün içeriğini ayıklayın.

### <a name="explore-the-sample"></a>Örneği keşfetme

Ayıklanan örnek klasöründe dosyasını açın **digital-twins-samples-csharp\digital-twins-samples.code-workspace** Visual Studio code'da. Bu dosyada iki proje bulunur:

* Sağlama örneği kullanabilirsiniz **doluluk-quickstart** yapılandırmak ve sağlamak için bir [uzamsal zeka graf](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Bu grafik, fiziksel alanları ve bunları kaynakları sayısal görüntüsüdür. Bunu kullanan bir [nesne modeli](concepts-objectmodel-spatialgraph.md#digital-twins-object-models), nesneler için akıllı bir yapı tanımlar. Dijital İkizlerini nesneleri ve REST API'lerinin tam listesi için ziyaret [bu REST API belgelerini](https://docs.westcentralus.azuresmartspaces.net/management/swagger) veya yönetim API'si URL'si için oluşturulan [örneğinizin](#deploy-digital-twins).

   Örnek dijital İkizlerini örneğiniz ile nasıl iletişim kurduğu görmek için keşfetmek için ile başlayabilirsiniz **src\actions** klasör. Bu klasördeki dosyalar bu öğreticilerde kullanacağınız komutları uygulayın:
    - **ProvisionSample.cs** dosya uzamsal grafınızı sağlamak nasıl gösterir.
    - **GetSpaces.cs** dosya sağlanan alanları hakkında bilgi alır.
    - **GetAvailableAndFreshSpaces.cs** dosya bir kullanıcı tanımlı işlev olarak adlandırılan özel bir işlev sonucunu alır.
    - **CreateEndpoints.cs** diğer hizmetlerle etkileşim için uç dosyası oluşturur.

* Benzetim örneği **cihaz bağlantısı** sensör verilerini benzetimini yapar ve dijital İkizlerini Örneğiniz için sağlanan IOT hub'ına gönderir. Bu örnekte kullanacağınız [uzamsal grafınızı sağladıktan sonra sonraki öğreticiye](tutorial-facilities-udf.md#simulate-sensor-data). Bu örneği yapılandırmak için kullandığınız sensör ve cihaz tanımlayıcıları grafınızı sağlamak için kullanacaksınız ile aynı olması gerekir.

### <a name="configure-the-provisioning-sample"></a>Sağlama örneğini yapılandırma

1. Bir komut penceresi açın ve indirilen örneğe gidin. Şu komutu çalıştırın:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Şu komutu çalıştırarak bağımlılıkları örnek projeye geri yükleyin:

    ```cmd/sh
    dotnet restore
    ```

1. Visual Studio Code'da açmak [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) dosyası **doluluk-quickstart** proje. Aşağıdaki değerleri güncelleştirin:
   * **ClientID**: Azure AD uygulama kaydınızı uygulama Kimliğini girin. Bölümünde bu kimliği not ettiğiniz Burada, [uygulama izinleri ayarla](#grant-permissions-to-your-app).
   * **Kiracı**:, dizin kimliği girin, [Azure AD kiracısı](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Ayrıca bu kimliği bölümünde belirtildiği Burada, [uygulama izinleri ayarla](#grant-permissions-to-your-app).
   * **BaseUrl**: Digital Twins örneğinizin URL'sini girin. Bu URL 'YI almak için, bu URL 'deki yer tutucuları örneğinizin değerleriyle değiştirin: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Yönetim API'si URL'den değiştirerek bu URL'yi alabilirsiniz [dağıtım bölümü](#deploy-digital-twins). Değiştirin **swagger /** ile **api/v1.0/** .

1. Örnek kullanarak keşfedebilirsiniz dijital İkizlerini özelliklerin bir listesi bakın. Şu komutu çalıştırın:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Sağlama işlemini anlama

Bu bölümde örnekte binanın uzamsal grafının nasıl sağlandığı gösterilmektedir.

Visual Studio Code'da Gözat **doluluk quickstart\src\actions** klasörü ve dosyayı açın **provisionSample.cs**. Aşağıdaki işleve dikkat edin:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Bu işlev kullanır [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) aynı klasörde yer alan. Bu dosyayı açın ve bir ofis binasındaki hiyerarşisini unutmayın: *mekan*, *kat*, *alan*, ve *odaları*. Bu fiziksel alanların herhangi birinde *cihazlar* ve *sensörler* bulunabilir. Her girişin bir önceden tanımlanmış sahip `type` &mdash;Örneğin, Floor, yer.

Örnek **yaml** dosyasını kullanan olan bir uzamsal grafiği gösterir `Default` dijital İkizlerini nesne modeli. Bu model türlerinin çoğu için genel adlar sağlar. Genel adlar bir yapı için yeterlidir. Örnek SensorDataType için sıcaklık ve için SpaceBlobType eşleyin. Bir örnek alanı subtypes FocusRoom oda, ConferenceRoom ve benzeri türüdür. 

Fabrika gibi farklı bir mekan için uzamsal graf oluşturmanız gerekmesi durumunda başka bir nesne modeline ihtiyaç duyabilirsiniz. Hangi modelleri komutunu çalıştırarak kullanılabilir olduğunu öğrenmek `dotnet run GetOntologies` sağlama örneğinin komut satırında. 

Uzamsal graflar ve nesne modelleri hakkında daha fazla bilgi için okuma [dijital İkizlerini anlama modelleri ve uzamsal zeka graf nesnesi](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Örnek uzamsal grafiğe değiştirme

**ProvisionSample.yaml** dosyası aşağıdaki düğümleri içerir:

- **Kaynakları**: `resources` kurulumunuzu aygıtları ile iletişim kurmak için bir Azure IOT hub'ı kaynak düğümü oluşturur. IOT hub'ı, grafiğin kök düğümde, tüm cihazlardan ve sensörlerden grafınızı ile iletişim kurabilir.  

- **spaces**: Digital Twins nesne modelinde `spaces`, fiziksel konumları temsil eder. Her alana sahip bir `Type` &mdash;, bölge, mekan ya da müşteri&mdash;ve kolay bir `Name`. Alanları, hiyerarşik bir yapıyı başka alanları için ait olabilir. Sanal bir yapı uzamsal grafiğini provisionSample.yaml dosyası vardır. Mantıksal iç içe tür alanları Not `Floor` içinde `Venue`, `Area` içinde bir katı ve `Room` bir alan düğümleri. 

- **devices**: Alanlarda, bir dizi sensörü yöneten fiziksel veya sanal varlıklar olan `devices` öğeleri bulunabilir. Örneğin, bir cihaz bir kullanıcıya ait telefon, Raspberry Pi algılayıcı pod veya bir ağ geçidi olabilir. Örnekteki hayali binanın **Focus Room** adlı odasında bir **Raspberry Pi 3 A1** cihazı bulunmaktadır. Her cihaz düğümü, örneğe sabit kodlanmış benzersiz bir `hardwareId` değerine sahiptir. Bu örneği üretim amaçlı kullanım için yapılandırmak isterseniz bu değerleri kendi sisteminizdeki değerlerle değiştirmeniz gerekir.  

- **algılayıcılar**: bir cihaza birden çok içerebilir `sensors`. Bunlar algılayabilir ve sıcaklık, hareket ve pil düzeyi kayıt fiziksel değişiklikleri ister. Her sensör düğümü, burada sabit kodlanmış `hardwareId` değeriyle benzersiz olarak tanımlanmıştır. Gerçek bir uygulama için bu kurulumda sensörlerden öğesinin benzersiz tanımlayıcıları kullanarak değiştirin. ProvisionSample.yaml dosyayı kaydetmek için iki algılayıcılara sahiptir *hareket* ve *CarbonDioxide*. CarbonDioxide sensörünün tanımlandığı satırların altına aşağıdaki satırları ekleyerek *Temperature* (Sıcaklık) kaydı yapacak yeni bir sensör ekleyin. Bunlar provisionSample.yaml içinde derleme dışı bırakılan satır olarak verildiğini unutmayın. Bunları kaldırarak açıklamasını `#` kuyruğun her satırın karakter. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Emin `dataType` ve `hardwareId` anahtarları hizalama deyimleriyle yukarıdaki Bu kod parçacığı. Ayrıca düzenleyicinizin boşlukları sekmelerle değiştirmediğinden de emin olun. 

ProvisionSample.yaml dosyasını kaydedip kapatın. Sonraki öğreticide, daha fazla bilgi bu dosyaya ekleyin ve ardından, Azure dijital İkizlerini örnek yapı sağlamak.

> [!TIP]
> Görüntüleyebilir ve uzamsal graph aracılığıyla değiştirmek [Azure dijital İkizlerini graf Görüntüleyicisi](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu noktada Azure dijital İkizlerini keşfetmeye durdurmak istiyorsanız, bu öğreticide oluşturulan kaynakları silmek çekinmeyin:

1. Sol menüden [Azure portalında](https://portal.azure.com)seçin **tüm kaynakları**dijital İkizlerini kaynak grubunuzu seçin ve seçin **Sil**.

    > [!TIP]
    > Dijital İkizlerini örneğinizin silme sorun olduysa, bir hizmet güncelleştirmesi düzeltme alındı. Örneğiniz silme yeniden deneyin.

1. Gerekirse, iş makinenizde örnek uygulamayı silin.

## <a name="next-steps"></a>Sonraki adımlar

Koşullar oluşturma Örneğinizdeki izlemek için özel bir mantıksal uygulama hakkında bilgi edinmek için serideki sonraki öğretici gidin: 
> [!div class="nextstepaction"]
> [Öğretici: Binanızı sağlama ve çalışma koşullarını izleme](tutorial-facilities-udf.md)