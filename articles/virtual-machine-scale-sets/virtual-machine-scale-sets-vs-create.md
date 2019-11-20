---
title: Visual Studio kullanarak sanal makine ölçek kümesini dağıtma | Microsoft Docs
description: Visual Studio ve bir Kaynak Yöneticisi şablonu kullanarak sanal makine ölçek kümeleri dağıtma
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: d397f81ce29e0ec738156b755948985a4edfc70b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802262"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Visual Studio ile sanal makine ölçek kümesi oluşturma

Bu makalede, bir Visual Studio kaynak grubu dağıtımı kullanarak bir Azure sanal makine ölçek kümesinin nasıl dağıtılacağı gösterilir.

[Azure sanal makine ölçek kümeleri](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) , benzer sanal makinelerin bir koleksiyonunu otomatik ölçeklendirme ve Yük Dengeleme ile dağıtmak ve yönetmek Için bir Azure işlem kaynağıdır. [Azure Resource Manager şablonları](https://github.com/Azure/azure-quickstart-templates)kullanarak sanal makine ölçek kümeleri temin edebilir ve dağıtabilirsiniz. Azure Resource Manager şablonları Azure CLı, PowerShell, REST ve ayrıca doğrudan Visual Studio 'dan dağıtılabilir. Visual Studio, bir Azure Kaynak grubu dağıtım projesinin parçası olarak dağıtabileceğiniz örnek şablonlar kümesi sağlar.

Azure Kaynak grubu dağıtımları, bir dizi ilgili Azure kaynağını tek bir dağıtım işleminde gruplamak ve yayımlamak için bir yoldur. Daha fazla bilgi için bkz. [Visual Studio aracılığıyla Azure Kaynak grupları oluşturma ve dağıtma](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Önkoşullar

Visual Studio 'da sanal makine ölçek kümelerini dağıtmaya başlamak için aşağıdaki önkoşullara sahip olmanız gerekir:

* Visual Studio 2013 veya üzeri
* Azure SDK 2,7, 2,8 veya 2,9

>[!NOTE]
>Bu makalede, [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)Ile Visual Studio 2019 kullanılmaktadır.

## Proje oluşturma<a name="creating-a-project"></a> 

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur**' da **Azure Kaynak grubu** ' nu seçin C# ve ardından **İleri**' yi seçin.

1. **Yeni projenizi Yapılandır**' da, bir ad girin ve **Oluştur**' u seçin.

    ![Projeyi adlandırın ve oluşturun](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Şablon listesinden, **Windows sanal makine ölçek kümesi** veya **Linux sanal makine ölçek kümesi** şablonunu seçin. **Tamam**’ı seçin.

   ![Bir sanal makine şablonu seçin](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Projenizi oluşturduktan sonra, **Çözüm Gezgini** bir PowerShell dağıtım betiği, bir Azure Resource Manager şablonu ve sanal makine ölçek kümesi için bir parametre dosyası içerir.

## <a name="customize-your-project"></a>Projenizi özelleştirme

Artık şablonu, uygulamanızın gereksinimlerine göre özelleştirmek üzere düzenleyebilirsiniz. Sanal makine uzantısı özellikleri ekleyebilir veya yük dengeleme kurallarını düzenleyebilirsiniz. Varsayılan olarak, sanal makine ölçek kümesi şablonları **AzureDiagnostics** uzantısını dağıtmak üzere yapılandırılmıştır, bu da otomatik ölçeklendirme kuralları eklemenizi kolaylaştırır. Şablonlar, gelen NAT kurallarıyla yapılandırılmış genel IP adresi olan bir yük dengeleyici de dağıtır.

Yük dengeleyici, sanal makine örneklerine SSH (Linux) veya RDP (Windows) ile bağlanmanızı sağlar. Ön uç bağlantı noktası aralığı 50000 ' de başlar. Linux için, bağlantı noktası 50000 ' e SSH kullanıyorsanız, Yük Dengeleme sizi ölçek kümesindeki ilk sanal makinenin 22 numaralı bağlantı noktasına yönlendirir. 50001 numaralı bağlantı noktasına bağlantı, ikinci sanal makinenin 22 numaralı bağlantı noktasına yönlendirilir ve bu şekilde devam eder.

 Visual Studio ile şablonlarınızı düzenlemenin iyi bir yolu, **JSON ana hattını**kullanmaktır. Parametreleri, değişkenleri ve kaynakları düzenleyebilirsiniz. Şemayı anlamak için, Visual Studio dağıtmadan önce şablonunuzda hataları işaret edebilir.

![JSON Gezgini](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Projeyi dağıtma

Sanal makine ölçek kümesi kaynağını oluşturmak için Azure Resource Manager şablonunu dağıtın:

1. **Çözüm Gezgini**, projeye sağ tıklayın ve @no__t **Dağıt**-2**Yeni**' yi seçin.

    ![Projenizi dağıtın](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. **Kaynak grubuna dağıt**bölümünde kullanılacak aboneliği seçin ve bir kaynak grubu seçin. Gerekirse bir kaynak grubu oluşturabilirsiniz.

1. Sonra, şablonunuza geçirilen parametreleri girmek için **parametreleri Düzenle** ' yi seçin.

   ![Abonelik ve kaynak grubu girin](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. İşletim sistemi için Kullanıcı adını ve parolayı belirtin. Bu değerler, dağıtımı oluşturmak için gereklidir. Yüklü PowerShell Tools for Visual Studio yoksa gizli bir PowerShell komut isteminden kaçınmak için **parolaları kaydet** ' i seçin veya [Key Vault desteğini](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)kullanın. Devam etmek için **Kaydet** ' i seçin.

    ![Dağıtım parametrelerini Düzenle](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. **Kaynak grubuna dağıt**bölümünde **Dağıt**' ı seçin. Eylem **Deploy-AzureResourceGroup. ps1** betiğini çalıştırır. **Çıkış** penceresinde dağıtım ilerleme durumu gösterilir.

   ![Çıktı sonuçları gösterir](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## Sanal makine ölçek kümesini keşfet<a name="exploring-your-virtual-machine-scale-set"></a>

Yeni sanal makine ölçek kümesini görüntülemek için  > **bulut Gezginini** **görüntüle**' yi seçin. Gerekirse **Tümünü Yenile**seçeneğini kullanın.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** , uygulamaları geliştirirken Visual Studio 'da Azure kaynaklarını yönetmenizi sağlar. Ayrıca, [Azure Portal](https://portal.azure.com) ve [Azure Kaynak Gezgini](https://resources.azure.com/)sanal makine ölçek kümesini görüntüleyebilirsiniz.

 Portal, Azure altyapınızı bir Web tarayıcısı ile yönetmenin en iyi yolunu sağlar. Azure Kaynak Gezgini, Azure kaynaklarını keşfetmeye ve hata ayıklamanıza yönelik kolay bir yol sağlar. Azure Kaynak Gezgini, örnek görünümünü sunar ve ayrıca, aradığınız kaynaklar için PowerShell komutlarını gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Sanal makine ölçek kümelerini Visual Studio aracılığıyla başarıyla dağıttıktan sonra, projenizi uygulama gereksinimlerinize uyacak şekilde daha da özelleştirebilirsiniz. Örneğin, bir **Öngörüler** kaynağı ekleyerek otomatik ölçeklendirmeyi yapılandırın. Şablonunuza tek başına sanal makineler gibi altyapı ekleyebilir veya özel Betik uzantısı kullanarak uygulamalar dağıtabilirsiniz. İyi örnek şablonlar, [Azure hızlı başlangıç şablonları](https://github.com/Azure/azure-quickstart-templates) GitHub deposunda bulunabilir. `vmss` arayın.
