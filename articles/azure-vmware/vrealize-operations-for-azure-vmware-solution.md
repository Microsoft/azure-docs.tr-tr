---
title: Azure VMware çözümü için sanal Işlemleri ayarlama
description: Azure VMware çözümünüz özel bulutunuz için sanal Işlemleri ayarlamayı öğrenin.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 8015bb61a7401b4c97807e0256e06d4967c39026
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802497"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Azure VMware çözümü için sanal Işlemleri ayarlama


Vfarkında Operations Manager, VMware altyapı yöneticilerinin sistem kaynaklarını izlemesine izin veren bir Operations Management platformudur. Bu sistem kaynakları uygulama düzeyi veya altyapı düzeyi (fiziksel ve sanal) nesneler olabilir. Çoğu VMware Yöneticisi, VMware özel bulut bileşenlerini – vCenter, ESXi, NSX-T, vSAN ve VMware HCX ' i izlemek ve yönetmek için sanal Işlemleri kullanır.  Sağlanan her Azure VMware çözümü özel bulutu, adanmış bir vCenter, NSX-T, vSAN ve HCX dağıtımı içerir. 

[Başlamadan önce](#before-you-begin) ve [ön koşullardan](#prerequisites) önce ayrıntılı bir şekilde gözden geçirin. Ardından, iki tipik dağıtım topolojilerinde size kılavuzluk ederiz:

> [!div class="checklist"]
> * [Azure VMware çözüm dağıtımını yöneten şirket içi sanal Işlemler](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Azure VMware Çözüm dağıtımında çalışan sanal Işlemler](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Başlamadan önce
* Vfarkında Işlemleri dağıtma hakkında daha fazla bilgi edinmek için [Operations Manager ürün belgelerini](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) gözden geçirin. 
* Temel Azure VMware Çözüm Software-Defined veri merkezi (SDDC) [öğretici serisini](tutorial-network-checklist.md)gözden geçirin.
* İsteğe bağlı olarak, Azure VMware Çözüm dağıtımı seçeneğini yöneten şirket içi sanal Işlemler için [sanal Işlemler uzak denetleyicisi](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) ürün belgelerini gözden geçirin. 


## <a name="prerequisites"></a>Önkoşullar
* [Vfarkında Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) yüklendi.
* Şirket içi ve Azure VMware çözümü SDDC arasında yapılandırılmış bir VPN veya Azure ExpressRoute.
* Azure 'da bir Azure VMware çözümü özel bulutu dağıtıldı.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Azure VMware çözüm dağıtımını yöneten şirket içi sanal Işlemler
Çoğu müşterinin bir veya daha fazla şirket içi vCenter etki alanını yönetmek için mevcut bir şirket içi sanal dağıtım dağıtımı vardır. Azure VMware çözümü özel bulutu sağladıklarında, bir Azure ExpressRoute veya katman 3 VPN çözümü kullanarak şirket içi ortamlarını kendi özel bulutlarıyla birbirine bağlayabilirler.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Azure VMware çözüm dağıtımını yöneten şirket içi sanal Işlemler" border="false":::

Sanal Işlem yeteneklerini Azure VMware Çözüm özel bulutuna genişletmek için [özel bulut kaynakları için](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html)bir bağdaştırıcı örneği oluşturursunuz. Azure VMware çözümü özel bulutundaki verileri toplar ve şirket içi sanal Işlemler 'e getirir. Şirket içi sanal Operations Manager örneği, Azure VMware çözümünde vCenter ve NSX-T yöneticisine doğrudan bağlanabilir. İsteğe bağlı olarak, Azure VMware Çözüm özel bulutu 'nda bir sanal Işlemler uzak toplayıcısı dağıtabilirsiniz. Toplayıcı şirket içinde çalışan Operations Manager ExpressRoute veya VPN ağı üzerinden gönderilmeden önce özel buluttan toplanan verileri sıkıştırır ve şifreler. 

> [!TIP]
> Vfarkında Operations Manager yükleme hakkında adım adım kılavuz için [VMware belgelerine](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) bakın. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Azure VMware Çözüm dağıtımında çalışan sanal Işlemler

Başka bir seçenek de, özel buluttaki bir vSphere kümesine bir vanOperations Manager örneği dağıtmaktır. 

>[!IMPORTANT]
>Bu seçenek şu anda VMware tarafından desteklenmiyor.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Azure VMware çözümünde çalışan sanal Işlemler" border="false":::

Örnek dağıtıldıktan sonra, vCenter, ESXi, NSX-T, vSAN ve HCX 'ten veri toplamak için sanal Işlemleri yapılandırabilirsiniz. 



## <a name="known-limitations"></a>Bilinen sınırlamalar

- Azure VMware çözümünde **cloudadmin \@ vSphere. Local** kullanıcısı [sınırlı ayrıcalıklara](concepts-identity.md)sahiptir.  Azure VMware çözümünde sanal makineler (VM), VMware araçları kullanılarak Konuk içi bellek toplamayı desteklemez.  Etkin ve tüketilen bellek kullanımı bu durumda çalışmaya devam eder.
- Azure VMware çözümleri, DRS ayarları dahil olmak üzere küme yapılandırmasını yönettiğinden, ana bilgisayar tabanlı iş amacı için iş yükü iyileştirmesi çalışmıyor.
- Küme tabanlı iş amacını kullanarak SDDC içindeki çapraz küme yerleştirme için iş yükü iyileştirmesi, Operations Manager 8,0 ve sonraki sürümlerde tam olarak desteklenmektedir. Ancak, iş yükü iyileştirmesi kaynak havuzlarından haberdar değildir ve VM 'Leri küme düzeyinde yerleştirmez. Bir Kullanıcı bunu Azure VMware Çözüm vCenter Server arabiriminde el ile düzeltebilir.
- Azure VMware çözümünüzü vCenter Server kimlik bilgilerinizi kullanarak Operations Manager VNET 'te oturum açamazsınız. 
- Azure VMware çözümü, Vfarkında Operations Manager eklentisini desteklemez.

Azure VMware Çözüm vCenter 'ı vCenter Server bir bulut hesabı kullanarak Operations Manager Vfarkında 'e bağladığınızda, bir uyarı görürsünüz:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Uyarı bağdaştırıcı örneği oluşturma başarılı oldu":::

Uyarı oluşur çünkü Azure VMware çözümünde **cloudadmin \@ vSphere. Local** kullanıcısı kayıt için gereken tüm vCenter Server eylemlerini yapmak için yeterli ayrıcalıklara sahip değil. Ancak, ayrıcalıklar aşağıda görüldüğü gibi, bağdaştırıcı örneğinin veri toplamayı yapması için yeterlidir:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Veri toplamayı gerçekleştirmek için bağdaştırıcı örneği":::

Daha fazla bilgi için bkz. [vCenter bağdaştırıcı örneği yapılandırmak Için gereken ayrıcalıklar](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




