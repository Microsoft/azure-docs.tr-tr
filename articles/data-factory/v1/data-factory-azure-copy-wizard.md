---
title: Azure kopyalama Sihirbazı Data Factory
description: Desteklenen veri kaynaklarından verileri havuza kopyalamak için Data Factory Azure kopyalama Sihirbazı 'nı kullanma hakkında bilgi edinin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393386"
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

Sihirbaz, farklı veri ve nesne türleri desteğiyle, başlangıçtan itibaren göz önünde bulundurularak büyük verilerle tasarlanmıştır. Yüzlerce klasör, dosya veya tablo taşıyarak Data Factory işlem hatlarını yazabilirsiniz. Sihirbaz otomatik veri önizlemeyi, şema yakalamayı ve eşlemeyi ve veri filtrelemeyi destekler.

## <a name="automatic-data-preview"></a>Otomatik veri önizleme
Verilerin kopyalamak istediğiniz gibi olup olmadığını doğrulamak için seçili veri kaynağından verilerin bir bölümünü önizleyebilirsiniz. Ayrıca, kaynak verileri bir metin dosyası ise kopyalama Sihirbazı, satır ve sütun sınırlayıcılarını ve şemayı otomatik olarak öğrenmek için metin dosyasını ayrıştırır.

![Dosya biçimi ayarları](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Şema yakalama ve eşleme
Giriş verilerinin şeması, bazı durumlarda çıkış verileri şemasıyla eşleşmeyebilir. Bu senaryoda, kaynak şemadan sütunları hedef şemadaki sütunlara eşlemeniz gerekir.

> [!TIP]
> SQL Server veya Azure SQL veritabanındaki verileri Azure SYNAPSE Analytics 'e kopyalarken, tablo hedef depoda yoksa, kaynak şemasını kullanarak otomatik tablo oluşturmayı destekler Data Factory. [Azure Data Factory kullanarak Azure SYNAPSE Analytics 'e ve bu kaynaklardan veri taşıma](./data-factory-azure-sql-data-warehouse-connector.md)hakkında daha fazla bilgi edinin.

Hedef şemadaki bir sütunla eşlenecek kaynak şemadan bir sütun seçmek için açılan listeyi kullanın. Kopyalama Sihirbazı, sütun eşleme örüntüsünün anlaşılmasına çalışır. Bu, sütunların geri kalanına aynı düzeni uygular, böylece şema eşlemesini tamamlamaya yönelik her bir sütunu tek tek seçmeniz gerekmez. İsterseniz, sütunları tek tek eşlemek için açılan listeleri kullanarak bu eşlemeleri geçersiz kılabilirsiniz. Daha fazla sütun eşleştirirken, bu kalıp daha doğru hale gelir. Kopyalama Sihirbazı, her bir düzende güncellenir ve sonunda elde etmek istediğiniz sütun eşlemesi için doğru düzene ulaşır.     

