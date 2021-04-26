---
title: Stream Analytics Data Lake Storage 1.-Azure 'a veri akışı
description: Bir Azure Storage blobundan verileri okuyan basit bir senaryoya sahip bir Azure Stream Analytics işi için Azure Data Lake Storage 1. çıktı olarak kullanmayı öğrenin.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91576226"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Azure Stream Analytics kullanarak Azure Data Lake Storage 1. Azure Depolama Blobu veri akışı
Bu makalede, Azure Data Lake Storage 1. bir Azure Stream Analytics iş için çıktı olarak nasıl kullanacağınızı öğreneceksiniz. Bu makalede, verileri bir Azure depolama blobundan (giriş) okuyan ve verileri Data Lake Storage 1. (çıktı) yazan basit bir senaryo gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiye başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* **Azure depolama hesabı**. Bu hesaptan bir blob kapsayıcısını, bir Stream Analytics işi için veri girmek üzere kullanacaksınız. Bu öğreticide, **storageforasa** adlı bir depolama hesabınız ve **storageforasacontainer** adlı hesap içinde bir kapsayıcı olduğunu varsayalım. Kapsayıcıyı oluşturduktan sonra bir örnek veri dosyası yükleyin. 
  
* **Data Lake Storage 1. hesabı**. [Azure Portal kullanarak Azure Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-portal.md)yönergelerini izleyin. **Myadlsg1** adlı bir Data Lake Storage 1. hesabınız olduğunu varsayalım. 

## <a name="create-a-stream-analytics-job"></a>Stream Analytics İşi oluşturma
Giriş kaynağı ve çıkış hedefi içeren bir Stream Analytics işi oluşturarak başlayın. Bu öğretici için kaynak bir Azure Blob kapsayıcısıdır ve hedef Data Lake Storage 1..

