---
title: Azure Data Factory kullanarak Office 365 ' den veri yükleme
description: Office 365 ' den veri kopyalamak için Azure Data Factory kullanma
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 0c938caef81063409d3c8d6632cd7e8df99f1ea1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672611"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Office 365 ' den veri yükleme

Bu makalede, _Office 365 ' de Azure Blob depolama alanına veri yükleme_Data Factory nasıl kullanılacağı gösterilmektedir. Azure Data Lake Gen1 veya Gen2 'e veri kopyalamak için benzer adımları izleyebilirsiniz. Office 365 ' den genel olarak veri kopyalama hakkında [office 365 Bağlayıcısı makalesine](connector-office-365.md) başvurun.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Sol menüde **kaynak oluştur** > **analiz** > **Data Factory**' yı seçin: 
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **Yeni Veri Fabrikası** sayfasında, aşağıdaki görüntüde gösterilen alanlar için değerler girin:
      
   ![Yeni veri fabrikası sayfası](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Ad**: Azure Data Factory 'niz için genel olarak benzersiz bir ad girin. "Data Factory Name *LoadFromOffice365Demo* kullanılamıyor" hatasını alırsanız Veri Fabrikası için farklı bir ad girin. Örneğin _**, Name**_ **LoadFromOffice365Demo**adını kullanabilirsiniz. Data Factory 'yi yeniden oluşturmayı deneyin. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).
    * **Abonelik**: veri fabrikasının oluşturulacağı Azure aboneliğinizi seçin. 
    * **Kaynak grubu**: açılan listeden var olan bir kaynak grubunu seçin veya **Yeni oluştur** seçeneğini belirleyin ve bir kaynak grubunun adını girin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
    * **Sürüm**: **v2**'yi seçin.
    * **Konum**: veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Data Factory tarafından kullanılan veri depoları diğer konumlarda ve bölgelerde olabilir. Bu veri depoları Azure Data Lake Store, Azure depolama, Azure SQL veritabanı vb. içerir.

3. **Oluştur**'u seçin.
4. Oluşturma işlemi tamamlandıktan sonra, veri fabrikanıza gidin. Aşağıdaki görüntüde gösterildiği gibi **Data Factory** giriş sayfasını görürsünüz:
   
   ![Data factory giriş sayfası](./media/load-office-365-data/data-factory-home-page.png)

5. Veri tümleştirme uygulamasını ayrı bir sekmede başlatmak için **yazar & İzleyici** kutucuğunu seçin.

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

1. "Başlarken" sayfasında işlem **hattı oluştur**' u seçin.
 
    ![İşlem hattı oluşturma](./media/load-office-365-data/create-pipeline-entry.png)

2. İşlem hattının **Genel sekmesinde** , Işlem hattının **adı** Için "copypipeline" yazın.

3. Etkinlikler Araç kutusunda > & dönüştürme kategorisini taşıyın > **kopyalama etkinliğini** araç kutusundan sürükleyin ve işlem hattı tasarımcı yüzeyine bırakın. "CopyFromOffice365ToBlob" öğesini etkinlik adı olarak belirtin.

### <a name="configure-source"></a>Kaynağı yapılandırma

1. İşlem hattı > **kaynak sekmesine**gidin ve kaynak veri kümesi oluşturmak Için **+ Yeni** ' ye tıklayın. 

2. Yeni veri kümesi penceresinde **Office 365**' i seçin ve ardından **devam**' ı seçin.
 