![Şema eşleme](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Verileri filtreleme
Yalnızca havuz veri deposuna kopyalanması gereken verileri seçmek için kaynak verilerini filtreleyebilirsiniz. Filtreleme, havuz veri deposuna kopyalanacak verilerin hacmini azaltır ve bu nedenle kopyalama işleminin aktarım hızını geliştirir. Bir ilişkisel veritabanındaki verileri, SQL sorgu dilini veya [Data Factory işlevleri ve değişkenleri](data-factory-functions-variables.md)kullanarak bir Azure Blob klasöründeki dosyaları kullanarak filtrelemek için esnek bir yol sağlar.   

### <a name="filtering-of-data-in-a-database"></a>Veritabanındaki verilerin filtrelenmesi
Aşağıdaki ekran görüntüsünde, işlevi ve değişkenini kullanan bir SQL sorgusu gösterilmektedir `Text.Format` `WindowStart` .

![İfadeleri doğrula](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure Blob klasöründeki verilerin filtrelenmesi
Çalışma zamanında belirlenen klasörden verileri [sistem değişkenlerine](data-factory-functions-variables.md#data-factory-system-variables)göre kopyalamak için klasör yolundaki değişkenleri kullanabilirsiniz. Desteklenen değişkenler şunlardır: **{Year}**, **{Month}**, **{Day}**, **{Hour}**, **{Minute}** ve **{Custom}**. Örneğin: ınputfolder/{year}/{month}/{Day}.

Giriş klasörlerinizi aşağıdaki biçimde kullandığınızı varsayalım:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

**Dosya veya klasör** için **Araştır** düğmesine tıklayın, bu klasörlerden birine gidin (örneğin, 2016->03->01->02) ve **Seç**' e tıklayın. `2016/03/01/02`Metin kutusunda görmeniz gerekir. Şimdi, **2016** öğesini { **Year}**, **03** ile { **Month**}, **01** ile { **Day**} ve **02** ile **{Hour}** ile değiştirin ve **sekme** tuşuna basın. Bu dört değişkenin biçimini seçmek için açılan listeleri görmeniz gerekir:

![Sistem değişkenlerini kullanma](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Aşağıdaki ekran görüntüsünde gösterildiği gibi, **özel** bir değişken ve [desteklenen biçim dizelerini](/dotnet/standard/base-types/custom-date-and-time-format-strings)de kullanabilirsiniz. Bu yapıya sahip bir klasör seçmek için, önce **Araştır** düğmesini kullanın. Sonra bir değeri **{Custom}** ile değiştirin ve biçim dizesini girebileceğiniz metin kutusunu görmek için **Tab** tuşuna basın.     

![Özel değişken kullanma](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Zamanlama seçenekleri
Kopyalama işlemini bir kez veya bir zamanlamaya göre (saatlik, günlük, vb.) çalıştırabilirsiniz. Bu seçeneklerin her ikisi de şirket içi, bulut ve yerel masaüstü kopyası dahil olmak üzere ortamlarda bağlayıcılar genelinde kullanılabilir.

Tek seferlik kopyalama işlemi, veri hareketini bir kaynaktan hedefe yalnızca bir kez sağlar. Her boyuttaki ve desteklenen biçimdeki veriler için geçerlidir. Zamanlanan kopya, verileri önceden belirlenmiş bir tekrarda kopyalamanızı sağlar. Zamanlanmış kopyayı yapılandırmak için zengin ayarları (yeniden deneme, zaman aşımı ve uyarılar gibi) kullanabilirsiniz.

![Zamanlama özellikleri](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Sorun giderme

Bu bölüm Azure Data Factory içindeki kopyalama Sihirbazı için sık karşılaşılan sorun giderme yöntemlerini anlatıyor.

> [!NOTE] 
> Bu sorun giderme ipuçları, Data Factory sürüm 1 ' deki kopyalama Sihirbazı 'na uygulanır. Data Factory v2 için bkz. sorun giderme kılavuzu [Azure Data Factory](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Hata kodu: kopyalama sihirbazında doğrulanamadı

- **Belirtiler**: kopyalama sihirbazının ilk adımında, "doğrulanamadı" uyarı iletisiyle karşılaşırsınız.
- **Nedenler**: tüm üçüncü taraf tanımlama bilgileri devre dışı bırakıldığında bu durum oluşabilir.
- **Çözüm**: 
    - Internet Explorer veya Microsoft Edge tarayıcısı 'nı kullanın.
    - Chrome tarayıcısı kullanıyorsanız, *microsoftonline.com* ve *Windows.net* için tanımlama bilgisi özel durumu eklemek için aşağıdaki yönergeleri izleyin.
        1.  Chrome tarayıcısını açın.
        2.  Sağ tarafta bulunan wranya veya üç satıra tıklayın (Google Chrome 'u özelleştirin ve denetleyin).
        3.  **Ayarlar**'a tıklayın.
        4.  **Tanımlama bilgilerini** arayın veya Gelişmiş ayarlar altında **Gizlilik** 'e gidin.
        5.  **Içerik ayarları**' nı seçin.    
        6.  Tanımlama bilgileri, **Yerel verilerin ayarlanmasına izin verecek şekilde ayarlanmalıdır (önerilir)**.
        7.  **Özel durumları Yönet**' e tıklayın. **Ana bilgisayar adı deseninin** altında aşağıdakileri girin ve **izin ver** ' in davranış ayarlanmış olduğundan emin olun.
            - login.microsoftonline.com
            - login.windows.net
        8.  Tarayıcıyı kapatın ve yeniden başlatın.
    - Firefox tarayıcısı kullanıyorsanız, tanımlama bilgileri özel durumu eklemek için aşağıdaki yönergeleri izleyin.
        1. Firefox menüsünden **Araçlar**  >  **Seçenekler**' e gidin.
        2. **Gizlilik**  >  **geçmişi** altında geçerli ayarın **Geçmiş için özel ayarları kullanıp** görmeyebilirsiniz.
        3. **Üçüncü taraf tanımlama bilgilerini kabul et**' de, geçerli ayarınız **hiçbir** şekilde olmayabilir, ardından aşağıdaki siteleri eklemek için sağdaki **özel durumlar** ' a tıklamanız gerekir.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Tarayıcıyı kapatın ve yeniden başlatın. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Hata kodu: oturum açma sayfası açılamıyor ve parola gir

- **Belirtiler**: kopyalama Sihirbazı sizi oturum açma sayfasına yönlendirir, ancak oturum açma sayfası başarıyla gösterilmez.
- **Nedenler**: ağ ortamını ofis ağından ev ağına değiştirdiyseniz bu sorun oluşabilir. Tarayıcılarda bazı önbellekler vardır. 
- **Çözüm**: 
    1.  Tarayıcıyı kapatın ve yeniden deneyin. Sorun hala varsa sonraki adıma gidin.   
    2.  Internet Explorer tarayıcısı kullanıyorsanız, özel modda açmayı deneyin ("Ctrl" + "SHIFT" + "P" tuşlarına basın). Chrome tarayıcısı kullanıyorsanız, dosyayı ınbilito modunda açmayı deneyin ("Ctrl" + "SHIFT" + "N" tuşlarına basın). Sorun hala varsa sonraki adıma gidin. 
    3.  Başka bir tarayıcı kullanmayı deneyin. 


## <a name="next-steps"></a>Sonraki adımlar
Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için Data Factory kopyalama Sihirbazı 'Nı kullanmaya yönelik hızlı bir anlatım için bkz. [öğretici: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md).