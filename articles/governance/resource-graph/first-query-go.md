---
title: 'Hızlı başlangıç: ilk go sorgunuz'
description: Bu hızlı başlangıçta, kaynak grafiği paketini Go için etkinleştirmek ve ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920071"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Hızlı başlangıç: go kullanarak ilk kaynak grafik sorgunuzu çalıştırma

Azure Kaynak Grafiği 'ni kullanmanın ilk adımı, Go için gereken paketlerin yüklenip yüklenmediğini denetlerdir. Bu hızlı başlangıç, paketleri go yüklemenize ekleme sürecinde size yol gösterir.

Bu işlemin sonunda, paketleri go yüklemenize eklemiş ve ilk kaynak Graf sorgunuzu çalıştıracaksınız.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="add-the-resource-graph-package"></a>Kaynak Grafiği paketini ekleme

Azure Kaynak Grafiği 'ni sorgulamaya izin vermek için paketin eklenmesi gerekir. Bu paket, [Windows 10 ' da Bash](/windows/wsl/install-win10) veya yerel olarak yüklü dahil olmak üzere her yerde go kullanılabilir.

1. En son go 'nun yüklü olup olmadığını denetleyin (en az **1,14**). Henüz yüklenmemişse, [Golang.org](https://golang.org/dl/)adresinden indirin.

1. En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.5.1**). Henüz yüklenmemişse bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Aşağıdaki örnekte yöntemini kullanmak için Azure CLı 'nin etkinleştirilmesi gerekir `auth.NewAuthorizerFromCLI()` . Diğer seçenekler hakkında daha fazla bilgi için bkz. [Go için Azure SDK-daha fazla kimlik doğrulaması ayrıntıları](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Azure CLı aracılığıyla kimlik doğrulaması yapın.

   ```azurecli
   az login
   ```

1. İstediğiniz git ortamınızda Azure Kaynak Grafiği için gerekli paketleri yükler:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>İlk Kaynak Grafiği sorgunuzu çalıştırma

Git paketleri tercih ettiğiniz ortamınıza eklendiğinde, basit bir kaynak grafiği sorgusu denemenize zaman atalım. Sorgu, ilk beş Azure kaynağını her bir kaynağın **adı** ve **kaynak türü** ile döndürür.

1. Go uygulamasını oluşturun ve aşağıdaki kaynağı şu şekilde kaydedin `argQuery.go` :

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. Go uygulamasını oluşturun:

   ```bash
   go build argQuery.go
   ```

1. Derlenmiş Go uygulamasını kullanarak ilk Azure Kaynak Grafiği sorgunuzu çalıştırın. `<SubID>`ABONELIK Kimliğinizle değiştirin:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Bu sorgu örneği, `order by` gibi bir sıralama değiştirici sağlamadığı için, bu sorgunun birden çok kez çalıştırılması muhtemelen istek başına farklı bir kaynak kümesi sunacaktır.

1. İlk parametreyi öğesine değiştirin `argQuery` ve sorguyu Name özelliği olarak değiştirin `order by` .  `<SubID>`ABONELIK Kimliğinizle değiştirin:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. İlk parametreyi olarak değiştirin `argQuery` ve sorguyu ilk olarak `order by` **ad** özelliğine, sonra da `limit` en üstteki beş sonuca değiştirin. `<SubID>`ABONELIK Kimliğinizle değiştirin:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yüklü paketleri go ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak grafiği paketlerini go ortamınıza eklediniz ve ilk sorgunuzu çalıştırdık. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)