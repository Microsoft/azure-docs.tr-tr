---
title: Azure DevTest Labs bir laboratuvara etiket ekleme | Microsoft Docs
description: Azure DevTest Labs laboratuvara nasıl etiket ekleneceğini öğrenin
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302788"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs laboratuvara etiket ekleme

Kaynaklarınızın mantıksal olarak sınıflandırmasını sağlamak için özel Etiketler oluşturabilir ve bunları DevTest Labs kaynaklarına uygulayabilirsiniz. Daha sonra, bu etikete sahip aboneliğinizdeki tüm kaynakları hızlıca ve kolayca görebilirsiniz. Etiketler faturalandırma veya yönetim için kaynakları düzenlemeniz gerektiğinde faydalıdır.

Etiketler tarafından desteklenen kaynaklar şunlardır

* İşlem VM 'Leri
* NIC’ler
* IP adresleri
* Yük dengeleyiciler
* Depolama hesapları
* Yönetilen Diskler

[Bir laboratuvar oluşturup](devtest-lab-create-lab.md) daha sonra yapılandırma ve Ayarlar altındaki Etiketler dikey penceresinde bunları yönetirken Etiketler uygulayabilirsiniz.

Her etiket bir **ad**/**değer** çiftinden oluşur. Örneğin, *34543*değerine sahip *costcenter* adlı bir etiket oluşturabilirsiniz. Bu gibi bir etiket, kuruluşunuzun bu belirli alanı için faturalandırılabilen laboratuvar kaynaklarını daha sonra belirlemenize yardımcı olur. Aboneliğinizi nasıl düzenlemek istediğinize ilişkin anlamlı adlar ve değerler seçmeniz gerekir.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Mevcut laboratuvarda etiketleri yönetme adımları

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)’ında oturum açın.
1. Gerekirse, **tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin. Laboratuvarınız panoda zaten **tüm kaynaklar**altında gösteriliyor olabilir.
1. Laboratuvarlar listesinden, etiketleri eklemek veya yönetmek istediğiniz Laboratuvarı seçin.
1. Laboratuvarın **genel bakış** alanında **yapılandırma ve ilkeler**' i seçin.

    ![Yapılandırma ve ilkeler düğmesi](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. **Yönet**altında sol tarafta **Etiketler**' i seçin.
1. Bu laboratuvar için yeni bir etiket oluşturmak üzere bir **ad**/**değer** çifti girin ve **Kaydet**' i seçin. Bu etiketle ilişkili kaynakları görüntülemek veya yönetmek için listeden varolan bir etiketi de seçebilirsiniz.

    ![Etiketleri yönet](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Laboratuvar düzeyinde oluşturulan Etiketler, laboratuvarın aboneliğinizde aldığı tüm faturalanabilir kaynaklar üzerinden akar. Örneğin, laboratuvar düzeyi etiketleri laboratuvar VM 'lerinin temel işlem VM 'lerine akar. etiketleri maliyet yönetimi bağlamında kullanabilirsiniz. Laboratuvar düzeyi etiketleri, maliyet yönetimi için etiket filtreinde görünür.

## <a name="understanding-limitations-to-tags"></a>Etiketlere yönelik sınırlamaları anlama

Etiketler için aşağıdaki sınırlamalar geçerlidir:

* Her kaynak veya kaynak grubu en fazla 15 etiket adı/değer çifti içerebilir. Bu sınırlama yalnızca kaynak grubu veya kaynağa doğrudan uygulanan etiketler için geçerlidir. Kaynak grupları, her biri 15 etiket adı/değer çiftine sahip çok sayıda kaynak içerebilir.
* Etiket adı 512 karakter ile sınırlıdır ve etiket değeri 256 karakter ile sınırlıdır. Depolama hesapları için etiket adı 128 karakter ile sınırlıdır ve etiket değeri 256 karakter ile sınırlıdır.
* Kaynak grubuna uygulanan etiketler, bu kaynak grubundaki kaynaklar tarafından devralınmaz.

[Azure kaynaklarınızı düzenlemek için etiketleri kullanın](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) , PowerShell veya Azure CLI kullanarak etiketleri yönetme dahil olmak üzere Azure 'da etiketleri kullanma hakkında daha fazla ayrıntı sağlar.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Sonraki adımlar
* Özelleştirilmiş ilkeleri kullanarak aboneliğinizde kısıtlamalar ve kurallar uygulayabilirsiniz. Tanımladığınız bir ilke, tüm kaynakların belirli bir etiket için bir değere sahip olmasını gerektirebilir. Daha fazla bilgi için bkz. [ilkeleri ve zamanlamaları ayarlama](devtest-lab-set-lab-policy.md).
* [DevTest Labs Azure Resource Manager hızlı başlangıç şablonu galerisini](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)gezin.
