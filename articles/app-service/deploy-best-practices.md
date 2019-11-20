---
title: Dağıtım en iyi uygulamaları-Azure App Service | Microsoft Docs
description: Azure App Service dağıtmaya yönelik temel bileşenler hakkında bilgi edinin.
keywords: Azure App Service, Web uygulaması, dağıtma, dağıtım, işlem hatları, derleme
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: 121ea4b7e29510ef86b61350ed97ffca5d133d56
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199481"
---
# <a name="deployment-best-practices"></a>Dağıtım En Iyi uygulamaları

Her geliştirme ekibinin, her türlü bulut hizmetinde verimli bir dağıtım işlem hattı uygulamayı zorlaştırmaları için benzersiz gereksinimleri vardır. Bu makalede, App Service dağıtım kaynakları, derleme işlem hatları ve dağıtım mekanizmaları için dağıtmanın üç ana bileşeni tanıtılmaktadır. Bu makalede ayrıca belirli dil yığınları için bazı en iyi uygulamalar ve ipuçları ele alınmaktadır.

## <a name="deployment-components"></a>Dağıtım bileşenleri

### <a name="deployment-source"></a>Dağıtım kaynağı

Dağıtım kaynağı, uygulama kodunuzun konumudur. Üretim uygulamaları için dağıtım kaynağı genellikle [GitHub, BitBucket veya Azure Repos](deploy-continuous-deployment.md)gibi sürüm denetimi yazılımıyla barındırılan bir depodur. Geliştirme ve test senaryolarında, dağıtım kaynağı [Yerel makinenizde bir proje](deploy-local-git.md)olabilir. App Service [, OneDrive ve Dropbox klasörlerini](deploy-content-sync.md) de dağıtım kaynakları olarak destekler. Bulut klasörleri App Service kullanmaya başlamanızı kolaylaştırırken, genellikle bu kaynak kurumsal düzeyde üretim uygulamaları için kullanılması önerilmez. 

### <a name="build-pipeline"></a>Derleme işlem hattı

