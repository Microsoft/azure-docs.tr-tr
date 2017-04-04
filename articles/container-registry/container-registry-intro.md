---
title: "Azure’da özel Docker kapsayıcısı kayıt defterleri | Microsoft Docs"
description: "Bulut tabanlı, yönetilen, özel Docker kayıt defterleri sağlayan Azure Container Kayıt Defteri hizmetine giriş."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: dd504c95e22d322707c55818815b09d8a36c7ca4
ms.lasthandoff: 03/24/2017

---
# <a name="introduction-to-private-docker-container-registries"></a>Özel Docker kapsayıcısı kayıt defterlerine giriş


Azure Container Kayıt Defteri, açık kaynak Docker Registry 2.0’ı temel alan bir yönetilen [Docker kayıt defteri](https://docs.docker.com/registry/) hizmetidir. Özel [Docker kapsayıcısı](https://www.docker.com/what-docker) görüntülerinizi depolamak ve yönetmek için Azure kapsayıcısı kayıt defterleri oluşturun ve bunların bakımını yapın. Azure’da mevcut kapsayıcı geliştirme ve dağıtım işlem hatlarınızla kapsayıcı kayıt defterleri kullanın ve Docker topluluğunun uzmanlığından yararlanın.

Docker ve kapsayıcılarla ilgili arka plan bilgileri için bkz.

* [Docker kullanıcı kılavuzu](https://docs.docker.com/engine/userguide/)




## <a name="use-cases"></a>Uygulama alanları
Azure kapsayıcısı kayıt defterinden çeşitli dağıtım hedeflerine görüntü çekme:

* [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) ve [Kubernetes](http://kubernetes.io/docs/) dahil olmak üzere konak kümeleri arasında kapsayıcı haline getirilmiş uygulamaları yöneten **ölçeklenebilir düzenleme sistemleri** .
* [Container Service](../container-service/index.md), [App Service](/app-service/index.md), [Batch](../batch/index.md) ve [Service Fabric](../service-fabric/index.md) dahil olmak üzere uygun ölçekte uygulama oluşturulmasını ve çalıştırılmasını destekleyen **Azure hizmetleri**.

Geliştiriciler bir kapsayıcı geliştirme iş akışı kapsamında bir kapsayıcı kayıt defterine de öğe itebilir. Örneğin, [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) veya [Jenkins](https://jenkins.io/) gibi bir sürekli tümleştirme ve dağıtım aracından bir kapsayıcı kayıt defteri hedeflenebilir.





## <a name="key-concepts"></a>Önemli kavramlar
* **Kayıt Defteri** - Azure aboneliğinizde bir veya daha fazla kapsayıcı kayıt defteri oluşturun. Her kayıt defteri aynı konumdaki standart bir Azure [depolama hesabı](../storage/storage-introduction.md) tarafından desteklenir. Kapsayıcı görüntülerinizin yerel, kapalı bir ağda depolanmasının avantajlarından yararlanmak için dağıtımlarınızla aynı Azure konumunda bir kayıt defteri oluşturun.

  Kayıt defterleri, abonenin [Azure Active Directory kiracısına](../active-directory/active-directory-howto-tenant.md) bağlı olarak bir kök etki alanında adlandırılır. Örneğin, Contoso etki alanında kurumsal bir hesabınız varsa tam kayıt defteri adınız `myregistry-contoso.azurecr.io` biçiminde olur.

  Azure Active Directory destekli bir [hizmet sorumlusunu](../active-directory/active-directory-application-objects.md) veya sağlanan bir yönetici hesabını kullanarak kapsayıcı kayıt defterine [erişimi denetlersiniz](container-registry-authentication.md). Bir kayıt defteriyle kimlik doğrulamak için standart `docker login` komutunu çalıştırın.

* **Depo** -Bir kayıt defteri, kapsayıcı görüntüleri grubu olan bir veya daha çok depo içerir. Azure Container Kayıt Defteri, çok düzeyli depo ad alanlarını destekler. Bu özellik, belirli bir uygulamayla veya uygulama koleksiyonuyla ilişkili görüntü koleksiyonlarını belirli geliştirme veya işlem ekipleri halinde gruplandırmanıza imkan tanır. Örneğin:

  * `myregistry.azurecr.io/aspnetcore:1.0.1`, kurum çapında bir görüntüyü temsil eder
  * `myregistry.azurecr.io/warrantydept/dotnet-build`, garanti departmanı genelinde paylaşılan .NET uygulamaları oluşturmak için kullanılan bir görüntüyü temsil eder
  * `myregistry.azrecr.io/warrantydept/customersubmissions/web`, garanti departmanına ait olan müşteri gönderileri uygulamasında gruplandırılan bir web görüntüsünü temsil eder

* **Görüntü** - Bir depoda depolanan görüntülerin her biri, bir Docker kapsayıcısının salt okunur anlık görüntüsüdür. Azure kapsayıcısı kayıt defterleri hem Windows hem de Linux görüntüleri içerebilir. Tüm kapsayıcı dağıtımlarınız için görüntü adlarını siz denetlersiniz. Bir depoya görüntü itmek ya da bir depodan görüntü çekmek için standart [Docker komutlarını](https://docs.docker.com/engine/reference/commandline/) kullanın.

* **Kapsayıcı** - Kapsayıcı, bir yazılım uygulamasını ve uygulamanın bağımlılıklarını kod, çalışma zamanı, sistem araçları ve kitaplıkları içeren eksiksiz bir dosya sistemi şeklinde sarmalanmış bir halde tanımlar. Docker kapsayıcılarını bir kapsayıcı kayıt defterinden çektiğiniz Windows veya Linux görüntülerine bağlı olarak çalıştırın. Tek bir makinede çalışan kapsayıcılar işletim sistemi çekirdeğini paylaşır. Docker kapsayıcıları tüm büyük Linux dağıtımlarına, Mac’e ve Windows’a tümüyle taşınabilir.




## <a name="next-steps"></a>Sonraki adımlar
* [Azure portalını kullanarak kapsayıcı kayıt defteri oluşturma](container-registry-get-started-portal.md)
* [Azure CLI’yı kullanarak kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md)
* [Docker CLI’yı kullanarak ilk görüntünüzü itme](container-registry-get-started-docker-cli.md)
* Visual Studio Team Services, Azure Container Service ve Azure Container Kayıt Defteri’ni kullanarak sürekli tümleştirme ve dağıtım iş akışı oluşturmak için [bu öğreticiye](../container-service/container-service-setup-ci-cd.md) bakın.
* Azure’da kendi Docker özel kayıt defterinizi ayarlamak (genel bir uç noktası olmadan) istiyorsanız bkz. [Azure’da Kendi Özel Docker Kayıt Defterinizi Dağıtma](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md).

