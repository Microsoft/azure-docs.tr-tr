---
title: Azure Data Factory olay tabanlı Tetikleyiciler oluşturma
description: Bir olaya yanıt olarak bir işlem hattı çalıştıran Azure Data Factory tetikleyici oluşturmayı öğrenin.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 3021d049a38f1d883518fc7c45aa8ca0a906c2f7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221594"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Bir depolama olayına yanıt olarak bir işlem hattı çalıştıran bir tetikleyici oluşturma

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Data Factory işlem hatlarında oluşturabileceğiniz depolama olay tetikleyicileri açıklanmaktadır.

Olay odaklı mimari (EDA), olaylar için üretim, algılama, tüketim ve yeniden eyleme sahip ortak bir veri tümleştirme modelidir. Veri tümleştirme senaryoları genellikle Data Factory müşterilerin Azure Blob depolama hesabındaki bir dosyayı alma veya silme gibi, depolama hesabında oluşan olaylara göre işlem hatlarını tetiklemesine gerek duyar. Data Factory, bu olaylar üzerinde işlem hatlarını tetiklemenizi sağlayan [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)yerel olarak tümleştirilir.

Bu özelliğin on dakikalık bir giriş ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> Bu makalede açıklanan tümleştirme [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)bağımlıdır. Aboneliğinizin Event Grid kaynak sağlayıcısına kayıtlı olduğundan emin olun. Daha fazla bilgi için bkz. [kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). *Microsoft. EventGrid/Eventabonelikleri/** eylemini yapabilmelisiniz. Bu eylem EventGrid EventSubscription katılımcısı yerleşik rolünün bir parçasıdır.

## <a name="data-factory-ui"></a>Data Factory Kullanıcı Arabirimi (UI)

Bu bölümde, Azure Data Factory Kullanıcı arabirimi içinde bir depolama olay tetikleyicisi oluşturma gösterilmektedir.

1. Kalem simgesiyle gösterilen **Düzenle** sekmesine geçin.

1. Menüde **tetikleyici** ' yi seçin ve ardından **Yeni/Düzenle**' yi seçin.

1. **Tetikleyici Ekle** sayfasında **tetikleyici seç...** öğesini seçin ve **+ Yeni**' yi seçin.

1. Tetikleyici türü **depolama olayı** seçin

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image1.png" alt-text="Data Factory Kullanıcı arabiriminde yeni bir depolama olay tetikleyicisi oluşturmak için yazar sayfasının ekran görüntüsü.":::

1. Azure aboneliği açılan listesinden veya depolama hesabı kaynak KIMLIĞINI kullanarak el ile depolama hesabınızı seçin. Olayların gerçekleşmesini istediğiniz kapsayıcıyı seçin. Kapsayıcı seçimi gereklidir, ancak tüm kapsayıcıları seçtiğinizde çok sayıda olaya yol açabilir.

   > [!NOTE]
   > Depolama olayı tetikleyicisi Şu anda yalnızca Azure Data Lake Storage 2. ve genel amaçlı sürüm 2 depolama hesaplarını desteklemektedir. Azure Event Grid sınırlaması nedeniyle, Azure Data Factory yalnızca depolama hesabı başına en fazla 500 depolama olay tetikleyicisi destekler. Sınıra ulaşırsanız, öneriler için desteğe başvurun ve Event Grid ekibi tarafından değerlendirme sırasında limiti arttırın. 

   > [!NOTE]
   > Yeni bir depolama olayı tetikleyicisi oluşturmak veya var olan bir depolama olay tetikleyicisini değiştirmek için Data Factory oturum açmak için kullanılan Azure hesabı depolama hesabında uygun rol tabanlı erişim denetimi (Azure RBAC) iznine sahip olmalıdır. Ek izin gerekmez: Azure Data Factory için hizmet sorumlusu, depolama hesabı veya Event Grid için özel _izin gerektirmez._ Erişim denetimi hakkında daha fazla bilgi için bkz. [rol tabanlı erişim denetimi](#role-based-access-control) bölümü.

1. **BLOB yolu ile başlar** ve **BLOB yolu, özellikler ile biter** ve olayları almak istediğiniz kapsayıcıları, klasörleri ve BLOB adlarını belirtmenize olanak tanır. Depolama olay Tetikleyiciniz, bu özelliklerden en az birinin tanımlanmasını gerektirir. Her iki **BLOB yolu ile başlar** ve **BLOB yolu** , bu makalenin ilerleyen kısımlarında gösterildiği gibi özellikleriyle biter.

    * **BLOB yolu şununla başlar:** Blob yolu bir klasör yoluyla başlamalıdır. Geçerli değerler `2018/` ve içerir `2018/april/shoes.csv` . Kapsayıcı seçilmezse bu alan seçilemez.
    * **BLOB yolu şununla biter:** Blob yolu bir dosya adı veya uzantısıyla bitmelidir. Geçerli değerler `shoes.csv` ve içerir `.csv` . Kapsayıcı ve klasör adları, belirtildiğinde, bir segmentle ayrılmaları gerekir `/blobs/` . Örneğin, ' Orders ' adlı bir kapsayıcının değeri olabilir `/orders/blobs/2018/april/shoes.csv` . Herhangi bir kapsayıcıdaki bir klasörü belirtmek için baştaki '/' karakterini atlayın. Örneğin, `april/shoes.csv` `shoes.csv` herhangi bir kapsayıcıda ' Nisan ' adlı klasörde adında bir olay tetikleyecektir.
    * Blob yolunun ile **başladığı** ve **bitişi** , depolama olay tetikleyicisinde izin verilen tek örüntü olduğunu unutmayın. Diğer joker karakter eşleştirme türleri tetikleyici türü için desteklenmez.

1. Tetikleyicinizin **BLOB tarafından oluşturulan** bir olaya, **BLOB Deleted** olayına veya her ikisine de yanıt verip vermeyeceğini seçin. Belirttiğiniz depolama konumunda, her olay tetikleyiciyle ilişkili Data Factory işlem hatlarını tetikler.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image2.png" alt-text="Depolama olay tetikleyicisi oluşturma sayfasının ekran görüntüsü.":::

1. Tetikleyicinizin sıfır bayt olan Blobları yoksayıp saymayacağını seçin.

1. Tetikleyiciyi yapılandırdıktan sonra Ileri ' ye tıklayın **: veri önizleme**. Bu ekranda, depolama olay tetikleyicisi yapılandırmanızla eşleşen mevcut blob 'lar gösterilir. Belirli filtrelerinizin bulunduğundan emin olun. Çok geniş olan filtrelerin yapılandırılması, oluşturulan/silinen çok sayıda dosya ile eşleştirebilir ve maliyetinizi önemli ölçüde etkileyebilir. Filtre koşullarınız doğrulandıktan sonra **son**' a tıklayın.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image3.png" alt-text="Depolama olayı tetikleyicisi önizleme sayfasının ekran görüntüsü.":::

1. Bu tetikleyiciye bir işlem hattı eklemek için işlem hattı tuvaline gidin ve **Tetikle** ' e tıklayın ve **Yeni/Düzenle**' yi seçin. Yan gezinti göründüğünde **tetikleyici seç...** açılan listesine tıklayın ve oluşturduğunuz tetikleyiciyi seçin. Ileri ' ye tıklayın: yapılandırmanın doğru olduğunu doğrulamak için **veri önizleme** ' **ye tıklayın ve ardından veri** önizlemenin doğru olduğundan emin olun.

1. İşlem hattınızda parametreler varsa, bunları tetikleyici parametre tarafı gezin ' i çalıştırır ' de belirtebilirsiniz. Depolama olayı tetikleyicisi, Blobun klasör yolunu ve dosya adını özelliklerine ve özelliklerine yakalar `@triggerBody().folderPath` `@triggerBody().fileName` . Bu özelliklerin değerlerini bir işlem hattında kullanmak için, özellikleri işlem hattı parametrelerine eşlemeniz gerekir. Özellikleri parametrelere eşleştirdikten sonra, tetikleyici tarafından yakalanan değerlere işlem `@pipeline().parameters.parameterName` hattı boyunca ifade aracılığıyla erişebilirsiniz. Ayrıntılı açıklama için bkz. işlem hatlarında [başvuru tetikleyici meta verileri](how-to-use-trigger-parameterization.md)

   :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image4.png" alt-text="Depolama olayı tetikleyicisi eşleme özelliklerinin işlem hattı parametrelerine yönelik ekran görüntüsü.":::

   Önceki örnekte tetikleyici, kapsayıcı _örnek verilerinde_. csv dosyasında bir blob yolu oluşturulduğunda,. csv _' de sonlanan_ bir blob yolu başlatıldığında tetiklenecek şekilde yapılandırılmıştır. **FolderPath** ve **filename** özellikleri yeni Blobun konumunu yakalar. Örneğin, yola MoviesDB.csv eklendiğinde-Data/Event-Testing `@triggerBody().folderPath` değeri, değerine sahiptir `sample-data/event-testing` ve değeri vardır `@triggerBody().fileName` `moviesDB.csv` . Bu değerler, örnekte işlem hattı parametrelerine `sourceFolder` ve `sourceFile` sırasıyla, ardışık düzen boyunca kullanılabilecek şekilde eşlenir `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` .

   > [!NOTE]
   > [Azure SYNAPSE Analytics](../synapse-analytics/overview-what-is.md)'te işlem hattınızı ve tetikleyiciyi oluşturuyorsanız, `@trigger().outputs.body.fileName` ve `@trigger().outputs.body.folderPath` parametreleri olarak kullanmanız gerekir. Bu iki özellik blob bilgilerini yakalar. Ve kullanmak yerine bu özellikleri kullanın `@triggerBody().fileName` `@triggerBody().folderPath` .

1. İşiniz bittiğinde **son** ' a tıklayın.

## <a name="json-schema"></a>JSON şeması

Aşağıdaki tabloda, depolama olay tetikleyicilerle ilgili şema öğelerine genel bir bakış sunulmaktadır:

| **JSON öğesi** | **Açıklama** | **Tür** | **İzin Verilen Değerler** | **Gerekli** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **kapsam** | Depolama hesabının Azure Resource Manager kaynak KIMLIĞI. | Dize | Azure Resource Manager KIMLIĞI | Yes |
| **olayları** | Bu tetikleyicinin tetiklenmesine neden olan olayların türü. | Dizi    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Evet, bu değerlerin herhangi bir birleşimi. |
| **blobPathBeginsWith** | Blob yolu, tetikleyicinin tetiklenmesi için belirtilen Düzenle başlamalı. Örneğin, `/records/blobs/december/` yalnızca `december` kapsayıcının altındaki klasörde bulunan bloblara yönelik tetikleyiciyi tetikler `records` . | Dize   | | Şu özelliklerden en az biri için bir değer belirtin: `blobPathBeginsWith` veya `blobPathEndsWith` . |
| **blobPathEndsWith** | Blob yolu, tetikleyicinin tetiklenmesi için belirtilen Düzenle bitmelidir. Örneğin, `december/boxes.csv` yalnızca bir klasörde adlı Bloblar için tetikleyiciyi harekete geçirilir `boxes` `december` . | Dize   | | Şu özelliklerden en az biri için bir değer belirtmeniz gerekir: `blobPathBeginsWith` veya `blobPathEndsWith` . |
| **ıgnoreemptyblob 'Lar** | Sıfır baytlık Blobların bir işlem hattı çalıştırmasını tetikleyip tetikleyemayacağı. Varsayılan olarak, bu true olarak ayarlanır. | Boole | true veya false | Hayır |

## <a name="examples-of-storage-event-triggers"></a>Depolama olay tetikleyicisi örnekleri

Bu bölümde, depolama olay tetikleyicisi ayarlarının örnekleri verilmiştir.

> [!IMPORTANT]
> `/blobs/`Her kapsayıcı ve klasör, kapsayıcı ve dosya ya da kapsayıcı, klasör ve dosya belirttiğinizde, aşağıdaki örneklerde gösterildiği gibi yolun segmentini eklemeniz gerekir. **Blobpathbeginswith** için, Data Factory kullanıcı ARABIRIMI, `/blobs/` JSON tetikleyicisi içindeki klasör ve kapsayıcı adı arasına otomatik olarak eklenir.

| Özellik | Örnek | Description |
|---|---|---|
| **Blob yolu şununla başlar** | `/containername/` | Kapsayıcıdaki tüm Bloblar için olayları alır. |
| **Blob yolu şununla başlar** | `/containername/blobs/foldername/` | Kapsayıcı ve klasördeki Bloblar için olayları alır `containername` `foldername` . |
| **Blob yolu şununla başlar** | `/containername/blobs/foldername/subfoldername/` | Ayrıca, bir alt klasöre de başvurabilirsiniz. |
| **Blob yolu şununla başlar** | `/containername/blobs/foldername/file.txt` | `file.txt` `foldername` Kapsayıcının altındaki klasörde adlı bir blob için olayları alır `containername` . |
| **Blob yolu şununla biter** | `file.txt` | Herhangi bir yolda adlı bir blob için olayları alır `file.txt` . |
| **Blob yolu şununla biter** | `/containername/blobs/file.txt` | Kapsayıcı altında adlı bir blob için olayları alır `file.txt` `containername` . |
| **Blob yolu şununla biter** | `foldername/file.txt` | `file.txt`Herhangi bir kapsayıcı altındaki klasöründe adlı bir blob için olayları alır `foldername` . |

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

Azure Data Factory, Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanarak, blob olaylarına bağlı olan işlem hatlarını dinlemek, onlara abone olmak ve tetiklemeye izin vermek için kesinlikle yasaktır.

* Yeni bir yeni oluşturmak veya mevcut bir depolama olay tetikleyicisini güncelleştirmek için Data Factory oturum açan Azure hesabının ilgili depolama hesabına uygun erişimi olması gerekir. Aksi takdirde, işlem _erişim reddedildi_ hatasıyla başarısız olur.
* Data Factory Event Grid için özel bir izin gerekmez ve işlem için Data Factory hizmet sorumlusuna özel RBAC izni _atamanız gerekmez._

Aşağıdaki RBAC ayarlarından herhangi biri depolama olay tetikleyicisi için geçerlidir:

* Depolama hesabına sahip rolü
* Depolama hesabına katkıda bulunan rolü
* _Microsoft. EventGrid/Eventabonelikler/_ depolama hesabına yazma izni _/Subscriptions/# # # #/resourceGroups/#_ # # #/Providers/Microsoft.Storage/storageAccounts/storageAccountName

Azure Data Factory iki ele geçirmesine neden olduğunu anlamak için, bir adım geri dönüp sahnenin arkasına gizli bir göz atalım. Data Factory, depolama ve Event Grid arasında tümleştirme için üst düzey iş akışları aşağıda verilmiştir.

### <a name="create-a-new-storage-event-trigger"></a>Yeni bir depolama olay tetikleyicisi oluştur

Bu üst düzey iş akışı, Azure Data Factory depolama olay tetikleyicisi oluşturmak için Event Grid nasıl etkileşime gireceğini açıklar

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="Depolama olayı tetikleyicisi oluşturma iş akışı.":::

İş akışlarından oluşan iki dikkat çekici çağrı:

* Azure Data Factory depolama hesabı _ile doğrudan iletişim yapmaz._ Abonelik oluşturma isteği Event Grid tarafından aktarılıp işlenir. Bu nedenle, bu adım için depolama hesabına izin Data Factory gerekir.

* Azure Data Factory tarafında erişim denetimi ve izin denetimi gerçekleşir. ADF, depolama olayına abone olmak için bir istek göndermeden önce kullanıcının iznini denetler. Özellikle, oturum açmış ve depolama olay tetikleyicisi oluşturmaya çalışan Azure hesabının ilgili depolama hesabına uygun erişimi olup olmadığını denetler. İzin denetimi başarısız olursa, tetikleyici oluşturma da başarısız olur.

### <a name="storage-event-trigger-data-factory-pipeline-run"></a>Depolama olayı tetikleyicisi Data Factory işlem hattı çalıştırması

Bu üst düzey iş akışları, depolama olayının işlem hattının nasıl çalışacağını Event Grid

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="İşlem hattı çalıştırılan depolama olayı iş akışı.":::

Data Factory içinde olay tetikleme ardışık düzenine geldiğinde, iş akışında üç dikkat çekici çağrı aşımları:

* Event Grid, depolama iletisini sisteme bırakmaları durumunda iletiyi en kısa sürede aktaran bir anında Iletme modeli kullanır. Bu, bir çekme sisteminin kullanıldığı Kafka gibi mesajlaşma sisteminden farklıdır.
* Azure Data Factory olay tetikleyicisi gelen iletiye etkin bir dinleyici olarak hizmet verir ve ilişkili ardışık düzeni düzgün şekilde tetikler.
* Depolama olayı tetikleyicisinin kendisi depolama hesabı ile doğrudan iletişim yapmaz

  * Bu şekilde, depolama hesabındaki verileri işlemek için işlem hattının içindeki bir kopya ya da başka bir etkinliğiniz varsa Data Factory, bağlantılı hizmette depolanan kimlik bilgilerini kullanarak depolama ile doğrudan iletişim kuracaktır. Bağlı hizmetin uygun şekilde ayarlandığından emin olun
  * Ancak, işlem hattındaki depolama hesabına herhangi bir başvuru yapmazsanız, depolama hesabına erişmek için Data Factory izin vermeniz gerekmez

## <a name="next-steps"></a>Sonraki adımlar

* Tetikleyiciler hakkında ayrıntılı bilgi için bkz. işlem [hattı yürütme ve Tetikleyiciler](concepts-pipeline-execution-triggers.md#trigger-execution).
* İşlem hattında tetikleyici meta verilerine nasıl başvurulacağını öğrenin, bkz. işlem [hattı çalıştırmalarıyla başvuru tetikleyici meta verileri](how-to-use-trigger-parameterization.md)
