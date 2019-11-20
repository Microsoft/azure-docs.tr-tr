---
title: Jenkins ve Azure'a genel bakış
description: Jenkins 'i Azure 'da oluşturun ve dağıtın ve sürekli tümleştirme ve dağıtım (CI/CD) işlem hatlarınızı genişletmek için Azure işlem ve depolama kaynaklarını kullanın.
keywords: jenkins, azure, devops, genel bakış
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: a7ac07af4d9f3c065ce033fac3982091a0b9c679
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158750"
---
# <a name="azure-and-jenkins"></a>Azure ve Jenkins

[Jenkins](https://jenkins.io/), yazılım projelerinizde sürekli tümleştirme ve dağıtım (CI/CD) için kullanılan popüler bir kaynak otomasyonu sunucusudur. Jenkins dağıtımınızı Azure'da yapılandırabilir veya var olan Jenkins yapılandırmanızı Azure kaynaklarını kullanacak şekilde genişletebilirsiniz. Uygulamalarınız için Azure'da CI/CD süreçlerini basitleştirmek için kullanabileceğiniz Jenkins eklentileri de mevcuttur.

Jenkins kullanıcılarına sunulan temel Azure özelliklerini anlatan bu makale, Azure ile Jenkins kullanımına giriş niteliğindedir. Azure'da kendi Jenkins sunucunuzu kullanmaya başlama hakkında daha fazla bilgi edinmek için bkz. [Azure'da Jenkins sunucusu oluşturma](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Jenkins sunucularınızı Azure'da barındırma

Derleme otomasyonunuzu tek merkezden yönetmek ve yazılım projelerinizin gereksinimleri arttıkça dağıtımınızı ölçeklendirmek için Jenkins dağıtımınızı Azure'da barındırın. Jenkins'i Azure'da dağıtmak için aşağıdaki çözümleri kullanabilirsiniz:
 
- Azure Market'teki [Jenkins çözüm şablonu](install-jenkins-solution-template.md).
- [Azure sanal makineleri](/azure/virtual-machines/linux/overview). VM üzerinde Jenkins örneği oluşturma [öğreticisine](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) bakın.
- [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough) üzerinde çalışan bir Kubernetes kümesinde, ayrıntılar için [Nasıl yapılır?](/azure/container-service/kubernetes/container-service-kubernetes-jenkins) makalesine göz atın.

Azure [izleyici günlüklerini](/azure/log-analytics/log-analytics-overview) ve [Azure CLI](/cli/azure)'Yı kullanarak Azure Jenkins dağıtımınızı izleyin ve yönetin.

## <a name="scale-your-build-automation-on-demand"></a>Derleme otomasyonunuzu isteğe bağlı olarak ölçeklendirme

Derleme sayısı ile işlerinizin ve işlem hatlarınızın karmaşıklık düzeyi arttıkça Jenkins derleme kapasitenizi ölçeklendirmek için var olan Jenkins dağıtımınıza derleme aracıları ekleyebilirsiniz. Azure [VM aracıları eklentisini](jenkins-azure-vm-agents.md)kullanarak bu yapı aracılarını Azure sanal makinelerinde çalıştırabilirsiniz. Daha fazla ayrıntı için [öğreticiye](/azure/jenkins/jenkins-azure-vm-agents) bakın.

[Azure hizmet sorumlusu](/azure/azure-resource-manager/resource-group-overview) ile yapılandırılan Jenkins işleri ve işlem hatları bu kimlik bilgileriyle aşağıdaki işlemleri gerçekleştirebilir:

- [Azure Storage eklentisini](https://plugins.jenkins.io/windows-azure-storage)kullanarak [Azure Storage](/azure/storage/common/storage-introduction) 'da derleme yapıtlarını güvenli bir şekilde depolayın ve arşivleyin. Daha fazla bilgi için [Jenkins depolama nasıl yapılır?](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) öğreticisini inceleyin.
- [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline) ile Azure kaynaklarını yönetme ve yapılandırma.

## <a name="deploy-your-code-into-azure-services"></a>Kodunuzu Azure hizmetlerine dağıtma

Jenkins eklentilerini kullanarak uygulamalarınızı Jenkins CI/CD işlem hatlarıyla birlikte Azure'a dağıtabilirsiniz. Uygulamalarınızı [Azure App Service](/azure/app-service/) ve [Azure Container Service](/azure/container-service/kubernetes/) hizmetlerine dağıtarak altyapı yönetimiyle uğraşmadan uygulamalarınızı hazırlayabilir, test edebilir ve yayımlayabilirsiniz.

 Eklentileri kullanarak uygulamaları aşağıdaki hizmetlere ve ortamlara dağıtabilirsiniz:

- [Linux üzerinde Azure App Service](/azure/app-service/containers/app-service-linux-intro). Kullanmaya başlamak için [öğreticiye](java-deploy-webapp-tutorial.md) bakın.
- [Azure App Service](/azure/app-service/overview). Kullanmaya başlamak için [Nasıl yapılır?](deploy-Jenkins-app-service-plugin.md) makalesine bakın.