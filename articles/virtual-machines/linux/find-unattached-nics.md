---
title: Eklenmemiş Azure NIC 'Leri bulma ve silme
description: Azure CLı ile sanal makinelere eklenmemiş Azure NIC 'leri bulma ve silme
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 5b28226cfe6bc51a2619c4dd63e666ddd51dad2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016208"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Azure VM 'Leri için eklenmemiş ağ arabirim kartlarını (NIC 'Ler) bulma ve silme
Azure 'da bir sanal makineyi (VM) sildiğinizde, ağ arabirimi kartları (NIC 'ler) varsayılan olarak silinmez. Birden çok VM oluşturup silerseniz kullanılmayan NIC 'Ler iç IP adresi kiralamalarını kullanmaya devam eder. Diğer VM NIC 'Leri oluştururken, alt ağın adres alanında bir IP kirası edinemeyebilir. Bu makalede, eklenmemiş NIC 'Leri bulma ve silme işlemlerinin nasıl yapılacağı gösterilir.

## <a name="find-and-delete-unattached-nics"></a>Eklenmemiş NIC’leri bulma ve silme

Bir NIC 'in *VirtualMachine* ÖZELLIĞI, NIC 'nin bağlı olduğu VM 'nin kimliğini ve kaynak grubunu depolar. Aşağıdaki betik, bir abonelikteki tüm NIC 'lerde döngü gerçekleştirir ve *VirtualMachine* özelliğinin null olup olmadığını denetler. Bu özellik null ise, NIC bir VM 'ye eklenmez.

Eklenmemiş tüm NIC 'Leri görüntülemek için, önce betiği *Deleteunattachednic* değişkeniyle birlikte *0* olarak çalıştırmanız önerilir. Liste çıkışını gözden geçirdikten sonra, eklenmemiş tüm NIC 'Leri silmek için betiği *Deleteunattachednıc* 'ler ile *1*' e çalıştırın.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da sanal ağlar oluşturma ve yönetme hakkında daha fazla bilgi için bkz. [VM ağlarını oluşturma ve yönetme](tutorial-virtual-network.md).
