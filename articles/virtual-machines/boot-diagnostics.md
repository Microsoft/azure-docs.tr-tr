---
title: Azure önyükleme tanılaması
description: Azure önyükleme tanılaması ve yönetilen önyükleme tanılama 'ya genel bakış
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 030f19d61e19349de7e2d3416a3aee1385fd03c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607532"
---
# <a name="azure-boot-diagnostics"></a>Azure önyükleme tanılaması

Önyükleme Tanılaması, Azure sanal makineler (VM) için VM önyükleme hatalarının tanılanmasına izin veren bir hata ayıklama özelliğidir. Önyükleme Tanılaması, seri günlük bilgilerini ve ekran görüntülerini toplayarak bir kullanıcının VM 'nin durumunu gözlemlemeye olanak sağlar.

## <a name="boot-diagnostics-storage-account"></a>Önyükleme tanılama depolama hesabı
Azure portal içinde bir sanal makine oluştururken, önyükleme tanılaması varsayılan olarak etkindir. Önerilen önyükleme tanılama deneyimi, bir Azure VM oluşturma zamanında önemli performans geliştirmeleri elde eden bir yönetilen depolama hesabı kullanmaktır. Bunun nedeni, bir Azure yönetilen depolama hesabının kullanılması, önyükleme tanılama verilerini depolamak için yeni bir kullanıcı depolama hesabı oluşturmak için gereken süreyi ortadan kaldırır.

> [!IMPORTANT]
> Önyükleme tanılaması veri Blobları (günlüklerin ve anlık görüntü görüntülerinin bulunduğu) yönetilen bir depolama hesabında depolanır. Müşteriler, diskin sağlanan boyutunda değil, Bloblar tarafından yalnızca kullanılan GiBs üzerinden ücretlendirilecektir. Anlık görüntü ölçümleri yönetilen depolama hesabının faturalandırılması için kullanılacaktır. Yönetilen hesaplar standart LRS veya standart ZRS üzerinde oluşturulduğundan, müşteriler yalnızca tanılama veri Blobları için aylık olarak $0,$ GB üzerinden ücretlendirilir. Bu fiyatlandırma hakkında daha fazla bilgi için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/). Müşteriler bu ücreti VM Kaynak URI 'sine bağlı olarak görür. 

Alternatif bir önyükleme tanılama deneyimi, Kullanıcı tarafından yönetilen bir depolama hesabı kullanmaktır. Bir Kullanıcı yeni bir depolama hesabı oluşturabilir ya da var olan bir depolama hesabı kullanabilir.
> [!NOTE]
> Önyükleme tanılamaları ile ilişkili kullanıcı tarafından yönetilen depolama hesapları, depolama hesabının ve ilişkili sanal makinelerin aynı abonelikte bulunmasını gerektirir. 



## <a name="boot-diagnostics-view"></a>Önyükleme tanılaması görünümü
Sanal makine dikey penceresinde bulunan önyükleme tanılama seçeneği Azure portal *destek ve sorun giderme* bölümünün altındadır. Önyükleme tanılaması seçildiğinde, bir ekran görüntüsü ve seri günlüğü bilgileri görüntülenir. Seri günlüğü çekirdek mesajlaşma içerir ve ekran görüntüsü, VM 'Lerin geçerli durumunun bir anlık görüntüsüdür. VM 'nin çalışıyor olması veya Linux 'un, beklenen ekran görüntüsünün nasıl görüneceğini belirler. Windows için, kullanıcılar bir masaüstü arka planı ve Linux için, kullanıcılar bir oturum açma istemi görür.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux önyükleme tanılaması ekran görüntüsü":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows önyükleme tanılaması 'nın ekran görüntüsü":::

## <a name="enable-managed-boot-diagnostics"></a>Yönetilen önyükleme tanılamayı etkinleştir 
Yönetilen önyükleme tanılaması Azure portal, CLı ve ARM şablonları aracılığıyla etkinleştirilebilir. PowerShell aracılığıyla etkinleştirme henüz desteklenmiyor. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Azure portal kullanarak yönetilen önyükleme tanılamayı etkinleştirme
Azure portal bir VM oluştururken, varsayılan ayar, bir yönetilen depolama hesabı kullanarak önyükleme Tanılamanın etkinleştirilmesini sağlar. Bunu görüntülemek için VM oluşturma sırasında *Yönetim* sekmesine gidin. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="VM oluşturma sırasında yönetilen önyükleme tanılamayı etkinleştiren ekran görüntüsü.":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>CLı kullanarak yönetilen önyükleme tanılamayı etkinleştirme
Yönetilen depolama hesabıyla önyükleme Tanılaması, Azure CLı 2.12.0 ve sonrasında desteklenir. Depolama hesabı için bir ad veya URI girişi yapmazsanız, yönetilen bir hesap kullanılacaktır. Daha fazla bilgi ve kod örneği için bkz. [önyükleme tanılaması Için CLI belgeleri](/cli/azure/vm/boot-diagnostics).

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Azure Resource Manager (ARM) şablonları kullanarak yönetilen önyükleme tanılamayı etkinleştirme
API sürüm 2020-06-01 ' den sonraki her şey yönetilen önyükleme tanılamayı destekler. Daha fazla bilgi için bkz. [önyükleme tanılama örneği görünümü](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Sınırlamalar
- Önyükleme tanılaması yalnızca Azure Resource Manager VM 'Ler için kullanılabilir.
- Yönetilen önyükleme Tanılaması, yönetilmeyen işletim sistemi disklerini kullanan VM 'Leri desteklemez.
- Önyükleme tanılaması için Premium depolama hesabı kullanılıyorsa, önyükleme tanılaması Premium Depolama hesaplarını desteklemez `StorageAccountTypeNotSupported` . 
- Yönetilen depolama hesapları, Kaynak Yöneticisi API sürümü "2020-06-01" ve üzeri sürümlerde desteklenir.
- Azure seri konsol Şu anda önyükleme tanılaması için yönetilen bir depolama hesabıyla uyumsuz. [Azure seri konsolu](/troubleshoot/azure/virtual-machines/serial-console-overview)hakkında daha fazla bilgi edinin.
- Portal yalnızca tek örnekli VM 'Ler için yönetilen bir depolama hesabıyla birlikte önyükleme tanılaması kullanımını destekler.

## <a name="next-steps"></a>Sonraki adımlar

Azure [seri konsolu](/troubleshoot/azure/virtual-machines/serial-console-overview) ve [Azure 'da sanal makinelerde sorun gidermek](/troubleshoot/azure/virtual-machines/boot-diagnostics)için önyükleme tanılamayı kullanma hakkında daha fazla bilgi edinin.