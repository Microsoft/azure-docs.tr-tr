---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure CLı-bir MySQL için Azure veritabanı-esnek sunucu'
description: Bu hızlı başlangıçta bir Azure Kaynak grubunda MySQL için Azure veritabanı esnek sunucusu oluşturmak üzere Azure CLı 'nın nasıl kullanılacağı açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770246"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak MySQL için Azure veritabanı esnek sunucusu oluşturma

Bu hızlı başlangıçta, beş dakikada bir MySQL için Azure veritabanı esnek sunucusu oluşturmak üzere [Azure Cloud Shell](https://shell.azure.com) [Azure CLI](/cli/azure/get-started-with-azure-cli) komutlarının nasıl kullanılacağı gösterilmektedir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

> [!IMPORTANT] 
> MySQL için Azure veritabanı esnek sunucu şu anda genel önizlemede

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

[Azure Cloud Shell](../../cloud-shell/overview.md) , bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de açabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve çalıştırmak için **ENTER** ' u seçin.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2,0 veya üzerini gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Önkoşullar

[Az Login](/cli/azure/reference-index#az_login) komutunu kullanarak hesabınızda oturum açmanız gerekir. Azure hesabınızın **ABONELIK kimliğini** ifade eden **ID** özelliğine göz önüne alın.

```azurecli-interactive
az login
```

[Az Account set](/cli/azure/account#az_account_set) komutunu kullanarak hesabınız altındaki belirli bir aboneliği seçin. Komutta **abonelik** bağımsız değişkeninin değeri olarak kullanılacak **az Login** çıktısından **ID** değerini bir yere unutmayın. Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. Aboneliğinizi tamamen almak için [az Account List](/cli/azure/account#az_account_list)kullanın.

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Esnek sunucu oluşturma

Komutunu kullanarak bir [Azure Kaynak grubu](../../azure-resource-manager/management/overview.md) oluşturun `az group create` ve ardından bu kaynak grubunun içinde MySQL esnek sunucunuzu oluşturun. Benzersiz bir ad sağlamanız gerekir. Aşağıdaki örnek `eastus2` konumunda `myresourcegroup` adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Komutuyla esnek bir sunucu oluşturun `az mysql flexible-server create` . Bir sunucu birden çok veritabanı içerebilir. Aşağıdaki komut, Azure CLı 'nın [Yerel bağlamından](/cli/azure/local-context)hizmet varsayılanlarını ve değerlerini kullanarak bir sunucu oluşturur: 

```azurecli-interactive
az mysql flexible-server create
```

Oluşturulan sunucu aşağıdaki özniteliklere sahiptir: 
- Otomatik olarak oluşturulan sunucu adı, Yönetici Kullanıcı adı, yönetici parolası, kaynak grubu adı (zaten yerel bağlamda belirtilmemişse) ve kaynak grubunuzda aynı konumda 
- Kalan sunucu yapılandırmalarının hizmet Varsayılanları: İşlem Katmanı (Burstable), işlem boyutu/SKU (B1MS), yedekleme saklama süresi (7 gün) ve MySQL sürümü (5,7)
- Varsayılan bağlantı yöntemi, otomatik olarak oluşturulan bir sanal ağ ve alt ağ ile özel erişime (VNet tümleştirme) sahiptir

> [!NOTE] 
> Sunucu oluşturulduktan sonra bağlantı yöntemi değiştirilemez. Örneğin, oluştur sırasında *özel erişim (VNET tümleştirmesi)* seçtiyseniz, oluşturulduktan sonra *ortak erişime (izin verilen IP adresleri)* geçiş yapılamaz. VNet tümleştirmesini kullanarak sunucunuza güvenli bir şekilde erişmek için özel erişime sahip bir sunucu oluşturmanız önemle önerilir. [Kavramlar makalesinde](./concepts-networking.md)özel erişim hakkında daha fazla bilgi edinin.

Herhangi bir Varsayılanı değiştirmek istiyorsanız, lütfen yapılandırılabilir CLı parametrelerinin tüm listesi için Azure CLı [başvuru belgelerine](/cli/azure/mysql/flexible-server) bakın. 

Aşağıda örnek bir çıktı verilmiştir: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Herhangi bir Varsayılanı değiştirmek istiyorsanız, lütfen yapılandırılabilir CLı parametrelerinin tüm listesi için Azure CLı [başvuru belgelerine](/cli/azure/mysql/flexible-server) bakın. 

## <a name="create-a-database"></a>Veritabanı oluşturma
Bir veritabanı oluşturmak için aşağıdaki komutu çalıştırın, henüz bir tane oluşturmadıysanız **NewDataBase** .

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> MySQL için Azure Veritabanı bağlantıları 3306 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, 3306 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 3306 numaralı bağlantı noktasını açmadığı sürece sunucunuza bağlanamazsınız.

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **fullyQualifiedDomainName** ve **administratorLogin** bilgilerini not alın. JSON çıkışının bir örneği aşağıda verilmiştir: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Azure CLı kullanarak bağlantıyı bağlama ve test etme

MySQL için Azure veritabanı esnek sunucu, Azure CLı komutuyla MySQL sunucunuza bağlanmanızı sağlar ```az mysql flexible-server connect``` . Bu komut, veritabanı sunucunuza yönelik bağlantıyı test etmenize, hızlı bir başlangıç veritabanı oluşturmanıza ve mysql.exe veya MySQL çalışma ekranı 'nı yüklemeye gerek kalmadan doğrudan sunucunuza yönelik sorguları çalıştırmanıza olanak sağlar.  Ayrıca, birden çok sorgu çalıştırmak için komutu etkileşimli modda çalıştırabilirsiniz.

Geliştirme ortamınızdan veritabanına bağlantıyı sınamak ve doğrulamak için aşağıdaki betiği çalıştırın.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Örnek:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Başarılı bağlantı için aşağıdaki çıktıyı görmeniz gerekir:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Bağlantı başarısız olursa, şu çözümleri deneyin:
- İstemci makinenizde 3306 bağlantı noktasının açık olup olmadığını denetleyin.
- sunucu yöneticinizin Kullanıcı adı ve parolası doğruysa
- istemci makineniz için güvenlik duvarı kuralı yapılandırdıysanız
- sunucunuzu sanal ağ 'da özel erişim ile yapılandırdıysanız, istemci makinenizin aynı sanal ağda bulunduğundan emin olun.

Bağımsız değişkenini kullanarak tek bir sorgu yürütmek için aşağıdaki komutu çalıştırın ```--querytext``` ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Örnek:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Komutunu kullanma hakkında daha fazla bilgi için ```az mysql flexible-server connect``` [bağlanma ve sorgu](connect-azure-cli.md) belgelerine bakın.

## <a name="connect-using-mysql-command-line-client"></a>MySQL komut satırı istemcisini kullanarak bağlanma

Esnek sunucunuzu özel erişim (VNet tümleştirmesi) kullanarak oluşturduysanız, sunucunuza aynı sanal ağ içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturabilir ve bunu esnek sunucunuz ile oluşturulan sanal ağa ekleyebilirsiniz. Daha fazla bilgi edinmek için [özel erişim belgelerini](how-to-manage-virtual-network-portal.md) yapılandırma konusuna bakın.

Ortak erişim (izin verilen IP adresleri) kullanarak esnek sunucunuzu oluşturduysanız, yerel IP adresinizi sunucunuzdaki güvenlik duvarı kuralları listesine ekleyebilirsiniz. Adım adım yönergeler için [güvenlik duvarı kuralları oluşturma veya yönetme belgelerini](how-to-manage-firewall-portal.md) inceleyin.

Yerel ortamınızdan sunucusuna bağlanmak için [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ya da [MySQL çalışma ekranı](./connect-workbench.md) kullanabilirsiniz. MySQL için Azure veritabanı esnek sunucu, istemci uygulamalarınızı, daha önce Güvenli Yuva Katmanı (SSL) olarak bilinen Aktarım Katmanı Güvenliği (TLS) kullanarak MySQL hizmetine bağlamayı destekler. TLS, veritabanı sunucunuz ile istemci uygulamalarınız arasında şifrelenmiş ağ bağlantıları sağlayan ve uyumluluk gereksinimlerine bağlı olmanızı sağlayan bir endüstri standardı protokolüdür. MySQL esnek sunucunuza bağlanmak için, sertifika yetkilisi doğrulaması için [genel SSL sertifikasını](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) indirmeniz gerekir. Şifrelenmiş bağlantılarla bağlantı kurma veya SSL 'yi devre dışı bırakma hakkında daha fazla bilgi edinmek için bkz. [şifrelenmiş bağlantıları olan MySQL Için Azure veritabanı 'Na bağlanma-esnek sunucu](how-to-connect-tls-ssl.md) .

Aşağıdaki örnek, MySQL komut satırı arabirimini kullanarak esnek sunucunuza nasıl bağlanılacağını gösterir. Önce bir daha yüklenmemişse MySQL komut satırını yükleyeceksiniz. SSL bağlantıları için gereken Digiccertglobalrootca sertifikasını indirirsiniz. TLS/SSL sertifika doğrulamasını zorlamak için--SSL-Mode = REQUIRED bağlantı dizesi ayarını kullanın. Yerel sertifika dosyası yolunu--SSL-CA parametresine geçirin. Değerleri gerçek sunucu adı ve parolasıyla değiştirin.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Esnek sunucunuzu **ortak erişim** kullanarak sağladıysanız, aşağıda gösterildiği gibi önceden yüklenmiş MySQL istemcisini kullanarak esnek sunucunuza bağlanmak için [Azure Cloud Shell](https://shell.azure.com/bash) de kullanabilirsiniz:

Esnek sunucunuza bağlanmak için Azure Cloud Shell kullanabilmeniz için, Azure Cloud Shell ağ üzerinden esnek sunucunuza erişim izni vermeniz gerekir. Bunu başarmak için, MySQL esnek sunucunuz için Azure portal **ağ** dikey penceresine gidebilir ve **güvenlik duvarı** bölümündeki kutuyu, aşağıdaki ekran görüntüsünde gösterildiği gibi, "Azure 'daki herhangi bir Azure hizmetinden bu sunucuya genel erişime izin ver" diyen ve ayarı sürdürmek için Kaydet ' e tıklayabilirsiniz.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Ortak erişim ağı yapılandırması için MySQL esnek sunucusuna Azure Cloud Shell erişimine izin vermeyi gösteren ekran görüntüsü.":::
 
 
> [!NOTE]
> **Azure 'daki herhangi bir Azure hizmetinden bu sunucuya genel erişime Izin ver** ' in yalnızca geliştirme veya test için kullanılması gerekir. Güvenlik duvarını, diğer müşterilerin aboneliklerinden gelen bağlantılar da dahil olmak üzere herhangi bir Azure hizmetine veya varlığına ayrılan IP adreslerinden gelen bağlantılara izin verecek şekilde yapılandırır.

Azure Cloud Shell başlatmak için **dene** ' ye tıklayın ve esnek sunucunuza bağlanmak için aşağıdaki komutları kullanın. Komutta sunucu adınızı, Kullanıcı adınızı ve parolanızı kullanın. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Azure Cloud Shell kullanarak esnek sunucunuza bağlanırken--SSL = true parametresini kullanmanız gerekir;--SSL-Mode = gereklı değil.
> Birincil neden Azure Cloud Shell,--SSL parametresi gerektiren MariaDB dağılımda önceden yüklenmiş mysql.exe istemcisi ile birlikte gelir.

Daha önce komutu takip eden esnek sunucunuza bağlanırken aşağıdaki hata iletisini görürseniz, daha önce bahsedilen "Azure 'daki herhangi bir Azure hizmetinden ortak erişime Izin ver" seçeneğini kullanarak güvenlik duvarı kuralını ayarlamayı kaçırdınız veya seçenek kaydedilmez. Lütfen güvenlik duvarını ayarlamayı yeniden deneyin ve tekrar deneyin.

Hata 2002 (HY000): MySQL sunucusuna bağlanılamıyor <servername> (115)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu kaynaklara başka bir hızlı başlangıç/öğretici için gereksinim duymuyorsanız aşağıdaki komutu çalıştırarak kaynakları silebilirsiniz:

```azurecli-interactive
az group delete --name myresourcegroup
```

Yalnızca yeni oluşturulan sunucuyu silmek istiyorsanız `az mysql server delete` komutunu çalıştırabilirsiniz.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar

>[!div class="nextstepaction"]
> [Azure CLI](connect-azure-cli.md) 
>  kullanarak bağlanma ve sorgulama [Şifrelenmiş bağlantılarla, MySQL Için Azure veritabanı 'Na bağlanma-esnek sunucu](how-to-connect-tls-ssl.md) 
>  [MySQL Ile php (Laralevel) Web uygulaması oluşturma](tutorial-php-database-app.md)
