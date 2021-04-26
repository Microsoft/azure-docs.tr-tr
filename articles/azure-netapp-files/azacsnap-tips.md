---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanmaya yönelik ipuçları ve püf noktaları | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracını kullanmaya yönelik ipuçları ve püf noktaları sağlar.
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
ms.openlocfilehash: 6465acc0d4ce760e0bf89c73dace7c8c66d37c49
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869948"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı 'nı (Önizleme) kullanmaya yönelik ipuçları ve püf noktaları

Bu makale, AzAcSnap kullandığınızda yararlı olabilecek ipuçları ve püf noktaları sağlar.

## <a name="limit-service-principal-permissions"></a>Hizmet sorumlusu izinlerini sınırla

AzAcSnap hizmet sorumlusu kapsamının sınırlandırılacak olması gerekebilir.  Azure kaynakları hakkında ayrıntılı erişim yönetimi hakkında daha fazla bilgi için [Azure RBAC belgelerini](../role-based-access-control/index.yml) gözden geçirin.  

Aşağıda AzAcSnap to işlevi için gerekli olan en düşük eylemleri içeren örnek bir rol tanımı verilmiştir.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

Geri yükleme seçeneklerinin başarılı bir şekilde çalışması için AzAcSnap hizmeti sorumlusunun de birim oluşturabiliyor olması gerekir.  Bu durumda, rol tanımına ek bir eylem olması gerekir, bu nedenle tüm hizmet sorumlusu aşağıdaki örnekteki gibi görünmelidir.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>Anlık görüntüleri el ile al

Herhangi bir yedekleme komutunu () yürütmeden önce `azacsnap -c backup` , test komutlarını çalıştırarak yapılandırmayı denetleyin ve başarılı bir şekilde yürütüldüğünü doğrulayın.  Bu testlerin doğru yürütülmesi, `azacsnap` yüklü SAP HANA veritabanıyla ve **Azure büyük örneğindeki** veya **Azure NetApp Files** sisteminde SAP HANA temel alınan depolama sistemiyle iletişim kurabilir.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Daha sonra el ile veritabanı anlık görüntüsü yedeklemesi yapmak için aşağıdaki komutu çalıştırın:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Otomatik anlık görüntü yedeklemesini ayarla

Bu, `cron` bir sistemde çalışan komutları otomatik hale getirmek için kullanılan UNIX/Linux sistemlerinde yaygın bir uygulamadır. Anlık görüntü araçları için standart uygulama, kullanıcının kurulumunu kullanmaktır `crontab` .

`crontab`Kullanıcının `azacsnap` anlık görüntüleri otomatik hale getirmesi için bir örnek aşağıda verilmiştir.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Yukarıdaki crontab açıklaması.

- `MAILTO=""`: boş bir değere sahip olan bu, cron 'nin, yerel kullanıcının posta dosyasında büyük olasılıkla sona erdirmek için crontab girişini yürütürken otomatik olarak kullanıcıya e-posta almaya çalışmasını önler.
- Crontab girdileri için zamanlamaya ait toplu sürümler kendi kendine açıklayıcıdır:
  - `@monthly` = Ayda bir kez çalıştırın, yani "0 0 1 * *".
  - `@weekly`  = Haftada bir kez çalıştır, diğer bir deyişle, "0 0 * * 0".
  - `@daily`   = Günde bir kez çalıştır, yani "0 0 * * *".
  - `@hourly`  = Bir saatte bir kez çalıştırın, yani "0 * * * *".
- İlk beş sütun zamanları belirlemek için kullanılır, aşağıdaki sütun örneklerine başvurun:
  - `0,15,30,45`: 15 dakikada bir
  - `0-23`: Her saat
  - `*` : Her gün
  - `*` : Her ay
  - `*` : Haftanın her günü
- "()" Köşeli ayraçları içinde yürütülecek komut satırı
  - `. /home/azacsnap/.profile` = $PATH vb. dahil olmak üzere, kendi ortamını ayarlamak için kullanıcının. profilini çekin.
  - `cd /home/azacsnap/bin` = yürütme dizinini yapılandırma dosyalarının olduğu "/Home/azacsnap/bin" konumuyla değiştirin.
  - `azacsnap -c .....` = Tüm seçenekler dahil olmak üzere, çalıştırılacak tam azacsnap komutu.

