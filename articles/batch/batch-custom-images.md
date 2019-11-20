---
title: Yönetilen görüntüden özel bir havuz Sağlama-Azure Batch | Microsoft Docs
description: Uygulamanıza yönelik yazılım ve verilerle işlem düğümleri sağlamak için yönetilen bir görüntü kaynağından bir Batch havuzu oluşturun.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: lahugh
ms.openlocfilehash: 82a5f79ca7b7a16cd8f7294ebd1f70816b40ad82
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "73519243"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Bir sanal makine havuzu oluşturmak için yönetilen bir görüntü kullanma

Batch havuzunuzun sanal makineleri (VM 'Ler) için özel bir görüntü oluşturmak üzere [paylaşılan görüntü galerisini](batch-sig-images.md)veya *yönetilen bir görüntü* kaynağını kullanabilirsiniz.

> [!TIP]
> Çoğu durumda, paylaşılan görüntü Galerisi 'Ni kullanarak özel görüntüler oluşturmanız gerekir. Paylaşılan görüntü Galerisi 'ni kullanarak, havuzları daha hızlı sağlayabilir, daha büyük miktarlarda VM 'Ler ölçeklendirebilir ve VM 'Ler sağlanırken güvenilirliği daha iyi bir şekilde sağlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [paylaşılan görüntü galerisini kullanarak özel bir havuz oluşturma](batch-sig-images.md).

## <a name="prerequisites"></a>Önkoşullar

- **Yönetilen bir görüntü kaynağı**. Özel bir görüntü kullanarak bir sanal makine havuzu oluşturmak için, Batch hesabıyla aynı Azure aboneliği ve bölgesinde bir yönetilen görüntü kaynağı oluşturmanız veya oluşturmanız gerekir. Görüntü, sanal makinenin işletim sistemi diskinin anlık görüntülerinden ve isteğe bağlı olarak bağlı veri diskine oluşturulmalıdır. Yönetilen bir görüntüyü hazırlama hakkında daha fazla bilgi ve adım için aşağıdaki bölüme bakın.
  - Oluşturduğunuz her havuz için benzersiz bir özel görüntü kullanın.
  - Batch API 'Lerini kullanarak görüntüyle bir havuz oluşturmak için, `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`formda olan görüntünün **kaynak kimliğini** belirtin. Portalı kullanmak için görüntünün **adını** kullanın.  
  - Yönetilen görüntü kaynağı, havuzun kullanım ömrü için mevcut olmalıdır ve havuz silindikten sonra kaldırılabilirler.

- **Azure Active Directory (AAD) kimlik doğrulaması**. Batch istemci API 'sinin AAD kimlik doğrulamasını kullanması gerekir. AAD için Azure Batch desteği [Active Directory Batch hizmeti çözümlerinin kimlik doğrulaması konusunda](batch-aad-auth.md)belgelenmiştir.

## <a name="prepare-a-custom-image"></a>Özel görüntü hazırlama

Azure 'da, yönetilen bir görüntüyü şuradan hazırlayabilirsiniz:

- Azure VM 'nin işletim sistemi ve veri disklerinin anlık görüntüleri
- Yönetilen disklere sahip genelleştirilmiş bir Azure VM
- Buluta yüklenen genelleştirilmiş bir şirket içi VHD

Toplu Iş havuzlarını özel bir görüntüyle güvenilir bir şekilde ölçeklendirmek için, *yalnızca* ilk yöntemi kullanarak yönetilen bir görüntü oluşturmanız ÖNERILIR: VM disklerinin anlık görüntülerini kullanma. Bir VM hazırlamak, anlık görüntü almak ve anlık görüntüden görüntü oluşturmak için aşağıdaki adımlara bakın.

### <a name="prepare-a-vm"></a>VM hazırlama

Görüntü için yeni bir VM oluşturuyorsanız, yönetilen görüntünüz için temel görüntü olarak Batch tarafından desteklenen ilk taraf Azure Marketi görüntüsünü kullanın. Yalnızca ilk taraf görüntüleri temel görüntü olarak kullanılabilir. Azure Batch tarafından desteklenen Azure Market görüntüsü başvurularının tam listesini almak için bkz. [düğüm Aracısı SKU 'Larını Listele](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) işlemi.

> [!NOTE]
> Temel görüntünüz için ek lisans ve satın alma koşullarına sahip bir üçüncü taraf görüntüsü kullanamazsınız. Bu market görüntüleri hakkında daha fazla bilgi için bkz. [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) veya [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM 'leri Kılavuzu.

- VM 'nin yönetilen bir disk ile oluşturulduğundan emin olun. Bu, bir VM oluşturduğunuzda varsayılan depolama ayarıdır.
- Özel Betik uzantısı gibi Azure uzantılarını VM 'ye yüklemeyin. Görüntü önceden yüklenmiş bir uzantı içeriyorsa, Azure Batch havuzunu dağıttığınızda sorunlarla karşılaşabilir.
- Bağlı veri diskleri kullanılırken, bunları kullanmak için diskleri bir VM içinden bağlamanız ve biçimlendirmeniz gerekir.
- Sağladığınız temel işletim sistemi görüntüsünün varsayılan geçici sürücüyü kullandığından emin olun. Batch düğüm Aracısı Şu anda varsayılan geçici sürücüyü bekliyor.
- VM çalışmaya başladıktan sonra RDP (Windows için) veya SSH (Linux için) aracılığıyla buna bağlanın. Gerekli yazılımları yükler veya istenen verileri kopyalayın.  

### <a name="create-a-vm-snapshot"></a>VM anlık görüntüsü oluşturma

Anlık görüntü, bir VHD 'nin tam ve salt okunurdur kopyasıdır. Bir sanal makinenin işletim sistemi veya veri disklerinin anlık görüntüsünü oluşturmak için Azure portal veya komut satırı araçlarını kullanabilirsiniz. Anlık görüntü oluşturma adımları ve seçenekleri için bkz. [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) veya [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 'leri Kılavuzu.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Bir veya daha fazla anlık görüntüden görüntü oluşturma

Bir anlık görüntüden yönetilen bir görüntü oluşturmak için [az Image Create](/cli/azure/image) komutu gibi Azure komut satırı araçlarını kullanın. Bir işletim sistemi disk anlık görüntüsü ve isteğe bağlı olarak bir veya daha fazla veri diski anlık görüntüsü belirterek görüntü oluşturabilirsiniz.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Portalda özel görüntüden havuz oluşturma

Özel görüntünüzü kaydettikten ve kaynak KIMLIĞINI veya adını öğrendikten sonra, bu görüntüden bir Batch havuzu oluşturun. Aşağıdaki adımlarda Azure portal havuzun nasıl oluşturulacağı gösterilmektedir.

> [!NOTE]
> Havuzu Batch API 'Lerinden birini kullanarak oluşturuyorsanız, AAD kimlik doğrulaması için kullandığınız kimliğin görüntü kaynağı için izinlere sahip olduğundan emin olun. Bkz. [Active Directory Batch hizmeti çözümlerini kimlik doğrulama](batch-aad-auth.md).
>
> Yönetilen görüntünün kaynağı havuzun ömrü için mevcut olmalıdır. Temeldeki kaynak silinirse, havuz ölçeklenmeyebilir.

1. Azure portalında Batch hesabınıza gidin. Bu hesabın, özel görüntüyü içeren kaynak grubuyla aynı abonelikte ve bölgede olması gerekir.
2. Soldaki **Ayarlar** penceresinde **havuzlar** menü öğesini seçin.
3. **Havuzlar** penceresinde **Ekle** komutunu seçin.
4. **Havuz Ekle** penceresinde, **görüntü türü** açılan menüsünden **özel görüntü (Linux/Windows)** öğesini seçin. **Özel VM görüntüsü** açılan menüsünde, görüntü adını (kaynak kimliği için kısa biçim) seçin.
5. Özel görüntünüz için doğru **yayımcıyı/teklifi/SKU 'yu** seçin.
6. **Düğüm boyutu**, **hedef adanmış düğümler**ve **düşük öncelikli düğümlerin**yanı sıra istediğiniz isteğe bağlı ayarları da kapsayan, kalan gerekli ayarları belirtin.

    Örneğin, Microsoft Windows Server Datacenter 2016 özel görüntüsü için, **Havuz Ekle** penceresi aşağıda gösterildiği gibi görünür:

    ![Özel Windows görüntüsünden havuz ekleme](media/batch-custom-images/add-pool-custom-image.png)
  
Mevcut bir havuzun özel bir görüntüye dayalı olup olmadığını denetlemek için **Havuz** penceresinin kaynak Özeti bölümünde **işletim sistemi** özelliğine bakın. Havuz özel bir görüntüden oluşturulduysa, **özel VM görüntüsü**olarak ayarlanır.

Bir havuz ile ilişkili tüm özel görüntüler havuzun **Özellikler** penceresinde görüntülenir.

## <a name="considerations-for-large-pools"></a>Büyük havuzlara dikkat edilecek noktalar

Özel bir görüntü kullanarak yüzlerce VM veya daha fazlasını içeren bir havuz oluşturmayı planlıyorsanız, bir VM anlık görüntüsünden oluşturulmuş bir görüntüyü kullanmak için yukarıdaki yönergeleri izlemeniz önemlidir.

Ayrıca aşağıdaki noktalara dikkat edin:

- **Boyut limitleri** -özel bir görüntü kullandığınızda toplu işlem havuz boyutunu 2500 adanmış işlem düğümlerine veya 1000 düşük öncelikli düğümlere sınırlar.

  Birden çok havuz oluşturmak için aynı görüntüyü (veya aynı temel anlık görüntüye dayalı birden çok görüntüyü) kullanırsanız, havuzlardaki toplam işlem düğümleri önceki limitleri aşamaz. Tek bir havuzun daha fazla olması için bir görüntünün veya temel anlık görüntüsünün kullanılması önerilmez.

  Havuzu [gelen NAT havuzlarıyla](pool-endpoint-configuration.md)yapılandırırsanız sınırlar azaltılabilir.

- **Yeniden boyutlandırma zaman aşımı** -havuzunuz sabit sayıda düğüm içeriyorsa (otomatik ölçeklendirme yapmaz), havuzun resizeTimeout özelliğini 20-30 dakika gibi bir değere yükseltin. Havuzunuz, zaman aşımı süresi içinde hedef boyutuna ulaşmazsa, başka bir [yeniden boyutlandırma işlemi](/rest/api/batchservice/pool/resize)gerçekleştirin.

  300 ' den fazla işlem düğümüne sahip bir havuz planlıyorsanız, hedef boyutuna ulaşmak için havuzu birden çok kez yeniden boyutlandırmanız gerekebilir.
  
[Paylaşılan görüntü galerisini](batch-sig-images.md)kullanarak, daha fazla paylaşılan görüntü çoğaltmalarıyla birlikte özelleştirilmiş görüntülerinize sahip daha büyük havuzlar da oluşturabilirsiniz. Paylaşılan görüntüleri kullanarak havuzun kararlı duruma ulaşması için gereken süre %25 daha hızlı ve VM boşta kalma gecikmesi %30 ' a kadar daha kısadır.

## <a name="considerations-for-using-packer"></a>Packer kullanma konuları

Doğrudan Packer ile yönetilen bir görüntü kaynağı oluşturmak yalnızca kullanıcı aboneliği modu Batch hesaplarıyla yapılabilir. Batch hizmeti modu hesaplarında, önce bir VHD oluşturmanız ve ardından VHD 'yi yönetilen bir görüntü kaynağına aktarmanız gerekir. Havuz ayırma moduna (Kullanıcı aboneliği veya Batch hizmeti) bağlı olarak, yönetilen bir görüntü kaynağı oluşturma adımlarınız farklılık gösterecektir.

Yönetilen görüntüyü oluşturmak için kullanılan kaynağın, Özel görüntüye başvuran herhangi bir havuzun yaşam süreleri için mevcut olduğundan emin olun. Bunun yapılmaması, havuz ayırma hatalarına ve/veya yeniden boyutlandırmaya neden olabilir.

Görüntü veya temel alınan kaynak kaldırılırsa şuna benzer bir hata alabilirsiniz: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Bu hatayı alırsanız, temeldeki kaynağın kaldırılmadığından emin olun.

Sanal makine oluşturmak için Packer kullanma hakkında daha fazla bilgi için bkz. [Packer Ile Linux görüntüsü oluşturma](../virtual-machines/linux/build-image-with-packer.md) veya [Packer Ile Windows görüntüsü oluşturma](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Sonraki adımlar

Toplu Işe yönelik ayrıntılı genel bakış için bkz. [Batch ile büyük ölçekli paralel işlem çözümleri geliştirme](batch-api-basics.md).
