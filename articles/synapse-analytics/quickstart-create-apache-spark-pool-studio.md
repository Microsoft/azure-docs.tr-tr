---
title: 'Hızlı başlangıç: SYNAPSE Studio kullanarak sunucusuz Apache Spark havuzu oluşturma'
description: Bu kılavuzdaki adımları izleyerek SYNAPSE Studio kullanarak bir sunucusuz Apache Spark havuzu oluşturun.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/16/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d280e96b354f38a86545ed5f6bcaa6c9a314c46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462038"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-using-synapse-studio"></a>Hızlı başlangıç: SYNAPSE Studio kullanarak sunucusuz Apache Spark havuzu oluşturma

Azure SYNAPSE Analytics, verilerinizi almanıza, dönüştürmenizi, modellemenizi, çözümlemenize ve sungetirmenize yardımcı olmak için çeşitli analiz motorları sunmaktadır. Apache Spark havuz, açık kaynak büyük veri işlem yetenekleri sunar. SYNAPSE çalışma alanınızda bir Apache Spark havuzu oluşturduktan sonra, veriler yüklenebilir, modellenebilir, işlenebilir ve öngörüleri elde etmek için sunulabilir.  

Bu hızlı başlangıçta SYNAPSE Studio kullanarak bir Synapse çalışma alanında Apache Spark havuzu oluşturma adımları açıklanmaktadır.

> [!IMPORTANT]
> Spark örnekleri için faturalandırma, bunları kullanıp kullanmayacağınızı de dakika başına eşit olarak dağıtılır. Kullanmayı bitirdikten sonra Spark örneğinizi kapattığınızdan emin olun veya kısa bir zaman aşımı ayarlayın. Daha fazla bilgi için bu makalenin **Kaynakları temizleme** bölümüne bakın.

Azure aboneliğiniz yoksa [başlamadan önce ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [SYNAPSE çalışma alanı](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalda](https://portal.azure.com/) oturum açma

## <a name="navigate-to-the-synapse-workspace"></a>SYNAPSE çalışma alanına gidin

1. Arama çubuğuna hizmet adı (veya doğrudan kaynak adı) yazarak Apache Spark havuzunun oluşturulacağı SYNAPSE çalışma alanına gidin.

    ![İçinde SYNAPSE çalışma alanlarıyla yazılan arama çubuğu Azure portal.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Çalışma alanları listesinden, açmak için çalışma alanının adını (veya adının bir bölümünü) yazın. Bu örnekte, **contosoanalytics** adlı bir çalışma alanı kullanacağız.

    ![Contoso adını içeren bunları göstermek üzere filtrelenmiş SYNAPSE çalışma alanlarının listesi.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Synapse Studio'yu başlatma 

Çalışma alanına genel bakış ' da, SYNAPSE Studio 'Yu açmak için **çalışma alanı Web URL** 'sini seçin.

![Azure portal SYNAPSE çalışma alanına genel bakış ve Launch SYNAPSE Studio vurgulanmış.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>SYNAPSE Studio 'da Apache Spark havuzunu oluşturma

1. SYNAPSE Studio giriş sayfasında, **Yönet** simgesini seçerek sol gezinti bölmesinde **yönetim merkezine** gidin.

    ![Yönetim Merkezi bölümü vurgulanmış olan SYNAPSE Studio giriş sayfası.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Yönetim hub 'ında bir kez, çalışma alanında kullanılabilen Apache Spark havuzlarının geçerli listesini görmek için **Apache Spark havuzları** bölümüne gidin.
    
    ![Apache Spark havuzları gezintisi seçiliyken SYNAPSE Studio Yönetim Merkezi](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. **+ Yeni** ' yi seçin ve yeni Apache Spark havuzu oluşturma Sihirbazı görüntülenir. 

1. **Temel bilgiler** sekmesinde aşağıdaki ayrıntıları girin:

    | Ayar | Önerilen değer | Açıklama |
    | :------ | :-------------- | :---------- |
    | **Apache Spark havuzu adı** | contosospark | Bu, Apache Spark havuzunun sahip olacağı addır. |
    | **Düğüm boyutu** | Küçük (4 vCPU/32 GB) | Bu hızlı başlangıç için maliyetleri azaltmak üzere en küçük boyuta ayarlayın |
    | **Otomatik Ölçeklendirme** | Devre dışı | Bu hızlı başlangıçta otomatik ölçeklendirme gerekmiyor |
    | **Düğüm sayısı** | 8 | Bu hızlı başlangıçta maliyetleri sınırlandırmak için küçük bir boyut kullanın|
       
    ![SYNAPSE Studio yeni Apache Spark havuzu için temel bilgiler](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    
    > [!IMPORTANT]
    > Apache Spark havuzların kullanabileceği adlarla ilgili özel sınırlamalar olduğunu unutmayın. Adlar yalnızca harf veya sayı içermelidir, 15 veya daha az karakter olmalıdır, bir harfle başlamalı, ayrılmış sözcükler içermemelidir ve çalışma alanında benzersiz olmalıdır.

1. Sonraki sekmede (ek ayarlar), tüm ayarları varsayılan olarak bırakın.

1. Şimdilik hiç etiket eklememiz, bu nedenle **gözden geçir + oluştur**' u seçin.

1. **Gözden geçir + oluştur** sekmesinde, ayrıntıların daha önce girilmiş olan öğesine göre doğru göründüğünden emin olun ve **Oluştur**' a basın. 

    ![SYNAPSE Studio yeni Apache Spark Havuzu Oluştur](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. Apache Spark havuz, sağlama işlemini başlatacak.

1. Sağlama tamamlandıktan sonra yeni Apache Spark havuzu listede görüntülenir.
    
    ![SYNAPSE Studio yeni Apache Spark havuz listesi](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>SYNAPSE Studio 'Yu kullanarak Apache Spark havuzu kaynaklarını Temizleme

Apache Spark havuzunu SYNAPSE Studio kullanarak çalışma alanından silmek için aşağıdaki adımları izleyin.
> [!WARNING]
> Spark havuzunu silme, analiz altyapısını çalışma alanından kaldırır. Havuza bağlanmayacak ve bu Spark havuzunu kullanan tüm sorgular, işlem hatları ve Not defterleri artık çalışmayacak.

Apache Spark havuzunu silmek istiyorsanız, aşağıdakileri yapın:

1. SYNAPSE Studio 'daki yönetim hub 'ındaki Apache Spark havuzlarına gidin.
1. Apache Spark havuzunun komutlarını göstermek için, silinecek Apache havuzunun yanındaki üç nokta simgesini (Bu örnekte, **contosospark**) seçin.

    ![Son oluşturulan havuz seçiliyken Apache Spark havuzlarının listelenmesi.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)

1. **Sil**'e basın.
1. Silmeyi onaylayın ve **Sil** düğmesine basın.
1. İşlem başarıyla tamamlandığında, Apache Spark havuzu artık çalışma alanı kaynaklarında listelenmeyecektir. 

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [hızlı başlangıç: Web araçlarını kullanarak SYNAPSE Studio 'da Apache Spark havuzu oluşturma](quickstart-apache-spark-notebook.md).
- Bkz. [hızlı başlangıç: Azure Portal kullanarak Apache Spark havuzu oluşturma](quickstart-create-apache-spark-pool-portal.md).
