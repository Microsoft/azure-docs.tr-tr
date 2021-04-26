---
title: Öğretici-VMware HCX 'i dağıtma ve yapılandırma
description: Azure VMware çözümünüz özel bulutunuz için bir VMware HCX çözümünü dağıtmayı ve yapılandırmayı öğrenin.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9ee451ce4be59c51299d66e4042ed344026100cf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011009"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX’i dağıtma ve yapılandırma

Bu makalede, Azure VMware çözümünüz özel bulutunuz için şirket içi VMware HCX bağlayıcısının nasıl dağıtılacağı ve yapılandırılacağı gösterilmektedir. VMware HCX ile, VMware iş yüklerinizi Azure VMware çözümüne ve diğer bağlı sitelere çeşitli geçiş türleri aracılığıyla geçirebilirsiniz. Azure VMware çözümü HCX Cloud Manager 'ı dağıttığı ve yapılandıracağından, HCX bağlayıcısını şirket içi VMware veri merkezinizde indirmeniz, etkinleştirmeniz ve yapılandırmanız gerekir.

VMware HCX gelişmiş Bağlayıcısı, Azure VMware çözümünde önceden dağıtılır. En fazla üç site bağlantısını (Şirket içi buluta veya buluta buluta) destekler. Üçten fazla site bağlantısına ihtiyacınız varsa [VMware HCX kurumsal](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) eklentisini etkinleştirmek için bir [destek isteği](https://portal.azure.com/#create/Microsoft.Support) gönderebilirsiniz.  

>[!TIP]
>VMware yapılandırma en yüksek Aracı, şirket içi bağlayıcı ile bulut Yöneticisi arasında en yüksek site çiftlerini 25 olacak şekilde tanımlıyor olsa da, bu lisans bunu HCX Advanced ve HCX Enterprise Edition için 10 ' un üç ile sınırlandırır.

>[!NOTE]
>VMware HCX Enterprise, Önizleme hizmeti olarak Azure VMware çözümü ile kullanılabilir. Bu ücretsizdir ve bir önizleme hizmeti için hüküm ve koşullara tabidir. VMware HCX Enterprise hizmeti genel kullanıma sunulduğunda, faturalandırmaya geçiş yapılacak 30 günlük bir bildirim alırsınız. Ayrıca hizmeti kapatma veya devre dışı bırakma seçeneğiniz de vardır. HCx Enterprise 'tan HCX gelişmiş 'e yeniden dağıtım yapmanız gerekmez, ancak bu eylemin gerçekleşmesi için bir destek bileti günlüğe kaydetmek zorunda olacaksınız. Bir indirgeme planlanırsa, hiçbir geçişin planlanmadığından ve ya da PV gibi özelliklerin kullanımda olmadığından emin olun.

İlk olarak, [başlamadan önce](#before-you-begin), [yazılım sürümü gereksinimleri](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)ve [önkoşulları](#prerequisites)gözden geçirin. 

Ardından, aşağıdakileri yapmak için gereken tüm yordamları inceleyeceğiz:

> [!div class="checklist"]
> * VMware HCX bağlayıcı OVA 'yı indirin.
> * Şirket içi VMware HCX OVA 'yı (VMware HCX bağlayıcısını) dağıtın.
> * VMware HCX bağlayıcısını etkinleştirin.
> * Şirket içi VMware HCX bağlayıcınızı Azure VMware çözümünüz HCX bulut yöneticisiyle eşleştirin.
> * InterConnect 'i yapılandırın (ağ profili, işlem profili ve hizmet ağı).
> * Gereç durumunu denetleyerek ve geçişin mümkün olduğunu doğrulayarak kurulumu tamamen doldurun.

İşiniz bittiğinde, bu makalenin sonunda önerilen sonraki adımları izleyin.  

## <a name="before-you-begin"></a>Başlamadan önce

Dağıtımınızı hazırlarken aşağıdaki VMware belgelerini incelemenizi öneririz:

* [VMware HCX Kullanıcı Kılavuzu](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [VMware HCX ile sanal makineleri geçirme](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [VMware HCX dağıtım konuları](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware blog serisi-bulut geçişi](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [VMware HCX için gereken ağ bağlantı noktaları](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Önkoşullar

VMware HCX Enterprise kullanmayı planlıyorsanız, Azure VMware Çözüm desteği kanalları aracılığıyla etkinleştirme isteğinde bulunulduğunuzdan emin olun.


### <a name="on-premises-vsphere-environment"></a>Şirket içi vSphere ortamı

Şirket içi vSphere ortamınızın (kaynak ortam) [En düşük gereksinimleri](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)karşıladığından emin olun. 

### <a name="network-and-ports"></a>Ağ ve bağlantı noktaları

* [Azure expressroute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) , şirket Içi ve Azure VMware çözümü özel bulutu ExpressRoute devreleri arasında yapılandırılır.

* [Tüm gerekli bağlantı noktaları](https://ports.vmware.com/home/VMware-HCX) , şirket içi bileşenler ve Azure VMware çözümü özel arasındaki iletişim için açıktır.

### <a name="ip-addresses"></a>IP adresleri

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>VMware HCX bağlayıcısını indirin OVA

Sanal Gereci şirket içi vCenter 'ınıza dağıtmadan önce, VMware HCX bağlayıcı OVA 'yı indirmeniz gerekir.  

1. Azure portal Azure VMware çözümü özel bulutu ' nı seçin. 

1. Bağlantıyı **Yönet**  >   ' i seçin ve Azure VMware çözümü HCX yöneticisinin IP adresini belirlemek için **HCX** sekmesini seçin. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="VMware HCX IP adresinin ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. **Manage**  >  **Identity** öğesini seçin. 

   Özel bulut vCenter ve NSX-T Manager için URL 'Ler ve Kullanıcı kimlik bilgileri görüntülenir.

   > [!TIP]
   > VCenter parolası, özel bulutu ayarlarken tanımlanmıştır. Azure VMware Solution HCX Manager 'da oturum açmak için kullandığınız parola aynı. Yeni vCenter ve NSX-T parolaları oluşturmak için **Yeni bir parola oluştur** seçeneğini belirleyebilirsiniz.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Özel bulut vCenter ve NSX Yöneticisi URL 'Leri ve kimlik bilgilerini görüntüleyin." border="true":::


1. Bir tarayıcı penceresi açın, 443 numaralı bağlantı noktasındaki Azure VMware Solution HCX Manager 'da `https://x.x.x.9` **cloudadmin \@ vSphere** ile oturum açın. Yerel Kullanıcı kimlik bilgileri

1. **Yönetim**  >  **sistemi güncelleştirmeleri** ' ni seçin ve ardından **indirme bağlantısı iste**' yi seçin.

1. VMware HCX bağlayıcı OVA dosyasını indirmek için tercih ettiğiniz seçeneği belirleyin.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>VMware HCX bağlayıcı OVA 'yı şirket içinde dağıtma

1. Şirket içi vCenter 'ağınızda, VMware HCX bağlayıcısını şirket içi vCenter 'nize dağıtmak için bir [ovf şablonu](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) seçin. 

   > [!TIP]
   > Önceki bölümde indirdiğiniz OVA dosyasını seçersiniz.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="OVF şablonuna göz atma ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Bir ad ve konum seçin ve VMware HCX bağlayıcısını dağıttığınız kaynağı veya kümeyi seçin. Ardından ayrıntıları ve gerekli kaynakları gözden geçirin ve **İleri**' yi seçin.  

1. Lisans koşullarını gözden geçirin. Kabul ediyorsanız gereken depolama ve ağ ' ı seçin ve ardından **İleri**' yi seçin.

1. Depolama ' yı seçin ve **İleri ' yi** seçin.

1. [IP adresi önkoşulları](#ip-addresses) bölümünde daha önce tanımladığınız VMware HCX yönetim ağı segmentini seçin.  Ardından **İleri**' yi seçin.

1. **Şablonu Özelleştir**' de gerekli tüm bilgileri girin ve ardından **İleri**' yi seçin. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Bir şablonu özelleştirmeye yönelik kutuların ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Yapılandırmayı doğrulayın ve sonra VMware HCX bağlayıcı OVA 'yı dağıtmak için **son** ' u seçin.
   
   > [!IMPORTANT]
   > Sanal Gereci el ile açmanız gerekir.  Etkinleştirildikten sonra, sonraki adıma geçmeden önce 10-15 dakika bekleyin.

Bu yordama uçtan uca genel bakış için [Azure VMware çözümü: HCX gereç dağıtımı](https://www.youtube.com/embed/UKmSTYrL6AY) videosunu görüntüleyin. 


## <a name="activate-vmware-hcx"></a>VMware HCX 'i etkinleştir

VMware HCX bağlayıcısını şirket içinde dağıttıktan sonra gereci başlattıktan sonra, ' yi etkinleştirmeye hazırsınız demektir. İlk olarak, Azure VMware Çözüm Portalından bir lisans anahtarı almanız gerekir.

1. Azure VMware Çözüm portalında   >  **bağlantıyı** Yönet ' e gidin, **HCX** sekmesini seçin ve ardından **Ekle**' yi seçin.

1. Şirket içi VMware HCX yöneticisinde oturum açmak için **yönetici** kimlik bilgilerini kullanın `https://HCXManagerIP:9443` . 

   > [!TIP]
   > VMware HCX Manager OVA dosya dağıtımı sırasında **yönetici** Kullanıcı parolasını tanımladınız.

   > [!IMPORTANT]
   > `9443`VMware HCX Manager IP adresine sahip bağlantı noktası numarasını eklediğinizden emin olun.

1. **Lisanslama** bölümünde **HCX gelişmiş anahtarınız** Için anahtarınızı girin ve **Etkinleştir**' i seçin.  
   
    > [!NOTE]
    > VMware HCX Manager 'da açık internet erişimi veya bir ara sunucu yapılandırılmış olmalıdır.

1. **Veri merkezi konumunda**, VMware HCX yöneticisini şirket içi yükleme için en yakın konumu belirtin. Daha sonra **Devam** seçeneğini belirleyin.

1. **Sistem adı**' nda adı değiştirin veya varsayılanı kabul edin ve **devam**' ı seçin.
   
1. **Evet, devam et ' i** seçin.

1. **VCenter 'Larınızı bağlayın** bölümünde vCenter sunucunuzun FQDN 'SINI veya IP adresini ve uygun kimlik bilgilerini girip **devam**' ı seçin.
   
   > [!TIP]
   > VCenter Server, veri merkezinizde VMware HCX bağlayıcısını dağıttığınız yerdir.

1. **SSO/PSC yapılandırma** bölümünde, platform HIZMETLERI denetleyicinizin FQDN 'SINI veya IP adresini girip **devam**' ı seçin.
   
   > [!NOTE]
   > Genellikle, vCenter FQDN 'niz veya IP adresiniz ile aynıdır.

1. Girilen bilgilerin doğru olduğundan emin olun ve **Yeniden Başlat**' ı seçin.
    
   > [!NOTE]
   > Sonraki adımda uyarılmadan önce yeniden başlattıktan sonra bir gecikme yaşarsınız.

Hizmetler yeniden başlatıldıktan sonra, vCenter görüntülenen ekranda yeşil olarak gösterilir. Hem vCenter hem de SSO, önceki ekranla aynı olması gereken uygun yapılandırma parametrelerine sahip olmalıdır.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Yeşil vCenter durumu içeren panonun ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Bu yordama uçtan uca genel bakış için [Azure VMware çözümünü görüntüleyin: HCX videosunu etkinleştirin](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720) .

   > [!IMPORTANT]
   > HCX gelişmiş veya HCX kurumsal kuruluş kullanıp kullanmayacağınızı VMware 'in [KB makalesi 81558](https://kb.vmware.com/s/article/81558)' den yüklemeniz gerekebilir. 

## <a name="configure-the-vmware-hcx-connector"></a>VMware HCX bağlayıcısını yapılandırma

Artık bir site eşleştirme eklemeye, bir ağ ve bilgi işlem profili oluşturmaya ve geçiş, ağ uzantısı ya da olağanüstü durum kurtarma gibi hizmetleri etkinleştirmeye hazır olursunuz. 

### <a name="add-a-site-pairing"></a>Site eşleştirmesi ekleme

Azure VMware çözümünde VMware HCX bulut yöneticisini, veri merkezinizdeki VMware HCX Bağlayıcısı ile bağlanabilir veya eşleştirin. 

1. Şirket içi vCenter oturumunuzu açın ve **giriş** bölümünde **HCX**' i seçin.

1. **Altyapı** altında **site eşleştirme**' yi seçin ve ardından **uzak siteye Bağlan** seçeneğini belirleyin (ekranın ortasında). 

1. Daha önce not ettiğiniz Azure VMware Solution HCX Cloud Manager URL 'sini veya IP adresini `https://x.x.x.9` , Azure VMware çözümü cloudadmin \@ vSphere. Local Kullanıcı adı ve parolayı girin. Ardından **Bağlan**'ı seçin.

   > [!NOTE]
   > Bir site çiftini başarıyla oluşturmak için:
   > * VMware HCX Bağlayıcınız, 443 numaralı bağlantı noktası üzerinden HCX Cloud Manager IP 'nize yönlendiribilmelidir.
   >
   > * VCenter 'da oturum açmak için kullandığınız parolayı kullanın. Bu parolayı ilk dağıtım ekranında tanımladınız.

   Azure VMware çözümünde VMware HCX bulut yöneticinizin ve şirket içi VMware HCX bağlayıcısının bağlı (eşleştirilmiş) olduğunu gösteren bir ekran görürsünüz.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Azure VMware çözümünde HCX yöneticisinin ve VMware HCX bağlayıcısının eşleştirmesinin gösterildiği ekran görüntüsü.":::

Bu yordama uçtan uca genel bakış için [Azure VMware çözümü: HCX site eşleştirme](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720) videosunu görüntüleyin.

### <a name="create-network-profiles"></a>Ağ profilleri oluşturma

VMware HCX Bağlayıcısı, birden çok IP kesimi gerektiren sanal gereçlerin (otomatik) bir alt kümesini dağıtır. Ağ profillerinizi oluştururken, [VMware HCX ağ kesimleri dağıtım öncesi hazırlama ve planlama aşaması](production-ready-deployment-steps.md#vmware-hcx-network-segments)SıRASıNDA tanımladığınız IP segmentlerini kullanırsınız.

Dört ağ profili oluşturacaksınız:

   - Yönetim
   - vMotion
   - Çoğaltma
   - Yukarı

1. **Altyapı** altında, **bağlantı**  >  **çok siteli hizmet**  >  **ağı ağ profilleri** ağ  >  **profili oluştur**' u seçin.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Bir ağ profili oluşturmaya başlama seçimlerinin ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Her ağ profili için, ağ ve bağlantı noktası grubunu seçin, bir ad girin ve segmentin IP havuzunu oluşturun. Ardından **Oluştur**’u seçin. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Yeni bir ağ profili için ayrıntıların ekran görüntüsü.":::

Bu yordama uçtan uca genel bakış için [Azure VMware çözümü: HCX ağ profili](https://www.youtube.com/embed/O0rU4jtXUxc) videosunu görüntüleyin.


### <a name="create-a-compute-profile"></a>İşlem profili oluşturma

1. **Altyapı** altında, **Interconnect**  >  **işlem profilleri**  >  **işlem profili oluştur**' u seçin.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Bir işlem profili oluşturmaya başlamak için seçimleri gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Profil için bir ad girin ve **devam**' ı seçin.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="İşlem profili adı ve devam düğmesi girişini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Geçiş, ağ uzantısı veya olağanüstü durum kurtarma gibi etkinleştirilecek hizmetleri seçin ve ardından **devam**' ı seçin.
  
   > [!NOTE]
   > Genellikle burada hiçbir şey değişiklik yapmaz.

1. **Hizmet kaynaklarını Seç** bölümünde, seçilen VMware HCX hizmetlerini etkinleştirmek için bir veya daha fazla hizmet kaynağı (küme) seçin.  

1. Şirket içi veri merkezinizde kümeleri gördüğünüzde **devam**' ı seçin.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Seçili hizmet kaynaklarını ve devam düğmesini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. **Veri deposundan Seç**' den, VMware HCX Interconnect gereçlerini dağıtmak için veri deposu depolama kaynağını seçin. Daha sonra **Devam** seçeneğini belirleyin.

   Birden çok kaynak seçildiğinde, VMware HCX, kapasitesi tükenene kadar seçilen ilk kaynağı kullanır.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Seçilen bir veri depolama kaynağını ve devam düğmesini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. **Yönetim ağı profilini seç**' den önceki adımlarda oluşturduğunuz yönetim ağı profilini seçin. Daha sonra **Devam** seçeneğini belirleyin.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Bir yönetim ağ profili ve devam düğmesi seçimini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. **Yukarı bağlantı ağı profilini seç**' den önceki yordamda oluşturduğunuz yukarı bağlantı ağ profilini seçin. Daha sonra **Devam** seçeneğini belirleyin.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Bir yukarı bağlantı ağ profili ve devam düğmesi seçimini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. **VMotion ağ profilini seç**' den önceki adımlarda oluşturduğunuz vMotion ağ profilini seçin. Daha sonra **Devam** seçeneğini belirleyin.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="VMotion ağ profili ve devam düğmesi seçimini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. **VSphere çoğaltma ağı profilini seçin** bölümünde, önceki adımlarda oluşturduğunuz çoğaltma ağ profilini seçin. Daha sonra **Devam** seçeneğini belirleyin.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Bir çoğaltma ağ profili ve devam düğmesi seçimini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. **Ağ uzantıları Için dağıtılmış anahtarlar seçin** sayfasında, katman 2 genişletilmiş ağ üzerinde Azure VMware çözümüne geçirilecek sanal makineleri içeren anahtarları seçin. Daha sonra **Devam** seçeneğini belirleyin.

   > [!NOTE]
   > Katman 2 (L2) genişletilmiş ağlardaki sanal makineleri geçiriyorsanız, bu adımı atlayabilirsiniz.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Dağıtılmış sanal anahtarların ve devam düğmesinin seçimini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Bağlantı kurallarını gözden geçirin ve **devam**' ı seçin.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Bağlantı kurallarını ve devam düğmesini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. İşlem profili oluşturmak için **son** ' u seçin.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="İşlem profili bilgilerini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Bu yordama uçtan uca genel bakış için [Azure VMware çözümü: Işlem profili](https://www.youtube.com/embed/e02hsChI3b8) videosu ' nı görüntüleyin.

### <a name="create-a-service-mesh"></a>Hizmet ağı oluşturma

Artık şirket içi ve Azure VMware çözümü özel bulutu arasında bir hizmet ağı yapılandırma zamanı.



> [!NOTE]
> Azure VMware çözümü ile başarılı bir şekilde hizmet ağı kurmak için:
>
> * UDP 500/4500 bağlantı noktaları, şirket içi VMware HCX bağlayıcısının ' yukarı bağlantı ' ağ profili adresiniz ve Azure VMware çözümü HCX bulutu ' yukarı bağlantı ağı profili adresleri arasında açıktır.
>
> * [VMware HCX gereken bağlantı noktalarını](https://ports.vmware.com/home/VMware-HCX)gözden geçirdiğinizden emin olun.

1. **Altyapı** altında, **Interconnect**  >  **Service kafesi**  >  **hizmet ağı oluştur**' u seçin.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Hizmet ağı oluşturmaya başlamak için seçimlerin ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Önceden doldurulmuş siteleri gözden geçirin ve ardından **devam**' ı seçin. 

   > [!NOTE]
   > Bu ilk hizmet ağı yapılandırmanızda ise, bu ekranı değiştirmeniz gerekmez.  

1. Açılan listelerden kaynak ve Uzaktan işlem profillerini seçip **devam**' ı seçin.  

   Seçimler, VM 'Lerin VMware HCX hizmetlerini kullanabileceği kaynakları tanımlar.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Kaynak hesaplama profilini seçmeyi gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Uzaktan işlem profilinin seçildiğinin gösterildiği ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Etkinleştirilecek hizmetleri gözden geçirin ve ardından **devam**' ı seçin.  

1. **Gelişmiş yapılandırma-yukarı bağlantı ağ profillerini geçersiz kıl**, **devam**' ı seçin.  

   Yukarı bağlantı ağ profilleri, uzak sitenin Interconnect gereçlerine erişilebileceği ağa bağlanır.  
  
1. **Gelişmiş yapılandırma-ağ uzantısı gereç ölçeği ölçeğini** Inceleyin ve **devam**' ı seçin. 

   Her gereç için en fazla sekiz VLAN olabilir, ancak başka bir gereci daha fazla sekiz VLAN eklemek üzere dağıtabilirsiniz. Ayrıca, ek gereçlere yönelik olarak bir IP alanınız olmalıdır ve bu, Gereç başına bir IP olur.  Daha fazla bilgi için bkz. [VMware HCX yapılandırma sınırları](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="VLAN sayısının nerede artırması gerektiğini gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. **Gelişmiş yapılandırma-Trafik Mühendisliği**' nda, gereken tüm değişiklikleri gözden geçirin ve yapın ve ardından **devam**' ı seçin.

1. Topoloji önizlemesini inceleyin ve **devam**' ı seçin.

1. Bu hizmet ağı için Kullanıcı dostu bir ad girin ve tamamlanacak **son** ' u seçin.  

1. Dağıtımı izlemek için **görevleri görüntüle** ' yi seçin. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Görevleri görüntülemek için düğmeyi gösteren ekran görüntüsü.":::

   Hizmet kafesi dağıtımı başarıyla tamamlandığında, Hizmetleri yeşil olarak görürsünüz.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Hizmetlerde yeşil göstergeleri gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Gereç durumunu denetleyerek hizmet kafesinin sistem durumunu doğrulayın. 

1. **Interconnect**  >  **gereçlerini** seçin.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Gereç durumunu denetlemeye yönelik seçimleri gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Bu yordama uçtan uca genel bakış için [Azure VMware çözümü: hizmet ağı](https://www.youtube.com/embed/COY3oIws108) videosu ' nı görüntüleyin.

### <a name="optional-create-a-network-extension"></a>Seçim Ağ uzantısı oluşturma

Şirket içi ortamınızdan herhangi bir ağı Azure VMware çözümüne genişletmek istiyorsanız aşağıdaki adımları izleyin:

1. **Hizmetler**' in altında ağ **Uzantısı**  >  **ağ uzantısı oluştur**' u seçin.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Bir ağ uzantısı oluşturmaya başlamak için seçimleri gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware çözümüne genişletmek istediğiniz ağların her birini seçin ve ardından **İleri**' yi seçin.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Bir ağın seçimini gösteren ekran görüntüsü.":::

1. Genişletçalıştığınız ağların her biri için şirket içi ağ geçidi IP 'sini girin ve ardından **Gönder**' i seçin. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Bir ağ geçidi IP adresinin girişini gösteren ekran görüntüsü.":::

   Ağ uzantısının tamamlanabilmesi birkaç dakika sürer. Ne zaman, **uzantı tamamlandığında** durum değişikliğini görürsünüz.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Uzantı durumunun tamamlandığını gösteren ekran görüntüsü." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Bu yordama uçtan uca genel bakış için [Azure VMware çözümü: ağ uzantısı](https://www.youtube.com/embed/gYR0nftKui0) videosunu görüntüleyin.


## <a name="next-steps"></a>Sonraki adımlar

HCX Interconnect tünel durumu **Açık** ve yeşil Ise, VMware HCX kullanarak Azure VMware Çözüm VM 'lerini geçirebilir ve koruyabilirsiniz. Azure VMware çözümü, iş yükü geçişlerini destekler (ağ uzantısı olan veya olmayan). Yine de sanal iş yüklerini vSphere ortamınızda geçirebilirsiniz, bu ağlarda ağların şirket içi olarak oluşturulmasını ve VM 'lerin dağıtımını yapabilirsiniz.  

HCX kullanma hakkında daha fazla bilgi için VMware teknik belgelerine gidin:

* [VMware HCX belgeleri](https://docs.vmware.com/en/VMware-HCX/index.html)
* [VMware HCX ile sanal makineleri geçirme](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX gereken bağlantı noktaları](https://ports.vmware.com/home/VMware-HCX)
* [Lisans anahtarını onaylamadan önce bir HCX ara sunucusu ayarlama](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