1. [Azure Portal](https://portal.azure.com)oturum açın.

2. Sol bölmeden **Stream Analytics işleri**' ne ve ardından **Ekle**' ye tıklayın.

    ![Stream Analytics Işi oluşturma](./media/data-lake-store-stream-analytics/create.job.png "Akış Analizi işi oluşturma")

    > [!NOTE]
    > Depolama hesabı ile aynı bölgede iş oluşturduğunuzdan emin olun veya bölgeler arasında veri taşımaya yönelik ek ücret ödemeniz gerekir.
    >

## <a name="create-a-blob-input-for-the-job"></a>İş için bir blob girişi oluşturma

1. Stream Analytics işi için sayfayı açın, sol bölmede **girişler** sekmesine tıklayın ve ardından **Ekle**' ye tıklayın.

    ![Girişler seçeneğiyle Stream Analytics Işi dikey penceresinin ve akış girişi ekle seçeneğinin ekran görüntüsü.](./media/data-lake-store-stream-analytics/create.input.1.png "İşinize bir giriş ekleyin")

2. **Yeni giriş** dikey penceresinde aşağıdaki değerleri sağlayın.

    ![BLOB depolama-yeni giriş dikey penceresinin ekran görüntüsü.](./media/data-lake-store-stream-analytics/create.input.2.png "İşinize bir giriş ekleyin")

   * **Giriş diğer adı** için, iş girişi için benzersiz bir ad girin.
   * **Kaynak türü** için **veri akışı**' nı seçin.
   * **Kaynak** için **BLOB depolama**' yı seçin.
   * **Abonelik** için **geçerli abonelikten blob depolamayı kullan**' ı seçin.
   * **Depolama hesabı** için, önkoşulların bir parçası olarak oluşturduğunuz depolama hesabını seçin. 
   * **Kapsayıcı** için, seçilen depolama hesabında oluşturduğunuz kapsayıcıyı seçin.
   * **Olay serileştirme biçimi** için **CSV**' yi seçin.
   * **Sınırlayıcı** için **sekme**' i seçin.
   * **Kodlama** için **UTF-8**' i seçin.

     **Oluştur**’a tıklayın. Portal şimdi girişi ekler ve ona bağlantıyı sınar.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>İş için Data Lake Storage 1. çıktısı oluşturma

1. Stream Analytics işi için sayfayı açın, **çıktılar** sekmesine tıklayın, **Ekle**' ye tıklayın ve **Data Lake Storage 1.**' i seçin.

    ![Çıktılar seçeneği, Ekle seçeneği ve Data Lake Storage Gen 1 seçeneği ile Stream Analytics Iş dikey penceresinin ekran görüntüsü.](./media/data-lake-store-stream-analytics/create.output.1.png "İşinize bir çıktı ekleyin")

2. **Yeni çıkış** dikey penceresinde aşağıdaki değerleri sağlayın.

    ![Yetkilendirme seçeneği olarak adlandırılan Data Lake Storage Gen 1-yeni çıkış dikey penceresinin ekran görüntüsü.](./media/data-lake-store-stream-analytics/create.output.2.png "İşinize bir çıktı ekleyin")

    * **Çıktı diğer adı** için iş çıktısı için benzersiz bir ad girin. Bu, sorgu çıkışını bu Data Lake Storage 1. hesabına yönlendirmek için sorgularda kullanılan kolay bir addır.
    * Data Lake Storage 1. hesabına erişimi yetkilendirmeniz istenir. **Yetkilendir**'e tıklayın.

3. **Yeni çıkış** dikey penceresinde aşağıdaki değerleri sağlamaya devam edin.

    ![Data Lake Storage Gen 1-yeni çıkış dikey penceresinin ekran görüntüsü.](./media/data-lake-store-stream-analytics/create.output.3.png "İşinize bir çıktı ekleyin")

   * **Hesap adı** için, iş çıktısının gönderilmesini istediğiniz yerde zaten oluşturduğunuz Data Lake Storage 1. hesabı seçin.
   * **Yol ön eki** için, belirtilen Data Lake Storage 1. hesabı içinde dosyalarınızı yazmak için kullanılan bir dosya yolu girin.
   * **Tarih biçimi** için, önek yolunda bir tarih belirteci kullandıysanız dosyalarınızın düzenlendiği tarih biçimini seçebilirsiniz.
   * **Saat biçimi** için, ön ek yolunda bir zaman belirteci kullandıysanız dosyalarınızın düzenlendiği zaman biçimini belirtin.
   * **Olay serileştirme biçimi** için **CSV**' yi seçin.
   * **Sınırlayıcı** için **sekme**' i seçin.
   * **Kodlama** için **UTF-8**' i seçin.
    
     **Oluştur**’a tıklayın. Portal şimdi çıktıyı ekler ve ona bağlantıyı sınar.
    
## <a name="run-the-stream-analytics-job"></a>Stream Analytics işini çalıştırma

1. Bir Stream Analytics işini çalıştırmak için, **sorgu** sekmesinden bir sorgu çalıştırmanız gerekir. Bu öğreticide, aşağıdaki ekran görüntüsünde gösterildiği gibi yer tutucuları iş ve çıkış diğer adlarıyla değiştirerek örnek sorguyu çalıştırabilirsiniz.

    ![Sorguyu Çalıştır](./media/data-lake-store-stream-analytics/run.query.png "Sorgu çalıştırma")

2. Ekranın üstündeki **Kaydet** ' e tıklayın ve ardından **genel bakış** sekmesinden **Başlat**' a tıklayın. İletişim kutusunda **özel saat**' i seçin ve geçerli tarih ve saati ayarlayın.

    ![İş süresini ayarla](./media/data-lake-store-stream-analytics/run.query.2.png "İş süresini ayarla")

    İşi başlatmak için **Başlat** ' a tıklayın. İşin başlatılması birkaç dakika sürebilir.

3. İşi Blobun verileri seçmek üzere tetiklemek için, blob kapsayıcısına bir örnek veri dosyası kopyalayın. [Azure Data Lake git deposundan](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)örnek bir veri dosyası alabilirsiniz. Bu öğreticide, **vehicle1_09142014.csv** dosyayı kopyalayalim. Blob kapsayıcısına veri yüklemek için [Azure Depolama Gezgini](https://storageexplorer.com/)gibi çeşitli istemcileri kullanabilirsiniz.

4. **Genel bakış** sekmesinde, **izleme** altında, verilerin nasıl işlendiği konusuna bakın.

    ![İşi izleme](./media/data-lake-store-stream-analytics/run.query.3.png "İşi izleme")

5. Son olarak, iş çıkışı verilerinin Data Lake Storage 1. hesapta kullanılabilir olduğunu doğrulayabilirsiniz. 

    ![Çıkışı doğrulama](./media/data-lake-store-stream-analytics/run.query.4.png "Çıkışı doğrulama")

    Veri Gezgini bölmesinde, çıktının Data Lake Storage 1. çıkış ayarları 'nda () belirtilen bir klasör yoluna yazıldığını unutmayın `streamanalytics/job/output/{date}/{time}` .  

## <a name="see-also"></a>Ayrıca bkz.
* [Data Lake Storage 1. kullanmak için bir HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md)
