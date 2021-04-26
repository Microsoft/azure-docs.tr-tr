---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü aracı nedir? | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracı için bir giriş sağlar.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 4ba679459686340396e0e4d65344295c0fa9c4be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869965"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulama ile tutarlı anlık görüntü aracı nedir (Önizleme)

Azure uygulamayla tutarlı anlık görüntü aracı (AzAcSnap), bir depolama anlık görüntüsünü almadan önce bir uygulama tutarlı duruma koymak için gereken tüm düzenleme işlemlerini işleyerek, üçüncü taraf veritabanları için veri korumasına olanak tanıyan bir komut satırı aracıdır.

## <a name="supported-platforms-and-os"></a>Desteklenen platformlar ve işletim sistemi

- **Veritabanları**
  - SAP HANA (Ayrıntılar için [destek matrisine](azacsnap-get-started.md#snapshot-support-matrix-from-sap) bakın)

- **İşletim sistemleri**
  - SUSE Linux Enterprise Server 12 +
  - Red Hat Enterprise Linux 7 +

## <a name="benefits-of-using-azacsnap"></a>AzAcSnap kullanmanın avantajları

AzAcSnap, Azure NetApp Files ve Azure büyük örneğindeki birim anlık görüntüsünü ve çoğaltma işlevlerini kullanır.  Aşağıdaki avantajları sağlar:

- **Uygulamayla tutarlı veri koruma** AzAcSnap, kritik veritabanı dosyalarını yedeklemeye yönelik merkezi bir çözümdür. Bir depolama birimi anlık görüntüsü gerçekleştirmeden önce veritabanı tutarlılığını sağlar. Sonuç olarak, depolama birimi anlık görüntüsünün veritabanı kurtarması için kullanılabilir olmasını sağlar.
- **Veritabanı kataloğu yönetimi** AzAcSnap 'yi yerleşik bir yedekleme kataloğuna sahip bir veritabanıyla kullandığınızda, katalogdaki kayıtlar depolama anlık görüntüleriyle güncel tutulur.  Bu özellik bir veritabanı yöneticisinin Yedekleme etkinliğini görmesini sağlar.
- Geçici **birim koruması** Bu özellik, depolama anlık görüntüsü almadan önce uygulama sessiz moda gerektirmeyen veritabanı olmayan birimler için yararlıdır.  SAP HANA günlük yedekleme birimleri veya SAPTRANS birimleri örnekleri verilebilir.
- **Depolama birimlerinin kopyalanması** Bu özellik, geliştirme ve test amaçları için alan verimli depolama birimi klonlarını sağlar.
- **Olağanüstü durum kurtarma desteği** AzAcSnap, uzak bir sitede çoğaltılan uygulamayla tutarlı anlık görüntüleri kurtarmak için seçenekler sağlamak üzere depolama birimi çoğaltmasını kullanır.

AzAcSnap tek bir ikili.  Veritabanı veya depolama ile etkileşim kurmak için ek aracıların veya eklentilerin (Azure Resource Manager ve SSH aracılığıyla Azure büyük örnek aracılığıyla Azure NetApp Files olması gerekmez).  AzAcSnap, veritabanı ve depolama bağlantısı olan bir sistemde yüklü olmalıdır.  Ancak, yükleme ve yapılandırma esnekliği, tek bir merkezi yüklemeye veya her bir veritabanı yüklemesinde yüklü kopyalarla tam olarak dağıtılmış bir yüklemeye izin verir.

## <a name="architecture-overview"></a>Mimariye genel bakış

AzAcSnap, veritabanı ile aynı konağa (SAP HANA) yüklenebilir veya merkezi bir sisteme yüklenebilirler.  Ancak, veritabanı sunucularıyla ağ bağlantısı ve depolama arka ucu (Azure büyük örnek için Azure NetApp Files veya SSH için Azure Resource Manager) olmalıdır.

AzAcSnap, genellikle bir dış Scheduler 'dan yürütülen hafif bir uygulamadır.  Çoğu Linux sisteminde bu işlem, `cron` belgelerin odaklanacağı şeydir.  Ancak Zamanlayıcı, kullanıcının kabuk profilini içeri aktarabileceği sürece alternatif bir araç olabilir `azacsnap` .  Kullanıcının ortam ayarlarını içeri aktarmak, dosya yollarının ve izinlerin doğru bir şekilde başlatılmasını sağlar.

## <a name="command-synopsis"></a>Komut Özeti

Komutların genel biçimi şu şekildedir: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Komut seçenekleri

Komut seçenekleri, komutları ana madde işaretleri ve ilişkili alt komutlar olarak girintili madde işaretleri olarak aşağıdaki gibidir:

- **`-h`** AzAcSnap kullanımındaki örneklerle genişletilmiş komut satırı yardımı sağlar.
- **`-c configure`** Bu komut, yapılandırma dosyası oluşturmak veya değiştirmek için bir stil arabirimi&etkileşimli bir soru sağlar `azacsnap` (varsayılan = `azacsnap.json` ).
  - **`--configuration new`** Yeni bir yapılandırma dosyası oluşturacaktır.
  - **`--configuration edit`** , var olan bir yapılandırma dosyasını düzenler.
  - [komut başvurusunu yapılandırma](azacsnap-cmd-ref-configure.md)bölümüne bakın.
- **`-c test`** yapılandırma dosyasını doğrular ve bağlantıyı test edin.
  - **`--test hana`**  SAP HANA örneğiyle bağlantıyı sınar.
  - **`--test storage`** yapılandırılan tüm birimlerde geçici bir depolama anlık görüntüsü oluşturup bunları kaldırarak, temel alınan depolama arabirimiyle iletişimi sınar `data` .
  - **`--test all`** sırayla hem hem de `hana` `storage` testlerini gerçekleştirir.
  - [test komut başvurusuna](azacsnap-cmd-ref-test.md)bakın.
- **`-c backup`** , diğer (örneğin, paylaşılan, günlük yedeklemeleri veya önyükleme) birimleri & verileri (SAP HANA veri birimleri) için veritabanı tutarlı depolama anlık görüntülerini yürütmeye yönelik birincil komuttur.
  - **`--volume data`** yapılandırma dosyasının Stanza 'daki tüm birimlerin anlık görüntüsünü almak için `dataVolume` .
  - **`--volume other`** yapılandırma dosyasının Stanza 'daki tüm birimlerin anlık görüntüsünü almak için `otherVolume` .
  - [yedekleme komut başvurusuna](azacsnap-cmd-ref-backup.md)bakın.
- **`-c details`** anlık görüntüler veya çoğaltma hakkında bilgi sağlar.
  - **`--details snapshots`** Yapılandırılmış her bir birimin anlık görüntülerle ilgili temel ayrıntıların bir listesini sağlar.
  - **`--details replication`** Üretim sitesinden olağanüstü durum kurtarma sitesine çoğaltma durumu etrafında temel ayrıntılar sağlar.
  - [Ayrıntılar komut başvurusuna](azacsnap-cmd-ref-details.md)bakın.
- **`-c delete`** Bu komut bir depolama anlık görüntüsünü veya bir anlık görüntü kümesini siler. SAP HANA yedekleme KIMLIĞINI HANA Studio veya depolama anlık görüntü adı 'nda bulunan olarak kullanabilirsiniz. Yedekleme KIMLIĞI yalnızca `hana` veri ve paylaşılan birimler için oluşturulan anlık görüntülere bağlıdır. Aksi takdirde, anlık görüntü adı girilirse, girilen anlık görüntü adıyla eşleşen tüm anlık görüntüleri arar.
  - bkz. [Delete](azacsnap-cmd-ref-delete.md).
- **`-c restore`** , anlık görüntüyü temel alan yeni bir birim oluşturarak ya da bir birimi bir önizleme durumuna geri yükleyerek bir birime anlık görüntü geri yüklemek için iki yöntem sunar.
  - **`--restore snaptovol`** Hedef birimdeki en son anlık görüntüye göre yeni bir birim oluşturur.
  - **`-c restore --restore revertvolume`** Hedef birimi en son anlık görüntüye göre önceki bir duruma geri döndürür.
  - [geri yükleme komut başvurusunu](azacsnap-cmd-ref-restore.md)inceleyin.
- **`[--configfile <configfilename>]`** Farklı bir JSON yapılandırma dosya adı sağlamak için isteğe bağlı komut satırı parametresi.  Bu, SID başına ayrı bir yapılandırma dosyası oluşturmak için özellikle yararlıdır (ör `--configfile H80.json` .).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanmaya başlama](azacsnap-get-started.md)
