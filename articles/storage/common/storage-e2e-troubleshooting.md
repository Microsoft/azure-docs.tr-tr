---
title: Tanılama & Ileti Çözümleyicisi ile Azure depolama sorunlarını giderme | Microsoft Docs
description: Azure Depolama Analizi, AzCopy ve Microsoft Message Analyzer ile uçtan uca sorun gidermeyi gösteren bir öğretici
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/15/2017
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2ca81280bed52508c606a5a693fe0162837ac117
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854621"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Azure depolama ölçümlerini ve günlüğe kaydetme, AzCopy ve Ileti Çözümleyicisi kullanarak uçtan uca sorun giderme
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Tanılama ve sorun giderme, Microsoft Azure Depolama ile istemci uygulamaları oluşturmaya ve desteklemeye yönelik bir temel yetenbedir. Bir Azure uygulamasının dağıtılmış doğası nedeniyle hata ve performans sorunlarını tanılamak ve sorunlarını gidermek geleneksel ortamlarından daha karmaşık olabilir.

Bu öğreticide, performansı etkileyebilecek belirli hataların nasıl belirlenmesini ve istemci uygulamasını iyileştirmek için Microsoft ve Azure Storage tarafından sunulan araçları kullanarak uçtan uca sorun gidermeyi gösteririz.

