---
title: Azure hızlı başlangıç-Azure portal kullanarak bir olay hub 'ı oluşturma
description: Bu hızlı başlangıçta Azure portal kullanarak bir Azure Olay Hub 'ı oluşturmayı öğreneceksiniz.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bf83c53792c4934f43e0039d092bb3e36954bf7b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303126"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Hızlı başlangıç: Azure portalı kullanarak olay hub'ı oluşturma
Azure Event Hubs, saniye başına milyonlarca olayı alabilen ve işleyesağlayan büyük bir veri akışı platformu ve olay alma hizmetidir. Event Hubs dağıtılan yazılımlar ve cihazlar tarafından oluşturulan olayları, verileri ve telemetrileri işleyebilir ve depolayabilir. Bir olay hub’ına gönderilen veriler, herhangi bir gerçek zamanlı analiz sağlayıcısı ve işlem grubu oluşturma/depolama bağdaştırıcıları kullanılarak dönüştürülüp depolanabilir. Olay Hub’larının ayrıntılı genel bakışı için bkz. [Olay Hub’larına genel bakış](event-hubs-about.md) ve [Olay Hub’ları özellikleri](event-hubs-features.md).

Bu hızlı başlangıçta [Azure portalı](https://portal.azure.com) kullanarak olay hub'ı oluşturacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakileri yaptığınızdan emin olun:

- Azure aboneliği. Bir tane yoksa, başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynakları için mantıksal bir koleksiyondur. Tüm kaynaklar bir kaynak grubuna dağıtılır ve buradan yönetilir. Kaynak grubu oluşturmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **kaynak grupları**' nı seçin. Ardından **Ekle**'yi seçin.

   ![Kaynak grupları - Ekle düğmesi](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. **Abonelik** için, kaynak grubunu oluşturmak istediğiniz Azure aboneliğinin adını seçin.
1. **Kaynak grubu için** benzersiz bir ad yazın. Sistem, adın seçili Azure aboneliğinde var olup olmadığını kontrol eder.
1. Kaynak grubu için bir **bölge** seçin.
1. **Gözden geçir + Oluştur**’u seçin.

   ![Kaynak grubu - oluştur](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. **Gözden geçir + oluştur** sayfasında **Oluştur**' u seçin. 

## <a name="create-an-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma

Bir Event Hubs ad alanı, bir veya daha fazla olay hub 'ı oluşturduğunuz benzersiz bir kapsam kapsayıcısı sağlar. Portalı kullanarak kaynak grubunuzda bir ad alanı oluşturmak için aşağıdaki eylemleri gerçekleştirin:

1. Azure portalda ekranın sol üst köşesindeki **Kaynak oluştur**'u seçin.
1. Sol taraftaki menüden **tüm hizmetler** ' i seçin ve **analiz** kategorisinde **Event Hubs** ' ın yanındaki **yıldız ( `*` )** seçeneğini belirleyin. **Event Hubs** sol gezinti menüsünde **Sık kullanılanlara** eklendiğini doğrulayın. 
    
   ![Event Hubs arayın](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. Sol gezinti menüsünde **Sık Kullanılanlar** ' ın altında **Event Hubs** ' ı seçin ve araç çubuğunda **Ekle** ' yi seçin.

   ![Ekle düğmesi](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. **Ad alanı oluştur** sayfasında, aşağıdaki adımları uygulayın:  
   1. Ad alanını oluşturmak istediğiniz **aboneliği** seçin.  
   1. Önceki adımda oluşturduğunuz **kaynak grubunu** seçin.   
   1. Ad alanı için bir **ad** girin. Adın kullanılabilirliği sistem tarafından hemen kontrol edilir.  
   1. Ad alanı için bir **konum** seçin.
   1. **Fiyatlandırma katmanını** (temel veya standart) seçin. Temel ve standart Katmanlar arasındaki farklılıklar hakkında bilgi edinmek için bkz. [Event Hubs fiyatlandırması](https://azure.microsoft.com/pricing/details/event-hubs/), Katmanlar [ve kotalar ile sınırlamalar](event-hubs-quotas.md) [arasındaki farklar](event-hubs-faq.yml#what-is-the-difference-between-event-hubs-basic-and-standard-tiers-). 
   1. **Verimlilik birimleri** ayarlarını olduğu gibi bırakın. İşleme birimleri önceden satın alınan kapasite birimleridir. İşleme birimleri hakkında bilgi edinmek için bkz. [Event Hubs ölçeklenebilirlik](event-hubs-scalability.md#throughput-units).  
   1. Sayfanın alt kısmındaki **gözden geçir + oluştur** ' u seçin.
      
      ![Olay hub’ı ad alanı oluşturma](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. **Gözden geçir + oluştur** sayfasında ayarları gözden geçirin ve **Oluştur**' u seçin. Dağıtımın tamamlanmasını bekleyin. 
      
      ![İnceleme + sayfa oluştur](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. **Dağıtım** sayfasında, ad alanınız için sayfaya gitmek üzere **Kaynağa Git** ' i seçin. 
      
      ![Dağıtım Tamam-kaynağa git](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Aşağıdaki örneğe benzer **Event Hubs ad alanı** sayfasını görtığınızdan emin olun:   
      
      ![Ad alanı için ana sayfa](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

      > [!NOTE]
      > Azure Event Hubs, size bir Kafka uç noktası sağlar. Bu uç nokta, Event Hubs ad alanının [Apache Kafka](https://kafka.apache.org/intro) ileti protokolünü ve API 'leri yerel olarak anlamasına olanak sağlar. Bu özellik sayesinde, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Kafka konularda yaptığınız gibi Olay Hub 'lerinizle iletişim kurabilirsiniz. Event Hubs, [1,0 ve üzeri sürümleri Apache Kafka](https://kafka.apache.org/10/documentation.html) destekler. Daha fazla bilgi için bkz. [Apache Kafka uygulamalardan Event Hubs kullanma](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Ad alanında bir olay hub'ı oluşturmak için aşağıdaki eylemleri gerçekleştirin:

1. Event Hubs ad alanı sayfasında, soldaki menüden **Event Hubs** ' i seçin.
1. Pencerenin üst kısmında **+ Event hub**' ı seçin.
   
    ![Olay Hub'ı Ekle - düğme](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Olay Hub 'ınız için bir ad yazın ve **Oluştur**' u seçin.
   
    ![Olay hub'ı oluşturma](./media/event-hubs-quickstart-portal/create-event-hub5.png)

    **Bölüm sayısı** ayarı, birçok tüketici genelinde tüketim paralel hale getirmek sağlar. Daha fazla bilgi için bkz. [bölümler](event-hubs-scalability.md#partitions).

    **İleti bekletme** ayarı, Event Hubs hizmetinin verileri ne kadar süreyle tutacağını belirtir. Daha fazla bilgi için bkz. [olay saklama](event-hubs-features.md#event-retention).
1. Uyarı ' da Olay Hub 'ı oluşturma durumunu kontrol edebilirsiniz. Olay Hub 'ı oluşturulduktan sonra, bunu Olay Hub 'ları listesinde görürsünüz.

    ![Olay Hub 'ı oluşturuldu](./media/event-hubs-quickstart-portal/event-hub-created.png)
    
## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir kaynak grubu, bir Event Hubs ad alanı ve bir olay hub'ı oluşturdunuz. Olay Hub 'ından olay alma (veya) olaylarını gönderme hakkında adım adım yönergeler için şu öğreticilere bakın: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Git](event-hubs-go-get-started-send.md)
- [C (yalnızca gönderme)](event-hubs-c-getstarted-send.md)
- [Apache Storm (yalnızca alma)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
