---
title: Data Lake araçlarla kaynaklara erişme
description: Azure Data Lake Analytics kaynaklarına erişmek için Azure Data Lake araçlarını kullanmayı öğrenin.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754758"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Azure Data Lake araçlarla kaynaklara erişme

Azure Data Tools komutları veya eylemleri VS Code kolayca Azure Data Lake Analytics kaynaklara erişebilirsiniz.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Komut ile Azure Data Lake Analytics tümleştirme

Hesapları listelemek, meta verilere erişmek ve analiz işlerini görüntülemek için Azure Data Lake Analytics kaynaklarına erişebilirsiniz.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Azure aboneliğinizin altındaki Azure Data Lake Analytics hesaplarını listelemek için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Adl: List hesaplarını** girin. Hesaplar **Çıkış** bölmesinde görünür.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Azure Data Lake Analytics meta verilerine erişmek için

1. CTRL + SHIFT + P ' yi seçin ve ardından **adl: List tablolarını** girin.
2. Data Lake Analytics hesaplarından birini seçin.
3. Data Lake Analytics veritabanlarından birini seçin.
4. Şemalardan birini seçin. Tablo listesini görebilirsiniz.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics işleri görüntülemek için

1. Komut paletini açın (Ctrl + Shift + P) ve **adl: Işleri göster**' i seçin.
2. Data Lake Analytics veya yerel hesap seçin.
3. Hesap için iş listesinin görünmesini bekleyin.
4. İş listesinden bir iş seçin. Data Lake araçlar, sağ bölmedeki iş görünümünü açar ve VS Code çıkışında bazı bilgileri görüntüler.

    ![İş listesi](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Komut ile Azure Data Lake Store tümleştirme

Azure Data Lake Store ilgili komutları kullanarak şunları yapabilirsiniz:

- [Azure Data Lake Store kaynaklarına göz at](#list-the-storage-path)
- [Azure Data Lake Store dosyasını önizleyin](#preview-the-storage-file)
- Dosyayı doğrudan VS Code Azure Data Lake Store yükleme
- Dosyayı doğrudan VS Code Azure Data Lake Store 'tan indirin

### <a name="list-the-storage-path"></a>Depolama yolunu listeleme

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Depolama yolunu komut paleti aracılığıyla listelemek için

1. Betik düzenleyicisine sağ tıklayın ve **adl: liste yolu**' nu seçin.
2. Listeden klasörü seçin veya **bir yol girin** ' i veya **kök yolundan gözatıp**' yi seçin. (Örnek olarak **bir yol girin** .)
3. Data Lake Analytics hesabınızı seçin.
4. Depolama klasörü yoluna gidin veya (örneğin,/output/) girin.  

Komut paleti, girdinize bağlı olarak yol bilgilerini listeler.

![Depolama yolu sonuçları](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Göreli yolu listelemek için daha uygun bir yol, kısayol menüsü kullanmaktır.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Kısayol menüsü aracılığıyla depolama yolunu listelemek için

Yol dizesine sağ tıklayın ve **liste yolu**' nu seçin.

![Kısayol menüsünde "liste yolu"](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Depolama dosyasının önizlemesini görüntüleyin

1. Betik düzenleyicisine sağ tıklayın ve **adl: Preview dosyası**' nı seçin.
2. Data Lake Analytics hesabınızı seçin.
3. Bir Azure depolama dosya yolu girin (örneğin,/Output/SearchLog.txt).

Dosya VS Code açılır.

![Adımlar ve depolama dosyasının önizlenmesi sonucu](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Dosyayı önizlemenin bir başka yolu da dosyanın tam yolundaki kısayol menüsü veya dosya Düzenleyicisi 'ndeki dosyanın göreli yolu ile yapılır.

### <a name="upload-a-file-or-folder"></a>Dosya veya klasörü karşıya yükleme

1. Betik düzenleyicisine sağ tıklayıp **dosyayı karşıya yükle** veya **klasörü karşıya yükle**' yi seçin.
2. **Karşıya dosya yükle**' yi seçtiyseniz bir dosya veya birden çok dosya seçin veya **klasörü karşıya yükle**' yi seçtiyseniz tüm klasörü seçin. Ardından **Karşıya Yükle**’yi seçin.
3. Listeden depolama klasörünü seçin veya **bir yol girin** veya **kök yolundan gözatıp**' yi seçin. (Örnek olarak **bir yol girin** .)
4. Data Lake Analytics hesabınızı seçin.
5. Depolama klasörü yoluna gidin veya (örneğin,/output/) girin.
6. Karşıya yükleme hedefini belirtmek için **geçerli klasörü seç** ' i seçin.

![Dosya veya klasör yükleme adımları ve sonuçları](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Dosyaları depolama alanına yüklemenin başka bir yolu da dosyanın tam yolundaki kısayol menüsünün veya dosya Düzenleyicisi 'ndeki dosyanın göreli yolunda yer alır.

[Karşıya yükleme durumunu izleyebilirsiniz](#check-storage-tasks-status).

### <a name="download-a-file"></a>Dosya indirme

**Adl: Download File** veya **adl: Download FILE (Gelişmiş)** komutunu kullanarak bir dosyayı indirebilirsiniz.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>ADL: Download File (Gelişmiş) komutuyla bir dosyayı indirmek için

1. Betik düzenleyicisine sağ tıklayıp **dosya indir (Gelişmiş)** öğesini seçin.
2. VS Code bir JSON dosyası görüntüler. Dosya yollarını girebilir ve aynı anda birden çok dosyayı indirebilirsiniz. Yönergeler **Çıkış** penceresinde görüntülenir. Dosya veya dosyaları karşıdan yüklemeye devam etmek için JSON dosyasını kaydedin (CTRL + S).

    ![Dosya indirme yollarındaki JSON dosyası](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

**Çıkış** penceresinde, dosya indirme durumu görüntülenir.

![İndirme durumundaki çıkış penceresi](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

[İndirme durumunu izleyebilirsiniz](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>ADL: Download File komutuyla bir dosyayı indirmek için

1. Betik Düzenleyicisi ' ne sağ tıklayın, **dosya indir**' i seçin ve ardından **Klasör Seç** iletişim kutusunda hedef klasörü seçin.

1. Listeden klasörü seçin veya **bir yol girin** ' i veya **kök yolundan gözatıp**' yi seçin. (Örnek olarak **bir yol girin** .)

1. Data Lake Analytics hesabınızı seçin.

1. Depolama klasörü yoluna gidin veya girin (örneğin,/output/) ve ardından İndirilecek dosyayı seçin.

![Adımlar ve dosya indirme sonucu](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Depolama dosyalarını karşıdan yüklemenin başka bir yolu da dosyanın tam yolundaki kısayol menüsünün veya dosya Düzenleyicisi 'ndeki dosyanın göreli yolunda yer alır.

[İndirme durumunu izleyebilirsiniz](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Depolama görevlerinin durumunu denetle

Karşıya yükleme ve indirme durumu durum çubuğunda görüntülenir. Durum çubuğunu seçin ve ardından **Çıkış** sekmesinde durum görüntülenir.

![Durum çubuğu ve çıkış ayrıntıları](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Gezgin Azure Data Lake Analytics ile tümleştirme

Oturum açtıktan sonra Azure hesabınız için tüm abonelikler, **Azure Data Lake** altında sol bölmede listelenir.

![Data Lake Gezgini](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics meta veri gezintisi

Azure aboneliğinizi genişletin. **U-SQL veritabanları** düğümü altında, u-SQL veritabanınıza göz atabilir ve **şemalar**, **kimlik bilgileri**, **derlemeler**, **Tablolar** ve **Dizin** gibi klasörleri görüntüleyebilirsiniz.

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics meta veri varlık yönetimi

**U-SQL veritabanlarını** genişletin. Karşılık gelen düğüme sağ tıklayıp ardından kısayol menüsünde **oluşturulacak betiği** seçerek bir veritabanı, şema, tablo, tablo türü, dizin veya istatistik oluşturabilirsiniz. Açılan komut dosyası sayfasında, komut dosyasını gereksinimlerinize göre düzenleyin. Ardından, sağ tıklayıp **adl: gönder işini** seçerek işi gönderebilirsiniz.

Öğeyi oluşturmayı tamamladıktan sonra, düğümüne sağ tıklayın ve ardından öğeyi göstermek için **Yenile** ' yi seçin. Öğeyi sağ tıklayıp **Sil**' i seçerek de silebilirsiniz.

![Data Lake Gezgini 'ndeki kısayol menüsünde "oluşturulacak komut dosyası" komutu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Yeni öğe için komut dosyası sayfası](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics bütünleştirilmiş kod kaydı

**Derlemeler** düğümüne sağ tıklayıp **derlemeyi kaydet**' i seçerek ilgili veritabanına bir derlemeyi kaydedebilirsiniz.

![Derlemeler düğümünün kısayol menüsünde "derlemeyi Kaydet" komutu](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Gezgin Azure Data Lake Store ile tümleştirme

**Data Lake Store** gidin:

- Klasör düğümüne sağ tıklayıp **Yenile**, **Sil**, **karşıya yükle**, **klasörü karşıya yükle**, **göreli yolu Kopyala** ve kısayol menüsünde **tam yolu Kopyala** komutlarını kullanabilirsiniz.

   ![Data Lake Gezgininde klasör düğümü için kısayol menü komutları](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Dosya düğümüne sağ tıklayıp **Önizleme**, **indirme**, **silme**, **ayıklama betiği oluşturma** (yalnızca CSV, TSV ve txt dosyaları için kullanılabilir), **göreli yolu Kopyala** ve kısayol menüsünde **tam yolu Kopyala** komutlarını kullanabilirsiniz.

   ![Data Lake Gezgininde bir dosya düğümü için kısayol menü komutları](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Gezgin 'den Azure Blob depolama ile tümleştirme

Blob depolamaya gidin:

- Blob kapsayıcı düğümüne sağ tıklayıp ardından **Yenile**, **BLOB kapsayıcısını Sil** ve BLOB komutlarını kısayol menüsünde **Yükle** komutlarını kullanabilirsiniz.

   ![BLOB depolama alanındaki bir blob kapsayıcı düğümü için kısayol menü komutları](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Klasör düğümüne sağ tıklayıp ardından kısayol menüsünde blobu **Yenile** ve **karşıya yükle** komutlarını kullanabilirsiniz.

   ![BLOB depolama altındaki klasör düğümü için kısayol menü komutları](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Dosya düğümüne sağ tıklayıp **Önizleme/düzenleme**, **indirme**, **silme**, **ayıklama betiği oluşturma** (yalnızca CSV, TSV ve txt dosyaları için kullanılabilir), **göreli yolu Kopyala** ve kısayol menüsünde **tam yolu Kopyala** komutlarını kullanabilirsiniz.

    ![BLOB depolama alanındaki bir dosya düğümü için kısayol menü komutları](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Portalda Data Lake Gezginini açın

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Açık web Azure Depolama Gezgini** girin veya bir göreli yolu ya da bir tam yolu sağ tıklatın ve ardından **Web Azure Depolama Gezgini aç**' ı seçin.
3. Bir Data Lake Analytics hesabı seçin.

Data Lake araçlar Azure portal Azure depolama yolunu açar. Yolu bulabilir ve dosyanın önizlemesini web 'den görüntüleyebilirsiniz.

## <a name="additional-features"></a>Ek özellikler

VS Code için Data Lake araçları aşağıdaki özellikleri destekler:

- **IntelliSense otomatik tamamlama**: öneriler, Yöntemler ve değişkenler gibi öğelerin etrafında açılır pencereler içinde görünür. Farklı simgeler farklı nesne türlerini temsil eder:

  - Scala veri türü
  - Karmaşık veri türü
  - Yerleşik Udtalar
  - .NET koleksiyonu ve sınıfları
  - C# ifadeleri
  - Yerleşik C# UDF 'Leri, UDOs ve Udadags
  - U-SQL işlevleri
  - U-SQL Pencereleme işlevleri

    ![IntelliSense nesne türleri](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Data Lake Analytics meta verilerde IntelliSense otomatik tamamlama**: Data Lake araçları Data Lake Analytics meta veri bilgilerini yerel olarak indirir. IntelliSense özelliği, nesneleri Data Lake Analytics meta verilerden otomatik olarak doldurur. Bu nesneler veritabanı, şema, tablo, görünüm, tablo değerli işlev, yordamlar ve C# derlemelerini içerir.

  ![IntelliSense meta verileri](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense hata işaretleyicisi**: Data Lake araçları, U-SQL ve C# için hataları düzenlemenin alt çizgileri.
- **Sözdizimi vurguları**: Data Lake araçları, değişkenleri, anahtar sözcükleri, veri türleri ve işlevleri gibi öğeleri ayırt etmek için renkler kullanır.

    ![Çeşitli renklerle sözdizimi](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Visual Studio için Azure Data Lake Araçları sürüm 2.3.3000.4 veya sonraki bir sürüme yükseltmenizi öneririz. Önceki sürümler artık indirilemiyor ve kullanım dışı.  

## <a name="next-steps"></a>Sonraki adımlar

- [VS Code Azure Data Lake Analytics için Python, R ve C Sharp ile U-SQL geliştirme](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL yerel çalıştırma ve Visual Studio Code ile yerel hata ayıklama](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Öğretici: Azure Data Lake Analytics kullanmaya başlayın](data-lake-analytics-get-started-portal.md)
- [Öğretici: Visual Studio için Data Lake araçları 'nı kullanarak U-SQL betikleri geliştirme](data-lake-analytics-data-lake-tools-get-started.md)