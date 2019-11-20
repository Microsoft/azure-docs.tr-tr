---
title: 'Azure Cloud Shell’den Databricks CLI kullanma '
description: Azure Databricks üzerinde işlem gerçekleştirmek için Azure Cloud Shell Databricks CLı 'yi nasıl kullanacağınızı öğrenin.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605715"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Azure Cloud Shell’den Databricks CLI kullanma

Databricks üzerinde işlem gerçekleştirmek için Azure Cloud Shell Databricks CLı 'yi kullanmayı öğrenin.

## <a name="prerequisites"></a>Ön koşullar

* Bir Azure Databricks çalışma alanı ve kümesi. Yönergeler için bkz. [Azure Databricks kullanmaya başlama](quickstart-create-databricks-workspace-portal.md). 

* Databricks 'te kişisel erişim belirteci ayarlayın. Yönergeler için bkz. [belirteç yönetimi](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell kullanın

1. [Azure portalı](https://portal.azure.com)’nda oturum açın.
 
2. Sağ üst köşeden **Cloud Shell** simgesine tıklayın.

   ![Cloud Shell Başlat](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Azure Cloud Shell'i başlatma")

3. Cloud Shell ortamı için **Bash** ' i seçtiğinizden emin olun. Aşağıdaki ekran görüntüsünde gösterildiği gibi açılır seçenekten seçim yapabilirsiniz.

   ![Cloud Shell ortamı için bash seçin](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Bash seçin") 

4. Databricks CLı 'yı yükleyebileceğiniz sanal bir ortam oluşturun. Aşağıdaki kod parçacığında `databrickscli`adlı bir sanal ortam oluşturursunuz.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Oluşturduğunuz sanal ortama geçiş yapın.

       source databrickscli/bin/activate

6. Databricks CLı 'yı yükler.

       pip install databricks-cli

7. Bir önkoşul kapsamında listelenen, oluşturduğunuz erişim belirtecini kullanarak Databricks ile kimlik doğrulamasını ayarlayın. Aşağıdaki komutu kullanın:

       databricks configure --token

    Aşağıdaki istemler gönderilir:

    * İlk olarak, Databricks konağını girmeniz istenir. `https://eastus2.azuredatabricks.net`biçimde değeri girin. Burada, **Doğu ABD 2** Azure Databricks çalışma alanınızı oluşturduğunuz Azure bölgesidir.

    * Ardından, bir belirteç girmeniz istenir. Daha önce oluşturduğunuz belirteci girin.

Bu adımları tamamladıktan sonra Azure Cloud Shell ' den Databricks CLı kullanmaya başlayabilirsiniz.

## <a name="use-databricks-cli"></a>Databricks CLı kullanma

Artık Databricks CLı 'yi kullanmaya başlayabilirsiniz. Örneğin, çalışma alanınızdaki tüm Databricks kümelerini listelemek için aşağıdaki komutu çalıştırın.

    databricks clusters list

Ayrıca, Databricks dosya sistemine (DBFS) erişmek için aşağıdaki komutu kullanabilirsiniz.

    databricks fs ls


Komutlarla ilgili kapsamlı bir başvuru için bkz. [Databricks CLI](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>Sonraki adımlar

* Azure CLı hakkında daha fazla bilgi edinmek için bkz. [Azure CLI genel bakış](../cloud-shell/overview.md)
* Azure CLı komutlarının listesini görmek için bkz. [Azure CLI başvurusu](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Databricks CLı komutlarının listesini görmek için bkz. [Databricks CLI](/azure/databricks/dev-tools/databricks-cli)


