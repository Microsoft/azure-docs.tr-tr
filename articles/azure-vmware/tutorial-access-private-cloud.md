---
title: Öğretici-özel buluta erişin
description: Azure VMware çözümüne özel buluta erişmeyi öğrenin
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f2af1cffda08bf4b9c62e63f32d36cc9bbd7024a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494402"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Öğretici: Azure VMware çözümüne özel buluta erişme

Azure VMware çözümü, şirket içi vCenter 'unuzda özel bulutunuzu yönetmenize izin vermez. Bir bağlantı kutusu aracılığıyla Azure VMware Çözüm vCenter örneğine bağlanmanız gerekir. 

Bu öğreticide, [önceki öğreticide](tutorial-configure-networking.md) oluşturduğunuz kaynak grubunda bir geçiş kutusu oluşturacak ve Azure VMware Çözüm vCenter ' de oturum açacaksınız. Bu sıçrama kutusu, oluşturduğunuz aynı sanal ağ üzerinde bir Windows sanal makinedir (VM).  Hem vCenter hem de NSX Manager erişimi sağlar. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure VMware Çözüm vCenter erişimi için bir Windows sanal makinesi oluşturma
> * Bu sanal makineden vCenter oturumu aç

## <a name="create-a-new-windows-virtual-machine"></a>Yeni bir Windows sanal makinesi oluşturma

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Özel bulutunuzun yerel vCenter 'a bağlanma

1. Sıçrama kutusundan, bir bulut Yöneticisi Kullanıcı adı kullanarak VMware vCenter SSO ile vSphere Istemcisinde oturum açın ve Kullanıcı arabiriminin başarıyla görüntülendiğini doğrulayın.

1. Azure Portal, özel bulutunuzu seçin ve ardından   >  **kimliği** yönetin. 

   Özel bulut vCenter ve NSX-T Manager için URL 'Ler ve Kullanıcı kimlik bilgileri görüntülenir.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Özel bulut vCenter ve NSX Yöneticisi URL 'Leri ve kimlik bilgilerini görüntüleyin." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. Önceki adımda oluşturduğunuz VM 'ye gidin ve sanal makineye bağlanın. 

   VM 'ye bağlanmada yardıma ihtiyacınız varsa, Ayrıntılar için bkz. [sanal makineye bağlanma](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) .

1. Windows VM 'de bir tarayıcı açın ve iki sekmede vCenter ve NSX-T Yöneticisi URL 'Lerine gidin. 

1. VCenter sekmesinde, `cloudadmin@vmcp.local` önceki adımdan Kullanıcı kimlik bilgilerini girin.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Özel bulut vCenter ' da oturum açın." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter portalı." border="true":::

1. Tarayıcının ikinci sekmesinde NSX-T Yöneticisi ' nde oturum açın.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Tarayıcının ikinci sekmesinde NSX-T Yöneticisi ' nde oturum açın." border="true":::



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * VCenter 'a bağlanmak için kullanılacak bir Windows sanal makinesi oluşturun
> * Sanal makinenizden vCenter 'da oturum açın

Özel bulut kümeleriniz için yerel yönetim ayarlamak üzere bir sanal ağ oluşturmayı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Sanal ağ oluşturma](tutorial-configure-networking.md)


