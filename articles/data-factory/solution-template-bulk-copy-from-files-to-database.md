---
title: Dosyalardan veritabanına toplu kopyalama
description: Azure Data Lake Storage 2. toplu olarak Azure SYNAPSE Analytics/Azure SQL veritabanı 'na veri kopyalamak için bir çözüm şablonu kullanmayı öğrenin.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 5f68c7b33a26bc8c01f3f413943351ff8d61b380
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376130"
---
# <a name="bulk-copy-from-files-to-database"></a>Dosyalardan veritabanına toplu kopyalama

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, verileri Azure Data Lake Storage 2. Azure SYNAPSE Analytics/Azure SQL veritabanı 'na toplu olarak kopyalamak için kullanabileceğiniz bir çözüm şablonu açıklanmaktadır.

## <a name="about-this-solution-template"></a>Bu çözüm şablonu hakkında

Bu şablon dosyaları Azure Data Lake Storage 2. kaynağından alır. Sonra kaynaktaki her bir dosya üzerinde dolaşır ve dosyayı hedef veri deposuna kopyalar. 

Şu anda bu şablon yalnızca **Delimitedtext** biçimindeki verilerin kopyalanmasını destekler. Diğer veri biçimlerinde bulunan dosyalar kaynak veri deposundan da alınabilir, ancak hedef veri deposuna kopyalanamıyor.  

Şablon üç etkinlik içerir:
- **Meta veri al** etkinliği Azure Data Lake Storage 2. dosyaları alır ve sonraki *foreach* etkinliğine geçirir.
- **Foreach** etkinliği, *meta verileri al* etkinliğinden dosyaları alır ve her dosyayı *kopyalama* etkinliğine yineler.
- **Kopyalama** etkinliği, her bir dosyayı kaynak veri deposundan hedef veri deposuna kopyalamak için *foreach* etkinliğinde bulunur.

Şablon aşağıdaki iki parametreyi tanımlar:
- *Sourcecontainer* , verilerin Azure Data Lake Storage 2. kopyalandığı kök kapsayıcı yoludur. 
- *Sourcedirectory* , verilerin Azure Data Lake Storage 2. kopyalandığı kök kapsayıcısının altındaki Dizin yoludur.

## <a name="how-to-use-this-solution-template"></a>Bu çözüm şablonunu kullanma

1. **Dosyalardan veritabanı şablonuna toplu kopyalama** sayfasına gidin. Kaynak Gen2 deposuna **Yeni** bir bağlantı oluşturun. "GetMetadataDataset" ve "SourceDataset" öğesinin, kaynak dosya deponuzda aynı bağlantıya başvuru olduğunu unutmayın.

    ![Kaynak veri deposuna yeni bir bağlantı oluşturun](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Verileri kopyalamakta olduğunuz havuz veri deposuna **Yeni** bir bağlantı oluşturun.

    ![Havuz veri deposuna yeni bir bağlantı oluşturun](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. **Bu şablonu kullan**' ı seçin.

    ![Bu şablonu kullan](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Aşağıdaki örnekte gösterildiği gibi oluşturulmuş bir işlem hattı görürsünüz:

    ![İşlem hattını gözden geçirme](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Yukarıda belirtilen **2. adımdaki** veri hedefi olarak **Azure SYNAPSE Analytics** 'ı seçtiyseniz, Azure SYNAPSE Analytics PolyBase 'in gerektirdiği şekilde hazırlama için Azure Blob depolama alanına bir bağlantı girmeniz gerekir. Aşağıdaki ekran görüntüsünde gösterildiği gibi, şablon otomatik olarak BLOB depolama alanı için bir *depolama yolu* oluşturacaktır. İşlem hattı çalıştırıldıktan sonra kapsayıcının oluşturulup oluşturulmadıysa emin olun.
        
    ![PolyBase ayarı](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. **Hata Ayıkla**' yı seçin, **parametreleri** girin ve ardından **son**' u seçin.

    ![* * Hata Ayıkla * * öğesine tıklayın](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. İşlem hattı çalıştırması başarıyla tamamlandığında, aşağıdaki örneğe benzer sonuçlar görürsünüz:

    ![Sonucu gözden geçirin](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory'ye giriş](introduction.md)