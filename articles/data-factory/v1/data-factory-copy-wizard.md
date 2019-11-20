---
title: Kopyalama Sihirbazı ile verileri kolayca kopyalama-Azure
description: Desteklenen veri kaynaklarından verileri havuza kopyalamak için Data Factory kopyalama Sihirbazı 'nı kullanma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 88e5603405ad5594b06674aaf6cbb2c4d23b754c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682780"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Azure Data Factory kopyalama sihirbazıyla verileri kolayca kopyalama veya taşıma
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-dot-net.md). 


Azure Data Factory kopyalama Sihirbazı, verileri içe aktarmak için bir uçtan uca veri tümleştirme senaryosunda ilk bir adımdır. Azure Data Factory kopyalama Sihirbazı ' nı kullanırken, bağlı hizmetler, veri kümeleri ve işlem hatları için herhangi bir JSON tanımını anlamanız gerekmez. Ancak, sihirbazdaki tüm adımları tamamladıktan sonra sihirbaz, verileri seçilen veri kaynağından seçilen hedefe kopyalamak için otomatik olarak bir işlem hattı oluşturur. Ayrıca, kopyalama Sihirbazı, özellikle veri kaynağından ilk kez veri aldığınızda, yazma sırasında alınan verileri doğrulamanızı sağlar ve bu da zamandan çoğunu kaydeder. Kopyalama Sihirbazı 'nı başlatmak için veri fabrikanızın giriş sayfasındaki **veri Kopyala** kutucuğuna tıklayın.

