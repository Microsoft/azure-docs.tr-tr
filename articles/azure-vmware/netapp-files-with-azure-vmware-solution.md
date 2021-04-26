---
title: Azure VMware çözümü ile Azure NetApp Files
description: Şirket içi sunucular, Azure VMware Çözüm VM 'Leri ve bulut altyapılarında veri geçirmek ve eşitlemek için Azure VMware Çözüm VM 'Leri ile Azure NetApp Files kullanın.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 09b9ba2ff6b95e12558b1ac49e401ce6dede4942
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870994"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure VMware çözümü ile Azure NetApp Files

Bu makalede, Azure VMware Çözüm tabanlı iş yükleriyle Azure NetApp Files tümleştirme adımlarını adım adım inceleyeceğiz. Konuk işletim sistemi Azure NetApp Files birimlerine erişen sanal makinelerin (VM 'Ler) içinde çalışır. 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files genel bakış

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) , geçiş Için bir Azure hizmetidir ve bulutta en zorlu kurumsal dosya iş yüklerini (VERITABANLARı, SAP ve yüksek performanslı bilgi işlem uygulamaları) kod değişikliği olmadan çalıştırmaya çalışır.

### <a name="features"></a>Özellikler
(Azure NetApp Files kullanıldığı hizmetler.)

- **Active Directory bağlantıları**: Azure NetApp Files [Active Directory Domain Services ve Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use)destekler.

- **Paylaşma Protokolü**: Azure NetApp Files, sunucu ileti bloğu (SMB) ve ağ dosya SISTEMI (NFS) protokollerini destekler. Bu destek, birimlerin Linux istemcisine bağlanalabileceği ve Windows istemcisinde eşleştirilebilecek anlamına gelir.

- **Azure VMware çözümü**: Azure NetApp Files paylaşımları Azure VMware Çözüm ortamında oluşturulan VM 'lerden bağlanabilir.

