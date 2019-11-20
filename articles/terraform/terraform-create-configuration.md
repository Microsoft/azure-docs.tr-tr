---
title: Hızlı başlangıç-Azure için Terrayform yapılandırması oluşturma
description: Azure 'da bir Azure Cosmos DB ve Azure Container Instances dağıtarak Terrayform ile çalışmaya başlayın.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 47eec1f238d3fdff99b75953bc6d7e92137cca2d
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159322"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Hızlı başlangıç: Azure için Terrayform yapılandırması oluşturma

Bu hızlı başlangıçta, Terrayform kullanarak Azure kaynakları oluşturursunuz. Bu makaledeki adımlar aşağıdaki kaynakları oluşturma konusunda size yol gösterir:

> [!div class="checklist"]
> * Azure Cosmos DB örneği
> * Azure Container Örneği
> * Bu iki kaynak arasında çalışacak uygulama

## <a name="create-first-configuration"></a>İlk yapılandırma oluştur

Bu bölümde, bir Azure Cosmos DB örneği için yapılandırma oluşturacaksınız.

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.

1. Azure Cloud Shell açın.

1. Cloud Shell düzenleyicisini başlatın:

    ```bash
    code main.tf
    ```

1. Bu adımdaki yapılandırma birkaç Azure kaynağını modellediğinde. Bu kaynaklar bir Azure Kaynak grubu ve bir Azure Cosmos DB örneği içerir. Rastgele bir tamsayı, benzersiz bir Cosmos DB örnek adı oluşturmak için kullanılır. Çeşitli Cosmos DB ayarları da yapılandırılır. Daha fazla bilgi için [Cosmos DB Terrayform başvurusuna](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html)bakın. Aşağıdaki Terrayform yapılandırmasını kopyalayıp düzenleyiciye yapıştırın:

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

## <a name="run-the-configuration"></a>Yapılandırmayı çalıştırma

Bu bölümde, yapılandırmayı çalıştırmak için çeşitli Teraform komutlarını kullanırsınız.

1. [Terrayform init](https://www.terraform.io/docs/commands/init.html) komutu çalışma dizinini başlatır. Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```bash
    terraform init
    ```

1. [Terrayform plan](https://www.terraform.io/docs/commands/plan.html) komutu yapılandırma sözdizimini doğrulamak için kullanılabilir. `-out` parametresi, sonuçları bir dosyaya yönlendirir. Çıktı dosyası daha sonra yapılandırmayı uygulamak için kullanılabilir. Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```bash
    terraform plan --out plan.out
    ```

1. Bu yapılandırmayı uygulamak için [terrayform Apply](https://www.terraform.io/docs/commands/apply.html) komutu kullanılır. Önceki adımdan alınan çıkış dosyası belirtilir. Bu komut, Azure kaynaklarının oluşturulmasına neden olur. Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```bash
    terraform apply plan.out
    ```

1. Azure portal içindeki sonuçları doğrulamak için yeni kaynak grubuna gidin. Yeni Azure Cosmos DB örneği yeni kaynak grubunda bulunur.

## <a name="update-configuration"></a>Güncelleştirme yapılandırması

Bu bölümde, yapılandırmanın bir Azure Kapsayıcı örneği içerecek şekilde nasıl güncelleştirilmesi gösterilmektedir. Kapsayıcı Cosmos DB verileri okuyan ve yazan bir uygulamayı çalıştırır.

1. Cloud Shell düzenleyicisini başlatın:

    ```bash
    code main.tf
    ```

1. Bu adımdaki yapılandırma iki ortam değişkeni belirler: `COSMOS_DB_ENDPOINT` ve `COSMOS_DB_MASTERKEY`. Bu değişkenler veritabanına erişmek için konum ve anahtarı tutar. Bu değişkenlerin değerleri, önceki adımda oluşturulan veritabanı örneğinden alınır. Bu işlem ilişkilendirme olarak bilinir. Terrayform ilişkilendirme hakkında daha fazla bilgi için bkz. [enterpolasyon sözdizimi](https://www.terraform.io/docs/configuration/interpolation.html). Yapılandırma ayrıca kapsayıcı örneğinin tam etki alanı adını (FQDN) döndüren bir çıkış bloğu da içerir. Aşağıdaki kodu kopyalayıp düzenleyiciye yapıştırın:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

1. Önceki bölümde yaptığınız gibi, yapılan değişiklikler için görsel olarak aşağıdaki komutu çalıştırın:

    ```bash
    terraform plan --out plan.out
    ```

1. Yapılandırmayı uygulamak için `terraform apply` komutunu çalıştırın.

    ```bash
    terraform apply plan.out
    ```

1. Kapsayıcı örneği FQDN 'sini bir yere getirin.

## <a name="test-application"></a>Uygulamayı test etme

Uygulamayı test etmek için kapsayıcı örneğinin FQDN 'sine gidin. Aşağıdaki çıktıya benzer sonuçlar görmeniz gerekir:

![Azure oy uygulaması](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin.

Bu öğreticide oluşturulan Azure kaynaklarını kaldırmak için [terkform Destroy](https://www.terraform.io/docs/commands/destroy.html) komutunu çalıştırın:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure kaynaklarını sağlamak Için Terrayform 'U yükleyip yapılandırın](../virtual-machines/linux/terraform-install-configure.md).