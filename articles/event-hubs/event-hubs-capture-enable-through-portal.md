---
title: Azure portal - Azure Event Hubs kullanarak akış etkinlikleri | Microsoft Docs
description: Bu makalede Azure portalını kullanarak Azure Event Hubs ile akış olayların yakalamayı etkinleştirme.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 18dea8e8e4cfcabec63e35dd302f6a5259721a8a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320303"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Azure Event Hubs ile olayların akışını yakalamayı etkinleştirme

Azure [Event Hubs yakalama][capture-overview] , akış verilerini Event Hubs bir [Azure Blob depolama alanına](https://azure.microsoft.com/services/storage/blobs/) veya seçtiğiniz [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) hesabına otomatik olarak göndermenizi sağlar.

[Azure portalını](https://portal.azure.com) kullanarak olay hub'ı oluşturma sırasında Yakalama özelliğini yapılandırabilirsiniz. Verileri bir Azure [Blob depolama](https://azure.microsoft.com/services/storage/blobs/) kapsayıcısına veya bir [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) hesabına alabilirsiniz.

Daha fazla bilgi için [Event Hubs yakalamaya genel bakış][capture-overview]bölümüne bakın.

## <a name="capture-data-to-an-azure-storage-account"></a>Azure Depolama hesabına veri alma  

Bir olay hub’ı oluşturduğunuzda **Olay Hub'ı oluştur** portal ekranında **Aç** düğmesine tıklayarak Yakalama özelliğini etkinleştirebilirsiniz. Ardından **Yakalama Sağlayıcısı** kutusunda **Azure Storage**’a tıklayarak bir Depolama Hesabı ve kapsayıcı belirtirsiniz. Event Hubs Yakalama özelliği, depolama ile hizmetten hizmete kimlik doğrulama kullandığından depolama bağlantı dizesi belirtmenize gerek yoktur. Kaynak seçici, depolama hesabınız için kaynak URI'sini otomatik olarak seçer. Azure Resource Manager kullanıyorsanız bu URI'yi dize olarak açıkça belirtmeniz gerekir.

Zaman penceresi varsayılan olarak 5 dakikadır. En düşük değer 1, en yüksek değer ise 15'tir. **Boyut** penceresi 10-500 MB aralığındadır.

![Zaman penceresi için yakalama][1]

> [!NOTE]
> Yakalama penceresi sırasında hiçbir olay gerçekleşmeyecek boş dosyaları yaymayı etkinleştirebilir veya devre dışı bırakabilirsiniz. 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Azure Data Lake Store hesabına veri alma

Azure Data Lake Store'a veri almak için bir Data Lake Store hesabı ve bir olay hub'ı oluşturun:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Azure Data Lake Store hesabı ve klasörleri oluşturma


1. [Azure Portal'ı kullanarak Azure Data Lake Store ile çalışmaya başlama](../data-lake-store/data-lake-store-get-started-portal.md) bölümündeki yönergeleri uygulayarak bir Data Lake Store hesabı oluşturun.
2. Event Hubs verilerini almak ve Data Lake Store hesabınıza veri yazabilmesi için Event Hubs'a izin atamak istediğiniz Data Lake Stora hesabı içinde bir klasör oluşturmak için [Event Hubs'a izin atama](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) bölümündeki yönergeleri uygulayın.  


### <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

1. Olay hub'ının oluşturduğunuz Azure Data Lake Store ile aynı Azure aboneliğinde olması gerektiğini unutmayın. **Event Hub'ı oluştur** portal sayfasının **Yakalama** bölümünde **Açık** düğmesine tıklayarak olay hub'ını oluşturun. 
2. **Event Hub'ı oluştur** portal sayfasında, **Yakalama Sağlayıcısı** kutusundan **Azure Data Lake Store**’u seçin.
3. **Data Lake Store’u seçin** kısmında, daha önce oluşturduğunuz Data Lake Store hesabını belirtin ve **Data Lake Yolu** alanına, oluşturduğunuz veri klasörünün yolunu girin.

    ![Data Lake Storage hesabını seçin][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Mevcut bir olay hub'ında Yakalama özelliğini yapılandırma

Event Hubs ad alanlarında mevcut olan olay hub'ları üzerinde Yakalama özelliğini yapılandırabilirsiniz. Yakalama özelliğini mevcut bir olay hub'ında etkinleştirmek veya Yakalama ayarlarınızı değiştirmek için ad alanına tıklayarak genel bakış ekranını yükleyin, ardından Yakalama ayarını etkinleştirmek veya değiştirmek istediğiniz olay hub'ına tıklayın. Son olarak, açık sayfanın sol tarafındaki **Yakala** seçeneğine tıklayın ve aşağıdaki şekilde gösterildiği gibi ayarları düzenleyin:

### <a name="azure-blob-storage"></a>Azure Blob Depolama

![Azure Blob depolamayı yapılandırma][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![Azure Data Lake depolama yapılandırma][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Sonraki adımlar

- Event Hubs yakalama hakkında daha fazla bilgi edinmek için [Event Hubs yakalamaya genel bakış][capture-overview]makalesini okuyun.
- Dilerseniz Azure Resource Manager şablonlarını kullanarak da Event Hubs Yakalama özelliğini yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure Resource Manager şablonu kullanarak Yakalama özelliğini etkinleştirme](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Kaynağı Event Hubs ad alanı olarak bir Azure Event Grid aboneliği oluşturmayı öğrenin](store-captured-data-data-warehouse.md)
- [Azure portalı kullanarak Azure Data Lake Store ile çalışmaya başlama](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
