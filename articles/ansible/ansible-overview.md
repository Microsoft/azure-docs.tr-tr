---
title: Azure ile anormal kullanma
description: Bulut sağlamayı, yapılandırma yönetimini ve uygulama dağıtımlarını otomatik hale getiren kullanımı anlara giriş.
keywords: anerişilebilir, Azure, DevOps, genel bakış, bulut sağlama, yapılandırma yönetimi, uygulama dağıtımı, anormal modüller, anerişilebilir PlayBook 'lar
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: eab2a44257c6769106124c4c9ab6711ba3be531e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155786"
---
# <a name="using-ansible-with-azure"></a>Azure ile anormal kullanma

[Anerişilebilir](https://www.ansible.com) , bulut sağlamayı, yapılandırma yönetimini ve uygulama dağıtımlarını otomatikleştiren açık kaynaklı bir üründür. Kullanılması, sanal makineler, kapsayıcılar ve ağ ve tüm bulut altyapılarını temin edebilir. Ayrıca, anormal, ortamınızdaki kaynakların dağıtımını ve yapılandırmasını otomatikleştirmenize imkan tanır.

Bu makalede, Azure ile anormal kullanmanın avantajlarından bazılarına yönelik temel bir genel bakış sunulmaktadır.

## <a name="ansible-playbooks"></a>Anerişilebilir PlayBook 'lar

[Anormal profesyonel kitaplar](https://docs.ansible.com/ansible/latest/playbooks.html) , ortamınızı yapılandırmak için size doğrudan izin verir. PlayBook 'lar, YAML kullanılarak okunabilir, böylece insanlar okunabilir olacaktır. Öğreticiler bölümü, Azure kaynaklarını yüklemek ve yapılandırmak için PlayBook 'ları kullanmanın birçok örneğini sağlar. 

## <a name="ansible-modules"></a>Anerişilebilir modüller

Anormal, doğrudan uzak konaklarda veya [PlayBook](https://docs.ansible.com/ansible/latest/playbooks.html)'lar aracılığıyla çalıştırılan bir dizi [Modül](https://docs.ansible.com/ansible/latest/modules_by_category.html) paketini içerir. Kullanıcılar kendi modüllerini oluşturabilir. Modüller, hizmetler, paketler veya dosyalar gibi sistem kaynaklarını denetlemek veya sistem komutları yürütmek için kullanılır.

Azure hizmetleriyle etkileşim kurmak için, anormal bir [bulut modülleri](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure)paketini içerir. Bu modüller, Azure 'da altyapınızı oluşturmanızı ve düzenlemenizi sağlar. 

## <a name="migrate-existing-workload-to-azure"></a>Mevcut iş yükünü Azure 'a geçirin

Altyapınızı tanımlamak için kullanmanız gerekirse, Azure 'un ortamınızı gerektiği şekilde otomatik olarak ölçeklendirmesine izin vermek için uygulamanızın PlayBook 'unu uygulayabilirsiniz. 

## <a name="automate-cloud-native-application-in-azure"></a>Azure 'da bulutta yerel uygulamayı otomatikleştirin

Anormal, Azure 'da Azure [işlevleri](https://azure.microsoft.com//services/functions/) ve Azure 'Da [Kubernetes](https://azure.microsoft.com/services/container-service/kubernetes/)gibi Azure mikro hizmetlerini kullanarak bulutta yerel uygulamaları otomatikleştirmenizi sağlar.  

## <a name="manage-deployments-with-dynamic-inventory"></a>Dinamik envanter ile dağıtımları yönetme

[Dinamik envanter](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) özelliği aracılığıyla, Azure kaynaklarından envanter çekme yeteneği sağlar. Daha sonra mevcut Azure dağıtımlarınızı etiketleyebilir ve bu etiketli dağıtımları anormal bir şekilde yönetebilirsiniz.

## <a name="additional-azure-marketplace-options"></a>Ek Azure Marketi seçenekleri

[Anerişilebilir kule](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) , Red Hat Ile bir Azure Marketi görüntüsüdür. 

Anormal Tower, aşağıdaki özelliklere sahip olan bir Web tabanlı kullanıcı arabirimi ve panodur:

* Rol tabanlı erişim denetimi, iş zamanlaması ve grafik envanteri yönetimi tanımlamanızı sağlar. 
* , Var olan araçlara ve işlemlere Tower ekleyebileceğiniz bir REST API ve CLı içerir. 
* PlayBook çalıştırmalarının gerçek zamanlı çıkışını destekler. 
* Azure ve SSH anahtarları gibi kimlik bilgilerini şifreler. böylece, kimlik bilgilerini açığa çıkmadan görevleri atayabilirsiniz.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Azure için anerişilebilir modül ve Sürüm matrisi

Anormal, Azure kaynaklarını sağlama ve yapılandırma konusunda kullanılacak bir modül paketini içerir. Bu kaynaklar sanal makineler, ölçek kümeleri, ağ hizmetleri ve kapsayıcı hizmetleri içerir. [Anormal matris](./ansible-matrix.md) , Azure için erişilebilir modülleri ve teslim ettikleri anormal sürümleri listeler.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure 'da CentOS 'a yönelik anormal çözüm şablonu dağıtma](./ansible-deploy-solution-template.md)
- [Hızlı başlangıç: Azure 'da Linux sanal makinelerini kullanarak yapılandırma](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)