Azure NetApp Files birçok Azure bölgesinde kullanılabilir ve bölgeler arası çoğaltmayı destekler. Azure NetApp Files yapılandırma yöntemleriyle ilgili bilgi için bkz. [Azure NetApp Files depolama hiyerarşisi](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Başvuru mimarisi

Aşağıdaki diyagramda bir Azure VMware çözümü özel bulutuna Azure ExpressRoute üzerinden bir bağlantı gösterilmektedir. Azure VMware Çözüm ortamı, Azure VMware Çözüm VM 'lerine bağlı Azure NetApp Files paylaşımına erişir.

![Azure VMware Çözüm mimarisi için NetApp dosyalarını gösteren diyagram.](media/net-app-files/net-app-files-topology.png)

Bu makalede, Azure VMware Çözüm VM 'Leri için Azure NetApp Files birimini dosya paylaşımında ayarlama, test etme ve doğrulamaya yönelik yönergeler ele alınmaktadır. Bu senaryoda NFS protokolünü kullandık. Azure NetApp Files ve Azure VMware çözümü aynı Azure bölgesinde oluşturulur.

## <a name="prerequisites"></a>Önkoşullar 

> [!div class="checklist"]
> * Azure NetApp Files etkin Azure aboneliği
> * Azure NetApp Files alt ağı
> * Azure VMware çözümünde Linux sanal makinesi
> * Azure VMware çözümünde Windows VM 'Leri

## <a name="regions-supported"></a>Desteklenen bölgeler

Desteklenen bölgelerin listesi, [bölgeye göre Azure ürünlerinde](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)bulunabilir.

## <a name="verify-pre-configured-azure-netapp-files"></a>Önceden yapılandırılmış Azure NetApp Files doğrulayın 

Azure VMware Çözüm VM 'lerinde Azure NetApp Files birimleri oluşturmak ve bağlamak için aşağıdaki makalelerdeki adım adım yönergeleri izleyin.

- [NetApp hesabı oluşturma](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Kapasite havuzunu ayarlama](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files için SMB birimi oluşturma](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Azure NetApp Files için NFS birimi oluşturma](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Azure NetApp Files için bir alt ağı temsilci olarak belirleme](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

Aşağıdaki adımlar, Azure NetApp Files Premium hizmet düzeyinde Azure 'da oluşturulan önceden yapılandırılmış Azure NetApp Files doğrulamasını içerir.

1. Azure portal **depolama** altında **Azure NetApp Files**' ı seçin. Yapılandırılmış Azure NetApp Files listesi gösterilir. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Önceden yapılandırılmış Azure NetApp Files listesini gösteren ekran görüntüsü."::: 

2. Ayarlarını görüntülemek için yapılandırılmış bir NetApp dosyaları hesabı seçin. Örneğin **contoso-anf2**' ı seçin. 

3. Yapılandırılan havuzu doğrulamak için **Kapasite havuzlarını** seçin. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Yapılandırılmış bir NetApp dosyaları hesabının kapasite havuzlarını ve birimlerini görüntüleme seçeneklerini gösteren ekran görüntüsü.":::

    Kapasite ve hizmet düzeyini gösteren kapasite havuzları sayfası açılır. Bu örnekte, depolama havuzu Premium hizmet düzeyiyle 4 TiB olarak yapılandırılır.

4. Kapasite havuzu altında oluşturulan birimleri görüntülemek için **birimleri** seçin. (Önceki ekran görüntüsüne bakın.)

5. Yapılandırmasını görüntülemek için bir birim seçin.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Kapasite havuzu altında oluşturulan birimleri gösteren ekran görüntüsü.":::

    Birimin yapılandırma ayrıntılarını gösteren bir pencere açılır.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Bir birimin yapılandırma ayrıntılarını gösteren ekran görüntüsü.":::

    Anfvolume 'in boyutunun 200 GiB olduğunu ve kapasite havuzunda anfpool1 olduğunu görebilirsiniz. 10.22.3.4 aracılığıyla bir NFS dosya paylaşma olarak veriliyor:/ANFVOLUME. Azure NetApp Files için Azure sanal ağından (VNet) bir özel IP oluşturuldu ve sanal makineye bağlanacak NFS yolu.

    Boyut veya "Kota" ile Azure NetApp Files birim performansı hakkında bilgi edinmek için bkz. [Azure NetApp Files Için performans konuları](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Önceden yapılandırılmış Azure VMware Çözüm VM 'si paylaşma eşlemesini doğrulama

Azure NetApp Files paylaşımınızı Azure VMware Çözüm VM 'niz tarafından erişilebilir hale getirmek için SMB ve NFS paylaşma eşlemesini anlamanız gerekir. Yalnızca SMB veya NFS birimleri yapılandırıldıktan sonra, bunları burada belgelenen şekilde bağlayabilirsiniz.

- SMB paylaşma: bir SMB birimini dağıtmadan önce bir Active Directory bağlantısı oluşturun. Başarılı bir bağlantı için, belirtilen etki alanı denetleyicilerine Azure NetApp Files Temsilcili alt ağı tarafından erişilebilir olmalıdır. Active Directory Azure NetApp Files hesabında yapılandırıldıktan sonra, SMB birimleri oluşturulurken seçilebilir bir öğe olarak görünür.

- NFS paylaşma: NFS veya ikili protokol (NFS ve SMB) kullanarak birim oluşturmaya katkıda bulunur Azure NetApp Files. Birimin kapasite kullanımı, havuzunun sağlanan kapasitesinden sayılır. NFS, komut satırları veya/etc/fstab girdileri kullanılarak Linux sunucusuna bağlanabilir.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Azure VMware çözümü ile Azure NetApp Files durumlarını kullanma

Aşağıda yalnızca birkaç etkileyici Azure NetApp Files kullanım örnekleri verilmiştir. 
- Ufuk profili yönetimi
- Citrix profil yönetimi
- Uzak Masaüstü Hizmetleri profili yönetimi
- Azure VMware çözümünde dosya paylaşımları

## <a name="next-steps"></a>Sonraki adımlar

Azure VMware Çözüm iş yükleriniz ile Azure NetApp Files tümleştirmenin kapsandığına göre, şunları öğrenmek isteyebilirsiniz:

- [Azure NetApp Files için kaynak sınırlamaları](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Azure NetApp Files ağ planlaması yönergeleri](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Azure NetApp Files birimlerinin çapraz bölge çoğaltması](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Azure NetApp Files hakkında SSS](../azure-netapp-files/azure-netapp-files-faqs.md)
