---
title: Azure Site Recovery ile Azure VM olağanüstü durum kurtarmayı ayarlama
description: Azure Site Recovery (Önizleme) ile Azure VM’leri için ikincil Azure bölgesine olağanüstü durum kurtarmanın nasıl ayarlanacağını öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 502f71365d78bd8fd949d17fe8907817394586b2
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961489"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Azure VM 'Leri için olağanüstü durum kurtarmayı ayarlama

[Azure Site Recovery](site-recovery-overview.md) hizmeti, şirket içi makinelerin ve Azure sanal makinelerinin (VM) çoğaltma, yük devretme ve yeniden çalışma işlemlerini yönetip düzenleyerek olağanüstü durum kurtarma stratejinize katkıda bulunur.

Bu öğreticide, Azure sanal makineleri için bir Azure bölgesinden diğerine çoğaltılarak olağanüstü durum kurtarmayı ayarlama konusu gösterilmektedir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kurtarma Hizmetleri kasası oluşturma
> * Hedef kaynak ayarlarını doğrulama
> * VM 'Ler için giden ağ bağlantısını ayarlama
> * Sanal makine için çoğaltmayı etkinleştirme

> [!NOTE]
> Bu makale, en basit ayarlarla olağanüstü durum kurtarma dağıtımı için yönergeler sağlar. Özelleştirilmiş ayarlar hakkında bilgi edinmek istiyorsanız, [nasıl yapılır bölümü](azure-to-azure-how-to-enable-replication.md)altındaki makaleleri gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- [Senaryo mimarisini ve bileşenlerini](concepts-azure-to-azure-architecture.md) gözden geçirin.
- Başlamadan önce [destek gereksinimlerini](site-recovery-support-matrix-azure-to-azure.md) gözden geçirin.

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kaynak bölgesi dışında herhangi bir bölgede kasayı oluşturun.

