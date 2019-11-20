---
title: Azure 'da SAP HANA için ek ağ gereksinimleri (büyük örnekler) | Microsoft Docs
description: Azure 'daki SAP HANA için ek ağ gereksinimleri (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fc4e797bd74c28fc741bf2a3928b46f0984b1b9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099935"
---
# <a name="additional-network-requirements-for-large-instances"></a>Büyük örnekler için ek ağ gereksinimleri

Azure üzerinde büyük SAP HANA örneklerinin dağıtılması kapsamında ek ağ gereksinimlerine sahip olabilirsiniz.

## <a name="add-more-ip-addresses-or-subnets"></a>Daha fazla IP adresi veya alt ağ ekleyin

Daha fazla IP adresi veya alt ağ eklerken Azure portal, PowerShell veya Azure CLı kullanın.

Yeni IP adresi aralığını yeni bir toplu Aralık oluşturmak yerine, sanal ağ adres alanına yeni bir Aralık olarak ekleyin. Bu değişikliği Microsoft 'a gönder. Bu, bu yeni IP adresi aralığından, istemcinizdeki HANA büyük örnek birimlerine bağlanmanızı sağlar. Yeni sanal ağ adresi alanının eklenmesini sağlamak için bir Azure destek isteği açabilirsiniz. Onay aldıktan sonra sonraki adımları gerçekleştirin.

Azure portal ek bir alt ağ oluşturmak için, bkz. [Azure Portal kullanarak sanal ağ oluşturma](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). PowerShell 'den bir tane oluşturmak için bkz. [PowerShell kullanarak sanal ağ oluşturma](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Sanal ağ ekle

İlk olarak bir veya daha fazla Azure sanal ağını bağladıktan sonra, Azure 'daki SAP HANA erişen ek bilgisayarlara (büyük örnekler) bağlanmak isteyebilirsiniz. İlk olarak, bir Azure destek isteği gönderebilirsiniz. Bu istekte, belirli Azure dağıtımını tanımlayan belirli bilgileri ekleyin. Ayrıca, Azure sanal ağ adresi alanının IP adresi alanı aralığını veya aralıklarını de ekleyin. Microsoft hizmet yönetiminde SAP HANA, ek sanal ağları ve Azure ExpressRoute 'u bağlamak için gereken bilgileri sağlar. Her sanal ağ için, ExpressRoute bağlantı hattına HANA büyük örneklerine bağlanmak için benzersiz bir yetkilendirme anahtarına ihtiyacınız vardır.

## <a name="increase-expressroute-circuit-bandwidth"></a>ExpressRoute devre bant genişliğini artırma

Microsoft hizmet yönetiminde SAP HANA başvurun. Azure 'da SAP HANA bant genişliğini (büyük örnekler) ExpressRoute devresine artırmanız için bir Azure destek isteği oluşturun. (En fazla 10 Gbps 'e kadar tek bir devre bant genişliği artışı isteyebilirsiniz.) Sonra işlem tamamlandıktan sonra bildirim alırsınız; Azure 'da bu daha yüksek hıza olanak tanımak için başka bir şey yapmanız gerekmez.

## <a name="add-an-additional-expressroute-circuit"></a>Ek bir ExpressRoute devresi ekleyin

Microsoft hizmet yönetiminde SAP HANA başvurun. Ek bir ExpressRoute bağlantı hattı eklemenizi öneriyorlarsa, bir Azure destek isteği (yeni bağlantı hattını bağlamak için yetkilendirme bilgileri almak için bir istek dahil) oluşturun. İsteği yapmadan önce, sanal ağlarda kullanılan adres alanını tanımlamanız gerekir. Microsoft hizmet yönetimi SAP HANA, yetkilendirme sağlayabilir.

Yeni devre oluşturulduğunda ve Microsoft hizmet yönetimi yapılandırmasındaki SAP HANA tamamlandığında, devam etmeniz için gereken bilgileri içeren bir bildirim alırsınız. Azure sanal ağlarını aynı Azure bölgesindeki Azure (büyük örnek) ExpressRoute bağlantı hattı üzerindeki başka bir SAP HANA zaten bağlandıysa bu ek devreye bağlanamazsınız.

## <a name="delete-a-subnet"></a>Bir alt ağı silme

Bir sanal ağ alt ağını kaldırmak için Azure portal, PowerShell veya Azure CLı kullanabilirsiniz. Azure sanal ağ IP adresi aralığınızı veya adres alanınızı toplanmış bir Aralık ise, Microsoft ile ilgili bir izleme yoktur. (Ancak, sanal ağın Silinen alt ağı içeren BGP yönlendirme adres alanını yaymakta olduğunu unutmayın.) Azure sanal ağ adresi aralığını veya adres alanını, Silinen alt ağınıza atanmış birden çok IP adresi aralığı olarak tanımlamış olabilirsiniz. Bunu sanal ağ adres alanınızda sildiğinizden emin olun. Daha sonra, Azure 'daki SAP HANA (büyük örnekler) ile iletişim kurmasına izin verilen aralıklardan kaldırmak için Microsoft hizmet yönetimi 'nde SAP HANA bilgilendirin.

Daha fazla bilgi için bkz. [bir alt ağı silme](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Sanal ağı silme

Bilgi için bkz. [sanal ağı silme](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

Microsoft hizmet yönetimi SAP HANA, Azure 'daki SAP HANA (büyük örnekler) ExpressRoute bağlantı hattının mevcut yetkilendirmelerini kaldırır. Ayrıca, HANA büyük örneklerle iletişim için Azure sanal ağ IP adresi aralığını veya adres alanını da kaldırır.

Sanal ağı kaldırdıktan sonra, kaldırılacak IP adresi alanı aralığını veya aralıklarını sağlamak için bir Azure destek isteği açın.

Her şeyi kaldırmayı sağlamak için ExpressRoute bağlantısını, sanal ağ geçidini, sanal ağ geçidi genel IP 'sini ve sanal ağı silin.

## <a name="delete-an-expressroute-circuit"></a>ExpressRoute devresini silme

Azure (büyük örnekler) ExpressRoute bağlantı hattına ek bir SAP HANA kaldırmak için, Microsoft hizmet yönetiminde SAP HANA bir Azure destek isteği açın. Devrenin silinmesini isteyin. Azure aboneliği içinde, gerektiğinde sanal ağı silebilir veya tutabilirsiniz. Ancak, HANA büyük örnekler ExpressRoute bağlantı hattı ile bağlı sanal ağ geçidi arasındaki bağlantıyı silmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [SAP HANA'yı (büyük örnekler) Azure'a yükleme ve yapılandırma](hana-installation.md)
