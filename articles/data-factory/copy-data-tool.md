---
title: Veri Kopyalama araç Azure Data Factory
description: Azure Data Factory Kullanıcı arabirimindeki Veri Kopyalama aracı hakkında bilgi sağlar
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: b5c93213199cac399487f949a3a8cb04c2d9addb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678328"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Veri Kopyalama araç Azure Data Factory
Azure Data Factory Veri Kopyalama araç, verileri bir veri Gölü içine almak ve bu işlemi, genellikle uçtan uca bir veri tümleştirme senaryosunda ilk adımdan en iyi duruma getirir.  Özellikle bir veri kaynağından ilk kez veri almak için Azure Data Factory kullandığınızda zaman kazandırır. Bu aracı kullanmanın avantajlarından bazıları şunlardır:

- Azure Data Factory Veri Kopyalama aracını kullanırken, bağlı hizmetler, veri kümeleri, işlem hatları, etkinlikler ve Tetikleyiciler için Data Factory tanımları anlamanız gerekmez. 
- Veri Kopyalama aracın akışı, verileri bir Data Lake 'e yüklemek için sezgisel hale sahiptir. Araç, seçili kaynak veri deposundan verileri seçilen hedef/havuz veri deposuna kopyalamak için tüm gerekli Data Factory kaynaklarını otomatik olarak oluşturur. 
- Veri Kopyalama Aracı, yazma sırasında gerçekleştirilen verileri doğrulamanıza yardımcı olur. Bu, başındaki olası hatalardan kaçınmanıza yardımcı olur.
- Verileri bir veri Gölü içine yüklemek için karmaşık iş mantığı uygulamanız gerekiyorsa, Data Factory Kullanıcı arabirimindeki etkinlik başına yazmayı kullanarak Veri Kopyalama aracı tarafından oluşturulan Data Factory kaynaklarını düzenlemeye devam edebilirsiniz. 

Aşağıdaki tabloda, Data Factory Kullanıcı arabirimindeki Veri Kopyalama aracı ve etkinlik başına yazmanın ne zaman kullanılacağı hakkında rehberlik verilmektedir: 

| Veri Kopyalama aracı | Etkinlik başına (kopyalama etkinliği) yazma |
| -------------- | -------------------------------------- |
| Azure Data Factory varlıkları (bağlı hizmetler, veri kümeleri, işlem hatları, vb.) hakkında bilgi edinmeden kolayca bir veri yükleme görevi oluşturmak istiyorsunuz. | Gölü verileri yüklemek için karmaşık ve esnek mantık uygulamak istiyorsunuz. |
| Bir veri Gölü içine çok sayıda veri yapıtları hızlıca yüklemek istiyorsunuz. | Kopyalama etkinliğini, verileri temizleme veya işleme için sonraki etkinliklerle zincirlemek istiyorsunuz. |

Veri Kopyalama aracı 'nı başlatmak için veri fabrikanızın giriş sayfasındaki **veri kopyalama** kutucuğuna tıklayın.