1. [Azure Portal](https://portal.azure.com) > **Kurtarma Hizmetleri**’nde oturum açın.
2. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin. Ardından **yedekleme ve Site Recovery** > **Yönetim Araçları** ' nı seçin.
3. **Ad** alanında, kasayı tanımlamak için bir kolay ad belirtin. Birden fazla aboneliğiniz varsa uygun olanı seçin.
4. Kaynak grubu oluşturun veya var olan bir grubu seçin. Bir Azure bölgesi belirtin. Desteklenen bölgeleri kontrol etmek için [Azure Site Recovery Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/site-recovery/) bölümündeki coğrafi kullanılabilirlik kısmına bakın.
5. Panodan kasaya hızlıca erişmek için önce **Panoya sabitle** seçeneğine ve sonra **Oluştur**’a tıklayın.

   ![Yeni kasa](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Yeni kasa, **Pano**’da **Tüm kaynaklar** bölümüne ve ana **Kurtarma Hizmetleri kasaları** sayfasına eklenir.

## <a name="verify-target-resource-settings"></a>Hedef kaynak ayarlarını doğrulama

1. Azure aboneliğinizin hedef bölgede VM 'Ler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.
2. Aboneliğinizin, kaynak VM 'larınızla eşleşen VM boyutlarını desteklemek için yeterli kaynağa sahip olduğundan emin olun. Site Recovery, hedef VM için aynı boyutu veya mümkün olan en yakın boyutu seçer.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>VM 'Ler için giden ağ bağlantısını ayarlama

Site Recovery beklendiği gibi çalışması için, çoğaltmak istediğiniz VM 'lerden giden ağ bağlantısını değiştirmeniz gerekir.

> [!NOTE]
> Site Recovery, ağ bağlantısını denetlemek için bir kimlik doğrulama proxy 'si kullanmayı desteklemez.


### <a name="outbound-connectivity-for-urls"></a>URL'ler için giden bağlantı

Giden bağlantıyı denetlemek için URL tabanlı bir güvenlik duvarı proxy 'si kullanıyorsanız, bu URL 'Lere erişim izni verin.

| **URL** | **Ayrıntılar** |
| ------- | ----------- |
| *.blob.core.windows.net | Verilerin VM’den kaynak bölgedeki önbellek depolama hesabına yazılmasına izin verir. |
| login.microsoftonline.com | Site Recovery hizmet URL’leri için yetkilendirme ve kimlik doğrulama özellikleri sağlar. |
| *.hypervrecoverymanager.windowsazure.com | VM’nin Site Recovery hizmetiyle iletişim kurmasına izin verir. |
| *.servicebus.windows.net | VM’nin Site Recovery izleme ve tanılama verilerini yazmasına izin verir. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP adresi aralıkları için giden bağlantı

URL 'Ler yerine IP adreslerini kullanarak giden bağlantıyı denetlemek istiyorsanız IP tabanlı güvenlik duvarları, proxy veya NSG kuralları için bu adreslere izin verin.

  - [Microsoft Azure Veri Merkezi IP Aralıkları](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Almanya’daki Windows Azure Veri Merkezi IP Aralıkları](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Çin’deki Windows Azure Veri Merkezi IP Aralıkları](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Office 365 URL’leri ve IP adresi aralıkları](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery hizmeti uç nokta IP adresleri](https://aka.ms/site-recovery-public-ips)

NSG kullanıyorsanız, kaynak bölge için bir depolama hizmeti etiketi NSG kuralları oluşturabilirsiniz. [Daha fazla bilgi edinin](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

## <a name="verify-azure-vm-certificates"></a>Azure VM sertifikalarını doğrulama

Çoğaltmak istediğiniz VM 'Lerin en son kök sertifikalara sahip olup olmadığını denetleyin. Bunlar, güvenlik kısıtlamaları nedeniyle Site Recovery sanal makine tarafından kaydedilmemişse.

- Windows VM’ler için, güvenilir kök sertifikaların tamamı makinede mevcut olacak şekilde sanal makineye en son Windows güncelleştirmelerinin tümünü yükleyin. Bağlantısı kesilmiş bir ortamda, kuruluşunuz için standart Windows Update ve sertifika güncelleştirme işlemlerini uygulayın.
- Linux VM’ler için, sanal makinedeki en son güvenilir kök sertifikaları ve sertifika iptal listesini almak için Linux dağıtıcınız tarafından sağlanan yönergeleri izleyin.

## <a name="set-permissions-on-the-account"></a>Hesapta izinleri ayarlama

Azure Site Recovery, Site Recovery yönetim işlemlerini denetlemek için üç yerleşik rol sağlar.

- **Site Recovery Katkıda Bulunanı** - Bu rol, Kurtarma Hizmetleri kasasında Site Recovery işlemlerini yönetmek için gereken tüm izinlere sahiptir. Ancak bu role sahip kullanıcı, Kurtarma Hizmetleri kasasını oluşturamaz veya silemez ya da diğer kullanıcılara erişim hakkı atayamaz. Bu rol, uygulamalar veya kuruluşların tamamı için olağanüstü durum kurtarmayı etkinleştirebilen ve yönetebilen olağanüstü durum kurtarma yöneticileri için idealdir.

- **Site Recovery Operatörü** - Bu rol, Yük Devretme ve Yeniden Çalışma işlemlerini yürütme ve yönetme izinlerine sahiptir. Bu role sahip olan kullanıcı çoğaltmayı etkinleştirip devre dışı bırakabilir, kasaları oluşturup silebilir, yeni altyapılar kaydedebilir veya diğer kullanıcılara erişim hakkı atayabilir. Bu rol, uygulama sahipleri ve BT yöneticileri tarafından istendiğinde sanal makinelerin veya uygulamaların yükünü devredebilen olağanüstü durum kurtarma operatörü için idealdir. Olağanüstü durum sonrası çözümde DR operatörü sanal makineleri yeniden koruyabilir ve yeniden çalıştırabilir.

- **Site Recovery Okuyucusu** - Bu rol tüm Site Recovery yönetim işlemlerini görüntüleme iznine sahiptir. Bu rol, mevcut koruma durumunu izleyebilen ve destek biletleri oluşturabilen BT izleme yöneticisi için idealdir.

[Azure RBAC yerleşik rolleri](../role-based-access-control/built-in-roles.md)hakkında daha fazla bilgi edinin.

## <a name="enable-replication-for-a-vm"></a>Sanal makine için çoğaltmayı etkinleştirme

### <a name="select-the-source"></a>Kaynağı seçme

1. Kurtarma Hizmetleri kasalarında, Kasa adı > **+Çoğalt** seçeneğine tıklayın.
2. **Kaynak** bölümünde **Azure** seçeneğini belirleyin.
3. **Kaynak konumu**’nda, VM’lerinizin çalışmakta olduğu kaynak Azure bölgesini seçin.
4. Sanal makinelerin çalıştığı **Kaynak aboneliği** seçin. Bu abonelik, kurtarma hizmetleri kasanızın bulunduğu Azure Active Directory kiracısında bulunan aboneliklerden biri olabilir.
5. **Kaynak kaynak grubunu**seçin ve **Tamam** ' a tıklayarak ayarları kaydedin.

    ![Kaynağı ayarlama](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>VM’leri seçme

Site Recovery, abonelik ve kaynak grup/bulut hizmeti ile ilişkili VM’lerin listesini alır.

1. **Sanal Makineler** bölümünde çoğaltmak istediğiniz VM’leri seçin.
2. **OK (Tamam)** düğmesine tıklayın.

### <a name="configure-replication-settings"></a>Çoğaltma ayarlarını yapılandırma

Site Recovery, hedef bölge için varsayılan ayarları ve çoğaltma ilkesini oluşturur. Ayarları gerektiği gibi değiştirebilirsiniz.

1. Hedef ve çoğaltma ayarlarını görüntülemek için **Ayarlar**’a tıklayın.
2. Varsayılan hedef ayarlarını geçersiz kılmak için **kaynak grubu, ağ, depolama ve kullanılabilirlik**' ın yanındaki **Özelleştir** ' e tıklayın.

   ![Ayarları yapılandırma](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Hedef ayarları tabloda özetlenen şekilde özelleştirin.

    **Ayar** | **Ayrıntılar**
    --- | ---
    **Hedef abonelik** | Varsayılan olarak, hedef abonelik, kaynak abonelikle aynı olur. Aynı Azure Active Directory kiracısındaki farklı bir hedef aboneliği seçmek için 'Özelleştir'e tıklayın.
    **Hedef konum** | Olağanüstü durum kurtarma için kullanılan hedef bölge.<br/><br/> Hedef konumun Site Recovery kasasının konumuyla eşleşmesini öneririz.
    **Hedef kaynak grubu** | Yük devretmeden sonra Azure VM 'Leri tutan hedef bölgedeki kaynak grubu.<br/><br/> Site Recovery, varsayılan olarak hedef bölgede "asr" sonekine sahip yeni bir kaynak grup oluşturur. Hedef kaynak grubunun konumu, kaynak sanal makinelerinizin barındırıldığı bölge dışında herhangi bir bölge olabilir.
    **Hedef sanal ağ** | Hedef bölgedeki sanal makineler, yük devretmeden sonra bulunur.<br/><br/> Site Recovery varsayılan olarak hedef bölgede "asr" sonekine sahip yeni bir sanal ağ (ve alt ağlarını) oluşturur.
    **Önbellek depolama hesapları** | Site Recovery, kaynak bölgedeki bir depolama hesabını kullanır. Kaynak VM’lere yönelik değişiklikler, hedef konuma çoğaltılmadan önce bu hesaba gönderilir.<br/><br/> Güvenlik Duvarı etkinleştirilmiş önbellek depolama hesabı kullanıyorsanız, **Güvenilen Microsoft hizmetlerine Izin ver**' i etkinleştirdiğinizden emin olun. [Daha fazla bilgi edinin.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Hedef depolama hesapları (kaynak VM, yönetilmeyen diskler kullanır)** | Varsayılan olarak Site Recovery, kaynak VM depolama hesabını yansıtmak için hedef bölgede yeni bir depolama hesabı oluşturur.<br/><br/> Güvenlik Duvarı etkinleştirilmiş önbellek depolama hesabı kullanıyorsanız **Güvenilen Microsoft hizmetlerine Izin ver** ' i etkinleştirin.
    **Yönetilen çoğaltma diskleri (kaynak VM yönetilen diskleri kullanıyorsa)** | Varsayılan olarak Site Recovery, kaynak VM 'nin yönetilen diskiyle aynı depolama türüyle (Standart veya Premium) kaynak sanal makinenin yönetilen disklerini yansıtmak için hedef bölgede çoğaltma tarafından yönetilen diskler oluşturur. Yalnızca disk türünü özelleştirebilirsiniz 
    **Hedef kullanılabilirlik kümeleri** | Varsayılan olarak, Azure Site Recovery hedef bölgede, kaynak bölgedeki bir kullanılabilirlik kümesinin VM 'Leri bölümünde "ASR" sonekine sahip olan yeni bir kullanılabilirlik kümesi oluşturur. Azure Site Recovery tarafından oluşturulan kullanılabilirlik kümesi zaten mevcut olduğunda, yeniden kullanılır.
    **Hedef kullanılabilirlik alanları** | Varsayılan olarak, hedef bölge kullanılabilirlik bölgelerini destekliyorsa, Site Recovery hedef bölgedeki kaynak bölgeyle aynı bölge numarasını atar.<br/><br/> Hedef bölge kullanılabilirlik bölgelerini desteklemiyorsa, hedef VM 'Ler varsayılan olarak tek örnek olarak yapılandırılır.<br/><br/> VM 'Leri hedef bölgedeki bir kullanılabilirlik kümesinin parçası olarak yapılandırmak için **Özelleştir** ' e tıklayın.<br/><br/> Çoğaltmayı etkinleştirdikten sonra kullanılabilirlik türünü (tek örnek, kullanılabilirlik kümesi veya kullanılabilirlik bölgesi) değiştiremezsiniz. Kullanılabilirlik türünü değiştirmek için çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.

4. Çoğaltma İlkesi ayarlarını özelleştirmek için, **Çoğaltma İlkesi**' nin yanındaki **Özelleştir** ' e tıklayın ve ayarları gerektiği gibi değiştirin.

    **Ayar** | **Ayrıntılar**
    --- | ---
    **Çoğaltma ilkesi adı** | İlke adı.
    **Kurtarma noktası bekletme** | Varsayılan olarak, Site Recovery kurtarma noktalarını 24 saat korur. 1 ile 72 saat arasında bir değer yapılandırabilirsiniz.
    **Uygulamayla tutarlı anlık görüntü sıklığı** | Varsayılan olarak, Site Recovery her 4 saatte bir uygulamayla tutarlı bir anlık görüntü alır. 1 ile 12 saat arasında bir değer yapılandırabilirsiniz.<br/><br/> Uygulamayla tutarlı bir anlık görüntü, VM içindeki uygulama verilerinin zaman içinde bir noktadaki anlık görüntüsüdür. Birim Gölge Kopyası Hizmeti (VSS), anlık görüntü alınırken VM’deki uygulamanın tutarlı bir durumda olmasını sağlar.
    **Çoğaltma grubu** | Uygulamanızın VM 'lerde çoklu VM tutarlılığı gerekiyorsa, bu VM 'Ler için bir çoğaltma grubu oluşturabilirsiniz. Seçilen VM’ler varsayılan olarak hiçbir çoğaltma grubunun parçası değildir.

5. Yeni veya mevcut bir çoğaltma grubuna VM 'Ler eklemek istiyorsanız, **Özelleştir**' de, çoklu VM tutarlılığı için **Evet** ' i seçin. Daha sonra, **Tamam**'a tıklayın. 

    >[!NOTE]
    >- Bir çoğaltma grubundaki tüm makineler, yük devredildiği zaman, paylaşılan kilitlenme ile tutarlı ve uygulamayla tutarlı kurtarma noktalarına sahiptir.
    >- Çoklu VM tutarlılığını etkinleştirmek, iş yükü performansını etkileyebilir (CPU kullanımı yoğun). Yalnızca makineler aynı iş yükünü çalıştırıyorsa ve birden çok makine arasında tutarlılık olması durumunda kullanılmalıdır.
    >- Bir çoğaltma grubunda en fazla 16 VM olabilir.
    >- Çoklu VM tutarlılığını etkinleştirirseniz çoğaltma grubundaki makineler birbiriyle 20004 numaralı bağlantı noktası üzerinden iletişim kurar. Bu bağlantı noktası üzerinden VM 'Ler arasındaki iç iletişimi engelleyen bir güvenlik duvarı olmadığından emin olun.
    >- Bir çoğaltma grubundaki Linux sanal makineleri için, 20004 numaralı bağlantı noktasındaki giden trafiğin Linux sürümüne yönelik kılavuza göre el ile açık olduğundan emin olun.



### <a name="configure-encryption-settings"></a>Şifreleme ayarlarını yapılandırma

Kaynak VM 'de Azure disk şifrelemesi (ADE) etkinse ayarları gözden geçirin.

1. Ayarları doğrulayın:
    - **Disk şifreleme anahtarı kasaları**: varsayılan olarak Site Recovery, kaynak VM disk şifreleme anahtarları üzerinde "ASR" sonekiyle yeni bir Anahtar Kasası oluşturur. Anahtar Kasası zaten varsa, yeniden kullanılır.
    - **Anahtar şifreleme anahtarı kasaları**: varsayılan olarak Site Recovery, hedef bölgede yeni bir Anahtar Kasası oluşturur. Ad "ASR" sonekine sahiptir ve kaynak VM anahtar şifreleme anahtarlarını temel alır. Site Recovery tarafından oluşturulan Anahtar Kasası zaten varsa, yeniden kullanılır.

2. Özel anahtar kasaları seçmek için **Özelleştir** ' e tıklayın.

>[!NOTE]
>Yalnızca Windows işletim sistemlerini çalıştıran ve [Azure AD uygulamasıyla şifreleme için etkinleştirilen](https://aka.ms/ade-aad-app) Azure vm 'leri Azure Site Recovery tarafından desteklenmektedir.
>

### <a name="track-replication-status"></a>Çoğaltma durumunu izleme

1. En son durumu görüntülemek için **Ayarlar**’da **Yenile**’ye tıklayın.
2. İlerleme durumunu ve durumu aşağıdaki gibi izleyin:
    - **Ayarlar** > **işleri** **Site Recovery işleri** > , **Korumayı etkinleştirme** işinin ilerlemesini izleyin.
    - **Ayarlar** > **Çoğaltılan Öğeler** bölümünde VM’lerin durumunu ve ilk çoğaltma ilerleme durumunu görüntüleyebilirsiniz. Ayarlarının detayına gitmek için VM’ye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure VM’si için olağanüstü durum kurtarmayı yapılandırdınız. Artık yük devretmenin beklendiği gibi çalıştığını denetlemek için bir olağanüstü durum kurtarma detayına gidebilirsiniz.

> [!div class="nextstepaction"]
> [Olağanüstü durum kurtarma tatbikatı çalıştırma](azure-to-azure-tutorial-dr-drill.md)
