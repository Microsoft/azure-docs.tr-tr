---
title: 'Hızlı başlangıç: go ile bir yönetim grubu oluşturma'
description: Bu hızlı başlangıçta, kaynaklarınızı bir kaynak hiyerarşisinde düzenlemek üzere bir yönetim grubu oluşturmak için git ' i kullanacaksınız.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: bf2d2c556cfd6ada6d31fc6ee797888ed0899573
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091455"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Hızlı başlangıç: go ile bir yönetim grubu oluşturma

Yönetim grupları, birden çok abonelik üzerinde erişimi, ilkeyi ve uyumluluğu yönetmenize yardımcı olan kapsayıcılardır. [Azure ilkesi](../policy/overview.md) ve [Azure rol tabanlı erişim denetimleriyle](../../role-based-access-control/overview.md)kullanılabilecek etkili ve verimli bir hiyerarşi oluşturmak için bu kapsayıcıları oluşturun. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

Dizinde oluşturulan ilk yönetim grubunun tamamlanması 15 dakika sürebilir. Dizininiz için Azure 'da yönetim grupları hizmetini ayarlamak için ilk kez çalışan süreçler vardır. İşlem tamamlandığında bir bildirim alırsınız. Daha fazla bilgi için bkz. [yönetim gruplarının ilk kurulumu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- _ClientID_ ve _ClientSecret_ dahil olmak üzere bir Azure hizmet sorumlusu. Azure Ilkesiyle kullanılmak üzere bir hizmet sorumlusu yoksa veya yeni bir tane oluşturmak istiyorsanız, bkz. [.NET Yönetim kitaplıkları Için Azure kimlik doğrulaması](/dotnet/azure/sdk/authentication#mgmt-auth).
  Sonraki adımlarda bunu yapacağımız için .NET Core paketlerini yüklemek için adımı atlayın.

- Kiracıdaki tüm Azure AD kullanıcıları, [hiyerarşi koruması](./how-to/protect-resource-hierarchy.md#setting---require-authorization) etkinleştirilmemişse, bu kullanıcıya atanan yönetim grubu yazma izni olmadan bir yönetim grubu oluşturabilir. Bu yeni yönetim grubu kök yönetim grubunun veya [Varsayılan yönetim grubunun](./how-to/protect-resource-hierarchy.md#setting---default-management-group) bir alt öğesi haline gelir ve oluşturucuya "Owner" rol ataması verilir. Yönetim grubu hizmeti, bu becerisine, rol atamalarının kök düzeyinde gerekmemesi için izin verir. Kök yönetim grubuna, bir kullanıcı oluşturulduğunda erişemez. Yönetim grupları 'nı kullanmaya başlamak üzere Azure AD Genel yöneticilerini bulmanın önüne geçmek için, kök düzeyinde ilk yönetim gruplarının oluşturulmasına izin veririz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Yönetim grubu paketini ekleme

Yönetim gruplarını yönetmeye git özelliğini etkinleştirmek için paketin eklenmesi gerekir. Bu paket, [Windows 10 ' da Bash](/windows/wsl/install-win10) veya yerel olarak yüklü dahil olmak üzere her yerde go kullanılabilir.

1. En son go 'nun yüklü olup olmadığını denetleyin (en az **1,15**). Henüz yüklenmemişse, [Golang.org](https://golang.org/dl/)adresinden indirin.

1. En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.5.1**). Henüz yüklenmemişse bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Aşağıdaki örnekte yöntemini kullanmak için Azure CLı 'nin etkinleştirilmesi gerekir `auth.NewAuthorizerFromCLI()` . Diğer seçenekler hakkında daha fazla bilgi için bkz. [Go için Azure SDK-daha fazla kimlik doğrulaması ayrıntıları](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Azure CLı aracılığıyla kimlik doğrulaması yapın.

   ```azurecli
   az login
   ```

1. İstediğiniz git ortamınızda, yönetim grupları için gerekli paketleri yükler:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Uygulama kurulumu

Git paketleri tercih ettiğiniz ortamınıza eklendiğinde, bir yönetim grubu oluşturabileceğiniz Go uygulamasını ayarlamaya zaman atalım.

1. Go uygulamasını oluşturun ve aşağıdaki kaynağı şu şekilde kaydedin `mgCreate.go` :

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Go uygulamasını oluşturun:

   ```bash
   go build mgCreate.go
   ```

1. Derlenmiş Go uygulamasını kullanarak bir yönetim grubu oluşturun. `<Name>`Yeni yönetim grubunuzun adıyla değiştirin:

   ```bash
   mgCreate "<Name>"
   ```

Sonuç, kök yönetim grubundaki yeni bir yönetim grubudur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yüklü paketleri go ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak hiyerarşinizi düzenlemek için bir yönetim grubu oluşturdunuz. Yönetim grubu, abonelikleri veya diğer yönetim gruplarını içerebilir.

Yönetim grupları ve kaynak hiyerarşinizi yönetme hakkında daha fazla bilgi edinmek için şu çalışmaya devam edin:

> [!div class="nextstepaction"]
> [Kaynakları yönetim gruplarıyla yönetme](./manage.md)