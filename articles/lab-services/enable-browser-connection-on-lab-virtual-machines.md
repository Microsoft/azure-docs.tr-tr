---
title: Azure DevTest Labs sanal makinelerde tarayıcı bağlantısını etkinleştir | Microsoft Docs
description: DevTest Labs artık Azure savunma ile tümleşir, bu da laboratuvarın sahibi olarak tüm laboratuar sanal makinelerine erişimi bir tarayıcıdan sağlayabilir.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 2ddc56c60c547bd4ce48d620a83fb79246762bfb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642493"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Laboratuvar sanal makinelerinde tarayıcı bağlantısını etkinleştir 

DevTest Labs, sanal makinelerinize bir tarayıcıdan bağlanmanızı sağlayan [Azure](https://docs.microsoft.com/azure/bastion/)savunma ile tümleşir. Önce laboratuvar sanal makinelerinde tarayıcı bağlantısını etkinleştirmeniz gerekir.

Bir laboratuvarın sahibi olarak, tüm Laboratuvar sanal makinelerine bir tarayıcıdan erişmeyi etkinleştirebilirsiniz. Ek bir istemci, aracı veya yazılım parçası gerekmez. Azure savunma, SSL üzerinden Azure portal doğrudan sanal makinelerinize yönelik güvenli ve sorunsuz RDP/SSH bağlantısı sağlar. Azure savunma aracılığıyla bağlandığınızda, sanal makinelerinizin ortak bir IP adresi olması gerekmez. Daha fazla bilgi için bkz. Azure savunma nedir [?](../bastion/bastion-overview.md)

> [!NOTE]
> Laboratuvar sanal makinelerinde tarayıcı bağlantısının etkinleştirilmesi önizleme aşamasındadır.

Bu makalede, laboratuvar sanal makinelerinde tarayıcı bağlantısının nasıl etkinleştirileceği gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar 
Mevcut laboratuvarın sanal ağında bir savunma ana bilgisayarı dağıtın **(veya)** laboratuvarınızı yapılandırılmış bir VNET ile bağlayın. 

Bir savunma konağını VNet 'te dağıtmayı öğrenmek için bkz. [Azure savunma Konağı (Önizleme) oluşturma](../bastion/bastion-create-host-portal.md). Savunma konağını oluştururken, laboratuvarın sanal ağını seçin. 

Laboratuvarınızı yapılandırılmış bir sanal ağ ile nasıl bağlayacağınızı öğrenmek için bkz. [Azure DevTest Labs sanal ağı yapılandırma](devtest-lab-configure-vnet.md). Savunma konağının dağıtıldığı VNet 'i ve içinde **AzureBastionSubnet** seçin. Ayrıntılı adımlar aşağıda verilmiştir: 

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol gezinti menüsünde **tüm hizmetler** ' i seçin. 
1. Listeden **DevTest Labs**'i seçin. 
1. Laboratuvarlar listesinden *laboratuvarınızı*seçin. 

    > [!NOTE]
    > Azure savunma Şu anda önizleme aşamasındadır. Aşağıdaki bölgelerle sınırlıdır: Batı ABD, Doğu ABD, Batı Avrupa, Orta Güney ABD, Avustralya Doğu ve Japonya Doğu. Bu nedenle, laboratuvarınız bunlardan birinde değilse, bu bölgelerden birinde bir laboratuvar oluşturun. 
1. Sol menüdeki **Ayarlar** bölümünde **yapılandırma ve ilkeler** ' i seçin. 
1. **Sanal ağları**seçin.
1. Araç çubuğundan **Ekle** ' yi seçin. 
1. Savunma konağının dağıtıldığı **VNET** 'i seçin. 
1. Alt ağı seçin: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. **Sanal makine oluşturma seçeneğinde kullan '** ı seçin. 
1. Araç çubuğunda **Kaydet**’i seçin. 
1. Laboratuvar için eski bir VNet 'iniz varsa, * *...* seçeneğini belirleyerek kaldırın.  ve **kaldırın**. 

## <a name="enable-browser-connection"></a>Tarayıcı bağlantısını etkinleştir 

Laboratuvarda laboratuvar sahibi olarak yapılandırılmış VNet 'e sahip olduktan sonra, laboratuvar sanal makinelerinde tarayıcı bağlantısı sağlayabilirsiniz.

Laboratuvar sanal makinelerinde tarayıcı bağlantısı sağlamak için şu adımları izleyin:

1. Azure portal *laboratuvara*gidin.
1. **Yapılandırma ve ilkeler '** i seçin.
1. **Ayarlar**' da **tarayıcı bağlantısı (Önizleme)** öğesini seçin.

![Tarayıcı bağlantısını etkinleştir](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Sonraki Adımlar
Bir tarayıcı kullanarak sanal makinelerinize nasıl bağlanacağınızı öğrenmek için aşağıdaki makaleye bakın: [Sanal makinelerinize bir tarayıcı üzerinden bağlanma](connect-virtual-machine-through-browser.md)