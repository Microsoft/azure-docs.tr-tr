---
title: Azure NetApp Files kullanarak NFS birimi için dışarı aktarma ilkesini yapılandırma | Microsoft Docs
description: Azure NetApp Files kullanarak bir NFS birimine erişimi denetlemek için, dışa aktarma ilkesinin nasıl yapılandırılacağını açıklar.
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
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d323bd0b9684cfe4930d8c779a6728fcfd3836fb
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674922"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>NFS birimine yönelik dışarı aktarma ilkesini yapılandırma

İsteğe bağlı olarak Azure NetApp Files birimine erişimi denetlemek için dışarı aktarma ilkesi yapılandırabilirsiniz. Azure NetApp Files dışarı aktarma ilkesi yalnızca NFS birimlerini destekler.  Hem NFSv3 hem de NFSv4 desteklenir. 

## <a name="steps"></a>Adımlar 

1.  Azure NetApp Files gezinti bölmesinden **Ilkeyi dışarı aktar** ' a tıklayın. 

2.  İlke kuralını oluşturmak veya dışarı aktarmak için aşağıdaki alanların bilgilerini belirtin:   
    *  **Dizin**   
        Kuralın dizin numarasını belirtin.  
        Dışarı aktarma ilkesi en fazla beş kural içerir. Kurallar, dizin numaraları listesindeki sıraya göre değerlendirilir. Dizin numarası en düşük olanlar ilk değerlendirilir. Örneğin dizin numarası 1 olan kural dizin numarası 2 olan kuraldan önce değerlendirilir. 

    * **İzin Verilen İstemciler**   
        Değeri belirtirken aşağıdaki biçimlerden birini kullanın:  
        * IPv4 adresi, örneğin `10.1.12.24` 
        * Bit numaraları olarak belirtilen alt ağ maskesine sahip IPv4 adresi, örneğin `10.1.12.10/4`

    * **Erişim**  
        Aşağıdaki erişim türlerinden birini seçin:  
        * Erişim Yok 
        * Okuma ve Yazma
        * Salt Okunur

    ![İlkeyi dışarı aktarma](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Sonraki adımlar 
* [Birimleri yönetme](azure-netapp-files-manage-volumes.md)
* [Sanal makineler için birimi bağlama veya ayırma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Anlık görüntüleri yönetme](azure-netapp-files-manage-snapshots.md)
