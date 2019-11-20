---
title: Azure Backup için VM Snapshot Linux uzantısı
description: VM anlık görüntü uzantısı kullanarak Azure Backup sanal makinenin uygulamayla tutarlı yedeklemesini gerçekleştirin
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhk
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 186468119fb5b630b56a91b38026f202b98630d6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072918"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Azure Backup için VM Snapshot Linux uzantısı



Azure Backup, iş yüklerini Şirket içinden buluta yedeklemeye ve bulut kaynaklarını kurtarma hizmetleri kasasına yedeklemeye yönelik destek sağlar. Azure Backup VM 'yi kapatmaya gerek kalmadan Azure sanal makinesinin uygulamayla tutarlı bir yedeklemesini yapmak için VM anlık görüntü uzantısı 'nı kullanır. VM Snapshot Linux uzantısı, Azure Backup hizmeti kapsamında Microsoft tarafından yayımlanır ve desteklenir. Azure Backup, uzantıyı ilk zamanlanan yedeklemenin bir parçası olarak yükleyecek ve yedeklemeyi etkinleştirmeyecektir. Bu belgede VM anlık görüntüsü uzantısı için desteklenen platformlar, konfigürasyonlar ve dağıtım seçenekleri ayrıntılı olarak bulunmaktadır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi
Desteklenen işletim sistemlerinin listesi için lütfen [Azure Backup tarafından desteklenen Işletim sistemlerine](../../backup/backup-azure-arm-vms-prepare.md#before-you-start) başvurun

### <a name="internet-connectivity"></a>İnternet bağlantısı

VM anlık görüntüsü uzantısı, sanal makinenin bir yedeğini alırken hedef sanal makinenin internet 'e bağlanmasını gerektirir.

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON, VM Snapshot uzantısı için şemayı gösterir. Uzantı, görev KIMLIĞI gerektirir; Bu, VM üzerinde başlatılan anlık görüntüyü, durum blobu URI 'sini; anlık görüntü işleminin durumunun yazıldığı, anlık görüntünün zamanlanan başlangıç zamanı, anlık görüntünün zamanlanan başlangıç saati, anlık görüntü görevine karşılık gelen Günlükler olan Günlükler yazılır, objstr-VM disklerinin ve meta verilerin temsili.  Bu ayarların hassas veriler olarak değerlendirilmesi gerektiğinden, korumalı bir ayar yapılandırmasında depolanmalıdır. Azure VM uzantısının korumalı ayarı veriler şifrelenir ve yalnızca hedef sanal makinede şifresi. Lütfen bu ayarların Azure Backup hizmetinden yalnızca yedekleme işinin bir parçası olarak geçirilmesi önerilir.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| TaskID | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| commandStartTimeUTCTicks | 6.36458 e + 17 | string |
| ayarlar | tr-tr | string |
| objectStr | SAS URI dizisi kodlaması-"blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/VHD 'leri\/vmubuntu1404ltsc201652903941. vhd? ZF = 2014-02-14 & SR = b & Sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW", "https:\/\/sopattna8461.blob.core.windows.net\/VHD 'ler\/vmubuntu1404ltsc-20160629-122418. vhd? ZF = 2014-02-14 & SR = b & SIG = 5S0a6ydwvemqpakzwxvy% 2BS% 2FqMwzFMbamT5upwx05v8Q% 3B & St = 2017-11-09T14% 3A23% 3A28Z & b = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48in-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541. vhd? ZF = 2014-02-14 & SR = b & Sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4% 3D & St = 2017-11-09T14% 3A23% 3A28Z & b = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna5365.blob.core.windows.net\/VHD 'ler\/vmubuntu1404ltsc-20160701-163922. vhd? ZF = 2014-02-14 & SR = b & Sig = oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r% 2BC% 2BNIAork% 3B & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna5365.blob.core.windows.net\/VHD 'ler\/vmubuntu1404ltsc-20170705-124311. vhd? ZF = 2014-02-14 & SR = b & SIG = ZUM9d28Mvvm% 2FfrhJ71TFZh0Ni90m38bBs3zMl% 2FQ9rs0% 3B & St = 2017-11-09T14% 3A23% 3A28Z & b = 2017-11-09T17% 3A38% 3A28Z & SP = RW "] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir. Ancak, bir sanal makineye VM anlık görüntü uzantısı eklemenin önerilen yolu, sanal makinede yedekleme etkinleştiriliyor. Bu, bir Kaynak Yöneticisi şablonu aracılığıyla elde edilebilir.  [Azure hızlı başlangıç galerisinde](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)bir sanal makinede yedeklemeye izin veren bir örnek kaynak yöneticisi şablonu bulunabilir.


## <a name="azure-cli-deployment"></a>Azure CLI dağıtım

Azure CLı, bir sanal makinede yedeklemeyi etkinleştirmek için kullanılabilir. Yedeklemeyi etkinleştirme, ilk zamanlanmış yedekleme işi VM 'ye VM anlık görüntüsü uzantısını yükler.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtım durumuyla ilgili veriler, Azure portalından ve Azure CLI kullanılarak alınabilir. Belirli bir VM'nin için uzantıları dağıtım durumunu görmek için Azure CLI kullanarak aşağıdaki komutu çalıştırın.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uzantı yürütme çıkış aşağıdaki dosyasına kaydedilir:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Hata kodları ve anlamları

Sorun giderme bilgileri, [Azure VM yedeklemesi sorun giderme kılavuzunda](../../backup/backup-azure-vms-troubleshoot.md)bulunabilir.

### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).
