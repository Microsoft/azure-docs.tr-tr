---
title: Azure dosyalarının faturalandırmasını anlama | Microsoft Docs
description: Azure dosya paylaşımları için sağlanan ve kullandıkça öde faturalandırma modellerini nasıl yorumlayacağınızı öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11d22fd83106bb1802514d0c7d5f67724664464d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788394"
---
# <a name="understand-azure-files-billing"></a>Azure dosyalarının faturalandırmasını anlama
Azure dosyaları iki ayrı faturalandırma modeli sağlar: sağlanan ve kullandıkça öde. Sağlanan model yalnızca, **FileStorage** depolama hesabı türü içinde dağıtılan dosya paylaşımları olan Premium dosya paylaşımları için kullanılabilir. Kullandıkça Öde modeli, yalnızca **genel amaçlı sürüm 2 (GPv2)** depolama hesabı türünde dağıtılan dosya paylaşımları olan standart dosya paylaşımları için kullanılabilir. Bu makalede, her iki modelin de aylık Azure dosyaları faturanızı anlamanıza yardımcı olmak için nasıl çalıştığı açıklanır.

Azure dosyaları fiyatlandırma bilgileri için bkz. [Azure dosyaları fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Depolama birimleri    
Azure dosyaları, PB, MIB, GiB ve TiB depolama kapasitesini temsil etmek için temel 2 ölçü birimlerini kullanır. İşletim sisteminiz aynı ölçü birimini veya sayım sistemini kullanabilir ya da içermeyebilir.

### <a name="windows"></a>Windows

Hem Windows işletim sistemi hem de Azure dosyaları, temel 2 sayma sistemini kullanarak depolama kapasitesini ölçer, ancak birimleri etiketlemenin bir farkı vardır. Azure dosyaları, Windows 'un, 10 tabanında ölçüm birimlerindeki depolama kapasitesini etiketlediği sürece, depolama kapasitesini temel 2 ölçü birimiyle Etiketler. Depolama kapasitesini raporlarken Windows, depolama kapasitesini temel 2 ' den Base-10 ' a dönüştürmez.

|Kısaltması  |Tanım  |Birim  |Windows şöyle görüntülenir  |
|---------|---------|---------|---------|
|KiB     |1.024 bayt         |kibibyte         |KB (kilobayt)         |
|MIB     |1.024 KiB (1.048.576 bayt)         |mebibyte         |MB (megabayt)         |
|GiB     |1024 MIB (1.073.741.824 bayt)         |gibibayt         |GB (gigabayt)         |
|TiB     |1024 GiB (1.099.511.627.776 bayt)         |tebibyte         |TB (terabayt)         |

### <a name="macos"></a>Mac OS

Hangi sayım sisteminin kullanıldığını öğrenmek için [iOS ve macOS 'un Apple Web sitesinde depolama kapasitesini nasıl rapor](https://support.apple.com/HT201402) edin bölümüne bakın.

### <a name="linux"></a>Linux

Farklı bir sayma sistemi, her bir işletim sistemi veya tek bir yazılım parçası tarafından kullanılabilir. Depolama kapasitesini nasıl raporlamalarını öğrenmek için belgelerine bakın.

## <a name="provisioned-model"></a>Sağlanan model
Azure dosyaları Premium dosya paylaşımları için sağlanan modeli kullanır. Sağlanan bir iş modelinde, Azure dosyaları hizmetini, kullandığınız işe göre faturalandırılması yerine, depolama gereksinimlerinizin ne olduğunu önceden belirlersiniz. Bu, belirli bir depolama alanı ile bir Azure dosya paylaşımının sağlanması halinde Şirket içi satın alma ile benzerdir, ancak alan kullanmaya başladığınızda, şirket içi fiziksel medya maliyetlerini ödemekten bağımsız olarak bu depolama alanı için ödeme yapmayı başlamadınız. Şirket içi fiziksel medya satın alma işleminden farklı olarak, sağlanan dosya paylaşımları depolama ve GÇ performansı özelliklerine göre dinamik olarak ölçeklendirilebilir veya azaltılabilir.

Bir Premium dosya paylaşma sağladığınızda, iş yükünüzün kaç tane GiBs gerektirdiğini belirtirsiniz. Sağladığınız her GiB, sabit bir oran üzerinde ek ıOPS ve aktarım hızı sahibine. Garanti ettiğiniz taban çizgisi ıOPS 'ye ek olarak, her bir Premium dosya paylaşımının en iyi çaba temelinde elde edilmesi desteklenir. IOPS ve aktarım hızı formülleri aşağıdaki gibidir:

- Taban çizgisi ıOPS = 400 + 1 * sağlanan GiB. (En fazla 100.000 ıOPS).
- Patlama sınırı = MAX (4000, 3 * temel ıOPS).
- Çıkış oranı = 60 MIB/s + 0,06 * sağlanan GiB.
- Giriş oranı = 40 MIB/s + 0,04 * sağlanan GiB.

Dosya paylaşımının sağlanan boyutu herhangi bir zamanda artırılabilir, ancak son artmadan bu yana yalnızca 24 saat sonra azaltılabilir. Kota artışı olmadan 24 saat bekledikten sonra, yeniden artırana kadar, paylaşma kotasını istediğiniz kadar azaltabilirsiniz. IOPS/verimlilik ölçeği değişiklikleri, sağlanan boyut değişikliğinden sonra birkaç dakika içinde geçerli olacaktır.

Kullandığınız paylaşımın boyutunu, kullanılan GiB 'nizin altında azaltmak mümkündür. Bunu yaparsanız, verileri kaybetmezsiniz, ancak kullanılan boyutun performansını (temel ıOPS, aktarım hızı ve veri bloğu ıOPS) elde edersiniz ve bu boyut kullanılır.

Aşağıdaki tabloda sağlanan paylaşma boyutları için bu formüle birkaç örnek gösterilmektedir:

|Kapasite (GiB) | Temel ıOPS | Veri bloğu ıOPS | Çıkış (MIB/s) | Giriş (MIB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | 4.000 kadar     | 66   | 44   |
|500         | 900     | 4.000 kadar  | 90   | 60   |
|1.024       | 1.424   | 4.000 kadar   | 122   | 81   |
|5.120       | 5.520   | 15.360 kadar  | 368   | 245   |
|10.240      | 10.640  | 30.720 kadar  | 675   | 450   |
|33.792      | 34.192  | 100.000 kadar | 2.088 | 1.392   |
|51.200      | 51.600  | 100.000 kadar | 3.132 | 2.088   |
|102.400     | 100.000 | 100.000 kadar | 6.204 | 4.136   |

Etkin dosya paylaşma performansı, diğer birçok etken arasında makine ağ sınırlarına, kullanılabilir ağ bant genişliğine, GÇ boyutlarına ve paralellik 'e tabidir. Örneğin, 8 kıb okuma/yazma GÇ boyutlarına sahip iç teste bağlı olarak, SMB üzerinden çok kanallı etkin olmayan tek bir Windows sanal makinesi, SMB üzerinden Premium dosya paylaşımıyla bağlantılı *standart F16s_v2*, 20K Okuma IOPS ve 15K Yazma IOPS elde edebilirler. 512 MIB okuma/yazma GÇ boyutları ile aynı VM, 1,1 GiB/sn çıkış ve 370 MIB/s giriş aktarım hızını elde edebilirler. Aynı istemci, \~ Premium paylaşımlardaki çok KANALLı SMB etkinse 3x performansını elde edebilir. En yüksek performans ölçeğini elde etmek için, çok [KANALLı SMB 'yi etkinleştirin](storage-files-enable-smb-multichannel.md) ve yükü birden fazla VM arasında yayılır. Yaygın performans sorunları ve geçici çözümler için çok [kanallı SMB performansı](storage-files-smb-multichannel-performance.md) ve [sorun giderme kılavuzuna](storage-troubleshooting-files-performance.md) bakın.

### <a name="bursting"></a>Patlaması
İş yükünüz en yüksek talebi karşılamak için ek performansa ihtiyaç duyuyorsa, paylaşımınız, isteğe uyması için ihtiyaç duyması gereken paylaşma performansını sunmak üzere, bir yük çizgisi ıOPS sınırını paylaşmak için patlama kredilerini kullanabilir. Premium dosya paylaşımları, ıOPS 'yi 4.000 ' e kadar veya üç etmene kadar (hangisi daha yüksek bir değere) alabilir. Burdıya otomatik ve bir kredi sistemine göre çalışır. Patlama en iyi çaba temelinde çalışır ve veri bloğu sınırı bir garanti değildir; dosya paylaşımları 60 dakikalık en uzun süre sınırına kadar bir süre *kadar* sürebilir.

Krediler, dosya paylaşımınız için trafik temel ıOPS 'nin altında olduğunda bir patlama demetini biriktir. Örneğin, 100 GiB paylaşımında 500 temel ıOPS vardır. Paylaşımdaki gerçek trafik belirli bir 1 saniyelik Aralık için 100 ıOPS ise 400, kullanılmayan ıOPS, bir patlama demetine alacaklandırılır. Benzer şekilde, boş bir 1 TiB paylaşma, 1.424 ıOPS 'de patlama kredisi. Bu krediler daha sonra, işlemler temel ıOPS 'yi aştığında kullanılacaktır.

Bir paylaşımın temel ıOPS 'yi aşması ve bir patlama demeti içinde krediler varsa, izin verilen en yüksek patlama oranı üzerinden patlama olur. Bu paylaşımlar, kredilerin en fazla 60 dakikalık süreye kadar sürebileceği ancak bu, tahakkuk eden patlama kredilerinin sayısına göre, yük devretmeye devam edebilir. Taban çizgisi ıOPS 'nin ötesindeki her GÇ bir kredi kullanır ve tüm krediler tüketildikten sonra, paylaşımın taban çizgisi ıOPS 'ye dönüşmesi gerekir.

Paylaşılan kredilerin üç durumu vardır:

- Dosya paylaşımının taban çizgisi ıOPS 'den az kullanıldığı durumlarda tahakkuk edin.
- Dosya paylaşımının taban çizgisi ıOPS 'den fazla ve patlama modunda ne zaman kullanıldığı reddediliyor.
- Sabit, dosyalar paylaşımında tam olarak taban ıOPS kullanılıyorsa, tahakkuk edilen veya kullanılan krediler yoktur.

Yeni dosya paylaşımları, veri bloğu demetini içinde tam kredi sayısı ile başlar. Sunucu tarafından azaltma nedeniyle, paylaşılan ıOPS, temel ıOPS 'nin altında olursa patlama kredileri tahakkuk ettirilmeyecektir.

## <a name="pay-as-you-go-model"></a>Kullandıkça Öde modeli
Azure dosyaları, standart dosya paylaşımları için Kullandıkça Öde iş modeli kullanır. Kullandıkça Öde iş modelinde, ödeme yaptığınız miktar, sağlanan tutara göre değil, gerçekten ne kadar kullanacağınızı belirler. Yüksek düzeyde, diskte depolanan veri miktarı için bir maliyet ödeyin ve bu verilerin kullanımına bağlı olarak ek bir işlem kümesi ödersiniz. Bir Kullandıkça Öde modeli, gelecekteki büyüme veya performans gereksinimlerine göre hesaba ulaşmalı veya iş yükünüz veri parmak izi zaman içinde farklılık gösterdiği takdirde sağlamayı kaldırmak zorunda olmadığınız için uygun maliyetli olabilir. Diğer yandan, Kullandıkça Öde modeli, bir bütçeleme işleminin parçası olarak planlanabilir, çünkü Kullandıkça Öde faturalandırma modeli son kullanıcı tüketimine göre yönlendiriliyor.

### <a name="differences-in-standard-tiers"></a>Standart katmanlardaki farklılıklar
Standart bir dosya paylaşımından oluşturduğunuz zaman, işlem için iyileştirilmiş, sık erişimli ve seyrek katmanlar arasında seçim yapabilirsiniz. Üç katmanın hepsi, tam olarak aynı standart depolama donanımında depolanır. Bu üç katman için önemli fark, daha soğuk katmanlarda daha düşük olan ve daha soğuk katmanlarda olan işlem fiyatlarına ait olan, bekleyen veri depolama fiyatlarıdır. Diğer bir deyişle:

- Adın gösterdiği gibi işlem için iyileştirilmiş, yüksek işlem iş yüklerinin fiyatını iyileştirir. İşlem için iyileştirilmiş en yüksek veri, Rest depolama fiyatına, ancak en düşük işlem fiyatlarına sahiptir.
- Sık kullanılan etkin iş yükleri için çok sayıda işlem içermeyen ve daha düşük bir veri bekleyen depolama fiyatına sahip olan ve işlem iyileştirilmesine kıyasla biraz daha yüksek işlem fiyatları vardır. İşlem için iyileştirilmiş ve Cool katmanları arasında orta zemin olarak düşünün.
- Serin, çok fazla etkinliği olmayan iş yüklerinin fiyatını en iyi duruma getirir ve en düşük veri, en yüksek işlem fiyatlarını sunar.

İşlem için iyileştirilmiş katmana Seyrek erişilen bir iş yükü koyarsanız, paylaşımınızda işlem yaptığınız bir ayda birkaç kez hiçbir şey ödeyin, ancak veri depolama maliyetleri için yüksek miktarda ücret ödeyecektir. Aynı paylaşımın seyrek erişimli katmana taşımanız, ancak bu iş yükü için çok seyrek işlem yapmanız gerektiğinden, yalnızca işlem maliyetleri için neredeyse hiçbir şey ödemeniz gerekir, ancak Cool katmanının daha fazla ucuz veri depolama fiyatı vardır. Kullanım durumu için uygun katmanı seçmek, maliyetlerinizi önemli ölçüde azaltmanıza olanak tanır. Kullanım durumu için uygun katmanı seçmek, maliyetlerinizi önemli ölçüde azaltmanıza olanak tanır.

Benzer şekilde, seyrek erişimli bir iş yükünü Cool katmanına yerleştirirseniz, işlem maliyetlerinde çok daha fazla ödeme yaparsınız, ancak veri depolama maliyetleri daha düşüktür. Bu durum, işlem fiyatlarının artan maliyetlerinin arttığı bir duruma neden olabilir ve bu da işlem iyileştirilmesinden daha fazla para ödemenizi sağlar. Sık erişimli katman en uygun maliyetli katman olacağı sürece, Cool katmanının işlem iyileştirilenden daha pahalı olacağı bazı kullanım düzeyleri mümkündür.

İş yükünüz ve etkinlik düzeyiniz standart dosya paylaşımınız için en uygun maliyetli katmanı saptacaktır. Uygulamada, en uygun maliyetli katmanı seçmek için en iyi yol, paylaşımın gerçek kaynak tüketimine (depolanan veri, yazma işlemleri vb.) bakmaya devam ediyor.

### <a name="what-are-transactions"></a>İşlemler nelerdir?
İşlemler, dosya paylaşımının içeriğini karşıya yüklemek, indirmek veya başka bir şekilde işlemek için Azure dosyalarına yönelik işlemler veya isteklerdir. Bir dosya paylaşımında gerçekleştirilen her eylem bir veya daha fazla işleme çevrilir ve kullandıkça öde faturalandırma modelini kullanan standart paylaşımlardaki işlem maliyetlerine çevrilir.

Beş temel işlem kategorisi vardır: yazma, listeleme, okuma, diğer ve silme. REST API veya SMB aracılığıyla yapılan tüm işlemler aşağıdaki şekilde bu 4 kategoriden birine bölünmüştür:

| İşlem türü | Yazma işlemleri | İşlem listeleme | İşlemleri oku | Diğer işlemler | İşlemleri Sil |
|-|-|-|-|-|-|
| Yönetim işlemleri | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Veri işlemleri | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4,1 yalnızca sağlanan faturalandırma modelini kullanan Premium dosya paylaşımları için kullanılabilir, işlemler Premium dosya paylaşımları için faturalandırmayı etkilemez.

## <a name="see-also"></a>Ayrıca bkz.
- [Azure dosyaları fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage/files/).
- [Bir Azure dosyaları dağıtımını planlama](storage-files-planning.md) ve [bir Azure dosya eşitleme dağıtımı planlama](../file-sync/file-sync-planning.md).
- [Bir dosya paylaşma oluşturun](storage-how-to-create-file-share.md) ve [Azure dosya eşitleme dağıtın](../file-sync/file-sync-deployment-guide.md).