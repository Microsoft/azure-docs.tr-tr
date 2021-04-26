---
title: Azure için DevOps Starter 'a genel bakış | Microsoft Docs
description: DevOps başlangıç değerini anlayın
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330657"
---
# <a name="overview-of-devops-starter"></a>DevOps Starter 'a genel bakış

 DevOps Starter, GitHub eylemlerini veya Azure DevOps 'u kullanarak Azure 'da çalışmaya başlamanızı kolaylaştırır. Yalnızca birkaç hızlı adımda Azure portal en sevdiğiniz uygulamanızı seçtiğiniz Azure hizmetinde başlatmanıza yardımcı olur. 

 DevOps Starter, uygulamanızı geliştirmek, dağıtmak ve izlemek için ihtiyaç duyduğunuz her şeyi ayarlar. Kod yürütmelerinin, derlemelerin ve dağıtımlarınızın tümünü Azure portal tek bir görünümden izlemek için DevOps başlangıç panosunu kullanabilirsiniz.

## <a name="advantages-of-using-devops-starter"></a>DevOps başlangıç kullanmanın avantajları

  DevOps Starter, dağıtımlarınızı otomatikleştirmek için aşağıdaki iki CI/CD sağlayıcısını destekler
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter, uygulamanız için tüm sürekli tümleştirme (CI) ve sürekli teslim (CD) kurulumunu Azure 'da otomatikleştirir.  Mevcut kodla başlayabilir veya belirtilen örnek uygulamalardan birini kullanabilirsiniz. Daha sonra bu uygulamayı sanal makineler, App Service, Azure Kubernetes Hizmetleri (AKS), Azure SQL veritabanı ve Azure Service Fabric gibi çeşitli Azure hizmetlerine hızlıca dağıtabilirsiniz.  

  DevOps Starter, ilk Git deposunu ayarlamayı, CI/CD işlem hattını yapılandırmayı, izleme için bir Application Insights kaynağı oluşturmayı ve Azure portal bir DevOps başlangıç panosunun oluşturulmasıyla tüm çözümün tek bir görünümünü sağlamayı içeren bir DevOps işlem hattının ilk yapılandırması için çalışır.

DevOps Starter 'ı şu şekilde kullanabilirsiniz:

* Uygulamanızı Azure’a hızlı şekilde dağıtma
* CI/CD iş akışı veya işlem hattının kurulumunu otomatikleştirin
* Bir CI/CD iş akışını veya işlem hattını doğru şekilde ayarlamayı görüntüleyin ve anlayın
* Yayın işlem hatlarını belirli senaryolarınıza göre daha fazla özelleştirme

## <a name="how-to-use-devops-starter"></a>DevOps Starter nasıl kullanılır?

  DevOps Starter Azure portal kullanılabilir. Portaldan başka bir Azure kaynağı oluşturduğunuz gibi bir DevOps başlangıç kaynağı oluşturursunuz. DevOps Projeleri çeşitli yapılandırma seçenekleri için adım adım sihirbaza benzer bir deneyim sağlar.  

İlk kurulumun parçası olarak birçok yapılandırma seçeneği arasından seçim yaparsınız. Bu seçenekler şunlardır:

* Favoring CI/CD sağlayıcınızı seçme
* Belirtilen örnek uygulamayı kullanma veya kendi kodunuzu getirme (yalnızca Azure DevOps için)
* Uygulama dili seçme
* Bir uygulama çerçevesini dile göre seçme
* Azure hizmeti (dağıtım hedefi) seçme
* GitHub veya Azure DevOps kuruluşunuzu seçin
* Azure aboneliğinizi seçme
* Azure hizmetlerinin konumunu seçme
* Azure hizmetleri için çeşitli fiyatlandırma katmanlarından seçim yapma

DevOps Starter 'nizi oluşturduktan sonra şunları yapabilirsiniz:

* GitHub iş akışınızı veya Azure DevOps işlem hattınızı özelleştirme
* Kod akışınızı yönetmek ve kalitenizi yüksek tutmak için çekme isteklerini kullanma
* Kalite çıtasını yükseltmek için kodunuzu birleştirmeden önce her bir kaydı test edip derleme

DevOps Starter 'ı kullandıktan sonra, Azure portal DevOps başlangıç panosundan tek bir yerden tüm kaynakları da silebilirsiniz.

## <a name="devops-starter-and-github-integration"></a>DevOps Başlatıcı ve GitHub tümleştirmesi

DevOps Starter artık GitHub eylemlerini bir CI/CD sağlayıcısı olarak destekliyor. GitHub 'da bir CI/CD iş akışı ayarlamak için GitHub 'da gerekli olan tüm işleri otomatikleştirir. Mevcut bir GitHub kuruluşunda GitHub deposu oluşturur ve ardından örnek bir uygulamayı yeni GitHub deposuna kaydeder.  

Otomasyon ayrıca iş akışı için bir tetikleyici oluşturur, böylece her yeni kod, iş akışı içinde bir derleme ve dağıtım işi başlatır. Uygulama, tercih ettiğiniz Azure hizmetine dağıtılır. GitHub iş akışı ek senaryolar için özelleştirilebilir. 

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Başlatıcı ve Azure DevOps tümleştirmesi

Azure DevOps kullanan DevOps Starter, bir CI/CD işlem hattı ayarlamak için Azure Pipelines gereken tüm işleri otomatikleştirir. Yeni veya mevcut bir Azure DevOps kuruluşunda git deposu oluşturur ve ardından örnek bir uygulama ya da mevcut kodunuzu yeni bir git deposuna kaydeder.  

Otomasyon Ayrıca derleme için bir CI tetikleyicisi oluşturur, böylece her yeni kod yürütmesi bir derlemeyi başlatır. DevOps Starter, bir CD tetikleyicisi oluşturur ve her yeni başarılı derlemeyi seçtiğiniz Azure hizmetine dağıtır.  

Derleme ve yayın işlem hatları, ek senaryolar için özelleştirilebilir. Ayrıca, derleme ve yayın işlem hatlarını diğer projelerde kullanmak üzere kopyalayabilirsiniz.

## <a name="getting-started-with-devops-starter"></a>DevOps Starter ile çalışmaya başlama

* [DevOps Starter ile çalışmaya başlama](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps başlangıç videoları

* [Azure DevOps Starter ile CI/CD oluşturma](https://www.youtube.com/watch?v=NuYDAs3kNV8)
