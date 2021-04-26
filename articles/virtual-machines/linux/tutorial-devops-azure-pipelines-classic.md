---
title: Öğretici-Azure Linux sanal makineleri için sıralı dağıtımları yapılandırma
description: Bu öğreticide, sürekli dağıtım (CD) işlem hattı ayarlamayı öğreneceksiniz. Bu işlem hattı, sıralı dağıtım stratejisini kullanarak bir Azure Linux sanal makinesi grubunu artımlı olarak güncelleştirir.
author: moala
manager: jpconnock
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
ms.openlocfilehash: dd47250989be5c31d5f0ade2b602b9d6af535d83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564008"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Öğretici-Azure Linux sanal makineleri için sıralı dağıtım stratejisini yapılandırma

Azure DevOps, her türlü Azure kaynağı için DevOps işleminin her bölümünü otomatikleştiren yerleşik bir Azure hizmetidir. Uygulamanızda sanal makineler, Web Apps, Kubernetes veya başka herhangi bir kaynak kullanılıyorsa, Azure ve Azure DevOps ile kod olarak altyapı (IAC), sürekli tümleştirme, sürekli test, sürekli teslim ve sürekli izleme uygulayabilirsiniz.

![Hizmetler altında seçili Azure DevOps ile Azure portal](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Hizmet olarak altyapı (IaaS)-CI/CD 'yi yapılandırma

Azure Pipelines, sanal makinelere dağıtımlar için tam özellikli bir CI/CD otomasyon araçları kümesi sağlar. Azure portal bir Azure VM için sürekli teslim işlem hattı yapılandırabilirsiniz.

Bu makalede, Azure portal çok makineli dağıtımlar için bir CI/CD işlem hattının nasıl ayarlanacağı gösterilmektedir. Azure Portal Ayrıca [kanarya](./tutorial-azure-devops-canary-strategy.md) ve [mavi-yeşil](./tutorial-azure-devops-blue-green-strategy.md)gibi diğer stratejileri de destekler.

### <a name="configure-cicd-on-virtual-machines"></a>Sanal makinelerde CI/CD 'yi yapılandırma

Sanal makineleri bir [dağıtım grubuna](/azure/devops/pipelines/release/deployment-groups)hedefler olarak ekleyebilirsiniz. Daha sonra bunları çok makineli güncelleştirmeler için hedefleyebilirsiniz. Makinelere dağıttıktan sonra, dağıtım **geçmişini** bir dağıtım grubu içinde görüntüleyin. Bu görünüm VM 'den işlem hattına ve sonra işlemeye izlemenizi sağlar.

### <a name="rolling-deployments"></a>Sıralı dağıtımlar

Her yinelemede, sıralı dağıtım, uygulamanın önceki sürümünün örneklerinin yerini alır. Bunları, sabit bir makine kümesindeki (sıralı küme) yeni sürümün örnekleriyle değiştirir. Aşağıdaki izlenecek yol, sanal makinelere yapılan bir sıralı güncelleştirmenin nasıl yapılandırılacağını gösterir.

Sürekli teslim seçeneğini kullanarak, Azure portal içindeki sanal makinelerinize yönelik güncelleştirmeleri yapılandırabilirsiniz. Adım adım izlenecek yol:

1. Azure portal oturum açın ve bir sanal makineye gidin.
1. VM ayarlarının sol bölmesinde **sürekli teslim**' yı seçin. Ardından **Yapılandır**' ı seçin.

   ![Yapılandır düğmesi ile sürekli teslim bölmesi](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Yapılandırma panelinde, mevcut bir hesabı seçmek veya yeni bir hesap oluşturmak için **Azure DevOps organizasyonu** ' nı seçin. Ardından, ardışık düzeni yapılandırmak istediğiniz projeyi seçin.  

   ![Sürekli teslim paneli](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Dağıtım grubu, fiziksel ortamları temsil eden bir dağıtım hedefi makineleri kümesidir. Geliştirme, test, UAT ve üretim örneklere örnektir. Yeni bir dağıtım grubu oluşturabilir veya var olan bir dağıtım grubu seçebilirsiniz.
1. Sanal makineye dağıtılacak paketi yayımlayan derleme işlem hattını seçin. Yayınlanan paketin, paketin kök klasöründeki deployscripts klasöründe deploy.ps1 veya deploy.sh adlı bir dağıtım betiği olmalıdır. İşlem hattı bu dağıtım betiğini çalıştırır.
1. **Dağıtım stratejisi**' nde, sıralı ' **ı seçin.**
1. İsteğe bağlı olarak, her bir makineyi rolüyle etiketleyebilir. "Web" ve "DB" etiketleri örnek olarak verilebilir. Bu etiketler yalnızca belirli bir rolü olan VM 'Leri hedefetmenize yardımcı olur.
1. Sürekli teslim işlem hattını yapılandırmak için **Tamam ' ı** seçin.
1. Yapılandırma bittikten sonra, sanal makineye dağıtmak için yapılandırılmış bir sürekli teslim işlem hattı vardır.  

   ![Dağıtım geçmişini gösteren sürekli teslim paneli](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Sanal makine için dağıtım ayrıntıları görüntülenir. İşlem hattına gitmek için bağlantıyı, dağıtımı görüntülemek için **-1** ' i veya yayın işlem hattı tanımını değiştirmek için **Düzenle** ' yi seçebilirsiniz.

1. Birden çok VM yapılandırıyorsanız, dağıtım grubuna eklenecek diğer VM 'Ler için 2 ile 4 arasındaki adımları tekrarlayın. Zaten bir işlem hattı çalıştırması olan bir dağıtım grubu seçerseniz, VM 'Ler yalnızca dağıtım grubuna eklenir. Yeni işlem hattı oluşturulmaz.
1. Yapılandırma yapıldıktan sonra, işlem hattı tanımını seçin, Azure DevOps kuruluşuna gidin ve yayın işlem hattı için **Düzenle** ' yi seçin.

   ![Sıralı ardışık düzeni Düzenle](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. **Geliştirme** aşamasında **1 iş, 1 görev** seçin. **Dağıtım** aşamasını seçin.

   ![Dağıt görevi seçiliyken ardışık düzen görevleri](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. En sağdaki yapılandırma bölmesinden, her yinelemede paralel olarak dağıtmak istediğiniz makine sayısını belirtebilirsiniz. Aynı anda birden çok makineye dağıtmak istiyorsanız, kaydırıcıyı kullanarak makine sayısını yüzde olarak belirtebilirsiniz.  

1. Dağıtımı betiği Yürüt görevi varsayılan olarak deploy.ps1 veya deploy.sh dağıtım betiğini yürütür. Betik, yayımlanan paketin kök klasöründe yer aldığı deployscripts klasöründedir.

   ![Deployscripts klasöründe deploy.sh gösteren yapılar bölmesi](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Diğer dağıtım stratejileri

- [Kanarya dağıtım stratejisini yapılandırma](./tutorial-azure-devops-canary-strategy.md)
- [Mavi yeşil dağıtım stratejisini yapılandırma](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projeleri

Azure 'ı kolay bir şekilde kullanmaya başlayın. Azure DevOps Projeleri, aşağıdakileri yaparak uygulamanızı yalnızca üç adımda herhangi bir Azure hizmetinde çalıştırmaya başlayın:

- Uygulama dili
- Çalışma zamanı
- Bir Azure hizmeti
 
[Daha fazla bilgi edinin](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>Ek kaynaklar

- [Azure DevOps Projeleri kullanarak Azure sanal makinelerine dağıtma](../../devops-project/azure-devops-project-vms.md)
- [Azure sanal makine ölçek kümesine uygulamanızın sürekli dağıtımını uygulama](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)