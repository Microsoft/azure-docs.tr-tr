---
title: Azure hızlı başlangıç-Azure portal kullanarak Event Hubs adanmış küme oluşturma
description: Bu hızlı başlangıçta, Azure portal kullanarak Azure Event Hubs kümesi oluşturmayı öğreneceksiniz.
services: event-hubs
documentationcenter: ''
author: xurui203
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2019
ms.author: xurui
ms.openlocfilehash: bde6844962561232e4af51f2b4e803d49bf5398e
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720632"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak adanmış Event Hubs kümesi oluşturma 
Event Hubs kümeler en zorlu akış gereksinimlerine sahip müşteriler için tek kiracılı dağıtımlar sağlar. Bu teklif,% 99,99 SLA garanti etti ve yalnızca adanmış fiyatlandırma katmanımızda kullanılabilir. Bir [Event Hubs kümesi](event-hubs-dedicated-overview.md) , garantili kapasiteye ve alt saniyelik gecikmeye göre saniyede milyonlarca olayı alabilir. Bir küme içinde oluşturulan ad alanları ve Olay Hub 'ları, tüm giriş limitleri olmadan standart teklifin tüm özelliklerini ve daha fazlasını içerir. Adanmış teklif, ek bir ücret ödemeden popüler [Event Hubs yakalama](event-hubs-capture-overview.md) özelliğini de içerir, böylece veri akışlarını otomatik olarak [Azure Blob depolama alanına](../storage/blobs/storage-blobs-introduction.md) veya [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)' e otomatik olarak günlüğe kaydedebilirsiniz.

Adanmış kümeler, önceden ayrılmış bir CPU ve bellek kaynakları miktarı olan **Kapasite birimleri (cu düzeyinde kapsanır)** tarafından sağlanır ve faturalandırılır. Her küme için 1, 2, 4, 8, 12, 16 veya 20 cu düzeyinde kapsanır satın alabilirsiniz. Bu hızlı başlangıçta, Azure portal aracılığıyla 1 CU Event Hubs kümesi oluşturma konusunda size yol göstereceğiz.

