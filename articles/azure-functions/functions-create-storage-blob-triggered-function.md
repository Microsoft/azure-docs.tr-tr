---
title: Azure'da Blob depolama tarafından tetiklenen bir işlev oluşturma | Microsoft Docs
description: Azure Blob depolamaya eklenen öğeler tarafından çağrılan sunucusuz bir işlev oluşturmak için Azure İşlevlerini kullanın.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: azure-functions
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: b8855fa061e07ec77482ca960971db8252608b51
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096872"
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Azure Blob depolama ile tetiklenen bir işlev oluşturma

Azure Blob depolamada dosyalar karşıya yüklendiğinde veya güncelleştirildiğinde tetiklenen bir işlev oluşturma hakkında bilgi edinin.

![Günlüklerde iletiyi görüntüleyin.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Önkoşullar

+ [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/)'ni indirip yükleme.
+ Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-an-azure-function-app"></a>Azure İşlev uygulaması oluşturma

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![İşlev uygulaması başarıyla oluşturuldu.](./media/functions-create-first-azure-function/function-app-create-success.png)

Ardından, yeni işlev uygulamasında bir işlev oluşturun.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Blob depolama ile tetiklenen bir işlev oluşturma

1. İşlev uygulamanızı genişletin ve **İşlevler**'in yanındaki **+** düğmesine tıklayın. Bu, işlev uygulamanızdaki ilk işlevse **Portalda**'yı ve ardından **Devam**'ı seçin. Aksi takdirde üçüncü adıma geçin.

   ![Azure portalındaki İşlevler hızlı başlangıç sayfası](./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-portal.png)

1. **Diğer şablonlar**'ı ve ardından **Sonlandır ve şablonları görüntüle**'yi seçin.

    ![İşlevler hızlı başlangıcı diğer şablonlar](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

1. Arama alanına `blob` yazıp **Blob tetikleyici** şablonunu seçin.

1. İstenirse **Yükle**'yi seçerek Azure Depolama uzantısını ve işlev uygulaması için gerekli diğer bağımlılıkları yükleyin. Yükleme başarılı olduktan sonra **Devam**'ı seçin.

    ![Bağlama uzantılarını yükleme](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

1. Görüntünün altındaki tabloda belirtilen ayarları kullanın.

    ![Blob depolama ile tetiklenen bir işlev oluşturun.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Ayar | Önerilen değer | Açıklama |
    |---|---|---|
    | **Name** | İşlev uygulamanızda benzersiz olmalıdır | Blob ile tetiklenen bu işlevin adı. |
    | **Yolu**   | samples-workitems/{ad}    | İzlenmekte olan Blob depolamanın konumu. Blob’un dosya adı bağlamaya _name_ parametresi olarak geçirilir.  |
    | **Depolama hesabı bağlantısı** | AzureWebJobsStorage | İşlev uygulamanız tarafından kullanılmakta olan depolama hesabı bağlantısını kullanabilir veya yeni bir bağlantı oluşturabilirsiniz.  |

1. İşlevinizi oluşturmak için **Oluştur**'a tıklayın.

Ardından Azure Depolama hesabınıza bağlanıp **samples-workitems** kapsayıcısını oluşturun.

## <a name="create-the-container"></a>Kapsayıcı oluşturma

1. İşlevinizde **Tümleştir**'e tıklayın, **Belgeler**'i genişletin ve hem **Hesap adı** hem de **Hesap anahtarı** değerlerini kopyalayın. Depolama hesabına bağlanmak için bu kimlik bilgilerini kullanacaksınız. Depolama hesabınıza önceden bağlandıysanız 4. adıma geçin.

    ![Depolama hesabı bağlantısı için kimlik bilgilerini alın.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/) aracını çalıştırın, sol taraftaki bağlan simgesine tıklayın, **Depolama hesabı adı ve anahtarı kullan**'ı seçin ve **İleri**'ye tıklayın.

    ![Depolama Hesabı Gezgini aracını çalıştırın.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. 1 Adımda kopyaladığınız **Hesap adı** ve **Hesap anahtarı** değerlerini girin, **İleri**'ye ve ardından **Bağlan**'a tıklayın. 

    ![Depolama kimlik bilgilerini girin ve bağlanın.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Bağlı depolama hesabını genişletin, **Blob kapsayıcılar**’a sağ tıklayın, **Blob kapsayıcı oluştur**’a tıklayın, `samples-workitems` yazın ve ardından Enter tuşuna basın.

    ![Depolama kuyruğu oluşturun.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Artık bir blob kapsayıcısına sahip olduğunuza göre, kapsayıcıya bir dosya yükleyerek işlevi test edebilirsiniz.

## <a name="test-the-function"></a>İşlevi test etme

1. Azure portalına dönün, işlevinizi bulun, sayfanın en altındaki **Günlükler** bölümünü genişletin ve günlük akışının duraklatılmış olmadığından emin olun.

1. Depolama Gezgini'nde depolama hesabınızı genişletin, **Blob kapsayıcıları** ve **samples-workitems** öğesine tıklayın. **Karşıya Yükle**’ye ve ardından **Dosyaları yükle...** seçeneğine tıklayın.

    ![Dosyayı blob kapsayıcısına yükleyin.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. **Dosyaları karşıya yükle** iletişim kutusunda **Dosyalar** alanına tıklayın. Yerel bilgisayarınızda görüntü dosyası gibi bir dosyaya gidin, seçin, **Aç**’ı ve ardından **Karşıya Yükle**’yi seçin.

1. İşlev günlüklerinize geri dönün ve blob’un okunduğunu doğrulayın.

   ![Günlüklerde iletiyi görüntüleyin.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > İşlev uygulamanız varsayılan Tüketim planında çalıştığında, blob’un eklenmesi veya güncelleştirilmesi ile işlevin tetiklenmesi arasında birkaç dakika gecikme olabilir. Blob ile tetiklenen işlevlerde düşük gecikme süresi gerekiyorsa, işlev uygulamanızı bir App Service planında çalıştırmayı düşünün.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Blob depolamaya bir blob eklendiğinde çalışacak bir işlev oluşturdunuz. Blob depolama tetikleyicileri hakkında daha fazla bilgi için bkz. [Azure İşlevleri Blob depolama bağlamaları](functions-bindings-storage-blob.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]