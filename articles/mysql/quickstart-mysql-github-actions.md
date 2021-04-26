---
title: "Hızlı başlangıç: GitHub eylemleri ile Azure MySQL 'e bağlanma"
description: GitHub eylemleri iş akışından Azure MySQL kullanma
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 807fdbb1844eb6f89f71e639537a65baf8c76ad5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761772"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-mysql"></a>Hızlı başlangıç: Azure MySQL 'e bağlanmak için GitHub eylemlerini kullanma

**Uygulama hedefi**: :::image type="icon" source="./media/applies-to/yes.png" border="false"::: MySQL için Azure veritabanı-tek sunucu :::image type="icon" source="./media/applies-to/yes.png" border="false"::: MySQL Için Azure veritabanı-esnek sunucu

[MySQL Için Azure veritabanı](https://azure.microsoft.com/services/mysql/)'na veritabanı güncelleştirmelerini dağıtmak üzere bir iş akışı kullanarak [GitHub eylemleri](https://docs.github.com/en/actions) ile çalışmaya başlayın.


## <a name="prerequisites"></a>Önkoşullar

Şunlara ihtiyacınız vardır: 
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Örnek veri () içeren bir GitHub deposu `data.sql` . GitHub hesabınız yoksa [ücretsiz kaydolun](https://github.com/join).  
- MySQL için Azure veritabanı sunucusu.
    - [Hızlı başlangıç: Azure portal MySQL için Azure veritabanı sunucusu oluşturma](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>İş akışı dosyasına genel bakış

GitHub eylemleri iş akışı, deponuzdaki yoldaki bir YAML (. yıml) dosyası tarafından tanımlanır `/.github/workflows/` . Bu tanım, iş akışını oluşturan çeşitli adımları ve parametreleri içerir.

Dosya iki bölümden oluşur:

|Section  |Görevler  |
|---------|---------|
|**Kimlik Doğrulaması** | 1. bir hizmet sorumlusu tanımlayın. <br /> 2. GitHub gizli dizisi oluşturun. |
|**Dağıtma** | 1. veritabanını dağıtın. |

## <a name="generate-deployment-credentials"></a>Dağıtım kimlik bilgileri oluştur

[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) komutuyla bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) veya **deneyin** düğmesini seçerek çalıştırın.

Yer tutucuları `server-name` Azure 'da barındırılan MySQL sunucunuzun adıyla değiştirin. Ve öğesini `subscription-id` `resource-group` MySQL sunucunuza bağlı abonelik kimliği ve kaynak grubuyla değiştirin.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Çıktı, veritabanınıza aşağıdakine benzer şekilde erişim sağlayan rol atama kimlik bilgileri içeren bir JSON nesnesidir. Bu çıkış JSON nesnesini daha sonra kopyalayın.

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

## <a name="copy-the-mysql-connection-string"></a>MySQL bağlantı dizesini Kopyala 

Azure Portal, MySQL için Azure veritabanı sunucusuna gidin ve **Ayarlar**  >  **bağlantı dizeleri**' ni açın. **ADO.NET** bağlantı dizesini kopyalayın. Ve için yer tutucu değerlerini `your_database` değiştirin `your_password` . Bağlantı dizesi şuna benzer olacaktır. 

> [!IMPORTANT]
> - Tek sunucu için **uid = adminusername@servername** kullanın. Gerektiğini aklınızda edin **@servername** .
> - Esnek sunucu için, olmadan **uid = AdminUserName** kullanın @servername . MySQL esnek sunucusunun önizlemede olduğunu unutmayın. 


```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
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

1. Bağlantı dizesi değerini gizli dizi değeri alanına yapıştırın. Gizli dizi adını verin `AZURE_MYSQL_CONNECTION_STRING` .


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

1. İş akışınızı yeniden adlandırın `MySQL for GitHub Actions` ve kullanıma alma ve oturum açma eylemlerini ekleyin. Bu eylemler, site kodunuzu kullanıma alacak ve `AZURE_CREDENTIALS` daha önce oluşturduğunuz GitHub gizliliğini kullanarak Azure ile kimlik doğrulamasından acaktır. 

    ```yaml
    name: MySQL for GitHub Actions

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

1. MySQL örneğinize bağlanmak için Azure MySQL dağıtımı eylemini kullanın. `MYSQL_SERVER_NAME`Sunucunuzun adıyla değiştirin. Deponuzun kök düzeyinde adlı bir MySQL veri dosyasına sahip olmanız gerekir `data.sql` . 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Azure oturumunu kapatmak için bir eylem ekleyerek iş akışınızı doldurun. Tamamlanan iş akışı aşağıda verilmiştir. Dosya, `.github/workflows` deponuzdaki klasörde görüntülenir.

    ```yaml
    name: MySQL for GitHub Actions

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

            - uses: azure/mysql@v1
                with:
                    server-name: MYSQL_SERVER_NAME
                    connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
                    sql-file: './data.sql'

            # Azure logout 
            - name: logout
                run: |
                    az logout
    ```

## <a name="review-your-deployment"></a>Dağıtımınızı gözden geçirin

1. GitHub deponuz için **eylemlere** gidin. 

1. İş akışınızın çalışmasının ayrıntılı günlüklerini görmek için ilk sonucu açın. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="GitHub eylemlerinin çalışması günlüğü":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure MySQL veritabanınıza ve deponuza artık gerek duyulmadığında, kaynak grubunu ve GitHub deponuzu silerek dağıttığınız kaynakları temizleyin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure ve GitHub tümleştirmesi hakkında bilgi edinin](/azure/developer/github/)
