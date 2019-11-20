---
title: Şablonlarda güvenli yapıtlar
description: Yapıtlar, Azure Resource Manager şablonlarında kullanılan güvenli hale getirmeyi öğrenin.
author: mumian
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4aea85fe7f71d09c77b0596b4a8ec54922c8eee8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150424"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Öğretici: Azure Resource Manager şablon dağıtımları yapıları güvenliğini sağlama

Paylaşılan erişim imzaları (SAS) ile Azure depolama hesabı kullanarak, Azure Resource Manager şablonlarında kullanılan yapıtların güvenli hale getirmeyi öğrenin. Dağıtım yapıtları dağıtımı tamamlamak için gereken tüm ana şablon dosyası yanı sıra dosyalarıdır. Örneğin, [öğretici: Azure Resource Manager şablonları ile içeri aktarma SQL BACPAC dosyalarını](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), Azure SQL veritabanı'ana şablon oluşturur; ayrıca tablo oluşturup veri eklemek için bir BACPAC dosyasını çağırır. BACPAC dosyasını bir yapıdır. Yapıt ortak erişim ile bir Azure depolama hesabında depolanır. Bu öğreticide, SAS kendi Azure depolama hesabında BACPAC dosyasına sınırlı erişim vermek için kullanın. SAS hakkında daha fazla bilgi için bkz: [paylaşılan erişim imzaları (SAS) kullanma](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Bağlı şablon güvenliğini sağlamayı öğrenmek için bkz: [Öğreticisi: bağlı bir Azure Resource Manager şablonları oluşturma](./resource-manager-tutorial-create-linked-templates.md).

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * BACPAC dosyası hazırlama
> * Mevcut bir şablonu açın
> * Şablonu düzenleme
> * Şablonu dağıtma
> * Dağıtımı doğrulama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* [Visual Studio Code](https://code.visualstudio.com/) ve Resource Manager Araçları uzantısı. Bkz. [Uzantıyı yükleme](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Gözden geçirme [öğretici: Azure Resource Manager şablonları ile içeri aktarma SQL BACPAC dosyalarını](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Bu öğreticide kullanılan Bu öğreticide geliştirilen bir şablonudur. Bu makalede bir indirme bağlantısı tamamlanmış şablonu sağlanır.
* Güvenliği artırmak istiyorsanız SQL Server yönetici hesabı için oluşturulmuş bir parola kullanın. Parola oluşturma örneği aşağıda verilmiştir:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault şifreleme anahtarları ve diğer gizli dizileri korumak üzere tasarlanmıştır. Daha fazla bilgi için bkz. [Öğretici: Azure Key Vault'u Resource Manager şablonu dağıtımıyla tümleştirme](./resource-manager-tutorial-use-key-vault.md). Ayrıca parolanızı üç ayda bir güncelleştirmenizi öneririz.

## <a name="prepare-a-bacpac-file"></a>BACPAC dosyası hazırlama

Bu bölümde, Resource Manager şablonu dağıtırken dosyayı güvenli bir şekilde erişilebilir olacak şekilde BACPAC dosyasını hazırlayın. Bu bölümde beş yordamı vardır:

* BACPAC dosyasını indirin.
* Bir Azure Depolama hesabı oluşturun.
* Bir depolama hesabı Blob kapsayıcı oluşturun.
* BACPAC dosyasını kapsayıcıya yükleyin.
* BACPAC dosyası SAS belirtecini alır.

Bir PowerShell Betiği kullanarak aşağıdaki adımları otomatikleştirmek için komut dosyasından bkz [bağlı şablonu karşıya](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>BACPAC dosyasını indirin

İndirme [BACPAC dosyasına](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac)ve aynı ada sahip yerel bilgisayarınıza kaydedin **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

1. Aşağıdaki görüntüde Azure portalında Resource Manager şablonu açmak için seçin.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Aşağıdaki özellikleri girin:

    * **Abonelik**: Azure aboneliğinizi seçin.
    * **Kaynak grubu**: seçin **Yeni Oluştur** ve bir ad verin. Bir kaynak grubu bir Azure kaynakları için yönetim amaç için kapsayıcıdır. Bu öğreticide, depolama hesabı ve Azure SQL veritabanı için aynı kaynak grubunu kullanabilirsiniz. Bu kaynak grubu adını not edin, sonraki öğreticilerde Azure SQL veritabanı oluşturduğunuzda gerekir.
    * **Konum**: bir bölge seçin. Örneğin, **Orta ABD**.
    * **Depolama hesabı türü**: varsayılan değeri kullanın **Standard_LRS**.
    * **Konum**: varsayılan değeri kullanın **[resourceGroup () .location]** . Depolama hesabı için kaynak grubu konumunu kullanmak anlamına gelir.
    * **Koşulları kabul ediyorum ve koşullar çalışmaya yukarıda**: (Seçili)
3. **Satın al**'ı seçin.
4. Dağıtım durumunu görmek için portalının sağ üst köşesindeki bildirim simgesine (zil simgesi) seçin.

    ![Resource Manager Öğreticisi portal bildirimler bölmesi](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Depolama hesabı başarıyla dağıtıldıktan sonra seçin **kaynak grubuna gidin** bildirim bölmesinden kaynak grubunu açın.

### <a name="create-a-blob-container"></a>Blob kapsayıcısı oluşturma

Dosyaları karşıya yükleyebilmeniz, bir Blob kapsayıcısı gereklidir.

1. Açmak için depolama hesabını seçin. Kaynak grubunda listelenen yalnızca bir depolama hesabı göreceksiniz. Depolama hesabınızın adını, aşağıdaki ekran görüntüsünde gösterildiği bir farklıdır.

    ![Resource Manager Öğreticisi depolama hesabı](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Seçin **Blobları** Döşe.

    ![Resource Manager Öğreticisi BLOB'ları](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Seçin **+ kapsayıcı** en yeni bir kapsayıcı oluşturun.
4. Aşağıdaki değerleri girin:

    * **Adı**: girin **sqlbacpac**.
    * **Genel erişim düzeyi**: varsayılan değeri kullanın **özel (anonim erişim yok)** .
5. **Tamam**’ı seçin.
6. Seçin **sqlbacpac** yeni oluşturulan kapsayıcı açın.

### <a name="upload-the-bacpac-file-to-the-container"></a>BACPAC dosyasını kapsayıcıya yüklemek

1. **Karşıya Yükle**’yi seçin.
2. Aşağıdaki değerleri girin:

    * **Dosyaları**: daha önce indirilen BACPAC dosyasını seçmek için talimatları izleyerek. Varsayılan ad **SQLDatabaseExtension.bacpac**.
    * **Kimlik doğrulama türü**: seçin **SAS**.  *SAS* varsayılan değerdir.
3. **Karşıya Yükle**’yi seçin.  Dosya başarıyla karşıya yüklendikten sonra dosya adı kapsayıcıda listelenen.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Bir SAS belirteci oluştur

1. Sağ **SQLDatabaseExtension.bacpac** kapsayıcı ve ardından **Generate SAS**.
2. Aşağıdaki değerleri girin:

    * **İzni**: varsayılan **okuma**.
    * **Başlangıç ve bitiş tarihi/saati**: varsayılan değer size sekiz saat içinde bir SAS belirteci kullanabilir. Bu öğreticiyi tamamlamak için daha fazla süreye ihtiyacınız varsa güncelleştirme **bitiş**.
    * **İzin verilen IP adresleri**: Bu alanı boş bırakın.
    * **İzin verilen protokoller**: varsayılan değeri kullanın: **HTTPS**.
    * **İmzalama anahtarı**: varsayılan değeri kullanın: **anahtar 1**.
3. Seçin **blob SAS belirteci ve URL üretmek**.
4. Bir kopyasını **Blob SAS URL'si**. Dosya adı URL'dir ortasında **SQLDatabaseExtension.bacpac**.  Dosya adı, URL üç bölüme ayırır:

   - **Yapıt konumu**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Emin olun konumu ile biten bir "/".
   - **BACPAC dosyası adı**: SQLDatabaseExtension.bacpac.
   - **Yapıt konumuna SAS belirteci**: belirteç önündeki ile emin bir "?."

     Bu üç değerleri ihtiyacınız [şablonu dağıtmak](#deploy-the-template).

## <a name="open-an-existing-template"></a>Mevcut bir şablonu açın

Bu oturumda, oluşturduğunuz şablonu değiştirmek [öğretici: Azure Resource Manager şablonları ile içeri aktarma SQL BACPAC dosyalarını](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) BACPAC dosyasını bir SAS belirteci ile çağırmak için.  SQL uzantı öğreticisinde geliştirilen şablon [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)'da paylaşılır.

1. Visual Studio Code’dan **Dosya**>**Dosya Aç**’ı seçin.
2. **Dosya adı**’na şu URL’yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Dosyayı açmak için **Aç**’ı seçin.

    Şablonda tanımlanan beş kaynak vardır:

   * `Microsoft.Sql/servers`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Şablonu özelleştirmeden önce temel noktaları kavramak faydalı olacaktır.
4. **Dosya**>**Farklı Kaydet**'i seçerek dosyanın bir kopyasını yerel bilgisayarınıza **azuredeploy.json** adıyla kaydedin.

## <a name="edit-the-template"></a>Şablonu düzenleme

Aşağıdaki ek parametreleri ekleyin:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Resource Manager Öğreticisi güvenli yapıtları parametreleri](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Değeri şu iki öğeyi güncelleştirin:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Şablonu dağıtma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dağıtım yordamı için [Şablonu dağıtma](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) bölümüne bakın. Bunun yerine aşağıdaki PowerShell dağıtım betiğini kullanın:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Oluşturulan bir parola kullanın. [Ön koşullara](#prerequisites) bakın.
_ArtifactsLocation ve _artifactsLocationSasToken bacpacFileName değerleri için bkz. [bir SAS belirteci oluşturmak](#generate-a-sas-token).

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

Portalda yeni dağıtılan kaynak grubundaki SQL veritabanını seçin. **Sorgu düzenleyicisi (önizleme)** öğesini seçip yönetici kimlik bilgilerini girin. İki tablonun veritabanına aktarılmış olması gerekir:

![Azure Resource Manager SQL uzantılarını dağıtma BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.  Kaynak grubundaki toplam altı kaynak görüyor olmalısınız.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir SQL veritabanı bir SQL sunucusu dağıttınız ve SAS belirteci kullanarak BACPAC dosyasını içeri aktarıldı. Kaynak Yöneticisi şablonlarını sürekli olarak geliştirip dağıtmak için Azure işlem hattı oluşturmayı öğrenmek için bkz.

> [!div class="nextstepaction"]
> [Azure işlem hattı ile sürekli tümleştirme](./resource-manager-tutorial-use-azure-pipelines.md)
