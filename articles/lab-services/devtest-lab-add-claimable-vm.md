---
title: Azure DevTest Labs bir laboratuvarda çakışan sanal makineler oluşturun ve yönetin | Microsoft Docs
description: Azure DevTest Labs bir laboratuvara çakışan sanal makine eklemeyi öğrenin
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 7ad10a1763b4882aa3bb6aec7447f57ebaf07369
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123708"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Azure DevTest Labs çakışan VM 'Ler oluşturun ve yönetin
[Özel bir görüntü](devtest-lab-create-template.md), [Formül](devtest-lab-manage-formulas.md)veya [Market görüntüsü](devtest-lab-configure-marketplace-images.md)olan BIR *tabandan* , [Standart bir VM](devtest-lab-add-vm.md) 'yi nasıl ekleyeceğiniz gibi bir laboratuvarya bir laboratuvara bir sanal makine eklersiniz. Bu öğreticide, DevTest Labs içindeki bir laboratuvara çakışmaz bir VM eklemek için Azure portal kullanma ve bir kullanıcının VM 'yi talep etmek ve talep etmek için izlediği süreçler gösterilmektedir.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs bir laboratuvara çakışmaz bir VM ekleme adımları
1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.
1. **Tüm hizmetler**' i seçin ve **DevOps** bölümünde **DevTest Labs** ' i seçin. DevOps bölümünde, **DevTest Labs** ' in yanındaki * (Star) seçeneğini belirleyin. Bu eylem, bir sonraki sefer kolayca erişebilmek için **DevTest Labs** 'i sol gezinti menüsüne ekler. Ardından, sol gezinti menüsünde **DevTest Labs** ' i seçebilirsiniz.

    ![Tüm hizmetler-DevTest Labs seçin](./media/devtest-lab-create-lab/all-services-select.png)
1. Laboratuvarlar listesinden, sanal makineyi oluşturmak istediğiniz Laboratuvarı seçin.
2. Laboratuvarın **genel bakış** sayfasında **+ Ekle**' yi seçin.

    ![VM Ekle düğmesi](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. **Temel seçin** SAYFASıNDA, VM için bir market görüntüsü seçin.
1. **Sanal makine** sayfasının **temel ayarlar** sekmesinde aşağıdaki işlemleri yapın:
    1. **Sanal makine adı** metın kutusuna VM için bir ad girin. Metin kutusu, sizin için otomatik olarak oluşturulan benzersiz bir ad ile önceden doldurulur. Ad, e-posta adresiniz içindeki kullanıcı adına ve ardından benzersiz 3 basamaklı bir sayıya karşılık gelir. Bu özellik sizi bir makine adı düşünmek ve her makine oluşturduğunuzda yazmanız için zaman kazandırır. İsterseniz, bu otomatik doldurulmuş alanı seçtiğiniz bir adla geçersiz kılabilirsiniz. VM 'nin otomatik doldurulmuş adını geçersiz kılmak için, **sanal makine adı** metin kutusuna bir ad girin.
    2. Sanal makinede yönetici ayrıcalıklarına sahip bir **Kullanıcı adı** girin. Makinenin **Kullanıcı adı** , otomatik olarak oluşturulan benzersiz bir adla önceden doldurulmuştur. Ad, e-posta adresiniz içindeki kullanıcı adına karşılık gelir. Bu özellik size, her yeni makine oluşturduğunuzda bir kullanıcı adına karar verirken size zaman kazandırır. Daha sonra, isterseniz bu otomatik doldurulmuş alanı seçtiğiniz bir Kullanıcı adı ile geçersiz kılabilirsiniz. Kullanıcı adı için otomatik doldurulmuş değeri geçersiz kılmak için, **Kullanıcı adı** metin kutusuna bir değer girin. Bu kullanıcıya, sanal makinede **yönetici** ayrıcalıkları verilir.
    3. Laboratuvarda ilk VM oluşturuyorsanız, Kullanıcı için bir **parola** girin. Bu parolayı laboratuvara ilişkili Azure anahtar kasasında varsayılan parola olarak kaydetmek için **Varsayılan parola olarak kaydet**' i seçin. Varsayılan parola, anahtar kasasında şu adla kaydedilir: **VmPassword**. Laboratuvarda sonraki VM 'Leri oluşturmaya çalıştığınızda, **parola**için **VmPassword** otomatik olarak seçilir. Değeri geçersiz kılmak için, **kaydedilmiş gizli anahtar kullan** onay kutusunu temizleyin ve bir parola girin.

        Ayrıca, gizli dizileri önce anahtar kasasında kaydedebilir ve ardından laboratuvarda bir VM oluştururken kullanabilirsiniz. Daha fazla bilgi için bkz. [gizli dizileri bir anahtar kasasında depolayın](devtest-lab-store-secrets-in-key-vault.md). Anahtar kasasında depolanan parolayı kullanmak için, **kaydedilmiş bir gizli dizi kullan**' ı seçin ve gizli bir değer (parola) ile ilişkili bir anahtar değeri belirtin.
    4. **Diğer seçenekler** bölümünde **boyutu Değiştir**' i seçin. Oluşturulacak ön tanımlı öğelerden birini, işlemci çekirdeğini, RAM boyutunu ve oluşturulacak VM 'nin sabit sürücü boyutunu seçin.
    5. **Yapıtları Ekle veya Kaldır**' ı seçin. Taban görüntüsüne eklemek istediğiniz yapıtları seçin ve yapılandırın.
    **Note:** DevTest Labs 'e yeni başladıysanız veya yapıtları yapılandırıyorsanız, [BIR VM 'ye mevcut yapıt ekleme](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) bölümüne bakın ve sonra tamamlandığında buraya geri dönün.
