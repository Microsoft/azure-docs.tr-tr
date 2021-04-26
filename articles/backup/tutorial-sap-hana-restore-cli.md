---
title: Öğretici-CLı kullanarak Azure 'da DB geri yükleme SAP HANA
description: Bu öğreticide, Azure CLı kullanarak bir Azure Backup kurtarma hizmetleri kasasından Azure VM üzerinde çalışan SAP HANA veritabanlarını geri yüklemeyi öğrenin.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a249ab63aa72c1d39ab1626e72ff3b2037f3f723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768460"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Öğretici: Azure CLı kullanarak Azure VM 'de SAP HANA veritabanlarını geri yükleme

Azure CLı, komut satırından veya betiklerden Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bu belgelerde, Azure CLı kullanarak bir Azure VM 'de yedeklenmiş SAP HANA veritabanını geri yükleme ayrıntıları yer alır. Ayrıca, [Azure Portal](./sap-hana-db-restore.md)kullanarak da bu adımları uygulayabilirsiniz.

CLı komutlarını çalıştırmak için [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) kullanın.

Bu öğreticinin sonuna kadar şunları yapabilirsiniz:

> [!div class="checklist"]
>
> * Yedeklenen bir veritabanının geri yükleme noktalarını görüntüleme
> * Veritabanını geri yükleme

Bu öğreticide, Azure VM 'de Azure Backup kullanılarak yedeklenen SAP HANA bir veritabanı olduğunu varsaymaktadır. SAP HANA veritabanınızı yedeklemek için [CLI kullanarak Azure 'da bir SAP HANA veritabanı yedeklemeyi](tutorial-sap-hana-backup-cli.md) kullandıysanız aşağıdaki kaynakları kullanıyorsunuz:

* *Saphanaresourcegroup* adlı bir kaynak grubu
* *Saphanavault* adlı bir kasa
* *Vmappcontainer adlı korumalı kapsayıcı; İşlem; saphanaResourceGroup; saphanaVM*
* saphanadatabase adlı yedeklenen veritabanı/öğe *; hxe; hxe*
* *westus2* bölgesindeki kaynaklar

## <a name="view-restore-points-for-a-backed-up-database"></a>Yedeklenen bir veritabanının geri yükleme noktalarını görüntüleme

Bir veritabanı için tüm kurtarma noktalarının listesini görüntülemek için [az Backup RecoveryPoint List](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) cmdlet 'ini aşağıdaki gibi kullanın:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Kurtarma noktalarının listesi aşağıdaki gibi görünür:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Gördüğünüz gibi, yukarıdaki listede üç kurtarma noktası bulunur: her biri tam, fark ve günlük yedeklemesi için.

>[!NOTE]
>[Az Backup RecoveryPoint Show-log-zincir](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) cmdlet 'ini kullanarak, tüm kopuk günlük yedekleme zincirlerinin başlangıç ve bitiş noktalarını da görüntüleyebilirsiniz.

## <a name="prerequisites-to-restore-a-database"></a>Veritabanını geri yükleme önkoşulları

Bir veritabanını geri yüklemeden önce aşağıdaki önkoşulların karşılandığından emin olun:

* Veritabanını yalnızca aynı bölgedeki bir SAP HANA örneğine geri yükleyebilirsiniz
* Hedef örnek, kaynakla aynı kasada kayıtlı olmalıdır
* Azure Backup aynı VM 'de iki farklı SAP HANA örneği tanımlayamıyor. Bu nedenle, verileri aynı VM 'de bir örnekten diğerine geri yüklemek mümkün değildir.

## <a name="restore-a-database"></a>Veritabanını geri yükleme

Azure Backup, Azure VM 'lerinde çalışan SAP HANA veritabanlarını şu şekilde geri yükleyebilir:

* Günlük yedeklemeleri kullanarak belirli bir tarih veya saate (ikinci olarak) geri yükleyin. Azure Backup, uygun tam, değişiklik yedeklemelerini ve seçilen saate göre geri yüklemek için gereken günlük yedeklemeleri zincirini otomatik olarak belirler.
* Belirli bir kurtarma noktasına geri yüklemek için belirli bir tam veya değişiklik yedeklemesine geri yükleme yapın.

Bir veritabanını geri yüklemek için, girdilerden biri olarak bir kurtarma yapılandırma nesnesi gerektiren [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) cmdlet 'ini kullanın. Bu nesne [az Backup recoveryconfig Show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) cmdlet 'i kullanılarak oluşturulabilir. Kurtarma yapılandırma nesnesi, geri yükleme gerçekleştirmek için tüm ayrıntıları içerir. Bunlardan biri geri yükleme modu: **originalworkloadresdeposu** veya **alternateworkloadresrestore**.

