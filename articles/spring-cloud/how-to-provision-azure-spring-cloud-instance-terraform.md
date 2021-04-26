---
title: Terrayform ile Azure Spring Cloud örneğini sağlama
description: Terrayform ile bir Azure yay bulutu örneği sağlayın.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 060ef2d08b849706b47b24748142c608292971b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878776"
---
# <a name="provision-an-azure-spring-cloud-instance-with-terraform"></a>Terrayform ile Azure yay bulutu örneği sağlama

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Bu örnek, Terrayform kullanarak bir Azure Spring Cloud örneği oluşturur. Yordamlar aşağıdaki kaynakları oluşturma konusunda size yol gösterir:

> [!div class="checklist"]
> * Kaynak Grubu
> * Azure yay bulutu örneği
> * Log Analytics için Azure depolama

> [!NOTE]
> Terkform 'a özgü destek için, HashiCorp 'in topluluk destek kanallarından birini terkform 'a kullanın:
>
> * , [HashiCorp topluluk portalının](https://discuss.hashicorp.com/c/terraform-core) soru, kullanım örnekleri ve faydalı desenler: Terkform bölümü
> * Sağlayıcıyla ilgili sorular: [HashiCorp topluluk portalının Terkform sağlayıcıları bölümü](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Önkoşullar

- **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

## <a name="create-configuration-file"></a>Yapılandırma dosyası oluşturma

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.

1. [Azure Cloud Shell](../app-service/quickstart-java.md#use-azure-cloud-shell)açın.

1. Cloud Shell düzenleyicisini başlatın:

    ```bash
    code main.tf
    ```

1. Bu adımdaki yapılandırma, Azure Kaynak grubu ve bir Azure Spring Cloud Instance dahil olmak üzere Azure kaynaklarını modellediğinde.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Dosyayı kaydedin (**&lt; CTRL>S**) ve düzenleyiciden çıkın (**&lt; CTRL>Q**).

## <a name="apply-the-configuration"></a>Yapılandırmayı Uygula

Bu bölümde, yapılandırmayı çalıştırmak için çeşitli Teraform komutlarını kullanırsınız.

1. [Terrayform init](https://www.terraform.io/docs/commands/init.html) komutu çalışma dizinini başlatır. Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```bash
    terraform init
    ```

1. Bu komut, yapılandırma sözdizimini doğrulamak [için kullanılır.](https://www.terraform.io/docs/commands/plan.html) `-out`Parametresi, sonuçları bir dosyaya yönlendirir. Çıktı dosyası daha sonra yapılandırmayı uygulamak için kullanılabilir. Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```bash
    terraform plan --out plan.out
    ```

1. [Uygulama](https://www.terraform.io/docs/commands/apply.html) , yapılandırmayı uygulamak için kullanılır. Komut, önceki adımda bulunan çıkış dosyasını belirtir. Bu komut Azure kaynaklarını oluşturur. Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```bash
    terraform apply plan.out
    ```

1. Azure portal içindeki sonuçları doğrulamak için yeni kaynak grubuna gidin. Yeni **Azure yay bulutu** örneği yeni kaynak grubunda gösterilmektedir.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Yapılandırmayı yapılandırma günlükleri ve ölçümleriyle Güncelleştir

Bu bölümde, Azure depolama hesabıyla Azure Spring Cloud için günlük ve ölçümleri etkinleştirmek üzere yapılandırmanın nasıl güncelleştirilmesi gösterilmektedir.

1. Cloud Shell düzenleyicisini başlatın:

    ```bash
    code main.tf
    ```

1. Dosyanın sonuna aşağıdaki yapılandırmayı ekleyin:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Dosyayı kaydedin (**&lt; CTRL>S**) ve düzenleyiciden çıkın (**&lt; CTRL>Q**).

1. Önceki bölümde olduğu gibi, değişiklikleri yapmak için aşağıdaki komutu çalıştırın:

    ```bash
    terraform plan --out plan.out
    ```

1. `terraform apply`Yapılandırmayı uygulamak için komutunu çalıştırın.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin.

Bu alıştırmada oluşturulan Azure kaynaklarını kaldırmak için [terkform Destroy](https://www.terraform.io/docs/commands/destroy.html) komutunu çalıştırın:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure kaynaklarını sağlamak Için Terrayform 'U yükleyip yapılandırın](/azure/developer/terraform/getting-started-cloud-shell).