![Kullanmaya başlama sayfası-Veri Kopyalama araca bağlantı](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Data Lake 'e veri yüklemek için sezgisel akış
Bu araç, çok çeşitli kaynaklardan gelen verileri, sezgisel bir akışa göre dakikalar içinde hedefe kolayca taşımanızı sağlar:  

1. **Kaynak**için ayarları yapılandırın.
2. **Hedef**için ayarları yapılandırın. 
3. Kopyalama işlemi için, sütun eşleme, performans ayarları ve hata toleransı ayarları gibi **Gelişmiş ayarları** yapılandırın. 
4. Veri yükleme görevi için bir **zamanlama** belirtin. 
5. Oluşturulacak Data Factory varlıkların **özetini** gözden geçirin. 
6. Kopyalama etkinliğinin ayarlarını gerektiği şekilde güncelleştirmek için işlem hattını **düzenleyin** . 

   Araç, farklı veri ve nesne türleri desteğiyle başlangıçtan itibaren göz önünde bulundurularak büyük verilerle tasarlanmıştır. Yüzlerce klasör, dosya veya tablo taşımak için kullanabilirsiniz. Araç otomatik veri önizlemeyi, şema yakalamayı ve otomatik eşlemeyi ve veri filtrelemeyi de destekler.

![Veri Kopyalama aracı](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Otomatik veri önizleme
Verilerin bir kısmını seçili kaynak veri deposundan önizleyebilirsiniz, bu da Kopyalanmakta olan verileri doğrulamanızı sağlar. Ayrıca, kaynak veriler bir metin dosyası ise, Veri Kopyalama aracı metin dosyasını, satır ve sütun sınırlayıcılarını ve şemayı otomatik olarak algılayacak şekilde ayrıştırır.

![Dosya ayarları](./media/copy-data-tool/file-format-settings.png)

Algılandıktan sonra:

![Algılanan dosya ayarları ve Önizleme](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Şema yakalama ve otomatik eşleme
Veri kaynağının şeması birçok durumda veri hedefi şeması ile aynı olamaz. Bu senaryoda, kaynak şemadan sütunları hedef şemadaki sütunlara eşlemeniz gerekir.

Veri Kopyalama Aracı, kaynak ve hedef mağazalar arasında sütunları eşlerken davranışınızı izler ve öğrenir. Kaynak veri deposundan bir veya birkaç sütun belirledikten ve bunları hedef şemayla eşleştirdikten sonra, Veri Kopyalama aracı her iki taraftan de seçtiğiniz sütun çiftleri için olan düzeni çözümlemeye başlar. Daha sonra, sütunların geri kalanına aynı kalıbı uygular. Bu nedenle, birkaç tıklamayla hemen sonra istediğiniz şekilde, tüm sütunların hedefle eşleştirilmiş olduğunu görürsünüz.  Veri Kopyalama aracı tarafından sunulan sütun eşleme seçiminden memnun değilseniz, yoksayabilirsiniz ve sütunları el ile eşlemek için devam edebilirsiniz. Bu arada, Veri Kopyalama Aracı, kalıbı sürekli öğrenir ve güncelleştirir ve sonunda elde etmek istediğiniz sütun eşlemesi için doğru düzene ulaşır. 

> [!NOTE]
> SQL Server veya Azure SQL veritabanından Azure SQL veri ambarı 'na veri kopyalarken, tablo hedef depoda yoksa, Veri Kopyalama araç, kaynak şemasını kullanarak tablonun otomatik olarak oluşturulmasını destekler. 

## <a name="filter-data"></a>Verileri Filtrele
Yalnızca havuz veri deposuna kopyalanması gereken verileri seçmek için kaynak verilerini filtreleyebilirsiniz. Filtreleme, havuz veri deposuna kopyalanacak verilerin hacmini azaltır ve bu nedenle kopyalama işleminin aktarım hızını geliştirir. Veri Kopyalama araç, bir ilişkisel veritabanındaki verileri SQL sorgu dilini veya bir Azure Blob klasöründeki dosyaları kullanarak filtrelemek için esnek bir yol sağlar. 

### <a name="filter-data-in-a-database"></a>Veritabanındaki verileri filtreleme
Aşağıdaki ekran görüntüsünde, verileri filtrelemek için bir SQL sorgusu gösterilmektedir.

![Veritabanındaki verileri filtreleme](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Azure Blob klasöründeki verileri filtreleme
Klasör yolundaki değişkenleri bir klasörden veri kopyalamak için kullanabilirsiniz. Desteklenen değişkenler şunlardır: **{Year}** , **{Month}** , **{Day}** , **{Hour}** ve **{Minute}** . Örneğin: ınputfolder/{year}/{month}/{Day}. 

Giriş klasörlerinizi aşağıdaki biçimde kullandığınızı varsayalım: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

**Dosya veya klasör**için **Araştır** düğmesine tıklayın, bu klasörlerden birine gidin (örneğin, 2016-> 03-> 01-> 02) ve **Seç**' e tıklayın. Metin kutusunda 2016/03/01/02 ' i görmeniz gerekir. 

Ardından, **2016** öğesini **{Year}** , **03** ile { **Month**}, { **Day**} ile **01** ve **{Hour}** ile **02** ile değiştirin ve **sekme** tuşuna basın. Bu dört değişkenin biçimini seçmek için açılan listeleri görmeniz gerekir:

![Dosya veya klasörü filtrele](./media/copy-data-tool/filter-file-or-folder.png)

Veri Kopyalama Aracı, işlem hattı oluştururken {Year}, {month}, {Day}, {Hour} ve {Minute} göstermek için kullanılabilecek ifadeler, işlevler ve sistem değişkenleri içeren parametreler oluşturur.

## <a name="scheduling-options"></a>Zamanlama seçenekleri
Kopyalama işlemini bir kez veya bir zamanlamaya göre (saatlik, günlük, vb.) çalıştırabilirsiniz. Bu seçenekler, şirket içi, bulut ve Yerel Masaüstü gibi farklı ortamlarda bağlayıcılar için kullanılabilir. 

Tek seferlik kopyalama işlemi, veri hareketini bir kaynaktan hedefe yalnızca bir kez sağlar. Her boyuttaki ve desteklenen biçimdeki veriler için geçerlidir. Zamanlanan kopya, belirttiğiniz bir tekrarda verileri kopyalamanızı sağlar. Zamanlanmış kopyayı yapılandırmak için zengin ayarları (yeniden deneme, zaman aşımı ve uyarılar gibi) kullanabilirsiniz.

![Zamanlama seçenekleri](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Sonraki adımlar
Veri Kopyalama aracını kullanan Bu öğreticileri deneyin:

- [Hızlı başlangıç: Veri Kopyalama aracını kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-copy-data-tool.md)
- [Öğretici: Veri Kopyalama aracını kullanarak Azure 'da veri kopyalama](tutorial-copy-data-tool.md) 
- [Öğretici: Veri Kopyalama aracını kullanarak şirket içi verileri Azure 'a kopyalama](tutorial-hybrid-copy-data-tool.md)
