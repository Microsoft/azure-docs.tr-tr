---
title: SharePoint için StorSimple bağdaştırıcısı | Microsoft Docs
description: Yükleme ve yapılandırma veya bir SharePoint sunucu grubundaki SharePoint için StorSimple bağdaştırıcısı kaldırma işlemini açıklamaktadır.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: a2f8e75578e396085e7d80f43c1180e158967061
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60633428"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Yükleme ve SharePoint için StorSimple bağdaştırıcısını yapılandırın
## <a name="overview"></a>Genel Bakış
SharePoint için StorSimple bağdaştırıcısını Microsoft Azure StorSimple esnek depolama ve veri koruma için SharePoint sunucu grupları olanak sağlayan bir bileşendir. SQL Server içerik veritabanlarından Microsoft Azure StorSimple karma bulut depolama cihazına ikili büyük nesne (BLOB) içeriğini taşımak, bağdaştırıcısı kullanabilirsiniz.

SharePoint için StorSimple bağdaştırıcısını Uzak BLOB Depolama (KKY) sağlayıcısı olarak çalışır ve StorSimple cihaz tarafından desteklenen bir dosya sunucusunda (biçiminde Blobları) yapılandırılmamış SharePoint içeriği depolamak için SQL Server Uzak BLOB Depolama özelliği kullanır.

> [!NOTE]
> SharePoint için StorSimple bağdaştırıcısını, SharePoint Server 2010 uzaktan BLOB Depolama (KKY) destekler. SharePoint Server 2010 dış BLOB Depolama (EBS) desteklemez.


