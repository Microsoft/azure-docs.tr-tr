---
title: Microsoft Azure Cloud Foundry kullanmaya başlama
description: Microsoft Azure Cloud Foundry OSS veya özetleme 'yi çalıştırın
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 2f80f07cad0e82ee35fe71223e1282ea24f791bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530919"
---
# <a name="cloud-foundry-on-azure"></a>Azure’da Cloud Foundry

Cloud Foundry, çeşitli dillerde ve çerçevelerde geliştirilen 12 faktör uygulamaları oluşturmaya, dağıtmaya ve işletmeye yönelik açık kaynak bir hizmet olarak platformdur (PaaS). Bu belgede, Azure 'da Cloud Foundry çalıştırmak için sahip olduğunuz seçenekler ve nasıl başlatılacağınız açıklanmaktadır.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry teklifleri

Azure 'da çalıştırmak için kullanılabilecek iki Cloud Foundry biçimi vardır: açık kaynaklı Cloud Foundry (OSS CF) ve Özet Cloud Foundry (PCF). OSS CF, Cloud Foundry Foundation tarafından yönetilen Cloud Foundry tamamen [Açık kaynaklı](https://github.com/cloudfoundry) bir sürümüdür. Özet Cloud Foundry, özette Software Inc 'den Cloud Foundry kurumsal bir dağıtımdır. İki teklif arasındaki farklılıklara göz atacağız.

### <a name="open-source-cloud-foundry"></a>Açık kaynak Cloud Foundry

Önce bir BOSH Direktörü dağıtarak ve sonra [GitHub 'da sunulan yönergeleri](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)kullanarak Cloud Foundry Azure 'da Cloud Foundry. OSS CF kullanma hakkında daha fazla bilgi edinmek için Cloud Foundry Foundation tarafından sunulan [belgelere](https://docs.cloudfoundry.org/) bakın.

Microsoft, aşağıdaki topluluk kanalları aracılığıyla OSS CF için en iyi desteği sağlar:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>Bosh-Azure-MPE kanalı [Cloud Foundry bolluk](https://slack.cloudfoundry.org/)
- [CF-Bosh posta listesi](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- [MPE](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) ve [Hizmet Aracısı](https://github.com/Azure/meta-azure-service-broker/issues) için GitHub sorunları

>[!NOTE]
> Cloud Foundry çalıştırdığınız sanal makineler gibi Azure kaynaklarınız için destek düzeyi Azure destek anlaşmanıza dayalıdır. En iyi çaba topluluk desteği yalnızca Cloud Foundry özgü bileşenler için geçerlidir.

### <a name="pivotal-cloud-foundry"></a>Özetleme Cloud Foundry

Özet Cloud Foundry, bir özel yönetim araçları ve kurumsal destek kümesiyle birlikte, OSS dağıtımı ile aynı çekirdek platformunu içerir. Azure 'da PCF 'yi çalıştırmak için, özetten bir lisans edinmeniz gerekir. Azure Marketi 'nden PCF teklifi 90 günlük bir deneme lisansı içerir.

Araçlar, bir Cloud Foundry Foundation 'ın dağıtımını ve yönetimini kolaylaştıran bir Web uygulaması ve Kullanıcı ve uygulamaları yönetmeye yönelik bir Web uygulaması [olan özette](https://docs.pivotal.io/pivotalcf/console/) [Operations Manager](https://docs.pivotal.io/ops-manager/2-10/install/)içerir.

Yukarıdaki OSS CF için listelenen destek kanallarına ek olarak, bir PCF lisansı, destek için özette iletişim kurmanız sahibine. Microsoft ve Özette, yardım için her iki tarafın da iletişim kurabilmesini sağlayan destek iş akışlarını da etkinleştirdi ve bu da sorunun nerede olduğuna bağlı olarak, sorgunuz uygun şekilde yönlendirilsin.

## <a name="azure-service-broker"></a>Azure Hizmet Aracısı

Cloud Foundry, durum bilgisiz uygulama işlemlerinin ve durum bilgisi olan yedekleme hizmetlerinin temiz bir şekilde ayrılmasını sağlayan ["on-Factor App"](https://12factor.net/) yöntemini teşvik eder. [Hizmet aracıları](https://docs.cloudfoundry.org/services/api.html) , uygulamalara bakım sağlamak ve bu hizmetleri bağlamak için tutarlı bir yol sunar. [Azure Hizmet Aracısı](https://github.com/Azure/meta-azure-service-broker) , Azure depolama ve Azure SQL dahil olmak üzere bu kanal aracılığıyla bazı önemli Azure hizmetlerini sağlar.

Özette Cloud Foundry kullanıyorsanız, hizmet Aracısı da Özet ağdan [bir kutucuk olarak da kullanılabilir](https://docs.pivotal.io/azure-sb/installing.html) .

## <a name="related-resources"></a>İlgili kaynaklar

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services eklentisi

Cloud Foundry, sürekli tümleştirme (CI) ve sürekli teslim (CD) kullanımı da dahil olmak üzere çevik yazılım geliştirmeye uygundur. Projelerinizi yönetmek için Azure DevOps Services kullanırsanız ve bir CI/CD işlem hattı Cloud Foundry hedefleme ayarlamak istiyorsanız [Azure DevOps Services Cloud Foundry Build uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension)kullanabilirsiniz. Eklenti, Azure 'da veya başka bir ortamda çalışıp çalışmadığını Cloud Foundry dağıtımlarını yapılandırmayı ve otomatik hale getirmeyi basitleştirir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Marketi 'nden özetleme Cloud Foundry dağıtma](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Azure 'da Cloud Foundry uygulama dağıtma](./cloudfoundry-deploy-your-first-app.md)
