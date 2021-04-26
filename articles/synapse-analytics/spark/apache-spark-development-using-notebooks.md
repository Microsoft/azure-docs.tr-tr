---
title: SYNAPSE Studio Not defterleri
description: Bu makalede, veri hazırlama ve görselleştirme için Azure SYNAPSE Studio Not defterleri oluşturmayı ve geliştirmeyi öğreneceksiniz.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: c28bc47945882e6b7bbd39d1ba8524a1f7491ceb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835226"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te SYNAPSE Studio Not defterleri oluşturma, geliştirme ve bakımını yapma

SYNAPSE Studio Not defteri, canlı kod, görselleştirme ve anlatım metni içeren dosyalar oluşturmanız için bir Web arabirimidir. Not defterleri, fikirlerinizi doğrulamak ve hızlı denemeleri kullanarak verilerinizden Öngörüler elde etmek için iyi bir yerdir. Not defterleri, veri hazırlama, veri görselleştirme, makine öğrenimi ve diğer büyük veri senaryolarında da yaygın olarak kullanılır.

Bir Azure SYNAPSE Studio not defteriyle şunları yapabilirsiniz:

* Sıfır kurulum çabası ile çalışmaya başlayın.
* Yerleşik kurumsal güvenlik özellikleriyle verileri güvende tutun.
* Ham biçimlerdeki verileri (CSV, txt, JSON, vb.), işlenen dosya biçimlerini (Parquet, Delta Gölü, ORC, vb.) ve SQL tablolu veri dosyalarını Spark ve SQL 'de analiz edin.
* Gelişmiş yazma özellikleri ve yerleşik veri görselleştirmesi ile üretken olun.

Bu makalede, Azure SYNAPSE Studio 'da Not defterlerinin nasıl kullanılacağı açıklanır.

## <a name="preview-of-the-new-notebook-experience"></a>Yeni Not defteri deneyiminin önizlemesi
SYNAPSE ekibi, Microsoft müşterileri için tutarlı Not defteri deneyimi sağlamak ve bulunabilirliği, verimliliği, paylaşımı ve işbirliğini en üst düzeye çıkarmak için yeni not defterleri bileşenini SYNAPSE Studio 'ya getirdi. Yeni Not defteri deneyimi önizleme için hazırlanıyor. Açmak için Not defteri araç çubuğundaki **Önizleme özellikleri** düğmesine bakın. Aşağıdaki tablo, mevcut not defteri (yani "klasik Not defteri" olarak adlandırılır) arasındaki özelliği karşılaştırmayı yeni önizleme ile yakalar.  

|Özellik|Klasik Not defteri|İnceleme Not defteri|
|--|--|--|
|çalıştırma yüzdesi| Desteklenmez | &#9745;|
|geçmiş yüzdesi| Desteklenmez |&#9745;
|Yük yüzdesi| Desteklenmez |&#9745;|
|%% HTML| Desteklenmez |&#9745;|
|Hücreyi taşımak için sürükle ve bırak| Desteklenmez |&#9745;|
|Araç çubuğu düğmeleriyle metin hücresini Biçimlendir|&#9745;| Kullanılamaz |
|Hücre işlemini geri al| &#9745;| Kullanılamaz |


## <a name="create-a-notebook"></a>Not defteri oluşturma

Bir not defteri oluşturmanın iki yolu vardır. Yeni bir not defteri oluşturabilir veya var olan bir not defterini **Nesne Gezgini** bir Azure SYNAPSE çalışma alanına aktarabilirsiniz. Azure SYNAPSE Studio Not defterleri, standart Jupyter Notebook IPYNB dosyalarını algılayabilir.

