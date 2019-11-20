---
title: NFSv 4.1 varsayılan etki alanını Azure NetApp Files için yapılandırın | Microsoft Docs
description: Azure NetApp Files ile NFSv 4.1 kullanmak için NFS istemcisinin nasıl yapılandırılacağını açıklar.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906292"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Azure NetApp Files için NFSv 4.1 varsayılan etki alanını yapılandırın

NFSv4, bir kimlik doğrulama etki alanı kavramını tanıtır. Azure NetApp Files Şu anda hizmetten NFS istemcisine yalnızca kök Kullanıcı eşlemesini desteklemektedir. NFSv 4.1 işlevini Azure NetApp Files birlikte kullanmak için NFS istemcisini güncelleştirmeniz gerekir.

## <a name="default-behavior-of-usergroup-mapping"></a>Kullanıcı/Grup eşlemesinin varsayılan davranışı

NFSv4 etki alanı `localdomain`olarak ayarlandığı için kök eşleme varsayılan olarak `nobody` kullanıcısına ayarlanır. Bir Azure NetApp Files NFSv 4.1 birimini kök olarak bağladığınızda, dosya izinlerini şöyle görürsünüz:  

![NFSv 4.1 için Kullanıcı/Grup eşlemesinin varsayılan davranışı](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Yukarıdaki örnekte gösterildiği gibi, `file1` kullanıcısının `root`olması gerekir, ancak varsayılan olarak `nobody` eşlenir.  Bu makalede `file1` kullanıcının `root`nasıl ayarlanacağı gösterilmektedir.  

## <a name="steps"></a>Adımlar 

1. NFS istemcisinde `/etc/idmapd.conf` dosyasını düzenleyin.   
    Satır `#Domain` açıklamasını kaldırın (diğer bir deyişle, satırdan `#` kaldırın) ve değer `localdomain` değeri `defaultv4iddomain.com`olarak değiştirin. 

    İlk yapılandırma: 
    
    ![NFSv 4.1 için başlangıç yapılandırması](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Yapılandırma güncelleştirildi:
    
    ![NFSv 4.1 için yapılandırma güncelleştirildi](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Bağlı olan tüm NFS birimlerinin bağlantısını çıkarın.
3. `/etc/idmapd.conf` dosyasını güncelleştirin.
4. `rpcbind` hizmetini konakta yeniden başlatın (`service rpcbind restart`) veya yalnızca konağı yeniden başlatın.
5. NFS birimlerini gereken şekilde bağlayın.   

    Bkz. [Windows veya Linux sanal makineleri için bir birimi bağlama veya çıkarma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

Aşağıdaki örnek, sonuçta elde edilen Kullanıcı/Grup değişikliğini göstermektedir: 

![NFSv 4.1 için sonuç yapılandırması](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Örnekte gösterildiği gibi, Kullanıcı/Grup artık `nobody` `root`olarak değiştirilmiştir.

## <a name="behavior-of-other-non-root-users-and-groups"></a>Diğer (kök olmayan) kullanıcıların ve grupların davranışı

Azure NetApp Files, NFSv 4.1 birimlerindeki dosyalarla veya klasörlerle ilişkili izinlere sahip olan yerel kullanıcıları (bir konakta yerel olarak oluşturulan kullanıcılar) destekler. Ancak, hizmet şu anda birden çok düğümdeki kullanıcıları/grupları eşleştirmeyi desteklemez. Bu nedenle, bir konakta oluşturulan kullanıcılar varsayılan olarak başka bir konakta oluşturulan kullanıcılara eşlenmiyor. 

Aşağıdaki örnekte, `Host1` üç adet test Kullanıcı hesabı vardır (`testuser01`, `testuser02`, `testuser03`): 

![NFSv 4.1 için sonuç yapılandırması](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

`Host2`, test Kullanıcı hesaplarının oluşturulmadığını, ancak aynı birimin her iki konağa da bağlı olduğunu unutmayın:

![NFSv 4.1 için sonuç yapılandırması](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Sonraki adım 

[Windows veya Linux sanal makineleri için bir birimi bağlama veya çıkarma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

