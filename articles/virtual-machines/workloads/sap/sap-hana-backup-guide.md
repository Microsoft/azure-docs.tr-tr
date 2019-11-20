---
title: Azure sanal makinelerinde SAP HANA için yedekleme Kılavuzu | Microsoft Docs
description: SAP HANA için yedekleme Kılavuzu, Azure sanal makinelerinde SAP HANA için iki önemli yedekleme olanağı sağlar
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 05a4b8e8034e1c354a4209244694aeb2fc2c6007
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078757"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure Sanal Makineler’de SAP HANA için yedekleme kılavuzu

## <a name="getting-started"></a>Başlarken

Azure sanal makinelerinde çalışan SAP HANA için yedekleme kılavuzu yalnızca Azure 'a özgü konuları tanımlıyor. Genel SAP HANA yedeklemeyle ilgili öğeler için, SAP HANA belgelerini inceleyin (Bu makalenin ilerleyen bölümlerinde bulunan _SAP HANA yedekleme belgelerine_ bakın).

Bu makalenin konusu, Azure sanal makinelerinde SAP HANA yönelik iki önemli yedekleme olasılıkdır:

- Azure Linux sanal makinesindeki dosya sistemine HANA yedeklemesi (bkz. [SAP HANA Azure Backup dosya düzeyinde](sap-hana-backup-file-level.md))
- Azure Storage blob anlık görüntüsü özelliğini el ile veya Azure Backup hizmeti kullanarak depolama anlık görüntülerini temel alan HANA yedeklemesi (bkz. [depolama anlık görüntülerine göre SAP HANA yedekleme](sap-hana-backup-storage-snapshots.md))

SAP HANA, üçüncü taraf yedekleme araçlarının doğrudan SAP HANA tümleştirilmesine olanak sağlayan bir yedekleme API 'SI sunmaktadır. (Bu kılavuzun kapsamı içinde değil.) Artık bu API 'ye bağlı Azure Backup hizmet ile SAP HANA doğrudan tümleştirmesi yoktur.

SAP HANA, Azure M serisi gibi çeşitli Azure VM türlerinde resmi olarak desteklenir. SAP HANA sertifikalı Azure VM 'lerinin tüm listesi için [sertifikalı IaaS platformları bulun](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)' ı inceleyin. Bu makale, Azure 'daki SAP HANA yönelik yeni teklifler kullanılabilir hale geldiğinde güncelleştirilecektir.

Azure 'da kullanılabilen bir SAP HANA hibrit çözümü de vardır; burada SAP HANA fiziksel sunucularda sanallaştırılmamış olarak çalışır. Ancak, bu SAP HANA Azure Backup Kılavuzu, SAP HANA bir Azure VM 'de çalıştığı, büyük örneklerde SAP HANA değil, saf bir Azure ortamını &quot;ele alır.&quot; Depolama anlık görüntülerine göre &quot;büyük örneklerde&quot; bu yedekleme çözümü hakkında daha fazla bilgi için bkz. [SAP HANA (büyük örnekler) genel bakış ve Azure üzerinde mimari](hana-overview-architecture.md) .

Azure 'da desteklenen SAP ürünleri hakkında genel bilgiler, [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533)' de bulunabilir.

Aşağıdaki üç şekil, şu anda yerel Azure yeteneklerini kullanarak SAP HANA yedekleme seçeneklerine genel bir bakış sağlar ve gelecekteki üç olası yedekleme senaryosunu da gösterir. [Dosya düzeyinde Azure Backup SAP HANA](sap-hana-backup-file-level.md) ilgili makaleler ve [depolama anlık görüntülerine göre SAP HANA yedekleme](sap-hana-backup-storage-snapshots.md) , bu seçenekleri daha ayrıntılı bir şekilde anlatmaktadır. Bu seçenekler, boyutu çok terabayt olan SAP HANA yedeklemeler için boyut ve performans konuları da dahil olmak üzere daha ayrıntılı olarak açıklanır

![Bu şekilde, geçerli VM durumunun kaydedilmesine yönelik iki olasılık gösterilmektedir](media/sap-hana-backup-guide/image001.png)

