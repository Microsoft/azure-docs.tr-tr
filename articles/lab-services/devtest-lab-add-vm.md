---
title: Azure DevTest Labs 'de laboratuvara VM ekleme | Microsoft Docs
description: Azure DevTest Labs ' de bir laboratuvara sanal makine eklemeyi öğrenin
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 64a5892226b90019f175308b5984dc0a0108e226
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162245"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs 'de laboratuvara VM ekleme
[İlk sanal makineyi zaten oluşturduysanız](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), büyük olasılıkla önceden yüklenmiş bir [Market görüntüsünden](devtest-lab-configure-marketplace-images.md)olabilirsiniz. Bundan sonra laboratuvarıza sonraki VM 'Ler eklemek istiyorsanız, [özel bir görüntü](devtest-lab-create-template.md) veya [Formül](devtest-lab-manage-formulas.md)olan bir *tabanı* da seçebilirsiniz. Bu öğreticide, DevTest Labs içindeki bir laboratuvara VM eklemek için Azure portal kullanma adımları gösterilmektedir.

Bu makalede ayrıca laboratuvarınızda bir VM için yapıtları yönetme de gösterilmektedir.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs bir laboratuvara VM ekleme adımları
1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.
1. **Tüm hizmetler**' i seçin ve **DevOps** bölümünde **DevTest Labs** ' i seçin. DevOps bölümünde, **DevTest Labs** ' in yanındaki * (Star) seçeneğini belirleyin. Bu eylem, bir sonraki sefer kolayca erişebilmek için **DevTest Labs** 'i sol gezinti menüsüne ekler. Ardından, sol gezinti menüsünde **DevTest Labs** ' i seçebilirsiniz.

    ![Tüm hizmetler-DevTest Labs seçin](./media/devtest-lab-create-lab/all-services-select.png)
