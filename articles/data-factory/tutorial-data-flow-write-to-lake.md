---
title: Veri akışları ile Data Lake 'e dosya yazmak için en iyi uygulamalar
description: Bu öğreticide veri akışlarla Data Lake 'e dosya yazmak için en iyi yöntemler sunulmaktadır
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582321"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Veri akışları ile Data Lake 'e dosya yazmak için en iyi uygulamalar

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory kullanmaya yeni başlıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Bu öğreticide, veri akışları kullanılarak ADLS 2. veya Azure Blob depolama alanına dosya yazarken uygulanabilecek en iyi uygulamaları öğreneceksiniz. Bir Azure Blob depolama hesabına erişmeniz veya bir Parquet dosyasını okumak ve sonuçları klasörlerde depolamak için Azure Data Lake Store Gen2 hesabına erişmeniz gerekir.

## <a name="prerequisites"></a>Önkoşullar
* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**. ADLS depolamayı *kaynak* ve *Havuz* veri depoları olarak kullanırsınız. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Azure depolama hesabı oluşturma](../storage/common/storage-account-create.md).

Bu öğreticideki adımlarda, sahip olduğunuz varsayılmaktadır 

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Bu adımda, bir veri fabrikası oluşturur ve veri fabrikasında bir işlem hattı oluşturmak için Data Factory UX 'i açarsınız.

1. **Microsoft Edge** veya **Google Chrome**'ı açın. Şu anda Data Factory UI yalnızca Microsoft Edge ve Google Chrome Web tarayıcılarında desteklenir.
1. Sol taraftaki menüden **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin.
1. **Yeni Veri Fabrikası** sayfasında, **ad**' ın altına **ADFTutorialDataFactory** girin.
1. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin.
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

    a. **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.
    
    b. **Yeni oluştur**' u seçin ve bir kaynak grubunun adını girin. Kaynak grupları hakkında bilgi edinmek için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).
    
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum** bölümünden veri fabrikası için bir konum seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri Fabrikası tarafından kullanılan veri depoları (örneğin, Azure depolama ve SQL veritabanı) ve işlemler (örneğin, Azure HDInsight) başka bölgelerde olabilir.
1. **Oluştur**’u seçin.
1. Oluşturma işlemi tamamlandıktan sonra, Bildirim Merkezi ' nde bildirimi görürsünüz. Data Factory sayfasına gitmek için **Kaynağa Git** ' i seçin.
1. Data Factory Kullanıcı Arabirimini (UI) ayrı bir sekmede başlatmak için **Geliştir ve İzle**’yi seçin.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Veri akışı etkinliği ile işlem hattı oluşturma

Bu adımda, bir veri akışı etkinliği içeren bir işlem hattı oluşturacaksınız.

1. **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin.

   ![İşlem hattı oluşturma](./media/doc-common-process/get-started-page.png)

1. İşlem hattının **genel** sekmesinde, Işlem hattının **adı** için **deltalake** girin.
1. Fabrika üst çubuğunda, **veri akışı hata ayıklama** kaydırıcısını üzerine kaydırın. Hata ayıklama modu, canlı bir Spark kümesine karşı dönüştürme mantığının etkileşimli olarak test edilmesine olanak tanır. Veri akışı kümelerinin ısınma için 5-7 dakika sürer ve veri akışı geliştirmeyi planlarsa, kullanıcıların hata ayıklamanın ilk kez etkinleştirilmesi önerilir. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

    ![Veri akışı etkinliği](media/tutorial-data-flow/dataflow1.png)
1. **Etkinlikler** bölmesinde, **taşıma ve dönüştürme** Accordion ' ı genişletin. Bölmedeki **veri akışı** etkinliğini, işlem hattı tuvaline sürükleyin ve bırakın.

    ![Veri akışı etkinliğini geçirebileceğiniz işlem hattı tuvalini gösteren ekran görüntüsü.](media/tutorial-data-flow/activity1.png)