* SharePoint için StorSimple bağdaştırıcısını indirmek için Git [SharePoint için StorSimple bağdaştırıcısı][1] Microsoft Download Center'daki.
* KKY ve KKY sınırlamaları planlama hakkında daha fazla bilgi için Git [SharePoint 2013'te KKY kullanmaya karar vermeden][2] veya [planlama (SharePoint Server 2010) KKY için][3].

Bu genel bakışta geri kalanını SharePoint ve yükleyip bağdaştırıcısı yapılandırmadan önce bilincinde olmanız gereken SharePoint kapasite ve performans sınırları için StorSimple bağdaştırıcısını rolü kısaca açıklanmaktadır. Bu bilgileri gözden geçirdikten sonra Git [SharePoint yükleme için StorSimple bağdaştırıcısı](#storsimple-adapter-for-sharepoint-installation) bağdaştırıcısı kurulumunu başlatmak için.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>SharePoint avantajları için StorSimple bağdaştırıcısı
Bir SharePoint sitesinde içerik yapılandırılmamış BLOB verilerini bir veya daha fazla içerik veritabanlarında saklanır. Varsayılan olarak, bu veritabanları, SQL Server çalıştıran ve SharePoint sunucu grubunda bulunan bilgisayarlara barındırılır. Blobları kullanan şirket içi depolama büyük miktarlarda boyutu hızlı bir şekilde artırabilirsiniz. Bu nedenle, başka bir ve daha az pahalı depolama çözümü bulmak isteyebilirsiniz. SQL Server Uzak Blob Depolama (SQL Server veritabanı dışında dosya sistemindeki BLOB içeriği depolamanıza olanak sağlayan KKY) adı verilen bir teknoloji sağlar. KKY, BLOB, dosya sisteminde SQL Server çalıştıran bilgisayarda bulunabilir veya dosya sistemindeki başka bir sunucu bilgisayarı üzerinde depolanabilir.

KKY KKY SharePoint etkinleştirmek için SharePoint için StorSimple bağdaştırıcısını gibi bir KKY sağlayıcısını kullanmanızı gerektirir. SharePoint için StorSimple bağdaştırıcısını KKY, BLOB'ları Microsoft Azure StorSimple sistemi tarafından yedeklenen bir sunucuya taşımak süreniz ile çalışır. Microsoft Azure StorSimple sonra BLOB verilerini yerel olarak veya bulutta kullanıma göre depolar. Oldukça etkin olan (Katman 1 veya sık erişimli veriler genellikle denir) Blobları yerel olarak yer alır. Daha az etkin veri ve Arşiv verileri bulutta yer alır. Bir içerik veritabanında KKY etkinleştirdikten sonra SharePoint'te oluşturulan tüm yeni BLOB içeriği StorSimple cihazında alan ve içerik veritabanını depolanır.

Microsoft Azure StorSimple uygulamasını KKY aşağıdaki avantajları sağlar:

* Ayrı bir sunucuya taşıma BLOB içeriği SQL Server yanıt hızını iyileştirebilen SQL Server'da sorgu yükünü azaltabilir. 
* Azure StorSimple, veri boyutunu düşürmek için yinelenenleri kaldırma ve sıkıştırma kullanır.
* Azure StorSimple bulut anlık görüntüleri ve form yerel veri koruması sağlar. StorSimple cihazında veritabanı yerleştirirseniz, ayrıca, içerik veritabanı ve BLOB'ları birlikte kilitlenmeyle tutarlı bir şekilde yedekleyebilirsiniz. (Cihaza içerik veritabanını taşıma yalnızca StorSimple 8000 serisi cihaz için desteklenir. Bu özellik için 5000 veya 7000 Serisi desteklenmiyor.)
* Azure StorSimple, yük devretme, dosya ve birim kurtarma (test kurtarma dahil olmak üzere) ve verilerin hızlı geri yükleme gibi olağanüstü durum kurtarma özellikleri içerir.
* SharePoint içeriği öğe düzeyinde kurtarma gerçekleştirmek için BLOB verilerini StorSimple anlık görüntü ile veri kurtarma yazılımı Kroll Ontrack PowerControls gibi kullanabilirsiniz. (Bu veri kurtarma yazılımı ayrı satın içindir.)
* SharePoint için StorSimple bağdaştırıcısını, merkezi bir konumdan tüm SharePoint çözümünüzü yönetmenizi sağlayan SharePoint Merkezi Yönetim portalına yararlanmanıza imkan sağlar.

BLOB içeriği dosya sistemine taşıma diğer maliyet tasarrufu ve avantajlar sağlayabilir. Örneğin, KKY kullanarak pahalı Katman 1 depolama ihtiyacını azaltır ve içerik veritabanını küçültür çünkü KKY SharePoint sunucu grubundaki gerekli veritabanlarının sayısını azaltabilirsiniz. Ancak, veritabanı boyutu sınırları gibi diğer faktörlere ve KKY olmayan içerik miktarını da etkileyebilir depolama gereksinimleri. Maliyetleri ve KKY kullanmanın avantajları hakkında daha fazla bilgi için bkz. [planlama (SharePoint Foundation 2010) KKY için] [ 4] ve [SharePoint 2013'te KKY kullanmaya karar vermeden] [5].

### <a name="capacity-and-performance-limits"></a>Kapasite ve performans sınırları
SharePoint çözümünüzü KKY kullanmayı önce test edilmiş performans ve kapasite sınırları SharePoint Server 2010 ve SharePoint Server 2013'ü ve bu sınırları için kabul edilebilir bir performansı nasıl ilişki kuracağını farkında olmanız gerekir. Daha fazla bilgi için [yazılım sınırları ve SharePoint 2013 için sınırları](https://technet.microsoft.com/library/cc262787.aspx).

KKY yapılandırmadan önce aşağıdakileri gözden geçirin:

* (İçerik veritabanının boyutu) ve ilişkili te dış BLOB boyutu içeriğin toplam boyutunun SharePoint tarafından desteklenen KKY boyut sınırını aşmadığından emin olun. Bu limit, 200 GB olur. 
  
    **Ölçü içerik veritabanı ve BLOB boyutu**
  
  1. Bu sorgu, merkezi yönetim WFE üzerinde çalıştırın. SharePoint Yönetim Kabuğu'nu başlatın ve ardından içerik veritabanları boyutunu almak için aşağıdaki Windows PowerShell komutu girin:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Bu adım, disk üzerinde içerik veritabanı boyutunu alır.
  2. Aşağıdaki SQL sorgularını birini SQL server kutusunda her bir içerik veritabanında bulunan SQL Management Studio'da çalıştırın ve sayı olarak elde edilen adım 1 sonuç ekleyin.
     
     SharePoint 2013'ün üzerinde içerik veritabanları, girin:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     SharePoint 2010 içerik veritabanlarında girin:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Bu adım te dış, BLOB'ları boyutunu alır.
* Tüm veritabanını ve BLOB içeriği yerel olarak StorSimple cihaz üzerinde saklamanızı öneririz. StorSimple cihazı, yüksek kullanılabilirlik için iki düğümlü bir kümedir. İçerik veritabanları ve Blobları StorSimple cihaz üzerinde yerleştirilmesi, yüksek kullanılabilirlik sağlar.
  
    StorSimple cihazına içerik veritabanını taşımak için geleneksel SQL Server Geçiş en iyi yöntemleri kullanın. KKY yalnızca veritabanındaki tüm BLOB içeriği dosya paylaşımına taşındıktan sonra veritabanını taşıyın. StorSimple cihazına içerik veritabanını taşımak isterseniz, cihazdaki birincil bir birim olarak içerik veritabanını depolama yapılandırmanızı öneririz.
* Microsoft Azure katmanlı birim kullanıyorsanız, StorSimple, StorSimple cihaz Microsoft Azure bulut depolama alanına katmanlı değil yerel olarak depolanmış içeriği sağlamak için hiçbir yolu yoktur. Bu nedenle, StorSimple yerel olarak sabitlenmiş birimler SharePoint KKY birlikte kullanmanızı öneririz. Bu işlem tüm BLOB içeriği yerel olarak StorSimple cihazında kalır ve Microsoft Azure'a taşınmaz emin olun.
* StorSimple cihazında içerik veritabanları depolamayın KKY destekleyen geleneksel SQL Server yüksek kullanılabilirlik en iyi uygulamaları kullanın. SQL Server Kümelemesi KKY destekler, SQL Server güncelleştirilirken yansıtmayı desteklemez. 

> [!WARNING]
> KKY etkinleştirmediyseniz, StorSimple cihazı için içerik veritabanını taşıma önerilmez. Bu test edilmemiş bir yapılandırmadır.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>SharePoint yüklemesi için StorSimple bağdaştırıcısı
SharePoint için StorSimple bağdaştırıcısını yükleyebilmek için önce StorSimple cihazı yapılandırma ve SharePoint sunucu grubu ve SQL Server örneğini oluşturmada tüm önkoşulları karşıladığından emin olun. Bu öğreticide, yapılandırma gereksinimlerinin yanı sıra, yükleme ve SharePoint için StorSimple bağdaştırıcısını yükseltme yordamları açıklanmaktadır.

## <a name="configure-prerequisites"></a>Önkoşulları yapılandırma
SharePoint için StorSimple bağdaştırıcısını yükleyebilmek için önce StorSimple cihaz, SharePoint sunucu grubu ve SQL Server örneğini oluşturmada aşağıdaki önkoşulları karşıladığınızdan emin olun.

### <a name="system-requirements"></a>Sistem gereksinimleri
SharePoint için StorSimple bağdaştırıcısını, aşağıdaki donanım ve yazılım ile çalışır:

* Desteklenen işletim sistemi: Windows Server 2008 R2 SP1, Windows Server 2012 veya Windows Server 2012 R2
* Desteklenen SharePoint sürümleri – SharePoint Server 2010 veya SharePoint Server 2013
* Desteklenen SQL Server sürümleri – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition veya SQL Server 2012 Enterprise Edition
* StorSimple cihaz-desteklenen StorSimple 8000 serisi, StorSimple 7000 Serisi ve StorSimple 5000 Serisi.

### <a name="storsimple-device-configuration-prerequisites"></a>StorSimple cihaz yapılandırma önkoşulları
StorSimple cihazı blok cihazı ve bu nedenle veri barındırılabilen bir dosya sunucusu gerektirir. Mevcut bir sunucu yerine ayrı bir sunucuya SharePoint grubundan kullanmanızı öneririz. Bu dosya sunucusu içerik veritabanlarını barındıran SQL Server bilgisayarı aynı yerel ağda (LAN) olmalıdır.

> [!TIP]
> * Yüksek kullanılabilirlik için SharePoint grubu yapılandırırsanız, yüksek kullanılabilirlik için dosya sunucusu de dağıtmanız gerekir.
> * StorSimple cihazında içerik veritabanını depolamayın KKY destekleyen geleneksel yüksek kullanılabilirlik en iyi uygulamaları kullanın. SQL Server Kümelemesi KKY destekler, SQL Server güncelleştirilirken yansıtmayı desteklemez. 


SQL Server bilgisayarınızdan erişilebilir ve StorSimple Cihazınızı doğru şekilde yapılandırıldığını ve SharePoint Dağıtımınızı desteklemek için uygun birim yapılandırıldığından emin emin olun. Git [şirket içi StorSimple Cihazınızı dağıtma](storsimple-8000-deployment-walkthrough-u2.md) size henüz dağıtılan ve StorSimple Cihazınızı yapılandırılmış. StorSimple cihazı IP adresini not alın; Bu SharePoint yüklemesinde sırasında StorSimple bağdaştırıcısı gerekir.

Ayrıca, BLOB externalization için kullanılacak birim aşağıdaki gereksinimleri karşıladığından emin olun:

* Birim 64 KB ayırma birimi boyutu ile biçimlendirilmelidir.
* Web ön uç (WFE) ve uygulama sunucuları, bir Evrensel Adlandırma Kuralı (UNC) yolu üzerinden birim erişebilir olması gerekir.
* SharePoint sunucu grubu birime yazmak için yapılandırılmalıdır.

> [!NOTE]
> Bağdaştırıcıyı yüklediğinizde ve sonra tüm BLOB externalization StorSimple cihazı üzerinden geçmelidir (cihaz birimleri sunmak için SQL Server ve depolama katmanları yönetme). Diğer tüm hedeflerden BLOB externalization için kullanamazsınız.


StorSimple Snapshot Manager BLOB ve veritabanı veri anlık görüntülerini almak için kullanmayı planlıyorsanız, böylece SQL yazıcı hizmeti, Windows Birim Gölge Kopyası Hizmeti (VSS) uygulamak için kullanabilirsiniz, StorSimple Snapshot Manager veritabanı sunucusuna yüklemek emin olun.

> [!IMPORTANT]
> StorSimple Snapshot Manager SharePoint VSS Yazıcı desteklemez ve SharePoint verilerinin uygulamayla tutarlı anlık görüntüler alınamıyor. Bir SharePoint senaryosunda, StorSimple Snapshot Manager yalnızca kilitlenme tutarlı yedeklemeler sağlar.


## <a name="sharepoint-farm-configuration-prerequisites"></a>SharePoint grubu yapılandırma önkoşulları
SharePoint sunucu grubundaki doğru şu şekilde yapılandırıldığından emin olun:

* SharePoint sunucu grubundaki iyi durumda olduğundan emin olun ve aşağıdakileri denetleyin:
* Tüm SharePoint WFE ve grupta kayıtlı uygulama sunucuları çalıştıran ve üzerinde StorSimple bağdaştırıcısını SharePoint için yükleyeceğiniz sunucusundan ping gönderilebildiğini denetleyin.
* SharePoint Zamanlayıcı hizmeti (SPTimerV3 veya SPTimerV4), her bir WFE sunucusunda ve uygulama sunucusu çalışıyor.
* SharePoint Zamanlayıcı hizmeti hem SharePoint Merkezi Yönetim sitesi altında çalışan IIS uygulama havuzu yönetici ayrıcalıklarına sahip.
* Internet Explorer Artırılmış güvenlik bağlamı (IE ESC) devre dışı bırakıldığından emin olun. IE ESC'yi devre dışı bırakmak için aşağıdaki adımları izleyin:
  
  1. Internet Explorer'ın tüm örneklerini kapatın.
  2. Sunucu Yöneticisi'ni başlatın.
  3. Sol bölmede **yerel sunucu**.
  4. Sağ bölmede yanındaki **IE Artırılmış Güvenlik Yapılandırması**, tıklayın **üzerinde**.
  5. Altında **Yöneticiler**, tıklayın **kapalı**.
  6. **Tamam** düğmesine tıklayın.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Uzak bir BLOB Depolama (KKY) önkoşulları
SQL Server'ın desteklenen bir sürümünü kullandığınızdan emin olun. Desteklenen ve KKY kullanmak için yalnızca aşağıdaki sürümler şunlardır:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Bloblar, SQL Server için StorSimple cihazı sunan birimlerde externalized. Başka hiçbir hedef BLOB externalization için desteklenir.

Tüm gerekli yapılandırma adımları tamamladıktan sonra Git [SharePoint için StorSimple bağdaştırıcısı](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>SharePoint için StorSimple bağdaştırıcısını yükle
SharePoint için StorSimple bağdaştırıcısını yüklemek için aşağıdaki adımları kullanın. Yazılım yeniden olup [yükseltme ya da SharePoint için StorSimple bağdaştırıcısını yeniden](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Yükleme için gereken süreyi, SharePoint sunucu grubundaki SharePoint veritabanlarının toplam sayısına bağlıdır.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>KKY yapılandırın
SharePoint için StorSimple bağdaştırıcısını yükledikten sonra KKY aşağıdaki yordamda açıklandığı gibi yapılandırın.

> [!TIP]
> SharePoint için StorSimple bağdaştırıcısını etkin ya da SharePoint grubundaki her bir içerik veritabanında devre dışı KKY izin vererek, SharePoint Yönetim Merkezi sayfasına yararlanmanıza imkan sağlar. Ancak, içerik veritabanını KKY devre dışı bırakma veya etkinleştirme bir IIS sıfırlama, Grup yapılandırmanıza bağlı olarak, kısa bir süre içinde SharePoint web ön uç (WFE) kullanılabilirliğini kesintiye neden olur. (Bir ön uç yük dengeleyici, geçerli sunucu iş yükü ve benzeri gibi faktörlere sınırlamak veya bu kesintisi ortadan kaldırın.) Kullanıcılar bir kesinti korumak için etkinleştirin veya yalnızca bir planlı bakım penceresi sırasında KKY devre dışı bırak öneririz.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Çöp toplamayı yapılandır
Bir SharePoint sitesinden silinen nesneleri, bunlar otomatik olarak KKY depolama biriminden silinmez. Bunun yerine, bir zaman uyumsuz, arka plan bakım program yalnız bırakılmış Blobları dosya depolama alanından siler. Sistem yöneticileri bu işlemi düzenli aralıklarla çalışacak şekilde zamanlayabilirsiniz veya onu gerektiğinde başlayabilirsiniz.

KKY etkinleştirdiğinizde, bu bakım programı (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) tüm SharePoint WFE sunucuları ve uygulama sunucuları otomatik olarak yüklenir. Program şu konuma yüklenir: *önyükleme sürücüsü*: \Program SQL Uzak Blob Depolama 10.50\Maintainer\

Yapılandırma ve Bakım programı kullanma hakkında daha fazla bilgi için bkz: [korumak KKY SharePoint Server 2013'te][8].

> [!IMPORTANT]
> KKY Bakımcı yoğun kaynak programdır. SharePoint grupta yalnızca açık etkinlik dönemlerinde çalışacak şekilde zamanlamanız gerekir.


### <a name="delete-orphaned-blobs-immediately"></a>Yalnız bırakılmış BLOB'ları hemen silin
Yalnız bırakılmış BLOB'ları hemen silmeniz gerekirse, aşağıdaki yönergeleri kullanabilirsiniz. Bu yönergeler nasıl bu aşağıdaki bileşenleri ile SharePoint 2013 ortamında yapılabilir bir örnek olduğuna dikkat edin:

* İçerik veritabanı adı WSS_Content şeklindedir.
* SQL Server SHRPT13 SQL12\SHRPT13 adıdır.
* Web uygulamasının adını SharePoint – 80 ' dir.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>SharePoint için StorSimple bağdaştırıcısını yeniden yükleyin veya yükseltin
SharePoint sunucusunu yükseltmeniz ve ardından SharePoint için StorSimple bağdaştırıcısını yeniden yalnızca yükseltin veya mevcut bir SharePoint sunucu grubundaki bağdaştırıcısını yeniden için aşağıdaki yordamı kullanın.

> [!IMPORTANT]
> SharePoint yazılım ve/veya yükseltme yükseltme ya da SharePoint için StorSimple bağdaştırıcısını yeniden denemeden önce aşağıdaki bilgileri gözden geçirin:
> 
> * Daha önce KKY dış depolama birimine taşınan dosyalar yeniden yükleme tamamlandıktan ve KKY özelliği yeniden etkinleştirilene kadar kullanılamaz. Kullanıcı etkisini sınırlamak için herhangi bir yükseltme veya planlanan bir bakım penceresi sırasında yeniden gerçekleştirin.
> * Yükseltme/yeniden yükleme için gereken süre, SharePoint sunucu grubundaki SharePoint veritabanlarının toplam sayısına bağlı olarak değişebilir.
> * Yükseltme/yeniden yükleme tamamlandıktan sonra içerik veritabanları için KKY etkinleştirmeniz gerekir. Bkz: [yapılandırma KKY](#configure-rbs) daha fazla bilgi için.
> * Veritabanı (büyük), 200 ' çok büyük bir sayı içeren bir SharePoint çiftliği için yapılandırıyorsunuz KKY **SharePoint Yönetim Merkezi** sayfa zaman aşımı olabilir. Bu meydana gelirse, sayfayı yenileyin. Bu yapılandırma işlemini etkilemez.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Temizleme SharePoint için StorSimple bağdaştırıcısı
Aşağıdaki yordamlarda, BLOB'ları için SQL Server veritabanlarını geri taşıyın ve sonra SharePoint için StorSimple bağdaştırıcısı kaldırma açıklanır. 

> [!IMPORTANT]
> Bağdaştırıcı yazılımı kaldırmadan önce Bloblara içerik veritabanlarını geri taşımak zorunda.


### <a name="before-you-begin"></a>Başlamadan önce
Verileri için SQL Server veritabanlarını geri taşıyın ve bağdaştırıcı kaldırma işlemi başlamadan önce aşağıdaki bilgileri toplayın:

* KKY etkinleştirildiği tüm veritabanlarının adlarını
* Yapılandırılmış BLOB deposuna UNC yolu

### <a name="move-the-blobs-back-to-the-content-databases"></a>Bloblara içerik veritabanlarına dönün
Yazılım SharePoint için StorSimple bağdaştırıcısını kaldırmadan önce tüm Blobların te dış, geri SQL Server içerik veritabanlarına geçirmeniz gerekir. Tüm Bloblar için içerik veritabanlarını geri taşımadan önce SharePoint için StorSimple bağdaştırıcısı kaldırma denerseniz, aşağıdaki uyarı iletisi görürsünüz.

![Uyarı iletisi](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Bloblara içerik veritabanlarını geri taşımak için
1. Te dış nesnelerin her biri indirin.
2. Açık **SharePoint Yönetim Merkezi** sayfasında ve göz atın **sistem ayarlarını**.
3. Altında **Azure StorSimple**, tıklayın **StorSimple bağdaştırıcısını yapılandırın**.
4. Üzerinde **StorSimple bağdaştırıcısını yapılandırın** sayfasında **devre dışı** dış BLOB depolama alanından kaldırmak istediğiniz içerik veritabanlarının her biri aşağıda düğmesi. 
5. SharePoint'ten nesneleri silin ve ardından yeniden yükleyin.

Alternatif olarak, Microsoft kullanabilirsiniz `RBS Migrate()` PowerShell cmdlet'i ile SharePoint dahildir. Daha fazla bilgi için [içine veya dışına KKY İçerik Geçişi](https://technet.microsoft.com/library/ff628255.aspx).

Bloblara içerik veritabanını geri taşıdıktan sonra sonraki adıma gidin: [Bağdaştırıcı Kaldırma](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Bağdaştırıcı Kaldırma
BLOB'ları için SQL Server veritabanlarını geri taşıdıktan sonra SharePoint için StorSimple bağdaştırıcısını kaldırmak için aşağıdaki seçeneklerden birini kullanın.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Bağdaştırıcıyı kaldırmak için yükleme programını kullanmak için
1. Web ön uç (WFE) sunucusuna oturum açmak için yönetici ayrıcalıklarına sahip bir hesap kullanın.
2. StorSimple bağdaştırıcısı için SharePoint yükleyiciye çift tıklayın. Kurulum Sihirbazı'nı başlatır.
   
    ![Kurulum Sihirbazı](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. **İleri**’ye tıklayın. Aşağıdaki sayfa açılır.
   
    ![Kurulum Sihirbazı'nı Kaldır sayfası](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Tıklayın **Kaldır** temizleme işlemini seçin. Aşağıdaki sayfa açılır.
   
    ![Kurulum Sihirbazı onay sayfası](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Tıklayın **Kaldır** kaldırma işlemini onaylamak için. Aşağıdaki ilerleme durumu sayfası görüntülenir.
   
    ![Kurulum Sihirbazı ilerleme durumu sayfası](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Kaldırma tamamlandığında Son'a sayfası görüntülenir. Tıklayın **son** Kurulum Sihirbazı'nı kapatın.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Bağdaştırıcıyı kaldırmak için Denetim Masası'nı kullanmak için
1. Denetim Masası'nı açın ve ardından **programlar ve Özellikler**.
2. Seçin **SharePoint için StorSimple bağdaştırıcısı**ve ardından **kaldırma**.

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple hakkında daha fazla bilgi](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
