---
title: Tek bir veritabanı oluşturma
description: Azure portal, PowerShell veya Azure CLı kullanarak Azure SQL veritabanı 'nda tek bir veritabanı oluşturun.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/27/2021
ms.openlocfilehash: c0e86cad252da1b5484944c12351d4ee1e39edbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101656315"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Hızlı başlangıç: Azure SQL veritabanı tek veritabanı oluşturma

Bu hızlı başlangıçta, Azure SQL veritabanı 'nda Azure portal, bir PowerShell betiği veya bir Azure CLı betiği kullanarak [tek bir veritabanı](single-database-overview.md) oluşturacaksınız. Daha sonra Azure portal **sorgu düzenleyicisini** kullanarak veritabanını sorgulayın.



## <a name="prerequisite"></a>Önkoşul

- Etkin bir Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-a-single-database"></a>Tek bir veritabanı oluşturma

Bu hızlı başlangıç, [sunucusuz işlem katmanında](serverless-tier-overview.md)tek bir veritabanı oluşturur.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal tek bir veritabanı oluşturmak için bu hızlı başlangıç Azure SQL sayfasında başlatılır.

1. [SQL dağıtım seçeneği Seç](https://portal.azure.com/#create/Microsoft.AzureSQL) sayfasına gidin.
1. **SQL veritabanları**' nın altında **, kaynak türü** ' nü **tek veritabanı** olarak ayarlayın ve **Oluştur**' u seçin.

   ![Azure SQL 'e Ekle](./media/single-database-create-quickstart/select-deployment.png)

1. **SQL veritabanı oluştur** formunun **temel bilgiler** sekmesinde, **proje ayrıntıları**' nın altında, istenen Azure **aboneliğini** seçin.
1. **Kaynak grubu** Için **Yeni oluştur**' u seçin, *myresourcegroup* girin ve **Tamam**' ı seçin.
1. **Veritabanı adı** Için *mysampledatabase* yazın.
1. **Sunucu** Için **Yeni oluştur**' u seçin ve **Yeni sunucu** formunu aşağıdaki değerlerle doldurun:
   - **Sunucu adı**: *te yapılandırılmış MySQLServer* yazın ve benzersizlik için bazı karakterler ekleyin. Sunucu adları yalnızca bir abonelik içinde benzersiz değil, Azure 'daki tüm sunucular için genel olarak benzersiz olması gerektiğinden, kullanılacak tam sunucu adını sağlayamıyoruz. Bu nedenle, mysqlserver12345 gibi bir şey girin ve Portal kullanılabilir olup olmadığını bilmenize izin verir.
   - **Sunucu Yöneticisi oturum açma**: *azureuser* girin.
   - **Parola**: gereksinimleri karşılayan bir parola girin ve **Parolayı Onayla** alanına yeniden girin.
   - **Konum**: açılan listeden bir konum seçin.

   **Tamam**’ı seçin.

1. **SQL elastik havuz** kümesini **Hayır** olarak kullanmak istiyor.
1. **İşlem + depolama**'nın altında **Veritabanını yapılandır**'ı seçin.
1. Bu hızlı başlangıç sunucusuz bir veritabanını kullanır, bu nedenle **sunucusuz**' ı seçin ve **Uygula**' yı seçin. 

      ![Sunucusuz veritabanını Yapılandır](./media/single-database-create-quickstart/configure-database.png)

1. **İleri ' yi** seçin. sayfanın en altındaki Ağ.

   ![Yeni SQL veritabanı-temel sekmesi](./media/single-database-create-quickstart/new-sql-database-basics.png)

1. **Ağ** sekmesinde, **bağlantı yöntemi** için **ortak uç nokta**' ı seçin.
1. **Güvenlik duvarı kuralları** için, **geçerli Istemci IP adresini ekle** ' yi **Evet** olarak ayarlayın. **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin ver ' i** **Hayır** olarak bırakın.
1. Ileri ' yi seçin: sayfanın en altındaki **ek ayarlar** .

   ![Ağ sekmesi](./media/single-database-create-quickstart/networking.png)
  

1. **Ek ayarlar** sekmesinde, **veri kaynağı** bölümünde **var olan verileri kullan** için **örnek**' i seçin. Bu, boş bir boş veritabanı aksine, sorgu yapmak ve denemek üzere bazı tablolar ve veriler için bir AdventureWorksLT örnek veritabanı oluşturur.
1. İsteğe bağlı olarak, [SQL Için Azure Defender 'ı](../database/azure-defender-for-sql.md)etkinleştirin.
1. İsteğe bağlı olarak, [Bakım penceresini](../database/maintenance-window.md) , planlı bakımın veritabanınızın en iyi saatinde gerçekleştirilmesi için ayarlayın.
1. Sayfanın alt kısmındaki **gözden geçir + oluştur** ' u seçin:

   ![Ek ayarlar sekmesi](./media/single-database-create-quickstart/additional-settings.png)

1. **Gözden geçir + oluştur** sayfasında, gözden geçirdikten sonra **Oluştur**' u seçin.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com](https://shell.azure.com) . Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve **ENTER** tuşuna basarak çalıştırın.

## <a name="set-parameter-values"></a>Parametre değerlerini ayarla

Aşağıdaki değerler, sonraki komutlarda veritabanını ve gerekli kaynakları oluşturmak için kullanılır. Sunucu adlarının, sunucu adını oluşturmak için $RANDOM işlevin kullanıldığı tüm Azure genelinde genel olarak benzersiz olması gerekir. IP adresi aralığındaki 0.0.0.0 değerlerini, belirli ortamınızla eşleşecek şekilde değiştirin.

```azurecli-interactive
# Set the resource group name and location for your server
resourceGroupName=myResourceGroup
location=eastus

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567!

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
serverName=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name $resourceGroupName --location $location
```

## <a name="create-a-server"></a>Sunucu oluşturma

[Az SQL Server Create](/cli/azure/sql/server) komutuyla bir sunucu oluşturun.

```azurecli-interactive
az sql server create \
    --name $serverName \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password
```


## <a name="configure-a-firewall-rule-for-the-server"></a>Sunucu için bir güvenlik duvarı kuralı yapılandırma

[Az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) komutuyla bir güvenlik duvarı kuralı oluşturun.

```azurecli-interactive
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $serverName \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip
```


## <a name="create-a-single-database-with-azure-cli"></a>Azure CLı ile tek bir veritabanı oluşturma

[Az SQL DB Create](/cli/azure/sql/db) komutuyla bir veritabanı oluşturun. Aşağıdaki kod oluşturulur


```azurecli-interactive
az sql db create \
    --resource-group $resourceGroupName \
    --server $serverName \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --compute-model Serverless \
    --family Gen5 \
    --capacity 2
```


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Windows PowerShell kullanarak bir kaynak grubu, sunucu ve tek veritabanı oluşturabilirsiniz.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com](https://shell.azure.com) . Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve **ENTER** tuşuna basarak çalıştırın.

## <a name="set-parameter-values"></a>Parametre değerlerini ayarla

Aşağıdaki değerler, sonraki komutlarda veritabanını ve gerekli kaynakları oluşturmak için kullanılır. Sunucu adlarının tüm Azure genelinde genel olarak benzersiz olması gerekir, böylece Get-Random cmdlet 'i sunucu adını oluşturmak için kullanılır. IP adresi aralığındaki 0.0.0.0 değerlerini, belirli ortamınızla eşleşecek şekilde değiştirin.

```azurepowershell-interactive
   # Set variables for your server and database
   $resourceGroupName = "myResourceGroup"
   $location = "eastus"
   $adminLogin = "azureuser"
   $password = "Azure1234567!"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName
```


## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

```azurepowershell-interactive
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
```


## <a name="create-a-server"></a>Sunucu oluşturma

[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet 'i ile bir sunucu oluşturun.

```azurepowershell-interactive
  Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server
```

## <a name="create-a-firewall-rule"></a>Güvenlik duvarı kuralı oluşturma

[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) cmdlet 'i ile bir sunucu güvenlik duvarı kuralı oluşturun.

```azurepowershell-interactive
   Write-host "Configuring server firewall rule..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule
```


## <a name="create-a-single-database-with-powershell"></a>PowerShell ile tek bir veritabanı oluşturma

[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 'i ile tek bir veritabanı oluşturun.

```azurepowershell-interactive
   Write-host "Creating a gen5 2 vCore serverless database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeModel Serverless `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
```

---



## <a name="query-the-database"></a>Veritabanını sorgulama

Veritabanınız oluşturulduktan sonra veritabanına bağlanmak ve verileri sorgulamak için Azure portal **sorgu düzenleyicisini (Önizleme)** kullanabilirsiniz.

1. Portalda **SQL veritabanları**' nı arayıp seçin ve ardından listeden veritabanınızı seçin.
1. Veritabanınızın sayfasında, sol taraftaki menüden **sorgu Düzenleyicisi 'ni (Önizleme)** seçin.
1. Sunucu Yöneticisi oturum açma bilgilerinizi girip **Tamam**' ı seçin.

   ![Sorgu Düzenleyicisi 'nde oturum açın](./media/single-database-create-quickstart/query-editor-login.png)

1. **Sorgu Düzenleyicisi** bölmesine aşağıdaki sorguyu girin.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. **Çalıştır**'ı seçip **Sonuçlar** bölmesindeki sorgu sonuçlarını inceleyin.

   ![Sorgu Düzenleyicisi sonuçları](./media/single-database-create-quickstart/query-editor-results.png)

1. **Sorgu Düzenleyicisi** sayfasını kapatın ve kaydedilmemiş düzenlemelerinizi atmak isteyip Istemediğiniz sorulduğunda **Tamam** ' ı seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu, sunucuyu ve tek veritabanını bir sonraki adımlara devam etmek için ve veritabanınızı bağlanıp farklı yöntemlerle sorgulamayı öğrenin.

Bu kaynakları kullanmayı tamamladığınızda, oluşturduğunuz kaynak grubunu silebilirsiniz, bu da sunucuyu ve içindeki tek veritabanını da siler.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak **Myresourcegroup** ve tüm kaynaklarını silmek için:

1. Portalda **kaynak grupları**' nı arayıp seçin ve listeden **myresourcegroup** ' ı seçin.
1. Kaynak grubu sayfasında, **kaynak grubunu sil**' i seçin.
1. **Kaynak grubu adını yazın** altında *myresourcegroup* yazın ve ardından **Sil**' i seçin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kaynak grubunu ve tüm kaynaklarını silmek için, kaynak grubunuzun adını kullanarak aşağıdaki Azure CLı komutunu çalıştırın:

```azurecli-interactive
az group delete --name $resourceGroupName
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kaynak grubunu ve tüm kaynaklarını silmek için, kaynak grubunuzun adını kullanarak aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Sonraki adımlar

Farklı araçlar ve diller kullanarak veritabanınızı [bağlayın ve sorgulayın](connect-query-content-reference-guide.md) :
> [!div class="nextstepaction"]
> [SQL Server Management Studio kullanarak bağlanma ve sorgulama](connect-query-ssms.md)
>
> [Azure Data Studio kullanarak bağlanma ve sorgulama](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)

Bulut harcamalarınızı iyileştirmek ve kaydetmek istiyor musunuz?

> [!div class="nextstepaction"]
> [Maliyet yönetimi ile maliyetleri çözümlemeye başlayın](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)