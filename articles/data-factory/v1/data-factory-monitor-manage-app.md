---
title: Veri işlem hatlarını izleme ve yönetme-Azure
description: Izleme ve yönetim uygulamasını kullanarak Azure veri fabrikalarını ve işlem hatlarını izleme ve yönetme hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: de56ba1281d0f20c8be838fa1bc9ebc24905b26c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666936"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Izleme ve yönetim uygulamasını kullanarak Azure Data Factory işlem hatlarını izleme ve yönetme
> [!div class="op_single_selector"]
> * [Azure portal/Azure PowerShell kullanma](data-factory-monitor-manage-pipelines.md)
> * [Izleme ve yönetim uygulaması kullanma](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory işlem hatlarını izleme ve yönetme](../monitor-visually.md).

Bu makalede, Data Factory işlem hatlarınızı izlemek, yönetmek ve hatalarını ayıklamak için Izleme ve yönetim uygulamasının nasıl kullanılacağı açıklanır. Aşağıdaki videoyu izleyerek uygulamayı kullanmaya başlamanızı sağlayabilirsiniz:

> [!NOTE]
> Videoda gösterilen kullanıcı arabirimi, portalda gördiklerinize tam olarak uymayabilir. Biraz daha eski olsa da kavramlar aynı kalır. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Izleme ve yönetim uygulamasını başlatma
Izleyici ve yönetim uygulamasını başlatmak için veri fabrikanızın **Data Factory** dikey penceresinde **izleyici & Yönet** kutucuğuna tıklayın.

