---
title: Azure Analysis Services ölçeği genişletme | Microsoft Docs
description: Genişleme Azure Analysis Services sunucularını çoğaltın. Daha sonra istemci sorguları, genişleme sorgu havuzundaki birden çok sorgu çoğaltması arasında dağıtılabilir.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2385cb811e322bd44daefa03d821b2ae47e0652
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769274"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services ölçeğini genişletme

Genişleme ile, istemci sorguları bir *sorgu havuzundaki* birden çok *sorgu çoğaltması* arasında dağıtılabilir ve bu da yüksek sorgu iş yükleri sırasında yanıt sürelerini azaltır. Aynı zamanda, işlem işlemleri tarafından olumsuz etkilenmemesini sağlamak için sorgu havuzundan işlemeyi ayırabilirsiniz. Ölçek Genişletme Azure portal veya Analysis Services REST API kullanılarak yapılandırılabilir.

Standart fiyatlandırma katmanındaki sunucular için ölçek genişletme kullanılabilir. Her sorgu çoğaltması, sunucunuz ile aynı hızda faturalandırılır. Tüm sorgu çoğaltmaları, sunucunuz ile aynı bölgede oluşturulur. Yapılandırabileceğiniz sorgu çoğaltmalarının sayısı sunucunuzun bulunduğu bölge tarafından sınırlandırılır. Daha fazla bilgi için bkz. [bölgeye göre kullanılabilirlik](analysis-services-overview.md#availability-by-region). Genişleme, sunucunuz için kullanılabilir bellek miktarını artırmaz. Belleği artırmak için planınızı yükseltmeniz gerekir. 

## <a name="why-scale-out"></a>Neden ölçeklendirmeliyim?

Tipik bir sunucu dağıtımında, bir sunucu hem işlem sunucusu hem de sorgu sunucusu olarak görev yapar. Sunucunuzdaki modellerdeki istemci sorgularının sayısı, sunucunuzun planı için sorgu Işleme birimlerini (QPU) aşarsa veya model işleme, yüksek sorgu iş yükleri ile aynı zamanda gerçekleşirse performans düşebilir. 

Genişleme ile, yedi adede kadar ek sorgu çoğaltma kaynağı ( *birincil* sunucunuz dahil sekiz toplam) ile bir sorgu havuzu oluşturabilirsiniz. Sorgu havuzundaki çoğaltmaların sayısını, önemli zamanlarda QPU taleplerini karşılayacak şekilde ölçeklendirebilir ve istediğiniz zaman bir işlem sunucusunu sorgu havuzundan ayırabilirsiniz. 

Bir sorgu havuzunda sahip olduğunuz sorgu çoğaltmalarının sayısından bağımsız olarak, işleme iş yükleri sorgu çoğaltmaları arasında dağıtılmaz. Birincil sunucu işlem sunucusu olarak görev yapar. Sorgu çoğaltmaları yalnızca birincil sunucu ve sorgu havuzundaki her çoğaltma arasında eşitlenen model veritabanlarına karşı sorguları sunar. 

Ölçeği ölçeklendirirken, yeni sorgu çoğaltmalarının sorgu havuzuna artımlı olarak eklenmesi beş dakikaya kadar sürebilir. Tüm yeni sorgu çoğaltmaları çalışır duruma geldiğinde, yeni istemci bağlantıları sorgu havuzundaki kaynaklar arasında yük dengelemesi yapılır. Mevcut istemci bağlantıları, o anda bağlı oldukları kaynaktan değiştirilmez. ' De ölçeklendirilirken, sorgu havuzundan kaldırılmakta olan bir sorgu havuzu kaynağına yönelik mevcut istemci bağlantıları sonlandırılır. İstemciler kalan bir sorgu havuzu kaynağına yeniden bağlanabilir.

## <a name="how-it-works"></a>Nasıl çalışır?

Ölçeği ilk kez yapılandırırken, birincil sunucunuzdaki model veritabanları yeni bir sorgu havuzundaki yeni yinelemelerle *otomatik olarak* eşitlenir. Otomatik eşitleme yalnızca bir kez gerçekleşir. Otomatik eşitleme sırasında, birincil sunucunun veri dosyaları (blob depolamada Rest 'te şifrelenir), blob depolamada bekleyen bir ikinci konuma kopyalanır. Sorgu havuzundaki çoğaltmalar daha sonra ikinci dosya *kümesindeki verilerle birlikte* gösterilir. 

Otomatik eşitleme yalnızca bir sunucuyu ilk kez ölçeklendirirken gerçekleştirilir, ayrıca el ile eşitleme gerçekleştirebilirsiniz. Sorgu havuzundaki çoğaltmalarda verilerin eşitlenmesi, birincil sunucuyla eşleşir. Birincil sunucudaki modelleri işlerken (yenileme), işleme işlemleri *tamamlandıktan sonra* bir eşitleme gerçekleştirilmelidir. Bu eşitleme, güncelleştirilmiş verileri, BLOB depolama alanındaki birincil sunucunun dosyalarından ikinci dosya kümesine kopyalar. Sorgu havuzundaki çoğaltmalar daha sonra blob depolamada bulunan ikinci dosya kümesinden güncelleştirilmiş verilerle birlikte gösterilir. 

Daha sonraki bir genişleme işlemi gerçekleştirirken, örneğin, sorgu havuzundaki çoğaltmaların sayısını iki ila beş arasında artırarak, yeni çoğaltmalar blob depolamada bulunan ikinci dosya kümesindeki verilerle birlikte gösterilir. Eşitleme yok. Daha sonra ölçeği ölçeklendirdikten sonra bir eşitleme gerçekleştirirseniz, sorgu havuzundaki yeni çoğaltmalar, fazladan bir hidrasyon olmak üzere iki kez yeniden görüntülenir. Sonraki bir genişleme işlemi gerçekleştirirken göz önünde bulundurmanız önemlidir:

* Eklenen çoğaltmaların gereksiz bir şekilde hidramasını önlemek için *genişleme işleminden önce* bir eşitleme gerçekleştirin. Aynı anda çalışan eşzamanlı eşitlemeye ve genişleme işlemlerine izin verilmez.

* Hem işleme *hem* de genişleme işlemlerini otomatikleştirerek, öncelikle birincil sunucuda verilerin işlenmesi, sonra bir eşitleme gerçekleştirmesi ve sonra da genişleme işlemi gerçekleştirmeniz önemlidir. Bu sıra, QPU ve bellek kaynakları üzerinde en düşük etkiyi sağlar.

* Genişleme işlemleri sırasında, birincil sunucu dahil olmak üzere sorgu havuzundaki tüm sunucular geçici olarak çevrimdışı.

* Sorgu havuzunda çoğaltma olmadığında bile eşitlemeye izin verilir. Birincil sunucudaki bir işleme işlemindeki yeni verilerle bir veya daha fazla kopyaya ölçeklendirirseniz, eşitleme işlemini sorgu havuzunda çoğaltma olmadan gerçekleştirin ve ardından ölçeği ölçeklendirin. Ölçeklendirmeden önce eşitleme, yeni eklenen çoğaltmaların gereksiz bir şekilde yeniden alımını önler.

* Birincil sunucudan bir model veritabanını silerken, sorgu havuzundaki çoğaltmalardan otomatik olarak silinmez. Bu veritabanının dosya/s dosyasını çoğaltmanın paylaşılan BLOB depolama konumundan kaldıran [Sync-Azanalysisservicesınstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell komutunu kullanarak bir eşitleme işlemi gerçekleştirmeniz gerekir ve ardından sorgu havuzundaki çoğaltmalarda model veritabanını siler. Bir model veritabanının, birincil sunucuda değil, sorgu havuzunda çoğaltmalar üzerinde olup olmadığını anlamak için, **işlem sunucusunun havuzu sorgulama** ayarını **Evet** olarak doğrulayın. Ardından `:rw` , veritabanının mevcut olup olmadığını görmek için niteleyiciyi kullanarak birincil sunucuya bağlanmak üzere SSMS 'yi kullanın. Daha sonra `:rw` aynı veritabanının olup olmadığını görmek için niteleyici olmadan bağlanarak sorgu havuzundaki çoğaltmalara bağlanın. Veritabanı, birincil sunucuda değil, sorgu havuzunda çoğaltmalar üzerinde bulunuyorsa bir eşitleme işlemi çalıştırın.   

* Birincil sunucudaki bir veritabanını yeniden adlandırırken, veritabanının herhangi bir çoğaltmayla düzgün şekilde eşitlendiğinden emin olmak için ek bir adım gereklidir. Yeniden adlandırmadan sonra, eski veritabanı adıyla parametreyi belirten [Sync-Azanalysisservicesınstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) komutunu kullanarak bir eşitleme gerçekleştirin `-Database` . Bu eşitleme, tüm çoğaltmalardan veritabanını ve eski adı taşıyan dosyaları kaldırır. Ardından, `-Database` Yeni veritabanı adıyla parametresini belirten başka bir eşitleme gerçekleştirin. İkinci eşitleme, yeni adlandırılmış veritabanını ikinci dosya kümesine kopyalar ve tüm çoğaltmaları yeniden kapatır. Bu eşitlemeler portalda modeli eşitleme komutu kullanılarak gerçekleştirilemez.

### <a name="synchronization-mode"></a>Eşitleme modu

Varsayılan olarak, sorgu çoğaltmaları artımlı değil tam olarak yeniden boyanır. Yeniden doldurma aşamalarda gerçekleşir. Bunlar, belirli bir zamanda sorgular için en az bir çoğaltmanın çevrimiçi tutulduğundan emin olmak için bir kerede ayrılırlar ve iki kez eklenir (en az üç çoğaltma olduğu varsayılarak). Bazı durumlarda, bu işlem gerçekleşirken istemcilerin çevrimiçi çoğaltmalardan birine yeniden bağlanmasına gerek olabilir. **Replicasyncmode** ayarını kullanarak artık sorgu çoğaltma eşitlemesini paralel olarak belirtebilirsiniz. Paralel eşitleme aşağıdaki avantajları sağlar: 

- Eşitleme zamanında önemli bir düşüş. 
- Eşitleme işlemi sırasında çoğaltmalar genelinde verilerin tutarlı olma olasılığı yüksektir. 
- Veritabanları eşitleme işlemi boyunca tüm çoğaltmalarda çevrimiçi tutulduğundan, istemcilerin yeniden bağlanmasına gerek yoktur. 
- Bellek içi önbellek, modeli tamamen yeniden doldurma özelliğinden daha hızlı olabilen yalnızca değiştirilen verilerle artımlı olarak güncelleştirilir. 

#### <a name="setting-replicasyncmode"></a>ReplicaSyncMode ayarlanıyor

Gelişmiş özelliklerde ReplicaSyncMode ayarlamak için SSMS 'yi kullanın. Olası değerler şunlardır: 

- `1` (varsayılan): aşamalar halinde tam çoğaltma veritabanı yeniden doldurma (artımlı). 
- `2`: İyileştirilmiş eşitleme paralel olarak. 

![RelicaSyncMode ayarı](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

**Replicasyncmode = 2** ayarlanırken, önbelleğin ne kadarının güncelleştirilmesi gerektiğine bağlı olarak, sorgu çoğaltmaları tarafından ek bellek tüketilebilir. Verilerin ne kadarının değiştirildiğine bağlı olarak veritabanını çevrimiçi ve sorgular için kullanılabilir durumda tutmak için, hem eski hem de yeni segmentler aynı anda bellekte tutulduğundan, işlem çoğaltma üzerinde en fazla *bellek* gerektirebilir. Çoğaltma düğümleri birincil düğümle aynı bellek ayrılmasına sahiptir ve yenileme işlemleri için birincil düğümde normal olarak ek bellek vardır; bu nedenle çoğaltmalarda belleğin tükenme olasılığı düşüktür. Ayrıca, yaygın senaryo, veritabanının birincil düğümde artımlı olarak güncelleştirilmesini ve bu nedenle, çift bellek gereksinimi çok seyrek olmalıdır. Eşitleme işleminde bellek yetersiz hatası varsa, varsayılan tekniği (her seferinde iki kez Ekle/ayır) kullanarak yeniden dener. 

### <a name="separate-processing-from-query-pool"></a>İşlemi sorgu havuzundan ayır

Hem işleme hem de sorgu işlemleri için en yüksek performans için, işlem sunucunuzu sorgu havuzundan ayırmanızı tercih edebilirsiniz. Ayrıldığınızda, yeni istemci bağlantıları yalnızca sorgu havuzundaki sorgu çoğaltmalarına atanır. İşlem işlemleri yalnızca kısa bir süre sürüyorsa, işlem sunucunuzu sorgu havuzundan yalnızca işlem ve eşitleme işlemlerini gerçekleştirmek için gereken süre kadar ayırmanızı ve sonra bunu sorgu havuzuna geri eklemeyi seçebilirsiniz. İşlem sunucusu sorgu havuzundan ayrıldığınızda veya yeniden sorgu havuzuna eklemek işlemin tamamlanması beş dakikaya kadar sürebilir.

## <a name="monitor-qpu-usage"></a>QPU kullanımını izleme

Sunucunuz için ölçek genişletme gerekip gerekmediğini öğrenmek için, ölçümleri kullanarak [sunucunuzu Azure Portal izleyin](analysis-services-monitor.md) . QPU 'niz düzenli olarak kullanılıyorsa, modellerinize yönelik sorguların sayısı, planınız için QPU sınırını aşmasıdır. Sorgu havuzu iş kuyruğu uzunluğu ölçümü, sorgu iş parçacığı havuzu sırasındaki sorguların sayısı kullanılabilir QPU 'ları aştığında de artar. 

İzleme için başka bir iyi ölçüm, ServerResourceType tarafından ortalama QPU 'dir. Bu ölçüm, birincil sunucu için Ortalama QPU 'ları sorgu havuzu ile karşılaştırır. 

![Sorgu ölçeği genişletme ölçümleri](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**QPU 'yi ServerResourceType ile yapılandırmak için**

1. Ölçüm çizgisi grafiğinde **ölçüm Ekle**' ye tıklayın. 
2. **Kaynak** bölümünde, sunucunuzu seçin, ardından **ölçüm ad** alanında **Standart ölçümler Analysis Services** seçin, sonra **metrik** bölümünde **qpu**' **yi seçin ve** ardından **toplama**' yı seçin. 
3. **Bölmeyi Uygula**' ya tıklayın. 
4. **Değerler**' de **serverresourcetype**' ı seçin.  

### <a name="detailed-diagnostic-logging"></a>Ayrıntılı tanılama günlüğü

Ölçeği genişletilmiş sunucu kaynaklarının daha ayrıntılı tanılaması için Azure Izleyici günlüklerini kullanın. Günlükler ile, sunucu ve çoğaltmaya göre QPU ve bellek ayırmak için Log Analytics sorguları kullanabilirsiniz. Daha fazla bilgi için bkz. [tanılama günlüğü Analysis Services](analysis-services-logging.md#example-queries)örnek sorgular.


## <a name="configure-scale-out"></a>Ölçek genişletmeyi yapılandırma

### <a name="in-azure-portal"></a>Azure portal

1. Portalda **genişleme**' ya tıklayın. Sorgu çoğaltma sunucusu sayısını seçmek için kaydırıcıyı kullanın. Seçtiğiniz kopyaların sayısı, var olan sunucunuza ek niteliğindedir.  

2. İşleme sunucusunu **sorgulama havuzundan ayrı** olarak, işlem sunucunuzu sorgu sunucularından dışlamak için Evet ' i seçin. Varsayılan [](#connections) bağlantı dizesini (olmadan) kullanan istemci bağlantıları `:rw` , sorgu havuzundaki çoğaltmalara yeniden yönlendirilir. 

   ![Genişleme kaydırıcısı](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Yeni sorgu çoğaltma sunucularınızı sağlamak için **Kaydet** ' e tıklayın. 

Bir sunucu için ölçeği ilk kez yapılandırırken, birincil sunucunuzdaki modeller otomatik olarak sorgu havuzundaki yinelemelerle eşitlenir. Otomatik eşitleme yalnızca bir kez, ölçeği bir veya daha fazla çoğaltma için ilk yapılandırdığınızda oluşur. Aynı sunucudaki çoğaltmalar sayısında yapılan sonraki değişiklikler, *başka bir otomatik eşitleme tetiklemez*. Sunucuyu sıfır çoğaltmalar olarak ayarlayıp daha sonra istediğiniz sayıda çoğaltmaya ölçeklendirseniz bile otomatik eşitleme yeniden gerçekleşmez. 

## <a name="synchronize"></a>Eşitle 

Eşitleme işlemlerinin el ile veya REST API kullanılarak gerçekleştirilmesi gerekir.

### <a name="in-azure-portal"></a>Azure portal

**Genel bakış** > model > **modeli eşitler**.

![Synchronize simgesi](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

**Eşitleme** işlemini kullanın.

#### <a name="synchronize-a-model"></a>Bir modeli eşitler   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Eşitleme durumunu al  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Dönüş durum kodları:


|Kod  |Description  |
|---------|---------|
|-1     |  Geçersiz       |
|0     | Yapan        |
|1     |  Katmanındaki       |
|2     |   Tamamlandı       |
|3     |   Başarısız      |
|4     |    Yüklemesinin     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell 'i kullanmadan önce [en son Azure PowerShell modülünü yükleyip güncelleştirin](/powershell/azure/install-az-ps). 

Eşitleme çalıştırmak için [Sync-Azanalysisservicesınstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)komutunu kullanın.

Sorgu çoğaltmalarının sayısını ayarlamak için [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)kullanın. İsteğe bağlı `-ReadonlyReplicaCount` parametresini belirtin.

İşlem sunucusunu sorgu havuzundan ayırmak için [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)kullanın. Kullanılacak isteğe bağlı `-DefaultConnectionMode` parametreyi belirtin `Readonly` .

Daha fazla bilgi için bkz. [az. AnalysisServices modülüyle hizmet sorumlusu kullanma](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Bağlantılar

Sunucunuzun genel bakış sayfasında iki sunucu adı vardır. Henüz bir sunucu için ölçek genişletme yapılandırmadıysanız, her iki sunucu adı da aynı şekilde çalışır. Bir sunucu için genişleme 'yı yapılandırdıktan sonra, bağlantı türüne bağlı olarak uygun sunucu adını belirtmeniz gerekir. 

Power BI Desktop, Excel ve özel uygulamalar gibi son kullanıcı istemci bağlantıları için **sunucu adı**' nı kullanın. 

PowerShell, Azure Işlev uygulamaları ve AMO 'daki SSMS, Visual Studio ve bağlantı dizeleri için **Yönetim sunucusu adı**' nı kullanın. Yönetim sunucusu adı özel `:rw` (okuma-yazma) niteleyicisi içerir. Tüm işleme işlemleri (birincil) yönetim sunucusunda oluşur.

![Sunucu adları](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Ölçeği artırma, ölçek azaltma ve genişleme karşılaştırması

Birden çok çoğaltmayla bir sunucuda fiyatlandırma katmanını değiştirebilirsiniz. Aynı fiyatlandırma katmanı tüm çoğaltmalar için geçerlidir. Bir ölçeklendirme işlemi önce tüm çoğaltmaları tek seferde getirip yeni fiyatlandırma katmanındaki tüm çoğaltmaları getirir.

## <a name="troubleshoot"></a>Sorun giderme

**Sorun:** Kullanıcılar hata Al ' **' sunucu ' \<Name of the server> ' ' ReadOnly ' bağlantı modunda bulunamıyor.**

**Çözüm:** **Sorgulama havuzu seçeneğinde ayrı işlem sunucusunu** seçerken, varsayılan bağlantı dizesini (olmadan) kullanan istemci bağlantıları, `:rw` sorgu havuzu çoğaltmalarına yeniden yönlendirilir. Eşitleme henüz tamamlanmadığından sorgu havuzundaki çoğaltmalar henüz çevrimiçi değilse, yeniden yönlendirilen istemci bağlantıları başarısız olabilir. Başarısız bağlantıları engellemek için, bir eşitleme gerçekleştirirken sorgu havuzunda en az iki sunucu olmalıdır. Her sunucu ayrı olarak eşitlenir, diğerleri çevrimiçi kalır. İşlem sırasında sorgu havuzunda işleme sunucusuna sahip olmamak isterseniz, işlem için havuzdan kaldırmayı seçebilir ve sonra işlem tamamlandıktan sonra, ancak eşitlemeden önce havuza geri ekleyebilirsiniz. Eşitleme durumunu izlemek için bellek ve QPU ölçümlerini kullanın.



## <a name="related-information"></a>İlgili bilgiler

[Sunucu ölçümlerini izleme](analysis-services-monitor.md)   
[Azure Analysis Services Yönet](analysis-services-manage.md)