> [!NOTE]
> Bu self servis deneyimi şu anda [Azure portalında](https://aka.ms/eventhubsclusterquickstart)önizleme sürümünde sunulmaktadır. Adanmış teklif hakkında sorularınız varsa, lütfen [Event Hubs ekibine](mailto:askeventhubs@microsoft.com)ulaşın.


## <a name="prerequisites"></a>Ön koşullar
Bu hızlı başlangıcı tamamlamak için aşağıdakileri yaptığınızdan emin olun:

- Bir Azure hesabı. Bir hesabınız yoksa, başlamadan önce [bir hesap satın alın](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) . Bu özellik ücretsiz bir Azure hesabıyla desteklenmez. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 güncelleştirme 3 (sürüm 15,3, 26730,01) veya üzeri.
- [.NET Standard SDK'sı](https://dotnet.microsoft.com/download), sürüm 2.0 veya üzeri.
- [Kaynak grubu oluşturuldu](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Event Hubs Ayrılmış kümesi oluşturma
Event Hubs kümesi, bir veya daha fazla ad alanı oluşturabileceğiniz benzersiz bir kapsam kapsayıcısı sağlar. Portalın self servis deneyiminin bu önizleme aşamasında, select bölgelerinde 1 CU kümeleri oluşturabilirsiniz. 1 ' den büyük bir kümeye ihtiyacınız varsa, oluşturma işleminden sonra kümenizi ölçeklendirmek için bir Azure destek isteği gönderebilirsiniz.

Kaynak grubunuzda Azure portal kullanarak bir küme oluşturmak için lütfen aşağıdaki adımları izleyin:

1. Azure portal bir küme oluşturmak için [Bu bağlantıyı](https://aka.ms/eventhubsclusterquickstart) izleyin. Buna karşılık, sol gezinti bölmesinden **tüm hizmetler** ' i seçin, sonra arama çubuğuna "Event Hubs kümeleri" yazın ve sonuçlar listesinden "Event Hubs kümeler" ' ı seçin.
2. **Küme oluştur** sayfasında, aşağıdakileri yapılandırın:
    1. **Küme için bir ad**girin. Adın kullanılabilirliği sistem tarafından hemen denetlenir.
    2. Kümeyi oluşturmak istediğiniz **aboneliği** seçin.
    3. Kümeyi oluşturmak istediğiniz **kaynak grubunu** seçin.
    4. Küme için bir **konum** seçin. Tercih ettiğiniz bölge gri ise, geçici olarak kapasitesinin dışında olur ve Event Hubs ekibine bir [destek isteği](#submit-a-support-request) gönderebilirsiniz.
    5. Sayfanın alt kısmındaki **Sonraki: Etiketler** düğmesini seçin. Sistemin kaynakları tam olarak sağlaması için birkaç dakika beklemeniz gerekebilir.

        ![Event Hubs kümesi oluşturma-temel bilgiler sayfası](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. **Etiketler** sayfasında, aşağıdakileri yapılandırın:
    1. Eklemek istediğiniz etiket için bir **ad** ve bir **değer** girin. Bu adım **isteğe bağlıdır**.  
    2. **Gözden geçir + oluştur** düğmesini seçin.

        ![Event Hubs kümesi sayfası oluştur-Etiketler sayfası](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. **Gözden geçir + oluştur** sayfasında, ayrıntıları gözden geçirin ve **Oluştur**' u seçin. 

    ![Event Hubs kümesi oluşturma sayfası-gözden geçir + Oluştur sayfası](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Küme içinde bir ad alanı ve Olay Hub 'ı oluşturma

1. Küme içinde bir ad alanı oluşturmak için, kümenizin **Event Hubs küme** sayfasında, üstteki menüden **+ ad alanı** ' nı seçin.

    ![Küme yönetim sayfası-ad alanı Ekle düğmesi](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Ad alanı oluştur sayfasında, aşağıdaki adımları uygulayın:
    1. **Ad alanı için bir ad**girin.  Sistem, adın kullanılabilir olup olmadığını denetler.
    2. Ad alanı aşağıdaki özellikleri devralır:
        1. Abonelik Kimliği
        2. Kaynak Grubu
        3. Konum
        4. Küme Adı
    3. Ad alanını oluşturmak için **Oluştur** ' u seçin. Artık kümenizi yönetebilirsiniz.  

        ![Küme sayfasında ad alanı oluşturma](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Ad alanınız oluşturulduktan sonra, normal olarak bir ad alanı içinde oluşturduğunuz için [bir olay hub 'ı oluşturabilirsiniz](event-hubs-create.md#create-an-event-hub) . 


## <a name="submit-a-support-request"></a>Destek talebi gönderme

Oluşturulduktan sonra kümenizin boyutunu değiştirmek isterseniz veya tercih ettiğiniz bölge kullanılamıyorsa, lütfen şu adımları izleyerek bir destek isteği gönderebilirsiniz:

1. [Azure Portal](https://portal.azure.com)' de, sol menüden **Yardım + Destek** ' i seçin.
2. Destek menüsünden **+ Yeni destek isteği ' ni** seçin.
3. Destek sayfasında, aşağıdaki adımları izleyin:
    1. **Sorun türü**için, açılan listeden **Teknik** ' i seçin.
    2. **Abonelik** bölümünde aboneliğinizi seçin.
    3. **Hizmet**Için **Hizmetlerim**' i seçin ve ardından **Event Hubs**' yi seçin.
    4. **Kaynak**için zaten varsa kümenizi seçin, aksi takdirde **genel soru/kaynak kullanılamıyor**' ı seçin.
    5. **Sorun türü**için **Kota**' i seçin.
    6. **Sorun alt türü**için, açılan listeden aşağıdaki değerlerden birini seçin:
        1. Bölgede desteklenecek özelliği istemek için **ADANMıŞ SKU isteği** seçin.
        2. Adanmış kümenizin ölçeğini genişletmek veya daraltmak istiyorsanız **ayrılmış kümeyi** ölçeği büyütme veya küçültme isteği ' ni seçin. 
    7. **Konu**için sorunu betimleyen.

        ![Destek bileti sayfası](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Adanmış kümeyi silme
 
1. Kümeyi silmek için üstteki menüden **Sil** ' i seçin. Lütfen kümenizin, oluşturulduktan sonra en az 4 saat kullanım için faturalandırılacağını unutmayın. 
2. Kümeyi silmek istediğinizi onaylayan bir ileti görüntülenir.
3. Kümeyi silmek için **kümenin adını** yazın ve **Sil** ' i seçin.

    ![Küme sayfasını Sil](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Sonraki adımlar
Bu makalede bir Event Hubs kümesi oluşturdunuz. Olay Hub 'ından olay gönderme ve alma ve olayları Azure depolama veya Azure Data Lake Store yakalama hakkında adım adım yönergeler için aşağıdaki öğreticilere bakın:

- [.NET Core 'da olay gönderme ve alma](event-hubs-dotnet-standard-getstarted-send.md)
- [Event Hubs yakalamayı etkinleştirmek için Azure portal kullanma](event-hubs-capture-enable-through-portal.md)
- [Apache Kafka için Azure Event Hubs kullanma](event-hubs-for-kafka-ecosystem-overview.md)