3. Şimdi kopyalama etkinliği yapılandırma sekmesindedir. veri yapılandırmasına devam etmek için Office 365 veri kümesinin yanındaki **Düzenle** düğmesine tıklayın.

    ![Yapılandırma Office 365 veri kümesi genel](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Office 365 veri kümesi için yeni bir sekme açıldığını görürsünüz. Özellikler penceresi altındaki **Genel sekmesinde** , ad Için "SourceOffice365Dataset" yazın.
 
5. Özellikler penceresi **Bağlantı sekmesine** gidin. Bağlı hizmet metin kutusunun yanındaki **+ Yeni**' ye tıklayın.

6. Yeni bağlı hizmet penceresinde ad olarak "Office365LinkedService" yazın, hizmet sorumlusu KIMLIĞI ve hizmet sorumlusu anahtarını girin, ardından bağlantıyı test edin ve bağlı hizmeti dağıtmak için **Oluştur** ' u seçin.

    ![Yeni Office 365 bağlı hizmeti](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Bağlı hizmet oluşturulduktan sonra veri kümesi ayarlarına dönersiniz. **Tablo**' nın yanındaki aşağı oku seçerek kullanılabilir Office 365 veri kümeleri listesini genişletin ve "BasicDataSet_v0" öğesini seçin. Message_v0 "açılan listesinden:

    ![Yapılandırma Office 365 veri kümesi tablosu](./media/load-office-365-data/edit-dataset.png)

8. Artık Office 365 veri ayıklama için ek özellikler yapılandırmaya devam etmek üzere işlem **hattı** > **kaynak sekmesine** geri dönün.  Kullanıcı kapsamı ve kullanıcı kapsamı filtresi, Office 365 ' den çıkarmak istediğiniz verileri kısıtlamak için tanımlayabileceğiniz isteğe bağlı fiklardır. Bu ayarları nasıl yapılandıracağınızı öğrenmek için bkz. [Office 365 veri kümesi özellikleri](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) bölümü.

9. Tarih filtrelerinden birini seçmeniz ve başlangıç saati ile bitiş saati değerlerini sağlamanız gerekir.

10. Ileti veri kümesi için şemayı içeri aktarmak üzere **Şemayı Içeri aktar** sekmesine tıklayın.

    ![Config Office 365 veri kümesi şeması](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Havuzu yapılandırma

1. İşlem hattı > **Havuz sekmesine**gidin ve bir havuz veri kümesi oluşturmak Için **+ Yeni** ' yi seçin.
 
2. Yeni veri kümesi penceresinde, Office 365 ' den kopyalanırken yalnızca desteklenen hedeflerin seçili olduğuna dikkat edin. **Azure Blob depolama**' yı seçin, ikili biçim ' i seçin ve **devam**' ı seçin.  Bu öğreticide, Office 365 verilerini bir Azure Blob depolama alanına kopyalayaöğreneceksiniz.

3. Veri yapılandırmasına devam etmek için Azure Blob depolama veri kümesinin yanındaki **Düzenle** düğmesine tıklayın.

4. Özellikler penceresi **Genel sekmesinde** , ad alanına "OutputBlobDataset" yazın.

5. Özellikler penceresi **Bağlantı sekmesine** gidin. Bağlı hizmet metin kutusunun yanındaki **+ Yeni**' yi seçin.

6. Yeni bağlı hizmet penceresinde ad olarak "AzureStorageLinkedService" yazın, kimlik doğrulama yöntemlerinin açılan listesinden "hizmet sorumlusu" seçeneğini belirleyin, hizmet uç noktası, kiracı, hizmet sorumlusu KIMLIĞI ve hizmet sorumlusu anahtarını doldurup buraya kaydet ' i seçin. bağlı hizmeti dağıtın.  Azure Blob depolama için hizmet sorumlusu kimlik doğrulamasını [ayarlama bölümüne bakın](connector-azure-blob-storage.md#service-principal-authentication) .

    ![Yeni blob bağlı hizmeti](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>İşlem hattını doğrulama

İşlem hattını doğrulamak için araç çubuğundan **Doğrula**'yı seçin.

Ayrıca, sağ üst köşedeki kod ' a tıklayarak ardışık düzen ile ilişkili JSON kodunu da görebilirsiniz.

## <a name="publish-the-pipeline"></a>İşlem hattını yayımlama

Üstteki araç çubuğunda **Tümünü Yayımla**' yı seçin. Bu eylem, oluşturduğunuz varlıkları (veri kümeleri ve işlem hatları) Data Factory'de yayımlar.

![Değişiklikleri Yayımla](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>İşlem hattını el ile tetikleme

Araç çubuğunda **tetikleyici Ekle** ' yi seçin ve sonra **Şimdi Tetikle**' yi seçin. İşlem hattı çalıştırma sayfasında **son**' u seçin. 

## <a name="monitor-the-pipeline"></a>İşlem hattını izleme

Soldaki **İzleyici** sekmesine gidin. El ile tetikleme tarafından tetiklenmiş bir işlem hattı çalıştırması görürsünüz. Etkinlik ayrıntılarını görüntülemek ve işlem hattını yeniden çalıştırmak için **Eylemler** sütunundaki bağlantıları kullanabilirsiniz.

![İşlem hattını izleme](./media/load-office-365-data/pipeline-status.png) 

Işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görmek için Eylemler sütunundaki **etkinlik çalıştırmalarını görüntüle** bağlantısını seçin. Bu örnekte, tek bir etkinlik olduğundan listede tek giriş görürsünüz. Kopyalama işlemiyle ilgili ayrıntılar için Eylemler sütunundaki **Ayrıntılar** bağlantısını (gözlük simgesi) seçin.

![Etkinliği izle](./media/load-office-365-data/activity-status.png) 

Bu bağlam için ilk kez veri isteğinde bulunduğunuzu (veri tablosunun hangi veri tablosuna erişmekte olduğunu, hangi hedef hesabın içine yüklenmekte olduğunu ve hangi kullanıcı kimliğinin veri erişim isteğini yapmakta olduğunu), kopyalama etkinliğini görürsünüz durum **devam**ediyor ve yalnızca eylemler altındaki "Ayrıntılar" bağlantısına tıkladığınızda durum **RequesetingConsent**olarak görüntülenir.  Veri ayıklama işleminin devam edebilmesi için, veri erişimi onaylayan grubunun bir üyesinin Privileged Access Management isteği onaylaması gerekir.

_İzin isteği olarak durum:_ 
![Etkinlik yürütme ayrıntıları-istek onayı](./media/load-office-365-data/activity-details-request-consent.png) 

_Veri ayıklanan durum:_

![Etkinlik yürütme ayrıntıları-verileri Ayıkla](./media/load-office-365-data/activity-details-extract-data.png) 

Onay sağlandığında, veri ayıklama devam eder ve bir süre sonra işlem hattı çalıştırması başarılı olarak gösterilir.

![İzleme işlem hattı-başarılı](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Şimdi hedef Azure Blob depolama alanına gidin ve Office 365 verilerinin Ikili biçimde ayıklandığını doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Azure SQL veri ambarı desteği hakkında bilgi edinmek için aşağıdaki makaleye ilerleyin: 

> [!div class="nextstepaction"]
>[Office 365 Bağlayıcısı](connector-office-365.md)