---
title: Azure Backup için VM Snapshot Linux uzantısı
description: VM Snapshot Linux uzantısı kullanarak Azure Backup sanal makinenin uygulamayla tutarlı yedeklemesini yapın.
services: backup, virtual-machines
documentationcenter: ''
author: trinadhkotturu
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: ea984fdc4abeb08f4b080e913a0c34b99c59d93c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561118"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Azure Backup için VM Snapshot Linux uzantısı



Azure Backup, iş yüklerini Şirket içinden buluta yedeklemeye ve bulut kaynaklarını kurtarma hizmetleri kasasına yedeklemeye yönelik destek sağlar. Azure Backup VM 'yi kapatmaya gerek kalmadan Azure sanal makinesinin uygulamayla tutarlı bir yedeklemesini yapmak için VM anlık görüntü uzantısı 'nı kullanır. VM Snapshot Linux uzantısı, Azure Backup hizmeti kapsamında Microsoft tarafından yayımlanır ve desteklenir. Azure Backup, uzantıyı ilk zamanlanan yedeklemenin bir parçası olarak yükleyecek ve yedeklemeyi etkinleştirmeyecektir. Bu belgede VM anlık görüntüsü uzantısı için desteklenen platformlar, konfigürasyonlar ve dağıtım seçenekleri ayrıntılı olarak bulunmaktadır.

VMSnapshot uzantısı, yalnızca yönetilmeyen VM 'Ler için Azure portal görüntülenir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi
Desteklenen işletim sistemlerinin listesi için lütfen [Azure Backup tarafından desteklenen Işletim sistemlerine](../../backup/backup-azure-arm-vms-prepare.md#before-you-start) başvurun

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON, VM Snapshot uzantısı için şemayı gösterir. Uzantı, görev KIMLIĞI gerektirir; Bu işlem, VM 'de anlık görüntünün tetiklediği yedekleme işini tanımlar, durum blobu URI 'si-anlık görüntü işleminin durumunun yazıldığı, anlık görüntünün zamanlanan başlangıç saati, anlık görüntü göreviyle ilgili Günlükler, blob URI 'si, objstr-VM disklerinin ve meta verilerin gösterimi.  Bu ayarların hassas veriler olarak değerlendirilmesi gerektiğinden, korumalı bir ayar yapılandırmasında depolanmalıdır. Azure VM Uzantısı korumalı ayar verileri şifrelenir ve yalnızca hedef sanal makinede şifresi çözülür. Lütfen bu ayarların Azure Backup hizmetinden yalnızca yedekleme işinin bir parçası olarak geçirilmesi önerilir.

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

| Name | Değer/örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| TaskID | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| commandStartTimeUTCTicks | 6.36458 e + 17 | string |
| locale | tr-tr | string |
| objectStr | Encoding of sas uri array- "blobSASUri": ["https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee \/ vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları, Azure Resource Manager şablonlarıyla dağıtılabilir. Ancak, bir sanal makineye VM anlık görüntü uzantısı eklemenin önerilen yolu, sanal makinede yedekleme etkinleştiriliyor. Bu, bir Kaynak Yöneticisi şablonu aracılığıyla elde edilebilir.  [Azure hızlı başlangıç galerisinde](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)bir sanal makinede yedeklemeye izin veren bir örnek kaynak yöneticisi şablonu bulunabilir.


## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı

Azure CLı, bir sanal makinede yedeklemeyi etkinleştirmek için kullanılabilir. Yedeklemeyi etkinleştirme, ilk zamanlanmış yedekleme işi VM 'ye VM anlık görüntüsü uzantısını yükler.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal ve Azure CLı kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için, Azure CLı 'yı kullanarak aşağıdaki komutu çalıştırın.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uzantı yürütme çıkışı aşağıdaki dosyaya kaydedilir:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Hata kodları ve anlamları

Sorun giderme bilgileri, [Azure VM yedeklemesi sorun giderme kılavuzunda](../../backup/backup-azure-vms-troubleshoot.md)bulunabilir.

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