2. Üstteki **Gelişmiş ayarlar** sekmesine geçin ve aşağıdaki işlemleri yapın:
    1. VM 'nin bulunduğu sanal ağı değiştirmek için **VNET 'ı Değiştir**' i seçin.
    2. Alt ağı değiştirmek için **alt ağı Değiştir**' i seçin.
    3. VM 'nin IP adresinin **Genel, özel veya paylaşılan**olduğunu belirtin.
    4. VM 'yi otomatik olarak silmek için **süre sonu tarihini ve saatini**belirtin.
    5. VM 'nin bir laboratuvar kullanıcısı tarafından çakışarak kullanılabilmesini sağlamak için, **Bu makinenin** çakışmasına izin vermek için **Evet** ' i seçin.
    6. Laboratuvar kullanıcılarınız için kullanılabilir hale getirmek istediğiniz **sanal makine örneklerinin** sayısını belirtin.
3. Belirtilen VM 'yi laboratuvara eklemek için **Oluştur** ' u seçin.

   Laboratuvar sayfası, VM başlatıldıktan sonra **çalışıyor** olarak VM 'nin oluşturma durumunu ( **oluşturma**olarak) görüntüler.

> [!NOTE]
>  [Azure Resource Manager şablonları](devtest-lab-create-environment-from-arm.md)aracılığıyla laboratuvar VM 'leri dağıtırsanız, Özellikler bölümünde **allowclaim** özelliğini true olarak ayarlayarak çakışarak sanal makineler oluşturabilirsiniz.


## <a name="using-a-claimable-vm"></a>Çakışmaz bir VM kullanma

Bir Kullanıcı aşağıdaki adımlardan birini gerçekleştirerek, "çakışan sanal makineler" listesinden herhangi bir VM 'yi talep edebilir:

* Laboratuvarın "genel bakış" bölmesinin alt kısmındaki "çakışan sanal makineler" listesinden, listedeki VM 'lerden birine sağ tıklayın ve **talep makinesi**' ni seçin.

  ![Belirli bir çakışmaz VM isteyin.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* "Genel bakış" bölmesinin en üstünde, **talep**et ' i seçin. Çakışan VM 'Ler listesinden rastgele bir sanal makine atanır.

  ![Herhangi bir çakışmaz VM isteyin.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Kullanıcı bir VM talep ettikten sonra, DevTest Labs makineyi başlatır ve laboratuvar kullanıcısının "sanal makinelerim" listesine taşır. Bu, laboratuvar kullanıcısının artık bu mahcine üzerinde sahip ayrıcalıklı bir sahip olacağı anlamına gelir. Bu adım için gereken süre, başlangıç zamanına ve talep olayı sırasında gerçekleştirilen diğer özel eylemlere göre değişiklik gösterebilir. Talep alındıktan sonra makine, çakışan havuzda artık kullanılamaz.  

## <a name="unclaim-a-vm"></a>VM 'yi talep kaldırma

Bir Kullanıcı, talep edilen bir VM 'yi kullanmayı bitirdiğinde ve başka birinin kullanımına açmak istediğinde, aşağıdaki adımlardan birini gerçekleştirerek, istenen VM 'yi, çakışan sanal makineler listesine döndürebilir:

- "Sanal makinelerim" listesinden, listedeki VM 'lerden birine sağ tıklayın veya üç nokta (...) simgesini seçin ve **talebi kaldır**' ı seçin.

  ![VM listesi üzerinde bir VM 'yi talep kaldırma.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- "Sanal makinelerim" listesinde, yönetim bölmesini açmak için bir VM seçin, sonra üst menü çubuğundan **talebi geri al** ' ı seçin.

  ![VM 'nin Yönetim bölmesinde bir VM 'yi talep kaldırma.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Bir Kullanıcı bir VM 'yi talep açtığında, bu özel laboratuvar VM 'si için artık sahip izinlere sahip olmaz ve havuza yeniden tıklandı durumunda başka bir laboratuvar kullanıcısı tarafından talep edilebilir. 

### <a name="transferring-the-data-disk"></a>Veri diski aktarılıyor
Çakışan bir VM 'ye bağlı bir veri diski varsa ve bir kullanıcı talebi geri alıyorsa, veri diski VM 'de kalır. Daha sonra başka bir kullanıcı o VM 'yi talep ettiği zaman, bu yeni Kullanıcı, veri diskini ve sanal makineyi de talep ediyor.

Bu, "veri diski aktarılıyor" olarak bilinir. Daha sonra veri diski, **veri disklerimin** yönetilmesi için yeni kullanıcının listesinde kullanılabilir hale gelir.

![Veri disklerini talep kaldırma.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Sonraki adımlar
* Oluşturulduktan sonra, yönetim bölmesine **Bağlan** ' ı seçerek VM 'ye bağlanabilirsiniz.
* [DevTest Labs Azure Resource Manager hızlı başlangıç şablonu galerisini](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)gezin.
