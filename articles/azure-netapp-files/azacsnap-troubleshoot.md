---
title: Azure NetApp Files için Azure uygulamayla tutarlı anlık görüntü aracı sorunlarını giderme | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanarak ilgili sorun giderme içeriği sağlar.
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
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 7ba5a05515284612e17d5aba4cc673c7e78f7ba1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869931"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulamayla tutarlı anlık görüntü aracında sorun giderme (Önizleme)

Bu makalede, Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracını kullanma hakkında sorun giderme için içerik sunulmaktadır.

Komutları çalıştırırken karşılaşabileceğiniz yaygın sorunlar aşağıda verilmiştir. Sorunu çözmek için bahsedilen çözüm talimatlarını izleyin. Sorun yaşamaya devam ediyorsanız, Azure portal bir hizmet Isteği açın ve Microsoft Desteği yanıt vermesi için isteği SAP HANA büyük örnek kuyruğuna atayın.

## <a name="log-files"></a>Günlük dosyaları

AzAcSnap ile hata ayıklama için en iyi bilgi kaynaklarından biri günlük dosyalarıdır.  

### <a name="log-file-location"></a>Günlük dosyası konumu

Günlük dosyaları, `logPath` AzAcSnap yapılandırma dosyasındaki parametresi başına yapılandırılan dizinde depolanır.  Varsayılan yapılandırma dosya adı `azacsnap.json` ve varsayılan değeri, `logPath` `"./logs"` günlük dosyalarının `./logs` komutun çalıştırıldığı konuma göre dizine yazıldığı anlamına gelir `azacsnap` .  `logPath`Mutlak bir konum (örn.) yapmak, `/home/azacsnap/logs` `azacsnap` `/home/azacsnap/logs` komutun nerede çalıştırıldıklarına bakılmaksızın günlüklerin çıkış yapmasını sağlayacaktır `azacsnap` .

### <a name="log-file-naming"></a>Günlük dosyası adlandırma

Günlük dosya adı, uygulama adını (ör.), kullanılan komut seçeneğini (örneğin,,, `azacsnap` `-c` `backup` `test` `details` vb.) ve yapılandırma dosya adını (örneğin, varsayılan = `azacsnap.json` ) temel alır.  Bu nedenle `-c backup` , komut kullanılıyorsa, varsayılan olarak günlük dosya adı olur `azacsnap-backup-azacsnap.log` ve tarafından yapılandırılan dizine yazılır `logPath` .  

Bu adlandırma kuralı, her veritabanı için bir tane olmak üzere birden çok yapılandırma dosyasına izin vermek ve ilişkili günlük dosyalarını bulmanın kolaylığını sağlamak için oluşturulmuştur.  Bu nedenle, yapılandırma dosya adı ise, `SID.json` Seçenekler kullanılırken sonuç dosya adı olacaktır `azacsnap -c backup --configfile SID.json` `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>Sonuç dosyası ve Syslog

`-c backup`Komut seçeneği Için AzAcSnap komutuna, `*.result` komutunu kullanarak bir dosyaya ve sistem günlüğüne ( `/var/log/messages` ) yazar `logger` .  `*.result`Dosya adı, [günlük dosyası](#log-file-naming) ile aynı temel ada sahiptir ve [günlük dosyasıyla aynı konuma](#log-file-location)gider.  Aşağıdaki örneklere göre basit bir satır çıkış dosyasıdır.

Dosyadan çıkış örneği `*.result` .
```output
Database # 1 (PR1) : completed ok
```

Dosyadan çıkış örneği `/var/log/messages` .
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Azure NetApp Files ile iletişim kurulamadı

Azure NetApp Files ile iletişim doğrulanırken, iletişim başarısız olabilir veya zaman aşımına uğrar.  Güvenlik Duvarı kurallarının AzAcSnap çalıştıran sistemden aşağıdaki adreslere ve TCP/IP bağlantı noktalarına giden trafiği engellemediğinden emin olmak için denetleyin:-

- (https://) Management. Azure. com: 443
- (https://) login. microsoftonline. com: 443 

## <a name="failed-communication-with-sap-hana"></a>SAP HANA ile iletişim kurulamadı

İle bir test çalıştırarak SAP HANA ile iletişim doğrulanırken `azacsnap -c test --test hana` , aşağıdaki hatayı sağlar:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Çözüm:**

1. `azacsnap.json`SAP HANA veritabanı değerlerinin doğru olduğundan emin olmak için her BIR Hana örneği için yapılandırma dosyasını (örneğin,) denetleyin.
1. `hdbsql`Komutun yolda olup olmadığını ve SAP HANA sunucusuna bağlanıp bağlanamadiğini doğrulamak için aşağıdaki komutu çalıştırmayı deneyin. Aşağıdaki örnek, komutun ve çıktısının doğru çalışmasını gösterir.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    Bu örnekte, `hdbsql` komut kullanıcılarda değildir `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    Bu örnekte, `hdbsql` komut geçici olarak kullanıcının uygulamasına eklenir `$PATH` , ancak çalıştırıldığında bağlantı anahtarı komutuyla doğru şekilde ayarlanmadı `hdbuserstore Set` (Ayrıntılar için Başlarken kılavuzuna bakın):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Kullanıcıya kalıcı olarak eklemek için `$PATH` kullanıcının `$HOME/.profile` dosyasını güncelleştirin

