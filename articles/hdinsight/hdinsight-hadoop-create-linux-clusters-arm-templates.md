---
title: Şablonları kullanarak Apache Hadoop kümeleri oluşturma-Azure HDInsight
description: Kaynak Yöneticisi şablonlarını kullanarak HDInsight için kümeler oluşturmayı öğrenin
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5dd8b54e0d3febf7dbb3209a9f0bde76263aa726
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494811"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Kaynak Yöneticisi şablonları kullanarak HDInsight 'ta Apache Hadoop kümeleri oluşturma
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Bu makalede, Azure Resource Manager şablonları kullanarak Azure HDInsight kümeleri oluşturmanın çeşitli yollarını öğreneceksiniz. Daha fazla bilgi için bkz. [Azure Resource Manager şablonuyla uygulama dağıtma](../azure-resource-manager/resource-group-template-deploy.md). Diğer küme oluşturma araçları ve özellikleri hakkında bilgi edinmek için bu sayfanın üst kısmındaki sekme seçicisine tıklayın veya [küme oluşturma yöntemlerine](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)bakın.

## <a name="prerequisites"></a>Önkoşullar
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Bu makaledeki yönergeleri izlemek için şunlar gerekir:

* Bir [Azure aboneliği](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell ve/veya Azure klasik CLı.

### <a name="resource-manager-templates"></a>Resource Manager şablonları
Kaynak Yöneticisi şablonu, tek ve eşgüdümlü bir işlemde uygulamanız için aşağıdaki kaynakları oluşturmayı kolaylaştırır:
* HDInsight kümeleri ve bağımlı kaynakları (varsayılan depolama hesabı gibi).
* Diğer kaynaklar ( [Apache Sqoop](https://sqoop.apache.org/)'yi kullanmak IÇIN Azure SQL veritabanı gibi).

Şablonda, uygulama için gereken kaynakları tanımlarsınız. Ayrıca, farklı ortamların değerlerini girmek için dağıtım parametrelerini de belirtirsiniz. Şablon, dağıtımınız için değer oluşturmak için kullandığınız JSON ve ifadelerden oluşur.

HDInsight şablon örnekleri için [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?term=hdinsight)' nı bulabilirsiniz. Şablonu iş istasyonunuzda bir dosyaya kaydetmek için [Kaynak Yöneticisi uzantısı](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) veya bir metin düzenleyici ile platformlar arası [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) kullanın. 

Kaynak Yöneticisi şablonları hakkında daha fazla bilgi için aşağıdaki makalelere ve örneklere bakın:

* [Azure Resource Manager şablonları yaz](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager şablonlarıyla uygulama dağıtma](../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft. HDInsight/kümeler](/azure/templates/microsoft.hdinsight/allversions) şablon başvurusu
* [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Şablon oluşturma

Kaynak Yöneticisi, farklı araçları kullanarak aboneliğinizdeki mevcut kaynaklardan bir Kaynak Yöneticisi şablonu dışarı aktarmanız sağlar. Bu oluşturulan şablonu şablon söz dizimi hakkında bilgi edinmek veya çözümünüzün yeniden dağıtımını gerektiği gibi otomatikleştirmek için kullanabilirsiniz. Daha fazla bilgi için bkz. [şablonları dışarı aktarma](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="deploy-using-the-portal"></a>Portalı kullanarak dağıtma

Azure portal kullanarak bir Kaynak Yöneticisi şablonu dağıtabilirsiniz. Daha fazla bilgi için bkz. [özel şablondan kaynak dağıtma](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>PowerShell kullanarak dağıtma

Azure PowerShell kullanarak Kaynak Yöneticisi şablonu dağıtabilirsiniz. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ile kaynak dağıtma ve Azure POWERSHELL](../azure-resource-manager/resource-group-template-deploy.md) [SAS belirteci ve Azure PowerShell ile özel kaynak yöneticisi şablonu dağıtma](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Azure CLI’yi kullanarak dağıtma

Klasik CLı kullanarak bir Kaynak Yöneticisi şablonu dağıtabilirsiniz. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure CLI ile kaynakları dağıtma](../azure-resource-manager/resource-group-template-deploy-cli.md) ve [SAS BELIRTECI ve Azure clı ile özel kaynak yöneticisi şablonu dağıtma](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>REST API kullanarak dağıtma
REST API kullanarak Kaynak Yöneticisi şablonu dağıtabilirsiniz. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonlarıyla kaynak dağıtma ve REST API Kaynak Yöneticisi](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Visual Studio ile dağıtma
 Bir kaynak grubu projesi oluşturmak ve Kullanıcı arabirimi aracılığıyla Azure 'a dağıtmak için Visual Studio 'Yu kullanın. Projenize dahil edilecek kaynak türünü seçersiniz. Bu kaynaklar Kaynak Yöneticisi şablonuna otomatik olarak eklenir. Proje, şablonu dağıtmak için bir PowerShell betiği de sağlar.

Visual Studio 'Yu kaynak gruplarıyla kullanmaya giriş için bkz. [Visual Studio aracılığıyla Azure Kaynak grupları oluşturma ve dağıtma](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Sorun giderme

HDInsight kümeleri oluştururken sorun yaşarsanız bkz. [erişim denetimi gereksinimleri](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, bir HDInsight kümesi oluşturmanın çeşitli yollarını öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* HDInsight ile ilgili diğer şablonlar için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* .NET istemci kitaplığı aracılığıyla kaynak dağıtmaya ilişkin bir örnek için bkz. [.NET kitaplıkları ve şablon kullanarak kaynakları dağıtma](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Uygulama dağıtmaya yönelik ayrıntılı bir örnek için bkz. [Azure 'da mikro hizmetler sağlama ve dağıtma](../app-service/deploy-complex-application-predictably.md).
* Çözümünüzü farklı ortamlarda dağıtmaya yönelik kılavuz için bkz. [Microsoft Azure’da geliştirme ve test ortamları](../solution-dev-test-environments.md).
* Azure Resource Manager şablonun bölümleri hakkında bilgi edinmek için bkz. [yazma şablonları](../azure-resource-manager/resource-group-authoring-templates.md).
* Azure Resource Manager şablonunda kullanabileceğiniz işlevlerin listesi için bkz. [Şablon işlevleri](../azure-resource-manager/resource-group-template-functions.md).