![İçeri aktarma Not Defteri Oluştur](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Not defterleri geliştirme

Not defterleri, bağımsız olarak veya grup olarak çalıştırılan kod veya metin blokları olan hücrelerden oluşur.

### <a name="add-a-cell"></a>Hücre Ekle

Not defterinize yeni bir hücre eklemenin birden çok yolu vardır.

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

1. Sol üst **+ hücre** düğmesini genişletin ve **kod hücresi Ekle** ya da **metin hücresi Ekle**' yi seçin.

    ![Ekle-hücre-düğme](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. İki hücre arasındaki alanın üzerine gelin ve **Kod Ekle** veya **metin ekle**' yi seçin.

    ![boşluk Ekle-boşluk](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [Komut modu altında kısayol tuşlarını](#shortcut-keys-under-command-mode)kullanın. Geçerli hücrenin üstüne bir hücre eklemek için **a** 'ya basın. Geçerli hücrenin altına bir hücre eklemek için **B** tuşuna basın.


# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

1. Sol üst **+ hücre** düğmesini genişletin ve **kod hücresi** veya **markaşağı hücresini** seçin.
    ![Ekle-Azure-Not defteri-hücreli-hücre-düğme](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Hücrenin başındaki artı işaretini seçin ve **kod hücresi** veya **markaşağı hücresini** seçin.

    ![Ekle-Azure-Not defteri-boşluk-boşluk](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. [Komut modu altında aznb kısayol tuşlarını](#shortcut-keys-under-command-mode)kullanın. Geçerli hücrenin üstüne bir hücre eklemek için **a** 'ya basın. Geçerli hücrenin altına bir hücre eklemek için **B** tuşuna basın.

---

### <a name="set-a-primary-language"></a>Birincil dil ayarla

Azure SYNAPSE Studio Not defterleri dört Apache Spark dili destekler:

* pySpark (Python)
* Spark (Scala)
* Mini SQL
* Apache Spark için .NET (C#)

En üstteki komut çubuğundaki açılan listeden yeni eklenen hücreler için birincil dili ayarlayabilirsiniz.

   ![Varsayılan-SYNAPSE-Language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Birden çok dil kullanma

Bir hücrenin başlangıcında doğru dil Magic komutunu belirterek, bir not defterinde birden çok dil kullanabilirsiniz. Aşağıdaki tabloda, hücre dillerini değiştirmek için Magic komutları listelenmektedir.

|Magic komutu |Dil | Description |  
|---|------|-----|
|%% pyspark| Python | Spark bağlamına karşı bir **Python** sorgusu yürütün.  |
|%% Spark| Scala | Spark bağlamına yönelik bir **Scala** sorgusu yürütün.  |  
|%% SQL| Mini SQL | Spark bağlamına karşı bir **mini SQL** sorgusu yürütün.  |
|%% CSharp | Spark C için .NET # | Spark bağlamına karşı **Spark C# için .net** sorgusu yürütün. |

Aşağıdaki görüntü, bir **Spark (Scala)** not defterinde%% **pyspark** Magic komutunu veya **%% SQL** Magic komutuyla bir mini SQL sorgusunu kullanarak pyspark sorgusunun nasıl yazılacağını gösteren bir örnektir. Not defteri için birincil dilin pySpark olarak ayarlandığını unutmayın.

   ![SYNAPSE Spark Magic komutları](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages&quot;></a>Diller arasında verilere başvurmak için geçici tabloları kullanma

Bir Synapse Studio not defterindeki farklı dillerdeki verilere veya değişkenlere doğrudan başvurulamıyor. Spark 'ta, diller arasında geçici bir tabloya başvuru yapılabilir. Bir `Scala` `PySpark` `SparkSQL` geçici çözüm olarak Spark geçici tablosunu kullanarak ve ' de bir veri çerçevesinin nasıl okunmasına ilişkin bir örnek aşağıda verilmiştir.

1. 1. hücrede, bir SQL havuzu bağlayıcısından Scala kullanarak bir veri çerçevesini okuyun ve geçici bir tablo oluşturun.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. 2. hücrede Spark SQL kullanarak verileri sorgulayın.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 3. hücrede, PySpark içindeki verileri kullanın.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE stili IntelliSense

Azure SYNAPSE Studio Not defterleri, IDE stili IntelliSense 'i hücre düzenleyicisine getirmek için Monako düzenleyiciyle tümleşiktir. Sözdizimi vurgusu, hata işaretçisi ve otomatik kod tamamlama, kod yazmanıza ve sorunları daha hızlı belirlemenize yardımcı olur.

IntelliSense özellikleri farklı diller için farklı ölçü düzeyleridir. Desteklenen öğeleri görmek için aşağıdaki tabloyu kullanın.

|Diller| Sözdizimi vurgusu | Söz dizimi hata Işaretleyicisi  | Sözdizimi kodu tamamlama | Değişken kodu tamamlama| Sistem Işlevi kod tamamlama| Kullanıcı Işlevi kod tamamlama| Akıllı Girinti | Kod katlama|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Spark (Scala)|Yes|Yes|Yes|Yes|-|-|-|Yes|
|Mini SQL|Yes|Yes|-|-|-|-|-|-|
|Spark için .NET (C#)|Yes|-|-|-|-|-|-|-|



### <a name="code-snippets"></a>Kod Parçacıkları

Azure SYNAPSE Studio Not defterleri, Spark oturumunuzu yapılandırma, verileri Spark veri çerçevesi olarak okuma veya Matplotlib gibi grafikleri çizme gibi yaygın olarak kullanılan kod düzenlerini girmeyi kolaylaştıran kod parçacıkları sağlar.

Kod parçacıkları [IntelliSense](#ide-style-intellisense) 'de diğer önerilerden karışık olarak görünür. Kod parçacıkları içerikleri, kod hücresi diliyle hizalanır. Kod hücresi düzenleyicisindeki kod parçacığı başlığında bir kod **parçacığı** yazarak veya herhangi bir anahtar sözcük görünürse, kullanılabilir parçacıkları görebilirsiniz. Örneğin, **okuma** yazarak çeşitli veri kaynaklarından veri okumak için kod parçacıklarının listesini görebilirsiniz.

![SYNAPSE kod parçacıkları](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)



### <a name="format-text-cell-with-toolbar-buttons"></a>Araç çubuğu düğmeleriyle metin hücresini Biçimlendir

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Ortak markaşağı eylemleri yapmak için metin hücreleri araç çubuğundaki Biçim düğmelerini kullanabilirsiniz. Bu, kalın metin, italicizing metin, kod parçacıkları ekleme, sırasız liste ekleme, sıralı liste ekleme ve URL 'den görüntü ekleme içerir.

  ![SYNAPSE metin hücresi araç çubuğu](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Önizleme Not defteri deneyimi için biçimlendirme düğmesi araç çubuğu kullanılamıyor. 

---

### <a name="undo-cell-operations"></a>Hücre işlemlerini geri al

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

En son hücre işlemini iptal etmek için **geri al** düğmesini seçin veya **CTRL + Z** tuşlarına basın. Artık en son 20 geçmiş hücresi eylemini geri alabilirsiniz. 

   ![SYNAPSE geri alma hücreleri](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Geri alma işlemi, henüz önizleme Not defteri deneyimi için kullanılamaz. 

---

### <a name="move-a-cell"></a>Bir hücreyi taşıma

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

En sağdaki diğer hücre eylemleri menüsüne erişmek için üç nokta (...) simgesini seçin. Ardından, geçerli hücreyi taşımak için **hücreyi yukarı taşı** veya **hücreyi aşağı taşı** ' yı seçin. 

[Komut modu altında kısayol tuşlarını](#shortcut-keys-under-command-mode)da kullanabilirsiniz. Geçerli hücreyi taşımak için **Ctrl + Alt + ↑** tuşlarına basın. Geçerli hücreyi aşağı taşımak için **Ctrl + Alt + ↓** tuşlarına basın.

   ![hücre taşı](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Hücrenin sol tarafına tıklayın ve istediğiniz konuma sürükleyin. 
    ![SYNAPSE taşıma hücreleri](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Bir hücreyi silme

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Bir hücreyi silmek için, en sağdaki diğer hücre eylemleri menüsüne erişmek üzere üç nokta (...) simgesini seçin ve ardından **hücreyi Sil**' i seçin. 

[Komut modu altında kısayol tuşlarını](#shortcut-keys-under-command-mode)da kullanabilirsiniz. Geçerli hücreyi silmek için **d, d** tuşuna basın.
  
   ![hücreyi Sil](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Bir hücreyi silmek için, hücrenin sağ tarafındaki Sil düğmesini seçin. 

[Komut modu altında kısayol tuşlarını](#shortcut-keys-under-command-mode)da kullanabilirsiniz. Geçerli hücreyi silmek için **SHIFT + D** tuşlarına basın. 

   ![Azure-Not defteri-silme-a-hücre](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Hücre girişini daraltma

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Daraltmak için geçerli hücrenin alt kısmındaki ok düğmesini seçin. Genişletmek için, hücre darallarken ok düğmesini seçin.

   ![daraltma-hücre girişi](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Hücre araç çubuğunda **daha fazla komut** üç nokta (...) ve geçerli hücrenin girişini daraltmak için **giriş** ' i seçin. Genişletmek için, hücre daraltıldığında **gizlenen girişi** seçin.

   ![Azure-Not defteri-Daralt-hücre-giriş](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Hücre çıkışını daraltma

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Daraltmak için geçerli hücre çıktısının sol üst kısmındaki **çıktıyı Daralt** düğmesini seçin. Genişletmek için hücre çıktısı daraltıldığında **hücre çıkışını göster** ' i seçin.

   ![daraltma-hücre çıkışı](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Geçerli hücrenin çıkışını daraltmak için hücre araç çubuğunda ve **çıktıda** **diğer komutlar** üç nokta (...) simgesini seçin. Genişletmek için, hücrenin çıktısı gizlenirken aynı düğmeyi seçin.

   ![Azure-Not defteri-daraltma-hücre-çıkış](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Not defterlerini çalıştırma

Kod hücrelerini Not defterinizde tek seferde veya tümünde çalıştırabilirsiniz. Her hücrenin durumu ve ilerlemesi not defterinde temsil edilir.

### <a name="run-a-cell"></a>Bir hücre Çalıştır

Bir hücrede kodu çalıştırmanın birkaç yolu vardır.

1. Çalıştırmak istediğiniz hücreye gelin ve **hücreyi Çalıştır** düğmesini seçin veya **CTRL + ENTER** tuşlarına basın.

   ![1. çalışma hücresi](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. [Komut modu altında kısayol tuşlarını](#shortcut-keys-under-command-mode)kullanın. Geçerli hücreyi çalıştırmak için **SHIFT + enter** tuşlarına basın ve aşağıdaki hücreyi seçin. Geçerli hücreyi çalıştırmak ve aşağıya yeni bir hücre eklemek için **Alt + Enter** tuşlarına basın.

---

### <a name="run-all-cells"></a>Tüm hücreleri Çalıştır
Geçerli not defterindeki tüm hücreleri sırayla çalıştırmak için **Tümünü Çalıştır** düğmesini seçin.

   ![tüm hücreleri Çalıştır](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Yukarıdaki veya altındaki tüm hücreleri Çalıştır

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

En sağdaki diğer hücre eylemleri menüsüne erişmek için üç nokta (**...**) simgesini seçin. Ardından, geçerli sıradaki tüm hücreleri çalıştırmak için **yukarıdaki hücreleri Çalıştır** ' ı seçin. Sıradaki geçerli olan tüm hücreleri çalıştırmak için **aşağıdaki hücreleri Çalıştır** ' ı seçin.

   ![çalışma--veya-aşağı hücreleri](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

**Tümünü Çalıştır** düğmesini seçerek açılan listeyi genişletin, sonra geçerli sırada bulunan tüm hücreleri çalıştırmak için **yukarıdaki hücreleri Çalıştır** ' ı seçin. Sıradaki geçerli olan tüm hücreleri çalıştırmak için **aşağıdaki hücreleri Çalıştır** ' ı seçin.

   ![Azure-Not defteri-çalışma--veya-aşağı hücreleri](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Tüm çalışan hücreleri iptal et

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)
Çalışan hücreleri veya sırada bekleyen hücreleri iptal etmek için **Tümünü Iptal et** düğmesini seçin. 
   ![iptal-tümünü-hücreler](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Çalışan hücreleri veya sırada bekleyen hücreleri iptal etmek için **Tümünü Iptal et** düğmesini seçin. 
   ![Azure-Not defteri-iptal-tümünü-hücreler](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Not defteri başvurusu

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Desteklenmez.

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

```%run <notebook path>```Geçerli not defteri 'nin bağlamı içindeki başka bir not defterine başvurmak için Magic komutunu kullanabilirsiniz. Başvuru not defterinde tanımlanan tüm değişkenler geçerli not defterinde kullanılabilir. ```%run``` Magic komutu, iç içe çağrıları destekler ancak özyinelemeli çağrıları desteklemez. Deyimin derinliği beşten fazlaysa bir özel durum alırsınız. ```%run``` komut şu anda yalnızca bir not defteri yolunu parametre olarak geçirmek desteklemektedir. 

Örnek: ``` %run /path/notebookA ```.

> [!NOTE]
> SYNAPSE ardışık düzeninde Not defteri başvurusu desteklenmez.
>
>

---

### <a name="variable-explorer"></a>Değişken Gezgini

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Desteklenmez.

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

SYNAPSE Not defteri, PySpark (Python) hücreleri için geçerli Spark oturumunda değişken adı, tür, uzunluk ve değerin listesini görmeniz için yerleşik bir değişkenler Gezgini sağlar. Kod hücrelerinde tanımlandıklarında, daha fazla değişken otomatik olarak gösterilir. Her bir sütun başlığına tıkladığınızda tablodaki değişkenler sıralanır.

Değişken Gezginini açmak veya gizlemek için Not defteri komut çubuğundaki **değişkenler** düğmesini seçebilirsiniz.

![Azure-Not defteri-değişken-gezgin](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-variable-explorer.png)


---

### <a name="cell-status-indicator"></a>Hücre durumu göstergesi

Bir adım adım hücre yürütme durumu, geçerli ilerlemesini görmenizi sağlayacak şekilde hücrenin altında görüntülenir. Hücre çalıştırma işlemi tamamlandıktan sonra, toplam süre ve bitiş saatine sahip bir yürütme özeti gösterilir ve gelecekte başvuru için orada tutulur.

![hücre-durum](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark ilerleme göstergesi

Azure SYNAPSE Studio Not defteri yalnızca Spark tabanlıdır. Kod hücreleri, sunucusuz Apache Spark havuzunda uzaktan yürütülür. İş yürütme durumunu anlamanıza yardımcı olmak üzere gerçek zamanlı ilerleme çubuğu içeren bir Spark işi ilerleme göstergesi görünür.
Her iş veya aşama başına görev sayısı, Spark işinizin paralel düzeyini belirlemenize yardımcı olur. Ayrıca, iş (veya aşama) adındaki bağlantıyı seçerek belirli bir işin Spark Kullanıcı arabirimine (veya aşamasına) ayrıntılı olarak gidebilirsiniz.


![Spark-ilerleme-gösterge](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark oturum yapılandırması

**Yapılandırma oturumunda** geçerli Spark oturumuna verilecek yürüticilere zaman aşımı süresini, sayısını ve bunların boyutunu belirtebilirsiniz. Spark oturumunu yeniden başlatmak, yapılandırma değişikliklerinin etkili olması içindir. Tüm önbelleğe alınmış Not defteri değişkenleri temizlenir.

[![oturum yönetimi](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Spark oturum config Magic komutu
Ayrıca, bir Magic komutu **%% yapılandırması** aracılığıyla Spark oturum ayarlarını belirtebilirsiniz. Spark oturumunun, ayarların etkili olması için yeniden başlatılması gerekir. **%% Yapılandırmasını** not defterinizin başlangıcında çalıştırmanızı öneririz. Örnek olarak, https://github.com/cloudera/livy#request-body geçerli parametrelerin tam listesi için bkz. 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```
> [!NOTE]
> Spark oturum config Magic komutu, SYNAPSE ardışık düzeninde desteklenmez.
>
>

## <a name="bring-data-to-a-notebook"></a>Verileri bir not defterine getirme

Aşağıdaki kod örneklerinde gösterildiği gibi Azure Blob depolama, Azure Data Lake Store Gen 2 ve SQL havuzundaki verileri yükleyebilirsiniz.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Spark veri çerçevesi olarak Azure Data Lake Store Gen2 'ten CSV okuma

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob depolama 'dan bir CSV 'yi Spark veri çerçevesi olarak okuma

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Birincil depolama hesabından veri okuma

Birincil depolama hesabındaki verilere doğrudan erişebilirsiniz. Gizli anahtarları sağlamanız gerekmez. Veri Gezgini, bir dosyaya sağ tıklayıp **Yeni Not defteri** ' ni seçerek veri ayıklayıcısı otomatik olarak oluşturulur yeni bir not defteri görüntüleyin.

![verilerden hücrelere](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Not defterlerini kaydetme

Çalışma alanınızdaki tek bir not defterini veya tüm not defterlerini kaydedebilirsiniz.

1. Tek bir not defterine yaptığınız değişiklikleri kaydetmek için Not defteri komut çubuğundaki **Yayımla** düğmesini seçin.

   ![Yayımlama-Not defteri](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Çalışma alanınızdaki tüm not defterlerini kaydetmek için çalışma alanı komut çubuğunda **Tümünü Yayımla** düğmesini seçin. 

   ![tümünü Yayımla](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Not defteri özelliklerinde, kaydetme sırasında hücre çıkışının eklenip eklenmeyeceğini yapılandırabilirsiniz.

   ![Not defteri-Özellikler](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic komutları
Azure SYNAPSE Studio not defterlerinde tanıdık Jupyıter Magic komutlarını kullanabilirsiniz. Geçerli kullanılabilir sihirli komutları olarak aşağıdaki listeyi gözden geçirin. İhtiyaçlarınızı karşılamak için daha fazla sihirli komut oluşturmaya devam edebilmemiz için [kullanım durumlarınızı GitHub 'da](https://github.com/MicrosoftDocs/azure-docs/issues/new) bize söyleyin.

> [!NOTE]
> SYNAPSE ardışık düzeninde yalnızca şu sihirli komutlar desteklenir:%% pyspark,%% Spark,%% CSharp,%% SQL. 
>
>

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Kullanılabilir satır mıknatıcs: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Kullanılabilir hücre mıknatıcs: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture),%% [WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile),%% [SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages),%% [Spark](#use-multiple-languages), [%% CSharp](#use-multiple-languages),%%[Configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Kullanılabilir satır mıknatıcs: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [% History](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [% Run](#notebook-reference), [% Load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Kullanılabilir hücre mıknatıcs: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture),%% [WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile),%% [SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages),%% [Spark](#use-multiple-languages), [%% CSharp](#use-multiple-languages),%% [HTML](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html),%% [Configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Bir not defterini tümleştirme

### <a name="add-a-notebook-to-a-pipeline"></a>İşlem hattına Not defteri ekleme

Mevcut bir işlem hattına bir not defteri eklemek veya yeni bir işlem hattı oluşturmak için sağ üst köşedeki **ardışık düzene Ekle** düğmesini seçin.

![İşlem hattına Not defteri ekleme](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Bir parametre hücresi belirleme

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Not defterinizi parametreleştirin, en sağdaki diğer hücre eylemleri menüsüne erişmek için üç nokta (...) simgesini seçin. Ardından, parametreyi parametre hücresi olarak belirlemek için **parametre hücresini değiştirin** ' i seçin.

![iki durumlu parametre](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

Not defterinizi parametreleştirmek için, hücre araç çubuğundaki **diğer komutlara** erişmek üzere üç nokta (...) simgesini seçin. Ardından, parametreyi parametre hücresi olarak belirlemek için **parametre hücresini değiştirin** ' i seçin.

![Azure-Not defteri-değiştirme-parametre](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory, parametreler hücresini arar ve yürütme zamanında geçirilen parametreler için bu hücreyi varsayılanlar olarak değerlendirir. Yürütme altyapısı, varsayılan değerlerin üzerine yazmak için, Parameters hücresinin altına giriş parametreleriyle yeni bir hücre ekler. Bir parametre hücresi belirlenmediğinde, eklenen hücre, Not defterinin en üstüne eklenir.


### <a name="assign-parameters-values-from-a-pipeline"></a>İşlem hattından parametre değerleri atama

Parametrelere sahip bir not defteri oluşturduktan sonra, Azure SYNAPSE Not defteri etkinliğine sahip bir işlem hattından çalıştırabilirsiniz. Etkinliği işlem hattı Tuvalinize ekledikten sonra, **Ayarlar** sekmesindeki **temel parametreler** bölümünde parametre değerlerini ayarlayabilirsiniz. 

![Parametre atama](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Parametre değerlerini atarken, işlem [hattı ifade dilini](../../data-factory/control-flow-expression-language-functions.md) veya [Sistem değişkenlerini](../../data-factory/control-flow-system-variables.md)kullanabilirsiniz.



## <a name="shortcut-keys"></a>Kısayol tuşları

Jupyıter not defterlerine benzer şekilde, Azure SYNAPSE Studio Not defterleri de kalıcı bir kullanıcı arabirimine sahiptir. Klavye, Not defteri hücresinin bulunduğu moda bağlı olarak farklı şeyler yapar. SYNAPSE Studio Not defterleri, belirli bir kod hücresi için aşağıdaki iki modu destekler: komut modu ve düzenleme modu.

1. Bir hücre, yazmanızı isteyen bir metin imleci olmadığında komut modundadır. Bir hücre komut modundayken, Not defterini tek tek hücrelere değil, bir bütün olarak düzenleyebilirsiniz. `ESC`Bir hücrenin düzenleyici alanının dışında seçim yapmak için fare tuşuna basarak veya fareyi kullanarak komut moduna girin.

   ![komut modu](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. Düzenleme modu, düzenleyici alanına yazmanızı isteyen bir metin imlece belirtilir. Bir hücre düzenleme modundayken, hücreye yazabilirsiniz. `Enter`Fareyle sürükleyerek veya bir hücrenin düzenleyici alanında seçim yaparak düzenleme modunu girin.
   
   ![düzenleme modu](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Komut modu altında kısayol tuşları

# <a name="classical-notebook"></a>[Klasik Not defteri](#tab/classical)

Aşağıdaki tuş vuruşu kısayollarını kullanarak Azure SYNAPSE Not defterlerindeki kodu daha kolay bir şekilde gezinebilir ve çalıştırabilirsiniz.

| Eylem |SYNAPSE Studio Not defteri kısayolları  |
|--|--|
|Geçerli hücreyi çalıştırın ve aşağıdan seçin | Shift+Enter |
|Geçerli hücreyi çalıştırın ve Alta ekleyin | Alt+Enter |
|Üstteki hücreyi seçin| Yukarı |
|Aşağıdan hücre seçin| Aşağı |
|Yukarıya hücre ekle| A |
|Aşağıya hücre ekle| B |
|Üstteki seçili hücreleri Genişlet| SHIFT + yukarı |
|Seçili hücreleri aşağıdan Genişlet| SHIFT + aşağı|
|Hücreyi yukarı taşı| Ctrl + alt + ↑ |
|Hücreyi aşağı taşı| Ctrl + alt + ↓ |
|Seçili hücreleri sil| D, D |
|Düzenleme moduna geç| Enter |

# <a name="preview-notebook"></a>[İnceleme Not defteri](#tab/preview)

| Eylem |SYNAPSE Studio Not defteri kısayolları  |
|--|--|
|Geçerli hücreyi çalıştırın ve aşağıdan seçin | Shift+Enter |
|Geçerli hücreyi çalıştırın ve Alta ekleyin | Alt+Enter |
|Geçerli hücreyi Çalıştır| Ctrl+Enter |
|Üstteki hücreyi seçin| Yukarı |
|Aşağıdan hücre seçin| Aşağı |
|Önceki hücreyi Seç| K |
|Sonraki hücreyi Seç| J |
|Yukarıya hücre ekle| A |
|Aşağıya hücre ekle| B |
|Seçili hücreleri sil| SHIFT + D |
|Düzenleme moduna geç| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>Düzenleme modundaki kısayol tuşları


Aşağıdaki tuş vuruşu kısayollarını kullanarak, düzenleme modundayken Azure SYNAPSE Not defterlerindeki kodu daha kolay bir şekilde açabilir ve çalıştırabilirsiniz.

| Eylem |SYNAPSE Studio Not defteri kısayolları  |
|--|--|
|İmleci yukarı taşı | Yukarı |
|İmleci aşağı taşı|Aşağı|
|Geri Al|CTRL + Z|
|Yinele|CTRL + Y|
|Açıklama/açıklama kaldır|CTRL +/|
|Önceki kelimeyi Sil|CTRL + geri al|
|Sonra sözcüğü Sil|Ctrl + Delete|
|Hücre başlangıcına git|Ctrl + Home|
|Hücre sonuna git |CTRL + END|
|Bir kelimeyi sola git|Ctrl + sol|
|Bir sözcüğe sağ git|CTRL + sağ|
|Tümünü seç|CTRL + A|
|Leyebilirsiniz| CTRL +]|
|Girintiyi geri al|CTRL + [|
|Komut moduna geç| Esc |

---

## <a name="next-steps"></a>Sonraki adımlar
- [SYNAPSE örnek not defterlerine göz atın](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Hızlı başlangıç: Web araçlarını kullanarak Azure SYNAPSE Analytics 'te Apache Spark havuzu oluşturma](../quickstart-apache-spark-notebook.md)
- [Azure SYNAPSE Analytics 'te Apache Spark nedir?](apache-spark-overview.md)
- [Azure Synapse Analytics ile Apache Spark için .NET’i kullanma](spark-dotnet.md)
- [Apache Spark belgeleri için .NET](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)