![Data Factory giriş sayfasında kutucuk izleme](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Izleme ve yönetim uygulamasının ayrı bir pencerede açık olduğunu görmeniz gerekir.  

![İzleme ve Yönetim uygulaması](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Web tarayıcısının "yetkilendiriliyor..." konumunda takılı olduğunu görürseniz, **üçüncü taraf tanımlama bilgilerini ve site verilerini engelle** onay kutusunu temizleyin veya seçili durumda tutun, **login.microsoftonline.com**için bir özel durum oluşturun ve ardından uygulamayı yeniden açmayı deneyin.


Orta bölmedeki etkinlik pencereleri listesinde her bir etkinliğin çalışması için bir etkinlik penceresi görürsünüz. Örneğin, etkinlik beş saat boyunca saatte bir çalışacak şekilde zamanlanırsa, beş veri dilimiyle ilişkili beş etkinlik penceresi görürsünüz. Alttaki listede etkinlik pencerelerini görmüyorsanız şunları yapın:
 
- En üstteki **Başlangıç** ve **bitiş zamanı** filtrelerini, işlem hattının başlangıç ve bitiş zamanları ile eşleşecek şekilde güncelleştirin ve ardından **Uygula** düğmesine tıklayın.  
- Etkinlik Windows listesi otomatik olarak yenilenmez. **Etkinlik pencereleri** listesindeki araç çubuğunda **Yenile** düğmesine tıklayın.  

Bu adımları test etmek için bir Data Factory uygulamanız yoksa, [Data Factory kullanarak blob DEPOLAMADAN SQL veritabanına veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)öğreticisini yapın.

## <a name="understand-the-monitoring-and-management-app"></a>Izleme ve yönetim uygulamasını anlama
Sol tarafta üç sekme bulunur: **Kaynak Gezgini**, **Izleme görünümleri**ve **Uyarılar**. İlk sekme (**Kaynak Gezgini**) varsayılan olarak seçilidir.

### <a name="resource-explorer"></a>Kaynak Gezgini
Şunları görürsünüz:

* Sol bölmedeki Kaynak Gezgini **ağaç görünümü** .
* Orta bölmedeki üst kısımdaki **Diyagram görünümü** .
* Orta bölmedeki alt kısımdaki **etkinlik pencereleri** listesi.
* Sağ bölmedeki **Özellikler**, **etkinlik penceresi Gezgini**ve **betik** sekmeleri.

Kaynak Gezgini, bir ağaç görünümünde veri fabrikasındaki tüm kaynakları (işlem hatları, veri kümeleri, bağlı hizmetler) görürsünüz. Kaynak Gezgini bir nesne seçtiğinizde:

* İlişkili Data Factory varlığı Diyagram görünümünde vurgulanır.
* [İlişkili etkinlik pencereleri](data-factory-scheduling-and-execution.md) , alt kısımdaki etkinlik pencereleri listesinde vurgulanır.  
* Seçili nesnenin özellikleri sağ bölmedeki Özellikler penceresi gösterilir.
* Varsa, seçili nesnenin JSON tanımı gösterilir. Örneğin: bağlı bir hizmet, veri kümesi veya işlem hattı.

![Kaynak Gezgini](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Etkinlik pencereleri hakkında ayrıntılı kavramsal bilgi için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md) makalesi.

### <a name="diagram-view"></a>Diyagram Görünümü
Bir veri fabrikasının Diyagram görünümü bir veri fabrikasını ve varlıklarını izlemek ve yönetmek için tek bir cam bölmesi sağlar. Diyagram görünümünde bir Data Factory varlığı (veri kümesi/işlem hattı) seçtiğinizde:

* Data Factory varlığı ağaç görünümünde seçilir.
* İlişkili etkinlik pencereleri, etkinlik pencereleri listesinde vurgulanır.
* Seçili nesnenin özellikleri Özellikler penceresi gösterilmiştir.

İşlem hattı etkin olduğunda (duraklatılmış durumda değil) yeşil bir satırla gösterilir:

![İşlem hattı çalışıyor](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Bir işlem hattını Diyagram görünümünde seçerek ve Komut çubuğundaki düğmeleri kullanarak duraklatabilir, sürdürebilir veya sonlandırabilirsiniz.

![Komut çubuğunda Duraklat/devam edin](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Diyagram görünümünde işlem hattı için üç komut çubuğu düğmesi vardır. Ardışık düzeni duraklatmak için ikinci düğmeyi kullanabilirsiniz. Duraklatma, çalışmakta olan etkinlikleri sonlandırır ve tamamlanmasına devam etmenizi sağlar. Üçüncü düğme, işlem hattını duraklatır ve var olan çalışan etkinliklerini sonlandırır. İlk düğme ardışık düzeni sürdürür. Ardışık düzen duraklatıldığında, işlem hattının rengi değişir. Örneğin, duraklatılmış bir işlem hattı aşağıdaki görüntüde olduğu gibi görünür: 

![İşlem hattı duraklatıldı](./media/data-factory-monitor-manage-app/PipelinePaused.png)

CTRL tuşunu kullanarak iki veya daha fazla işlem hattı çoklu seçim yapabilirsiniz. Tek seferde birden çok işlem hattını duraklatmak/devam etmek için komut çubuğu düğmelerini kullanabilirsiniz.

Ayrıca bir işlem hattına sağ tıklayıp, bir işlem hattını askıya alma, işleme veya sonlandırma seçeneklerini belirleyebilirsiniz. 

![İşlem hattı için bağlam menüsü](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

İşlem hattındaki tüm etkinlikleri görmek için **açık işlem hattı** seçeneğine tıklayın. 

![İşlem hattı menüsünü açma](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Açık ardışık düzen görünümünde, işlem hattındaki tüm etkinlikleri görürsünüz. Bu örnekte yalnızca bir etkinlik vardır: kopyalama etkinliği. 

![Açık işlem hattı](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Önceki görünüme geri dönmek için en üstteki içerik haritası menüsünde Veri Fabrikası adına tıklayın.

İşlem hattı görünümünde, bir çıktı veri kümesi seçtiğinizde veya farenizi çıktı veri kümesinin üzerine getirdiğinizde, bu veri kümesi için etkinlik Windows açılır penceresini görürsünüz.

![Etkinlik Windows açılır penceresi](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Sağ bölmedeki **Özellikler** penceresinde, ayrıntılarını görmek için bir etkinlik penceresine tıklayabilirsiniz.

![Etkinlik penceresi özellikleri](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

Daha fazla ayrıntı görmek için sağ bölmede **etkinlik penceresi Gezgini** sekmesine geçin.

![Etkinlik penceresi Gezgini](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Ayrıca, **denemeler** bölümündeki bir etkinliğin her bir çalıştırma denemesi için **çözümlenen değişkenleri** görürsünüz.

![Çözümlenen değişkenler](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Seçili nesne için JSON betik tanımını görmek üzere **komut dosyası** sekmesine geçin.   

![Betik sekmesi](./media/data-factory-monitor-manage-app/ScriptTab.png)

Etkinlik pencerelerini üç yerde görebilirsiniz:

* Diyagram görünümünde etkinlik penceresi açılır penceresi (orta bölme).
* Sağ bölmedeki etkinlik penceresi Gezgini.
* Alt bölmedeki etkinlik pencereleri listesi.

Etkinlik Windows açılır ve etkinlik penceresi Gezgini ' nde sol ve sağ okları kullanarak önceki haftaya ve sonraki haftaya kaydırabilirsiniz.

![Etkinlik penceresi Gezgini sol/sağ ok](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Diyagram görünümünün en altında şu düğmeleri görürsünüz: Yakınlaştır, uzaklaştır, sığacak kadar Yakınlaştır, Yakınlaştır %100, kilit düzeni. **Düzen kilitle** düğmesi, Diyagram görünümünde tabloları ve işlem hatlarını yanlışlıkla taşımayı önler. Varsayılan olarak açık olur. Diyagram içinde devre dışı bırakabilirsiniz ve varlıkları taşıyabilirsiniz. Devre dışı bırakıldığında, son düğmesini kullanarak tabloları ve işlem hatlarını otomatik olarak konumlandırabilirsiniz. Ayrıca fare tekerleğini kullanarak da yakınlaştırıp uzaklaştırabilirsiniz.

![Diyagram Görünümü Yakınlaştırma komutları](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Etkinlik Pencereleri listesi
Orta bölmenin altındaki etkinlik Windows listesi, Kaynak Gezgini veya Diyagram görünümünde seçtiğiniz veri kümesi için tüm etkinlik pencerelerini görüntüler. Varsayılan olarak, liste azalan sırada ve en üstteki etkinlik penceresini görebileceğiniz anlamına gelir.

![Etkinlik Pencereleri listesi](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Bu liste otomatik olarak yenilenmez, bu nedenle el ile yenilemek için araç çubuğundaki Yenile düğmesini kullanın.  

Etkinlik pencereleri aşağıdaki durumlardan birinde olabilir:

<table>
<tr>
    <th align="left">Durum</th><th align="left">Dosya</th><th align="left">Açıklama</th>
</tr>
<tr>
    <td rowspan="8">Bekleniyor</td><td>ScheduleTime</td><td>Etkinlik penceresinin çalışması için geçen süre.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Yukarı akış bağımlılıkları için hazırlanma.</td>
</tr>
<tr>
<td>Bilgisayar ile kaynaklar</td><td>İşlem kaynakları kullanılamıyor.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Tüm etkinlik örnekleri diğer etkinlik pencerelerini çalıştırmakla meşgul.</td>
</tr>
<tr>
<td>Activityözgeçmişi</td><td>Etkinlik duraklatılır ve devam edene kadar etkinlik pencerelerini çalıştıramıyorum.</td>
</tr>
<tr>
<td>Yeniden Dene</td><td>Etkinlik yürütmesi yeniden deneniyor.</td>
</tr>
<tr>
<td>Doğrulama</td><td>Doğrulama henüz başlatılmadı.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Doğrulama yeniden denenmek üzere bekliyor.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Ediyor</td><td>Doğrulamada</td><td>Doğrulama devam ediyor.</td>
</tr>
<td>-</td>
<td>Etkinlik penceresi işleniyor.</td>
</tr>
<tr>
<td rowspan="4">Başarısız</td><td>Zaman aşımına uğradı</td><td>Etkinlik yürütmesi etkinliğin izin verilenden daha uzun sürdü.</td>
</tr>
<tr>
<td>İptal edildi</td><td>Etkinlik penceresi Kullanıcı eylemi tarafından iptal edildi.</td>
</tr>
<tr>
<td>Doğrulama</td><td>Doğrulama başarısız oldu.</td>
</tr>
<tr>
<td>-</td><td>Etkinlik penceresi oluşturulamadı veya doğrulanamadı.</td>
</tr>
<td>Hazır</td><td>-</td><td>Etkinlik penceresi tüketimine hazırlanıyor.</td>
</tr>
<tr>
<td>Atlanmış</td><td>-</td><td>Etkinlik penceresi işlenmedi.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Farklı bir durumla mevcut olan, ancak sıfırlanan bir etkinlik penceresi.</td>
</tr>
</table>


Listedeki bir etkinlik penceresine tıkladığınızda, **etkinlik Windows Gezgini** 'nde veya sağdaki **Özellikler** penceresinde onunla ilgili ayrıntıları görürsünüz.

![Etkinlik penceresi Gezgini](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Etkinlik pencerelerini Yenile
Ayrıntılar otomatik olarak yenilenmez. bu nedenle, etkinlik Windows listesini el ile yenilemek için Komut çubuğundaki Yenile düğmesini (ikinci düğme) kullanın.  

### <a name="properties-window"></a>Özellikler penceresi
Özellikler penceresi, Izleme ve yönetim uygulamasının en sağ bölmesidir.

![Özellikler penceresi](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Kaynak Gezgini (ağaç görünümü), Diyagram görünümü veya etkinlik pencereleri listesinde seçtiğiniz öğenin özelliklerini görüntüler.

### <a name="activity-window-explorer"></a>Etkinlik penceresi Gezgini
**Etkinlik penceresi gezgin** penceresi, Izleme ve yönetim uygulamasının en sağ bölmesinde bulunur. Etkinlik pencereleri açılır penceresinde veya etkinlik pencereleri listesinde seçtiğiniz etkinlik penceresi hakkındaki ayrıntıları görüntüler.

![Etkinlik penceresi Gezgini](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Üstteki Takvim görünümünde tıklayarak başka bir etkinlik penceresine geçebilirsiniz. Önceki haftadan veya sonraki haftadan etkinlik pencerelerini görmek için üstteki sol ok/sağ ok düğmelerini de kullanabilirsiniz.

Etkinlik penceresini yeniden çalıştırmak veya bölmedeki ayrıntıları yenilemek için alt bölmedeki araç çubuğu düğmelerini kullanabilirsiniz.

### <a name="script"></a>Betik
Seçili Data Factory varlığının (bağlı hizmet, veri kümesi veya işlem hattı) JSON tanımını görüntülemek için **komut dosyası** sekmesini kullanabilirsiniz.

![Betik sekmesi](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Sistem Görünümlerini kullanma
Izleme ve yönetim uygulaması, verileriniz için en son/başarısız/devam eden etkinlik pencerelerini görüntülemenize olanak tanıyan önceden oluşturulmuş sistem görünümlerini (**son etkinlik pencereleri**, **başarısız etkinlik pencereleri**, **devam eden etkinlik**pencereleri) içerir Çar.

Sol taraftaki **Izleme görünümleri** sekmesine tıklayarak bu sekmeye geçin.

![İzleme görünümleri sekmesi](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Şu anda desteklenen üç sistem görünümü vardır. Etkinlik Windows listesindeki son etkinlik pencerelerini, başarısız etkinlik pencerelerini veya sürmekte olan etkinlik pencerelerini görmek için bir seçenek belirleyin (orta bölmenin altında).

Son **etkinlik Windows** seçeneğini belirlediğinizde, **son deneme süresi**için tüm son etkinlik pencerelerini azalan sırada görürsünüz.

**Başarısız etkinlik Windows** görünümünü listedeki tüm başarısız etkinlik pencerelerini görmek için kullanabilirsiniz. **Özellikler** penceresinde veya **etkinlik penceresi Gezgini**' nde onunla ilgili ayrıntıları görmek için listede başarısız bir etkinlik penceresi seçin. Ayrıca, başarısız olan etkinlik penceresi için tüm günlükleri indirebilirsiniz.

## <a name="sort-and-filter-activity-windows"></a>Etkinlik pencerelerini Sırala ve filtrele
Etkinlik pencerelerini filtrelemek için Komut çubuğundaki **Başlangıç saati** ve **bitiş saati** ayarlarını değiştirin. Başlangıç saatini ve bitiş saatini değiştirdikten sonra, etkinlik pencereleri listesini yenilemek için bitiş saatinin yanındaki düğmeye tıklayın.

![Başlangıç ve bitiş zamanları](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Şu anda, her zaman Izleme ve yönetim uygulamasında UTC biçimindedir.
>
>

**Etkinlik pencereleri listesinde**bir sütunun adına tıklayın (örneğin: durum).

![Etkinlik Windows listesi sütun menüsü](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Şunları yapabilirsiniz:

* Artan sırada sıralayın.
* Azalan sırada sıralayın.
* Bir veya daha fazla değere göre filtreleyin (Ready, bekliyor, vb.).

Bir sütunda filtre belirttiğinizde, bu sütun için etkin filtre düğmesini görürsünüz. Bu, sütundaki değerlerin filtrelenmiş değerler olduğunu gösterir.

![Etkinlik Windows listesinin bir sütununda filtrele](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Filtreleri temizlemek için aynı açılır pencereyi kullanabilirsiniz. Etkinlik pencereleri listesinin tüm filtrelerini temizlemek için Komut çubuğundaki Filtreyi temizle düğmesine tıklayın.

![Etkinlik Windows listesi için tüm filtreleri temizle](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Batch eylemleri gerçekleştirme
### <a name="rerun-selected-activity-windows"></a>Seçili etkinlik pencerelerini yeniden çalıştır
Bir etkinlik penceresi seçin, ilk komut çubuğu düğmesine ait aşağı oka tıklayın ve **yeniden çalıştır** / **ardışık düzende yukarı akış ile yeniden çalıştır**' ı seçin. **Ardışık düzen seçeneğinde yukarı akış Ile yeniden çalıştır** seçeneğini belirlediğinizde, tüm yukarı akış etkinlik pencerelerini de yeniden çalıştırır.
    Etkinlik penceresini yeniden çalıştırmak ![](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Ayrıca, listede birden çok etkinlik penceresi seçebilir ve bunları aynı anda yeniden çalıştırabilirsiniz. Etkinlik pencerelerini duruma göre filtrelemek isteyebilirsiniz (örneğin: **başarısız**)--ve sonra etkinlik pencerelerinin başarısız olmasına neden olan sorunu düzelttikten sonra başarısız etkinlik pencerelerini yeniden çalıştırın. Listedeki etkinlik pencerelerini filtreleme hakkındaki ayrıntılar için aşağıdaki bölüme bakın.  

### <a name="pauseresume-multiple-pipelines"></a>Çoklu işlem hatlarını duraklatma/devam ettirme
CTRL tuşunu kullanarak iki veya daha fazla işlem hattı ekleyebilirsiniz. Bunları duraklatmak/devam etmek için komut çubuğu düğmelerini (aşağıdaki görüntüde kırmızı dikdörtgende vurgulanır) kullanabilirsiniz.

![Komut çubuğunda Duraklat/devam edin](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