Bu şekilde, VM disklerinin Azure Backup hizmet veya el ile anlık görüntüsü aracılığıyla geçerli VM durumunu kaydetme olasılığı gösterilmektedir. Bu yaklaşımda, tek bir&#39;SAP HANA yedeklemeleri yönetmesi gerekmez. Disk anlık görüntüsü senaryosunun çekişmesi, dosya sistemi tutarlılığı ve uygulamayla tutarlı bir disk durumudur. Tutarlılık konusu, bu makalede daha sonra _depolama anlık görüntüleri alırken veri tutarlılığı SAP HANA_ bölümünde ele alınmıştır. SAP HANA yedeklemeleriyle ilgili Azure Backup hizmetin özellikleri ve kısıtlamaları, bu makalenin ilerleyen bölümlerinde de anlatılmaktadır.

![Bu şekilde, sanal makine içinde SAP HANA dosya yedeklemesi alma seçenekleri gösterilmektedir](media/sap-hana-backup-guide/image002.png)

Bu şekilde, sanal makine içinde bir SAP HANA dosya yedeklemesi alma seçenekleri gösterilmektedir ve ardından bu farklı araçları kullanarak bu şekilde BT HANA yedekleme dosyalarını başka bir yerde depolarsınız. HANA yedeklemesini almak, bir anlık görüntü tabanlı yedekleme çözümüyle daha fazla zaman gerektirir, ancak bütünlük ve tutarlılık hakkında avantajlara sahiptir. Daha sonra bu makalenin ilerleyen ayrıntıları verilmiştir.

![Bu şekilde, olası bir gelecekte SAP HANA yedekleme senaryosu gösterilmektedir](media/sap-hana-backup-guide/image003.png)

Bu şekilde, olası bir gelecekte SAP HANA yedekleme senaryosu gösterilmektedir. SAP HANA çoğaltma ikincisinden yedekleme almaya izin verildiyse, yedekleme stratejileri için ek seçenekler ekler. Şu anda SAP HANA wiki 'deki bir gönderisine göre mümkün değildir:

_&quot;İkincil tarafta yedeklemeler almak mümkün mü?_

_Hayır, şu anda yalnızca birincil tarafta veri ve günlük yedeklemeleri gerçekleştirebilirsiniz. Otomatik günlük yedeklemesi etkinse, ikincil tarafa devralındıktan sonra günlük yedeklemeleri otomatik olarak yazılır.&quot;_

## <a name="sap-resources-for-hana-backup"></a>HANA yedeklemesi için SAP kaynakları

### <a name="sap-hana-backup-documentation"></a>SAP HANA yedekleme belgeleri

