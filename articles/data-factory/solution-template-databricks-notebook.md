---
title: Azure Data Factory 'da Databricks kullanarak veri dönüştürme
description: Azure Data Factory bir Databricks Not defteri kullanarak verileri dönüştürmek için bir çözüm şablonu kullanmayı öğrenin.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 471c1d9610fa346b0eac44c73fe02f4510bd2889
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684245"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Azure Data Factory 'da Databricks kullanarak veri dönüştürme

Bu öğreticide, Data Factory **arama**, **kopyalama**ve **databricks Not defteri** etkinliklerini içeren uçtan uca bir işlem hattı oluşturacaksınız.

-   Copy ve Analytics işini tetiklemeden önce, kaynak veri kümesinin aşağı akış tüketimine hazırlanmaya çalıştığından emin olmak için **arama** veya GetMetadata etkinliği kullanılır.

-   **Kopyalama etkinliği** kaynak dosyayı/veri kümesini havuz deposuna kopyalar. Veri kümesinin Spark tarafından doğrudan kullanılabilmesi için, havuz depolama alanı Databricks not defterine DBFS olarak bağlanır.

-   **Databricks Not defteri etkinliği** , veri kümesini dönüştüren Databricks Not defterini tetikler ve onu işlenen bir klasöre/SQL DW 'ye ekler.

Bu şablonu basit tutmak için şablon, zamanlanmış bir tetikleyici oluşturmaz. Gerekirse, bu, ekleyebilirsiniz.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Ön koşullar

1.  **Havuz**olarak kullanılacak bir **BLOB depolama hesabı** ve `sinkdata` adlı bir kapsayıcı oluşturun. Şablonda daha sonra başvurulduğundan, **depolama hesabı adı**, **kapsayıcı adı**ve **erişim anahtarı**' nı bir yere göz önünde bulundurun.

2.  **Azure Databricks bir çalışma alanına** sahip olduğunuzdan emin olun veya yenisini oluşturun.

1.  **ETL için Not defterini Içeri aktarın**. Aşağıdaki dönüşüm Not defterini Databricks çalışma alanına aktarın. (Bu, aşağıdaki gibi aynı konumda olması gerekmez, ancak daha sonra seçtiğiniz yolu unutmayın.) Bu URL 'YI URL alanına girerek aşağıdaki URL 'den Not defteri 'ni içeri aktarın: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. **Içeri aktar**' ı seçin.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Şimdi, **dönüştürme** Not defterini **depolama bağlantı bilgilerinizi** (ad ve erişim anahtarı) güncelleştirelim. Yukarıdaki içeri aktarılan not defterinde **komut 5** ' e gidin, vurgulanan değerleri değiştirdikten sonra aşağıdaki kod parçacığı ile değiştirin. Bu hesabın daha önce oluşturulmuş depolama hesabı olduğundan ve `sinkdata` kapsayıcısını içerdiğinden emin olun.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1.  Databricks 'e erişmek için Data Factory için bir **databricks erişim belirteci** oluşturun. ' Dapi32db32cbb4w6eee18b7d87e45exxxxxx ' gibi görünen bir Databricks bağlı hizmeti oluştururken daha sonra kullanmak için **erişim belirtecini kaydedin**

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Bağlı hizmetler ve veri kümeleri oluşturma

1.  *Bağlantılara bağlı hizmetler* 'e giderek Data Factory Kullanıcı arabiriminde yeni **bağlı hizmetler** oluşturun + yeni

    1.  **Kaynak – kaynak** verilere erişmek için. Bu örnek için kaynak dosyaları içeren genel BLOB depolama alanını kullanabilirsiniz.

        **BLOB depolamayı**seçin, kaynak depolamaya bağlanmak Için AŞAĞıDAKI **SAS URI** 'sini kullanın (salt okuma erişimi).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Havuz** – verileri içine kopyalamak için.

        Havuz bağlantılı hizmetinde, önkoşul 1 ' de oluşturulan bir depolama alanı seçin.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – databricks kümesine bağlanmak için

        Önkoşul 2. c içinde oluşturulan erişim anahtarını kullanarak bir Databricks bağlı hizmeti oluşturun. *Etkileşimli bir kümeniz*varsa, bunu seçebilirsiniz. (Bu örnek *yeni iş kümesi* seçeneğini kullanır.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  **Veri kümeleri** oluştur

    1.  Kaynak verilerin kullanılabilir olup olmadığını denetlemek için **' sourceAvailability_Dataset '** oluştur

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  Kaynak veri **kümesi –** kaynak verileri kopyalamak için (ikili kopya kullanarak)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  Havuz **veri kümesi** -havuz/hedef konumuna kopyalama için

        1.  Bağlı hizmet-1. b içinde oluşturulan ' sinkBlob_LS ' öğesini seçin

        2.  Dosya yolu-' sinkdata/staged_sink '

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Etkinlik oluşturma

1.  Kaynak kullanılabilirlik denetimi yapmak için bir arama etkinliği '**kullanılabilirlik bayrağı**' oluşturun (arama veya GetMetadata kullanılabilir). 2\. a içinde oluşturulan ' sourceAvailability_Dataset ' öğesini seçin.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Veri kümesini kaynaktan havuza kopyalamak için bir kopyalama etkinliği '**dosya-BLOB**' oluşturun. Bu durumda, veriler ikili dosyadır. Kopyalama etkinliğinde kaynak ve havuz yapılandırması için aşağıdaki ekran görüntülerine başvurun.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  İşlem **hattı parametrelerini** tanımlama

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  **Databricks etkinliği** oluşturma

    Önceki adımda oluşturulan bağlı hizmeti seçin.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    **Ayarları**yapılandırın. Ekran görüntüsünde gösterilen **temel parametreleri** oluşturun ve Data Factory Databricks not defterine geçirilecek parametreleri oluşturun. **Önkoşul 2**' de karşıya yüklenen **doğru not defteri yolunu** bulup **seçin** .

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  İşlem hattını **çalıştırın**. Daha ayrıntılı Spark günlükleri için Databricks günlüklerine bağlantı bulabilirsiniz.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Ayrıca, Depolama Gezgini 'ni kullanarak veri dosyasını doğrulayabilirsiniz. (Data Factory işlem hattı çalıştırmaları ile ilgili bir işlem için bu örnek, veri fabrikasından alınan işlem hattı çalıştırma KIMLIĞINI çıkış klasörüne ekler. Bu şekilde, her çalıştırma ile oluşturulan dosyaları izleyebilirsiniz.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory'ye giriş](introduction.md)
