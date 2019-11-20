---
title: Azure ağ Izleyicisi özelliklerini kullanmak için gereken izinler | Microsoft Docs
description: Ağ Izleyicisi özellikleri ile çalışmak için hangi Azure rol tabanlı erişim denetimi izinlerinin gerekli olduğunu öğrenin.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 5bd7e30a6a95d60bda4b7c3da44be1b8046bb71f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163789"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Ağ Izleyicisi yeteneklerini kullanmak için gereken rol tabanlı erişim denetimi izinleri

Azure rol tabanlı erişim denetimi (RBAC), kuruluşunuzun kendilerine atanan sorumlulukları tamamlaması için ihtiyaç duydukları üyelere yalnızca belirli eylemleri atamanızı sağlar. Ağ Izleyicisi yeteneklerini kullanmak için, Azure 'da oturum açarken kullandığınız hesap [sahibine](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [katkıda bulunan](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)veya [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) yerleşik rollerine atanmalıdır ya da şuna listelenen eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) atanmalıdır izleyen bölümlerde bulunan her bir ağ Izleyicisi özelliği. Ağ Izleyicisi 'nin özellikleri hakkında daha fazla bilgi edinmek için bkz. [Ağ İzleyicisi nedir?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Ağ İzleyicisi

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/Read                              | Ağ İzleyicisi edinme                                          |
| Microsoft. Network/networkWatchers/Write                             | Ağ İzleyicisi oluşturma veya güncelleştirme                             |
| Microsoft. Network/networkWatchers/Delete                            | Ağ İzleyicisi silme                                       |

## <a name="nsg-flow-logs"></a>NSG akış günlükleri

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/Configuyeniden Akıtılme günlüğü/eylemi           | Akış günlüğü yapılandırma                                           |
| Microsoft. Network/networkWatchers/queryFlowLogStatus/Action         | Akış günlüğü için sorgu durumu                                    |

## <a name="connection-troubleshoot"></a>Bağlantı sorunlarını giderme

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectivityCheck/Action          | Bir bağlantı başlatma test sorunlarını giderme
| Microsoft. Network/networkWatchers/Querybir TResult/Action    | Bir bağlantının sorgu sonuçları test sorunlarını giderme                |
| Microsoft. Network/networkWatchers/sorun giderme/eylem               | Bir bağlantı çalıştırın test sorunlarını giderme                             |

## <a name="connection-monitor"></a>Bağlantı izleyicisi

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/Connectionizleyicileri/Başlat/eylem   | Bağlantı İzleyicisi başlatma                                     |
| Microsoft. Network/networkWatchers/Connectionizleyicileri/durdur/eylem    | Bağlantı izleyicisini durdur                                      |
| Microsoft. Network/networkWatchers/Connectionizleyicileri/sorgu/eylem   | Bağlantı izleyicisini sorgulama                                     |
| Microsoft. Network/networkWatchers/Connectionmonitörleri/okuma           | Bağlantı İzleyicisi al                                       |
| Microsoft. Network/networkWatchers/Connectionmonitörleri/yazma          | Bağlantı izleyicisi oluşturma                                    |
| Microsoft. Network/networkWatchers/Connectionizleyicileri/silme         | Bağlantı izleyicisini silme                                    |

## <a name="packet-capture"></a>Paket yakalaması

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/Packetyakalamaları/queryStatus/Action | Paket yakalamanın durumunu sorgulama                           |
| Microsoft. Network/networkWatchers/Packetyakalamaları/durdur/eylem        | Paket yakalamayı durdur                                          |
| Microsoft. Network/networkWatchers/Packetyakalamaları/Read               | Paket yakalaması alma                                           |
| Microsoft. Network/networkWatchers/Packetyakalamaları/yazma              | Paket yakalaması oluşturma                                        |
| Microsoft. Network/networkWatchers/Packetyakalamaları/silme             | Paket yakalamayı silme                                        |

## <a name="ip-flow-verify"></a>IP akışı doğrulama

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/ipFlowVerify/Action               | Bir IP akışını doğrulama                                              |

## <a name="next-hop"></a>Sonraki atlama

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/Sonrakii/eylem                    | Bir VM 'den sonraki atlamayı al                                     |

## <a name="network-security-group-view"></a>Ağ güvenlik grubu görünümü

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/securityGroupView/Action          | Güvenlik gruplarını görüntüle                                           |

## <a name="topology"></a>Topoloji

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/topoloji/eylem                   | Topolojiyi al                                                   |

## <a name="reachability-report"></a>Ulaşılabilirlik raporu

| Action                                                              | Açıklama                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/azureReachabilityReport/Action    | Azure ulaşılabilirlik raporu alın                               |


## <a name="additional-actions"></a>Ek eylemler

Ağ Izleyicisi özellikleri aşağıdaki eylemleri de gerektirir:

| Eylem (ler)                                                           | Açıklama                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/\*/Read                                     | RBAC rol atamalarını ve ilke tanımlarını getirmek için kullanılır          |
| Microsoft. resources/abonelikler/resourceGroups/Read               | Bir abonelikteki tüm kaynak gruplarını listelemek için kullanılır    |
| Microsoft. Storage/storageAccounts/Read                              | Belirtilen depolama hesabının özelliklerini almak için kullanılır   |
| Microsoft. Storage/storageAccounts/listServiceSas/eylem, </br> Microsoft. Storage/storageAccounts/listAccountSas/eylem, <br> Microsoft. Storage/storageAccounts/listKeys/Action| [Depolama hesabına güvenli erişim](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) ve depolama hesabına yazma olanağı sağlayan paylaşılan erişim IMZALARıNı (SAS) getirmek için kullanılır |
| Microsoft. COMPUTE/virtualMachines/Read, </br> Microsoft. COMPUTE/virtualMachines/Write| VM 'de oturum açmak için kullanılan bir paket yakalama yapın ve depolama hesabına yükleyin|
| Microsoft. COMPUTE/virtualMachines/Extensions/okuma </br> Microsoft. COMPUTE/virtualMachines/uzantılar/Write| Ağ Izleyicisi uzantısının mevcut olup olmadığını denetlemek ve gerekirse yüklemek için kullanılır |
| Microsoft. COMPUTE/virtualMachineScaleSets/Read, </br> Microsoft. COMPUTE/virtualMachineScaleSets/Write| Sanal makine ölçek kümelerine erişmek için kullanılır, paket yakalar ve depolama hesabına yükler|
| Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read, </br> Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write| Ağ Izleyicisi uzantısının mevcut olup olmadığını denetlemek ve gerekirse yüklemek için kullanılır |
| Microsoft.Insights/alertRules/*                                     | Ölçüm uyarılarını ayarlamak için kullanılır                                     |
| Microsoft. support/*                                                 | Ağ izleyicisinden destek bileti oluşturmak ve güncelleştirmek için kullanılır |
