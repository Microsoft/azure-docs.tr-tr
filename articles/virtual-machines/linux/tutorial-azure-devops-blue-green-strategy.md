---
title: Öğretici-Azure Linux sanal makineleri için kanarya dağıtımlarını yapılandırma
description: Bu öğreticide, sürekli dağıtım (CD) işlem hattı ayarlamayı öğreneceksiniz. Bu işlem hattı, mavi yeşil dağıtım stratejisini kullanarak bir Azure Linux sanal makinesi grubunu güncelleştirir.
author: moala
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 4545891cce926f049673cd2c2380a8309f2e71a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552596"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Öğretici-Azure Linux sanal makineleri için mavi yeşil dağıtım stratejisini yapılandırma

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Hizmet olarak altyapı (IaaS)-CI/CD 'yi yapılandırma

Azure Pipelines, sanal makinelere dağıtımlar için tam özellikli bir CI/CD otomasyon araçları kümesi sağlar. Azure portal bir Azure VM için sürekli teslim işlem hattı yapılandırabilirsiniz.

Bu makalede, çok makineli dağıtımlar için mavi yeşil stratejiyi kullanan bir CI/CD işlem hattının nasıl ayarlanacağı gösterilmektedir. Azure Portal Ayrıca, [yuvarlama](./tutorial-devops-azure-pipelines-classic.md) ve [kanarya](./tutorial-azure-devops-canary-strategy.md)gibi diğer stratejileri de destekler.

### <a name="configure-cicd-on-virtual-machines"></a>Sanal makinelerde CI/CD 'yi yapılandırma

Sanal makineleri bir [dağıtım grubuna](/azure/devops/pipelines/release/deployment-groups)hedefler olarak ekleyebilirsiniz. Daha sonra bunları çok makineli güncelleştirmeler için hedefleyebilirsiniz. Makinelere dağıttıktan sonra, dağıtım **geçmişini** bir dağıtım grubu içinde görüntüleyin. Bu görünüm VM 'den işlem hattına ve sonra işlemeye izlemenizi sağlar.

### <a name="blue-green-deployments"></a>Mavi-yeşil dağıtımlar

Mavi yeşil bir dağıtım, aynı bekleme ortamına sahip olan kapalı kalma süresini azaltır. Her seferinde yalnızca bir ortam canlı olur.

Yeni bir sürüm hazırlandıktan sonra, yeşil ortamda testin son aşamasını doldurun. Yazılım yeşil ortamda çalıştıktan sonra, gelen tüm isteklerin yeşil ortama gitmesi için trafiği değiştirin. Mavi ortam boşta.

Sürekli teslim seçeneğini kullanarak, Azure portal sanal makinelerinize mavi yeşil dağıtımlar yapılandırabilirsiniz. Adım adım izlenecek yol:

1. Azure portal oturum açın ve bir sanal makineye gidin.
1. VM ayarlarının sol bölmesinde **sürekli teslim**' yı seçin. Ardından **Yapılandır**' ı seçin.

   ![Yapılandır düğmesi ile sürekli teslim bölmesi](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Yapılandırma panelinde, mevcut bir hesabı seçmek veya yeni bir hesap oluşturmak için **Azure DevOps organizasyonu** ' nı seçin. Ardından, ardışık düzeni yapılandırmak istediğiniz projeyi seçin.  

   ![Sürekli teslim paneli](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Dağıtım grubu, fiziksel ortamları temsil eden bir dağıtım hedefi makineleri kümesidir. Geliştirme, test, UAT ve üretim örneklere örnektir. Yeni bir dağıtım grubu oluşturabilir veya var olan bir dağıtım grubu seçebilirsiniz.
1. Sanal makineye dağıtılacak paketi yayımlayan derleme işlem hattını seçin. Yayınlanan paketin, paketin kök klasöründeki deployscripts klasöründe deploy.ps1 veya deploy.sh adlı bir dağıtım betiği olmalıdır. İşlem hattı bu dağıtım betiğini çalıştırır.
1. **Dağıtım stratejisi**' nda **mavi-yeşil**' i seçin.
1. Mavi-yeşil dağıtımların parçası olacak VM 'lere "mavi" veya "yeşil" bir etiket ekleyin. Bir VM bir bekleme rolü için ise, "yeşil" olarak etiketleyin. Aksi halde, "mavi" olarak etiketleyin.

   ![Dağıtım stratejisi değeri Blue-Green seçili olan sürekli teslim paneli](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Sanal makineye dağıtılacak sürekli teslim işlem hattını yapılandırmak için **Tamam ' ı** seçin.

   ![Mavi-yeşil işlem hattı](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Sanal makine için dağıtım ayrıntıları görüntülenir. Azure DevOps 'daki yayın ardışık düzenine gitmek için bağlantıyı seçebilirsiniz. Yayın ardışık düzeninde, işlem hattı yapılandırmasını görüntülemek için **Düzenle** ' yi seçin. İşlem hattı şu üç aşamaya sahiptir:

   1. Bu aşama bir dağıtım grubu aşamasıdır. Uygulamalar, "yeşil" olarak etiketlenen bekleme sanal makinelerine dağıtılır.
   1. Bu aşamada, işlem hattı duraklatılır ve el ile müdahale çalışmasının çalışmayı sürdürmesini bekler. Kullanıcılar, "yeşil" olarak etiketlenen VM 'lere dağıtım kararlılığını el ile yaptıktan sonra işlem hattı çalıştırmasını sürdürür.
   1. Bu aşama, VM 'lerdeki "mavi" ve "yeşil" etiketleri değiştirir. Bu, eski uygulama sürümlerine sahip VM 'Lerin artık "yeşil" olarak etiketlenmesini sağlar. Sonraki işlem hattı çalıştırıldığında, uygulamalar bu VM 'lere dağıtılır.

      ![Blue-Green dağıt görevinin dağıtım grubu bölmesi](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. Dağıtımı betiği Yürüt görevi varsayılan olarak deploy.ps1 veya deploy.sh dağıtım betiğini çalıştırır. Betik, yayımlanan paketin kök klasöründe yer aldığı deployscripts klasöründedir. Seçili derleme işlem hattının dağıtımı paketin kök klasöründe yayınlayıp yayımlamadığını doğrulayın.

   ![Deployscripts klasöründe deploy.sh gösteren yapılar bölmesi](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Diğer dağıtım stratejileri

- [Sıralı dağıtım stratejisini yapılandırma](./tutorial-devops-azure-pipelines-classic.md)
- [Kanarya dağıtım stratejisini yapılandırma](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projeleri

Azure 'ı kolay bir şekilde kullanmaya başlayın. Azure DevOps Projeleri, aşağıdakileri yaparak uygulamanızı yalnızca üç adımda herhangi bir Azure hizmetinde çalıştırmaya başlayın:

- Uygulama dili
- Çalışma zamanı
- Bir Azure hizmeti

[Daha fazla bilgi edinin](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure DevOps Projeleri kullanarak Azure sanal makinelerine dağıtma](../../devops-project/azure-devops-project-vms.md)
- [Azure sanal makine ölçek kümesine uygulamanızın sürekli dağıtımını uygulama](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)