1. **Veri akışı ekleme** açılır penceresinde **Yeni veri akışı oluştur** ' u seçin ve ardından veri akışınızı **deltalake** olarak adlandırın. Bittiğinde son ' a tıklayın.

    ![Yeni bir veri akışı oluştururken veri akışınızı nerede bulabileceğiniz gösteren ekran görüntüsü.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Veri akışı tuvalinde dönüştürme mantığı oluşturma

Herhangi bir kaynak veri alacaksınız (Bu öğreticide, bir Parquet dosya kaynağı kullanacağız) ve Data Lake ETL için en etkili mekanizmaların kullanıldığı verileri Parquet biçimine eklemek için bir havuz dönüşümü kullanın.

![Son akış](media/data-flow/parts-final.png "Son akış")

### <a name="tutorial-objectives"></a>Öğretici amaçları

1. Yeni bir veri akışında kaynak veri kümelerinizi seçin
1. Havuz veri kümenizi etkin bir şekilde bölümlemek için veri akışlarını kullanma
1. Bölümlendirilmiş verilerinizi ADLS 2. Gölü klasörlerine kara olarak

### <a name="start-from-a-blank-data-flow-canvas"></a>Boş bir veri akışı tuvalinden başlayın

İlk olarak, aşağıdaki ADLS 2. giriş verileri için aşağıda açıklanan mekanizmaların her biri için veri akışı ortamını ayarlayalım

1. Kaynak dönüştürmesi ' ne tıklayın.
1. Alt panelde DataSet ' in yanındaki yeni düğmesine tıklayın.
1. Bir veri kümesi seçin ya da yeni bir tane oluşturun. Bu tanıtımda Kullanıcı verileri adlı bir Parquet veri kümesi kullanacağız.
1. Türetilmiş bir sütun dönüştürmesi ekleyin. Bunu, istenen klasör adlarınızı dinamik olarak ayarlamanıza olanak verecek şekilde kullanacağız.
1. Havuz dönüştürmesi ekleyin.
   
### <a name="hierarchical-folder-output"></a>Hiyerarşik klasör çıkışı

Verilerinizi Gölü bölümleyerek bölümlemek üzere klasör hiyerarşileri oluşturmak için verilerinize benzersiz değerler kullanmak çok yaygındır. Bu, Gölü ve Spark 'ta (veri akışlarının arkasındaki işlem altyapısı) verileri düzenlemek ve işlemek için çok iyi bir yoldur. Ancak, çıktını bu şekilde düzenlemek için küçük bir performans maliyeti olacaktır. Havuzda bu mekanizmayı kullanarak genel işlem hattı performansında küçük bir azalma görmeniz beklenir.

1. Veri akışı Tasarımcısına dönün ve yukarıdaki veri akışı oluşturmayı düzenleyin. Havuz dönüşümüne tıklayın.
1. En Iyileştirme > bölümlendirme > anahtarını ayarla ' ya tıklayın
1. Hiyerarşik klasör yapınızı ayarlamak için kullanmak istediğiniz sütunları seçin.
1. Aşağıdaki örnekte, klasör adlandırma sütunları olarak Year ve month kullanılır. Sonuçlar formun klasörleri olacaktır ```releaseyear=1990/month=8``` .
1. Veri akışı kaynağındaki veri bölümlerine erişirken, yukarıdaki en üst düzey klasöre işaret eder ```releaseyear``` ve sonraki her klasör için bir joker karakter stili kullanın, örn. ```**/**/*.parquet```
1. Veri değerlerini değiştirmek için veya klasör adları için yapay değerler üretmek zorunda olsa bile, klasör adlarında kullanmak istediğiniz değerleri oluşturmak için türetilmiş sütun dönüşümünü kullanın.

![Anahtar bölümleme](media/data-flow/key-parts.png "Anahtar bölümleme")
   
### <a name="name-folder-as-data-values"></a>Klasörü veri değerleri olarak Adlandır

Anahtar/değer bölümleme ile aynı avantajı sunmayan ADLS 2. kullanarak Gölü verileri için biraz daha iyi bir havuz tekniği ```Name folder as column data``` . Hiyerarşik yapının anahtar bölümleme stili veri dilimlerini daha kolay bir şekilde işlememize izin verirken, bu teknik, verileri daha hızlı yazabilmesi için düzleştirilmiş bir klasör yapısıdır.

1. Veri akışı Tasarımcısına dönün ve yukarıdaki veri akışı oluşturmayı düzenleyin. Havuz dönüşümüne tıklayın.
1. En Iyileştirme > bölümlendirme ayarla > geçerli bölümleme kullan ' a tıklayın.
1. Ayarlar > klasörü sütun verileri olarak Adlandır ' a tıklayın.
1. Klasör adları oluşturmak için kullanmak istediğiniz sütunu seçin.
1. Veri değerlerini değiştirmek için veya klasör adları için yapay değerler üretmek zorunda olsa bile, klasör adlarında kullanmak istediğiniz değerleri oluşturmak için türetilmiş sütun dönüşümünü kullanın.

![Klasör seçeneği](media/data-flow/folders.png "Klasörler")

### <a name="name-file-as-data-values"></a>Dosyayı veri değerleri olarak Adlandır

Yukarıdaki öğreticilerde listelenen teknikler, Veri Gölü içinde klasör kategorileri oluşturmaya yönelik iyi kullanım çalışmalardır. Bu teknikler tarafından kullanılan varsayılan dosya adlandırma şeması Spark yürütücü iş KIMLIĞINI kullanmaktır. Bazen bir veri akışı metin havuzunda çıkış dosyasının adını ayarlamak isteyebilirsiniz. Bu teknik yalnızca küçük dosyalarla kullanım için önerilir. Bölüm dosyalarını tek bir çıkış dosyasına birleştirme işlemi uzun süredir çalışan bir işlemdir.

1. Veri akışı Tasarımcısına dönün ve yukarıdaki veri akışı oluşturmayı düzenleyin. Havuz dönüşümüne tıklayın.
1. Tek bölüm > bölümlendirme > ayarla ' ya tıklayın. Dosyalar birleştirildiğinde yürütme işleminde bir performans sorunu oluşturan bu tek bölümleme gereksinimidir. Bu seçenek yalnızca küçük dosyalar için önerilir.
1. Ayarlar > dosyayı sütun verileri olarak Adlandır ' a tıklayın.
1. Dosya adları oluşturmak için kullanmak istediğiniz sütunu seçin.
1. Veri değerlerini değiştirmek veya dosya adları için yapay değerler üretmesinin gerekli olması durumunda bile, dosya adlarında kullanmak istediğiniz değerleri oluşturmak için türetilmiş sütun dönüşümünü kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[Veri akışı havuzları](data-flow-sink.md)hakkında daha fazla bilgi edinin.
