---
title: Azure Data Lake Storage 2.
description: Azure SYNAPSE Analytics 'te verileri Azure Data Lake Storage 2. alma hakkında bilgi edinin
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: e3024fe1a8fe524a1deddef23a67d86a600b9394
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567613"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. verileri alma 

Bu makalede, Azure SYNAPSE Analytics kullanarak bir Azure Data Lake Gen 2 (Azure Data Lake Gen 2) depolama hesabında verileri bir konumdan diğerine alma hakkında bilgi edineceksiniz.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**: Azure Data Lake Gen 2 ' nı *kaynak* veri deposu olarak kullanırsınız. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Azure depolama hesabı oluşturma](../../storage/common/storage-account-create.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) .

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma

Azure SYNAPSE Analytics 'te, bağlı bir hizmet, bağlantı bilgilerinizi diğer hizmetlere tanımladığınız yerdir. Bu bölümde, Azure SYNAPSE Analytics ve Azure Data Lake Gen 2 ' yi bağlı hizmetler olarak ekleyeceksiniz.

1. Azure SYNAPSE Analytics UX ' i açın ve **Yönet** sekmesine gidin.
1. **Dış bağlantılar** altında **bağlı hizmetler**' i seçin.
1. Bağlı bir hizmet eklemek için **Yeni**' yi seçin.
1. Listeden Azure Data Lake Storage 2. kutucuğunu seçin ve **devam**' ı seçin.
1. Kimlik doğrulama kimlik bilgilerinizi girin. Hesap anahtarı, hizmet sorumlusu ve yönetilen kimlik, şu anda desteklenen kimlik doğrulama türleridir. Kimlik bilgilerinizin doğru olduğunu doğrulamak için Bağlantıyı Sına ' yı seçin. 
1. Bittiğinde **Oluştur**’u seçin.

## <a name="create-pipeline"></a>İşlem hattı oluşturma

İşlem hattı, bir dizi etkinliğin yürütülmesi için mantıksal akışı içerir. Bu bölümde, Azure Data Lake Gen 2 ' den özel bir SQL havuzuna veri veren bir kopyalama etkinliği içeren bir işlem hattı oluşturacaksınız.

1. **Orchestrate** sekmesine gidin. Ardışık düzen üstbilgisinin yanındaki artı simgesini seçin ve işlem **hattı**' nı seçin.
1. Etkinlikler bölmesinde **taşıma ve dönüştürme** ' nın altında, verileri ardışık düzen tuvaline **Kopyala** ' yı sürükleyin.
1. Kopyalama etkinliği ' ni seçin ve **kaynak** sekmesine gidin. Yeni bir kaynak veri kümesi oluşturmak için **Yeni** ' yi seçin.
1. Veri depolduğunuz Azure Data Lake Storage 2. seçin ve devam ' ı seçin.
1. Biçim olarak DelimitedText ' i seçin ve devam ' ı seçin.
1. Özellikleri ayarla bölmesinde, oluşturduğunuz ADLS bağlı hizmeti ' ni seçin. Kaynak verilerinizin dosya yolunu belirtin ve ilk satırın bir üst bilgisine sahip olup olmadığını belirtin. Şemayı dosya deposundan veya örnek bir dosyadan içeri aktarabilirsiniz. Bittiğinde Tamam ' ı seçin.
1. **Havuz** sekmesine gidin. Yeni bir havuz veri kümesi oluşturmak için **Yeni** ' yi seçin.
1. Veri depolama alanı olarak Azure Data Lake Storage Gen2 seçin ve devam ' ı seçin.
1. Biçim olarak DelimitedText ' i seçin ve devam ' ı seçin.
1. Özellikleri ayarla bölmesinde, oluşturduğunuz ADLS bağlı hizmeti ' ni seçin. Veri yazmak istediğiniz klasörün yolunu belirtin. Bittiğinde Tamam ' ı seçin.

## <a name="debug-and-publish-pipeline"></a>Hata ayıklama ve yayımlama işlem hattı

İşlem hattınızı yapılandırmayı tamamladıktan sonra, yapılarınızı yayımlamadan önce, her şeyin doğru olduğunu doğrulamak için bir hata ayıklama çalıştırması gerçekleştirebilirsiniz.

1. İşlem hattında hata ayıklamak için araç çubuğunda **Hata Ayıkla**'yı seçin. Pencerenin altındaki **Çıkış** sekmesinde işlem hattı çalıştırmasının durumu görüntülenir. 
1. İşlem hattı başarıyla çalıştırıldığında, üstteki araç çubuğunda **Tümünü Yayımla**' yı seçin. Bu eylem, oluşturduğunuz varlıkları (veri kümeleri ve işlem hatları) SYNAPSE Analytics hizmetinde yayımlar.
1. **Başarıyla yayımlandı** iletisini görene kadar bekleyin. Bildirim iletilerini görmek için sağ üst köşedeki zil düğmesini seçin. 


## <a name="trigger-and-monitor-the-pipeline"></a>İşlem hattını tetikleme ve izleme

Bu adımda, önceki adımda yayınlanan işlem hattını el ile tetiklersiniz. 

1. Araç çubuğunda **tetikleyici Ekle** ' yi seçin ve sonra **Şimdi Tetikle**' yi seçin. **İşlem Hattı Çalıştırma** sayfasında **Son**’u seçin.  
1. Sol kenar çubuğunda bulunan **izleyici** sekmesine gidin. El ile tetikleme tarafından tetiklenmiş bir işlem hattı çalıştırması görürsünüz. Etkinlik ayrıntılarını görüntülemek ve Işlem hattını yeniden çalıştırmak için **Eylemler** sütunundaki bağlantıları kullanabilirsiniz.
1. İşlem hattı çalıştırmalarıyla ilişkili etkinlik çalıştırmalarını görmek için **Eylemler** sütunundaki **Etkinlik Çalıştırmalarını Görüntüle** bağlantısını seçin. Bu örnekte yalnızca bir etkinlik bulunur, bu nedenle listede yalnızca bir giriş görürsünüz. Kopyalama işlemiyle ilgili ayrıntılar için **Eylemler** sütunundaki **Ayrıntılar** bağlantısını (gözlük simgesi) seçin. İşlem hattı çalıştırmaları görünümüne dönmek için üstteki işlem **hattı çalıştırmalarını** seçin. Görünümü yenilemek için **Yenile**’yi seçin.
1. Verilerinizin adanmış SQL havuzunda doğru yazıldığından emin olun.


## <a name="next-steps"></a>Sonraki adımlar

Azure SYNAPSE Analytics veri tümleştirmesi hakkında daha fazla bilgi için bkz. [Özel BIR SQL havuzunda verileri](data-integration-sql-pool.md) açma.