---
title: Microsoft Azure Cloud Foundry kullanmaya başlama
description: Microsoft Azure Cloud Foundry OSS veya özetleme 'yi çalıştırın
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 447550d2f6bca570869c9fa93724e4dbaba02132
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036816"
---
# <a name="cloud-foundry-on-azure"></a>Azure’da Cloud Foundry

Cloud Foundry, çeşitli dillerde ve çerçevelerde geliştirilen 12 faktör uygulamaları oluşturmaya, dağıtmaya ve işletmeye yönelik açık kaynak bir hizmet olarak platformdur (PaaS). Bu belgede, Azure 'da Cloud Foundry çalıştırmak için sahip olduğunuz seçenekler ve nasıl başlatılacağınız açıklanmaktadır.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry teklifleri

Azure 'da çalıştırmak için kullanılabilecek iki Cloud Foundry biçimi vardır: açık kaynaklı Cloud Foundry (OSS CF) ve Özet Cloud Foundry (PCF). OSS CF, Cloud Foundry Foundation tarafından yönetilen Cloud Foundry tamamen [Açık kaynaklı](https://github.com/cloudfoundry) bir sürümüdür. Özet Cloud Foundry, özette Software Inc 'den Cloud Foundry kurumsal bir dağıtımdır. İki teklif arasındaki farklılıklara göz atacağız.

### <a name="open-source-cloud-foundry"></a>Açık kaynak Cloud Foundry

Önce bir BOSH Direktörü dağıtarak ve sonra [GitHub 'da sunulan yönergeleri](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)kullanarak Cloud Foundry Azure 'da Cloud Foundry. OSS CF kullanma hakkında daha fazla bilgi edinmek için Cloud Foundry Foundation tarafından sunulan [belgelere](https://docs.cloudfoundry.org/) bakın.

Microsoft, aşağıdaki topluluk kanalları aracılığıyla OSS CF için en iyi desteği sağlar:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>Bosh-Azure-MPE kanalı [Cloud Foundry bolluk](https://slack.cloudfoundry.org/)
- [CF-Bosh posta listesi](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- [MPE](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) ve [Hizmet Aracısı](https://github.com/Azure/meta-azure-service-broker/issues) için GitHub sorunları

>[!NOTE]
> Cloud Foundry çalıştırdığınız sanal makineler gibi Azure kaynaklarınız için destek düzeyi Azure destek anlaşmanıza dayalıdır. En iyi çaba topluluk desteği yalnızca Cloud Foundry özgü bileşenler için geçerlidir.

### <a name="pivotal-cloud-foundry"></a>Özetleme Cloud Foundry

Özet Cloud Foundry, bir özel yönetim araçları ve kurumsal destek kümesiyle birlikte, OSS dağıtımı ile aynı çekirdek platformunu içerir. Azure 'da PCF 'yi çalıştırmak için, özetten bir lisans edinmeniz gerekir. Azure Marketi 'nden PCF teklifi 90 günlük bir deneme lisansı içerir.

Araçlar, bir Cloud Foundry Foundation 'ın dağıtımını ve yönetimini kolaylaştıran bir Web uygulaması ve Kullanıcı ve uygulamaları yönetmeye yönelik bir Web uygulaması [olan özette](https://docs.pivotal.io/pivotalcf/console/) [Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/)içerir.

Yukarıdaki OSS CF için listelenen destek kanallarına ek olarak, bir PCF lisansı, destek için özette iletişim kurmanız sahibine. Microsoft ve Özette, yardım için her iki tarafın da iletişim kurabilmesini sağlayan destek iş akışlarını da etkinleştirdi ve bu da sorunun nerede olduğuna bağlı olarak, sorgunuz uygun şekilde yönlendirilsin.

## <a name="azure-service-broker"></a>Azure Hizmet Aracısı

Cloud Foundry, durum bilgisiz uygulama işlemlerinin ve durum bilgisi olan yedekleme hizmetlerinin temiz bir şekilde ayrılmasını sağlayan ["on-Factor App"](https://12factor.net/) yöntemini teşvik eder. [Hizmet aracıları](https://docs.cloudfoundry.org/services/api.html) , uygulamalara bakım sağlamak ve bu hizmetleri bağlamak için tutarlı bir yol sunar. [Azure Hizmet Aracısı](https://github.com/Azure/meta-azure-service-broker) , Azure depolama ve Azure SQL dahil olmak üzere bu kanal aracılığıyla bazı önemli Azure hizmetlerini sağlar.

Özette Cloud Foundry kullanıyorsanız, hizmet Aracısı da Özet ağdan [bir kutucuk olarak da kullanılabilir](https://docs.pivotal.io/azure-sb/installing.html) .

## <a name="related-resources"></a>İlgili kaynaklar

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services eklentisi

Cloud Foundry, sürekli tümleştirme (CI) ve sürekli teslim (CD) kullanımı da dahil olmak üzere çevik yazılım geliştirmeye uygundur. Projelerinizi yönetmek için Azure DevOps Services kullanırsanız ve bir CI/CD işlem hattı Cloud Foundry hedefleme ayarlamak istiyorsanız [Azure DevOps Services Cloud Foundry Build uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension)kullanabilirsiniz. Eklenti, Azure 'da veya başka bir ortamda çalışıp çalışmadığını Cloud Foundry dağıtımlarını yapılandırmayı ve otomatik hale getirmeyi basitleştirir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Marketi 'nden özetleme Cloud Foundry dağıtma](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Azure 'da Cloud Foundry uygulama dağıtma](./cloudfoundry-deploy-your-first-app.md)
