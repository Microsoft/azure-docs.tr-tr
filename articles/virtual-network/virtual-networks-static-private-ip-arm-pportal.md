---
title: VM 'Ler için özel IP adresleri Yapılandırma-Azure portal
description: Azure portal kullanarak sanal makineler için özel IP adreslerini yapılandırmayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015953"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Azure portal kullanarak bir VM için özel IP adresi yapılandırma

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Aşağıdaki örnek adımlarda basit bir ortamın zaten oluşturulması beklenir. Bu belgede görüntülendikleri gibi adımları çalıştırmak istiyorsanız, önce [bir sanal ağ oluşturun](quick-create-portal.md#create-a-virtual-network). Ancak adım 3 ' te şu değerleri kullanın:

| Ayar | Değer |
| ------- | ----- |
| Ad | *TestVNet* |
| Adres alanı | *192.168.0.0/16* |
| Kaynak grubu | **Testrg** (gerekiyorsa, oluşturmak Için **Yeni oluştur** ' u seçin) |
| Alt ağ adı | *FrontEnd* |
| Alt Ağ - Adres aralığı | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Statik özel IP adreslerini test etmek için bir VM oluşturma
Kaynak Yöneticisi dağıtım modunda bir VM oluşturduğunuzda, Azure portal kullanarak statik bir özel IP adresi ayarlayamazsınız. Bunun yerine, önce VM 'yi oluşturursunuz. Ardından, özel IP 'sini statik olacak şekilde ayarlayabilirsiniz.

*Testvnet* adlı bir sanal ağın *ön uç* alt AĞıNDA *DNS01* adlı bir VM oluşturmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) menüsünde **kaynak oluştur**' u seçin.

    ![Kaynak oluşturma, Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. **İşlem**  >  **sanal makinesini** seçin.

    ![VM oluşturma, Azure portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. **Temel bilgiler** bölümünde, aşağıdaki tabloda açıklandığı gibi öğeler için değerler belirtin. Ardından **İleri &nbsp; : &nbsp; diskler** ve sonra ileri ' yi seçin **&nbsp; &nbsp;**.

    | Öğe | Değer |
    | --- | --- |
    | **Abonelik** | Geçerli aboneliğiniz |
    | **Kaynak grubu** | **Testrg** (açılan listeden seç) |
    | **Sanal makine adı** | *DNS01* |
    | **Bölge** | **(ABD) Doğu ABD** |
    | **Görüntü** | **Windows Server 2019 Datacenter** |
    | **Boyut** | **B1ls**'In **sanal makine boyutu** , **Standart** **sunumu** |
    | **Kullanıcı adı** | Yönetici hesabınızın Kullanıcı adı |
    | **Parola** | Yönetici hesabınızın Kullanıcı adı için parola |
    | **Parolayı onayla** | Parolayı yeniden |

    ![Temel bilgiler sekmesi, sanal makine oluşturma Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. **Ağ**' da, aşağıdaki tabloda açıklandığı gibi öğeler için değerler belirtin ve ardından **İleri**' yi seçin.

    | Öğe | Değer |
    | --- | --- |
    | **Sanal ağ** | **TestVNet** |
    | **Alt ağ** | **FrontEnd** |

    ![Ağ sekmesi, sanal makine oluşturma Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. **Yönetim** bölümünde, **Tanılama depolama hesabı** altında, **vnetstorage**' ı seçin. Bu depolama hesabı listede görünmezse, **Yeni oluştur**' u seçin, *vnetstorage* **adını** belirtin ve **Tamam**' ı seçin. Son olarak, **Gözden geçirme &nbsp; + &nbsp; Oluştur**' u seçin.

    ![Yönetim sekmesi, sanal makine oluşturma Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. **Gözden geçir + oluştur** bölümünde genel bakış bilgilerini gözden geçirin ve ardından **Oluştur**' u seçin.

    ![Gözden geçir + Oluştur sekmesi, sanal makine oluşturma Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

VM oluşturulduktan sonra aşağıdaki ileti görünür.

![Dağıtım tamamlama iletisi, sanal makine oluşturma Azure portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Bir VM için özel IP adresi bilgilerini alma
Yeni sanal makinenizin özel IP adresi bilgilerini görüntülemek için:

1. VM 'nizi bulmak için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

    ![Sanal makineler, arama kutusu, Azure portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Yeni VM 'nizin adını (**DNS01**) seçin.

    ![Sanal makine listesi, Azure portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. **Ağ**' ı seçin ve listelenen tek ağ arabirimini seçin.

    ![Ağ arabirimi, ağ, sanal makine, Azure portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. **IP yapılandırmaları**' nı seçin ve tabloda listelenen IP yapılandırmasını seçin.

    ![IP yapılandırması, ağ arabirimi, ağ, sanal makine, Azure portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. **Özel IP adresi ayarları**' nda, **Testvnet/ön uç** sanal ağ/alt ağ altında, **atama** değerini (**dinamik** veya **statik**) ve **IP adresini** not edin.

    ![Dinamik veya statik atama, eski özel IP adresi ayarları, IP yapılandırması, ağ arabirimi, ağ, sanal makine, Azure portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Var olan bir VM 'ye statik bir özel IP adresi ekleme
Yeni sanal makinenize statik bir özel IP adresi eklemek için:

1. IP yapılandırması sayfasında, özel IP adresi atamasını **statik** olarak ayarlayın.
2. Özel **IP adresinizi** *192.168.1.101* olarak değiştirin ve ardından **Kaydet**' i seçin.
   
    ![Dinamik veya statik atama, yeni özel IP adresi ayarları, IP yapılandırması, ağ arabirimi, ağ, sanal makine, Azure portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Atamanın hala **dinamik** olarak ayarlandığını **Kaydet** ' i SEÇTIKTEN sonra, yazdığınız IP adresi zaten kullanımda. Başka bir IP adresi deneyin.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Statik özel IP adresini bir VM 'den kaldırma
Statik özel IP adresini sanal makinenizde kaldırmak için:

IP yapılandırması sayfasında, özel IP adresi atamasını **dinamik** olarak ayarlayın ve ardından **Kaydet**' i seçin.

## <a name="set-ip-addresses-within-the-operating-system"></a>İşletim sistemi içinde IP adreslerini ayarla

Bir VM 'nin işletim sistemi içinden, Azure VM 'ye atanan *özel* IP 'yi statik olarak atamamalısınız. [VM 'lere bırçok IP adresi atarken](virtual-network-multiple-ip-addresses-portal.md), gerekli olduğunda yalnızca özel bir IP 'nin statik atamasını yapın. Özel IP adresini işletim sistemi içinde el ile ayarlarsanız, Azure [ağ arabirimine](virtual-network-network-interface-addresses.md#change-ip-address-settings)ATANMıŞ özel IP adresi ile eşleştiğinden emin olun. Aksi takdirde, VM bağlantısını kaybedebilirsiniz. [Özel IP adresi](virtual-network-network-interface-addresses.md#private) ayarları hakkında daha fazla bilgi edinin.

Ayrıca, sanal makinenin işletim sistemi içindeki bir Azure sanal makinesine atanan *genel* IP adresini asla el ile atamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[IP adresi ayarlarını](virtual-network-network-interface-addresses.md)yönetme hakkında bilgi edinin.