![Kopyalama Sihirbazı](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Verileri kopyalamak için sezgisel bir sihirbaz
Bu sihirbaz, çok çeşitli kaynaklardan gelen verileri dakikalar içinde kolayca hedeflere taşımanızı sağlar. Sihirbazla çalışmaya başladıktan sonra, bağımlı Data Factory varlıkları (bağlı hizmetler ve veri kümeleri) ile birlikte, kopyalama etkinliği içeren bir işlem hattı sizin için otomatik olarak oluşturulur. İşlem hattını oluşturmak için ek adım gerekmez.   

![Veri kaynağı seçme](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Verileri bir Azure blobundan Azure SQL veritabanı tablosuna kopyalamak için örnek bir işlem hattı oluşturmak üzere adım adım yönergeler için bkz. [Kopyalama Sihirbazı öğreticisi](data-factory-copy-data-wizard-tutorial.md) makalesi. 
> 
> 

Sihirbaz, başlangıçtan itibaren göz önünde bulundurmanız gereken büyük verilerle tasarlanmıştır. Veri Kopyalama sihirbazını kullanarak yüzlerce klasör, dosya veya tablo taşıyarak Data Factory işlem hatlarını yazmak basit ve verimlidir. Sihirbaz, aşağıdaki üç özelliği destekler: otomatik veri önizleme, şema yakalama ve eşleme ve verileri filtreleme. 

## <a name="automatic-data-preview"></a>Otomatik veri önizleme
Kopyalama Sihirbazı, verilerin kopyalamak istediğiniz doğru veri olup olmadığını doğrulamanız için seçili veri kaynağından verilerin bir kısmını incelemenizi sağlar. Ayrıca, kaynak verileri bir metin dosyası ise kopyalama Sihirbazı, satır ve sütun sınırlayıcılarını ve şemayı otomatik olarak öğrenmek için metin dosyasını ayrıştırır. 

![Dosya biçimi ayarları](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Şema yakalama ve eşleme
Giriş verilerinin şeması, bazı durumlarda çıkış verileri şemasıyla eşleşmeyebilir. Bu senaryoda, kaynak şemadan sütunları hedef şemadaki sütunlara eşlemeniz gerekir. 

Kopyalama Sihirbazı, kaynak şemadaki sütunları hedef şemadaki sütunlara otomatik olarak eşler. Açılan listeleri kullanarak eşlemeleri geçersiz kılabilirsiniz (veya) verileri kopyalarken bir sütunun Atlanmasının gerekip gerekmediğini belirtebilirsiniz.   

![Şema eşleme](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Verileri filtreleme
Sihirbaz, kaynak verileri filtrelemenizi ve yalnızca hedef/havuz veri deposuna kopyalanması gereken verileri seçmenizi sağlar. Filtreleme, havuz veri deposuna kopyalanacak verilerin hacmini azaltır ve bu nedenle kopyalama işleminin aktarım hızını geliştirir. [Data Factory işlevleri ve değişkenleri](data-factory-functions-variables.md)kullanarak bir Azure Blob klasöründeki SQL sorgu dili (veya) dosyalarını kullanarak ilişkisel veritabanındaki verileri filtrelemek için esnek bir yol sağlar.   

### <a name="filtering-of-data-in-a-database"></a>Veritabanındaki verilerin filtrelenmesi
Örnekte, SQL sorgusu `Text.Format` işlevini ve `WindowStart` değişkenini kullanır. 

![İfadeleri doğrula](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure Blob klasöründeki verilerin filtrelenmesi
Çalışma zamanında belirlenen klasörden verileri [sistem değişkenlerine](data-factory-functions-variables.md#data-factory-system-variables)göre kopyalamak için klasör yolundaki değişkenleri kullanabilirsiniz. Desteklenen değişkenler şunlardır: **{Year}** , **{Month}** , **{Day}** , **{Hour}** , **{Minute}** ve **{Custom}** . Örnek: ınputfolder/{year}/{month}/{Day}.

Giriş klasörlerinizi aşağıdaki biçimde kullandığınızı varsayalım:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

**Dosya veya klasör**için **Araştır** düğmesine tıklayın, bu klasörlerden birine gidin (örneğin, 2016-> 03-> 01-> 02) ve **Seç**' e tıklayın. Metin kutusunda `2016/03/01/02` görmeniz gerekir. Şimdi, **2016** öğesini { **Year}** , **03** ile { **Month**}, **01** ile { **Day**} ve **02** ile **{Hour}** ile değiştirin ve SEKME tuşuna basın. Bu dört değişkenin biçimini seçmek için açılan listeleri görmeniz gerekir:

![Sistem değişkenlerini kullanma](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Aşağıdaki ekran görüntüsünde gösterildiği gibi, **özel** bir değişken ve [desteklenen biçim dizelerini](https://msdn.microsoft.com/library/8kb3ddd4.aspx)de kullanabilirsiniz. Bu yapıya sahip bir klasör seçmek için, önce **Araştır** düğmesini kullanın. Sonra bir değeri **{Custom}** ile değiştirin ve biçim dizesini girebileceğiniz metin kutusunu görmek için Tab tuşuna basın.     

![Özel değişken kullanma](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Farklı veri ve nesne türleri için destek
Kopyalama Sihirbazı 'nı kullanarak yüzlerce klasör, dosya veya tabloyu etkin bir şekilde taşıyabilirsiniz.

![Verilerin kopyalanacağı tabloları seçin](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Zamanlama seçenekleri
Kopyalama işlemini bir kez veya bir zamanlamaya göre (saatlik, günlük, vb.) çalıştırabilirsiniz. Bu seçeneklerin her ikisi de şirket içi, bulut ve yerel masaüstü kopyası arasındaki bağlayıcıların sınırları için kullanılabilir.

Tek seferlik kopyalama işlemi, veri hareketini bir kaynaktan hedefe yalnızca bir kez sağlar. Her boyuttaki ve desteklenen biçimdeki veriler için geçerlidir. Zamanlanan kopya, verileri önceden belirlenmiş bir tekrarda kopyalamanızı sağlar. Zamanlanmış kopyayı yapılandırmak için zengin ayarları (yeniden deneme, zaman aşımı ve uyarılar gibi) kullanabilirsiniz.

![Zamanlama özellikleri](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Sonraki adımlar
Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için Data Factory kopyalama Sihirbazı 'Nı kullanmaya yönelik hızlı bir anlatım için bkz. [öğretici: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md).

