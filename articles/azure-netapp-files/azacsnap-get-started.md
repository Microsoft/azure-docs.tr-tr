---
title: Azure NetApp Files için Azure Application tutarlı anlık görüntü aracı ile çalışmaya başlama | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracı 'nı yüklemeye yönelik bir kılavuz sağlar.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736371"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanmaya başlama (Önizleme)

Bu makale, Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracı 'nı yüklemeye yönelik bir kılavuz sağlar.

## <a name="getting-the-snapshot-tools"></a>Anlık görüntü araçlarını alma

Önerilen müşteriler, Microsoft 'tan [Azacsnap yükleyicisinin](https://aka.ms/azacsnapdownload) en son sürümünü alırlar.

Kendi kendine yükleme dosyası, indirilen yükleyicinin GPG doğrulamasına izin vermek için Microsoft 'un ortak anahtarıyla imzalanmış ilişkili bir [Azacsnap yükleyicisi imza dosyasına](https://aka.ms/azacsnapdownloadsignature) sahiptir.

Bu İndirmeler tamamlandığında, yüklemek için bu kılavuzdaki adımları izleyin.

### <a name="verifying-the-download"></a>İndirme doğrulanıyor

Üzerinde indirilebilen yükleyicide dosya adı uzantısı ile ilişkili bir PGP imza dosyası bulunur `.asc` . Bu dosya, yükleyicinin indirildiği bir Microsoft tarafından sağlanmış dosya olduğundan emin olmak için kullanılabilir. Linux paketlerini imzalamak için kullanılan Microsoft PGP ortak anahtarı burada ( <https://packages.microsoft.com/keys/microsoft.asc> ) kullanılabilir ve imza dosyasını imzalamak için kullanılır.

Microsoft PGP ortak anahtarı, bir kullanıcının yerel öğesine şu şekilde aktarılabilir:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Aşağıdaki komutlar Microsoft PGP ortak anahtarına güvenir:

1. Depodaki anahtarları listeleyin.
2. Microsoft anahtarını düzenleyin.
3. Parmak izini denetleme `fpr`
4. Ona güvenmek için anahtarı imzalayın.

```bash
gpg --list-keys
```

Listelenen anahtarlar:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Etkileşimli `gpg` oturum Imzalama Microsoft ortak anahtarı çıkışı:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

Yükleyicinin PGP imza dosyası şu şekilde denetlenebilir:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

GPG kullanma hakkında daha fazla bilgi için bkz. [GNU gizlilik el kitabı](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Anlık görüntü araçları aşağıdaki senaryolarda kullanılabilir.

- Tek SID
- Birden çok SID
- HSR
- Ölçeği genişletme
- MDC (yalnızca tek kiracı destekleniyor)
- Tek Kapsayıcı
- SUSE Işletim sistemi
- RHEL Işletim sistemi
- SKU TÜRÜ ı
- SKU TÜRÜ ıı

[Hana büyük örnekleri için bkz. desteklenen senaryolar](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>SAP 'den anlık görüntü desteği matrisi

Aşağıdaki matris, depolama anlık görüntüsü yedeklemeleri için SAP tarafından desteklenen SAP HANA sürümlerinin bir kılavuz olarak sunulmaktadır.

| Veritabanı sürümleri       |1,0 SPS12|2,0 SPS0|2,0 SPS1|2,0 SPS2|2,0 SPS3|2,0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Tek kapsayıcı veritabanı| √       | √      | -      | -      | -      | -      |
|MDC tek kiracı        | -       | -      | √      | √      | √      | √      |
|MDC birden çok kiracı     | -       | -      | -      | -      | -      | √      |
> √ = <small>depolama anlık görüntüleri IÇIN SAP tarafından desteklenir</small>

## <a name="important-things-to-remember"></a>Dikkat etmeniz gereken önemli noktalar

- Anlık görüntü araçlarının kurulumundan sonra, kullanılabilir depolama alanını sürekli olarak izleyin ve gerekirse, depolama doldurmayı önlemek için düzenli olarak eski anlık görüntüleri silin.
- Her zaman en son anlık görüntü araçlarını kullanın.
- Aynı anlık görüntü araçları sürümünü yatay olarak kullanın.
- Anlık görüntü araçlarını test edin ve üretim sisteminde kullanmadan önce, gereken parametreleri ve komutun çıkışını rahat bir şekilde öğrenin.
- Bir yedekleme için HANA kullanıcısını ayarlarken (Bu belgede aşağıdaki Ayrıntılar), her bir HANA örneği için kullanıcıyı ayarlamanız gerekir. MDC için SYSTEMDB (SID veritabanında değil) altındaki HANA örneğine erişmek üzere bir SAP HANA Kullanıcı hesabı oluşturun. Tek kapsayıcı ortamında, Kiracı veritabanı altında ayarlanabilir.
- Müşterilerin, depolama erişimi için SSH ortak anahtarını sağlaması gerekir. Bu eylem, düğüm başına bir kez ve komutun yürütüldüğü her bir kullanıcı için yapılmalıdır.
- Birim başına anlık görüntü sayısı 250 ile sınırlıdır.
- Yapılandırma dosyasını el ile düzenlerseniz, her zaman Notepad gibi Windows düzenleyicilerinin değil, her zaman "VI" gibi bir Linux metin düzenleyicisi kullanın. Windows düzenleyicisini kullanarak dosya biçimi bozulabilir.
  - `hdbuserstore`SAP HANA kullanıcının SAP HANA iletişim kurması için ayarlayın.
- DR için: anlık görüntü araçları, DR 'nin ayarlanmadan önce DR düğümünde test edilmiş olmalıdır.
- Disk alanını düzenli olarak izleyin, otomatik günlük silme `--trim` ,   `azacsnap -c backup` SAP HANA 2 ve sonraki sürümleri için seçeneği ile yönetilir.
- Anlık **görüntülerin risk alınmıyor** -anlık görüntü araçları yalnızca yapılandırma dosyasında belirtilen SAP HANA sisteminin düğümüyle etkileşime geçin.  Bu düğüm kullanılamaz hale gelirse, otomatik olarak başka bir düğümle iletişim kurmaya başlamak için bir mekanizma yoktur.  
  - Bekleme senaryosunda bir **SAP HANA Scale-Out** için, anlık görüntü araçlarının ana düğüme yüklenmesi ve yapılandırılması normaldir. Ancak, ana düğüm kullanılamaz hale gelirse, bekleme düğümü ana düğüm rolünü ele alır. Bu durumda, hiçbir eksik anlık görüntüyü önlemek için uygulama ekibinin her iki düğümde (ana ve tek başına) anlık görüntü araçlarını yapılandırması gerekir. Normal durumda ana düğüm crontab tarafından başlatılan HANA anlık görüntülerini alır, ancak ana düğüm yük devretmeden sonra bu anlık görüntülerin yeni ana düğüm (eski bekleme) gibi başka bir düğümden yürütülmesi gerekir. Bu sonuca ulaşmak için, bekleme düğümünün, anlık görüntü aracının yüklü, depolama iletişimi etkinleştirilmiş, hdbuserstore yapılandırılmış, `azacsnap.json` yapılandırılmış ve crontab komutlarının yük devretmeyle önceden hazırlanmasına ihtiyacı vardır.
  - **SAP HANA HSR ha** senaryosu için hem (birincil hem de ikincil) düğümlerde anlık görüntü araçlarını yüklemek, yapılandırmak ve zamanlamak önerilir. Ardından, birincil düğüm kullanılamaz hale gelirse ikincil düğüm Ikincil üzerinde gerçekleştirilen anlık görüntülerle üzerinden sürer. Normal durumda, birincil düğüm crontab tarafından başlatılan HANA anlık görüntülerini alır ve Ikincil düğüm, birincil doğru şekilde çalıştığı için anlık görüntü alıp başarısız olur.  Ancak birincil düğüm yük devretmeden sonra bu anlık görüntüler Ikincil düğümden yürütülür. Bu sonuca ulaşmak için Ikincil düğümün, yük devretmeyle ilgili olarak, anlık görüntü aracının yüklü, yapılandırılmış `hdbuserstore` , yapılandırılmış ve yapılandırılmış azacsnap.jsve crontab etkinleştirilmiş olması gerekir.

## <a name="guidance-provided-in-this-document"></a>Bu belgede sunulan kılavuz

Anlık görüntü araçlarının kullanımını göstermek için aşağıdaki kılavuz verilmiştir.

### <a name="taking-snapshot-backups"></a>Anlık görüntü yedeklemeleri alınıyor

- [Depolama anlık görüntüsünün önkoşulları nelerdir?](azacsnap-installation.md#prerequisites-for-installation)
  - [Depolama ile iletişimi etkinleştir](azacsnap-installation.md#enable-communication-with-storage)
  - [SAP HANA ile iletişimi etkinleştir](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Anlık görüntüleri el ile alma](azacsnap-tips.md#take-snapshots-manually)
- [Otomatik anlık görüntü yedeklemesini ayarlama](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Anlık görüntüleri izleme](azacsnap-tips.md#monitor-the-snapshots)
- [Anlık görüntü nasıl silinir?](azacsnap-tips.md#delete-a-snapshot)
- [Anlık görüntü geri yükleme](azacsnap-tips.md#restore-a-snapshot)
- [Anlık görüntü geri yükleme `boot`](azacsnap-tips.md#restore-a-boot-snapshot)
- [Anlık görüntüler hakkında bilgi edinmek için önemli olgular nelerdir?](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Anlık görüntüler hem tek SID hem de birden çok SID için test edilir.

### <a name="performing-disaster-recovery"></a>Olağanüstü durum kurtarma gerçekleştiriliyor

- [DR kurulumu için Önkoşullar nelerdir?](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Olağanüstü durum kurtarma ayarlama](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Birincil sunucudan DR sitesine veri çoğaltmasını izleme](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [DR sitesine yük devretme işlemi nasıl yapılır?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure uygulaması tutarlı anlık görüntü aracı 'nı yükler](azacsnap-installation.md)