Bu öğreticide, uçtan uca sorun giderme senaryosuna yönelik uygulamalı bir araştırma sunulmaktadır. Azure depolama uygulamalarında sorun gidermeye yönelik ayrıntılı bir kavramsal kılavuz için bkz. [izleme, tanılama ve sorun giderme Microsoft Azure depolama](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Azure Storage uygulamalarında sorun gidermeye yönelik araçlar
Microsoft Azure Depolama kullanarak istemci uygulamalarında sorun gidermek için, bir sorunun ne zaman oluştuğunu ve sorunun nedenini belirlemek için bir araçlar bileşimini kullanabilirsiniz. Bu araçlar şunları içerir:

* **Azure depolama Analizi**. [Azure depolama Analizi](/rest/api/storageservices/Storage-Analytics) , Azure depolama için ölçüm ve günlüğe kaydetme sağlar.

  * **Depolama ölçümleri** , depolama hesabınız için işlem ölçümlerini ve kapasite ölçümlerini izler. Ölçümleri kullanarak, uygulamanızın çeşitli farklı ölçümlere göre nasıl çalıştığını belirleyebilirsiniz. Depolama Analizi tarafından izlenen ölçüm türleri hakkında daha fazla bilgi için bkz. [depolama Analizi ölçümleri tablo şeması](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) .
  * **Depolama günlüğü** her Isteği Azure Storage hizmetlerine bir sunucu tarafı günlüğüne kaydeder. Günlük, gerçekleştirilen işlem, işlemin durumu ve gecikme bilgileri de dahil olmak üzere her bir istek için ayrıntılı verileri izler. Günlüklere Depolama Analizi tarafından yazılan istek ve yanıt verileri hakkında daha fazla bilgi için bkz. [depolama Analizi günlük biçimi](/rest/api/storageservices/Storage-Analytics-Log-Format) .

* **Azure Portal**. [Azure Portal](https://portal.azure.com)depolama hesabınız için ölçümleri ve günlüğü yapılandırabilirsiniz. Ayrıca, uygulamanızın zaman içinde nasıl performans gösterdiğini gösteren grafikleri ve grafikleri görüntüleyebilir ve uygulamanız belirtilen bir ölçüm için beklenenden farklı bir şekilde gerçekleştirildiğinde sizi bilgilendirmek üzere uyarılar yapılandırabilirsiniz.

    Azure portal izlemeyi yapılandırma hakkında bilgi için bkz. [Azure Portal bir depolama hesabını izleme](storage-monitor-storage-account.md) .
* **AzCopy**. Azure depolama için sunucu günlükleri blob olarak depolanır. bu nedenle, Microsoft Message Analyzer 'ı kullanarak günlük bloblarını yerel bir dizine kopyalamak için AzCopy kullanabilirsiniz. AzCopy hakkında daha fazla bilgi için bkz. [AzCopy komut satırı yardımcı programı ile veri aktarma](storage-use-azcopy.md) .
* **Microsoft Message Analyzer**. İleti çözümleyici, günlük dosyalarını kullanan ve günlük verilerini, hataları ve performans sorunlarını analiz etmek için kullanabileceğiniz kullanışlı kümeler halinde filtrelemenizi, aramanızı ve gruplandırmayı kolaylaştıran bir görsel biçimde görüntüleyen bir araçtır. Ileti çözümleyici hakkında daha fazla bilgi için bkz. [Microsoft Message Analyzer Işletim kılavuzu](https://technet.microsoft.com/library/jj649776.aspx) .

## <a name="about-the-sample-scenario"></a>Örnek senaryo hakkında
Bu öğreticide, Azure depolama ölçümlerinin Azure Storage çağıran bir uygulama için yüzde düşük başarı oranını gösterdiği bir senaryoyu inceleyeceğiz. Yüzde düşük başarı oranı ölçümü ( [Azure Portal](https://portal.azure.com) ve ölçüm tablolarında **percentsuccess** olarak gösterilir), başarılı olan ancak 299 ' den büyük bir http durum kodu döndüren işlemleri izler. Sunucu tarafı depolama günlük dosyalarında, bu işlemler **Clienentothererrors**işlem durumuyla kaydedilir. Yüzde düşük başarı ölçümü hakkında daha fazla bilgi için bkz. [ölçümler düşük PercentSuccess veya Analytics günlük girişlerinde işlem durumu Clienentothererrors ile işlemler var](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure depolama işlemleri, normal işlevselliğinin bir parçası olarak 299 'den büyük HTTP durum kodları döndürebilir. Ancak bazı durumlarda bu hatalar, istemci uygulamanızı gelişmiş performans için iyileştirebileceğinizi gösterir.

Bu senaryoda, yüzde 100 ' un altında% ' luk bir başarı oranı düşeceğiz. Ancak gereksinimlerinize göre farklı bir ölçüm düzeyi seçebilirsiniz. Uygulamanızı test ederken, önemli performans ölçümleriniz için bir temel tolerans oluşturmanız önerilir. Örneğin, uygulamanıza göre, uygulamanızın% 90 veya% 85 oranında bir tutarlı yüzde başarı oranına sahip olması gerektiğini belirleyebilirsiniz. Ölçüm verileriniz, uygulamanın bu sayıdan sapdığını gösteriyorsa, ne kadar artışa neden olabileceğini araştırabilirsiniz.

Örnek senaryomız için,% 100 başarı oranı ölçüsünün% ' ün altında olduğunu oluşturduğumuzda, ölçülerle bağıntılı hataları bulmak için günlükleri inceleyeceğiz ve bu, daha düşük bir yüzde başarı oranına neden olduğunu anlamak için bunları kullanın. 400 aralığında özel olarak hatalara bakacağız. Daha sonra 404 (bulunamayan) hataları daha yakından araştıracağız.

### <a name="some-causes-of-400-range-errors"></a>400 aralıklı hataların bazı nedenleri
Aşağıdaki örneklerde, Azure Blob depolamaya yönelik istekler için bazı 400 Aralık hataların örneklemesi ve olası nedenleri gösterilmektedir. Bu hataların yanı sıra 300 aralığındaki ve 500 aralığındaki hatalar,% düşük bir başarı oranına katkıda bulunabilir.

Aşağıdaki listelerin tamamen tamamlandığını unutmayın. Genel Azure depolama hatalarıyla ilgili ayrıntılar ve depolama hizmetlerinin her birine özgü hatalar hakkında bilgi için bkz. MSDN 'de [durum ve hata kodları](https://msdn.microsoft.com/library/azure/dd179382.aspx) .

**Durum kodu 404 (bulunamadı) örnekleri**

Blob veya kapsayıcı bulunamadığı için bir kapsayıcıya veya bloba yönelik okuma işlemi başarısız olduğunda gerçekleşir.

* Bir kapsayıcı veya blob bu istekten önce başka bir istemci tarafından silinmişse oluşur.
* Mevcut olup olmadığını denetledikten sonra kapsayıcıyı veya blobu oluşturan bir API çağrısı kullanıyorsanız gerçekleşir. CreateIfNotExists API 'Leri, kapsayıcının veya Blobun varlığını denetlemek için önce bir baş çağrı yapar; yoksa, bir 404 hatası döndürülür ve sonra kapsayıcıyı veya blobu yazmak için ikinci bir PUT çağrısı yapılır.

**Durum kodu 409 (çakışma) örnekleri**

* Yeni bir kapsayıcı veya blob oluşturmak için bir oluşturma API 'SI kullanırsanız, önce varlığı denetlemeden ve bu ada sahip bir kapsayıcı veya blob zaten varsa gerçekleşir.
* Bir kapsayıcı siliniyorsa ve silme işlemi tamamlanmadan önce aynı ada sahip yeni bir kapsayıcı oluşturmaya çalışırsanız gerçekleşir.
* Bir kapsayıcı veya blob üzerinde kira belirtirseniz ve zaten bir kira varsa gerçekleşir.

**Durum kodu 412 (Önkoşul başarısız) örnekleri**

* Koşullu bir üstbilgi tarafından belirtilen koşul karşılanmazsa gerçekleşir.
* Belirtilen kira KIMLIĞI, kapsayıcıdaki veya Blobun kira KIMLIĞIYLE eşleşmediği zaman gerçekleşir.

## <a name="generate-log-files-for-analysis"></a>Analiz için günlük dosyaları oluştur
Bu öğreticide, Message Analyzer 'ı üç farklı türde günlük dosyası ile çalışacak şekilde kullanacağız, ancak bunlardan biriyle çalışmayı seçebilirsiniz:

* Azure depolama günlüğe kaydetmeyi etkinleştirdiğinizde oluşturulan **sunucu günlüğü**. Sunucu günlüğü, Azure depolama hizmetleri-blob, kuyruk, tablo ve dosyadan birinde çağrılan her işlemle ilgili verileri içerir. Sunucu günlüğü hangi işlemin çağrıldığını ve hangi durum kodunun döndürüldüğünü, ayrıca istek ve yanıt hakkındaki diğer ayrıntıları gösterir.
* .NET uygulamanız içinden istemci tarafı günlük kaydını etkinleştirdiğinizde oluşturulan **.NET istemci günlüğü**. İstemci günlüğü, istemcinin isteği nasıl hazırlar ve yanıtı alıp işleme hakkında ayrıntılı bilgiler içerir.
* Azure depolama işlemlerine yönelik işlemler de dahil olmak üzere HTTP/HTTPS isteği ve yanıt verilerinde veri toplayan **http ağ izleme günlüğü**. Bu öğreticide Ileti Çözümleyicisi aracılığıyla ağ izlemesini oluşturacağız.

### <a name="configure-server-side-logging-and-metrics"></a>Sunucu tarafı günlüğe kaydetme ve ölçümleri yapılandırma
İlk olarak, hizmet tarafındaki verileri analiz etmek için Azure depolama günlüğe kaydetme ve ölçümlerini yapılandırmamız gerekir. Günlük ve ölçümleri [Azure Portal](https://portal.azure.com), PowerShell kullanarak veya programlama yoluyla çeşitli yollarla yapılandırabilirsiniz. Günlüğe kaydetme ve ölçümleri yapılandırma hakkında ayrıntılı bilgi için bkz. [ölçümleri etkinleştirme](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) ve [günlüğü etkinleştirme](storage-analytics-logging.md#enable-storage-logging) .

### <a name="configure-net-client-side-logging"></a>.NET istemci tarafında günlüğe kaydetmeyi yapılandırma
Bir .NET uygulaması için istemci tarafı günlüğe kaydetmeyi yapılandırmak için, uygulamanın yapılandırma dosyasında (Web. config veya App. config) .NET tanılamayı etkinleştirin. Ayrıntılar için bkz. MSDN 'de [Java için MICROSOFT Azure depolama SDK ile](https://msdn.microsoft.com/library/azure/dn782844.aspx) birlikte, [.net depolama Istemci kitaplığı](https://msdn.microsoft.com/library/azure/dn782839.aspx) ve istemci tarafı günlüğü ile istemci tarafı günlüğe kaydetme.

İstemci tarafı günlüğü, istemcinin isteği nasıl hazırlar ve yanıtı alıp işleme hakkında ayrıntılı bilgiler içerir.

Depolama Istemci kitaplığı, istemci tarafı günlük verilerini uygulamanın yapılandırma dosyasında (Web. config veya App. config) belirtilen konumda depolar.

### <a name="collect-a-network-trace"></a>Bir ağ izlemesi toplayın
İstemci uygulamanız çalışırken bir HTTP/HTTPS ağ izlemesi toplamak için Ileti Çözümleyicisi 'ni kullanabilirsiniz. İleti çözümleyici arka uçta [Fiddler](https://www.telerik.com/fiddler) 'ı kullanır. Ağ izlemesini toplamadan önce, Fiddler 'i şifrelenmemiş HTTPS trafiğini kaydedecek şekilde yapılandırmanızı öneririz:

1. [Fiddler](https://www.telerik.com/download/fiddler)'i yükler.
2. Fiddler 'ı başlatın.
3. **Araç Seç | Fiddler seçenekleri**.
4. Seçenekler iletişim kutusunda, aşağıda gösterildiği gibi, https trafiğinin **Capture** ve **https trafiğinin şifresini çözme** seçeneklerinin de seçili olduğundan emin olun.

![Fiddler seçeneklerini yapılandırma](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Öğretici için Ileti çözümleyici 'de önce bir ağ izlemesi toplayıp kaydedin, ardından izlemeyi ve günlükleri çözümlemek için bir analiz oturumu oluşturun. Ileti Çözümleyicisi 'nde bir ağ izlemesi toplamak için:

1. Ileti Çözümleyicisi ' nde dosya ' yı seçin **| Hızlı Izleme | Şifrelenmemiş HTTPS**.
2. İzleme hemen başlatılır. İzlemeyi durdurmak için **Durdur** ' u seçerek yalnızca depolama trafiğini izlemek için yapılandırabiliriz.
3. İzleme oturumunu düzenlemek için **Düzenle** ' yi seçin.
4. **Microsoft-PEF-WebProxy** ETW sağlayıcısının sağ tarafındaki **Yapılandır** bağlantısını seçin.
5. **Gelişmiş ayarlar** Iletişim kutusunda **sağlayıcı** sekmesine tıklayın.
6. **Ana bilgisayar adı filtresi** alanında, depolama uç noktalarınızı boşluklarla ayırarak belirtin. Örneğin, uç noktalarınızı aşağıdaki gibi belirtebilirsiniz; `storagesample` depolama hesabınızın adına geçin:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. İletişim kutusundan çıkın ve ana bilgisayar adı filtresi ile izlemeyi toplamaya başlamak için **Yeniden Başlat** ' a tıklayın, böylece Izlemeye yalnızca Azure Storage ağ trafiği eklenir.

> [!NOTE]
> Ağ izinizi toplamayı bitirdikten sonra, bu ayarları HTTPS trafiğinin şifresini çözmek için Fiddler 'da değiştirdiğiniz ayarları döndürmenizi önemle öneririz. Fiddler seçenekleri iletişim kutusunda, **yakala HTTPS bağlantıları** ve **şifresini çöz HTTPS trafiği** onay kutularının işaretini kaldırın.
>
>

Daha fazla bilgi için bkz. technet 'te [ağ Izleme özelliklerini kullanma](https://technet.microsoft.com/library/jj674819.aspx) .

## <a name="review-metrics-data-in-the-azure-portal"></a>Azure portal ölçüm verilerini gözden geçirin
Uygulamanız bir süre çalışmaya başladıktan sonra, hizmetinizin nasıl çalıştığını gözlemlemek için [Azure Portal](https://portal.azure.com) görüntülenen ölçüm grafiklerini gözden geçirebilirsiniz.

İlk olarak, Azure portal depolama hesabınıza gidin. Varsayılan olarak, **Başarı yüzdesi** ölçüsü olan bir izleme grafiği, hesap dikey penceresinde görüntülenir. Grafiği daha önce farklı ölçümleri görüntüleyecek şekilde değiştirdiyseniz **Başarı yüzdesi** ölçüsünü ekleyin.

Artık, eklediğiniz diğer ölçümler ile birlikte izleme grafiğinde **başarı yüzdesini** görürsünüz. Ileti çözümleyici 'de günlükleri analiz ederek sonraki senaryoda, başarı oranı% 100 oranında daha düşük bir değer.

Ölçüm grafiklerini ekleme ve özelleştirme hakkında daha fazla bilgi için bkz. [ölçüm grafiklerini özelleştirme](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Depolama ölçümlerini etkinleştirdikten sonra ölçüm verilerinizin Azure portal görünmesi biraz zaman alabilir. Bunun nedeni, önceki saatin saatlik ölçümlerinin, geçerli saat bitene kadar Azure portal gösterilmemesi nedeniyle oluşur. Ayrıca, dakika ölçümleri Şu anda Azure portal görüntülenmiyor. Bu nedenle, ölçümleri etkinleştirdiğinizde, ölçüm verilerini görmek iki saate kadar zaman alabilir.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Sunucu günlüklerini yerel bir dizine kopyalamak için AzCopy kullanın
Azure depolama sunucu günlük verilerini bloblara yazar, ölçümler tablolara yazılır. Günlük blob 'ları, depolama hesabınız için iyi bilinen `$logs` kapsayıcıda kullanılabilir. Günlük Blobları yıl, ay, gün ve saate göre hiyerarşik olarak adlandırılır. böylece, araştırmak istediğiniz zaman aralığını kolayca bulabilirsiniz. Örneğin `storagesample` , hesapta, 01/02/2015 için günlük Blobları 8-9 ÖÖ ' den bir kapsayıcı olur `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Bu kapsayıcıdaki her bir blob, ile `000000.log`başlayarak ardışık olarak adlandırılır.

Bu sunucu tarafı günlük dosyalarını yerel makinenizde seçtiğiniz bir konuma indirmek için AzCopy komut satırı aracını kullanabilirsiniz. Örneğin, klasöre `C:\Temp\Logs\Server`2 Ocak 2015 ' de gerçekleşen blob işlemlerine yönelik günlük dosyalarını indirmek için aşağıdaki komutu kullanabilirsiniz; depolama hesabınızın adıyla ve `<storageaccountkey>` hesap erişim anahtarınızla değiştirin `<storageaccountname>` . :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy, [Azure İndirmeleri](https://azure.microsoft.com/downloads/) sayfasında indirilebilir. AzCopy kullanma hakkında ayrıntılı bilgi için bkz. [AzCopy komut satırı yardımcı programıyla veri aktarma](storage-use-azcopy.md).

Sunucu tarafı günlüklerini indirme hakkında daha fazla bilgi için bkz. [depolama günlüğü günlük verilerini indirme](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Günlük verilerini çözümlemek için Microsoft Message Analyzer 'ı kullanma
Microsoft Message Analyzer, sorun giderme ve tanılama senaryolarında protokol mesajlaşma trafiğini, olayları ve diğer sistem veya uygulama iletilerini yakalama, görüntüleme ve çözümleme için bir araçtır. İleti çözümleyici Ayrıca günlük ve kaydedilen izleme dosyalarından veri yükleme, toplama ve analiz etmenizi sağlar. Ileti çözümleyici hakkında daha fazla bilgi için bkz. [Microsoft Message Analyzer Işletim kılavuzu](https://technet.microsoft.com/library/jj649776.aspx).

İleti çözümleyici, Azure depolama için sunucu, istemci ve ağ günlüklerini çözümlemenize yardımcı olan varlıkları içerir. Bu bölümde, depolama günlüklerinde% düşük başarı sorununu gidermek için bu araçların nasıl kullanılacağını tartışacağız.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Ileti Çözümleyicisi ve Azure depolama varlıklarını indirme ve yükleme
1. [Ileti Çözümleyicisi](https://www.microsoft.com/download/details.aspx?id=44226) 'Ni Microsoft İndirme Merkezi ' nden indirin ve yükleyiciyi çalıştırın.
2. Ileti Çözümleyicisi 'ni başlatın.
3. **Araçlar** menüsünde, **varlık Yöneticisi**' ni seçin. **Varlık Yöneticisi** Iletişim kutusunda **indirmeler**' ı seçin ve ardından **Azure Storage**' a filtre uygulayın. Aşağıdaki resimde gösterildiği gibi Azure depolama varlıklarını görürsünüz.
4. Azure depolama varlıklarını yüklemek için **Tüm görüntülenecek öğeleri eşitle** ' ye tıklayın. Kullanılabilir varlıklar şunları içerir:
   * **Azure depolama renk kuralları:** Azure depolama renk kuralları, bir izlemede belirli bilgileri içeren iletileri vurgulamak için renk, metin ve yazı tipi stillerini kullanan özel filtreler tanımlamanıza olanak sağlar.
   * **Azure depolama grafikleri:** Azure depolama grafikleri, sunucu günlük verilerini grafik olarak gösteren önceden tanımlanmış grafiklerdir. Azure depolama grafiklerini Şu anda kullanmak için, yalnızca sunucu günlüğünü analiz kılavuzuna yükleyebileceğinizi unutmayın.
   * **Azure depolama Çözümleyicileri:** Azure depolama Çözümleyicileri, analiz kılavuzunda göstermek için Azure depolama istemcisini, sunucusunu ve HTTP günlüklerini ayrıştırır.
   * **Azure depolama filtreleri:** Azure depolama filtreleri, analiz kılavuzunda verilerinizi sorgulamak için kullanabileceğiniz önceden tanımlanmış ölçütlerdir.
   * **Azure depolama görünüm düzenleri:** Azure depolama görünüm düzenleri, analiz kılavuzunda önceden tanımlanmış sütun düzenleri ve gruplandırmalarıdır.
5. Varlıkları yükledikten sonra Ileti çözümleyici 'yi yeniden başlatın.

![İleti Çözümleyicisi varlık Yöneticisi](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Bu öğreticinin amaçları doğrultusunda gösterilen tüm Azure depolama varlıklarını yükler.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Günlük dosyalarınızı Ileti çözümleyici 'ye aktarın
Tüm kayıtlı günlük dosyalarınızı (sunucu tarafı, istemci tarafı ve ağ) analiz için Microsoft Message Analyzer 'da tek bir oturumda içeri aktarabilirsiniz.

1. Microsoft Message Analyzer 'daki **Dosya** menüsünde, **yeni oturum**' a tıklayın ve **boş oturum**' ye tıklayın. **Yeni oturum** iletişim kutusunda, analiz oturumunuz için bir ad girin. **Oturum ayrıntıları** panelinde **dosyalar** düğmesine tıklayın.
2. Ileti Çözümleyicisi tarafından oluşturulan ağ izleme verilerini yüklemek için **Dosya Ekle**' ye tıklayın, Web izleme oturumunuzla. matp dosyanızı kaydettiğiniz konuma gidin,. matp dosyasını seçin ve **Aç**' a tıklayın.
3. Sunucu tarafı günlük verilerini yüklemek için **Dosya Ekle**' ye tıklayın, sunucu tarafı günlüklerinizi indirdiğiniz konuma gidin, çözümlemek istediğiniz zaman aralığı için günlük dosyalarını seçin ve **Aç**' a tıklayın. Ardından, **oturum ayrıntıları** panelinde, Microsoft Message Analyzer 'ın günlük dosyasını doğru bir şekilde ayrıştırabilmesi için, her bir sunucu tarafı günlük dosyası Için **metin günlüğü yapılandırma** açılan kutusunda **AzureStorageLog** olarak ayarlayın.
4. İstemci tarafı günlük verilerini yüklemek için **Dosya Ekle**' ye tıklayın, istemci tarafı günlüklerinizi kaydettiğiniz konuma gidin, analiz etmek istediğiniz günlük dosyalarını seçin ve **Aç**' a tıklayın. Ardından, **oturum ayrıntıları** panelinde, Microsoft Message Analyzer 'ın günlük dosyasını doğru bir şekilde ayrıştırabilmesi için, her bir istemci tarafı günlük dosyası Için **metin günlüğü yapılandırma açılan metnini** **AzureStorageClientDotNetV4** olarak ayarlayın.
5. Günlük verilerini yüklemek ve ayrıştırmak için **yeni oturum** Iletişim kutusunda **Başlat** ' a tıklayın. Günlük verileri Ileti Çözümleyicisi analiz kılavuzunda görüntülenir.

Aşağıdaki resimde sunucu, istemci ve ağ izleme günlük dosyalarıyla yapılandırılmış örnek bir oturum gösterilmektedir.

![Ileti Çözümleyicisi oturumunu yapılandırma](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Ileti Çözümleyicisi 'nin günlük dosyalarını belleğe yüklediğini unutmayın. Büyük bir günlük verisi kümesine sahipseniz, Ileti çözümleyicisinden en iyi performansı elde etmek için filtre uygulamak isteyeceksiniz.

İlk olarak, gözden geçirmek istediğiniz zaman çerçevesini saptayın ve bu zaman çerçevesini mümkün olduğunca küçük tutun. Çoğu durumda, en fazla bir dakika veya saat dönemi gözden geçirmek isteyeceksiniz. İhtiyaçlarınızı karşılayabilirler en küçük günlük kümesini içeri aktarın.

Hala büyük miktarda günlük veriniz varsa, yüklemeden önce günlük verilerinizi filtrelemek için bir oturum filtresi belirtmek isteyebilirsiniz. **Oturum filtresi** kutusunda, önceden tanımlanmış bir filtre seçmek için **kitaplık** düğmesini seçin; Örneğin, Azure depolama filtrelerinden bir zaman aralığı filtrelemek için **genel zaman filtresi ı** ' nı seçin. Daha sonra, görmek istediğiniz Aralık için başlangıç ve bitiş zaman damgasını belirtmek üzere filtre ölçütlerini düzenleyebilirsiniz. Ayrıca belirli bir durum koduna filtre uygulayabilirsiniz; Örneğin, yalnızca durum kodu 404 olan günlük girişlerini yüklemeyi seçebilirsiniz.

Günlük verilerini Microsoft Message Analyzer 'a aktarma hakkında daha fazla bilgi için bkz. TechNet 'te [Ileti verilerini alma](https://technet.microsoft.com/library/dn772437.aspx) .

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Günlük dosyası verilerini ilişkilendirmek için istemci istek KIMLIĞINI kullanın
Azure Storage Istemci kitaplığı her istek için otomatik olarak benzersiz bir istemci istek KIMLIĞI oluşturur. Bu değer, istemci günlüğüne, sunucu günlüğüne ve ağ izlemeye yazılır, bu sayede Ileti çözümleyici içindeki üç günlük içindeki verileri ilişkilendirmek için kullanabilirsiniz. İstemci istek KIMLIĞI hakkında daha fazla bilgi için bkz. [istemci Istek kimliği](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) .

Aşağıdaki bölümlerde, istemci istek KIMLIĞINE göre verileri ilişkilendirmek ve gruplandırmak için önceden yapılandırılmış ve özel düzen görünümlerinin nasıl kullanılacağı açıklanır.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Analiz kılavuzunda görüntülenecek bir görünüm düzeni seçin
Ileti Çözümleyicisi için depolama varlıkları, farklı senaryolar için yararlı gruplandırmalar ve sütunlarla verilerinizi görüntülemek için kullanabileceğiniz önceden yapılandırılmış görünümler olan Azure Storage görünüm düzenlerini içerir. Ayrıca, özel görünüm düzenleri oluşturabilir ve bunları yeniden kullanmak üzere kaydedebilirsiniz.

Aşağıdaki resimde, araç çubuğu şeritten **görünüm düzeni** ' ni seçerek bulunan **görünüm düzeni** menüsü gösterilmektedir. Azure depolama için görünüm düzenleri, menüdeki **Azure Storage** düğümü altında gruplandırılır. Yalnızca Azure depolama Görünüm `Azure Storage` düzenlerini filtrelemek için arama kutusunda arama yapabilirsiniz. Ayrıca bir görünüm düzeninin yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyebilir ve menünün en üstünde görüntüleyebilirsiniz.

![Düzen menüsünü görüntüle](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Başlamak için **Clientrequestıd ve Module tarafından gruplanmış**' i seçin. Bu görünüm düzeni, istemci istek KIMLIĞINE göre ve ardından kaynak günlük dosyasına (veya Ileti Çözümleyicisi 'nde **Modül** ) göre, her üç günlüğün ilk olarak günlük verilerini gruplandırır. Bu görünümle, belirli bir istemci istek KIMLIĞININ detayına gidebilir ve bu istemci istek KIMLIĞI için üç günlük dosyasından verileri görebilirsiniz.

Aşağıdaki resimde, örnek günlük verilerine uygulanan bu düzen görünümü gösterilmektedir ve bu da sütun alt kümesi görüntülenir. Belirli bir istemci istek KIMLIĞI için analiz kılavuzunun, istemci günlüğünden, sunucu günlüğünden ve ağ izlemesinden verileri görüntülediğini görebilirsiniz.

![Azure depolama görünüm düzeni](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Farklı günlük dosyalarında farklı sütunlar bulunur, bu nedenle birden çok günlük dosyasındaki veriler analiz kılavuzunda görüntülenirken, bazı sütunlar belirli bir satır için herhangi bir veri içermeyebilir. Örneğin, yukarıdaki resimde, istemci günlüğü satırları **zaman damgası**, **timegeçen**, **kaynak**ve **hedef** sütunları için herhangi bir veri göstermez, çünkü bu sütunlar istemci günlüğünde yoktur, ancak Ağ izlemesinde mevcut değildir . Benzer şekilde, **zaman damgası** sütunu sunucu günlüğünden zaman damgası verilerini görüntüler, ancak sunucu günlüğünün bir parçası olmayan **timegeçen**, **kaynak**ve **hedef** sütunlar için hiçbir veri gösterilmez.
>
>

Azure depolama görünüm düzenlerini kullanmanın yanı sıra kendi görünüm düzenlerinizi de tanımlayabilir ve kaydedebilirsiniz. Verileri gruplandırmak için istenen diğer alanları seçebilirsiniz ve gruplandırmayı özel mizanpajın bir parçası olarak kaydedebilirsiniz.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Analiz kılavuzuna renk kuralları uygulama
Depolama varlıkları Ayrıca analiz kılavuzunda farklı hata türlerini belirlemek için görsel bir yol sunan renk kurallarını da içerir. Önceden tanımlanmış renk kuralları HTTP hataları için geçerlidir, bu nedenle yalnızca sunucu günlüğü ve ağ izleme için görünürler.

Renk kurallarını uygulamak için araç çubuğu şeridinde **renk kuralları** ' nı seçin. Menüde Azure depolama renk kurallarını görürsünüz. Öğreticide, aşağıdaki resimde gösterildiği gibi **Istemci hataları (durum kodu 400 ile 499 arasında)** seçeneğini belirleyin.

![Azure depolama görünüm düzeni](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Azure depolama renk kurallarını kullanmanın yanı sıra kendi renk kurallarınızı da tanımlayabilir ve kaydedebilirsiniz.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>400 aralıklı hataları bulmak için günlük verilerini gruplandırın ve filtreleyin
Daha sonra, 400 aralığındaki tüm hataları bulmak için günlük verilerini gruplarız ve filtreleyeceğiz.

1. Analiz kılavuzunda **StatusCode** sütununu bulun, sütun başlığına sağ tıklayın ve **Grup**' u seçin.
2. Sonra, **Clientrequestıd** sütunundaki grup. Analiz kılavuzundaki verilerin artık durum koduna ve istemci istek KIMLIĞINE göre düzenlendiğini görürsünüz.
3. Henüz görüntülenmiyorsa görünüm filtre araç penceresini görüntüleyin. Araç çubuğu şeridinde **araç pencereleri**' ni ve ardından **filtre görüntüle**' yi seçin.
4. Günlük verilerini yalnızca 400 aralıklı hataları görüntüleyecek şekilde filtrelemek için, **görüntüleme filtresi** penceresine aşağıdaki filtre ölçütlerini ekleyin ve **Uygula**' ya tıklayın:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Aşağıdaki resimde bu gruplandırma ve filtrenin sonuçları gösterilmektedir. Durum kodu 409 için gruplandırmanın altındaki **Clientrequestıd** alanı genişletiliyor, örneğin, bu durum kodu ile sonuçlanan bir işlemi gösterir.

![Azure depolama görünüm düzeni](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Bu filtreyi uyguladıktan sonra, istemci günlüğü bir **StatusCode** sütunu içermediği için istemci günlüğündeki satırların hariç tutulduğunu görürsünüz. ' I kullanmaya başlamak için sunucu ve ağ izleme günlüklerini inceleyerek 404 hataları bulabilir ve ardından bunlara yönelik istemci işlemlerini incelemek üzere istemci günlüğüne geri döneceğiz.

> [!NOTE]
> Durum kodunun null olduğu günlük girdilerini içeren filtreye bir ifade eklerseniz, **StatusCode** sütununda filtre uygulayabilir ve istemci günlüğü de dahil olmak üzere üç günlükteki verileri görüntüleyebilirsiniz. Bu filtre ifadesini oluşturmak için şunu kullanın:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Bu filtre, istemci günlüğünden tüm satırları ve yalnızca durum kodu 400 ' den büyük olan sunucu günlüğünden ve HTTP günlüğünden alınan satırları döndürür. Bunu istemci istek KIMLIĞINE ve modüle göre gruplanmış görünüm düzenine uygularsanız, üç günlüğün tümünün temsil edildiği yerleri bulmak için günlük girdilerini arayabilir veya kaydırabilirsiniz.   
>
>

### <a name="filter-log-data-to-find-404-errors"></a>404 hata bulmak için günlük verilerini filtrele
Depolama varlıkları, Aradığınız hataları veya eğilimleri bulmak üzere günlük verilerini daraltmak için kullanabileceğiniz önceden tanımlanmış filtreler içerir. Daha sonra, önceden tanımlanmış iki filtre uygulayacağız: sunucu ve ağ izleme günlüklerini 404 hata için filtreleyen bir tane ve belirli bir zaman aralığındaki verileri filtreleyen bir tane.

1. Henüz görüntülenmiyorsa görünüm filtre araç penceresini görüntüleyin. Araç çubuğu şeridinde **araç pencereleri**' ni ve ardından **filtre görüntüle**' yi seçin.
2. Görünüm filtresi penceresinde **kitaplık**' ı seçin ve Azure depolama filtrelerini bulmak `Azure Storage` için arama yapın. **Tüm günlüklerde 404 (bulunamadı) iletilerini filtrele '** yi seçin.
3. **Kitaplık** menüsünü yeniden görüntüleyin ve **genel zaman filtresini**bulup seçin.
4. Filtrede gösterilen zaman damgasını görüntülemek istediğiniz aralığa göre düzenleyin. Bu, analiz edilecek veri aralığını daraltmanıza yardımcı olur.
5. Filtreniz aşağıdaki örneğe benzer görünmelidir. Filtreyi analiz kılavuzuna uygulamak için **Uygula** ' ya tıklayın.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Azure depolama görünüm düzeni](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Günlük verilerinizi çözümleyin
Verilerinizi gruplandırdığınıza ve filtrelendiğine göre, 404 hata oluşturan bireysel isteklerin ayrıntılarını inceleyebilirsiniz. Geçerli görünüm düzeninde, veriler istemci istek KIMLIĞINE ve ardından günlük kaynağına göre gruplandırılır. StatusCode alanının 404 içerdiği isteklere filtre yaptığımız için, istemci günlük verilerini değil yalnızca sunucu ve ağ izleme verilerini görüyoruz.

Aşağıdaki resimde, blob olmadığı için bir get blob işleminin bir 404 tarafından gerçekleştiği belirli bir istek gösterilmektedir. İlgili verileri görüntülemek için bazı sütunların standart görünümden kaldırıldığını unutmayın.

![Filtrelenmiş sunucu ve ağ Izleme günlükleri](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Daha sonra, bu istemci isteği KIMLIĞINI istemci günlük verileriyle ilişkilendiriyoruz ve bu, hata meydana geldiğinde istemcinin hangi işlemleri yaptığını görür. Bu oturum için, ikinci bir sekmede açılan istemci günlüğü verilerini görüntülemek üzere yeni bir Analysis Grid görünümü görüntüleyebilirsiniz:

1. İlk olarak, **Clientrequestıd** alanının değerini panoya kopyalayın. Bunu, herhangi bir satırı seçip **clientrequestıd** alanını bularak, veri değerine sağ tıklayıp **' clientrequestıd '** öğesini seçerek yapabilirsiniz.
2. Araç çubuğu şeridinde **yeni Görüntüleyici**' yi seçin ve ardından yeni bir sekme açmak Için **analiz Kılavuzu** ' nu seçin. Yeni sekme, günlük dosyalarınızda gruplandırma, filtreleme veya renk kuralları olmadan tüm verileri gösterir.
3. Araç çubuğu şeridinde **düzeni görüntüle**' yi seçin ve ardından **Azure depolama** bölümünde **tüm .NET istemci sütunları** ' nı seçin. Bu görünüm düzeni, istemci günlüğünden ve sunucu ve ağ izleme günlüklerinin verilerini gösterir. Varsayılan olarak, **MessageNumber** sütununda sıralanır.
4. Daha sonra istemci günlüğünde istemci istek KIMLIĞI ' ni arayın. Araç çubuğu şeridinde **Iletileri bul**' u seçin ve ardından **bul** alanındaki istemci istek kimliği üzerinde bir özel filtre belirtin. Kendi istemci istek KIMLIĞINIZI belirterek filtre için bu sözdizimini kullanın:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

İleti çözümleyici, arama ölçütünün istemci istek KIMLIĞIYLE eşleştiği ilk günlük girişini bulur ve seçer. İstemci günlüğünde, her bir istemci istek KIMLIĞI için birkaç giriş vardır. bu nedenle, bunların hepsini birlikte görmeyi kolaylaştırmak için **Clientrequestıd** alanındaki grupları gruplamak isteyebilirsiniz. Aşağıdaki resimde, belirtilen istemci isteği KIMLIĞI için istemci günlüğündeki tüm iletiler gösterilmektedir.

![404 hatalarını gösteren istemci günlüğü](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Bu iki sekmede görünüm düzenlerinde gösterilen verileri kullanarak, hataya neyin neden olabileceğini belirlemek için istek verilerini çözümleyebilirsiniz. Ayrıca, önceki bir olayın 404 hatasına yol açmayabilir olup olmadığını görmek için bu tek başına isteklere de bakabilirsiniz. Örneğin, Blobun silinip silinmediğini veya bir kapsayıcı veya blob üzerinde bir CreateIfNotExists API 'sini çağıran istemci uygulaması nedeniyle hata olup olmadığını öğrenmek için bu istemci isteği KIMLIĞINDEN önceki istemci günlüğü girdilerini gözden geçirebilirsiniz. İstemci günlüğünde, blob 'un adresini **Açıklama** alanında bulabilirsiniz; sunucu ve ağ izleme günlüklerinde, bu bilgiler **Özet** alanında görüntülenir.

404 hatasını veren Blobun adresini öğrendikten sonra daha fazla araştırma yapabilirsiniz. Aynı blob üzerinde işlemlerle ilişkili diğer iletiler için günlük girişlerinde arama yaparsanız, istemcinin varlığı daha önce silmiş olup olmadığını kontrol edebilirsiniz.

## <a name="analyze-other-types-of-storage-errors"></a>Diğer depolama hatalarının türlerini çözümleme
Artık, günlük verilerinizi çözümlemek üzere Ileti çözümleyici 'yi kullanma hakkında bilgi sahibi olduğunuza göre, görünüm düzenlerini, renk kurallarını ve arama/filtreleme 'yi kullanarak diğer hata türlerini analiz edebilirsiniz. Aşağıdaki tablolarda karşılaşabileceğiniz bazı sorunlar ve bunları bulmak için kullanabileceğiniz filtre ölçütleri listelenmektedir. Filtre ve Ileti Çözümleyicisi filtreleme dilini oluşturma hakkında daha fazla bilgi için bkz. [Ileti verilerini filtreleme](https://technet.microsoft.com/library/jj819365.aspx).

| Araştırmak için... | Filtre Ifadesini kullan... | İfade günlüğe uygulanır (Istemci, sunucu, ağ, tümü) |
| --- | --- | --- |
| Bir kuyrukta ileti tesliminde beklenmeyen gecikmeler |AzureStorageClientDotNetV4. Description, "yeniden deneme başarısız işlem" içeriyor. |İstemci |
| Percentkısıtlar Lingerror 'da HTTP artışı |HTTP. Response. StatusCode = = 500 &#124; &#124; http. Response. StatusCode = = 503 |Ağ |
| PercentTimeoutError 'da artır |HTTP. Response. StatusCode = = 500 |Ağ |
| PercentTimeoutError (tümü) içinde artır |\* StatusCode = = 500 |Tümü |
| PercentNetworkError 'da artış |AzureStorageClientDotNetV4. EventLogEntry. Level < 2 |İstemci |
| HTTP 403 (yasak) iletileri |HTTP. Response. StatusCode = = 403 |Ağ |
| HTTP 404 (bulunamadı) iletileri |HTTP. Response. StatusCode = = 404 |Ağ |
| 404 (tümü) |\* StatusCode = = 404 |Tümü |
| Paylaşılan Erişim İmzası (SAS) yetkilendirme sorunu |AzureStorageLog. RequestStatus = = "SASAuthorizationError" |Ağ |
| HTTP 409 (çakışma) iletileri |HTTP. Response. StatusCode = = 409 |Ağ |
| 409 (tümü) |\* StatusCode = = 409 |Tümü |
| Düşük PercentSuccess veya Analytics günlük girişlerinde, Clienentothererrors işlem durumu ile işlemler var |AzureStorageLog. RequestStatus = = "Clienentothererror" |Sunucusu |
| Nagle uyarısı |((AzureStorageLog. Endtoendlatnems-AzureStorageLog. Serverlatnems) > (AzureStorageLog. Serverlatnems * 1,5)) ve (AzureStorageLog. RequestPacketSize < 1460) ve (AzureStorageLog. Endtoendlat, MS-AzureStorageLog. Serverlatnems > = 200) |Sunucusu |
| Sunucu ve ağ günlüklerinde zaman aralığı |#Timestamp > = 2014-10-20T16:36:38 ve #Timestamp < = 2014-10-20T16:36:39 |Sunucu, ağ |
| Sunucu günlüklerindeki zaman aralığı |AzureStorageLog. Timestamp > = 2014-10-20T16:36:38 ve AzureStorageLog. Timestamp < = 2014-10-20T16:36:39 |Sunucusu |

## <a name="next-steps"></a>Sonraki adımlar
Azure Storage 'da uçtan uca senaryolar hakkında sorun giderme hakkında daha fazla bilgi için şu kaynaklara bakın:

* [Microsoft Azure Depolama izleme, tanılama ve sorun giderme](storage-monitoring-diagnosing-troubleshooting.md)
* [Depolama Analizi](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Azure portal bir depolama hesabını izleme](storage-monitor-storage-account.md)
* [AzCopy Komut Satırı Yardımcı Programı ile veri aktarımı](storage-use-azcopy.md)
* [Microsoft Ileti Çözümleyicisi Işletim kılavuzu](https://technet.microsoft.com/library/jj649776.aspx)
