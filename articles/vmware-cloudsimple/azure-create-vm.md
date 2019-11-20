---
title: CloudSimple tarafından Azure VMware çözümü-VM şablonlarıyla Azure 'da sanal makine oluşturma
description: CloudSimple özel bulutunuz için VMware altyapısında VM şablonları kullanarak Azure 'da sanal makinelerin nasıl oluşturulacağını açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576842"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>VMware altyapısında VM şablonları kullanarak Azure 'da sanal makine oluşturma

CloudSimple yöneticinizin aboneliğiniz için etkinleştirildiği VMware altyapısında VM şablonlarını kullanarak Azure portal bir sanal makine oluşturabilirsiniz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure Portal](https://portal.azure.com) oturum açın.

## <a name="create-cloudsimple-virtual-machine"></a>CloudSimple sanal makinesi oluştur

1. **Tüm Hizmetler**’i seçin.

2. **Cloudsimple sanal makineleri**için arama yapın.

3. **Ekle**'yi tıklatın.

    ![CloudSimple sanal makinesi oluştur](media/create-cloudsimple-virtual-machine.png)

4. Temel bilgileri girin **İleri: boyut**' a tıklayın.

    > [!NOTE]
    > Azure 'da CloudSimple sanal makine oluşturma bir VM şablonu gerektirir.  Bu VM şablonu, özel bulut vCenter 'unuzda bulunmalıdır.  Özel bulutunuzda, istenen işletim sistemi ve yapılandırmalara sahip vCenter kullanıcı arabiriminden bir sanal makine oluşturun.  [Sanal makineyi vSphere Web istemcisindeki bir şablona kopyalama](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)bölümündeki yönergeleri kullanarak bir şablon oluşturun.

    ![CloudSimple sanal makinesi oluşturma-temel kavramlar](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Alan | Açıklama |
    | ------------ | ------------- |
    | Subscription | Özel bulutunuz ile ilişkili Azure aboneliği.  |
    | Kaynak Grubu | VM 'nin atanacağı kaynak grubu. Var olan bir grubu seçebilir veya yeni bir grup oluşturabilirsiniz. |
    | Name | VM 'yi tanımlayacak ad.  |
    | Location | Bu VM 'nin barındırıldığı Azure bölgesi.  |
    | Özel Bulut | Sanal makineyi oluşturmak istediğiniz CloudSimple özel bulutu. |
    | Kaynak havuzu | VM için eşlenen kaynak havuzu. Kullanılabilir kaynak havuzlarından seçin. |
    | vSphere şablonu | VM için vSphere şablonu.  |
    | Kullanıcı adı | VM yöneticisinin Kullanıcı adı (Windows şablonları için)|
    | istemcisiyle yönetilen bir cihaz için) <br>Parolayı onayla | VM yöneticisinin parolası (Windows şablonları için).  |

5. VM için çekirdek sayısını ve bellek kapasitesini seçip **İleri: konfigürasyonlar**' ı tıklatın. Konuk işletim sistemine tam CPU sanallaştırmayı göstermek istiyorsanız onay kutusunu işaretleyin. böylece, donanım sanallaştırma gerektiren uygulamalar ikili çeviri veya paravirtualization olmadan sanal makinelerde çalıştırılabilir. Daha fazla bilgi için [VMware donanım yardımlı sanallaştırma 'Yi kullanıma](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)sunma VMware makalesine bakın.

    ![CloudSimple sanal makinesi oluşturma-boyut](media/create-cloudsimple-virtual-machine-size.png)

6. Ağ arabirimlerini ve diskleri aşağıdaki tablolarda açıklandığı şekilde yapılandırın ve **gözden geçir + oluştur**' a tıklayın.

    ![CloudSimple sanal makinesi oluşturma-yapılandırma](media/create-cloudsimple-virtual-machine-configurations.png)

    Ağ arabirimleri için, **ağ arabirimi Ekle** ' ye tıklayın ve aşağıdaki ayarları yapılandırın.

    | Denetim | Açıklama |
    | ------------ | ------------- |
    | Name | Arabirimi tanımlamak için bir ad girin.  |
    | Ağ | Özel bulut vSphere 'unuzda yapılandırılmış dağıtılmış bağlantı noktası grubu listesinden seçin.  |
    | Örünü | VM için yapılandırılmış kullanılabilir türler listesinden bir vSphere bağdaştırıcısı seçin. Daha fazla bilgi için bkz. [sanal makineniz için bir ağ bağdaştırıcısı seçme](https://kb.vmware.com/s/article/1001805)VMware Bilgi Bankası makalesi. |
    | Önyüklemede açma | VM önyüklendiğinde NIC donanımını etkinleştirip etkinleştirmeyeceğinizi seçin. Varsayılan olarak **etkinleştirilir**. |

    Diskler için, **Disk Ekle** ' ye tıklayın ve aşağıdaki ayarları yapılandırın.

    | Öğe | Açıklama |
    | ------------ | ------------- |
    | Name | Diski tanımlamak için bir ad girin.  |
    | Size | Kullanılabilir boyutlardan birini seçin.  |
    | SCSI denetleyicisi | Disk için bir SCSI denetleyicisi seçin.  |
    | Mod | Diskin anlık görüntülere nasıl katıldığını belirler. Bu seçeneklerden birini seçin: <br> -Bağımsız kalıcı: Diske yazılan tüm veriler kalıcı olarak yazılmıştır.<br> -Bağımsız kalıcı olmayan: Sanal makineyi kapattığınızda veya sıfırladığınızda diske yazılan değişiklikler atılır.  Bağımsız olarak kalıcı olmayan bağımsız mod, sanal makineyi her zaman aynı durumda yeniden başlatmanıza olanak tanır. Daha fazla bilgi için bkz. [VMware belgeleri](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Doğrulama tamamlandıktan sonra ayarları gözden geçirin ve **Oluştur**' a tıklayın. Herhangi bir değişiklik yapmak için üstteki sekmeye tıklayın veya tıklayın.

    ![CloudSimple sanal makinesi oluşturma-gözden geçirme](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>CloudSimple sanal makinelerinin listesini görüntüle

1. **Tüm Hizmetler**’i seçin.

2. **Cloudsimple sanal makineleri**için arama yapın.

3. Özel bulutunuzun oluşturulduğu ' ı seçin.

    ![CloudSimple sanal makinelerinin listesi](media/list-cloudsimple-virtual-machines.png)

CloudSimple sanal makinelerinin listesi, Azure portal oluşturulan sanal makineleri içerir.  Eşlenmiş vCenter kaynak havuzundaki özel bulut vCenter üzerinde oluşturulan sanal makineler listede gösterilir.  
