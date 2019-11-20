---
title: Azure DevOps Projeleri genel bakış | Microsoft Docs
description: Azure DevOps Projeleri değerini anlayın
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
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: d39702f8e30046dd3cf634fc67ed7095471cd629
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971484"
---
# <a name="overview-of-azure-devops-projects"></a>Azure DevOps Projeleri genel bakış

 Azure DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. Yalnızca birkaç hızlı adımda Azure portal en sevdiğiniz uygulamanızı seçtiğiniz Azure hizmetinde başlatmanıza yardımcı olur. 

 DevOps Projeleri uygulamanızı geliştirmek, dağıtmak ve izlemek için ihtiyacınız olan her şeyi ayarlar. Kod yürütmelerinin, derlemelerin ve dağıtımlarınızın tümünü Azure portal tek bir görünümden izlemek için DevOps Projeleri panosunu kullanabilirsiniz.

## <a name="why-should-i-use-devops-projects"></a>Neden DevOps Projeleri kullanmalıyım?

  DevOps projesi, tüm sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattının kurulumunu Azure 'a otomatikleştirir.  Mevcut kodla başlayabilir veya belirtilen örnek uygulamalardan birini kullanabilirsiniz. Daha sonra bu uygulamayı sanal makineler, App Service, Azure Kubernetes Hizmetleri (AKS), Azure SQL veritabanı ve Azure Service Fabric gibi çeşitli Azure hizmetlerine hızlıca dağıtabilirsiniz.  

  DevOps Projeleri, ilk Git deposunu ayarlamaktan, CI/CD işlem hattını yapılandırmaya, izleme için bir Application Insights kaynağı oluşturmaya ve tek bir Azure portal bir DevOps Projeleri panosunun oluşturulmasıyla tüm çözümün görünümü.

DevOps Projeleri şunları yapmak için kullanabilirsiniz:

* Uygulamanızı Azure’a hızlı şekilde dağıtma
* CI/CD işlem hattının kurulumunu otomatikleştirin
* Bir CI/CD işlem hattının düzgün bir şekilde nasıl ayarlanacağını görüntüleme ve anlama
* Yayın işlem hatlarını belirli senaryolarınıza göre daha fazla özelleştirme

## <a name="how-do-i-use-devops-projects"></a>Nasıl yaparım? DevOps Projeleri kullanılsın mı?

  DevOps Projeleri Azure portal kullanılabilir. Portaldan başka bir Azure kaynağı oluşturduğunuz gibi bir DevOps Projeleri kaynağı oluşturursunuz. DevOps Projeleri çeşitli yapılandırma seçenekleri için adım adım sihirbaza benzer bir deneyim sağlar.  

İlk kurulumun parçası olarak birçok yapılandırma seçeneği arasından seçim yaparsınız. Bu seçenekler şunlardır:

* Belirtilen örnek uygulamayı kullanma veya kendi kodunuzu getirme
* Uygulama dili seçme
* Bir uygulama çerçevesini dile göre seçme
* Azure hizmeti (dağıtım hedefi) seçme
* Yeni bir Azure DevOps organizasyonu oluşturma veya mevcut bir kuruluşu kullanma 
* Azure aboneliğinizi seçme
* Azure hizmetlerinin konumunu seçme
* Azure hizmetleri için çeşitli fiyatlandırma katmanlarından seçim yapma

DevOps Projeleri kullandıktan sonra, tüm kaynakları Azure portal DevOps Projeleri panosundan tek bir yerden da silebilirsiniz.

## <a name="devops-projects-and-azure-devops-integration"></a>DevOps Projeleri ve Azure DevOps tümleştirmesi

DevOps Projeleri Azure DevOps tarafından desteklenir. DevOps Projeleri bir CI/CD işlem hattı ayarlamak için Azure Pipelines gereken tüm işleri otomatikleştirir. Yeni veya mevcut bir Azure DevOps kuruluşunda git deposu oluşturur ve ardından örnek bir uygulama ya da mevcut kodunuzu yeni bir git deposuna kaydeder.  

Otomasyon Ayrıca derleme için bir CI tetikleyicisi oluşturur, böylece her yeni kod yürütmesi bir derlemeyi başlatır. DevOps Projeleri bir CD tetikleyicisi oluşturur ve her yeni başarılı derlemeyi seçtiğiniz Azure hizmetine dağıtır.  

Derleme ve yayın işlem hatları, ek senaryolar için özelleştirilebilir. Ayrıca, derleme ve yayın işlem hatlarını diğer projelerde kullanmak üzere kopyalayabilirsiniz.

DevOps Projenizi oluşturduktan sonra aşağıdakileri yapabilirsiniz:

* Derleme ve yayın işlem hattınızı özelleştirme
* Kod akışınızı yönetmek ve kalitenizi yüksek tutmak için çekme isteklerini kullanma
* Kalite çıtasını yükseltmek için kodunuzu birleştirmeden önce her bir kaydı test edip derleme
* Kapsamınızı ve sorunlarınızı doğrudan uygulamanızla birlikte izleyin

## <a name="how-do-i-start-using-devops-projects"></a>Nasıl yaparım? DevOps Projeleri kullanmaya başlamak istiyor musunuz?

* [DevOps Projeleri kullanmaya başlayın](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>DevOps Projeleri videoları

* [Azure DevOps Projeleri ile CI/CD oluşturma](https://www.youtube.com/watch?v=NuYDAs3kNV8)
