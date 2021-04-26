---
title: Azure Kaynak taşıyıcısı ile Azure VM 'lerini bölgeler arasında taşıma
description: Azure Kaynak taşıyıcısı ile Azure VM 'lerini başka bir bölgeye taşımayı öğrenin
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5712448c8c5248d3c84ce43f8a41c669355f1d43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565742"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Öğretici: Azure VM 'lerini bölgeler arasında taşıma

Bu makalede, Azure sanal makinelerini ve ilgili ağ/depolama kaynaklarını [Azure Kaynak taşıyıcısı](overview.md)kullanarak farklı bir Azure bölgesine taşımayı öğrenin.
.


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları ve gereksinimleri denetleyin.
> * Taşımak istediğiniz kaynakları seçin.
> * Kaynak bağımlılıklarını çözümleyin.
> * Kaynak kaynak grubunu hazırlayın ve taşıyın. 
> * Diğer kaynakları hazırlayın ve taşıyın.
> * Taşımayı atmak mı yoksa kaydetmek mi istediğinize karar verin. 
> * Taşıma işleminden sonra kaynak bölgedeki kaynakları isteğe bağlı olarak kaldırın.

> [!NOTE]
> Öğreticiler, bir senaryoyu denemek için en hızlı yolu gösterir ve varsayılan seçenekleri kullanır. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun. Ardından [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="prerequisites"></a>Önkoşullar
**Gereksinim** | **Açıklama**
--- | ---
**Kaynak taşıyıcısı desteği** | Desteklenen bölgeleri ve diğer yaygın soruları [gözden geçirin](common-questions.md) .
**Abonelik izinleri** | Taşımak istediğiniz kaynakları içeren abonelikte *sahip* erişiminizin olduğunu denetleyin<br/><br/> **Neden sahip erişimine ihtiyacım var?** Azure aboneliğindeki belirli bir kaynak ve hedef çifti için ilk kez kaynak eklediğinizde, kaynak taşıyıcısı abonelik tarafından güvenilen [sistem tarafından atanan bir yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (eski adıyla yönetilen hizmet tanımlaması (MSI)) oluşturur. Kimliği oluşturmak ve gerekli rolü (katkıda bulunan ya da kaynak abonelikte Kullanıcı erişimi Yöneticisi) atamak için, kaynak eklemek için kullandığınız hesabın abonelikte *sahip* izinleri olması gerekir. Azure rolleri hakkında [daha fazla bilgi edinin](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) .
**VM desteği** |  Taşımak istediğiniz VM 'Lerin desteklendiğinden emin olun.<br/><br/> - Desteklenen Windows VM 'lerini [doğrulayın](support-matrix-move-region-azure-vm.md#windows-vm-support) .<br/><br/> - Desteklenen Linux VM 'lerini ve çekirdek sürümlerini [doğrulayın](support-matrix-move-region-azure-vm.md#linux-vm-support) .<br/><br/> -Desteklenen [işlem](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [depolama](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)ve [ağ](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) ayarlarını kontrol edin.
**Hedef abonelik** | Hedef bölgedeki aboneliğin, hedef bölgede taşıdığınız kaynakları oluşturmak için yeterli kotası olması gerekir. Kota içermiyorsa [ek sınırlamalar isteyin](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Hedef bölge ücretleri** | VM 'Leri taşıdığınız hedef Bölgeyle ilişkili fiyatlandırmayı ve ücretleri doğrulayın. Size yardımcı olması için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.
    

## <a name="prepare-vms"></a>VM 'Leri hazırlama

1. VM 'Lerin gereksinimleri karşılayıp karşılamadığını denetledikten sonra, taşımak istediğiniz sanal makinelerin açık olduğundan emin olun. Hedef bölgede kullanılabilir olmasını istediğiniz tüm VM disklerinin VM 'de eklenmiş ve başlatılmış olması gerekir.
1. VM 'Lerin en son Güvenilen Kök sertifikalara sahip olduğundan ve güncelleştirilmiş bir sertifika iptal listesi (CRL) olduğundan emin olun. Bunu yapmak için:
    - Windows VM 'lerinde en son Windows güncelleştirmelerini yükler.
    - Linux VM 'lerde, makinelerin en son sertifikalara ve CRL 'ye sahip olması için dağıtım kılavuzunu izleyin. 
1. VM 'lerden giden bağlantıya izin ver:
    - Giden bağlantıyı denetlemek için bir URL tabanlı güvenlik duvarı ara sunucusu kullanıyorsanız, bu [URL 'lere](support-matrix-move-region-azure-vm.md#url-access) erişim izni verin
    - Giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsanız, bu [hizmet etiketi kurallarını](support-matrix-move-region-azure-vm.md#nsg-rules)oluşturun.

## <a name="select-resources"></a>Kaynakları seçin 

Taşımak istediğiniz kaynakları seçin.

- Seçili kaynak bölgesi içindeki kaynak gruplarındaki tüm desteklenen kaynak türleri görüntülenir.
- Bölgeler arasında taşıma için önceden eklenmiş olan kaynaklar gösterilmez.
- Kaynakları kaynak bölgeyle aynı abonelikte yer alan bir hedef bölgeye taşırsınız. Aboneliği değiştirmek istiyorsanız, kaynaklar taşındıktan sonra bunu yapabilirsiniz.

1. Azure portal, *kaynak taşıyıcısı* için arama yapın. Ardından, **Hizmetler**' ın altında **Azure Kaynak taşıyıcısı**' ı seçin.

    ![Azure portal kaynak taşıyıcısı için arama sonuçları](./media/tutorial-move-region-virtual-machines/search.png)

2. **Genel bakış**' da **başlayın ' a tıklayın.**

    ![Başka bir bölgeye taşınacak kaynakları ekleme düğmesi](./media/tutorial-move-region-virtual-machines/get-started.png)

3. Kaynak **taşıma**  >  **kaynağı + hedef** bölümünde, kaynak aboneliğini ve bölgeyi seçin.
4. **Hedef** bölümünde, VM 'leri taşımak istediğiniz bölgeyi seçin. Ardından **İleri**'ye tıklayın.

    ![Kaynak ve hedef bölge seçme sayfası](./media/tutorial-move-region-virtual-machines/source-target.png)

6. **Taşınacak kaynaklar** bölümünde **kaynakları seç**' e tıklayın.
7. **Kaynakları seçin** bölümünde VM 'yi seçin. Yalnızca [taşıma için desteklenen kaynaklar](#prepare-vms)ekleyebilirsiniz. Sonra da **Bitti**’ye tıklayın.

    ![Taşınacak VM 'Leri seçmek için sayfa](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  **Taşınacak kaynaklar** bölümünde **İleri**' ye tıklayın.
9. **Gözden geçirme** bölümünde kaynak ve hedef ayarlarını kontrol edin. 

    ![Ayarları gözden geçirmek ve taşımaya devam etmek için sayfa](./media/tutorial-move-region-virtual-machines/review.png)
10. Kaynakları eklemeye başlamak için **devam**' a tıklayın.
11. Ekleme işlemi başarıyla tamamlandıktan sonra, bildirim simgesine **taşımak için kaynak ekleme** ' ye tıklayın.
12. Bildirime tıkladıktan sonra **bölgeler arası** sayfasında kaynakları gözden geçirin.

> [!NOTE]
> - Eklenen kaynaklar *hazırlama bekleme* durumunda.
> - VM 'Ler için kaynak grubu otomatik olarak eklenir.
> - Bir taşıma koleksiyonundan bir kaynağı kaldırmak istiyorsanız, bunu yapmak için yöntemi taşıma sürecinde nerede olduğunuza bağlıdır. [Daha fazla bilgi edinin](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Bağımlılıkları çözümle

1. Kaynaklar **sorunlar** sütununda bir *bağımlılıkları doğrula* iletisi gösterince, **bağımlılıkları doğrula** düğmesine tıklayın. Doğrulama işlemi başlar.
2. Bağımlılıklar bulunursa, **Bağımlılıklar Ekle**' ye tıklayın. 
3. **Bağımlılıklar Ekle**' de, varsayılan **tüm bağımlılıkları göster** seçeneğini bırakın.

    - Tüm bağımlılıkların, bir kaynağın tüm doğrudan ve dolaylı bağımlılıklarında yinelenir. Örneğin, bir VM için NIC, sanal ağ, ağ güvenlik grupları (NSG 'ler) vb. gösterir.
    - İlk düzey bağımlılıkları göster yalnızca doğrudan bağımlılıkları gösterir. Örneğin, bir VM için sanal ağı değil, NIC 'yi gösterir.


4. Eklemek istediğiniz bağımlı kaynakları seçin > **Bağımlılıklar ekleyin**. Bildirimlerde ilerlemeyi izleyin.

    ![Bağımlılık Ekle](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Bağımlılıkları yeniden doğrulayın. 
    ![Ek bağımlılıklar eklemek için sayfa](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)



## <a name="move-the-source-resource-group"></a>Kaynak kaynak grubunu taşıma 

VM 'Leri hazırlayabilmeniz ve taşıyabilmeniz için önce VM kaynak grubunun hedef bölgede mevcut olması gerekir. 

### <a name="prepare-to-move-the-source-resource-group"></a>Çıkış kaynak grubunu taşımaya hazırlama

Hazırlama işlemi sırasında, kaynak taşıyıcısı kaynak grubu ayarlarını kullanarak Azure Resource Manager (ARM) şablonları üretir. Kaynak grubu içindeki kaynaklar etkilenmez.

Aşağıdaki şekilde hazırlayın:

1. **Bölgeler arasında**, **hazırlama**> kaynak kaynak grubunu seçin.
2. **Kaynakları hazırlama** bölümünde **hazırla**' ya tıklayın.

    ![Kaynak grubunu hazırla](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Kaynak grubu hazırlandıktan sonra, bu *taşıma Işlemi başlatma beklemede* durumunda olur. 

 
### <a name="move-the-source-resource-group"></a>Kaynak kaynak grubunu taşıma

Taşımayı aşağıdaki gibi başlatın:

1. **Bölgeler arası** bölümünde kaynak grubunu seçerek **taşımayı başlatın** >
2. LN **Move kaynakları**, **taşımayı Başlat**' a tıklayın. Kaynak grubu, *devam eden bir taşıma* durumuna geçer.
3. Taşıma başlatıldıktan sonra, hedef kaynak grubu oluşturulan ARM şablonuna göre oluşturulur. Kaynak kaynak grubu, bir *tamamlama taşıma bekleme* durumuna geçer.

    ![Taşımayı Başlat düğmesine tıklayın](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Taşıma işlemini yürütmek ve tamamlamak için:

1. **Bölgeler arasında**, kaynak grubunu > **taşımayı kaydet**' i seçin.
2. LN **Move kaynakları**, **Kaydet**' e tıklayın.

> [!NOTE]
> Taşıma işlemi kaydedildikten sonra kaynak kaynak grubu *bekleyen bir kaynak silme* durumundadır.

## <a name="prepare-resources-to-move"></a>Taşınacak kaynakları hazırlama

Kaynak kaynak grubu taşındığına göre, *hazırlama bekleme* durumundaki diğer kaynakları taşımaya hazırlanabilirsiniz.

1. **Bölgeler arasında**, kaynakların şimdi *hazırlama bekleme* durumunda olduğunu ve sorun olmadan olduğunu doğrulayın. Aksi takdirde, tekrar doğrulayın ve bekleyen sorunları çözün.

    ![Hazırlama Bekleme durumundaki kaynakları gösteren sayfa](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

2. Taşıma işlemine başlamadan önce hedef ayarları düzenlemek istiyorsanız, kaynak için **hedef yapılandırma** sütunundaki bağlantıyı seçin ve ayarları düzenleyin. Hedef VM ayarlarını düzenlerseniz, hedef VM boyutu, kaynak VM boyutundan küçük olmamalıdır.  

Kaynak kaynak grubu taşındığına göre, diğer kaynakları taşımaya hazırlanabilirsiniz.

3. Hazırlamak istediğiniz kaynakları seçin. 

    ![Diğer kaynaklar için hazırla ' yı seçme sayfası](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. **Hazırla**' yı seçin. 

> [!NOTE]
> - Hazırlama işlemi sırasında, Azure Site Recovery Mobility Aracısı, VM 'Lere, onları çoğaltmak için yüklenir.
> - VM verileri, hedef bölgeye düzenli aralıklarla çoğaltılır. Bu, kaynak VM 'yi etkilemez.
> - Kaynak taşıma diğer kaynak kaynakları için ARM şablonları oluşturur.
> - Kaynaklar hazırlandıktan sonra, bir *taşıma beklemede* durumunda olurlar.

![Başlatma taşıma bekleme durumunda kaynakları gösteren sayfa](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Taşımayı Başlat

Kaynaklar hazırlandıktan sonra, şimdi taşımayı başlatabilirsiniz. 

1. **Bölgeler arasında**, durum *başlatma taşıma bekleyen* kaynaklar ' ı seçin. Sonra **taşımayı Başlat**' a tıklayın.
2. **Kaynakları taşıma** bölümünde, **taşımayı Başlat**' a tıklayın.

    ![Taşımayı Başlat düğmesine tıklayın](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Bildirim çubuğunda taşıma ilerlemesini izleyin.

> [!NOTE]
> - VM 'Ler için çoğaltma VM 'Leri hedef bölgede oluşturulur. Kaynak VM kapanmıştır ve bazı kesinti süreleri oluşur (genellikle dakikalar).
> - Kaynak taşıyıcısı, hazırlanan ARM şablonlarını kullanarak diğer kaynakları yeniden oluşturur. Genellikle kapalı kalma süresi yoktur.
> - Kaynakları taşıdıktan sonra, bunlar için bir *taşıma hareketi bekliyor* durumundadır.

![* Kaynağı Sil bekliyor * durumunda kaynakları gösteren sayfa](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


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
> Kaynakları atladıktan sonra, VM 'Ler bir *başlatma taşıma bekliyor* durumundadır.

## <a name="commit-the-move"></a>Taşımayı Yürüt

Taşıma işlemini tamamlamak istiyorsanız, taşımayı yürütün. 

1. **Bölgeler arasında**, durum *işlemede taşıma bekleyen* kaynaklar ' ı seçin ve **taşımayı Yürüt**' e tıklayın.
2. **Kaynakları kaydet**' de, **Yürüt**' e tıklayın.

    ![Taşıma işlemini sonlandırmak için kaynakları yürütmek için sayfa](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Bildirim çubuğunda tamamlama ilerlemesini izleyin.

> [!NOTE]
> - Taşıma işlemi tamamlandıktan sonra VM 'Ler çoğaltmayı durdurur. Kaynak VM, işlemeden etkilenmez.
> - Kayıt, kaynak ağ kaynaklarını etkilemez.
> - Taşıma işlemi tamamlandıktan sonra kaynaklar bir *silme kaynağı bekliyor* durumundadır.

![* Kaynağı Sil bekliyor * durumunda kaynakları gösteren sayfa](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Taşıma işleminden sonra ayarları Yapılandır

- Mobility hizmeti VM 'lerden otomatik olarak kaldırılmaz. El ile kaldırın veya sunucuyu yeniden taşımayı planlıyorsanız bırakın.
- Taşıma işleminden sonra Azure rol tabanlı erişim denetimi (Azure RBAC) kurallarını değiştirme.


## <a name="delete-source-resources-after-commit"></a>İşlemeden sonra kaynak kaynaklarını Sil

Taşıma işleminden sonra, kaynak bölgedeki kaynakları isteğe bağlı olarak silebilirsiniz. 

> [!NOTE]
> Birkaç kaynak (örneğin, Anahtar kasaları ve SQL Server sunucular) portaldan silinemez ve kaynak özellik sayfasından silinmelidir.

1. **Bölgeler arasında**, silmek istediğiniz kaynak kaynağın adına tıklayın.
2. **Kaynağı Sil**' i seçin.

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

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Azure VM 'lerini başka bir Azure bölgesine taşıdı.
> * VM 'lerle ilişkili kaynakları başka bir bölgeye taşıdı.

Şimdi, Azure SQL veritabanlarını ve elastik havuzları başka bir bölgeye taşımaya çalışıyorsunuz.

> [!div class="nextstepaction"]
> [Azure SQL kaynaklarını taşıma](./tutorial-move-region-sql.md)