- [SAP HANA yönetimine giriş](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Yedekleme ve kurtarma stratejinizi planlama](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [ABAP DBAKOKPIT kullanarak HANA yedeklemesini zamanlama](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Veri yedeklemeleri zamanla (SAP HANA kokpiti)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148) SAP HANA yedekleme hakkında SSS
- [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) SAP HANA veritabanı ve depolama anlık GÖRÜNTÜLERI hakkında SSS
- [SAP Note](https://launchpad.support.sap.com/#/notes/1820529) 'da yedekleme ve kurtarma için uygun olmayan ağ dosya sistemleri 1820529

### <a name="why-sap-hana-backup"></a>Neden yedekleme SAP HANA?

Azure depolama, kutudan daha fazla kullanılabilirlik ve güvenilirlik sunar (bkz. Azure depolama hakkında daha fazla bilgi için bkz. [Microsoft Azure depolama tanıtım](../../../storage/common/storage-introduction.md) ).

&quot;Yedekleme&quot; için en düşük değer Azure SLA 'larını temel alarak, SAP HANA verileri ve günlük dosyalarını SAP HANA sunucusu VM 'sine bağlı Azure VHD 'lerde saklayın. Bu yaklaşım, sanal makine hatalarını, SAP HANA veri ve günlük dosyalarını ya da yanlışlıkla verileri veya dosyaları silme gibi mantıksal hataları ele alır. Yedeklemeler, uyumluluk veya yasal nedenlerle de gereklidir. Kısacası SAP HANA yedeklemeler için her zaman bir gereksinim vardır.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA yedeğinin doğruluğunu doğrulama
Depolama anlık görüntülerini kullanırken, farklı bir sistemde bir test geri yüklemesi çalıştırmak önerilir. Bu yaklaşım, bir yedeklemenin doğru olduğundan ve yedekleme ve geri yükleme için iç işlemlerin beklendiği gibi çalışmasını sağlamak için bir yol sağlar. Bu, şirket içi önemli bir ANTI olsa da, bu amaçla gerekli kaynakları geçici olarak sağlayarak bulutta gerçekleştirilmesi çok daha kolay.

Basit bir geri yükleme ve HANA 'nın çalışır durumda olup olmadığını kontrol etmek için yeterli olup olmadığını aklınızda bulundurun. En ideal olarak, geri yüklenen veritabanının iyi olduğundan emin olmak için bir tablo tutarlılık denetimi çalıştırmalıdır. SAP HANA, [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584)' de açıklanan çeşitli türlerde tutarlılık denetimleri sunmaktadır.

Tablo tutarlılık denetimiyle ilgili bilgiler ayrıca, [tablo ve Katalog tutarlılığı DENETIMLERINDE](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)SAP web sitesinde bulunabilir.

Standart dosya yedeklemeleri için bir test geri yüklemesi gerekli değildir. Geri yükleme için hangi yedeklemenin kullanılabileceğini denetlemeye yardımcı olan iki SAP HANA araç vardır: hdbbackupdiag ve hdbbackupcheck. Bu araçlar hakkında daha fazla bilgi için [bir kurtarmanın mümkün olup olmadığını el Ile denetleme](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) bölümüne bakın.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA yedeklemesini ve depolama anlık görüntüsüne yönelik olumlu ve olumsuz yönleri

&#39;SAP, Hana yedeklemesini ve depolama anlık görüntüsüne yönelik tercihe izin vermez. Profesyonelleri ve dezavantajlarını listeler. bu nedenle, bir diğeri duruma ve kullanılabilir depolama teknolojisine bağlı olarak hangisinin kullanılacağını belirleyebilir (bkz. [yedekleme ve kurtarma stratejinizi planlama](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

Azure 'da, Azure Blob anlık görüntüsü özelliğinin&#39;dosya sistemi tutarlılığını garanti ettiğini unutmayın (bkz. [PowerShell ile blob anlık görüntüleri kullanma](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). Sonraki bölümde, _depolama anlık görüntülerini alırken veri tutarlılığı SAP HANA_, bu özellikle ilgili bazı konular açıklanmaktadır.

Bunlara ek olarak, bu makalede açıklandığı gibi BLOB anlık görüntüleri ile sık sık çalışırken faturalandırma etkilerini anlamalıdır: [Anlık görüntülerin nasıl tahakkuk ettirildiğini](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)&#39;anlama — Azure sanal diskleri 'ni kullanarak belirgin bir şekilde.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Depolama anlık görüntüleri alırken veri tutarlılığını SAP HANA

Dosya sistemi ve uygulama tutarlılığı, depolama anlık görüntüleri alırken karmaşık bir sorundur. Sorunlardan kaçınmak için en kolay yol SAP HANA veya belki de sanal makinenin tamamını kapatmaktır. Bir kapalı, bir tanıtım veya prototip ya da bir geliştirme sistemi olabilir, ancak bir üretim sistemi için bir seçenek değildir.

Azure 'da, Azure Blob anlık görüntü özelliğinin&#39;dosya sistemi tutarlılığını garanti vermediğini göz önünde bulundurmanız gerekir. Bununla birlikte, yalnızca tek bir sanal disk dahil olmak üzere SAP HANA anlık görüntü özelliğini kullanarak sorunsuz bir şekilde çalışacaktır. Ancak, tek bir diskle birlikte ek öğelerin denetlenmesi gerekir. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) , depolama anlık görüntüleri aracılığıyla SAP HANA yedeklemeleri hakkında önemli bilgiler içerir. Örneğin, XFS dosya sistemiyle, tutarlılığı güvence altına almak için bir depolama anlık görüntüsüne başlamadan önce **XFS\_donması** 'nı çalıştırmak için gereklidir (XFS [\_](https://linux.die.net/man/8/xfs_freeze) **ileilgiliayrıntılariçinbkz.XFSdondurma(8)-Linuxmansayfasıdondurma\_** ).

Tutarlılık konusu, tek bir dosya sisteminin birden fazla diske/birime yayıldığı bir durumda daha da zor hale gelir. Örneğin, mdaddm veya LVM ve şeritleme kullanarak. Aşağıda belirtilen SAP notunun durumları:

_&quot;Ancak, SAP HANA veri hacmi başına bir depolama anlık görüntüsü oluştururken depolama sisteminin g/ç tutarlılığını garanti etmesini unutmayın; Örneğin, SAP HANA hizmete özgü bir veri biriminin anlık görüntüsü oluşturma atomik bir işlem olmalıdır.&quot;_

Dört Azure sanal diski kapsayan bir XFS dosya sistemi olduğu varsayılarak, aşağıdaki adımlarda, HANA veri alanını temsil eden tutarlı bir anlık görüntü sağlanmıştır:

- HANA Snapshot hazırlama
- Dosya sistemini dondurma (örneğin, **\_XFS Freeze**kullanın)
- Tüm gerekli blob anlık görüntülerini Azure 'da oluşturun
- Dosya sistemini çöz
- HANA anlık görüntüsünü onaylama

Öneri, her durumda, hangi dosya sistemine bakılmaksızın, güvenli tarafta olması için yukarıdaki yordamı kullanmaktır. Ya da birden çok disk üzerinde mdaddm veya LVM aracılığıyla tek bir diskdir veya dizme.

HANA anlık görüntüsünü doğrulamak önemlidir. Kopyalama-yazma nedeniyle,&quot; bu anlık görüntü hazırlama modundayken SAP HANA ek disk alanı gerektirmeyebilir. &quot; &#39;Ayrıca, SAP HANA anlık görüntüsü onaylanana kadar yeni yedeklemeler başlatmak mümkün değildir.

Azure Backup hizmeti, dosya sistemi tutarlılığını sağlamak için Azure VM uzantıları 'nı kullanır. Bu VM uzantıları tek başına kullanım için kullanılamaz. Hala SAP HANA tutarlılığı yönetmek zorunda kalır. Daha fazla bilgi için [SAP HANA ilgili makaleye Azure Backup dosya düzeyinde](sap-hana-backup-file-level.md) bakın.

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA yedekleme zamanlama stratejisi

[Yedekleme ve kurtarma stratejinizi planlama](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) SAP HANA makalesi, yedeklemeleri yapmak için temel bir plan olduğunu belirtir:

- Depolama anlık görüntüsü (günlük)
- Dosya veya bacınt biçimini kullanarak veri yedeklemeyi tamamlama (haftada bir kez)
- Otomatik günlük yedeklemeleri

İsteğe bağlı olarak, bir diğeri depolama anlık görüntüleri olmadan tamamen geçebilir; Bunlar, artımlı veya fark yedeklemeleri gibi HANA Delta yedeklemeleri ile değiştirilebilir (bkz. [Delta yedeklemeleri](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

HANA Yönetim Kılavuzu, örnek bir liste sağlar. Bir kurtarma SAP HANA, aşağıdaki yedekleme sırasını kullanarak belirli bir zaman noktasına kurtarmanızı önerir:

1. Tam veri yedekleme
2. Değişiklik yedeği
3. Artımlı yedekleme 1
4. Artımlı yedekleme 2
5. Günlük yedeklemeleri

Belirli bir yedekleme türünün ne zaman ve ne sıklıkta olması gerektiği konusunda tam bir zamanlamaya ilişkin olarak, genel bir kılavuz vermek mümkün değildir, ancak sisteme özgü olan çok sayıda veri değişikliği olmasına bağlıdır. SAP tarafında, genel kılavuz olarak görülebilen bir temel öneri, haftada bir tam HANA yedeklemesi yapmak.
Günlük yedeklemeleriyle ilgili SAP HANA belge [günlüğü yedeklemelerini](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)inceleyin.

SAP Ayrıca, sonsuza kadar büyümeye devam etmek için Yedekleme kataloğunun bazı evlerden birini yapmanızı önerir (bkz. [Yedekleme kataloğu ve yedek depolama Için temizlik](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Yapılandırma dosyaları SAP HANA

[SAP not 1642148](https://launchpad.support.sap.com/#/notes/1642148)' deki SSS bölümünde belirtildiği gibi, SAP HANA yapılandırma dosyaları standart Hana yedeklemesinin bir parçası değildir. Bir sistemi geri yüklemek gerekli değildir. HANA yapılandırması geri yüklemeden sonra el ile değiştirilebilir. Geri yükleme işlemi sırasında aynı özel yapılandırmayı almak isterseniz, HANA yapılandırma dosyalarını ayrı olarak yedeklemeniz gerekir.

Standart HANA yedeklemeleri ayrılmış bir HANA yedekleme dosya sistemine gitecekse, bir diğeri de yapılandırma dosyalarını aynı yedekleme dosya sistemine kopyalayabilir ve ardından her şeyi seyrek erişimli BLOB depolama gibi son depolama hedefine kopyalayabilir.

### <a name="sap-hana-cockpit"></a>SAP HANA kokpiti

SAP HANA kokpiti bir tarayıcı aracılığıyla SAP HANA izleme ve yönetme olanağı sunar. Ayrıca, SAP HANA yedeklemelerin işlenmesine de olanak tanır ve bu nedenle SAP HANA Studio ve ABAP DBAKOKPIT için alternatif olarak kullanılabilir (daha fazla bilgi için bkz. [SAP HANA kokpiti](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) ).

![Bu şekilde SAP HANA kokpit veritabanı yönetim ekranı gösterilmektedir](media/sap-hana-backup-guide/image004.png)

Bu şekilde SAP HANA kokpit veritabanı yönetim ekranı ve sol taraftaki yedekleme kutucuğu gösterilmektedir. Yedekleme kutucuğunu görmek için oturum açma hesabı için uygun Kullanıcı izinleri gerekir.

![Yedeklemeler devam ederken SAP HANA kokpiti izlenebilir](media/sap-hana-backup-guide/image005.png)

Yedeklemeler SAP HANA kokpiti devam ederken izlenebilir ve işiniz bittiğinde tüm yedekleme ayrıntıları kullanılabilir.

![GNOME Desktop ile bir Azure SLES 12 VM 'de Firefox kullanan bir örnek](media/sap-hana-backup-guide/image006.png)

Önceki ekran görüntüleri bir Azure Windows VM 'den yapılmıştır. Bu, GNOME Desktop ile bir Azure SLES 12 VM 'de Firefox kullanan bir örnektir. SAP HANA kokpit içinde SAP HANA yedekleme zamanlamaları tanımlama seçeneğini gösterir. Bir diğeri de görebilir, bu, yedekleme dosyalarının öneki olarak tarih/saat önerir. SAP HANA Studio 'da, tam bir dosya yedeklemesi &quot;gerçekleştirirken varsayılan\_ön&quot; ek, tam\_veri yedeklemesi olur. Benzersiz bir ön ek kullanılması önerilir.

### <a name="sap-hana-backup-encryption"></a>SAP HANA yedekleme şifrelemesi

SAP HANA veri ve günlük şifrelemeyi sağlar. SAP HANA veri ve günlük şifrelenmemişse yedeklemeler de şifrelenmez. Bu, SAP HANA yedeklemelerini şifrelemek için bazı üçüncü taraf çözüm biçiminde bir müşterinin kullanımına sahiptir. SAP HANA şifreleme hakkında daha fazla bilgi edinmek için bkz. [veri ve günlük birimi şifrelemesi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) .

Microsoft Azure, müşteri, şifrelemek için IaaS VM Şifrelemesi özelliğini kullanabilir. Örneğin, bir, VM 'ye bağlı ayrılmış veri disklerini, SAP HANA yedeklemeleri depolamak için kullanılan ve ardından bu disklerin kopyalarını oluşturmak için kullanabilir.

Azure Backup hizmet, şifrelenmiş VM 'Leri/diskleri işleyebilir (bkz. [Azure Backup ile şifrelenmiş sanal makineleri yedekleme ve geri yükleme](../../../backup/backup-azure-vms-encryption.md)).

Diğer bir seçenek, şifreleme olmadan SAP HANA VM ve disklerinin bakımını yapmak ve SAP HANA yedekleme dosyalarını şifrelemenin etkinleştirildiği bir depolama hesabında depolamak (bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Test kurulumu

### <a name="test-virtual-machine-on-azure"></a>Azure 'da sanal makineyi test etme

Testlerinizi gerçekleştirmek için, aşağıdaki yedekleme/geri yükleme testlerinde bir Azure GS5 VM 'de SAP HANA yüklemesi kullanılmıştır. İlkeler, d serisi VM 'Lerle aynıdır.

![Bu şekilde, HANA test sanal makinesi için Azure portal genel bakış gösterilmektedir](media/sap-hana-backup-guide/image007.png)

Bu şekilde, HANA test sanal makinesi için Azure portal genel bakışın bir parçası gösterilmektedir.

### <a name="test-backup-size"></a>Sınama yedekleme boyutu

![Bu şekil, HANA Studio 'daki yedekleme konsolundan alınmıştır ve HANA dizin sunucusu için 229 GB yedek dosya boyutunu gösterir](media/sap-hana-backup-guide/image008.png)

Bir kukla tablo, gerçekçi performans verileri türetmek için 200 GB 'tan fazla veri yedekleme boyutu elde etmek üzere verilerle doldurulmuştur. Şekil, HANA Studio 'daki yedekleme konsolundan alınmıştır ve HANA dizin sunucusu için 229 GB yedek dosya boyutunu gösterir. Testler için, SAP HANA Studio 'daki varsayılan "COMPLETE_DATA_BACKUP" yedekleme ön eki kullanılmıştır. Gerçek üretim sistemlerinde daha yararlı bir ön ek tanımlanmalıdır. SAP HANA kokpiti tarih/saat önerir.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Dosyaları doğrudan Azure Storage 'a kopyalamak için test aracı

SAP HANA yedekleme dosyalarını doğrudan Azure Blob depolama alanına veya Azure dosya paylaşımlarına aktarmak için, her iki hedefi desteklediğinden ve komut satırı arabirimi nedeniyle Otomasyon betikleriyle kolayca tümleştirilebilen blobxfer aracı kullanılmıştır. Blobxfer aracı [GitHub](https://github.com/Azure/blobxfer)' da kullanılabilir.

### <a name="test-backup-size-estimation"></a>Test yedek boyut tahmini

SAP HANA yedekleme boyutunu tahmin etmek önemlidir. Bu tahmin, bir dosya kopyası sırasında paralellik nedeniyle, bir dizi yedekleme dosyası için en fazla yedek dosya boyutunu tanımlayarak performansı artırmaya yardımcı olur. (Bu ayrıntılar Bu makalenin ilerleyen kısımlarında açıklanmaktadır.) Ayrıca, bir tam yedekleme mi yoksa bir Delta yedeklemesi mi yapacağınıza (artımlı veya fark) karar vermelisiniz.

Neyse ki, yedekleme dosyalarının boyutunu tahmin eden basit bir SQL deyimidir: 5  **\* \_yedek\_\_boyut tahmininden seçim** yapın (bkz. [bir veri için dosya sisteminde gereken alanı tahmin etme Yedekleme](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Bu SQL ifadesinin çıktısı, disk üzerindeki tam veri yedeklemesinin neredeyse tam olarak gerçek boyutuyla eşleşir](media/sap-hana-backup-guide/image009.png)

Test sistemi için, bu SQL deyimin çıktısı disk üzerindeki tam veri yedeklemesinin neredeyse tam olarak gerçek boyutuyla eşleşir.

### <a name="test-hana-backup-file-size"></a>Test HANA yedekleme dosya boyutu

![HANA Studio yedekleme konsolu, bir birinin HANA yedekleme dosyalarının en büyük dosya boyutunu kısıtlayasağlar](media/sap-hana-backup-guide/image010.png)

HANA Studio yedekleme konsolu, bir birinin HANA yedekleme dosyalarının en büyük dosya boyutunu kısıtlayasağlar. Örnek ortamda, bu özellik 1 230 GB yedekleme dosyası yerine birden çok daha küçük yedekleme dosyası almanızı olanaklı kılar. Daha küçük dosya boyutu performans üzerinde önemli bir etkiye sahiptir ( [dosya düzeyinde Azure Backup SAP HANA](sap-hana-backup-file-level.md)ilgili makaleye bakın).

## <a name="summary"></a>Özet

Test sonuçlarına bağlı olarak aşağıdaki tablolarda, Azure sanal makinelerinde çalışan bir SAP HANA veritabanını yedeklemek için çözümlerin olumlu ve olumsuz yönleri gösterilmektedir.

**Dosya sistemine SAP HANA yedekleyin ve yedekleme dosyalarını daha sonra son yedekleme hedefine kopyalayın**

|Çözüm                                           |Uzmanları                                 |Simgeler                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Sanal makine disklerinde HANA yedeklemelerini sakla                      |Ek yönetim çalışmaları yok     |Yerel VM disk alanı oluştur           |
|Yedekleme dosyalarını blob depolamaya kopyalamak için blobxfer aracı |Birden çok dosyayı kopyalamak için paralellik, Cool blob Storage kullanma seçeneği | Ek araç bakımı ve özel betik oluşturma | 
|PowerShell veya CLı aracılığıyla blob kopyalama                    |Ek bir araç gerekli değildir, Azure PowerShell veya CLı aracılığıyla sağlanabilir |el ile gerçekleştirilen işlem, müşterinin geri yükleme için kopyalanmış Blobların komut dosyalarını ve yönetimini ele geçirmesine olanak|
|NFS paylaşımıyla Kopyala                                  |HANA sunucusu üzerinde etki olmadan yedekleme dosyalarının diğer VM 'de işlenmesi|Yavaş kopyalama işlemi|
|Blobxfer Azure dosya hizmeti 'ne kopyalama                |Yerel VM disklerinde boş alan yok|HANA yedeklemesi tarafından doğrudan yazma desteği yok, dosya paylaşımının boyut kısıtlaması Şu anda 5 TB|
|Azure Backup Aracısı                                 | Tercih edilen çözüm olacaktır         | Şu anda Linux üzerinde kullanılamaz    |



**Depolama anlık görüntülerine göre SAP HANA yedekleme**

|Çözüm                                           |Uzmanları                                 |Simgeler                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup hizmeti                               | Blob anlık görüntülerine göre VM yedeklemesine izin verir | Dosya düzeyinde geri yükleme kullanılırken, geri yükleme işlemi için yeni bir VM oluşturulması gerekir ve bu daha sonra yeni bir SAP HANA lisans anahtarı gereksinimini gösterir|
|El ile blob anlık görüntüleri                              | Benzersiz VM KIMLIĞINI değiştirmeden belirli VM disklerini oluşturma ve geri yükleme esnekliği|Müşteri tarafından yapılması gereken tüm el ile çalışma|

## <a name="next-steps"></a>Sonraki adımlar
* [Dosya düzeyinde Azure Backup SAP HANA](sap-hana-backup-file-level.md) dosya tabanlı yedekleme seçeneğini açıklar.
* [Depolama anlık görüntülerine göre SAP HANA yedekleme](sap-hana-backup-storage-snapshots.md) , depolama anlık görüntü tabanlı yedekleme seçeneğini açıklar.
* Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan (büyük örnekler) oluşturma hakkında bilgi edinmek için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md).
