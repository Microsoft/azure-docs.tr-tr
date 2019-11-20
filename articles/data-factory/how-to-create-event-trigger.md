---
title: Azure Data Factory olay tabanlı Tetikleyiciler oluşturma
description: Bir olaya yanıt olarak bir işlem hattı çalıştıran Azure Data Factory tetikleyici oluşturmayı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: 0b1d9fad2992397a3a6768d0f5e7ff26a400a2b3
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889320"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Bir olaya yanıt olarak bir işlem hattı çalıştıran bir tetikleyici oluşturma

Bu makalede, Data Factory işlem hatlarında oluşturabileceğiniz olay tabanlı tetikleyiciler açıklanmaktadır.

Olay odaklı mimari (EDA), olaylar için üretim, algılama, tüketim ve yeniden eyleme sahip ortak bir veri tümleştirme modelidir. Veri tümleştirme senaryoları genellikle Data Factory müşterilerin Azure Depolama hesabınızdaki bir dosyanın geliş veya silme gibi olaylara göre işlem hatlarını tetiklemesine gerek duyar. Data Factory artık, bir olayda işlem hatlarını tetiklemenizi sağlayan [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)ile tümleşiktir.

Bu özelliğin on dakikalık bir giriş ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Bu makalede açıklanan tümleştirme [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)bağımlıdır. Aboneliğinizin Event Grid kaynak sağlayıcısına kayıtlı olduğundan emin olun. Daha fazla bilgi için bkz. [kaynak sağlayıcıları ve türleri](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>Data Factory Kullanıcı Arabirimi (UI)

Bu bölümde, Azure Data Factory Kullanıcı arabiriminde nasıl bir olay tetikleyicisi oluşturacağınız gösterilmektedir.

1. **Yazma tuvaline** git

1. Sol alt köşede **Tetikleyiciler** düğmesine tıklayın

1. Tetikleyici oluşturma tarafı gezintisi ' ni açacak **+ Yeni** ' ye tıklayın

1. Tetikleyici türü **olayını** seçin

    ![Yeni olay tetikleyicisi oluştur](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Azure aboneliği açılan listesinden veya depolama hesabı kaynak KIMLIĞINI kullanarak el ile depolama hesabınızı seçin. Olayların gerçekleşmesini istediğiniz kapsayıcıyı seçin. Kapsayıcı seçimi isteğe bağlıdır, ancak tüm kapsayıcıları seçtiğinizde çok sayıda olaya yol açabilir.

   > [!NOTE]
   > Olay tetikleyicisi Şu anda yalnızca Azure Data Lake Storage 2. ve genel amaçlı sürüm 2 depolama hesaplarını desteklemektedir. Azure Event Grid sınırlaması nedeniyle, Azure Data Factory yalnızca depolama hesabı başına en fazla 500 olay tetikleyicisi destekler.

1. **BLOB yolu ile başlar** ve **BLOB yolu, özellikler ile biter** ve olayları almak istediğiniz kapsayıcıları, klasörleri ve BLOB adlarını belirtmenize olanak tanır. Olay Tetikleyiciniz bu özelliklerden en az birinin tanımlanmasını gerektirir. Her iki **BLOB yolu ile başlar** ve **BLOB yolu** , bu makalenin ilerleyen kısımlarında gösterildiği gibi özellikleriyle biter.

    * **BLOB yolu şununla başlar:** Blob yolu bir klasör yoluyla başlamalıdır. Geçerli değerler `2018/` ve `2018/april/shoes.csv`içerir. Kapsayıcı seçilmezse bu alan seçilemez.
    * **BLOB yolu şununla biter:** Blob yolu bir dosya adı veya uzantısıyla bitmelidir. Geçerli değerler `shoes.csv` ve `.csv`içerir. Kapsayıcı ve klasör adı isteğe bağlıdır, ancak belirtildiğinde, `/blobs/` kesimiyle ayrılmaları gerekir. Örneğin, ' Orders ' adlı bir kapsayıcı `/orders/blobs/2018/april/shoes.csv`bir değere sahip olabilir. Herhangi bir kapsayıcıdaki bir klasörü belirtmek için baştaki '/' karakterini atlayın. Örneğin `april/shoes.csv`, herhangi bir kapsayıcıda ' Nisan ' adlı klasördeki `shoes.csv` adlı herhangi bir dosyada bir olay tetikleyecektir. 

1. Tetikleyicinizin **BLOB tarafından oluşturulan** bir olaya, **BLOB Deleted** olayına veya her ikisine de yanıt verip vermeyeceğini seçin. Belirttiğiniz depolama konumunda, her olay tetikleyiciyle ilişkili Data Factory işlem hatlarını tetikler.

    ![Olay tetikleyicisini yapılandırma](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Tetikleyicinizin sıfır bayt içeren Blobları yoksayıp saymayacağını seçin.

1. Tetikleyiciyi yapılandırdıktan sonra Ileri ' ye tıklayın **: veri önizleme**. Bu ekranda, olay tetikleyicisi yapılandırmanızla eşleşen mevcut blob 'lar gösterilir. Belirli filtrelerinizin bulunduğundan emin olun. Çok geniş olan filtrelerin yapılandırılması, oluşturulan/silinen çok sayıda dosya ile eşleştirebilir ve maliyetinizi önemli ölçüde etkileyebilir. Filtre koşullarınız doğrulandıktan sonra **son**' a tıklayın.

    ![Olay tetikleyicisi veri önizleme](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Bu tetikleyiciye bir işlem hattı eklemek için işlem hattı tuvaline gidin ve **tetikleyici Ekle** ' ye tıklayın ve **Yeni/Düzenle**' yi seçin. Yan gezinti göründüğünde **tetikleyici seç...** açılan listesine tıklayın ve oluşturduğunuz tetikleyiciyi seçin. Ileri ' ye tıklayın: yapılandırmanın doğru olduğunu doğrulamak için **veri önizleme** ' **ye tıklayın ve ardından veri** önizlemenin doğru olduğundan emin olun.

1. İşlem hattınızda parametreler varsa, bunları tetikleyici parametre tarafı gezin ' i çalıştırır ' de belirtebilirsiniz. Olay tetikleyicisi, blob 'un klasör yolunu ve dosya adını `@triggerBody().folderPath` ve `@triggerBody().fileName`özelliklerine yakalar. Bu özelliklerin değerlerini bir işlem hattında kullanmak için, özellikleri işlem hattı parametrelerine eşlemeniz gerekir. Özellikleri parametrelere eşleştirdikten sonra, tetikleyici tarafından yakalanan değerlere işlem hattının tamamında `@pipeline().parameters.parameterName` ifadesi aracılığıyla erişebilirsiniz. İşiniz bittiğinde **son** ' a tıklayın.

    ![Özellikleri ardışık düzen parametrelerine eşleme](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Önceki örnekte tetikleyici, kapsayıcı örnek verilerinde. csv dosyasında bir blob yolu oluşturulduğunda,. csv ' de sonlanan bir blob yolu başlatıldığında tetiklenecek şekilde yapılandırılmıştır. **FolderPath** ve **filename** özellikleri yeni Blobun konumunu yakalar. Örneğin, MoviesDB. csv yolu örnek-Data/Event-Testing öğesine eklendiğinde, `@triggerBody().folderPath` bir `sample-data/event-testing` değerine sahiptir ve `@triggerBody().fileName` `moviesDB.csv`değeri vardır. Bu değerler örnekte `sourceFolder` işlem hattı parametrelerine eşlenir ve `sourceFile` ardışık düzen boyunca `@pipeline().parameters.sourceFolder` ve `@pipeline().parameters.sourceFile` olarak kullanılabilecek.

## <a name="json-schema"></a>JSON şeması

Aşağıdaki tabloda olay tabanlı tetikleyicilerle ilgili şema öğelerine genel bir bakış verilmiştir:

| **JSON öğesi** | **Açıklama** | **Tür** | **İzin verilen değerler** | **Gerekli** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **kapsam** | Depolama hesabının Azure Resource Manager kaynak KIMLIĞI. | Dize | Azure Resource Manager KIMLIĞI | Evet |
| **olayları** | Bu tetikleyicinin tetiklenmesine neden olan olayların türü. | Dizi    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Evet, bu değerlerin herhangi bir birleşimi. |
| **blobPathBeginsWith** | Blob yolu, tetikleyicinin tetiklenmesi için belirtilen Düzenle başlamalı. Örneğin, `/records/blobs/december/` yalnızca `records` kapsayıcısının altındaki `december` klasöründeki Blobların tetikleyicisini tetikler. | Dize   | | Şu özelliklerden en az biri için bir değer sağlamanız gerekir: `blobPathBeginsWith` veya `blobPathEndsWith`. |
| **blobPathEndsWith** | Blob yolu, tetikleyicinin tetiklenmesi için belirtilen Düzenle bitmelidir. Örneğin, `december/boxes.csv` yalnızca `december` bir klasörde `boxes` adlı bloblara yönelik tetikleyiciyi tetikler. | Dize   | | Şu özelliklerden en az biri için bir değer sağlamanız gerekir: `blobPathBeginsWith` veya `blobPathEndsWith`. |
| **ıgnoreemptyblob 'Lar** | Sıfır baytlık Blobların bir işlem hattı çalıştırmasını tetikleyip tetikleyemayacağı. Varsayılan olarak, bu true olarak ayarlanır. | Boole | true veya false | Hayır |

## <a name="examples-of-event-based-triggers"></a>Olay tabanlı tetikleyicilere örnekler

Bu bölümde olay tabanlı tetikleyici ayarlarının örnekleri verilmiştir.

> [!IMPORTANT]
> Yolun `/blobs/` segmentini, kapsayıcı ve klasör, kapsayıcı ve dosya ya da kapsayıcı, klasör ve dosya belirttiğinizde her seferinde aşağıdaki örneklerde gösterildiği gibi eklemeniz gerekir. **Blobpathbeginswith**için Data Factory kullanıcı ARABIRIMI, JSON tetikleyicisi içindeki klasör ve kapsayıcı adı arasında `/blobs/` otomatik olarak ekler.

| Özellik | Örnek | Açıklama |
|---|---|---|
| **Blob yolu şununla başlar** | `/containername/` | Kapsayıcıdaki tüm Bloblar için olayları alır. |
| **Blob yolu şununla başlar** | `/containername/blobs/foldername/` | `containername` Container ve `foldername` klasöründeki tüm Bloblar için olayları alır. |
| **Blob yolu şununla başlar** | `/containername/blobs/foldername/subfoldername/` | Ayrıca, bir alt klasöre de başvurabilirsiniz. |
| **Blob yolu şununla başlar** | `/containername/blobs/foldername/file.txt` | `containername` kapsayıcısının altındaki `foldername` klasöründe `file.txt` adlı bir blob için olayları alır. |
| **Blob yolu şununla biter** | `file.txt` | Herhangi bir yolda `file.txt` adlı bir blob için olayları alır. |
| **Blob yolu şununla biter** | `/containername/blobs/file.txt` | Kapsayıcı `containername`altında `file.txt` adlı bir blob için olayları alır. |
| **Blob yolu şununla biter** | `foldername/file.txt` | Herhangi bir kapsayıcı altında `foldername` klasöründe `file.txt` adlı bir blob için olayları alır. |

## <a name="next-steps"></a>Sonraki adımlar
Tetikleyiciler hakkında ayrıntılı bilgi için bkz. işlem [hattı yürütme ve Tetikleyiciler](concepts-pipeline-execution-triggers.md#triggers).
