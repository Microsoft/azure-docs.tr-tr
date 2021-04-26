---
title: Event Hubs verileri Azure Data Lake Storage 1. olarak yakala
description: Azure Event Hubs tarafından alınan verileri yakalamak için Azure Data Lake Storage 1. nasıl kullanacağınızı öğrenin. Önkoşulları doğrulayarak başlayın.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578249"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Event Hubs verileri yakalamak için Azure Data Lake Storage 1. kullanma

Azure Event Hubs tarafından alınan verileri yakalamak için Azure Data Lake Storage 1. nasıl kullanacağınızı öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage 1. hesabı**. Bir oluşturma hakkında yönergeler için bkz. Azure Data Lake Storage 1. kullanmaya [başlama](data-lake-store-get-started-portal.md).

*  **Bir Event Hubs ad alanı**. Yönergeler için bkz. [Event Hubs ad alanı oluşturma](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Data Lake Storage 1. hesabının ve Event Hubs ad alanının aynı Azure aboneliğinde bulunduğundan emin olun.


## <a name="assign-permissions-to-event-hubs"></a>Event Hubs izinleri atama

Bu bölümde, Event Hubs verileri yakalamak istediğiniz hesapta bir klasör oluşturacaksınız. Ayrıca, bir Data Lake Storage 1. hesabına veri yazabilmesi için Event Hubs izinler atarsınız. 

1. Event Hubs verileri yakalamak istediğiniz Data Lake Storage 1. hesabını açın ve **Veri Gezgini**' ye tıklayın.

    ![Data Lake Storage 1. Veri Gezgini](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage 1. Veri Gezgini")

1.  **Yeni klasör** ' e tıklayın ve ardından verileri yakalamak istediğiniz klasör için bir ad girin.

    ![Data Lake Storage 1. yeni bir klasör oluşturun](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Data Lake Storage 1. yeni bir klasör oluşturun")

1. Data Lake Storage 1. kökünde izin atayın. 

    a. **Veri Gezgini**' ye tıklayın, Data Lake Storage 1. hesabının kökünü seçin ve ardından **erişim**' e tıklayın.

    ![Hesap köküne sahip veri Gezgini 'nin ekran görüntüsü ve olarak adlandırılan erişim seçeneği.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Data Lake Storage 1. kök için izin atama")

    b. **Erişim** altında, **Ekle**' ye tıklayın, **Kullanıcı veya Grup Seç**' e tıklayın ve ardından arama yapın `Microsoft.EventHubs` . 

    ![Erişim sayfasının Ekle seçeneği ile ekran görüntüsü, Kullanıcı veya Grup seçeneğini belirleyin ve Microsoft Eventhubs seçeneği çağırılır.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Data Lake Storage 1. kök için izin atama")
    
    **Seç**’e tıklayın.

    c. **Izinleri ata** altında, **izinleri seç**' e tıklayın. Yürütülecek **izinleri** ayarlayın . **Bu klasöre ve tüm alt öğelere** **Ekle** ' ye ayarlayın. **Bir erişim izni girişi ve varsayılan izin girdisi** **olarak ekle** ' ye ayarlayın.

    > [!IMPORTANT]
    > Azure Event Hubs tarafından alınan verileri yakalamaya yönelik yeni bir klasör hiyerarşisi oluştururken, bu hedef klasöre erişim sağlamanın kolay bir yoludur.  Ancak, üst düzey bir klasörün tüm alt öğelerine çok sayıda alt dosya ve klasör içeren izinler eklemek uzun sürebilir.  Kök klasörünüz çok sayıda dosya ve klasör içeriyorsa,  `Microsoft.EventHubs` son hedef klasörünüzün yolundaki her bir klasöre ayrı ayrı yürütme izinleri eklemek daha hızlı olabilir. 

    ![İzinleri Seç seçeneği olarak adlandırılan Izinleri ata bölümünün ekran görüntüsü. Izinleri Seç bölümü, Çalıştır seçeneği, seçenek Ekle ve olarak adlandırılan seçenek olarak ekle seçeneğinin yanında bulunur.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Data Lake Storage 1. kök için izin atama")

    **Tamam**'a tıklayın.

1. Verileri yakalamak istediğiniz Data Lake Storage 1. hesabının altındaki klasör için izinler atayın.

    a. **Veri Gezgini**' ye tıklayın, Data Lake Storage 1. hesabındaki klasörü seçin ve ardından **erişim**' e tıklayın.

    ![Hesapta bir klasör ve erişim seçeneği olarak adlandırılan veri Gezgini ekran görüntüsü.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Data Lake Storage 1. klasörü için izin atama")

    b. **Erişim** altında, **Ekle**' ye tıklayın, **Kullanıcı veya Grup Seç**' e tıklayın ve ardından arama yapın `Microsoft.EventHubs` . 

    ![Veri Gezgini erişim sayfasının Ekle seçeneği ile ekran görüntüsü, Kullanıcı veya Grup seçeneğini belirleyin ve Microsoft Eventhubs seçeneği çağırılır.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Data Lake Storage 1. klasörü için izin atama")
    
    **Seç**’e tıklayın.

    c. **Izinleri ata** altında, **izinleri seç**' e tıklayın. **Okuma, yazma** ve **yürütme** **izinlerini** ayarlayın. **Bu klasöre ve tüm alt öğelere** **Ekle** ' ye ayarlayın. Son olarak, **bir erişim izni girişi ve varsayılan izin girişi** **olarak ekle** seçeneğini belirleyin.

    ![İzinleri Seç seçeneği olarak adlandırılan Izinleri ata bölümünün ekran görüntüsü. Izinleri Seç bölümü, okuma, yazma ve yürütme seçenekleri, Ekle seçeneği ve olarak adlandırılan Ekle seçeneği ile birlikte kullanılır.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Data Lake Storage 1. klasörü için izin atama")
    
    **Tamam**'a tıklayın. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Data Lake Storage 1. verileri yakalamak için Event Hubs yapılandırma

Bu bölümde, bir Event Hubs ad alanı içinde bir olay hub 'ı oluşturacaksınız. Ayrıca, bir Azure Data Lake Storage 1. hesabına veri yakalamak için Olay Hub 'ını yapılandırırsınız. Bu bölümde, zaten bir Event Hubs ad alanı oluşturmuş olduğunuz varsayılmaktadır.

1. Event Hubs ad alanının **genel bakış** bölmesinden **+ Event hub**' a tıklayın.

    ![Olay Hub 'ı seçeneği olarak adlandırılan genel bakış bölmesinin ekran görüntüsü.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Olay Hub'ı Oluşturma")

1. Data Lake Storage 1. verileri yakalamaya Event Hubs yapılandırmak için aşağıdaki değerleri sağlayın.

    ![Ad metin kutusuyla Olay Hub 'ı oluştur iletişim kutusunun ekran görüntüsü, yakala seçeneği, yakalama sağlayıcısı seçeneği, select Data Lake Store seçeneği ve olarak adlandırılan Data Lake Path seçeneği.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Olay Hub'ı Oluşturma")

    a. Olay Hub 'ı için bir ad sağlayın.
    
    b. Bu öğreticide, **bölüm sayısı** ve **ileti bekletme** değerlerini varsayılan değerlere ayarlayın.
    
    c. **Yakalamayı** **Açık** olarak ayarlayın. **Zaman penceresini** (yakalama sıklığı) ve **Boyut penceresini** (yakalanacak veri boyutu) ayarlayın. 
    
    d. **Yakalama sağlayıcısı** için **Azure Data Lake Store** ' yi seçin ve ardından daha önce oluşturduğunuz Data Lake Storage 1. hesabı seçin. **Data Lake yolu** için Data Lake Storage 1. hesapta oluşturduğunuz klasörün adını girin. Yalnızca klasöre göreli yolu sağlamanız gerekir.

    e. **Örnek yakalama dosya adı biçimlerini** varsayılan değere bırakın. Bu seçenek, yakalama klasörü altında oluşturulan klasör yapısını yönetir.

    f. **Oluştur**’a tıklayın.

## <a name="test-the-setup"></a>Kurulumu test etme

Artık Azure Olay Hub 'ına veri göndererek çözümü test edebilirsiniz. [Olayları Azure 'A gönderme Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)yönergeleri izleyin. Verileri göndermeye başladıktan sonra, belirttiğiniz klasör yapısını kullanarak Data Lake Storage 1. yansıtılan verileri görürsünüz. Örneğin, Data Lake Storage 1. hesabınızda aşağıdaki ekran görüntüsünde gösterildiği gibi bir klasör yapısı görürsünüz.

![Data Lake Storage 1. 'de örnek EventHub verileri](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Data Lake Storage 1. 'de örnek EventHub verileri")

> [!NOTE]
> Event Hubs geldiği iletiler olmasa bile, Event Hubs boş dosyaları Data Lake Storage 1. hesabına yalnızca üst bilgilerle yazar. Dosyalar, Event Hubs oluştururken belirttiğiniz zaman aralığında yazılır.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Data Lake Storage 1. verileri analiz etme

Veriler Data Lake Storage 1. olduktan sonra verileri işlemek ve cçalıştırmak için analitik işleri çalıştırabilirsiniz. Azure Data Lake Analytics kullanarak bunun nasıl yapılacağını öğrenmek için bkz. [Usql avro örneği](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) .
  

## <a name="see-also"></a>Ayrıca bkz.
* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Azure depolama Bloblarından veri kopyalama Data Lake Storage 1.](data-lake-store-copy-data-azure-storage-blob.md)
