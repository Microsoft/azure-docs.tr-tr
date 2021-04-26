---
title: Azure Kaynak taşıyıcısı ile kaynakları başka bir bölgeye taşıma
description: Kaynak grubu içindeki kaynakları Azure Kaynak taşıyıcısı ile başka bir bölgeye taşımayı öğrenin.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 79224c14fc5182df7a699864af3d78c9be36259f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797290"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Kaynakları bölgeler arasında taşıma (kaynak grubundan)

Bu makalede, belirli bir kaynak grubundaki kaynakları farklı bir Azure bölgesine taşımayı öğrenin. Kaynak grubunda, taşımak istediğiniz kaynakları seçersiniz. Daha sonra, [Azure Kaynak taşıyıcısı](overview.md)kullanarak bunları taşırsınız.

> [!IMPORTANT]
> Azure Kaynak taşıyıcısı Şu anda genel önizleme aşamasındadır.


## <a name="prerequisites"></a>Önkoşullar

- Taşımak istediğiniz kaynakların bulunduğu abonelikte *sahip* erişiminizin olması gerekir.
    - Bir Azure aboneliğinde belirli bir kaynak ve hedef eşleme için ilk kez kaynak eklediğinizde, kaynak taşıyıcısı abonelik tarafından güvenilen [sistem tarafından atanan bir yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (eski adıyla yönetilen hizmet tanımlaması (MSI)) oluşturur.
    - Kimliği oluşturmak ve gerekli rolü (katkıda bulunan ya da kaynak abonelikte Kullanıcı erişimi Yöneticisi) atamak için, kaynak eklemek için kullandığınız hesabın abonelikte *sahip* izinleri olması gerekir. Azure rolleri hakkında [daha fazla bilgi edinin](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) .
- Aboneliğin, hedef bölgede kaynak kaynakları oluşturmak için yeterli kotası olması gerekir. Değilse, ek sınırlamalar isteyin. [Daha fazla bilgi edinin](../azure-resource-manager/management/azure-subscription-service-limits.md).
- VM 'Leri taşıdığınız hedef Bölgeyle ilişkili fiyatlandırmayı ve ücretleri doğrulayın. Size yardımcı olması için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.
- Taşımak istediğiniz kaynakların kaynak taşıyıcısı tarafından desteklendiğinden emin olun:
    - Azure VM 'Leri ve ilişkili diskler
    - NIC’ler
    - Kullanılabilirlik kümeleri
    - Azure sanal ağları
    - Genel IP adresleri
    - Ağ güvenlik grupları (NSG)
    - İç ve genel yük dengeleyiciler
    - Azure SQL veritabanları ve elastik havuzlar


## <a name="check-vm-requirements"></a>VM gereksinimlerini denetleme

1. Taşımak istediğiniz VM 'Lerin desteklendiğinden emin olun.

    - Desteklenen Windows VM 'lerini [doğrulayın](support-matrix-move-region-azure-vm.md#windows-vm-support) .
    - Desteklenen Linux VM 'lerini ve çekirdek sürümlerini [doğrulayın](support-matrix-move-region-azure-vm.md#linux-vm-support) .
    - Desteklenen [işlem](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [depolama](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)ve [ağ](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) ayarlarını denetleyin.
2. VM 'Lerin en son Güvenilen Kök sertifikalara ve güncelleştirilmiş bir sertifika iptal listesine (CRL) sahip olduğundan emin olun. 
    - Windows çalıştıran Azure sanal makinelerinde en son Windows güncelleştirmelerini yükler.
    - Linux çalıştıran VM 'lerde, makinenin en son sertifikalara ve CRL 'ye sahip olduğundan emin olmak için Linux dağıtım kılavuzunu izleyin. 
3. VM 'lerden giden bağlantıya izin ver:
    - Giden bağlantıyı denetlemek için bir URL tabanlı güvenlik duvarı ara sunucusu kullanıyorsanız, bu [URL 'lere](support-matrix-move-region-azure-vm.md#url-access) erişim izni verin
    - Giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsanız, bu [hizmet etiketi kurallarını](support-matrix-move-region-azure-vm.md#nsg-rules)oluşturun.

## <a name="select-resources-to-move"></a>Taşınacak kaynakları seçin

Taşımak istediğiniz kaynakları seçin. Kaynakları kaynak bölgesi aboneliğindeki bir hedef bölgeye taşırsınız. Aboneliği değiştirmek istiyorsanız, kaynaklar taşındıktan sonra bunu yapabilirsiniz.

> [!NOTE]
>  İlişkili diskleri seçmeyin veya işlem başarısız olur. İlişkili diskler, sanal makine taşımasına otomatik olarak eklenir.

1. Azure portal ilgili kaynak grubunu açın.
2. Kaynak grubu sayfasında, taşımak istediğiniz kaynakları seçin.
3. Taşımayı   >  **başka bir bölgeye** taşı ' yı seçin.

    ![Kaynakları farklı bir bölgeye taşımak için seçim](./media/move-region-within-resource-group/select-move-region.png)
    
4. **Kaynak + hedef** bölümünde, kaynakları taşımak istediğiniz hedef bölgeyi seçin. Sonra **İleri**’yi seçin.


    ![Hedef bölgeyi seçmek için kaynak ve hedef sayfası](./media/move-region-within-resource-group/source-target.png)


7. **Taşınacak kaynaklar**' da **İleri**' yi seçin.  
8. **Kaynak Seç** bölümünde, taşımak istediğiniz kaynağı seçin. Yalnızca taşıma için desteklenen kaynaklar ekleyebilirsiniz. Ardından **Bitti**'yi seçin.
9. **Kaynakları taşı**' da **İleri**' yi seçin. 
10. **Gözden geçir + Ekle** bölümünde kaynak ve hedef ayrıntılarını kontrol edin.
11. Taşınan kaynaklarla ilgili meta verilerin bu amaç için oluşturulan bir kaynak grubunda depolandığını ve kaynak taşıyıcısı abonelik kaynaklarına erişmek için sistem tarafından yönetilen bir kimlik oluşturmasına izin vermeyi anladığınızdan emin olun.
1. Kaynakları eklemeye başlamak için **devam** ' ı seçin.

    ![Ayrıntıları denetlemek ve taşımaya devam etmek için Özet sayfası](./media/move-region-within-resource-group/summary.png)    

11. Kaynak ekleme işlemi başlar. İşlem tamamlandığında, bildirimler kaynakları eklendiğini ve dağıtım başarılı olduğunu gösterir.
13. Bildirimlerde, **taşıma için kaynak ekleme**' yi seçin.

    ![Bildirimlerde gösterilen ileti](./media/move-region-within-resource-group/notification.png)    


14. Bildirimi seçtikten sonra, seçtiğiniz kaynaklar Azure Kaynak taşıyıcısı hub 'ında bir taşıma koleksiyonuna eklenir.  Kaynak taşıyıcısı, bağımlılıkları denetlemenize ve kaynakları hedef bölgeye taşımaya başlamanıza yardımcı olur.

## <a name="resolve-dependencies"></a>Bağımlılıkları çözümle

Taşıdığınız kaynaklar, *hazırlama bekleme* durumunda **bölgeler arası** sayfasında görünür. Doğrulamayı aşağıdaki gibi başlatın:

1. Kaynaklar **sorunlar** sütununda *bağımlılıkları doğrula* Iletisini gösterip, **bağımlılıkları doğrula** düğmesini seçin. Doğrulama işlemi başlar.

    ![Bağımlılıkları doğrulamak için düğme](./media/move-region-within-resource-group/validate-dependencies.png)

2. Bağımlılıklar bulunursa, **Bağımlılıklar Ekle**' yi seçin. 
3. **Bağımlılıklar Ekle**' de, bağımlı kaynakları seçin > **Bağımlılıklar ekleyin**. Bildirimlerde ilerlemeyi izleyin.

    ![Bağımlılıklar eklemek için düğme](./media/move-region-within-resource-group/add-dependencies.png)

3. Gerekirse ek bağımlılıklar ekleyin ve bağımlılıkları gerektiği şekilde doğrulayın. Kaynakların güncel bir durumu göstermesini sağlamak için **Yenile** ' yi seçin.

4. **Bölgeler arası** sayfasında, kaynakların şimdi *hazırlama bekleme* durumunda olduğunu doğrulayın ve sorun yok.

    ![Tüm kaynaklar için hazırlık bekleme durumunu gösteren sayfa](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Kaynak kaynak grubunu taşıma 

Kaynakları hazırlayabilmeniz ve taşıyabilmeniz için önce kaynak kaynak grubunun hedef bölgede mevcut olması gerekir. 

### <a name="prepare-to-move-the-source-resource-group"></a>Çıkış kaynak grubunu taşımaya hazırlama

Aşağıdaki şekilde hazırlayın:

1. **Bölgeler arasında**, **hazırlama**> kaynak kaynak grubunu seçin.
2. **Kaynakları hazırlama** bölümünde **hazırla**' yı seçin.
1. 
    ![Kaynak kaynak grubunu hazırlama düğmesi](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Hazırlama işlemi sırasında, kaynak taşıyıcısı kaynak grubu ayarlarını kullanarak Azure Resource Manager (ARM) şablonları üretir. Kaynak grubu içindeki kaynaklar etkilenmez.
    
> [!NOTE]
>  Kaynak grubu hazırlandıktan sonra, bu *taşıma Işlemi başlatma beklemede* durumunda olur. En son durumu göstermek için yenileyin.

![Başlatma bekleme durumunu gösteren durum](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Kaynak kaynak grubunu taşıma

Taşımayı aşağıdaki gibi başlatın:

1. **Bölgeler arası** bölümünde kaynak grubunu seçerek **taşımayı başlatın** >
2. LN **Move kaynakları**, **taşımayı Başlat**' ı seçin. Kaynak grubu, *devam eden bir taşıma* durumuna geçer.
3. Taşıma başlatıldıktan sonra, hedef kaynak grubu oluşturulan ARM şablonuna göre oluşturulur. Kaynak kaynak grubu, bir *tamamlama taşıma bekleme* durumuna geçer.

![Tamamlama hareketini gösteren durum](./media/move-region-availability-zone/commit-move-pending.png)

Taşıma işlemini yürütmek ve tamamlamak için:

1. **Bölgeler arasında**, kaynak grubunu > **taşımayı kaydet** ' i seçin
2. LN **kaynak taşı**, **Kaydet**' i seçin.

> [!NOTE]
> Taşıma işlemi kaydedildikten sonra kaynak kaynak grubu *bekleyen bir kaynak silme* durumundadır.

## <a name="modify-target-settings"></a>Hedef ayarlarını değiştirme

Kaynak kaynağı taşımak istemiyorsanız, aşağıdakilerden birini yapabilirsiniz:

- Hedef bölgede kaynak bölgedeki kaynakla aynı ada ve ayarlara sahip bir kaynak oluşturun.
- Hedef bölgede yeni bir eşdeğer kaynak oluşturun. Belirttiğiniz ayarlar dışında, hedef kaynak kaynakla aynı ayarlarla oluşturulur.
- Hedef bölgede var olan bir kaynağı kullanın.

Bir ayarı aşağıdaki şekilde değiştirin:

1. Bir ayarı değiştirmek için kaynak için **hedef yapılandırma** sütununda girişi seçin.
2. **Hedef yapılandırma** sayfasında, kullanmak istediğiniz hedef ayarları belirtin.
    Değişiklikler yalnızca düzenlemekte olduğunuz kaynak için yapılır. Bağımlı kaynakları ayrı olarak güncelleştirmeniz gerekir.   
    
Değiştirdiğiniz tam ayarlar kaynak türüne bağlıdır. Hedef ayarlarını düzenlemeyle ilgili [daha fazla bilgi edinin](modify-target-settings.md) .

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

1. **Bölgeler arasında**, durum *başlatma taşıma bekleyen* kaynaklar ' ı seçin. Sonra **taşımayı Başlat**' ı seçin.
2. **Kaynakları taşıma** bölümünde **taşımayı Başlat**' ı seçin.

    ![Taşımayı Başlat düğmesi için seçin](./media/move-region-within-resource-group/initiate-move.png)

3. Bildirim çubuğunda taşıma ilerlemesini izleyin.


> [!NOTE]
> - VM 'Ler için çoğaltma VM 'Leri hedef bölgede oluşturulur. Kaynak VM kapanmıştır ve bazı kesinti süreleri oluşur (genellikle dakikalar).<br/>
> - Kaynak taşıyıcısı, hazırlanan ARM şablonlarını kullanarak diğer kaynakları yeniden oluşturur. Genellikle kapalı kalma süresi yoktur.<br/> 
> - Yük dengeleyiciler için NAT kuralları kopyalanmaz. Taşıma işleminden sonra bunları hedef bölgede oluşturun.
> - Genel IP adresleri için DNS ad etiketi kopyalanmaz. Taşıma işleminden sonra etiketi yeniden oluşturun.
> - Kaynaklar hazırlandıktan sonra, bunlar bir *taşıma taşıma bekliyor* durumundadır.


## <a name="discard-or-commit"></a>Atın veya teslim edilsin mi?

İlk taşıma işleminden sonra, taşımayı yürütmek mi yoksa atmak mi istediğinize karar verebilirsiniz. 

- **At**: test ediyorsanız bir taşıma atabilir ve kaynak kaynağı gerçekten taşımak istemezsiniz. Taşımayı atmak, kaynağı *başlatma bekleyen* bir durumuna döndürür.
- **COMMIT**: COMMIT, hedef bölgeye taşıma işlemini tamamlar. İşlem tamamlandıktan sonra kaynak kaynak, *bekleyen silme kaynağı* durumunda olur ve silmek istediğinize karar verebilirsiniz.


## <a name="discard-the-move"></a>Taşımayı at 

Taşımayı aşağıdaki şekilde atabilirsiniz:

1. **Bölgeler arasında**, durum *işlemede taşıma bekleyen* kaynaklar ' ı seçin ve **taşımayı at**' ı seçin.
2. **Taşımayı at**' da **at**' ı seçin.
3. Bildirim çubuğunda taşıma ilerlemesini izleyin.
4. Bildirimler taşımanın başarılı olduğunu gösterir, **Yenile**' yi seçin. 

> [!NOTE]
> VM 'Ler için kaynakları atladıktan sonra, bir *taşıma beklemede* durumunda olurlar.

## <a name="commit-the-move"></a>Taşımayı Yürüt

Taşıma işlemini tamamlamak istiyorsanız, taşımayı yürütün. 


1. **Bölgeler arasında**, durum *işlemede taşıma bekleyen* kaynaklar ' ı seçin ve **taşımayı Yürüt**' ü seçin.
2. **Kaynakları kaydet**' de, **Yürüt**' ü seçin.

    ![Taşıma işlemini sonlandırmak için kaynakları yürütmek için sayfa](./media/move-region-within-resource-group/commit-resources.png)

3. Bildirim çubuğunda tamamlama ilerlemesini izleyin.

> [!NOTE]
> - Taşıma işlemi tamamlandıktan sonra VM 'Ler çoğaltmayı durdurur. Kaynak VM, işlemeden etkilenmez.
> - Kayıt, kaynak ağ kaynaklarını etkilemez.
> - Taşıma işlemi tamamlandıktan sonra kaynaklar bir *silme kaynağı bekliyor* durumundadır.

## <a name="configure-settings-after-the-move"></a>Taşıma işleminden sonra ayarları Yapılandır

1. DNS ad etiketleri genel IP adresleri için üzerine kopyalanmadığından, taşıma işlemi tamamlandıktan sonra hedef kaynaklara gidin ve etiketi güncelleştirin. 
2. İç yük dengeleyiciler için, NAT kuralları üzerine kopyalanmadığından, hedef bölgede oluşturulan kaynaklara gidin ve NAT kurallarını güncelleştirin.
3. Mobility hizmeti VM 'lerden otomatik olarak kaldırılmaz. El ile kaldırın veya sunucuyu yeniden taşımayı planlıyorsanız bırakın.
## <a name="delete-source-resources-after-commit"></a>İşlemeden sonra kaynak kaynaklarını Sil

Taşıma işleminden sonra, kaynak bölgedeki kaynakları isteğe bağlı olarak silebilirsiniz. 

1. **Bölgeler arasında**' de, silmek istediğiniz her kaynak kaynağın adını seçin.
2. Her kaynak için Özellikler sayfasında **Sil**' i seçin.

## <a name="delete-additional-resources-created-for-move"></a>Taşıma için oluşturulan ek kaynakları Sil

Taşıma işleminden sonra, taşıma koleksiyonunu el ile silebilir ve oluşturulmuş kaynakları Site Recovery.

- Taşıma koleksiyonu varsayılan olarak gizlidir. Bunu görmek için gizli kaynakları açmanız gerekir.
- Önbellek depolamada silinmeden önce, silinmesi gereken bir kilit vardır.

Aşağıdaki gibi silin: 

1. Kaynak bölgedeki kaynak grubundaki kaynakları bulun ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Taşıma koleksiyonundaki tüm VM ve diğer kaynak kaynaklarının taşındığını/silindiğini kontrol edin. Bu, bunları kullanan beklemedeki kaynaklar olmamasını sağlar.
2. Kaynakları silin:

    - Taşıma koleksiyonu adı ```movecollection-<sourceregion>-<target-region>``` .
    - Önbellek depolama hesabı adı ```resmovecache<guid>```
    - Kasa adı ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Sonraki adımlar


Taşıma işlemi [hakkında bilgi edinin](about-move-process.md) .