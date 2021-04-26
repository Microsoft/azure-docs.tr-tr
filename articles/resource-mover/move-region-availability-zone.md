---
title: Azure Kaynak taşıyıcısı ile Azure VM 'lerini başka bir bölgedeki kullanılabilirlik bölgelerine taşıma
description: Azure Kaynak taşıyıcısı ile Azure VM 'lerini kullanılabilirlik bölgelerine taşımayı öğrenin.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543009"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Azure VM 'lerini başka bir bölgedeki kullanılabilirlik bölgesine taşıma

Bu makalede, Azure sanal makinelerini (ve ilgili ağ/depolama kaynaklarını) [Azure Kaynak taşıyıcısı](overview.md)kullanarak farklı bir Azure bölgesindeki bir kullanılabilirlik bölgesine taşımayı öğrenin.

[Azure kullanılabilirlik alanları](../availability-zones/az-overview.md#availability-zones) , Azure dağıtımınızın veri merkezi arızalarına karşı korunmasına yardımcı olur. Her kullanılabilirlik alanı, bağımsız enerji, soğutma ve ağ kaynaklarıyla donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm [etkin bölgelerde](../availability-zones/az-region.md)en az üç ayrı bölge vardır. Kaynak taşıyıcısı kullanarak şunları yapabilirsiniz:

- Hedef bölgedeki bir kullanılabilirlik bölgesine/kullanılabilirlik kümesine yönelik tek örnekli bir VM.
- Kullanılabilirlik alanındaki bir VM, hedef bölgede kullanılabilirlik alanı/kullanılabilirlik kümesine göre ayarlanmıştır.
- Kaynak bölge kullanılabilirlik bölgesindeki bir VM, hedef bölgedeki bir kullanılabilirlik bölgesine göre.


> [!IMPORTANT]
> Azure Kaynak taşıyıcısı Şu anda genel önizleme aşamasındadır.

VM 'Leri aynı bölgedeki farklı bir kullanılabilirlik bölgesine taşımak istiyorsanız, [Bu makaleyi gözden geçirin](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Önkoşullar

- Taşımak istediğiniz kaynakların bulunduğu abonelikte *sahip* erişimi bulunur.
    - Bir Azure aboneliğinde belirli bir kaynak ve hedef eşleme için ilk kez kaynak eklediğinizde, kaynak taşıyıcısı abonelik tarafından güvenilen [sistem tarafından atanan bir yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (eski adıyla yönetilen hizmet tanımlaması (MSI)) oluşturur.
    - Kimliği oluşturmak ve gerekli rolü (katkıda bulunan ya da kaynak abonelikte Kullanıcı erişimi Yöneticisi) atamak için, kaynak eklemek için kullandığınız hesabın abonelikte *sahip* izinleri olması gerekir. Azure rolleri hakkında [daha fazla bilgi edinin](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) .
- Aboneliğin, hedef bölgede kaynak kaynakları oluşturmak için yeterli kotası olması gerekir. Değilse, ek sınırlamalar isteyin. [Daha fazla bilgi edinin](../azure-resource-manager/management/azure-subscription-service-limits.md).
- VM 'Leri taşıdığınız hedef Bölgeyle ilişkili fiyatlandırmayı ve ücretleri doğrulayın. Size yardımcı olması için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.
    


## <a name="check-vm-requirements"></a>VM gereksinimlerini denetleme

1. Taşımak istediğiniz VM 'Lerin desteklendiğinden emin olun.

    - Desteklenen Windows VM 'lerini [doğrulayın](support-matrix-move-region-azure-vm.md#windows-vm-support) .
    - Desteklenen Linux VM 'lerini ve çekirdek sürümlerini [doğrulayın](support-matrix-move-region-azure-vm.md#linux-vm-support) .
    - Desteklenen [işlem](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [depolama](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)ve [ağ](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) ayarlarını denetleyin.
2. Taşımak istediğiniz sanal makinelerin açık olduğundan emin olun.
3. VM 'Lerin en son Güvenilen Kök sertifikalara ve güncelleştirilmiş bir sertifika iptal listesine (CRL) sahip olduğundan emin olun. 
    - Windows çalıştıran Azure sanal makinelerinde en son Windows güncelleştirmelerini yükler.
    - Linux çalıştıran VM 'lerde, makinenin en son sertifikalara ve CRL 'ye sahip olduğundan emin olmak için Linux dağıtım kılavuzunu izleyin. 
4. VM 'lerden giden bağlantıya izin ver:
    - Giden bağlantıyı denetlemek için bir URL tabanlı güvenlik duvarı ara sunucusu kullanıyorsanız, bu [URL 'lere](support-matrix-move-region-azure-vm.md#url-access) erişim izni verin
    - Giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsanız, bu [hizmet etiketi kurallarını](support-matrix-move-region-azure-vm.md#nsg-rules)oluşturun.

## <a name="select-resources-to-move"></a>Taşınacak kaynakları seçin

Taşımak istediğiniz kaynakları seçin.

- Seçtiğiniz kaynak bölgedeki kaynak grupları arasında desteklenen herhangi bir kaynak türünü seçebilirsiniz.
- Kaynakları kaynak bölgesi aboneliğindeki bir hedef bölgeye taşırsınız. Aboneliği değiştirmek istiyorsanız, kaynaklar taşındıktan sonra bunu yapabilirsiniz.

1. Azure portal, kaynak taşıyıcısı için arama yapın. Ardından, **Hizmetler**' ın altında **Azure Kaynak taşıyıcısı**' ı seçin.

    ![Kaynak taşıyıcısı arama](./media/move-region-availability-zone/search.png)

2. **Genel bakış**' da **başlayın ' ı seçin.**

    ![Kullanmaya başlamak için düğme](./media/move-region-availability-zone/get-started.png)

3. Kaynak **taşıma**  >  **kaynağı + hedef** bölümünde, kaynak aboneliğini ve bölgeyi seçin.
4. **Hedef** bölümünde, VM 'leri taşımak istediğiniz bölgeyi seçin. Ardından **İleri**'ye tıklayın.

     ![Kaynak ve hedef abonelik/bölge doldurulacak sayfa](./media/move-region-availability-zone/source-target.png)

6. **Taşınacak kaynaklar** bölümünde **kaynakları seç**' e tıklayın.
7. **Kaynakları seçin** bölümünde VM 'yi seçin. Yalnızca taşıma için desteklenen kaynaklar ekleyebilirsiniz. Sonra da **Bitti**’ye tıklayın. **Taşınacak kaynaklar** bölümünde **İleri**' ye tıklayın.

    ![Taşınacak VM 'Leri seçmek için sayfa](./media/move-region-availability-zone/select-vm.png)
8. **Gözden geçir + Ekle** bölümünde kaynak ve hedef ayarlarını kontrol edin.

    ![Ayarları gözden geçirmek ve taşımaya devam etmek için sayfa](./media/move-region-availability-zone/review.png)

9. Kaynakları eklemeye başlamak için **devam**' a tıklayın.
10. Ekleme işlemi başarıyla tamamlandıktan sonra, bildirim simgesine **taşımak için kaynak ekleme** ' ye tıklayın.

    ![Bildirimlerde ileti](./media/move-region-availability-zone/notification.png)

Bildirime tıkladıktan sonra, kaynaklar **bölgeler arası** sayfasında görünür

> [!NOTE]
> Bildirime tıkladıktan sonra kaynaklar **bölgeler arası** sayfasında, *hazırlama bekleme* durumunda görünür.
> - Bir taşıma koleksiyonundan bir kaynağı kaldırmak istiyorsanız, bunu yapmak için yöntemi taşıma sürecinde nerede olduğunuza bağlıdır. [Daha fazla bilgi edinin](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Bağımlılıkları çözümle

1. Kaynaklar **sorunlar** sütununda bir *bağımlılıkları doğrula* iletisi gösterince, **bağımlılıkları doğrula** düğmesine tıklayın. Doğrulama süreci.
2. Bağımlılıklar bulunursa, **Bağımlılıklar Ekle**' ye tıklayın. 
3. **Bağımlılıklar Ekle**' de, bağımlı kaynakları seçin > **Bağımlılıklar ekleyin**. Bildirimlerde ilerlemeyi izleyin.

    ![Bağımlılıklar eklemek için düğme](./media/move-region-availability-zone/add-dependencies.png)

3. Gerekirse ek bağımlılıklar ekleyin ve bağımlılıkları yeniden doğrulayın. 

    ![Ek bağımlılıklar eklemek için sayfa](./media/move-region-availability-zone/add-additional-dependencies.png)

4. **Bölgeler arası** sayfasında, kaynakların şimdi *hazırlama bekleme* durumunda olduğunu doğrulayın ve sorun yok.

    ![Hazırlama Bekleme durumundaki kaynakları gösteren sayfa](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Kaynak kaynak grubunu taşıma 

VM 'Leri hazırlayabilmeniz ve taşıyabilmeniz için önce kaynak kaynak grubunun hedef bölgede mevcut olması gerekir. 

### <a name="prepare-to-move-the-source-resource-group"></a>Çıkış kaynak grubunu taşımaya hazırlama

Aşağıdaki şekilde hazırlayın:

1. **Bölgeler arasında**, **hazırlama**> kaynak kaynak grubunu seçin.
2. **Kaynakları hazırlama** bölümünde **hazırla**' ya tıklayın.

    ![Kaynak kaynak grubunu hazırlama düğmesi](./media/move-region-availability-zone/prepare-resource-group.png)

    Hazırlama işlemi sırasında, kaynak taşıyıcısı kaynak grubu ayarlarını kullanarak Azure Resource Manager (ARM) şablonları üretir. Kaynak grubu içindeki kaynaklar etkilenmez.

> [!NOTE]
>  Kaynak grubu hazırlandıktan sonra, bu *taşıma Işlemi başlatma beklemede* durumunda olur. 

![Başlatma bekleme durumunu gösteren durum](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Kaynak kaynak grubunu taşıma

Taşımayı aşağıdaki gibi başlatın:

1. **Bölgeler arası** bölümünde kaynak grubunu seçerek **taşımayı başlatın** >
2. LN **Move kaynakları**, **taşımayı Başlat**' a tıklayın. Kaynak grubu, *devam eden bir taşıma* durumuna geçer.
3. Taşıma başlatıldıktan sonra, hedef kaynak grubu oluşturulan ARM şablonuna göre oluşturulur. Kaynak kaynak grubu, bir *tamamlama taşıma bekleme* durumuna geçer.

![Tamamlama hareketini gösteren durum](./media/move-region-availability-zone/commit-move-pending.png)

Taşıma işlemini yürütmek ve tamamlamak için:

1. **Bölgeler arasında**, kaynak grubunu > **taşımayı kaydet** ' i seçin
2. LN **Move kaynakları**, **Kaydet**' e tıklayın.

> [!NOTE]
> Taşıma işlemi kaydedildikten sonra kaynak kaynak grubu *bekleyen bir kaynak silme* durumundadır.


## <a name="add-a-target-availability-zone"></a>Hedef kullanılabilirlik bölgesi ekleme

Kalan kaynakları taşımadan önce VM için bir hedef kullanılabilirlik bölgesi ayarlayacağız.

1. **Bölgeler arası** sayfasında, taşıdığınız sanal makinenin **hedef yapılandırması** sütunundaki bağlantıya tıklayın.

    ![VM özellikleri](./media/move-region-availability-zone/select-vm-settings.png)

3. **Yapılandırma ayarları**' nda hedef VM için ayarı belirtin. Hedef bölgede bir VM 'yi şu şekilde yapılandırabilirsiniz:
    -  Hedef bölgede yeni bir eşdeğer kaynak oluşturun. Belirttiğiniz ayarlar dışında, hedef kaynak kaynakla aynı ayarlarla oluşturulur.
    - Hedef bölgede var olan bir VM 'yi seçin. 

4. **Bölgeler**' de, sanal makineyi taşırken yerleştirmek istediğiniz bölgeyi seçin.

    Değişiklikler yalnızca düzenlemekte olduğunuz kaynak için yapılır. Bağımlı kaynakları ayrı olarak güncelleştirmeniz gerekir.

5. **SKU**'da, hedef VM 'ye atamak istediğiniz [Azure katmanını](..//virtual-machines/sizes.md) belirtin.
6. Kullanılabilirlik **kümesi**' nde, hedef VM 'nin kullanılabilirlik bölgesindeki bir kullanılabilirlik kümesi içinde çalışmasını istiyorsanız bir kullanılabilirlik kümesi seçin.
7. **Değişiklikleri kaydet**'i seçin.

    ![VM ayarları](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Taşınacak kaynakları hazırlama

Kaynak kaynak grubu taşındığına göre, diğer kaynakları taşımaya hazırlanabilirsiniz.

1. **Bölgeler arasında**, hazırlamak istediğiniz kaynakları seçin. 

    ![Diğer kaynaklar için hazırla ' yı seçme sayfası](./media/move-region-availability-zone/prepare-other.png)

2. **Hazırla**' yı seçin. 

> [!NOTE]
> - Hazırlama işlemi sırasında, çoğaltma için Azure Site Recovery Mobility Aracısı VM 'Lere yüklenir.
> - VM verileri, hedef bölgeye düzenli aralıklarla çoğaltılır. Bu, kaynak VM 'yi etkilemez.
> - Kaynak taşıma diğer kaynak kaynakları için ARM şablonları oluşturur.
> - Kaynaklar hazırlandıktan sonra, bir *taşıma beklemede* durumunda olurlar.

![Başlatma taşıma bekleme durumunda kaynakları gösteren sayfa](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Taşımayı Başlat

Kaynaklar hazırlandıktan sonra, şimdi taşımayı başlatabilirsiniz. 

1. **Bölgeler arasında**, durum *başlatma taşıma bekleyen* kaynaklar ' ı seçin. Sonra **taşımayı Başlat** ' a tıklayın.
2. **Kaynakları taşıma** bölümünde, **taşımayı Başlat**' a tıklayın.

    ![Kaynak taşımayı başlatma sayfası](./media/move-region-availability-zone/initiate-move.png)

3. Bildirim çubuğunda taşıma ilerlemesini izleyin.


> [!NOTE]
> - VM 'Ler için çoğaltma VM 'Leri hedef bölgede oluşturulur. Kaynak VM kapanmıştır ve bazı kesinti süreleri oluşur (genellikle dakikalar).
> - Kaynak taşıyıcısı, hazırlanan ARM şablonlarını kullanarak diğer kaynakları yeniden oluşturur. Genellikle kapalı kalma süresi yoktur.
> - Kaynaklar hazırlandıktan sonra, bunlar bir *taşıma taşıma bekliyor* durumundadır.


![Kaynakları bir COMMIT taşıması bekleniyor durumunda göstermek için sayfa](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Atın veya teslim edilsin mi?

İlk taşıma işleminden sonra, taşımayı yürütmek mi yoksa atmak mi istediğinize karar verebilirsiniz. 

- **At**: test ediyorsanız bir taşıma atabilir ve kaynak kaynağı gerçekten taşımak istemezsiniz. Taşımayı atmak, kaynağı *başlatma bekleyen* bir durumuna döndürür.
- **COMMIT**: COMMIT, hedef bölgeye taşıma işlemini tamamlar. İşlem tamamlandıktan sonra kaynak kaynak, *bekleyen silme kaynağı* durumunda olur ve silmek istediğinize karar verebilirsiniz.

## <a name="discard-the-move"></a>Taşımayı at 

Taşımayı aşağıdaki şekilde atabilirsiniz:

1. **Bölgeler arasında**, durum *işlemede taşıma bekleyen* kaynaklar ' ı seçin ve **taşımayı at**' a tıklayın.
2. **Taşımayı at**' da **at**' a tıklayın.
3. Bildirim çubuğunda taşıma ilerlemesini izleyin.
 

> [!NOTE]
> VM 'Ler için kaynakları atladıktan sonra, bir *taşıma beklemede* durumunda olurlar.

## <a name="commit-the-move"></a>Taşımayı Yürüt

Taşıma işlemini tamamlamak istiyorsanız, taşımayı yürütün. 

1. **Bölgeler arasında**, durum *işlemede taşıma bekleyen* kaynaklar ' ı seçin ve **taşımayı Yürüt**' e tıklayın.
2. **Kaynakları kaydet**' de, **Yürüt**' e tıklayın.

    ![Taşıma işlemini sonlandırmak için kaynakları yürütmek için sayfa](./media/move-region-availability-zone/commit-resources.png)

3. Bildirim çubuğunda tamamlama ilerlemesini izleyin.

> [!NOTE]
> - Taşıma işlemi tamamlandıktan sonra VM 'Ler çoğaltmayı durdurur. Kaynak VM, işlemeden etkilenmez.
> - Kayıt, kaynak ağ kaynaklarını etkilemez.
> - Taşıma işlemi tamamlandıktan sonra kaynaklar bir *silme kaynağı bekliyor* durumundadır.

![* Kaynağı Sil bekliyor * durumunda kaynakları gösteren sayfa](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Taşıma işleminden sonra ayarları Yapılandır

Mobility hizmeti VM 'lerden otomatik olarak kaldırılmaz. El ile kaldırın veya sunucuyu yeniden taşımayı planlıyorsanız bırakın.


## <a name="delete-source-resources-after-commit"></a>İşlemeden sonra kaynak kaynaklarını Sil

Taşıma işleminden sonra, kaynak bölgedeki kaynakları isteğe bağlı olarak silebilirsiniz.

1. **Bölgeler arasında**, silmek istediğiniz her kaynak kaynağın adına tıklayın.
2. Her kaynak için Özellikler sayfasında **Sil**' i seçin.

## <a name="delete-additional-resources-created-for-move"></a>Taşıma için oluşturulan ek kaynakları Sil

Taşıma işleminden sonra, taşıma koleksiyonunu el ile silebilir ve oluşturulmuş kaynakları Site Recovery.

- Taşıma koleksiyonu varsayılan olarak gizlidir. Bunu görmek için gizli kaynakları açmanız gerekir.
- Önbellek depolamada silinmeden önce, silinmesi gereken bir kilit vardır.

Aşağıdaki gibi silin: 

1. Kaynak grubundaki kaynakları bulun ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Kaynak bölgedeki tüm VM ve diğer kaynak kaynaklarının taşındığını veya silindiğini kontrol edin. Bu, bunları kullanan beklemedeki kaynaklar olmamasını sağlar.
2. Kaynakları silin:

    - Taşıma koleksiyonu adı ```movecollection-<sourceregion>-<target-region>``` .
    - Önbellek depolama hesabı adı ```resmovecache<guid>```
    - Kasa adı ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Sonraki adımlar

Taşıma işlemi [hakkında bilgi edinin](about-move-process.md) .