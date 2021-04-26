---
title: Azure Kaynak taşıyıcısı 'ni kullanarak bölgeler arasında şifrelenmiş Azure VM 'lerini taşıma
description: Azure Kaynak taşıyıcısı kullanarak şifrelenmiş Azure sanal makinelerini başka bir bölgeye taşımayı öğrenin.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600701"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Eğitim: bölgeler arasında şifrelenmiş Azure VM 'lerini taşıma

Bu makalede, [Azure Kaynak taşıyıcısı](overview.md)kullanılarak şifrelenmiş Azure sanal makinelerini (VM) farklı bir Azure bölgesine nasıl taşıyacağınız açıklanır. 

Şifrelenmiş VM 'ler şu şekilde açıklanabilir:

- Azure disk şifrelemesi etkinleştirilmiş disklere sahip VM 'Ler. Daha fazla bilgi için, bkz. [Azure Portal kullanarak Windows sanal makinesi oluşturma ve şifreleme](../virtual-machines/windows/disk-encryption-portal-quickstart.md).
- Bekleyen şifreleme için müşteri tarafından yönetilen anahtarlar (CMKs) veya sunucu tarafı şifrelemesi kullanan VM 'Ler. Daha fazla bilgi için bkz. [yönetilen diskler için müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi etkinleştirmek için Azure Portal kullanma](../virtual-machines/disks-enable-customer-managed-keys-portal.md).


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları denetleyin. 
> * Azure Disk Şifrelemesi etkin olan VM 'Ler için, anahtar ve gizli dizileri kaynak bölgesi anahtar kasasından hedef bölge anahtar kasasına kopyalayın.
> * VM 'Leri taşımaya ve kaynak bölgede taşımak istediğiniz kaynakları seçmesini hazırlayın.
> * Kaynak bağımlılıklarını çözümleyin.
> * Azure Disk Şifrelemesi etkin olan VM 'Ler için, hedef anahtar kasasını el ile atayın. Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi kullanan VM 'Ler için, hedef bölgede el ile bir disk şifreleme kümesi atayın.
> * Anahtar kasasını veya disk şifreleme kümesini taşıyın.
> * Kaynak kaynak grubunu hazırlayın ve taşıyın. 
> * Diğer kaynakları hazırlayın ve taşıyın.
> * Taşımanın atılıp atılmayacağına karar verin. 
> * İsteğe bağlı olarak, taşıma işleminden sonra kaynak bölgedeki kaynakları kaldırın.

