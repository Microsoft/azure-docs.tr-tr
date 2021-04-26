---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520808"
---
### <a name="create-the-cluster"></a>Kümeyi oluşturma

[Azure Red Hat OpenShift kümesi oluşturmak](../tutorial-create-cluster.md)için öğreticiyi izleyin. CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLı sürüm 2.6.0 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

### <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Azure Red Hat OpenShift kümesini yönetmek için, OpenShift komut satırı istemcisini kullanarak [OC](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)'yi kullanırsınız.

> [!NOTE]
> [OpenShift komut satırını](../tutorial-connect-cluster.md) [Azure Cloud Shell](https://shell.azure.com/) yüklemenizi ve aşağıdaki tüm komut satırı işlemleri için kullanmanızı öneririz. Kabuğunu shell.azure.com adresinden başlatın veya bağlantıya tıklayın:
>
> [![Ekli başlatma](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Azure Cloud Shell’i başlatma")](https://shell.azure.com/bash)

CLı 'yı yüklemek, küme kimlik bilgilerini almak ve web konsolunu ve OpenShift CLı kullanarak [kümeye bağlanmak](../tutorial-connect-cluster.md) için öğreticiyi izleyin.

Oturum açtıktan sonra, projeyi kullandığınızı söyleyen bir ileti görmeniz gerekir `default` .

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