Bir dağıtım kaynağına karar verdikten sonra bir sonraki adımınız bir yapı işlem hattı seçmek olur. Derleme işlem hattı kaynak kodunuzu dağıtım kaynağından okur ve uygulamayı bir çalıştırılabilir durumda almak için bir dizi adımı (kodu derleme, HTML ve JavaScript 'i çalıştırma, testleri çalıştırma ve paketleme bileşenleri) yürütür. Yapı işlem hattı tarafından yürütülen belirli komutlar, dil yığınınıza bağlıdır. Bu işlemler Azure Pipelines gibi bir yapı sunucusunda yürütülebilir veya yerel olarak yürütülür.

### <a name="deployment-mechanism"></a>Dağıtım mekanizması

Dağıtım mekanizması, oluşturulan uygulamanızı Web uygulamanızın */Home/site/Wwwroot* dizinine koymak için kullanılan eylemdir. */Wwwroot* dizini, Web uygulamanızın tüm örnekleri tarafından paylaşılan bağlı bir depolama konumudur. Dağıtım mekanizması uygulamanızı bu dizine yerleştiriyorsa, örneklerinizin yeni dosyaları eşitlemek için bir bildirim alır. App Service aşağıdaki dağıtım mekanizmalarını destekler:

- Kudu uç noktaları: [kudu](https://github.com/projectkudu/kudu/wiki) , Windows App Service 'de ayrı bir işlem olarak ve Linux App Service ikinci bir kapsayıcı olarak çalışan açık kaynaklı geliştirici üretkenlik aracıdır. Kudu sürekli dağıtımları işler ve dağıtım için zipdeploy gibi HTTP uç noktaları sağlar.
- FTP ve WebDeploy: [sitenizi veya Kullanıcı kimlik bilgilerinizi](deploy-configure-credentials.md)kullanarak FTP veya WebDeploy [aracılığıyla](deploy-ftp.md) dosya yükleyebilirsiniz. Bu mekanizmalar kudu 'ye gitmez.  

Azure Pipelines, Jenkins ve düzenleyici eklentileri gibi dağıtım araçları bu dağıtım mekanizmalarından birini kullanır.

## <a name="language-specific-considerations"></a>Dile özgü hususlar

### <a name="java"></a>Java

JAR uygulamalarını dağıtmak için kudu [zipdeploy/](deploy-zip.md) API ve War uygulamaları için [wardeploy/](deploy-zip.md#deploy-war-file) API kullanın. Jenkins kullanıyorsanız, bu API 'Leri doğrudan dağıtım aşamasınca kullanabilirsiniz. Daha fazla bilgi için [bu makaleye](../jenkins/execute-cli-jenkins-pipeline.md) bakın.

### <a name="node"></a>Düğüm

Varsayılan olarak kudu, düğüm uygulamanız (`npm install`) için derleme adımlarını yürütür. Azure DevOps gibi bir yapı hizmeti kullanıyorsanız kudu derlemesi gereksizdir. Kudu derlemesini devre dışı bırakmak için, `SCM_DO_BUILD_DURING_DEPLOYMENT`bir uygulama ayarı oluşturun, `false`değeri.

### <a name="net"></a>.NET 

Varsayılan olarak, kudu .NET uygulamanız için derleme adımlarını yürütür (`dotnet build`). Azure DevOps gibi bir yapı hizmeti kullanıyorsanız kudu derlemesi gereksizdir. Kudu derlemesini devre dışı bırakmak için, `SCM_DO_BUILD_DURING_DEPLOYMENT`bir uygulama ayarı oluşturun, `false`değeri.

## <a name="other-deployment-considerations"></a>Diğer dağıtım konuları

### <a name="use-deployment-slots"></a>Dağıtım yuvalarını kullanma

Mümkün olduğunda, yeni bir üretim derlemesi dağıttığınızda [dağıtım yuvalarını](deploy-staging-slots.md) kullanın. Standart bir App Service planı katmanını veya daha iyi bir şekilde kullanırken, uygulamanızı bir hazırlama ortamına dağıtabilir, değişikliklerinizi doğrulayabilir ve duman testleri yapabilirsiniz. Hazırsanız, hazırlama ve üretim yuvalarınızı değiştirebilirsiniz. Değiştirme işlemi, gerekli çalışan örneklerini üretim ölçeklendirmenize uyacak şekilde çarpıtılarak kapalı kalma süresini ortadan kaldırır. 

### <a name="local-cache"></a>Yerel Önbellek

Azure App Service içerik Azure Storage 'da depolanır ve bir içerik paylaşımında dayanıklı bir biçimde ortaya çıkmış olur. Ancak, bazı uygulamaların yüksek kullanılabilirliğe sahip yalnızca yüksek performanslı, salt okunurdur bir içerik deposu olması yeterlidir. Bu uygulamalar, [Yerel önbellek](overview-local-cache.md)kullanmanın avantajlarından yararlanabilir. WordPress gibi içerik yönetimi siteleri için yerel önbellek önerilmez.

Kapalı kalma süresini engellemek için her zaman yerel önbelleği [dağıtım yuvalarıyla](deploy-staging-slots.md) birlikte kullanın. Bu özellikleri birlikte kullanma hakkında bilgi için [Bu bölüme](overview-local-cache.md#best-practices-for-using-app-service-local-cache) bakın.

### <a name="high-cpu-or-memory"></a>Yüksek CPU veya bellek

App Service planınız kullanılabilir CPU veya belleğin %90 ' den fazla kullanılıyorsa, temeldeki sanal makinede dağıtımınızı işleme sorunu olabilir. Bu durumda, dağıtımı gerçekleştirmek için örnek sayınız geçici olarak ölçeklendirin. Dağıtım tamamlandıktan sonra, örnek sayısını önceki değerine döndürebilirsiniz.
