---
title: Öğretici-Azure Linux sanal makineleri için kanarya dağıtımlarını yapılandırma
description: Bu öğreticide, sürekli dağıtım (CD) işlem hattı ayarlamayı öğreneceksiniz. Bu işlem hattı, bir Azure Linux sanal makinesi grubunu kanarya dağıtım stratejisini kullanarak güncelleştirir.
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
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552516"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Öğretici-Azure Linux sanal makineleri için kanarya dağıtım stratejisini yapılandırma

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Hizmet olarak altyapı (IaaS)-CI/CD 'yi yapılandırma

Azure Pipelines, sanal makinelere dağıtımlar için tam özellikli bir CI/CD otomasyon araçları kümesi sağlar. Azure portal bir Azure VM için sürekli teslim işlem hattı yapılandırabilirsiniz.

Bu makalede, çok makineli dağıtımlar için kanarya stratejisini kullanan bir CI/CD işlem hattının nasıl ayarlanacağı gösterilmektedir. Azure portal Ayrıca, [yuvarlama](./tutorial-devops-azure-pipelines-classic.md) ve [mavi-yeşil](./tutorial-azure-devops-blue-green-strategy.md)gibi diğer stratejileri de destekler.

### <a name="configure-cicd-on-virtual-machines"></a>Sanal makinelerde CI/CD 'yi yapılandırma

Sanal makineleri bir [dağıtım grubuna](/azure/devops/pipelines/release/deployment-groups)hedefler olarak ekleyebilirsiniz. Daha sonra bunları çok makineli güncelleştirmeler için hedefleyebilirsiniz. Makinelere dağıttıktan sonra, dağıtım **geçmişini** bir dağıtım grubu içinde görüntüleyin. Bu görünüm VM 'den işlem hattına ve sonra işlemeye izlemenizi sağlar.

### <a name="canary-deployments"></a>Canary dağıtımları

Canarlı bir dağıtım, kullanıcıların küçük bir alt kümesinde yavaş değişiklik yaparak riski azaltır. Yeni sürümden güvenli bir şekilde ulaşdığınızda, altyapınızdaki daha fazla sunucuya serbest bırakabilir ve daha fazla Kullanıcı yönlendirebilirsiniz.

Sürekli teslim seçeneğini kullanarak, Azure Portal sanal makinelerinize yönelik kanarya dağıtımlarını yapılandırabilirsiniz. Adım adım izlenecek yol:

1. Azure portal oturum açın ve bir sanal makineye gidin.
1. VM ayarlarının sol bölmesinde **sürekli teslim**' yı seçin. Ardından **Yapılandır**' ı seçin.

   ![Yapılandır düğmesi ile sürekli teslim bölmesi](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Yapılandırma panelinde, mevcut bir hesabı seçmek veya yeni bir hesap oluşturmak için **Azure DevOps organizasyonu** ' nı seçin. Ardından, ardışık düzeni yapılandırmak istediğiniz projeyi seçin.  

   ![Sürekli teslim paneli](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Dağıtım grubu, fiziksel ortamları temsil eden bir dağıtım hedefi makineleri kümesidir. Geliştirme, test, UAT ve üretim örneklere örnektir. Yeni bir dağıtım grubu oluşturabilir veya var olan bir dağıtım grubu seçebilirsiniz.
1. Sanal makineye dağıtılacak paketi yayımlayan derleme işlem hattını seçin. Yayınlanan paketin, paketin kök klasöründeki deployscripts klasöründe deploy.ps1 veya deploy.sh adlı bir dağıtım betiği olmalıdır. İşlem hattı bu dağıtım betiğini çalıştırır.
1. **Dağıtım stratejisi**' nde **Canary**' ı seçin.
1. Kanarya dağıtımlarının parçası olacak VM 'lere "kanarya" etiketini ekleyin. Kanarya dağıtımı başarılı olduktan sonra gerçekleştirilen dağıtımların parçası olan sanal makinelere bir "üretim" etiketi ekleyin. Etiketler yalnızca belirli bir role sahip olan VM 'Leri hedefetmenize yardımcı olur.

   ![Dağıtım stratejisi değeri tarafından seçilen sürekli teslim paneli](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Sanal makineye dağıtılacak sürekli teslim işlem hattını yapılandırmak için **Tamam ' ı** seçin.

   ![Kanarya işlem hattı](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Sanal makine için dağıtım ayrıntıları görüntülenir. Azure DevOps 'daki yayın ardışık düzenine gitmek için bağlantıyı seçebilirsiniz. Yayın ardışık düzeninde, işlem hattı yapılandırmasını görüntülemek için **Düzenle** ' yi seçin. İşlem hattı şu üç aşamaya sahiptir:

   1. Bu aşama bir dağıtım grubu aşamasıdır. Uygulamalar, "Canary" olarak etiketlenen sanal makinelere dağıtılır.
   1. Bu aşamada, işlem hattı duraklatılır ve el ile müdahale çalışmasının çalışmayı sürdürmesini bekler.
   1. Bu bir dağıtım grubu aşaması olur. Güncelleştirme artık "üretim" olarak etiketlenen VM 'lere dağıtılır.

      ![Canary görevi dağıt için dağıtım grubu bölmesi](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. İşlem hattı çalıştırmasına devam etmeden önce en az bir VM 'nin "üretim" olarak etiketlendiğinden emin olun. İşlem hattının üçüncü aşamasında, uygulamalar yalnızca "üretim" etiketine sahip olan VM 'lere dağıtılır.

1. Dağıtımı betiği Yürüt görevi varsayılan olarak deploy.ps1 veya deploy.sh dağıtım betiğini çalıştırır. Betik, yayımlanan paketin kök klasöründe yer aldığı deployscripts klasöründedir. Seçili derleme işlem hattının dağıtımı paketin kök klasöründe yayınlayıp yayımlamadığını doğrulayın.

   ![Deployscripts klasöründe deploy.sh gösteren yapılar bölmesi](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Diğer dağıtım stratejileri
- [Sıralı dağıtım stratejisini yapılandırma](./tutorial-devops-azure-pipelines-classic.md)
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