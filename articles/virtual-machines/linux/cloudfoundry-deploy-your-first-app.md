---
title: Microsoft Azure Cloud Foundry için ilk uygulamanızı dağıtın
description: Azure 'da Cloud Foundry uygulama dağıtma
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: b1f9ab5289a41aacb5514e954f1ca01f6ad66152
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036837"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Microsoft Azure Cloud Foundry için ilk uygulamanızı dağıtın

[Cloud Foundry](https://cloudfoundry.org) , Microsoft Azure sunulan popüler bir açık kaynaklı uygulama platformudur. Bu makalede, bir Azure ortamında Cloud Foundry bir uygulamanın nasıl dağıtılacağını ve yönetileceğini göstereceğiz.

## <a name="create-a-cloud-foundry-environment"></a>Cloud Foundry ortamı oluşturma

Azure 'da Cloud Foundry ortamı oluşturmak için birkaç seçenek vardır:

- PCF Ops Manager ve Azure Hizmet Aracısı içeren standart bir ortam oluşturmak için Azure Marketi 'ndeki [özetleme Cloud Foundry teklifini][pcf-azuremarketplace] kullanın. Market Teklifini dağıtmaya yönelik [tüm yönergeleri][pcf-azuremarketplace-pivotaldocs] Özet belgelerinde bulabilirsiniz.
- [Özetleme Cloud Foundry El ile dağıtarak][pcf-custom]özelleştirilmiş bir ortam oluşturun.
- Cloud Foundry ortamının dağıtımını koordine eden bir VM olan [Bosh](https://bosh.io) Direktörü ayarlayarak [Açık kaynaklı Cloud Foundry paketlerini doğrudan dağıtın][oss-cf-bosh] .

> [!IMPORTANT] 
> Azure Marketi 'nden PCF dağıtıyorsanız, her ikisi de Market dağıtım kılavuzu 'nda açıklanan özetleme uygulama Yöneticisi 'ne erişmek için SYSTEMDOMAINURL 'sini ve yönetici kimlik bilgilerini bir yere getirin. Bu öğreticiyi tamamlaması gerekir. Market dağıtımları için SYSTEMDOMAINURL 'SI https://systembiçimindedir. *IP adresi*. CF.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Bulut denetleyicisine bağlanma

Bulut denetleyicisi, uygulamaları dağıtmak ve yönetmek için bir Cloud Foundry ortamına yönelik birincil giriş noktasıdır. Çekirdek bulut denetleyicisi API 'SI (CCAPı) bir REST API, ancak çeşitli araçlar aracılığıyla erişilebilir. Bu durumda, [Cloud Foundry CLI][cf-cli]aracılığıyla etkileşime geçeceğiz. CLı 'yi Linux, MacOS veya Windows 'a yükleyebilirsiniz ancak onu hiç yüklememeyi tercih ediyorsanız, [Azure Cloud Shell][cloudshell-docs]önceden yüklenmiş olarak bulunur.

Oturum açmak için, Market dağıtımından edindiğiniz SYSTEMDOMAINURL 'sine `api` ekleyin. Varsayılan dağıtım otomatik olarak imzalanan bir sertifika kullandığından, `skip-ssl-validation` anahtarını da eklemeniz gerekir.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Bulut denetleyicisinde oturum açmanız istenir. Market dağıtım adımlarından edindiğiniz yönetici hesabı kimlik bilgilerini kullanın.

Cloud Foundry, paylaşılan bir dağıtım içindeki takımları ve ortamları yalıtmak için ad alanları olarak *kuruluşlar* ve *boşluklar* sağlar. PCF Market dağıtımı, varsayılan *sistem* org 'ı ve otomatik ölçeklendirme hizmeti ve Azure Hizmet Aracısı gibi temel bileşenleri içerecek şekilde oluşturulan bir boşluk kümesini içerir. Şimdilik *sistem* alanını seçin.


## <a name="create-an-org-and-space"></a>Kuruluş ve alan oluşturma

`cf apps`yazarsanız, sistem kuruluşu içindeki Sistem alanına dağıtılan bir sistem uygulamaları kümesi görürsünüz. 

*System org 'ı* sistem uygulamaları için ayrılmış tutmanız gerekir, bu nedenle örnek uygulamamızı barındırmak için bir kuruluş ve alan oluşturun.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Yeni kuruluş ve alana geçiş yapmak için target komutunu kullanın:

```bash
cf target -o testorg -s dev
```

Artık, bir uygulamayı dağıtırken yeni kuruluş ve alanda otomatik olarak oluşturulur. Yeni kuruluş/alan üzerinde şu anda hiçbir uygulama olmadığını doğrulamak için `cf apps` yeniden yazın.

> [!NOTE] 
> Org 'ler ve boşluklar ve rol tabanlı erişim denetimi (RBAC) için nasıl kullanılabilecekleri hakkında daha fazla bilgi için [Cloud Foundry belgelerine][cf-orgs-spaces-docs]bakın.

## <a name="deploy-an-application"></a>Uygulama dağıtma

Daha sonra, Java 'da yazılmış ve [Spring Framework](https://spring.io) ve [Spring Boot](https://projects.spring.io/spring-boot/)temel alınarak, Hello Spring Cloud adlı bir örnek Cloud Foundry uygulaması kullanalım.

### <a name="clone-the-hello-spring-cloud-repository"></a>Merhaba yay bulutu deposunu Kopyala

Merhaba yay bulutu örnek uygulaması GitHub ' da kullanılabilir. Ortamınıza kopyalayın ve yeni dizine değiştirin:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Uygulama oluşturma

[Apache Maven](https://maven.apache.org)kullanarak uygulamayı oluşturun.

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Uygulamayı CF Push ile dağıtma

`push` komutunu kullanarak Cloud Foundry çoğu uygulamayı dağıtabilirsiniz:

```bash
cf push
```

*Bir uygulamayı gönderdiğinizde,* Cloud Foundry uygulamanın türünü (Bu durumda, bir Java uygulaması) algılar ve bağımlılıklarını tanımlar (Bu durumda Spring Framework). Ardından, kodunuzu bir *damlacık*olarak bilinen tek başına kapsayıcı görüntüsünde çalıştırmak için gereken her şeyi paketler. Son olarak, Cloud Foundry ortamınızdaki kullanılabilir makinelerden birindeki uygulamayı zamanlar ve bu, komutun çıkışında kullanılabilen bir URL oluşturur.

![CF Push komutundan çıkış][cf-push-output]

Merhaba-yay-bulut uygulamasını görmek için, tarayıcıda belirtilen URL 'YI açın:

![Merhaba yay bulutu için varsayılan kullanıcı arabirimi][hello-spring-cloud-basic]

> [!NOTE] 
> `cf push`sırasında ne olacağı hakkında daha fazla bilgi için, bkz. uygulamalar Cloud Foundry belgelerde [nasıl hazırlanır][cf-push-docs] .

## <a name="view-application-logs"></a>Uygulama günlüklerini görüntüle

Bir uygulamanın günlüklerini adına göre görüntülemek için Cloud Foundry CLı kullanabilirsiniz:

```bash
cf logs hello-spring-cloud
```

Varsayılan olarak, Logs komutu yazıldığında yeni Günlükler gösteren *tail*kullanır. Yeni günlükleri görmek için tarayıcıda Merhaba-Spring-Cloud uygulamasını yenileyin.

Zaten yazılmış olan günlükleri görüntülemek için `recent` anahtarını ekleyin:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Uygulamayı ölçeklendirme

Varsayılan olarak `cf push` yalnızca uygulamanızın tek bir örneğini oluşturur. Yüksek kullanılabilirlik sağlamak ve daha yüksek aktarım hızı için ölçeklendirmeyi etkinleştirmek üzere, genellikle uygulamalarınızın birden fazla örneğini çalıştırmak istersiniz. `scale` komutunu kullanarak zaten dağıtılmış uygulamaları kolayca ölçeklendirebilirsiniz:

```bash
cf scale -i 2 hello-spring-cloud
```

Uygulamada `cf app` komutunun çalıştırılması, Cloud Foundry uygulamanın başka bir örneğini oluşturuyor olduğunu gösterir. Uygulama başladıktan sonra, Cloud Foundry Yük Dengeleme trafiğini otomatik olarak başlatır.


## <a name="next-steps"></a>Sonraki adımlar

- [Cloud Foundry belgelerini okuyun][cloudfoundry-docs]
- [Cloud Foundry için Azure DevOps Services eklentisini ayarlama][vsts-plugin]
- [Cloud Foundry için Microsoft Log Analytics kafa 'yi yapılandırma][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
