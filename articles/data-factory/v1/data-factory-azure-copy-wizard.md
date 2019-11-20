---
title: Azure kopyalama Sihirbazı Data Factory
description: Desteklenen veri kaynaklarından verileri havuza kopyalamak için Data Factory Azure kopyalama Sihirbazı 'nı kullanma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b2ec8e2cc09b6deb236bd83cb5cca99fead97279
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683225"
---
# <a name="azure-data-factory-copy-wizard"></a>Azure Data Factory kopyalama Sihirbazı
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. 

Azure Data Factory kopyalama Sihirbazı, verileri almak için bir uçtan uca veri tümleştirme senaryosunda ilk bir adımdır. Azure Data Factory kopyalama Sihirbazı ' nı kullanırken, bağlı hizmetler, veri kümeleri ve işlem hatları için herhangi bir JSON tanımını anlamanız gerekmez. Sihirbaz, verileri seçilen veri kaynağından seçilen hedefe kopyalamak için otomatik olarak bir işlem hattı oluşturur. Ayrıca, kopyalama Sihirbazı, yazma sırasında alınan verileri doğrulamanıza yardımcı olur. Bu, özellikle veri kaynağından ilk kez veri alırken zaman kazandırır. Kopyalama Sihirbazı 'nı başlatmak için veri fabrikanızın giriş sayfasındaki **veri Kopyala** kutucuğuna tıklayın.