>[!NOTE]
> **Originalworkloadresstore** -verileri özgün kaynakla aynı SAP HANA örneğine geri yükleyin. Bu seçenek özgün veritabanının üzerine yazar. <br>
> **Alternateworkloadresstore** -veritabanını alternatif bir konuma geri yükleyin ve özgün kaynak veritabanını koruyun.

## <a name="restore-to-alternate-location"></a>Alternatif konuma geri yükle

Bir veritabanını alternatif bir konuma geri yüklemek için, geri yükleme modu olarak **Alternateworkloadresdeposu** ' nu kullanın. Ardından, önceki bir nokta veya önceki geri yükleme noktalarından biri olabilecek geri yükleme noktasını seçmeniz gerekir.

Bu öğreticide, önceki bir geri yükleme noktasına geri yükleyeceksiniz. Veritabanının [geri yükleme noktalarının listesini görüntüleyin](#view-restore-points-for-a-backed-up-database) ve geri yüklemek istediğiniz noktayı seçin. Bu öğretici, geri yükleme noktasını *7660777527047692711* adıyla kullanacaktır.

Yukarıdaki geri yükleme noktası adını ve geri yükleme modunu kullanarak, [az Backup recoveryconfig Show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) cmdlet 'ini kullanarak kurtarma yapılandırma nesnesini oluşturalım. Bu cmdlet 'teki kalan parametrelerin her birinin ne anlama geldiğini inceleyelim:

* **--Target-öğe-adı** Bu, geri yüklenen veritabanının kullanacağı addır. Bu durumda *restored_database* adı kullandık.
* **--target-sunucu-adı** Bu, bir kurtarma hizmetleri kasasında başarıyla kaydedilen ve geri yüklenecek veritabanıyla aynı bölgede yer alan bir SAP HANA sunucusunun adıdır. Bu öğreticide, veritabanını, *hxehost* adlı koruduğumuz SAP HANA sunucuya geri yükleyeceğiz.
* **--target-sunucu türü** SAP HANA veritabanlarının geri yüklenmesi için **Hanaınstance** kullanılması gerekir.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

Yukarıdaki sorguya olan yanıt, şuna benzer bir kurtarma yapılandırma nesnesi olacaktır:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Şimdi veritabanını geri yüklemek için [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) cmdlet 'ini çalıştırın. Bu komutu kullanmak için, *üzerinderecoveryconfig.js* adlı bir dosyaya kaydedilen yukarıdaki JSON çıkışını gireceğiz.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Çıktı şöyle görünür:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Yanıt size iş adı verecektir. Bu iş adı [az Backup Job Show](/cli/azure/backup/job#az_backup_job_show) cmdlet 'ini kullanarak iş durumunu izlemek için kullanılabilir.

## <a name="restore-and-overwrite"></a>Geri yükleme ve üzerine yazma

Özgün konuma geri yüklemek için geri yükleme modu olarak **OrignialWorkloadRestore** kullanacağız. Ardından, önceki bir nokta veya önceki geri yükleme noktalarından biri olabilecek geri yükleme noktasını seçmeniz gerekir.

Bu öğreticide, geri yüklemek için önceki "28-11-2019-09:53:00" zaman noktasını seçeceğiz. Bu geri yükleme noktasını şu biçimlerde sağlayabilirsiniz: gg-aa-yyyy, gg-aa-yyyy-HH: mm: ss. Geri yüklenecek geçerli bir zaman noktası seçmek için, bozulan günlük zinciri yedeklerinin aralıklarını listeleyen [az Backup RecoveryPoint Show-log-zincir](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) cmdlet 'ini kullanın.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Yukarıdaki sorguya olan yanıt, aşağıdaki gibi görünen bir kurtarma yapılandırma nesnesi olacaktır:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

Şimdi veritabanını geri yüklemek için [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) cmdlet 'ini çalıştırın. Bu komutu kullanmak için, *üzerinderecoveryconfig.js* adlı bir dosyaya kaydedilen yukarıdaki JSON çıkışını gireceğiz.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Çıktı şöyle görünür:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Yanıt size iş adı verecektir. Bu iş adı, [az Backup Job Show](/cli/azure/backup/job#az_backup_job_show) cmdlet 'ini kullanarak iş durumunu izlemek için kullanılabilir.

## <a name="restore-as-files"></a>Dosya olarak geri yükleme

Yedekleme verilerini bir veritabanı yerine dosya olarak geri yüklemek için geri yükleme modu olarak **Restoreasdosyalarýný** kullanacağız. Ardından, önceki bir nokta veya önceki geri yükleme noktalarından biri olabilecek geri yükleme noktasını seçin. Dosyalar belirtilen bir yola alındıktan sonra, bu dosyaları bir veritabanı olarak geri yüklemek istediğiniz herhangi bir SAP HANA makineye alabilirsiniz. Bu dosyaları herhangi bir makineye taşıyabildiğiniz için artık verileri abonelikler ve bölgeler arasında geri yükleyebilirsiniz.

Bu öğreticide, geri yükleme için bir önceki zaman noktasını `28-11-2019-09:53:00` ve yedekleme dosyalarının dökümünü almak için aynı SAP HANA sunucusuna kadar olan konumu seçeceğiz `/home/saphana/restoreasfiles` . Bu geri yükleme noktasını aşağıdaki biçimlerden birinde sağlayabilirsiniz: **gg-aa-yyyy** veya **gg-aa-yyyy-HH: mm: ss**. Geri yüklenecek geçerli bir zaman noktası seçmek için, bozulan günlük zinciri yedeklerinin aralıklarını listeleyen [az Backup RecoveryPoint Show-log-zincir](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) cmdlet 'ini kullanın.

Yukarıdaki geri yükleme noktası adını ve geri yükleme modunu kullanarak, [az Backup recoveryconfig Show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) cmdlet 'ini kullanarak kurtarma yapılandırma nesnesini oluşturalım. Bu cmdlet 'teki kalan parametrelerin her birinin ne anlama geldiğini inceleyelim:

* **--target-Container-Name** Bu, bir kurtarma hizmetleri kasasında başarıyla kaydedilen ve geri yüklenecek veritabanıyla aynı bölgede yer alan bir SAP HANA sunucusunun adıdır. Bu öğreticide, veritabanını dosya olarak, koruduğumuz aynı SAP HANA sunucusuna geri yükleyeceğiz, *hxehost* adlı.
* **--RP-adı** Bir zaman noktaya geri yükleme için geri yükleme noktası adı **DefaultRangeRecoveryPoint** olur

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

Yukarıdaki sorguya olan yanıt, aşağıdaki gibi görünen bir kurtarma yapılandırma nesnesi olacaktır:

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

Şimdi, veritabanını dosyalar olarak geri yüklemek için [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) cmdlet 'ini çalıştırın. Bu komutu kullanmak için, *üzerinderecoveryconfig.js* adlı bir dosyaya kaydedilen JSON çıkışını girmeniz gerekir.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

Çıktı şöyle görünür:

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Yanıt size iş adı verecektir. Bu iş adı, [az Backup Job Show](/cli/azure/backup/job#az_backup_job_show) cmdlet 'ini kullanarak iş durumunu izlemek için kullanılabilir.

Hedef kapsayıcıya dökülebilir dosyalar şunlardır:

* Veritabanı yedekleme dosyaları
* Katalog dosyaları
* JSON meta veri dosyaları (dahil edilen her yedekleme dosyası için)

Genellikle, bir ağ paylaşımının yolu veya hedef yolu olarak belirtildiğinde bağlı bir Azure dosya paylaşımının yolu, bu dosyalara aynı ağdaki diğer makineler veya bunlara bağlı aynı Azure dosya paylaşımıyla daha kolay erişim sağlar.

>[!NOTE]
>Veritabanı yedekleme dosyalarını hedef kayıtlı VM 'ye bağlanmış bir Azure dosya paylaşımında geri yüklemek için, kök hesabın Azure dosya paylaşımında okuma/yazma izinlerine sahip olduğundan emin olun.

Seçilen geri yükleme noktası türüne göre (**zaman içinde** veya **tam & fark**), hedef yolunda oluşturulmuş bir veya daha fazla klasör görürsünüz. Adlı klasörlerden biri `Data_<date and time of restore>` , tam ve fark yedeklemelerini ve adlı diğer klasörü, `Log` günlük yedeklemelerini içerir.

Bu geri yüklenen dosyaları bir veritabanı olarak geri yüklemek istediğiniz SAP HANA sunucuya taşıyın. Ardından, veritabanını geri yüklemek için şu adımları izleyin:

1. Yedekleme dosyalarının depolandığı klasör/dizin üzerinde aşağıdaki komutu kullanarak izinleri ayarlayın:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Sonraki komut kümesini şu şekilde Çalıştır `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Geri yükleme için katalog dosyasını oluşturun. Geri yükleme işleminde daha sonra kullanılacak olan tam yedekleme için, JSON meta veri dosyasından **BackupID** 'yi ayıklayın. Tam ve günlük yedeklemelerin farklı klasörlerde bulunduğundan emin olun ve bu klasörlerdeki katalog dosyalarını ve JSON meta veri dosyalarını silin.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    Yukarıdaki komutta:

    * `<DataFileDir>` -tam yedeklemeleri içeren klasör
    * `<LogFilesDir>` -günlük yedeklemelerini içeren klasör
    * `<PathToPlaceCatalogFile>` -Katalog dosyasının oluşturulduğu klasör yerleştirilmesi gerekir

1. Yeni oluşturulan katalog dosyasını HANA Studio aracılığıyla geri yükleyin veya bu yeni oluşturulan katalogla HDBSQL restore sorgusunu çalıştırın. HDBSQL sorguları aşağıda listelenmiştir:

    * Zaman içinde bir noktaya geri yüklemek için:

        Geri yüklenen yeni bir veritabanı oluşturuyorsanız, HDBSQL komutunu çalıştırarak yeni bir veritabanı oluşturun `<DatabaseName>` ve ardından veritabanını geri yükleme için durdurun. Ancak, yalnızca var olan bir veritabanını geri yüklüyorsanız, veritabanını durdurmak için HDBSQL komutunu çalıştırın.

        Ardından, veritabanını geri yüklemek için aşağıdaki komutu çalıştırın:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` -Geri yüklemek istediğiniz yeni veritabanının veya var olan veritabanının adı
        * `<Timestamp>` -Zaman içindeki noktaya geri yükleme için tam zaman damgası
        * `<DatabaseName@HostName>` -Backup 'ın geri yükleme için kullanıldığı veritabanının adı ve bu veritabanının bulunduğu **konak** /SAP HANA sunucu adı. `USING SOURCE <DatabaseName@HostName>`Seçeneği, veri yedeklemesinin (geri yükleme için kullanılan), hedef SAP HANA makineden farklı bır SID veya ada sahip bir veritabanı olduğunu belirtir. Bu nedenle, yedeklemenin alındığı aynı HANA sunucusunda yapılan geri yüklemeler için belirtilmesi gerekmez.
        * `<PathToGeneratedCatalogInStep3>`- **3. adımda** oluşturulan katalog dosyasının yolu
        * `<DataFileDir>` -tam yedeklemeleri içeren klasör
        * `<LogFilesDir>` -günlük yedeklemelerini içeren klasör
        * `<BackupIdFromJsonFile>`- **3. adımda** ayıklanan **BackupID**

    * Belirli bir tam veya değişiklik yedeklemesine geri yüklemek için:

        Geri yüklenen yeni bir veritabanı oluşturuyorsanız, HDBSQL komutunu çalıştırarak yeni bir veritabanı oluşturun `<DatabaseName>` ve ardından veritabanını geri yükleme için durdurun. Ancak, yalnızca var olan bir veritabanını geri yüklüyorsanız, veritabanını durdurmak için HDBSQL komutunu çalıştırın:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` -geri yüklemek istediğiniz yeni veritabanının veya var olan veritabanının adı
        * `<Timestamp>` -zaman içinde nokta geri yükleme 'nin tam zaman damgası
        * `<DatabaseName@HostName>` -geri yükleme için yedekleme kullanılan veritabanının adı ve bu veritabanının bulunduğu **konak** /SAP HANA sunucu adı. `USING SOURCE <DatabaseName@HostName>`Seçeneği, veri yedeklemesinin (geri yükleme için kullanılan), hedef SAP HANA makineden farklı bır SID veya ada sahip bir veritabanı olduğunu belirtir. Bu nedenle, yedeklemenin alındığı aynı HANA sunucusunda yapılan geri yüklemeler için belirtilmemesi gerekir.
        * `<PathToGeneratedCatalogInStep3>`- **3. adımda** oluşturulan katalog dosyasının yolu
        * `<DataFileDir>` -tam yedeklemeleri içeren klasör
        * `<LogFilesDir>` -günlük yedeklemelerini içeren klasör
        * `<BackupIdFromJsonFile>`- **3. adımda** ayıklanan **BackupID**

## <a name="next-steps"></a>Sonraki adımlar

* Azure CLı kullanarak yedeklenen SAP HANA veritabanlarının nasıl yönetileceğini öğrenmek için, [CLI kullanarak Azure VM 'de SAP HANA veritabanını yönetme](tutorial-sap-hana-backup-cli.md) öğreticisine devam edin

* Azure VM 'de çalışan bir SAP HANA veritabanını Azure portal kullanarak geri yükleme hakkında bilgi edinmek için bkz. [Azure VM 'lerinde SAP HANA veritabanlarını geri yükleme](./sap-hana-db-restore.md)