1. Laboratuvarlar listesinden, sanal makineyi oluşturmak istediğiniz Laboratuvarı seçin.
2. Laboratuvarın **genel bakış** sayfasında **+ Ekle**' yi seçin.

    ![VM Ekle düğmesi](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. **Temel seçin** SAYFASıNDA, VM için bir market görüntüsü seçin.
1. **Sanal makine** sayfasının **temel ayarlar** sekmesinde aşağıdaki işlemleri yapın:
    1. **Sanal makine adı** metın kutusuna VM için bir ad girin. Metin kutusu, sizin için otomatik olarak oluşturulan benzersiz bir ad ile önceden doldurulur. Ad, e-posta adresiniz içindeki kullanıcı adına ve ardından benzersiz 3 basamaklı bir sayıya karşılık gelir. Bu özellik sizi bir makine adı düşünmek ve her makine oluşturduğunuzda yazmanız için zaman kazandırır. İsterseniz, bu otomatik doldurulmuş alanı seçtiğiniz bir adla geçersiz kılabilirsiniz. VM 'nin otomatik doldurulmuş adını geçersiz kılmak için, **sanal makine adı** metin kutusuna bir ad girin.
    2. Sanal makinede yönetici ayrıcalıklarına sahip bir **Kullanıcı adı** girin. Makinenin **Kullanıcı adı** , otomatik olarak oluşturulan benzersiz bir adla önceden doldurulmuştur. Ad, e-posta adresiniz içindeki kullanıcı adına karşılık gelir. Bu özellik size, her yeni makine oluşturduğunuzda bir kullanıcı adına karar verirken size zaman kazandırır. Daha sonra, isterseniz bu otomatik doldurulmuş alanı seçtiğiniz bir Kullanıcı adı ile geçersiz kılabilirsiniz. Kullanıcı adı için otomatik doldurulmuş değeri geçersiz kılmak için, **Kullanıcı adı** metin kutusuna bir değer girin. Bu kullanıcıya, sanal makinede **yönetici** ayrıcalıkları verilir.
    3. Laboratuvarda ilk VM oluşturuyorsanız, Kullanıcı için bir **parola** girin. Bu parolayı laboratuvara ilişkili Azure anahtar kasasında varsayılan parola olarak kaydetmek için **Varsayılan parola olarak kaydet**' i seçin. Varsayılan parola, şu ada sahip anahtar kasasında kaydedilir: **VmPassword**. Laboratuvarda sonraki VM 'Leri oluşturmaya çalıştığınızda, **parola**için **VmPassword** otomatik olarak seçilir. Değeri geçersiz kılmak için, **kaydedilmiş gizli anahtar kullan** onay kutusunu temizleyin ve bir parola girin.

        ![Bir temel seçin](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Ayrıca, gizli dizileri önce anahtar kasasında kaydedebilir ve ardından laboratuvarda bir VM oluştururken kullanabilirsiniz. Daha fazla bilgi için bkz. [gizli dizileri bir anahtar kasasında depolayın](devtest-lab-store-secrets-in-key-vault.md). Anahtar kasasında depolanan parolayı kullanmak için, **kaydedilmiş bir gizli dizi kullan**' ı seçin ve gizli bir değer (parola) ile ilişkili bir anahtar değeri belirtin.
    4. **Diğer seçenekler** bölümünde **boyutu Değiştir**' i seçin. Oluşturulacak ön tanımlı öğelerden birini, işlemci çekirdeğini, RAM boyutunu ve oluşturulacak VM 'nin sabit sürücü boyutunu seçin.
    5. **Yapıtları Ekle veya Kaldır**' ı seçin. Taban görüntüsüne eklemek istediğiniz yapıtları seçin ve yapılandırın.
    **Not:** DevTest Labs 'e yeni başladıysanız veya yapıtları yapılandırıyorsanız, [BIR VM 'ye mevcut yapıt ekleme](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) bölümüne bakın ve sonra tamamlandığında buraya geri dönün.
2. Üstteki **Gelişmiş ayarlar** sekmesine geçin ve aşağıdaki işlemleri yapın:
    1. VM 'nin bulunduğu sanal ağı değiştirmek için **VNET 'ı Değiştir**' i seçin.
    2. Alt ağı değiştirmek için **alt ağı Değiştir**' i seçin.
    3. VM 'nin IP adresinin **Genel, özel veya paylaşılan**olduğunu belirtin.
    4. VM 'yi otomatik olarak silmek için **süre sonu tarihini ve saatini**belirtin.
    5. VM 'nin bir laboratuvar kullanıcısı tarafından çakışarak kullanılabilmesini sağlamak için, **Bu makinenin** çakışmasına izin vermek için **Evet** ' i seçin.
    6. Laboratuvar kullanıcılarınız için kullanılabilir hale getirmek istediğiniz **sanal makine örneklerinin** sayısını belirtin.

        ![Bir temel seçin](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Belirtilen VM 'yi laboratuvara eklemek için **Oluştur** ' u seçin.

   Laboratuvar sayfası, VM başlatıldıktan sonra **çalışıyor** olarak VM 'nin oluşturma durumunu ( **oluşturma**olarak) görüntüler.

    ![Sanal makine oluşturma durumu](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>VM 'ye mevcut bir yapıt ekleme
Bir VM oluştururken, var olan yapıtları ekleyebilirsiniz. Her laboratuvar, ortak DevTest Labs yapıt deposundaki yapıtlar ile oluşturduğunuz ve kendi yapıt deponuza eklediğiniz yapıtları içerir.

* Azure DevTest Labs *yapıtlar* , Windows PowerShell betikleri çalıştırma, Bash komutlarını çalıştırma ve yazılım yükleme gıbı, VM sağlandığında gerçekleştirilen *eylemleri* belirtmenize olanak tanır.
* Yapıt *parametreleri* , belirli senaryonuz için yapıtı özelleştirmenizi sağlar

Yapıtları oluşturma hakkında [bilgi edinmek için, DevTest Labs ile kullanmak üzere kendi yapılarınızı nasıl yazacağınızı öğrenmek](devtest-lab-artifact-author.md)için makalesine bakın.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.
1. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.
1. Laboratuvarlar listesinden, çalışmak istediğiniz VM 'yi içeren Laboratuvarı seçin.
1. **Sanal makinelerimi**seçin.
1. İstediğiniz VM 'yi seçin.
1. **Yapıtları Yönet**' i seçin.
1. **Yapıtları Uygula**' yı seçin.
1. **Yapıtları Uygula** BÖLMESINDE, VM 'ye eklemek istediğiniz yapıtı seçin.
1. **Yapıt Ekle** bölmesinde gerekli parametre değerlerini ve ihtiyacınız olan tüm isteğe bağlı parametreleri girin.
1. Yapıtı eklemek için **Ekle** ' yi seçin ve **yapıtları Uygula** bölmesine dönün.
1. VM 'niz için gereken yapıtları eklemeye devam edin.
1. Yapılarınızı ekledikten sonra [yapıtların çalıştırıldığı sırayı değiştirebilirsiniz](#change-the-order-in-which-artifacts-are-run). Ayrıca, [bir yapıtı görüntülemek veya değiştirmek](#view-or-modify-an-artifact)için geri dönebilirsiniz.
1. Yapıtları eklemeyi tamamladığınızda **Uygula** ' yı seçin.

## <a name="change-the-order-in-which-artifacts-are-run"></a>Yapıtların çalıştırıldığı sırayı değiştirme
Varsayılan olarak, yapıtların eylemleri VM 'ye eklendikleri sırada yürütülür.
Aşağıdaki adımlarda yapıtların çalıştırıldığı sıranın nasıl değiştirileceği gösterilmektedir.

1. **Yapıtları Uygula** bölmesinin en üstünde, VM 'ye eklenmiş yapıların sayısını belirten bağlantıyı seçin.

    ![VM 'ye eklenen yapıt sayısı](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **Seçilen yapılar** bölmesinde, yapıtları istediğiniz sırada sürükleyin ve bırakın. **Not:** Yapıtı sürüklerken sorun yaşıyorsanız, yapıtın sol tarafından sürüklediğinizden emin olun.
1. Tamamladığınızda **Tamam**’ı seçin.

## <a name="view-or-modify-an-artifact"></a>Yapıtı görüntüleme veya değiştirme
Aşağıdaki adımlarda, bir yapının parametrelerinin nasıl görüntüleneceği veya değiştirileceği gösterilmektedir:

1. **Yapıtları Uygula** bölmesinin en üstünde, VM 'ye eklenmiş yapıların sayısını belirten bağlantıyı seçin.

    ![VM 'ye eklenen yapıt sayısı](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **Seçilen yapılar** bölmesinde, görüntülemek veya düzenlemek istediğiniz yapıtı seçin.
1. **Yapıt Ekle** bölmesinde, gerekli değişiklikleri yapın ve **Tamam** ' ı seçerek **yapıt Ekle** bölmesini kapatın.
1. **Seçilen yapılar** bölmesini kapatmak için **Tamam ' ı** seçin.

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager şablonu Kaydet
Azure Resource Manager şablonu, yinelenebilir bir dağıtımı tanımlamanın bildirim temelli bir yolunu sağlar.
Aşağıdaki adımlarda, oluşturulmakta olan VM için Azure Resource Manager şablonunun nasıl kaydedileceği açıklanmaktadır.
Kaydedildikten sonra, [Azure PowerShell yeni VM 'leri dağıtmak](../azure-resource-manager/template-deployment-overview.md)için Azure Resource Manager şablonunu kullanabilirsiniz.

1. **Sanal makine** bölmesinde **Azure Resource Manager şablonu görüntüle**' yi seçin.
2. **Azure Resource Manager şablonu görüntüle** bölmesinde Şablon metnini seçin.
3. Seçili metni panoya kopyalayın.
4. **Azure Resource Manager şablon bölmesini**kapatmak için **Tamam ' ı** seçin.
5. Bir metin düzenleyicisi açın.
6. Panodaki Şablon metnini yapıştırın.
7. Dosyayı daha sonra kullanmak üzere kaydedin.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Sonraki adımlar
* VM oluşturulduktan sonra VM 'nin bölmesinde **Bağlan** ' ı seçerek VM 'ye bağlanabilirsiniz.
* [DevTest Labs VM 'niz için özel yapıtlar oluşturmayı](devtest-lab-artifact-author.md)öğrenin.
* [DevTest Labs Azure Resource Manager hızlı başlangıç şablonu galerisini](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)gezin.