![Kopyalama Sihirbazı](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Büyük veriler için tasarlandı
Bu sihirbaz, çok çeşitli kaynaklardan gelen verileri dakikalar içinde kolayca hedeflere taşımanızı sağlar. Sihirbaza geçtikten sonra, sizin için bir kopyalama etkinliğine sahip bir işlem hattı, bağımlı Data Factory varlıkları (bağlı hizmetler ve veri kümeleri) ile birlikte sizin için otomatik olarak oluşturulur. İşlem hattını oluşturmak için ek adım gerekmez.   

![Veri kaynağı seçme](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Verileri bir Azure blobundan Azure SQL veritabanı tablosuna kopyalamak üzere örnek bir işlem hattı oluşturmaya yönelik adım adım yönergeler için, [Kopyalama Sihirbazı öğreticisine](data-factory-copy-data-wizard-tutorial.md)bakın.
>
>

Sihirbaz, farklı veri ve nesne türleri desteğiyle, başlangıçtan itibaren göz önünde bulundurularak büyük verilerle tasarlanmıştır. Yüzlerce klasör, dosya veya tablo taşıyarak Data Factory işlem hatlarını yazabilirsiniz. Sihirbaz otomatik veri önizlemeyi, şema yakalamayı ve eşlemeyi ve veri filtrelemeyi destekler.

## <a name="automatic-data-preview"></a>Otomatik veri önizleme
Verilerin kopyalamak istediğiniz gibi olup olmadığını doğrulamak için seçili veri kaynağından verilerin bir bölümünü önizleyebilirsiniz. Ayrıca, kaynak verileri bir metin dosyası ise kopyalama Sihirbazı, satır ve sütun sınırlayıcılarını ve şemayı otomatik olarak öğrenmek için metin dosyasını ayrıştırır.

![Dosya biçimi ayarları](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Şema yakalama ve eşleme
Giriş verilerinin şeması, bazı durumlarda çıkış verileri şemasıyla eşleşmeyebilir. Bu senaryoda, kaynak şemadan sütunları hedef şemadaki sütunlara eşlemeniz gerekir.

> [!TIP]
> SQL Server veya Azure SQL veritabanından Azure SQL veri ambarı 'na veri kopyalarken, tablo hedef depoda yoksa, kaynak şemasını kullanarak otomatik tablo oluşturmayı destekler Data Factory. [Azure Data Factory kullanarak Azure SQL veri ambarı 'na veri taşıma](./data-factory-azure-sql-data-warehouse-connector.md)hakkında daha fazla bilgi edinin.
>

Hedef şemadaki bir sütunla eşlenecek kaynak şemadan bir sütun seçmek için açılan listeyi kullanın. Kopyalama Sihirbazı, sütun eşleme örüntüsünün anlaşılmasına çalışır. Bu, sütunların geri kalanına aynı düzeni uygular, böylece şema eşlemesini tamamlamaya yönelik her bir sütunu tek tek seçmeniz gerekmez. İsterseniz, sütunları tek tek eşlemek için açılan listeleri kullanarak bu eşlemeleri geçersiz kılabilirsiniz. Daha fazla sütun eşleştirirken, bu kalıp daha doğru hale gelir. Kopyalama Sihirbazı, her bir düzende güncellenir ve sonunda elde etmek istediğiniz sütun eşlemesi için doğru düzene ulaşır.     

![Şema eşleme](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Verileri filtreleme
Yalnızca havuz veri deposuna kopyalanması gereken verileri seçmek için kaynak verilerini filtreleyebilirsiniz. Filtreleme, havuz veri deposuna kopyalanacak verilerin hacmini azaltır ve bu nedenle kopyalama işleminin aktarım hızını geliştirir. Bir ilişkisel veritabanındaki verileri, SQL sorgu dilini veya [Data Factory işlevleri ve değişkenleri](data-factory-functions-variables.md)kullanarak bir Azure Blob klasöründeki dosyaları kullanarak filtrelemek için esnek bir yol sağlar.   

### <a name="filtering-of-data-in-a-database"></a>Veritabanındaki verilerin filtrelenmesi
Aşağıdaki ekran görüntüsünde `Text.Format` işlevi ve `WindowStart` değişkeni kullanılarak bir SQL sorgusu gösterilmektedir.

![İfadeleri doğrula](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure Blob klasöründeki verilerin filtrelenmesi
Çalışma zamanında belirlenen klasörden verileri [sistem değişkenlerine](data-factory-functions-variables.md#data-factory-system-variables)göre kopyalamak için klasör yolundaki değişkenleri kullanabilirsiniz. Desteklenen değişkenler şunlardır: **{Year}** , **{Month}** , **{Day}** , **{Hour}** , **{Minute}** ve **{Custom}** . Örneğin: ınputfolder/{year}/{month}/{Day}.

Giriş klasörlerinizi aşağıdaki biçimde kullandığınızı varsayalım:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

**Dosya veya klasör**için **Araştır** düğmesine tıklayın, bu klasörlerden birine gidin (örneğin, 2016-> 03-> 01-> 02) ve **Seç**' e tıklayın. Metin kutusunda `2016/03/01/02` görmeniz gerekir. Şimdi, **2016** öğesini { **Year}** , **03** ile { **Month**}, **01** ile { **Day**} ve **02** ile **{Hour}** ile değiştirin ve **sekme** tuşuna basın. Bu dört değişkenin biçimini seçmek için açılan listeleri görmeniz gerekir:

![Sistem değişkenlerini kullanma](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Aşağıdaki ekran görüntüsünde gösterildiği gibi, **özel** bir değişken ve [desteklenen biçim dizelerini](https://msdn.microsoft.com/library/8kb3ddd4.aspx)de kullanabilirsiniz. Bu yapıya sahip bir klasör seçmek için, önce **Araştır** düğmesini kullanın. Sonra bir değeri **{Custom}** ile değiştirin ve biçim dizesini girebileceğiniz metin kutusunu görmek için **Tab** tuşuna basın.     

![Özel değişken kullanma](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Zamanlama seçenekleri
Kopyalama işlemini bir kez veya bir zamanlamaya göre (saatlik, günlük, vb.) çalıştırabilirsiniz. Bu seçeneklerin her ikisi de şirket içi, bulut ve yerel masaüstü kopyası dahil olmak üzere ortamlarda bağlayıcılar genelinde kullanılabilir.

Tek seferlik kopyalama işlemi, veri hareketini bir kaynaktan hedefe yalnızca bir kez sağlar. Her boyuttaki ve desteklenen biçimdeki veriler için geçerlidir. Zamanlanan kopya, verileri önceden belirlenmiş bir tekrarda kopyalamanızı sağlar. Zamanlanmış kopyayı yapılandırmak için zengin ayarları (yeniden deneme, zaman aşımı ve uyarılar gibi) kullanabilirsiniz.

![Zamanlama özellikleri](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Sonraki adımlar
Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için Data Factory kopyalama Sihirbazı 'Nı kullanmaya yönelik hızlı bir anlatım için bkz. [öğretici: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md).