Cron hakkında daha fazla açıklama ve crontab dosyasının biçimi burada verilmiştir: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Kullanıcılar, anlık görüntülerin başarıyla oluşturulduğundan emin olmak için cron işlerinin izlenmesinden sorumludur.

## <a name="monitor-the-snapshots"></a>Anlık görüntüleri izleme

Sağlıklı bir sistem sağlamak için aşağıdaki koşulların izlenmesi gerekir:

1. Kullanılabilir disk alanı. Anlık görüntüler, eski disk bloklarının anlık görüntüde saklanması durumunda disk alanını yavaş tüketir.
    1. Disk alanı yönetimini otomatik hale getirmeye yardımcı olmak için, `--retention` ve `--trim` seçeneklerini kullanarak eski anlık görüntüleri ve veritabanı günlük dosyalarını otomatik olarak temizleyin.
1. Anlık görüntü araçlarının başarılı yürütülmesi
    1. `*.result`En son çalıştırmanın başarısı veya başarısızlığı için dosyayı denetleyin `azacsnap` .
    1. `/var/log/messages`Komutun çıktısını denetleyin `azacsnap` .
1. Anlık görüntülerin tutarlılığı, düzenli aralıklarla başka bir sisteme geri yükler.

> [!NOTE]
> Anlık görüntü ayrıntılarını listelemek için komutunu yürütün `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Anlık görüntüyü silme

Bir anlık görüntüyü silmek için komutunu yürütün `azacsnap -c delete` . Anlık görüntülerin işletim sistemi düzeyinden silinmesi mümkün değildir. `azacsnap -c delete`Depolama anlık görüntülerini silmek için doğru komutu () kullanmanız gerekir.

> [!IMPORTANT]
> Bir anlık görüntüyü sildiğinizde dikkatli olun. Silindikten sonra, silinen anlık görüntüleri kurtarmak **olanaksızdır** .

## <a name="restore-a-snapshot"></a>Anlık görüntüyü geri yükleme

Bir depolama birimi anlık görüntüsü, yeni bir birime () geri yüklenebilir `-c restore --restore snaptovol` .  Azure büyük örnek için, birim bir anlık görüntüye () geri döndürülebilir `-c restore --restore revertvolume` .

> [!NOTE]
> Belirtilen veritabanı kurtarma komutu **yok** .

Bir anlık görüntü, SAP HANA veri alanına geri kopyalanabilir, ancak bir kopya () yapıldığında SAP HANA çalışmıyor olmalıdır `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` .

Azure büyük örnek için, mevcut kullanılabilir anlık görüntülerden istenen bir anlık görüntüyü geri yüklemek üzere bir hizmet isteği açarak Microsoft Operasyon ekibine başvurabilirsiniz. Azure portal bir hizmet isteği açabilirsiniz: <https://portal.azure.com>

Olağanüstü durum kurtarma yük devretmesini gerçekleştirmeye karar verirseniz, `azacsnap -c restore --restore revertvolume` Dr sitesindeki komut, `/hana/data` SAP HANA kurtarmaya izin vermek için en son (ve `/hana/logbackups` ) birim anlık görüntülerini otomatik olarak kullanılabilir hale getirir. Üretim ve DR siteleri arasında çoğaltmayı kesdiğinden bu komutu dikkatli kullanın.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Yalnızca ' önyükleme ' birimleri için anlık görüntüleri ayarla

> [!IMPORTANT]
> Bu işlem yalnızca Azure büyük örneği için geçerlidir.

Bazı durumlarda, müşterilerin SAP HANA koruma araçları zaten vardır ve yalnızca ' önyükleme ' birim anlık görüntülerini yapılandırmak ister.  Bu durumda, görev basitleştirilmiştir ve aşağıdaki adımlar gerçekleştirilmelidir.

1. Yükleme için önkoşulların 1-4. adımlarını uygulayın.
1. Depolama ile iletişimi etkinleştirin.
1. Anlık görüntü araçlarını yüklemek için yükleyiciyi çalıştırın öğesini indirin.
1. Anlık görüntü araçlarının kurulumunu tamamlar.
1. Yeni bir yapılandırma dosyasını aşağıda gösterildiği gibi oluşturun. Önyükleme birimi ayrıntıları, diğer toplu Stanza (Kullanıcı girdileri <span style="color:red">kırmızı</span>) olmalıdır:
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Yapılandırma dosyasını denetleyin, aşağıdaki örneğe bakın:

    `cat`Yapılandırma dosyasının içeriğini göstermek için komutunu kullanın:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t210_sles_boot_azsollabbl20a31_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. Önyükleme birimi yedeklemesini sınama

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Listelenmiş olup olmadığını kontrol edin ve `--snapshotfilter` döndürülen anlık görüntü listesini sınırlandırma seçeneğinin eklenmesi konusuna bakın.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Komut çıkışı:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Otomatik anlık görüntü yedeklemesini ayarlayın.

> [!NOTE]
> SAP HANA ile iletişim kurulması gerekli değildir.

## <a name="restore-a-boot-snapshot"></a>' Önyükleme ' anlık görüntüsünü geri yükleme

> [!IMPORTANT]
> Bu işlem Azure büyük örnek için.
> Anlık görüntü çekilirken sunucu noktaya geri yüklenecek.

' Önyükleme ' anlık görüntüsü şu şekilde kurtarılabilir:

1. Müşterinin sunucuyu kapatması gerekir.
1. Sunucu kapatıldıktan sonra, müşterinin geri yüklenecek makine KIMLIĞINI ve anlık görüntüsünü içeren bir hizmet isteği açması gerekir.
    > Müşteriler Azure portal bir hizmet isteği açabilir: <https://portal.azure.com>
1. Microsoft, belirtilen makine KIMLIĞINI ve anlık görüntüsünü kullanarak Işletim sistemi LUN 'unu geri yükleyecek ve ardından sunucuyu önyükleyecek.
1. Daha sonra müşterinin sunucu önyüklendiğini ve sağlıklı olduğunu doğrulamamalıdır.

Geri yüklemeden sonra gerçekleştirilecek ek adım yok.

## <a name="key-facts-to-know-about-snapshots"></a>Anlık görüntüler hakkında bilgi edinmek için önemli olgular

Depolama birimi anlık görüntülerinin anahtar öznitelikleri:

- **Anlık görüntülerin konumu**: anlık görüntüler, birim içindeki bir sanal dizinde ( `.snapshot` ) bulunabilir.  **Azure büyük örneği** için aşağıdaki örneklere bakın:
  - Veritabanınızı `/hana/data/<SID>/mnt00001/.snapshot`
  - Paylaşılan `/hana/shared/<SID>/.snapshot`
  - Açıldığında `/hana/logbackups/<SID>/.snapshot`
  - Önyükleme: HLı için önyükleme anlık görüntüleri işletim sistemi düzeyinden **görünmez** , ancak kullanılarak listelenebilir `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` , anlık görüntülere salt okuma erişimi sağlayan salt okunurdur gizli bir *sanal* klasördür.

- **En fazla anlık görüntü:** Donanım birim başına 250 anlık görüntüye dayanabilir. Snapshot komutu, komut satırındaki bekletme kümesine göre ön ek için en fazla sayıda anlık görüntü tutar ve saklanacak en fazla sayının ötesine geçse en eski anlık görüntüyü siler.
- **Anlık görüntü adı:** Anlık görüntü adı, müşteri tarafından sunulan önek etiketini içerir.
- **Anlık görüntü boyutu:** Veritabanı düzeyindeki boyut/değişikliklere göre değişir.
- **Günlük dosyası konumu:** Komutlar tarafından oluşturulan günlük dosyaları, varsayılan olarak komutun çalıştırıldığı bir alt klasör olan JSON yapılandırma dosyasında tanımlanan klasörlere çıktılardır (örneğin, `./logs` ).

## <a name="next-steps"></a>Sonraki adımlar

- [Sorun giderme](azacsnap-troubleshoot.md)
