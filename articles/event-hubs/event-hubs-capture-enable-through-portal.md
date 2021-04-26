---
title: Event Hubs-Azure portal kullanarak akış olaylarını yakala
description: Bu makalede, Azure portal kullanılarak Azure Event Hubs aracılığıyla olayların akışını yakalamaya nasıl etkinleştireceğinizi açıklanmaktadır.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 8c89a6e56c2d0bc7b7ff09d5fd63737009239eb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653960"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Azure Event Hubs aracılığıyla olay akışı yakalamayı etkinleştirme

Azure [Event Hubs yakalama][capture-overview] , akış verilerini Event Hubs bir [Azure Blob depolama alanına](https://azure.microsoft.com/services/storage/blobs/) veya [Azure Data Lake Storage 1. ya da Gen 2](https://azure.microsoft.com/services/data-lake-store/) hesabına otomatik olarak sunmanıza olanak sağlar.

[Azure portalını](https://portal.azure.com) kullanarak olay hub'ı oluşturma sırasında Yakalama özelliğini yapılandırabilirsiniz. Verileri bir Azure [BLOB depolama](https://azure.microsoft.com/services/storage/blobs/) kapsayıcısına veya [Azure Data Lake Storage Gen 1 veya Gen 2](https://azure.microsoft.com/services/data-lake-store/) hesabına yakalayabilirsiniz.

Daha fazla bilgi için bkz. [Event Hubs Capture'a genel bakış][capture-overview].

> [!IMPORTANT]
> Hedef depolama (Azure depolama veya Azure Data Lake Storage) hesabı, Olay Hub 'ı ile aynı abonelikte olmalıdır.

## <a name="capture-data-to-azure-storage"></a>Verileri Azure depolama 'da yakala

Bir olay hub’ı oluşturduğunuzda **Olay Hub'ı oluştur** portal ekranında **Aç** düğmesine tıklayarak Yakalama özelliğini etkinleştirebilirsiniz. Ardından **Yakalama Sağlayıcısı** kutusunda **Azure Storage**’a tıklayarak bir Depolama Hesabı ve kapsayıcı belirtirsiniz. Event Hubs Yakalama özelliği, depolama ile hizmetten hizmete kimlik doğrulama kullandığından depolama bağlantı dizesi belirtmenize gerek yoktur. Kaynak seçici, depolama hesabınız için kaynak URI'sini otomatik olarak seçer. Azure Resource Manager kullanıyorsanız bu URI'yi dize olarak açıkça belirtmeniz gerekir.

Zaman penceresi varsayılan olarak 5 dakikadır. En düşük değer 1, en yüksek değer ise 15'tir. **Boyut** penceresi 10-500 MB aralığındadır.

![Yakalama için zaman penceresi][1]

> [!NOTE]
> Yakalama penceresi sırasında hiçbir olay gerçekleşmeyecek boş dosyaları yaymayı etkinleştirebilir veya devre dışı bırakabilirsiniz. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 ' ye veri yakala 

1. Bir Azure depolama hesabı oluşturmak için [depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) makalesini izleyin. Azure Data Lake Storage Gen 2 hesabı yapmak için, **Gelişmiş** sekmesinde **hiyerarşik ad alanını** **etkin** olarak ayarlayın.
2. Bir olay hub 'ı oluştururken aşağıdaki adımları uygulayın: 

    1. **Yakalama** için **Açık** seçeneğini belirleyin. 
    2. Yakalama sağlayıcısı olarak **Azure Storage** ' ı seçin. **Yakalama sağlayıcısı** için gördüğünüz **Azure Data Lake Store** seçeneği, Azure Data Lake Storage Gen 1 ' dir. Azure Data Lake Storage Gen 2 ' yi kullanmak için **Azure Storage**' ı seçin.
    2. **Kapsayıcı Seç** düğmesini seçin. 

        ![Data Lake Storage Gen 2 ' ye yakalamayı etkinleştir](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Listeden **Azure Data Lake Storage Gen 2** hesabını seçin. 

    ![Data Lake Storage Gen 2 ' yi seçin](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. **Kapsayıcıyı** seçin (Data Lake Storage Gen 2 ' deki dosya sistemi).

    ![Depolama alanında dosya sistemi seçin](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. **Olay Hub 'ı oluştur** sayfasında **Oluştur**' u seçin. 

    ![Oluştur düğmesini seçin](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Bu Kullanıcı arabirimini (UI) kullanarak Azure Data Lake Storage Gen 2 içinde oluşturduğunuz kapsayıcı, **Depolama Gezgini** **dosya sistemleri** altında gösterilir. Benzer şekilde, bir Data Lake Storage Gen 2 hesabında oluşturduğunuz dosya sistemi, bu kullanıcı arabirimindeki bir kapsayıcı olarak gösterilir. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 ' e veri yakala 

Azure Data Lake Storage Gen 1 ' e veri yakalamak için, bir Data Lake Storage Gen 1 hesabı ve bir olay hub 'ı oluşturursunuz:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Azure Data Lake Storage Gen 1 hesabı ve klasörleri oluşturma

1. [Azure Portal kullanarak Azure Data Lake Storage Gen 1 ile çalışmaya başlama](../data-lake-store/data-lake-store-get-started-portal.md)bölümündeki yönergeleri izleyerek Data Lake Storage hesabı oluşturun.
2. [Event Hubs Izinleri ata](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) bölümünde yer alan yönergeleri izleyerek Event Hubs verileri yakalamak Istediğiniz Data Lake Storage Gen 1 hesabında bir klasör oluşturun ve Data Lake Storage Gen 1 hesabınıza veri yazabilmesi için Event Hubs izinler atayın.  


### <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

1. Olay Hub 'ı, oluşturduğunuz Azure Data Lake Storage Gen 1 hesabıyla aynı Azure aboneliğinde olmalıdır. **Event Hub'ı oluştur** portal sayfasının **Yakalama** bölümünde **Açık** düğmesine tıklayarak olay hub'ını oluşturun. 
2. **Event Hub'ı oluştur** portal sayfasında, **Yakalama Sağlayıcısı** kutusundan **Azure Data Lake Store**’u seçin.
3. **Data Lake Store** açılır listesinin yanındaki **Mağaza Seç** ' de, daha önce oluşturduğunuz Data Lake Storage Gen 1 hesabını belirtin ve **Data Lake yolu** alanına oluşturduğunuz veri klasörünün yolunu girin.

    ![Data Lake Storage hesabı seçin][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Mevcut bir olay hub'ında Yakalama özelliğini yapılandırma

Event Hubs ad alanlarında mevcut olan olay hub'ları üzerinde Yakalama özelliğini yapılandırabilirsiniz. Yakalama özelliğini mevcut bir olay hub'ında etkinleştirmek veya Yakalama ayarlarınızı değiştirmek için ad alanına tıklayarak genel bakış ekranını yükleyin, ardından Yakalama ayarını etkinleştirmek veya değiştirmek istediğiniz olay hub'ına tıklayın. Son olarak, açık sayfanın sol tarafındaki **Yakala** seçeneğine tıklayın ve aşağıdaki şekilde gösterildiği gibi ayarları düzenleyin:

### <a name="azure-blob-storage"></a>Azure Blob Depolama

![Azure Blob depolamayı yapılandırma][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage 2. Nesil

![Azure Data Lake Storage Gen 2 ' ye yapılandırma](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage 1. Nesil 

![Azure Data Lake Storage Yapılandır][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Sonraki adımlar

- Olay hub’larını yakalama hakkında daha fazla bilgi için bkz. [Event Hubs Yakalama özelliğine genel bakış][capture-overview].
- Dilerseniz Azure Resource Manager şablonlarını kullanarak da Event Hubs Yakalama özelliğini yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure Resource Manager şablonu kullanarak Yakalama özelliğini etkinleştirme](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Kaynağı Event Hubs ad alanı olarak bir Azure Event Grid aboneliği oluşturmayı öğrenin](store-captured-data-data-warehouse.md)
- [Azure portal kullanarak Azure Data Lake Store kullanmaya başlama](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
