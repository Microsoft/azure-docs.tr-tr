---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanarak yedekleme | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının yedekleme komutunu çalıştırmaya yönelik bir kılavuz sağlar.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730947"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı 'nı (Önizleme) kullanarak yedekleme

Bu makalede, Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının yedekleme komutunu çalıştırmaya yönelik bir kılavuz sunulmaktadır.

## <a name="introduction"></a>Giriş

Bir depolama anlık görüntüsü tabanlı yedekleme, komutu kullanılarak çalıştırılır `azacsnap -c backup` .  Bu komut, VERI birimlerinde veritabanı tutarlı bir depolama anlık görüntüsünün düzenlemesini ve DIĞER birimlerde bir depolama anlık görüntüsünü (veritabanı tutarlılık kurulumu olmadan) gerçekleştirir.  

VERI birimleri için `azacsnap` , veritabanını bir depolama anlık görüntüsüne hazırlar, ardından yapılandırılan tüm birimler için depolama anlık görüntüsünü alır, son olarak veritabanına anlık görüntünün tamamlandığını tavsiye edilir.  Ayrıca, anlık görüntü yedekleme etkinliğini (ör. SAP HANA yedekleme kataloğu) kaydeden tüm veritabanı girişlerini de yönetir.

## <a name="command-options"></a>Komut seçenekleri

`-c backup`Komut aşağıdaki bağımsız değişkenleri alır:

- `--volume=`anlık görüntüye ait birimin türü, bu parametre veya içerebilir `data``other`
  - `data` yapılandırma dosyasının dataVolume Stanza içindeki birimleri görüntüler.
  - `other` yapılandırma dosyasının otherVolume Stanza içindeki birimleri görüntüler.
  
  > [!NOTE]
  > Diğer birim olarak önyükleme birimi ile ayrı bir yapılandırma dosyası oluşturarak, `boot` anlık görüntülerin tamamen farklı bir zamanlamaya göre (örneğin, günlük) alınması mümkündür.

- `--prefix=` anlık görüntü adı için müşteri anlık görüntüsü ön eki. Bu parametrenin iki amacı vardır. İlk amaç, anlık görüntülerin gruplandırılması için benzersiz bir ad sağlamaktır. `--retention`Belirtilen için tutulan depolama anlık görüntülerinin sayısını belirleme `--prefix` .

    > [!IMPORTANT]
    > Yalnızca alfa sayısal ("A-Z, A-z, 0-9"), alt çizgi ("_") ve tire ("-") karakterlerine izin verilir.

- `--retention` Saklanacak tanımlanan anlık görüntü sayısı `--prefix` . Bunun için yeni bir anlık görüntü alındıktan sonra herhangi bir ek anlık görüntü kaldırılır `--prefix` .

- `--trim` SAP HANA v2 ve üzeri sürümlerde kullanılabilen bu seçenek, yedekleme kataloğunu ve disk kataloğu ve günlük yedeklemelerini korur. Yedekleme kataloğunda tutulacak giriş sayısı `--retention` Yukarıdaki seçeneğe göre belirlenir ve tanımlanan önek () için eski girişleri `--prefix` yedekleme kataloğundan ve ilgili fiziksel günlük yedekinden siler. Ayrıca, günlük olmayan en eski yedekleme girişinden daha eski olan günlük yedekleme girişlerini de siler. Bu işlemler, günlük yedeklerinin tüm kullanılabilir disk alanını kullanmasını önlemeye yardımcı olur.

  > [!NOTE]
  > Aşağıdaki örnek komut, 9 depolama anlık görüntülerini tutar ve Yedekleme kataloğunun, korunan 9 depolama anlık görüntülerinin eşleşmesi için sürekli olarak kırpılmakta olduğundan emin olur.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` ya da SAP HANA ile iletişim kurmak için kullanılan şifreleme yöntemini tanımlayan isteğe bağlı bir parametre `openssl` `commoncrypto` . Tanımlanmışsa, `azacsnap -c backup` komut aynı dizinde iki dosya bulmayı bekler, bu dosyaların karşılık gelen SID 'den sonra adlandırılması gerekir. [SAP HANA iletişim IÇIN SSL kullanma](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)bölümüne bakın. Aşağıdaki örnek, `hana` öneki olan bir tür anlık görüntüsünü alır `hana_TEST` ve `9` SSL () kullanarak SAP HANA iletişim kurmasını sağlayacaktır `openssl` .

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` , özel yapılandırma dosyası adlarına izin veren isteğe bağlı bir parametredir.