> [!NOTE]
> Bu öğreticide senaryo gerçekleştirmeye yönelik en hızlı yol gösterilmektedir. Yalnızca varsayılan seçenekleri kullanır. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun. Ardından [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="prerequisites"></a>Önkoşullar

Gereksinim |Ayrıntılar
--- | ---
**Abonelik izinleri** | Taşımak istediğiniz kaynakları içeren abonelikte *sahip* erişiminizin bulunduğundan emin olun.<br/><br/> *Neden sahip erişimine ihtiyacım var?* Bir Azure aboneliğinde belirli bir kaynak ve hedef çifti için ilk kez kaynak eklediğinizde, kaynak taşıyıcısı eski adıyla Yönetilen Hizmet Kimliği (MSI) olarak bilinen bir [sistem tarafından atanan yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)oluşturur. Bu kimlik, abonelik tarafından güvenilirdir. Kimliği oluşturabilmeniz ve gerekli rolleri (kaynak abonelikte *katkıda* bulunan ve *Kullanıcı erişimi Yöneticisi* ) atamadan önce, kaynak eklemek Için kullandığınız hesabın abonelikte *sahip* izinleri olması gerekir. Daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD rolleri](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).
**VM desteği** | Taşımak istediğiniz sanal makinelerin aşağıdakileri yaparak desteklendiğinden emin olun:<li>Desteklenen Windows VM 'lerini [doğrulayın](support-matrix-move-region-azure-vm.md#windows-vm-support) .<li>Desteklenen Linux VM 'lerini ve çekirdek sürümlerini [doğrulayın](support-matrix-move-region-azure-vm.md#linux-vm-support) .<li>Desteklenen [işlem](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [depolama](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)ve [ağ](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) ayarlarını denetleyin.
**Anahtar Kasası gereksinimleri (Azure disk şifrelemesi)** | VM 'Ler için Azure disk şifrelemesi etkinleştirilmişse, hem kaynak hem de hedef bölgelerde bir Anahtar Kasası gerekir. Daha fazla bilgi için bkz. [Anahtar Kasası oluşturma](../key-vault/general/quick-create-portal.md).<br/><br/> Kaynak ve hedef bölgelerdeki Anahtar kasaları için şu izinlere ihtiyacınız vardır:<li>Anahtar izinleri: anahtar yönetim Işlemleri (Get, List) ve şifreleme Işlemleri (şifre çözme ve şifreleme)<li>Gizli dizi izinleri: gizli yönetim Işlemleri (Get, List ve set)<li>Sertifika (liste ve Al)
**Disk şifreleme kümesi (CMK ile sunucu tarafı şifreleme)** | Bir CMK kullanan sunucu tarafı şifrelemesi olan VM 'Leri kullanıyorsanız, hem kaynak hem de hedef bölgelerde bir disk şifrelemesi kümesine sahip olmanız gerekir. Daha fazla bilgi için bkz. [disk şifreleme kümesi oluşturma](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Müşteri tarafından yönetilen anahtarlar için donanım güvenlik modülünü (HSM anahtarları) kullanıyorsanız, bölgeler arasında geçiş desteklenmez.
**Hedef bölge kotası** | Aboneliğin, hedef bölgede taşıdığınız kaynakları oluşturmak için yeterli kotası olması gerekir. Kota içermiyorsa [ek sınırlamalar isteyin](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Hedef bölge ücretleri** | VM 'Leri taşıdığınız hedef Bölgeyle ilişkili fiyatlandırmayı ve ücretleri doğrulayın. [Fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/)kullanın.


## <a name="verify-permissions-in-the-key-vault"></a>Anahtar kasasındaki izinleri doğrulama

Azure disk şifrelemesi etkinleştirilmiş VM 'Leri taşıyorsanız, [anahtarları hedef anahtar kasasına Kopyala](#copy-the-keys-to-the-destination-key-vault) bölümünde belirtildiği gibi bir betiği çalıştırmanız gerekir. Betiği çalıştıran kullanıcıların bunu yapmak için uygun izinlere sahip olması gerekir. Hangi izinlerin gerekli olduğunu anlamak için aşağıdaki tabloya bakın. Azure portal, izinleri değiştirme seçeneklerini, anahtar kasasına giderek bulacaksınız. **Ayarlar** altında **erişim ilkeleri**' ni seçin.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Anahtar Kasası ayarları bölmesindeki ' erişim ilkeleri ' bağlantısının ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Kullanıcı izinleri yerinde değilse, **erişim Ilkesi Ekle**' yi seçin ve ardından izinleri belirtin. Kullanıcı hesabının zaten bir ilkesi varsa, **Kullanıcı** altında, izinleri aşağıdaki tablodaki yönergelere göre ayarlayın.

Azure disk şifrelemesi kullanan Azure VM 'Leri aşağıdaki çeşitlere sahip olabilir ve izinleri ilgili bileşenlerine göre ayarlamanız gerekir. VM 'Ler şunları içerebilir:
- Diskin yalnızca gizli dizilerle şifrelendiği varsayılan seçenektir.
- [Anahtar şifreleme anahtarı (kek)](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)kullanan güvenlik eklendi.

### <a name="source-region-key-vault"></a>Kaynak bölgesi Anahtar Kasası

Betiği çalıştıran kullanıcılar için aşağıdaki bileşenler için izinleri ayarlayın: 

Bileşen | Gereken izinler
--- | ---
Gizli Diziler |  *Al* <br></br> Gizli **izinler**  >  **gizli yönetim işlemlerini** seçin ve ardından **Al**' ı seçin. 
Anahtarlar <br></br> Bir KEK kullanıyorsanız, gizli dizi izinlerinin yanı sıra bu izinlere de ihtiyacınız vardır. | *Al* ve *Çöz* <br></br> **Anahtar izinleri**  >  **anahtar yönetim işlemleri**' ni seçin ve ardından **Al**' ı seçin. **Şifreleme işlemleri**' nde **şifre çöz**' ü seçin.

### <a name="destination-region-key-vault"></a>Hedef bölge Anahtar Kasası

**Erişim ilkeleri**' nde, **birim şifrelemesi Için Azure disk şifrelemesi** ' nin etkinleştirildiğinden emin olun. 

Betiği çalıştıran kullanıcılar için aşağıdaki bileşenler için izinleri ayarlayın: 

Bileşen | Gereken izinler
--- | ---
Gizli Diziler |  *Ayarla* <br></br> Gizli **izinler**  >  **gizli yönetim işlemlerini** seçin ve ardından **Ayarla**' yı seçin. 
Anahtarlar <br></br> Bir KEK kullanıyorsanız, gizli dizi izinlerinin yanı sıra bu izinlere de ihtiyacınız vardır. | *Edinme*, *oluşturma* ve *şifreleme* <br></br> **Anahtar izinleri**  >  **anahtar yönetim işlemleri**' ni seçin ve ardından **Al** ve **Oluştur** ' u seçin. **Şifreleme işlemleri**' nde, **şifreleyin**' ı seçin.

<br>

Hedef anahtar kasasındaki önceki izinlere ek olarak, kaynak taşıyıcısı tarafından sizin adınıza Azure kaynaklarına erişmek için kullanılan [yönetilen sistem kimliği](./common-questions.md#how-is-managed-identity-used-in-resource-mover) için izinler eklemeniz gerekir. 

1. **Ayarlar** altında **erişim ilkeleri Ekle**' yi seçin. 
1. **Asıl seçin** bölümünde, MSI 'yi arayın. MSI adı ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
1. MSI için aşağıdaki izinleri ekleyin:

    Bileşen | Gereken izinler
    --- | ---
    Gizli Diziler|  *Al* ve *Listele* <br></br> Gizli **izinler**  >  **gizli yönetim işlemleri**' ni seçin ve ardından **Al** ve **Listele**' yi seçin. 
    Anahtarlar <br></br> Bir KEK kullanıyorsanız, gizli dizi izinlerinin yanı sıra bu izinlere de ihtiyacınız vardır. | *Al* ve *Listele* <br></br> **Anahtar izinleri**  >  **anahtar yönetim işlemleri**' ni seçin ve ardından **Al** ve **Listele**' yi seçin.

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>Anahtarları hedef anahtar kasasına Kopyala

Sağladığınız bir [betiği](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) kullanarak kaynak anahtar kasasından şifreleme gizli dizilerini ve anahtarlarını hedef anahtar kasasına kopyalayın.

- Betiği PowerShell 'de çalıştırın. En son PowerShell sürümünü kullanmanızı öneririz.
- Özel olarak, komut dosyası bu modülleri gerektirir:
    - Az.Compute
    - Az. Keykasası (sürüm 3.0.0)
    - Az. Accounts (sürüm 2.2.3)

Betiği çalıştırmak için şunları yapın:

1. [Betiği](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) GitHub 'da açın.
1. Betiğin içeriğini yerel bir dosyaya kopyalayın ve *Copy-keys.ps1* olarak adlandırın.
1. Betiği çalıştırın.
1. Azure portalında oturum açın.
1. **Kullanıcı girişleri** penceresindeki aşağı açılan listelerde, kaynak aboneliği, kaynak grubu ve kaynak VM ' yi seçin ve ardından hedef konumu ve disk ve anahtar şifrelemesi için hedef kasalarını seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Komut dosyası değerlerini girmeye yönelik ' Kullanıcı girdileri ' penceresinin ekran görüntüsü." :::

1. **Seç** düğmesini belirleyin. 
   
   Betik çalışmayı bitirdiğinde, bir ileti, CopyKeys 'in başarılı olduğunu bildirir.

## <a name="prepare-vms"></a>VM 'Leri hazırlama

1. VM 'Lerin [önkoşulları](#prerequisites)karşıladıktan emin olduktan sonra, taşımak Istediğiniz sanal makinelerin açık olduğundan emin olun. Hedef bölgede kullanılabilir olmasını istediğiniz tüm VM disklerinin VM 'de eklenmiş ve başlatılmış olması gerekir.
1. VM 'Lerin en son Güvenilen Kök sertifikalara ve güncelleştirilmiş bir sertifika iptal listesine (CRL) sahip olduğundan emin olmak için aşağıdakileri yapın:
    - Windows VM 'lerinde en son Windows güncelleştirmelerini yükler.
    - Linux VM 'lerde, makinelerin en son sertifikalara ve CRL 'ye sahip olması için dağıtım kılavuzunu izleyin. 
1. VM 'lerden giden bağlantılara izin vermek için aşağıdakilerden birini yapın:
    - Giden bağlantıyı denetlemek için URL tabanlı bir güvenlik duvarı proxy 'si kullanıyorsanız, URL 'lere [erişime izin verin](support-matrix-move-region-azure-vm.md#url-access).
    - Giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsanız, bu [hizmet etiketi kurallarını](support-matrix-move-region-azure-vm.md#nsg-rules)oluşturun.

## <a name="select-the-resources-to-move"></a>Taşınacak kaynakları seçin

- Seçtiğiniz kaynak bölgedeki herhangi bir kaynak grubunda desteklenen herhangi bir kaynak türünü seçebilirsiniz.  
- Kaynakları kaynak bölgeyle aynı abonelikte bulunan bir hedef bölgeye taşıyabilirsiniz. Aboneliği değiştirmek istiyorsanız, kaynaklar taşındıktan sonra bunu yapabilirsiniz.

Kaynakları seçmek için aşağıdakileri yapın:

1. Azure portal, **kaynak taşıyıcısı** için arama yapın. Ardından, **Hizmetler**' ın altında **Azure Kaynak taşıyıcısı**' ı seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Azure portal Azure Kaynak taşıyıcısı için arama sonuçlarının ekran görüntüsü." :::

1. Azure Kaynak taşıyıcısı **genel bakış** bölmesinde **bölgeler arasında taşı**' yı seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Başka bir bölgeye taşınacak kaynakları eklemek için ' bölgeler arasında taşı ' düğmesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. **Kaynakları taşı** bölmesinde, **kaynak + hedef** sekmesini seçin. Ardından, açılan listelerde kaynak aboneliğini ve bölgeyi seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Kaynak ve hedef bölge seçme sayfası.." :::

1. **Hedef** altında, VM 'leri taşımak istediğiniz bölgeyi seçin ve ardından **İleri**' yi seçin.

1. **Taşınacak kaynakları** seçin ve ardından **kaynakları seç**' i seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="' Kaynakları taşı ' bölmesinin ve ' Kaynak Seç ' düğmesinin ekran görüntüsü.]." :::

1. **Kaynakları seçin** bölmesinde, taşımak Istediğiniz VM 'leri seçin. [Taşınacak kaynakları seçin](#select-the-resources-to-move) bölümünde belirtildiği gibi, yalnızca bir taşıma için desteklenen kaynakları ekleyebilirsiniz.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Taşınacak VM 'Leri seçmek için ' Kaynak Seç ' bölmesinin ekran görüntüsü." :::

    > [!NOTE]
    >  Bu öğreticide, müşteri tarafından yönetilen bir anahtarla sunucu tarafı şifreleme (Rayne-VM) ve disk şifrelemesi etkinleştirilmiş bir VM (Rayne-VM-ade) kullanan bir VM seçiyoruz.

1. **Bitti** seçeneğini belirleyin.
1. **Taşınacak kaynakları** seçin ve ardından **İleri**' yi seçin.
1. **İnceleme** sekmesini seçin ve kaynak ve hedef ayarlarını kontrol edin. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Kaynak ve hedef ayarlarını gözden geçirme bölmesinin ekran görüntüsü." :::

1. Kaynakları eklemeye başlamak için **devam** ' ı seçin.
1. İlerlemeyi izlemek için Bildirimler simgesini seçin. İşlem başarıyla tamamlandıktan sonra, **Bildirimler** bölmesinde, **taşıma için eklenen kaynaklar**' ı seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Kaynakların başarıyla eklendiğini onaylayan ' bildirimler ' bölmesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. Bildirimi seçtikten sonra **bölgeler arası** sayfasında kaynakları gözden geçirin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="' Hazırlama bekleniyor ' durumuna sahip eklenen kaynakların ekran görüntüsü." :::

> [!NOTE]
> - Eklediğiniz kaynaklar *hazırlama bekleme* durumuna konur.
> - VM 'Ler için kaynak grubu otomatik olarak eklenir.
> - Hedef **yapılandırma** girdilerini hedef bölgede zaten mevcut olan bir kaynağı kullanacak şekilde değiştirirseniz, bir taşıma başlatmanız gerekmiyorsa kaynak durumu, *bekleyen* olarak ayarlanır.
> - Eklenen bir kaynağı kaldırmak istiyorsanız, kullanacağınız yöntem taşıma sürecinde nerede olduğunuza bağlıdır. Daha fazla bilgi için bkz. [taşıma koleksiyonlarını ve kaynak gruplarını yönetme](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Bağımlılıkları çözümle

1. Herhangi bir kaynak **sorunlar** sütununda *bağımlılıkları doğrula* iletisini gösterince, **bağımlılıkları doğrula** düğmesini seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="' Bağımlılıkları Doğrula ' düğmesini gösteren ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Doğrulama işlemi başlar.
1. Bağımlılıklar bulunursa, **Bağımlılıklar Ekle**' yi seçin.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="' Bağımlılık Ekle ' düğmesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. **Bağımlılıklar Ekle** bölmesinde, varsayılan **tüm bağımlılıkları göster** seçeneğini bırakın.

    - **Tüm bağımlılıkların** , bir kaynağın tüm doğrudan ve dolaylı bağımlılıklarında yinelenir. Örneğin, bir VM için NIC, sanal ağ, ağ güvenlik grupları (NSG 'ler) vb. gösterir.
    - **Birinci düzey bağımlılıkları göster** yalnızca doğrudan bağımlılıkları gösterir. Örneğin, bir VM için sanal ağı değil, NIC 'yi gösterir.
 
1. Eklemek istediğiniz bağımlı kaynakları seçin ve ardından **Bağımlılıklar Ekle**' yi seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Bağımlılıklar listesinin ve ' bağımlılıkları Ekle ' düğmesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Bağımlılıkları yeniden doğrulayın. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Bağımlılıkların yeniden doğrulanması için bölmenin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Hedef kaynakları ata

Şifrelemeyle ilişkili hedef kaynakları el ile atamanız gerekir.

- Azure disk şifrelemesi etkinleştirilmiş bir VM 'yi taşıyorsanız, hedef bölgesindeki Anahtar Kasası bir bağımlılık olarak görünür.
- Bir VM 'yi CMKs kullanan sunucu tarafı şifrelemesiyle taşıyorsanız, hedef bölgede ayarlanan disk şifrelemesi bir bağımlılık olarak görünür. 
- Bu öğretici, Azure Disk Şifrelemesi etkin olan ve CMK kullanan bir VM 'yi taşımayı gösterdiği için hem hedef Anahtar Kasası hem de disk şifreleme kümesi bağımlılıklar olarak görünür.

Hedef kaynakları el ile atamak için aşağıdakileri yapın:

1. Disk şifreleme kümesi girdisinde, **hedef yapılandırma** sütununda **kaynak atanmamış** ' ı seçin.
1. **Yapılandırma ayarları**' nda hedef disk şifreleme kümesini seçin ve ardından **Değişiklikleri Kaydet**' i seçin.
1. Değiştirdiğiniz Kaynağın bağımlılıklarını kaydedebilir ve doğrulayabilir veya yalnızca değişiklikleri kaydedebilir ve ardından aynı anda değiştirdiğiniz her şeyi doğrulayabilirsiniz.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Hedef bölgedeki değişiklikleri kaydetmek için ' hedef yapılandırma ' bölmesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Hedef kaynağı ekledikten sonra, disk şifreleme kümesinin durumu *taşıma bekleniyor* olarak değiştirilir.

1. Anahtar Kasası girişinde, **hedef yapılandırma** sütununda **atanan kaynak** ' ı seçin. **Yapılandırma ayarları** altında hedef anahtar kasasını seçin ve ardından değişikliklerinizi kaydedin. 

Bu aşamada, disk şifreleme kümesi ve Anahtar Kasası durumları *taşıma bekleniyor* olarak değiştirilmiştir.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Diğer kaynakları hazırlama bölmesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Şifreleme kaynakları için taşıma işlemini yürütmek ve tamamlamak için aşağıdakileri yapın:

1. **Bölgeler arasında**, kaynağı (disk şifreleme kümesi veya Anahtar Kasası) seçin ve ardından **taşımayı Yürüt**' ü seçin.
1. **Kaynakları taşıma** bölümünde, **Yürüt**' ü seçin.

> [!NOTE]
> Taşıma işlemi gerçekleştirildikten sonra kaynak durumu, *bekleyen kaynağı Sil* olarak değişir.


## <a name="move-the-source-resource-group"></a>Kaynak kaynak grubunu taşıma 

VM 'Leri hazırlayabilmeniz ve taşıyabilmeniz için önce VM kaynak grubunun hedef bölgede mevcut olması gerekir. 

### <a name="prepare-to-move-the-source-resource-group"></a>Çıkış kaynak grubunu taşımaya hazırlama

Hazırlama işlemi sırasında kaynak taşıyıcısı, kaynak grubu ayarlarından Azure Resource Manager (ARM) şablonları üretir. Kaynak grubu içindeki kaynaklar etkilenmemiştir.

Kaynak kaynak grubunu taşımaya hazırlanmak için aşağıdakileri yapın:

1. **Bölgeler arasında**, kaynak kaynak grubunu seçin ve ardından **hazırla**' yı seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="' Kaynakları hazırlama ' bölmesindeki ' hazırla ' düğmesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. **Kaynakları hazırlama** bölümünde **hazırla**' yı seçin.

> [!NOTE]
> Taşıma Işlemini hazırladıktan sonra, kaynak grubu durumu *taşımayı başlatmak* için değiştirilir. 

 
### <a name="move-the-source-resource-group"></a>Kaynak kaynak grubunu taşıma

Aşağıdaki işlemleri gerçekleştirerek kaynak kaynak grubunu taşımaya başlayın:

1. **Bölgeler arası** bölmesinde, kaynak grubunu seçin ve ardından **taşımayı Başlat**' ı seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="' Bölgeler arası ' bölmesindeki ' taşımayı Başlat ' düğmesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. **Kaynakları taşı** bölmesinde **taşımayı Başlat**' ı seçin. *Devam eden taşıma Işlemini başlatmak* için kaynak grubu durumu değişir.   
1. Taşıma işlemini başlattıktan sonra, hedef kaynak grubu oluşturulan ARM şablonuna göre oluşturulur. Kaynak kaynak grubu durumu, *taşıma hareketi bekliyor* olarak değişir.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Kaynak grubu durumunu gösteren &quot;kaynakları taşı&quot; bölmesinin ekran görüntüsü, ' taşıma bekletiliyor bekleniyor ' olarak değiştirildi." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Taşımayı tamamlamak ve işlemi tamamlamak için aşağıdakileri yapın:

1. **Bölgeler arası** bölmesinde, kaynak grubunu seçin ve ardından **taşımayı Yürüt**' ü seçin.
1. **Kaynakları taşı** bölmesinde, **Yürüt**' ü seçin.

> [!NOTE]
> Taşıma işlemi gerçekleştirildikten sonra kaynak kaynak grubu durumu *bekleyen kaynağı Sil* olarak değişir.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="' Kaynağı Sil bekliyor ' olarak değiştirilen durumu gösteren kaynak kaynak grubunun ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Taşınacak kaynakları hazırlama

Artık şifreleme kaynakları ve kaynak kaynak grubu taşındıktan sonra, geçerli durumu *hazırlama beklemede* olan diğer kaynakları taşımaya hazırlanabilirsiniz.


1. **Bölgeler arası** bölmesinde, taşımayı yeniden doğrulayıp sorunları çözün.
1. Taşıma işlemine başlamadan önce hedef ayarları düzenlemek istiyorsanız, kaynak için **hedef yapılandırma** sütunundaki bağlantıyı seçin ve ardından ayarları düzenleyin. Hedef VM ayarlarını düzenlerseniz, hedef VM boyutu, kaynak VM boyutundan küçük olmamalıdır.
1. Taşımak istediğiniz *hazırlık bekleme* durumuna sahip kaynaklar için **hazırla**' yı seçin.
1. **Kaynakları hazırla** bölmesinde, **hazırla**' yı seçin.

    - Hazırlık sırasında, Azure Site Recovery Mobility Aracısı onları çoğaltmak için VM 'Lere yüklenir.
    - VM verileri, hedef bölgeye düzenli aralıklarla çoğaltılır. Bu, kaynak VM 'yi etkilemez.
    - Kaynak taşıma diğer kaynak kaynakları için ARM şablonları oluşturur.

> [!NOTE]
> Kaynakları hazırladıktan sonra, durum *taşıma Işlemini başlatmak* için durumları değişir.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="' Kaynak hazırlama ' bölmesindeki kaynakları gösteren ' kaynakları hazırla ' durumunun ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Taşımayı Başlat

Hazırlanan kaynakları hazırladığınıza göre, taşımayı başlatabilirsiniz. 

1. **Bölgeler arası** bölmesinde, durumu *taşıma beklemede* olan kaynakları seçin ve sonra **taşımayı Başlat**' ı seçin.
1. **Kaynakları taşı** bölmesinde **taşımayı Başlat**' ı seçin.
1. Bildirimler çubuğunda taşımanın ilerlemesini izleyin.

    - VM 'Ler için çoğaltma VM 'Leri hedef bölgede oluşturulur. Kaynak VM kapanmıştır ve bazı kesinti süreleri oluşur (genellikle dakikalar).
    - Kaynak taşıyıcısı hazırlanan ARM şablonlarını kullanarak diğer kaynakları yeniden oluşturur. Genellikle kapalı kalma süresi yoktur.
    - Kaynakları taşıdıktan sonra, bunların durum değişiklikleri *işlemeye bekliyor* olarak değişir.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="' Taşıma taşıma bekleniyor ' durumuna sahip kaynak listesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Atın veya teslim edilsin mi?

İlk taşıdıktan sonra, taşımayı mi yoksa atmayı mi yapacağınıza karar verebilirsiniz. 

- **At**: testi çalıştırıyorsanız ve kaynak kaynağı gerçekten taşımak istemiyorsanız bir taşımayı atabilirsiniz. Taşımayı atmak, *taşıma bekleyen durumunu başlatmak* için kaynağı döndürür.
- **COMMIT**: COMMIT, hedef bölgeye taşıma işlemini tamamlar. Kaynak kaynağı kaydettikten sonra, durumu, *bekleyen kaynağı Sil* olarak değişir ve silmek isteyip istemediğinize karar verebilirsiniz.


## <a name="discard-the-move"></a>Taşımayı at 

Taşımayı atmak için aşağıdakileri yapın:

1. **Bölgeler arası** bölmesinde, durumu *tamamlama taşıma beklemede* olan kaynaklar ' ı seçin ve ardından **taşımayı at**' ı seçin.
1. **Taşımayı at** bölmesinde, **at**' ı seçin.
1. Bildirimler çubuğunda taşımanın ilerlemesini izleyin.


> [!NOTE]
> Kaynakları iptal ettikten sonra, sanal makine durumları *taşımayı başlatmak* üzere değişir.

## <a name="commit-the-move"></a>Taşımayı Yürüt

Taşıma işlemini tamamlamak için, aşağıdakileri yaparak taşımayı yürütün: 

1. **Bölgeler arası** bölmesinde, durumu *tamamlama taşıma beklemede* olan kaynaklar ' ı seçin ve ardından **taşımayı Yürüt**' ü seçin.
1. **Kaynakları kaydet** bölmesinde, **Yürüt**' ü seçin.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Taşıma işlemini sonlandırmak için kaynakları yürütmek üzere kaynak listesinin ekran görüntüsü." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Bildirim çubuğunda tamamlama ilerlemesini izleyin.

> [!NOTE]
> - Taşıma işleminden sonra VM 'Ler çoğaltmayı durdurur. Kaynak VM, işlemeden etkilenmemiştir.
> - İşleme işlemi kaynak ağ kaynaklarını etkilemez.
> - Taşıma işlemi gerçekleştirildikten sonra kaynak durumları *bekleyen kaynağı Sil* olarak değişir.



## <a name="configure-settings-after-the-move"></a>Taşıma işleminden sonra ayarları Yapılandır

- Mobility hizmeti VM 'lerden otomatik olarak kaldırılmaz. El ile kaldırın veya sunucuyu yeniden taşımayı planlıyorsanız bırakın.
- Taşıma işleminden sonra Azure rol tabanlı erişim denetimi (RBAC) kurallarını değiştirme.

## <a name="delete-source-resources-after-commit"></a>İşlemeden sonra kaynak kaynaklarını Sil

Taşıma işleminden sonra, kaynak bölgedeki kaynakları isteğe bağlı olarak silebilirsiniz. 

1. **Bölgeler arası** bölmesinde, silmek istediğiniz her kaynak kaynağı seçin ve ardından **kaynağı Sil**' i seçin.
1. **Kaynağı Sil**' de, ne silmek istediğinizi gözden geçirin ve **Silmeyi Onayla**' da **Evet** yazın. Eylem geri alınamaz, bu nedenle dikkatli olun!
1. **Evet** yazdıktan sonra **kaynağı Sil**' i seçin.

> [!NOTE]
>  Kaynak taşıma portalında kaynak grupları, Anahtar kasaları veya SQL Server örnekleri silemezsiniz. Her bir kaynağın Özellikler sayfasından her birini ayrı ayrı silmeniz gerekir.


## <a name="delete-resources-that-you-created-for-the-move"></a>Taşıma için oluşturduğunuz kaynakları silme

Taşıma işleminden sonra, taşıma koleksiyonunu ve bu işlem sırasında oluşturduğunuz kaynakları Site Recovery el ile silebilirsiniz.

- Taşıma koleksiyonu varsayılan olarak gizlidir. Bunu görmek için gizli kaynakları açmanız gerekir.
- Önbellek depolamada silinmeden önce, silinmesi gereken bir kilit vardır.

Kaynaklarınızı silmek için aşağıdakileri yapın: 
1. Kaynak grubundaki kaynakları bulun ```RegionMoveRG-<sourceregion>-<target-region>``` .
1. Kaynak bölgedeki tüm VM 'Lerin ve kaynak kaynakların taşındığından veya silindiğinden emin olmak için denetleyin. Bu adım, bunları kullanan beklemedeki kaynaklar olmamasını sağlar.
1. Kaynakları silin:

    - Koleksiyon adını taşı: ```movecollection-<sourceregion>-<target-region>```
    - Önbellek depolama hesabı adı: ```resmovecache<guid>```
    - Kasa adı: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Şifrelenmiş Azure VM 'Leri ve bağımlı kaynaklarını başka bir Azure bölgesine taşıdı.


Sonraki adım olarak, Azure SQL veritabanlarını ve elastik havuzları başka bir bölgeye taşımayı deneyin.

> [!div class="nextstepaction"]
> [Azure SQL kaynaklarını taşıma](./tutorial-move-region-sql.md)
