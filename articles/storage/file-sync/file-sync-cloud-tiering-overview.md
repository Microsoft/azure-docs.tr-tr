---
title: Bulut katmanlaması Azure Dosya Eşitleme anlayın | Microsoft Docs
description: İsteğe bağlı bir Azure Dosya Eşitleme özelliği olan bulut katmanlamayı anlayın. Sık erişilen dosyalar sunucuda yerel olarak önbelleğe alınır; diğerleri Azure dosyaları için katmanlılanlardır.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f58aa4e2aa4637dfb9fc8b29c52209975e3e367
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797383"
---
# <a name="cloud-tiering-overview"></a>Bulut katmanlaması genel bakış
İsteğe bağlı bir Azure Dosya Eşitleme bulut katmanlaması, şirket içi dosya sunucusunun performansını korurken gereken yerel depolama miktarını azaltır.

Bu özellik etkinleştirildiğinde, yerel sunucunuzda yalnızca sık erişilen dosyaları depolar. Seyrek erişilen (seyrek erişimli) dosyalar ad alanına (dosya ve klasör yapısı) ve dosya içeriğine ayrılır. Ad alanı yerel olarak depolanır ve buluttaki bir Azure dosya paylaşımında depolanan dosya içeriği. 

Kullanıcı katmanlı bir dosya açtığında, Azure Dosya Eşitleme dosya verilerini Azure 'daki dosya paylaşımından sorunsuzca geri çeker.

## <a name="how-cloud-tiering-works"></a>Bulut katmanlaması nasıl yapılır?

### <a name="cloud-tiering-policies"></a>Bulut katmanlama ilkeleri
Bulut katmanlamayı etkinleştirdiğinizde, katman seyrek erişimli dosyalar için Azure Dosya Eşitleme bildirmek üzere ayarlayabileceğiniz iki ilke vardır: **birim boş alan ilkesi** ve **tarih ilkesi**. 

#### <a name="volume-free-space-policy"></a>Birim boş alan ilkesi
**Birim boş alan ilkesi** , yerel diskinizde belirli bir miktarda alan olduğunda Azure dosya eşitleme seyrek erişimli dosyaları buluta göre katmana bildirir. 

Örneğin, yerel disk kapasiteniz 200 GB ise ve yerel disk kapasitenizi her zaman ücretsiz olarak bırakmak 40 istiyorsanız, birim boş alan ilkesini %20 olarak ayarlamanız gerekir. Birim boş alanı, bireysel dizin veya sunucu uç noktaları düzeyinde değil, birim düzeyinde geçerlidir. 

Birim boş alan ilkesi 'nin, yeni bir sunucu uç noktası eklendiğinde ilk indirilen dosyaları nasıl etkileyeceğini öğrenmek için ( [bulut katmanlamayı etkileyen eşitleme ilkelerine](#sync-policies-that-affect-cloud-tiering)bakın) bölümüne bakın.

#### <a name="date-policy"></a>Tarih ilkesi
**Tarih ilkesi** ile, seyrek erişimli dosyalar (yani, okunan veya yazılan) x güne kadar gün boyunca buluta katmanlı. Örneğin, yalnızca 15 günden daha uzun bir süre geçmiş dosyalara, genellikle arşiv dosyaları olduğunu fark ederseniz, tarih ilkenizi 15 güne ayarlamanız gerekir. 

Tarih ilkesi ve birim boş alan ilkesinin birlikte nasıl çalıştığı hakkında daha fazla örnek için bkz. [Azure dosya eşitleme bulut katmanlama Ilkeleri seçme](file-sync-choose-cloud-tiering-policies.md).

