---
title: Hızlı Başlangıç - CentOS üzerinde WildFly
description: CentOS sanal makinesinde Yavaya Java uygulamaları dağıtın
author: Theresa-Nguyen
ms.author: bicnguy
ms.date: 10/23/2020
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: bad9331a6d9a0c0936b1458e3aa4a84559e4a107
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533560"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Hızlı başlangıç: CentOS 8 ' de

Bu hızlı başlangıçta, CentOS 8 VM 'sinin tek başına düğümünü nasıl dağıtacağınız gösterilmektedir. Azure 'da Kurumsal Java uygulamalarının geliştirilmesi ve test edilmesi için idealdir. Bu hızlı başlangıcı dağıtmak için uygulama sunucusu aboneliği gerekli değildir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. Azure aboneliğiniz yoksa, [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Kullanım örneği

Yavalara, Azure 'da Kurumsal Java uygulamalarının geliştirilmesi ve test edilmesi için idealdir. Açık bir şekilde 18 sunucu yapılandırma profillerinde bulunan teknolojilerin listesi, [Yavaya Başlarken kılavuzunda](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)bulunabilir.

Yavaya kullanım örneği başına tek başına veya küme modunda bir şekilde kullanabilirsiniz. Önemli Jakarta, bir düğüm kümesi ile yüksek düzeyde kullanılabilirlik sağlayabilirsiniz. Az sayıda uygulama yapılandırma değişikliği yapıp uygulamayı kümede dağıtın. Bu konuda daha fazla bilgi edinmek için lütfen [yüksek kullanılabilirlik Kılavuzu](https://docs.wildfly.org/18/High_Availability_Guide.html)' na bakın.

## <a name="configuration-choice"></a>Yapılandırma seçimi

Yavalar **tek başına sunucu** modunda önyüklenebilir-tek başına sunucu örneği, bir Jpatron uygulama sunucusu (as) 3, 4, 5 veya 6 örneği gibi bağımsız bir işlemdir. Tek başına örnekler, standalone.sh veya standalone.bat başlatma betikleri aracılığıyla başlatılabilir. Birden fazla tek başına örnek için, sunucu genelinde çok sunuculu yönetimi koordine eden kullanıcının sorumluluğundadır.

Ayrıca, yapılandırma klasöründe bulunan yapılandırma dosyalarını kullanarak, diğer yapılandırma ile de Yavaya eden bir örnek başlatabilirsiniz.

Tek başına sunucu yapılandırma dosyaları aşağıda verilmiştir:

- standalone.xml (varsayılan)-Bu yapılandırma, Yavama örneğini başlatmak için kullanılan varsayılan dosyadır. Gerekli teknolojilerle Jakarta Web profili sertifikalı yapılandırması içerir.
   
- standalone-ha.xml-Jakarta EE Web profili 8 (Web uygulamalarına hedeflenmiş) yüksek kullanılabilirliğe sahip sertifikalı yapılandırma.
   
- standalone-full.xml-Jakarta, Cakarta EE uygulamalarını barındırmak için gerekli tüm teknolojiler dahil olmak üzere sertifikalı yapılandırma.

- standalone-full-ha.xml-Jakarta, Cakarta EE uygulamalarını barındırmak için yüksek kullanılabilirliğe sahip, sertifikalı yapılandırma.

Tek başına Yavama sunucunuzu başka bir sağlanmış yapılandırmayla başlatmak için, sunucu-yapılandırma dosyası ile--Server-config bağımsız değişkenini kullanın.

Örneğin, Jakarta EE platform 8 ' i kümeleme özellikleri ile kullanmak için aşağıdaki komutu kullanın:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Konfigürasyonlar hakkında daha fazla bilgi edinmek için, [Başhızlı başlangıç kılavuzuna](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations)göz atın.

## <a name="licensing-support-and-subscription-notes"></a>Lisanslama, destek ve abonelik notları

Azure CentOS 8 görüntüsü, Kullandıkça Öde (PAYG) sanal makinesi görüntüsüdür ve kullanıcının bir lisans almasını gerektirmez. VM ilk kez başlatıldığında, VM 'nin işletim sistemi lisansı otomatik olarak etkinleştirilir ve saatlik bir ücret üzerinden ücretlendirilir. Bu, Microsoft 'un Linux saatlik VM tarifelerinin yanı sıra. Ayrıntılar için [LINUX VM fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) ' na tıklayın. Yavaya indirme ve kullanma ücretsizdir ve Red Hat aboneliği veya lisansı gerekmez.

## <a name="how-to-consume"></a>Nasıl tüketiliyor

Şablonu aşağıdaki üç şekilde dağıtabilirsiniz:

- Aşağıdaki komutları çalıştırarak PowerShell 'i kullanın-şablonu dağıtın: (Azure PowerShell yükleme ve yapılandırma hakkında bilgi için [Azure PowerShell](/powershell/azure/) kullanıma alma).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Azure CLı 'yi kullanma-aşağıdaki komutları çalıştırarak şablonu dağıtın: (Azure platformlar arası Command-Line arabirimini yükleme ve yapılandırma hakkında ayrıntılı bilgi için [Azure platformlar arası komut satırını](/cli/azure/install-azure-cli) inceleyin).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Azure portal kullanın, şablonu <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">buraya</a> tıklayarak dağıtın ve Azure Portal oturum açın.

## <a name="arm-template"></a>ARM şablonu

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> CentOS 8 ' de 18 ' de (tek BAŞıNA VM)</a> , bu, VM, sanal ağ ve tanılama depolama hesabı için özel bir IP içeren kaynak GRUBUNUZDAKI (RG) bir tek başına bir 18.0.1. Final düğümü oluşturan bir hızlı başlangıç şablonudur. Ayrıca, Azure 'da Jpatron-EAP adlı örnek bir Java uygulamasını da bir arada dağıtır.

## <a name="resource-links"></a>Kaynak bağlantıları

* Bu konuda daha fazla bilgi edinin [18](https://docs.wildfly.org/18/)
* [Azure 'Da Linux dağıtımları](../../linux/endorsed-distros.md) hakkında daha fazla bilgi edinin
* [Java geliştiricileri için Azure belgeleri](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Sonraki adımlar

Üretim ortamı için Red Hat Jpatron EAP Azure hızlı başlangıç ARM şablonlarına göz atın:

Örnek uygulamayla tek başına RHEL sanal makinesi:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> RHEL üzerinde Jpatron EAP (tek başına VM)</a>

Örnek uygulamayla kümelenmiş RHEL sanal makineleri:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> RHEL üzerinde Jpatron EAP (kümelenmiş VM 'Ler)</a>

Örnek uygulamayla kümelenmiş RHEL sanal makine ölçek kümesi:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> RHEL üzerinde Jpatron EAP (kümelenmiş sanal makine ölçek kümesi)</a>
