---
title: Azure tanılama günlükleri | Microsoft Docs
description: Müşteri, Azure CDN için günlük analizi etkinleştirebilirsiniz.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: 86696ed6715b4e43a9d02232c013eb64feb61f67
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594201"
---
# <a name="azure-diagnostic-logs"></a>Azure tanılama günlükleri

Azure tanılama günlükleri ile çekirdek analizi görüntüleyebilir ve bunları da dahil olmak üzere bir veya daha fazla hedefleri kaydedin:

 - Azure Storage hesabı
 - Azure Event Hubs
 - [Log Analytics çalışma alanı](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Bu özellik, CDN uç noktalarda tüm fiyatlandırma katmanları için kullanılabilir. 

Azure tanılama günlükleri, özelleştirilmiş bir şekilde kullanma temel kullanım ölçümlerini, CDN uç noktasından çeşitli kaynaklardan dışa olanak tanır. Örneğin, aşağıdaki türde verileri dışarı aktarma yapabilirsiniz:

- Blob depolama, CSV'ye dışarı aktarma ve Excel grafik oluşturmak için verileri dışarı aktarın.
- Event Hubs'a verileri dışarı aktarma ve diğer Azure hizmetlerinden gelen verilerle ilişkilendirin.
- Azure İzleyici günlüklerine verileri dışarı aktarma ve kendi Log Analytics çalışma alanında verileri görüntüleme

Aşağıdaki diyagram tipik CDN çekirdek analiz verilerinin bir görünümünü gösterir.

![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Şekil 1 - CDN çekirdek analiz görünümü*

Tanılama günlükleri hakkında daha fazla bilgi için bkz. [tanılama günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Azure portalı ile günlük kaydını etkinleştirme

Bu adımları etkinleştir CDN çekirdek analiz ile günlüğe kaydetme izleyin:

[Azure Portal](https://portal.azure.com) oturum açın. Akışınız için CDN yoksa zaten etkinleştirdiğiniz varsa [bir Azure CDN profili ve uç noktası oluşturma](cdn-create-new-endpoint.md) devam etmeden önce.

1. Azure portalında gidin **CDN profili**.

2. Azure portalında bir CDN profili için arama yapın veya panonuzdan seçin. Ardından, tanılama günlükleri etkinleştirmek istediğiniz CDN uç noktası seçin.

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Seçin **tanılama günlükleri** izleme bölümünde.

   **Tanılama günlükleri** sayfası görüntülenir.

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Azure depolama ile günlük kaydını etkinleştirme

Günlükleri depolamak için bir depolama hesabı kullanmak için aşağıdaki adımları izleyin:
    
1. İçin **adı**, tanılama günlüğü ayarlarınız için bir ad girin.
 
2. Seçin **bir depolama hesabında arşivle**, ardından **CoreAnalytics**. 

2. İçin **bekletme (gün)** , bekletme gün sayısını seçin. Bekletme günü sayısının sıfır günlükler süresiz olarak depolar. 

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Seçin **depolama hesabı**.

    **Bir depolama hesabı seçin** sayfası görüntülenir.

4. Aşağı açılan listeden bir depolama hesabı seçin ve ardından **Tamam**.

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Tanılama Günlüğü ayarlarınızı yaptıktan sonra seçin **Kaydet**.

### <a name="logging-with-azure-monitor"></a>Azure İzleyici ile günlüğe kaydetme

Azure İzleyici, günlükleri depolamak için kullanmak için aşağıdaki adımları izleyin:

1. Gelen **tanılama günlükleri** sayfasında **Log Analytics'e gönderme**. 

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Seçin **yapılandırma** Azure İzleyici günlüğe kaydetmeyi yapılandırmak için. 

   **Log Analytics çalışma alanları** sayfası görüntülenir.

    >[!NOTE] 
    >OMS çalışma alanları artık Log Analytics çalışma alanları olarak adlandırılır.

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Seçin **yeni çalışma alanı oluşturma**.

    **Log Analytics çalışma alanı** sayfası görüntülenir.

    >[!NOTE] 
    >OMS çalışma alanları artık Log Analytics çalışma alanları olarak adlandırılır.

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/07_Create-new.png)

4. İçin **Log Analytics çalışma alanı**, Log Analytics çalışma alanı adı girin. Log Analytics çalışma alanı adı benzersiz olmalı ve yalnızca harf, rakam ve kısa çizgi içermelidir; boşluk ve alt çizgi izin verilmez. 

5. İçin **abonelik**, aşağı açılan listeden mevcut bir abonelik seçin. 

6. İçin **kaynak grubu**yeni bir kaynak grubu oluşturun veya varolan bir tanesini seçin.

7. İçin **konumu**, listeden bir konum seçin.

8. Seçin **panoya Sabitle** panonuza günlük yapılandırmasını kaydetmek istiyorsanız. 

9. Seçin **Tamam** yapılandırmasını tamamlamak için.

10. Çalışma alanınızı oluşturduktan sonra için dönersiniz **tanılama günlükleri** sayfası. Yeni bir Log Analytics çalışma alanınızın adını onaylayın.

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Seçin **CoreAnalytics**, ardından **Kaydet**.

12. Yeni bir Log Analytics çalışma alanı görüntülemek için seçin **çekirdek analizi** CDN uç noktası sayfanızdan.

    ![Portalı - tanılama günlükleri](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Log Analytics çalışma alanınız artık verileri günlüğe kaydetmek hazırdır. Bu verileri kullanmak kullanmalısınız bir [Azure İzleyici günlükleri çözümü](#consuming-diagnostics-logs-from-a-log-analytics-workspace), bu makalenin devamındaki kapsanan.

Günlük veri gecikmeler hakkında daha fazla bilgi için bkz. [oturum verileri gecikmeleri](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>PowerShell ile günlüğe kaydetmeyi etkinleştirme

Aşağıdaki örnek, Azure PowerShell cmdlet'leri aracılığıyla tanılama günlüklerini etkinleştirme gösterilmektedir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Bir depolama hesabında oturum tanılama etkinleştirme

1. Oturum açın ve bir abonelik seçin:

    Connect AzAccount 

    Select-AzureSubscription - Subscriptionıd 

2. Tanılama günlükleri bir depolama hesabında etkinleştirmek için şu komutu girin:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Bir Log Analytics çalışma alanında tanılama günlüklerini etkinleştirmek için şu komutu girin:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Azure depolama biriminden tanılama günlüklerini kullanma
Bu bölümde CDN çekirdek analiz şemasını açıklar nasıl bir Azure depolama hesabı içinde düzenlenir ve bir CSV dosyasında günlükleri indirmek için örnek kod sağlar.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Depolama Gezgini'ni kullanma
Temel analiz verileri bir Azure depolama hesabından erişebilmeniz için önce bir depolama hesabı içeriğine erişmek için bir aracı ilk gerekir. Varken çeşitli araçlar kullanılabilir pazarında, Microsoft Azure Depolama Gezgini önerdiğimiz bir bileşendir. Aracı indirmek için bkz: [Azure Depolama Gezgini](https://storageexplorer.com/). İndirme ve yazılım yükleme sonrasında, CDN tanılama günlükleri için hedef olarak yapılandırılan aynı Azure depolama hesabını kullanacak şekilde yapılandırın.

1.  Açık **Microsoft Azure Depolama Gezgini**
2.  Depolama hesabını bulun
3.  Genişletin **Blob kapsayıcıları** düğümünde bu depolama hesabı.
4.  Adlı kapsayıcıyı seçin *ınsights günlükleri coreanalytics*.
5.  Sağ bölmede, olarak ilk düzeyi ile başlayan show sonuçları *ResourceId =* . Dosyayı bulana kadar her düzeyde seçmeye devam *PT1H.json*. Yolun bir açıklaması için bkz: [Blob yol biçimi](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Her blob *PT1H.json* dosya bir saatlik belirli bir CDN uç noktası veya kendi özel etki alanı analizi günlüklerinde gösterir.
7.  Bu JSON dosyasının içeriği şemasını çekirdek analizi günlüklerinde bölüm şemasında açıklanmıştır.


#### <a name="blob-path-format"></a>BLOB yol biçimi

Çekirdek analizi günlüklerinde saatte oluşturulur ve veriler toplanır ve tek bir Azure blob iç bir JSON yükü olarak depolanır. Depolama Gezgini aracını Yorumlar '/' dizin ayırıcı ve programları hiyerarşi, Azure blob yolu hiyerarşik bir yapısı olduğu kabul görünür olduğundan ve blob adını temsil eder. Blob adı aşağıdaki adlandırma kuralını izler:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Alan açıklaması:**

|Value|Açıklama|
|-------|---------|
|Abonelik Kimliği    |GUID biçiminde Azure abonelik kimliği.|
|Kaynak grubu adı |CDN kaynakları ait olduğu kaynak grubunun adı.|
|Profil Adı |CDN profilinin adı|
|Uç nokta adı |CDN uç noktası adı|
|Yıl|  Örneğin, 2017 yılı dört basamaklı temsili|
|Ay| İki haneli ay sayısı gösterimi. 01 Ocak =... 12 Aralık =|
|Gün|   Ayın gününü iki basamaklı temsili|
|PT1H.json| Analiz verilerinin nerede depolanacağını gerçek bir JSON dosyası|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Temel analiz verileri bir CSV dosyasına aktarılıyor.

Çekirdek analizi erişimi kolaylaştırmak için bir aracı için örnek kod sağlanır. Bu aracı, grafik veya diğer toplamaları oluşturmak için kullanılabilecek bir virgülle ayrılmış düz dosya biçimine JSON dosyaları indirme sağlar.

Aracı'nı nasıl kullanabileceğinizi aşağıda verilmiştir:

1.  GitHub bağlantıyı ziyaret edin: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Kodu indirin.
3.  Derleme ve yapılandırma yönergelerini izleyin.
4.  Aracı çalıştırın.
5.  Elde edilen CSV dosyası, basit düz bir hiyerarşide analiz verilerini gösterir.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Tanılama günlükleri Log Analytics çalışma alanını kullanma
Azure İzleyici, bulut izler ve şirket içi Ortamlarınızdaki kullanılabilirliği ve performansı korumak için bir Azure hizmetidir. Birden fazla kaynak arasında analiz sağlamak üzere bulut ve şirket içi ortamlarınızdaki kaynaklar ile diğer izleme araçları tarafından oluşturulan verileri toplar. 

Azure İzleyicisi'ni kullanmak için şunları yapmanız gerekir [günlüğe kaydetmeyi etkinleştirme](#enable-logging-with-azure-storage) Azure Log Analytics çalışma alanına, bu makalenin önceki bölümlerinde alınmıştır.

### <a name="using-the-log-analytics-workspace"></a>Log Analytics çalışma alanını kullanma

 Aşağıdaki diyagramda, girişleri mimarisi ve deponun çıktıları gösterir:

![Log Analytics çalışma alanı](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Şekil 3 - Log Analytics Deposu'na*

Yönetim çözümleri kullanarak çeşitli yollarla verileri görüntüleyebilirsiniz. Yönetim çözümleri elde edebilirsiniz [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

İzleme çözümleri seçerek Azure Market'ten yükleyebilirsiniz **şimdi edinin** her çözüm alt kısmındaki bağlantı.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Azure İzleyici izleme çözümü CDN ekleme

Bir Azure İzleyici ile izleme çözümü eklemek için aşağıdaki adımları izleyin:

1.   Azure aboneliğinizi kullanarak Azure portalında oturum açın ve panonuza gidin.
    ![Azure Panosu](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. İçinde **yeni** sayfasındaki **Market**seçin **izleme + Yönetim**.

    ![Market](./media/cdn-diagnostics-log/14_Marketplace.png)

3. İçinde **izleme + Yönetim** sayfasında **tümünü gör**.

    ![Tümünü incele](./media/cdn-diagnostics-log/15_See-all.png)

4. CDN arama kutusuna arayın.

    ![Tümünü incele](./media/cdn-diagnostics-log/16_Search-for.png)

5. Seçin **Azure CDN çekirdek analiz**. 

    ![Tümünü incele](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Seçtikten sonra **Oluştur**, yeni bir Log Analytics çalışma alanı oluşturun veya mevcut bir istenir. 

    ![Tümünü incele](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Önce oluşturulan çalışma alanını seçin. Ardından bir Otomasyon hesabı eklemeniz gerekir.

    ![Tümünü incele](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Aşağıdaki ekranda doldurmak zorunda Otomasyon hesabı formunu gösterir. 

    ![Tümünü incele](./media/cdn-diagnostics-log/20_Automation.png)

9. Otomasyon hesabı oluşturduktan sonra çözümünüze eklemek hazır olursunuz. **Oluştur** düğmesini seçin.

    ![Tümünü incele](./media/cdn-diagnostics-log/21_Ready.png)

10. Çözümünüzün artık çalışma alanınıza eklenmiş. Azure portalı panonuza geri dönün.

    ![Tümünü incele](./media/cdn-diagnostics-log/22_Dashboard.png)

    Çalışma alanınıza dönmek için oluşturulan bir Log Analytics çalışma alanı seçin. 

11. Seçin **OMS portalında** yeni çözümünüzü görmek için kutucuğu.

    ![Tümünü incele](./media/cdn-diagnostics-log/23_workspace.png)

12. Portalınızı, aşağıdaki ekrana görünmelidir:

    ![Tümünü incele](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Verilerinizi birkaç görünüm görmek için kutucuklar birini seçin.

    ![Tümünü incele](./media/cdn-diagnostics-log/25_Interior-view.png)

    Tek bir görünüm verilerini temsil eden başka kutucuklar görmek için sağa veya sola kaydırma yapabilirsiniz. 

    Verileriniz hakkında daha fazla ayrıntı için kutucuklar birini seçin.

     ![Tümünü incele](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Teklifleri ve fiyatlandırma katmanları

Teklifleri ve fiyatlandırma katmanları yönetimi çözümleri için gördüğünüz [burada](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Görünümlerini özelleştirme

Verilerinizi kullanarak görünümünü özelleştirebilirsiniz **Görünüm Tasarımcısı**. Tasarlamaya için Log Analytics çalışma alanınıza gidin ve seçin **Görünüm Tasarımcısı** Döşe.

![Görünüm Tasarımcısı](./media/cdn-diagnostics-log/27_Designer.png)

Sürükle ve bırak grafikler ve veri doldurma türleri, ayrıntılı analiz etmek istersiniz.

![Görünüm Tasarımcısı](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Günlük veri gecikmeleri

Aşağıdaki tabloda günlük veri gecikme gösterir **Azure CDN standart Microsoft gelen**, **akamai'den Azure CDN standart**, ve **Azure CDN standart/Premium verizon'dan**.

Microsoft günlük veri gecikmeleri | Verizon günlük veri gecikmeleri | Akamai günlük veri gecikmeleri
--- | --- | ---
1 saatlik olarak ertelendi. | 1 saatlik olarak Gecikmeli ve uç nokta yayma işlemi tamamlandıktan sonra görünen başlatmak için 2 saat sürebilir. | 24 saat Gecikmeli; 24 saatten daha önce oluşturulmuş olsa bile, görünen başlatmak için 2 saat sürer. Kısa bir süre önce oluşturulduysa, görünen başlatmak günlükleri 25 saate kadar sürebilir.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN çekirdek analiz türlerinde Tanılama Günlüğü

Microsoft şu anda CDN POP/kenarlardan HTTP yanıtı istatistiklerinin ve çıkış istatistikleri görüldüğü gibi gösteren ölçümleri içeren çekirdek analizi günlüklerinde yalnızca sunar.

### <a name="core-analytics-metrics-details"></a>Çekirdek analizi ölçüm ayrıntıları
Aşağıdaki tablo, analiz günlükleri için çekirdek kullanılabilir ölçümlerin bir listesini gösterir **Azure CDN standart Microsoft gelen**, **akamai'den Azure CDN standart**, ve **Azure CDN standart/Premium verizon'dan**. Bu farklara minimal olsa da, tüm ölçümler tüm sağlayıcılardan kullanılabilir. Tablo, belirli bir metrik bir sağlayıcıdan kullanılabilir olup olmadığını da görüntüler. Ölçümler, trafik bunlara sahip CDN uç için kullanılabilir.


|Ölçüm                     | Açıklama | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | İstek İsabeti bu süre boyunca toplam sayısı. | Evet | Evet |Evet |
| RequestCountHttpStatus2xx | 2xx HTTP kodu (örneğin, 200, 202) sonuçlanan tüm isteklerinin sayısı. | Evet | Evet |Evet |
| RequestCountHttpStatus3xx | 3xx HTTP kodu (örneğin, 300, 302) sonuçlanan tüm isteklerinin sayısı. | Evet | Evet |Evet |
| RequestCountHttpStatus4xx | 4xx HTTP kodu (örneğin, 400, 404) sonuçlanan tüm isteklerinin sayısı. | Evet | Evet |Evet |
| RequestCountHttpStatus5xx | 5xx HTTP kodu (örneğin, 500, 504) sonuçlanan tüm isteklerinin sayısı. | Evet | Evet |Evet |
| RequestCountHttpStatusOthers | Diğer tüm HTTP kodları (dışında 2xx 5xx) sayısı. | Evet | Evet |Evet |
| RequestCountHttpStatus200 | 200 HTTP kodu yanıtıyla sonuçlanan tüm isteklerin sayısı. | Evet | Hayır  |Evet |
| RequestCountHttpStatus206 | 206 HTTP kodu yanıtıyla sonuçlanan tüm isteklerin sayısı. | Evet | Hayır  |Evet |
| RequestCountHttpStatus302 | Bir 302 HTTP kodu yanıtıyla sonuçlanan tüm isteklerin sayısı. | Evet | Hayır  |Evet |
| RequestCountHttpStatus304 | 304 HTTP kodu yanıtıyla sonuçlanan tüm isteklerin sayısı. | Evet | Hayır  |Evet |
| RequestCountHttpStatus404 | 404 HTTP kodu yanıtıyla sonuçlanan tüm isteklerin sayısı. | Evet | Hayır  |Evet |
| RequestCountCacheHit | İsabet önbellekte sonuçlanan tüm isteklerin sayısı. Varlık doğrudan POP istemciye sunulduğu. | Evet | Evet | Hayır  |
| RequestCountCacheMiss | Önbellek isabetsizliği sonuçlanan tüm isteklerinin sayısı. Önbellek isabetsizliği varlık istemcinin en yakın POP bulunamadı ve bu nedenle kaynaktan alınan anlamına gelir. | Evet | Evet | Hayır |
| RequestCountCacheNoCache | Bir varlık için edge üzerinde bir kullanıcı yapılandırması nedeniyle önbelleğe alınmasını engellenir tüm isteklerin sayısı. | Evet | Evet | Hayır |
| RequestCountCacheUncacheable | Varlığın Cache-Control ve onu POP veya HTTP istemcisi tarafından önbelleğe alınması gerektiğini değil olduğunu belirtmek Expires üst bilgileri tarafından önbelleğe alınmasını engellenir varlıklar için tüm istekleri sayısı. | Evet | Evet | Hayır |
| RequestCountCacheOthers | Önbellek durumu tarafından yukarıda bahsedilmeyen sahip tüm isteklerin sayısı. | Hayır | Evet | Hayır  |
| EgressTotal | Giden veri aktarımı GB | Evet |Evet |Evet |
| EgressHttpStatus2xx | Giden veri aktarımı * GB 2xx HTTP durum kodları içeren yanıtlar için. | Evet | Evet | Hayır  |
| EgressHttpStatus3xx | GB cinsinden 3xx HTTP durum kodları içeren yanıtlar için giden veri aktarımı. | Evet | Evet | Hayır  |
| EgressHttpStatus4xx | GB cinsinden 4xx HTTP durum kodları içeren yanıtlar için giden veri aktarımı. | Evet | Evet | Hayır  |
| EgressHttpStatus5xx | GB cinsinden 5xx HTTP durum kodları içeren yanıtlar için giden veri aktarımı. | Evet | Evet | Hayır |
| EgressHttpStatusOthers | Diğer HTTP durum kodları GB içeren yanıtlar için giden veri aktarımı. | Evet | Evet | Hayır  |
| EgressCacheHit | Giden veri teslim edilen yanıtlar için CDN POP/kenarlardan doğrudan CDN önbelleğinden aktarma. | Evet | Evet | Hayır |
| EgressCacheMiss. | Değil en yakın POP sunucusunda bulunan ve kaynak sunucudan alınan yanıtları için giden veri aktarımı. | Evet | Evet | Hayır |
| EgressCacheNoCache | Edge üzerinde bir kullanıcı yapılandırması nedeniyle önbelleğe alınmasını engellenir varlıklar için giden veri aktarımı. | Evet | Evet | Hayır |
| EgressCacheUncacheable | Varlığın Cache-Control ve/veya Expires üst bilgileri tarafından önbelleğe alınmasını engellenir varlıklar için giden veri aktarımı. Bunu POP veya HTTP istemcisi tarafından önbelleğe alınması gerektiğini değil olduğunu gösterir. | Evet | Evet | Hayır |
| EgressCacheOthers | Diğer önbellek senaryolar için giden veri aktarımları. | Hayır | Evet | Hayır |

\* Giden veri aktarımı, CDN POP sunucudan istemciye trafiği ifade eder.


### <a name="schema-of-the-core-analytics-logs"></a>Çekirdek analizi günlüklerinde şeması 

Tüm günlükler, JSON biçiminde depolanır ve her bir giriş alanlarına göre aşağıdaki şema vardır:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Burada *zaman* istatistikleri raporlanır kendisi için saatlik sınırın başlangıç zamanı temsil eder. Bir ölçüm çift veya bir tamsayı değeri yerine bir CDN sağlayıcısı tarafından desteklenmediği durumlarda bir null değer yoktur. Bu null değer olmaması bir ölçüm gösterir ve 0 değerinden farklı. Bu ölçümler uç noktasında yapılandırılmış etki alanı başına bir dizi yoktur.

Örnek özellikleri:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure tanılama günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Azure CDN ek portal aracılığıyla temel analiz](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure İzleyici günlükleri](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