### <a name="windows-server-data-deduplication"></a>Windows Server yinelenen verileri kaldırma
Yinelenen verileri kaldırma özelliği, Windows Server 2016 ile başlayarak bulut katmanlaması etkinleştirilmiş birimlerde desteklenir. Daha fazla ayrıntı için lütfen [Azure dosya eşitleme dağıtımı planlama](file-sync-planning.md#data-deduplication)konusuna bakın.

### <a name="cloud-tiering-heatmap"></a>Bulut katmanlama ısı haritasını
Azure Dosya Eşitleme, zaman içinde dosya erişimini (okuma ve yazma işlemleri) izler ve sık ve son erişimin ne kadar sık olduğunu, her dosyaya bir ısı puanı atar. Bu puanları, her sunucu uç noktasında ad alanınızı "heatmap" oluşturmak için kullanır. Bu ısı haritasını, bulut katmanlaması etkin olan bir konumda tüm eşitleme dosyalarının, ısı puanlarına göre sıralanmış bir listesidir. En son açılan dosyalar sık erişilen olarak kabul edilir, ancak belirli bir süre boyunca erişilen ve erişilemeyen dosyalar seyrek kabul edilir. 

Bu heatmap içindeki tek bir dosyanın göreli konumunu öğrenmek için sistem, zaman damgalarının en fazla değerini şu sırada kullanır: MAX (son erişim saati, son değiştirilme saati, oluşturma saati). 

Genellikle, son erişim zamanı izlenir ve kullanılabilir. Ancak, yeni bir sunucu uç noktası oluşturulduğunda, bulut katmanlaması etkinken dosya erişimini gözlemlemeye yetecek kadar zaman geçti. Geçerli bir son erişim zamanı yoksa, heatmap 'teki göreli konumu değerlendirmek için bunun yerine son değiştirme zamanı kullanılır.  

Tarih ilkesi aynı şekilde çalışmaktadır. Son erişim zamanı olmadan tarih ilkesi, son değiştirilme zamanına göre hareket eder. Bu kullanılamıyorsa, dosyanın oluşturma saatine geri döner. Zaman içinde, sistem daha fazla ve daha fazla dosya erişim isteği gözlemleyecek ve otomatik olarak izlenen son erişim zamanını kullanmaya başlayacak.

> [!Note]
> Bulut katmanlaması, son erişim süresini izlemek için NTFS özelliğine bağlı değildir. Bu NTFS özelliği varsayılan olarak kapalıdır ve performans konuları nedeniyle bu özelliği el ile etkinleştirmenizi önermiyoruz. Bulut katmanlaması, son erişim süresini ayrı olarak izler.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Bulut katmanlamayı etkileyen eşitleme ilkeleri

Azure Dosya Eşitleme Agent sürümü 11 ile, ayarlayabileceğiniz iki ek Azure Dosya Eşitleme ilkesi vardır: **ilk indirme** ve **proaktif geri çekme**.

#### <a name="initial-download"></a>İlk indirme

Bir sunucu, içindeki dosyalarla bir Azure dosya paylaşımıyla bağlantı kurarak, artık sunucunun dosya paylaşma verilerini ilk kez indirmesini seçebilirsiniz. Bulut katmanlama etkinleştirildiğinde iki seçeneğiniz vardır. 

![Bulut katmanlama etkinleştirildiğinde ilk indirmenin ekran görüntüsü](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

İlk seçenek önce ad alanını yeniden çağırır, sonra yerel disk kapasitesine ulaşılana kadar dosya içeriğini son değiştirme zaman damgasıyla çağırır. Yeterli disk alanınız varsa ve son değiştirilen dosyaların yerel olarak önbelleğe alınması gerektiğini biliyorsanız, bu seçenek ideal olur. Disk alanının bulunmaması nedeniyle yerel olarak depolanmayan dosyalar katmanlı olacaktır.        

İkinci seçenek başlangıçta yalnızca ad alanını hatırlayabiliyor ve yalnızca erişildiğinde dosya içeriğini geri çekmelidir. Bu seçenek, yerel diskinizde kullanılan kapasiteyi en aza indirmek ve kullanıcıların hangi dosyaların yerel olarak önbelleğe alınması gerektiğine karar vermesini istiyorsanız en iyi seçenektir. Tüm dosyalar, bu seçenekle katmanlı dosyalar olarak başlatılır.

![Bulut katmanlama devre dışı bırakıldığında ilk indirmenin ekran görüntüsü](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Bulut katmanlaması devre dışı bırakıldığında, katmanlı dosyaların hepsini bir arada kullanmaktan kaçınmanız gereken üçüncü bir seçeneğiniz vardır. Bu durumda, dosyalar yalnızca tam olarak İndirildiklerinde sunucuda görünür. Tam dosyaların mevcut olmasını gerektiren uygulamalarınız varsa ve ad alanındaki katmanlı dosyalara tolerans uygulamadıysanız, bu ideal olur.      

#### <a name="proactive-recalling"></a>Proaktif geri çekme

Bir dosya oluşturulduğunda veya değiştirildiğinde, belirttiğiniz sunuculara bir dosyayı proaktif olarak geri çekmeniz gerekir. Bu, yeni veya değiştirilmiş dosyayı belirtilen her bir sunucuda tüketim için hazır hale getirir. 

Örneğin, küresel olarak dağıtılmış bir şirketin ABD 'de ve Hindistan 'da şube ofisi vardır. Sabah (US saat) bilgi çalışanları, yeni bir proje için yeni bir klasör ve yeni dosyalar oluşturur ve tüm gün üzerinde çalışır. Azure Dosya Eşitleme, klasör ve dosyaları Azure dosya paylaşımında (bulut uç noktası) eşitler. Hindistan 'daki bilgi çalışanları proje üzerinde kendi saat dilimlerinde çalışmaya devam edecektir. Her sabah ulaşan yerel Azure Dosya Eşitleme etkin sunucu, Hindistan ekibinin yerel bir önbellekte etkin bir şekilde çalışması için bu yeni dosyaların yerel olarak kullanılabilmesini gerektirir. Bu modu etkinleştirmek, istek üzerine geri çekme nedeniyle ilk dosya erişiminin daha yavaş olmasını engeller ve sunucunun Azure dosya paylaşımında değiştirildikleri anda dosyaları önceden çekmesine olanak tanır.

Sunucuya geri çekilen dosyalar gerçekten yerel olarak gerekmiyorsa gereksiz geri çağırma, çıkış trafiğinizi ve maliyetlerinizi artırabilir. Bu nedenle, yalnızca bir sunucu önbelleğinin buluttan son değişikliklerle önceden doldurulmasının, bu sunucudaki dosyaları kullanan kullanıcılar veya uygulamalar üzerinde olumlu bir etkiye sahip olduğunu bildiğiniz durumlarda proaktif geri çekmeyi etkinleştirin. 

Proaktif geri çekmeyi etkinleştirmek, sunucuda bant genişliği kullanımının artmasına neden olabilir ve geri çekilen dosyalardaki artış nedeniyle yerel sunucu üzerindeki diğer görece yeni içeriğin öngörüde olmasını sağlayabilir. Bu, sırasıyla katmanlı olan dosyalar sunucular tarafından etkin olarak kabul edildiğinde daha fazla geri çekme işlemine yol açabilir. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Şu anda etkin olan bir sunucu uç noktası için Azure dosya paylaşımının karşıdan yükleme davranışını gösteren bir resim ve bunu değiştirmeye izin veren bir menü açmak için bir düğme.":::

Proaktif geri çekme hakkında daha fazla bilgi için bkz. [dağıtma Azure dosya eşitleme](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Katmanlı ve yerel olarak önbelleğe alınan dosya davranışı

Bulut katmanlaması, ad alanı (dosya ve klasör hiyerarşisi ile dosya özellikleri) ve dosya içeriği arasındaki ayırmasıdır. 

#### <a name="tiered-file"></a>Katmanlı dosya

Katmanlı dosyalar için, dosya içeriğinin yerel olarak depolanmadığından diskteki boyut sıfırdır. Bir dosya katmanlı olduğunda, Azure Dosya Eşitleme dosya sistemi filtresi (StorageSync.sys) dosyayı yerel olarak bir işaretçi (yeniden ayrıştırma noktası) ile değiştirir. Yeniden ayrıştırma noktası, Azure dosya paylaşımındaki dosyanın bir URL 'sini temsil eder. Katmanlı bir dosyanın hem "çevrimdışı" özniteliği hem de FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS özniteliği bulunur. böylece, üçüncü taraf uygulamaların katmanlı dosyaları güvenle belirleyebilmesini sağlayabilirsiniz.   

![Yalnızca katmanlı ad alanı olduğunda bir dosyanın özelliklerinin ekran görüntüsü.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Yerel olarak önbelleğe alınan dosya

Öte yandan, şirket içi bir dosya sunucusunda depolanan bir dosya için diskteki boyut, dosyanın tamamı (dosya öznitelikleri + dosya içeriği) yerel olarak depolandığından dosyanın mantıksal boyutuna eşittir.     

![Katmanlı-ad alanı + dosya içeriği olmayan bir dosyanın özelliklerinin ekran görüntüsü.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Ayrıca, bir dosyanın kısmen katmanlı (veya kısmen geri çekilmiş) olması da mümkündür. Kısmen katmanlı bir dosyada, dosyanın bir kısmı diskte bulunur. Dosyalar, dosyaları akışa erişimi destekleyen multimedya oyuncuları veya ZIP yardımcı programları gibi uygulamalar tarafından kısmen okunarak bu durum oluşabilir. Azure Dosya Eşitleme akıllı ve yalnızca bağlı Azure dosya paylaşımından istenen bilgileri geri çeker.

> [!NOTE]
> Boyut, dosyanın mantıksal boyutunu temsil eder. Diskteki boyut, diskte depolanan dosya akışının fiziksel boyutunu temsil eder.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Dosya Eşitleme bulut katmanlama ilkeleri seçin](file-sync-choose-cloud-tiering-policies.md)
* [Azure Dosya Eşitleme dağıtımı planlama](file-sync-planning.md)