## <a name="the-hdbuserstore-location"></a>`hdbuserstore`Konum

SAP HANA ile iletişim kurarken, `hdbuserstore` Program güvenli iletişim ayarlarını oluşturmak için kullanılır.  `hdbuserstore`Program genellikle veya altında bulunur `/usr/sap/<SID>/SYS/exe/hdb/` `/usr/sap/hdbclient` .  Normalde yükleyici, kullanıcının öğesine doğru konumu ekler `azacsnap` `$PATH` .

## <a name="failed-test-with-storage"></a>Depolama ile başarısız test

Komut `azacsnap -c test --test storage` başarıyla tamamlanmadı.

### <a name="azure-large-instance"></a>Azure büyük örnek

Aşağıdaki örnek `azacsnap` Azure büyük örneğindeki SAP HANA üzerinde çalışır:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Çözüm:** Yukarıdaki hata, genellikle Azure büyük örnek depolama kullanıcısının temeldeki depolamaya erişimi olmadığında görüntülenir. Depolama ile belirtilen depolama kullanıcısına erişimi doğrulamak için, `ssh` depolama platformuyla iletişimi doğrulamak üzere komutunu çalıştırın.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Beklenen çıktıya sahip bir örnek:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>' 172.18.18.11 (172.18.18.11) ' konağının gerçekliği oluşturulamıyor

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Çözüm:** Evet ' i seçmeyin. Depolama IP adresinizin doğru olduğundan emin olun. Sorun devam ederse, Microsoft operasyon ekibinin bulunduğu depolama IP adresini onaylayın.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Aşağıdaki örnek, `azacsnap` Azure NetApp Files kullanarak BIR VM üzerinde çalışır:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Çözüm:**

1. `azureauth.json`Yapılandırma dosyasında ayarlandığı gibi, hizmet sorumlusu dosyasının mevcut olup olmadığını denetleyin `azacsnap.json` .
1. `logs/azacsnap-test-azacsnap.log`Hizmet sorumlusu ( `azureauth.json` ) doğru içeriğe sahip olup olmadığını görmek için günlük dosyasını (örneğin,) denetleyin.  Günlükte aşağıdaki gibi bir örnek:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. `logs/azacsnap-test-azacsnap.log`Hizmet sorumlusunun ( `azureauth.json` ) sona erdiğinden emin olmak için günlük dosyasına (örneğin,) bakın. Günlükte aşağıdaki gibi bir örnek:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Sonraki adımlar

- [İpuçları](azacsnap-tips.md)