## <a name="snapshot-backups-are-fast"></a>Anlık görüntü yedeklemeleri hızlıdır

Bir anlık görüntü yedeğinin süresi, 10 GB 'lik bir birimle aynı yaklaşık bir süre içinde geçen 10 TB 'lik bir birim ile birim boyutundan bağımsızdır.  

Genel yürütme süresini etkileyen başlıca faktörler, anlık görüntü yapılacak birimlerin sayısıdır ve parametresindeki tüm değişiklikler `--retention` (fazla anlık görüntüler kaldırıldığında, bir azalmanın yürütme süresini artırabileceği).

Yukarıdaki örnek yapılandırmada ( **Azure büyük örnek** için), iki birimin anlık görüntülerinin tamamlanması 5 saniyeden daha az sürdü. **Azure NetApp Files** için, iki birim için anlık görüntüler yaklaşık 60 saniye sürer.

> [!NOTE]
> `--retention`Önceki saatten önemli ölçüde düşürüleceğinden `azacsnap` (örneğin, öğesinden `--retention 50` `--retention 5` ), geçen süre, `azacsnap` ek anlık görüntüleri kaldırmak için gereken süre artar.

## <a name="example-with-data-parameter"></a>Parametreli örnek `data`

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

Komut konsola çıkış yapmaz, ancak bir günlük dosyasına, bir sonuç dosyasına ve bir dosyasına yazar `/var/log/messages` .

*Günlük dosyası* , +-c seçeneği + yapılandırma dosya adı ' nın altında oluşturulur. Varsayılan olarak `-c backup` , varsayılan bir yapılandırma dosya adı ile bir çalıştırma için günlük dosya adı `azacsnap-backup-azacsnap.log` .

*Sonuç* dosyası, günlük dosyası ile aynı temel ada sahiptir (örneğin, soneki), `.result` Örneğin, `azacsnap-backup-azacsnap.result` aşağıdaki çıktıyı içerir:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

`/var/log/messages`Dosya, dosyayla aynı çıktıyı içerir `.result` . Aşağıdaki örneğe bakın (farklı çalıştır kökü):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Parametreli örnek `other`

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

Komut konsola çıkış yapmaz, ancak yalnızca bir günlük dosyasına yazar.  Bir sonuç  dosyasına veya `/var/log/messages` .

*Günlük dosyası* , +-c seçeneği + yapılandırma dosya adı ' nın altında oluşturulur. Varsayılan olarak `-c backup` , varsayılan bir yapılandırma dosya adı ile bir çalıştırma için günlük dosya adı `azacsnap-backup-azacsnap.log` .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Parametresi ile örnek `other` (konak işletim sistemini yedeklemek için)

> [!NOTE]
> Yalnızca önyükleme birimlerini içeren başka bir yapılandırma dosyası ( `--configfile bootVol.json` ) kullanımı.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

Komut konsola çıkış yapmaz, ancak yalnızca bir günlük dosyasına yazar.  Bir sonuç  dosyasına veya `/var/log/messages` .

Bu örnekteki *günlük dosyası* adı `azacsnap-backup-bootVol.log` .

> [!NOTE]
> Günlük dosyası adı "(komut adı-( `-c` seçenek)-(yapılandırma dosya adı)" öğesinden oluşur.  Örneğin, `-c backup` bir günlük dosyası adı ile seçeneğini kullanıyorsanız `h80.json` , günlük dosyası çağrılır `azacsnap-backup-h80.log` .  Ya da `-c test` seçeneği aynı yapılandırma dosyası ile kullanılıyorsa günlük dosyası çağrılır `azacsnap-test-h80.log` .

- HANA büyük örnek türü: `TYPEI` `TYPEII` Hana büyük örnek birimine sahip veya bağımlı iki geçerli değer vardır.
- Kullanılabilir SKU 'Ları onaylamak için bkz. [Hana büyük örnekleri Için kullanılabilir SKU 'lar](../virtual-machines/workloads/sap/hana-available-skus.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Anlık görüntü ayrıntılarını al](azacsnap-cmd-ref-details.md)
- [Anlık görüntüleri Sil](azacsnap-cmd-ref-delete.md)