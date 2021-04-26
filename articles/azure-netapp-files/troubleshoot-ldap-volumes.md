---
title: Azure NetApp Files için LDAP birimi sorunlarını giderme | Microsoft Docs
description: Azure NetApp Files için LDAP birimlerini yapılandırırken sahip olabileceğiniz hata koşullarının çözümlerini açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499028"
---
# <a name="troubleshoot-ldap-volume-issues"></a>LDAP birimi sorunlarını giderme

Bu makalede, LDAP birimlerini yapılandırırken sahip olabileceğiniz hata koşullarının çözümleri açıklanmaktadır.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>LDAP birimlerine yönelik hatalar ve çözümler

|     Hata koşulları    |     Çözümler    |
|-|-|
| Doğru olarak ldapEnabled ile bir SMB birimi oluşturulurken hata oluştu: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | LDAP özellikli bir SMB birimi oluşturamazsınız. <br> LDAP devre dışı bırakılmış SMB birimleri oluşturun. |
| Mevcut bir birimin ldapEnabled parametre değeri güncelleştirilirken hata oluştu: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  Birim oluşturduktan sonra LDAP seçenek ayarını değiştiremezsiniz. <br> Oluşturulan bir birimde LDAP seçenek ayarını güncelleştirmeyin. Ayrıntılar için bkz. [Configure for NFS Volume Access için genişletilmiş gruplar Ile LDAP ekler](configure-ldap-extended-groups.md) . |
| LDAP özellikli bir NFS birimi oluşturulurken hata oluştu: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  DNS ulaşılamaz olduğu için bu hata oluşur. <br> <ul><li> Azure NetApp Files için doğru siteyi (site kapsamları) yapılandırılıp yapılandırılmadığını denetleyin. </li><li> DNS 'nin ulaşılamaz olmasının nedeni, yanlış bir DNS IP adresi veya ağ sorunları olabilir. Doğru olduğundan emin olmak için AD bağlantısına girilen DNS IP adresini denetleyin. </li><li> AD ve birimin aynı bölgede ve aynı VNet 'te bulunduğundan emin olun. Bunlar farklı VNET 'lerde yer alıyorsa, iki sanal ağ arasında VNet eşlemesi kurulduğundan emin olun.</li></ul> |
| Anlık görüntüden birim oluşturulurken hata oluştu: <br> `Aggregate does not exist` | Azure NetApp Files, LDAP devre dışı bir birime ait olan bir anlık görüntüden yeni, LDAP özellikli bir birimin sağlanması desteklenmez. <br> Verilen anlık görüntüden LDAP devre dışı yeni bir birim oluşturmayı deneyin. |

## <a name="next-steps"></a>Sonraki adımlar  

* [Yapılandırma, NFS birim erişimi için genişletilmiş gruplarla LDAP ekler](configure-ldap-extended-groups.md)
* [Azure NetApp Files için NFS birimi oluşturma](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files için bir çift protokol (NFSv3 ve SMB) birimi oluşturun](create-volumes-dual-protocol.md)