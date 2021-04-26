---
title: "Hızlı başlangıç: GitHub eylemleriyle Azure SQL veritabanı 'na bağlanma"
description: GitHub eylemleri iş akışından Azure SQL kullanma
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: ceb9f0f9ef2a88532d5af16a03fcfd0282da84f8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787296"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>Azure SQL veritabanı 'na bağlanmak için GitHub eylemlerini kullanma

[Azure SQL veritabanı](../azure-sql-iaas-vs-paas-what-is-overview.md)'na veritabanı güncelleştirmeleri dağıtmak için bir iş akışı kullanarak [GitHub eylemleri](https://docs.github.com/en/actions) ile çalışmaya başlayın. 

## <a name="prerequisites"></a>Önkoşullar

Şunlara ihtiyacınız vardır: 
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Dacpac paketi () içeren bir GitHub deposu `Database.dacpac` . GitHub hesabınız yoksa [ücretsiz kaydolun](https://github.com/join).  
- Bir Azure SQL veritabanı.
    - [Hızlı başlangıç: Azure SQL veritabanı tek veritabanı oluşturma](single-database-create-quickstart.md)
    - [Mevcut SQL Server veritabanından bir dacpac paketi oluşturma](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>İş akışı dosyasına genel bakış

GitHub eylemleri iş akışı, deponuzdaki yoldaki bir YAML (. yıml) dosyası tarafından tanımlanır `/.github/workflows/` . Bu tanım, iş akışını oluşturan çeşitli adımları ve parametreleri içerir.

Dosya iki bölümden oluşur:

|Section  |Görevler  |
|---------|---------|
|**Kimlik Doğrulaması** | 1. bir hizmet sorumlusu tanımlayın. <br /> 2. GitHub gizli dizisi oluşturun. |
|**Dağıtma** | 1. veritabanını dağıtın. |

## <a name="generate-deployment-credentials"></a>Dağıtım kimlik bilgileri oluştur

[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutuyla bir [hizmet sorumlusu](../../active-directory/develop/app-objects-and-service-principals.md) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) veya **deneyin** düğmesini seçerek çalıştırın.

Yer tutucuları `server-name` Azure üzerinde BARıNDıRıLAN SQL Server 'ın adıyla değiştirin. Ve öğesini `subscription-id` `resource-group` SQL sunucunuza bağlı abonelik kimliği ve kaynak grubuyla değiştirin.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Çıktı, veritabanınıza bu örneğe benzer şekilde erişim sağlayan rol atama kimlik bilgileri içeren bir JSON nesnesidir. Çıktı JSON nesnenizin daha sonra için kopyalanması.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> En az erişim sağlamak her zaman iyi bir uygulamadır. Önceki örnekteki kapsam, kaynak grubu tümüyle değil, belirli bir sunucuyla sınırlıdır.

## <a name="copy-the-sql-connection-string"></a>SQL bağlantı dizesini Kopyala 

Azure Portal Azure SQL veritabanınıza gidin ve **Ayarlar**  >  **bağlantı dizeleri**' ni açın. **ADO.NET** bağlantı dizesini kopyalayın. Ve için yer tutucu değerlerini `your_database` değiştirin `your_password` . Bağlantı dizesi bu çıkışa benzer şekilde görünür. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Bağlantı dizesini GitHub parolası olarak kullanacaksınız. 

## <a name="configure-the-github-secrets"></a>GitHub gizli dizilerini yapılandırma

1. [GitHub](https://github.com/)'da deponuza gözatamazsınız.

1. **Yeni gizli > > ayarlar**' ı seçin.

1. Azure CLı komutundan tüm JSON çıkışını gizli dizi değeri alanına yapıştırın. Gizli dizi adını verin `AZURE_CREDENTIALS` .

    Daha sonra iş akışı dosyasını yapılandırdığınızda, `creds` Azure oturum açma eyleminin girişi için gizli anahtarı kullanırsınız. Örnek:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. **Yeni gizli** dizi seçimini yeniden seçin. 

1. Bağlantı dizesi değerini gizli dizi değeri alanına yapıştırın. Gizli dizi adını verin `AZURE_SQL_CONNECTION_STRING` .


## <a name="add-your-workflow"></a>İş akışınızı ekleyin

1. GitHub deponuz için **eylemlere** gidin. 

2. **İş akışınızı kendiniz ayarlama** seçeneğini belirleyin. 

2. `on:`İş akışı dosyanızın bölümünden sonraki her şeyi silin. Örneğin, kalan iş akışınız şöyle görünebilir. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. İş akışınızı yeniden adlandırın `SQL for GitHub Actions` ve kullanıma alma ve oturum açma eylemlerini ekleyin. Bu eylemler, site kodunuzu kullanıma alacak ve `AZURE_CREDENTIALS` daha önce oluşturduğunuz GitHub gizliliğini kullanarak Azure ile kimlik doğrulamasından acaktır. 

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. SQL örneğinize bağlanmak için Azure SQL dağıtımı eylemini kullanın. `SQL_SERVER_NAME`Sunucunuzun adıyla değiştirin. Deponuzun kök düzeyinde bir dacpac paketi ( `Database.dacpac` ) olmalıdır. 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. Azure oturumunu kapatmak için bir eylem ekleyerek iş akışınızı doldurun. Tamamlanan iş akışı aşağıda verilmiştir. Dosya, `.github/workflows` deponuzdaki klasörde görüntülenir.

    ```yaml
   name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Dağıtımınızı gözden geçirin

1. GitHub deponuz için **eylemlere** gidin. 

1. İş akışınızın çalışmasının ayrıntılı günlüklerini görmek için ilk sonucu açın. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="GitHub eylemlerinin çalışması günlüğü":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure SQL veritabanınız ve deponuzu artık gerekmiyorsa, kaynak grubunu ve GitHub deponuzu silerek dağıttığınız kaynakları temizleyin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure ve GitHub tümleştirmesi hakkında bilgi edinin](/azure/developer/github/)