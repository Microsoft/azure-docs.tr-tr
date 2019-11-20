---
title: Güvenli bir Web uygulaması geliştirme | Microsoft Docs
description: Bu basit örnek uygulama, Azure 'da geliştirme yaparken uygulamanızı ve kuruluşunuzun güvenlik duruşunu geliştiren en iyi güvenlik uygulamalarını uygular.
keywords: yana
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 640900458eccc36afe58cb148ffd7b94b43be879
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934907"
---
# <a name="develop-a-secure-web-app"></a>Güvenli bir Web uygulaması geliştirme

Bu örnek, Azure 'da uygulama geliştirmeye yönelik güvenlik kaynaklarına bağlantılar içeren bir Web sayfası görüntüleyen basit bir Python uygulamasıdır. Uygulama, Azure 'da uygulama geliştirirken uygulamanızın ve kuruluşunuzun güvenlik duruşunuzun artırılmasına yardımcı olabilecek en iyi güvenlik uygulamalarını uygular.

Uygulama bileşenlerinin düzgün yapılandırıldığından emin olmak için bu makalede açıklanan adımları sırayla izlemelisiniz. Veritabanı, Azure App Service, Azure Key Vault örneği ve Azure Application Gateway örneği birbirlerine bağlıdır.

Dağıtım betikleri altyapıyı ayarlar. Dağıtım betiklerini çalıştırdıktan sonra, bileşenleri ve Hizmetleri birbirine bağlamak için Azure portal el ile yapılandırma yapmanız gerekir.

Örnek uygulama, kendi uygulamalarında güvenlik önlemleri uygulamak isteyen yeni başlayanlar için Azure 'da uygulama geliştirmeye yöneliktir.

Bu uygulamayı geliştirirken ve dağıttığınızda şunları öğreneceksiniz:

- Azure Key Vault bir örnek oluşturun, bu dosyadan gizli dizileri depolayın ve alın.
- PostgreSQL için Azure veritabanı 'nı dağıtın, güvenli parolalar ayarlayın ve ona erişimi yetkilendirin.
- Linux için Azure Web Apps 'da bir alp Linux kapsayıcısı çalıştırın ve Azure kaynakları için yönetilen kimlikleri etkinleştirin.
- [OWASP Top 10 RuleSet](https://coreruleset.org/)kullanan bir güvenlik duvarı ile Azure Application Gateway örneği oluşturun ve yapılandırın.
- Azure hizmetlerini kullanarak geçişte ve bekleyen verilerin şifrelenmesini etkinleştirin.

Bu uygulamayı geliştirip dağıttıktan sonra, açıklanan yapılandırma ve güvenlik ölçüleriyle birlikte aşağıdaki örnek Web uygulamasını ayarlamış olursunuz.

![Örnek Web uygulaması](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Mimari
Uygulama, üç katman içeren tipik bir n katmanlı uygulamadır. İzleme ve gizli yönetim bileşenleriyle tümleşik olan ön uç, arka uç ve veritabanı katmanı burada gösterilmektedir:

![Uygulama mimarisi](./media/secure-web-app/architecture.png)

Mimari aşağıdaki bileşenlerden oluşur:

- [Azure Application Gateway](../../application-gateway/index.yml). Uygulama mimarimiz için ağ geçidini ve güvenlik duvarını sağlar.
- [Linux üzerinde Azure Web Apps](../../app-service/containers/app-service-linux-intro.md). Bir Linux ortamında Python uygulamasını çalıştırmak için kapsayıcı çalışma zamanı sağlar.
- [Azure Key Vault](../../key-vault/index.yml). Uygulamanın gizli dizilerini depolar ve şifreler ve bunların çevresindeki erişim ilkelerinin oluşturulmasını yönetir.
- [PostgreSQL Için Azure veritabanı](https://azure.microsoft.com/services/postgresql/). Uygulama verilerini güvenli bir şekilde depolar.
- [Azure Güvenlik Merkezi](../../security-center/index.yml) ve [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Uygulamamızın işlemi üzerinde izleme ve uyarılar sağlar.

## <a name="threat-model"></a>Tehdit modeli
Tehdit modellemesi, işletmenizin ve uygulamanızın olası güvenlik tehditlerini tanımlama ve daha sonra uygun bir risk azaltma planının yerinde olmasını sağlama işlemidir.

Bu örnek, güvenli örnek uygulama için tehdit modellemesini uygulamak üzere [Microsoft Threat Modeling Tool](threat-modeling-tool.md) kullandı. Bileşenleri ve veri akışlarını diyagram oluşturarak, geliştirme sürecinde sorunları ve tehditleri erkenden ayırt edebilirsiniz. Bu, daha sonra zaman ve para tasarrufu sağlar.

Örnek uygulama için tehdit modelidir:

![Tehdit modeli](./media/secure-web-app/threat-model.png)

Tehdit modelleme aracının ürettiği bazı örnek tehditler ve olası güvenlik açıkları aşağıdaki ekran görüntüsünde gösterilmiştir. Tehdit modeli, sunulan saldırı yüzeyine genel bir bakış sunar ve geliştiricilerin sorunları nasıl azaltacağını düşündüğünü ister.

![Tehdit modeli çıkışı](./media/secure-web-app/threat-model-output.png)

Örneğin, önceki tehdit modeli çıktısına SQL ekleme, Kullanıcı girişlerini silerek ve PostgreSQL için Azure veritabanı 'nda saklı işlevler kullanılarak azaltılmaktadır. Bu hafifletme, veri okuma ve yazma işlemleri sırasında sorguların rastgele yürütülmesini önler.

Geliştiriciler tehdit modeli çıktısındaki tehditleri her birini azaltacak şekilde sistemin genel güvenliğini geliştirir.

## <a name="deployment"></a>Dağıtım
Aşağıdaki seçenekler Azure App Service Linux çalıştırmanızı sağlar:

- Azure 'da destekleyici teknolojilerle oluşturulan önceden oluşturulmuş Microsoft kapsayıcıları listesinden bir kapsayıcı seçin (Python, Ruby, PHP, Java, Node. js, .NET Core).
- Özel olarak oluşturulmuş bir kapsayıcı kullanın. Görüntünün kaynağı olarak kendi kapsayıcı kayıt defterlerinden birini seçin ve HTTP 'yi destekleyen birçok teknoloji üzerine derleyin.

Bu örnekte, WebApp 'yi App Service dağıtmaya ve kaynakları oluşturmaya yönelik dağıtım betiğini çalıştıracaksınız.

Uygulama aşağıda gösterilen farklı dağıtım modellerini kullanabilir:

![Dağıtım veri akışı diyagramı](./media/secure-web-app/deployment.png)

Azure 'da uygulama dağıtmanın birçok yolu vardır; örneğin:

- Azure Resource Manager şablonları
- PowerShell
- Azure CLI
- Azure portal
- Azure DevOps

Kullanılan uygulama:

- Kapsayıcı görüntülerini oluşturmak ve derlemek için [Docker](https://docs.docker.com/) .
- Dağıtım için [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .
- Kapsayıcı kayıt defteri olarak [Docker Hub 'ı](https://hub.docker.com/) .

## <a name="security-considerations"></a>Güvenlik konuları

### <a name="network"></a>Ağ
Örnek uygulama, ağ içine ve dışına akan geçiş içi veriler için uçtan uca SSL şifrelemesi kullanır. Ağ Geçidi, kendinden imzalı bir sertifika ile yapılandırılır.
> [!IMPORTANT]
> Bu gösteride otomatik olarak imzalanan bir sertifika kullanılır. Bir üretim ortamında, doğrulanmış bir sertifika yetkilisinden (CA) sertifikalar almalısınız.

Uygulama güvenlik duvarı, ağ trafiğinde kötü amaçlı trafik algılandığında gelen trafiği ve uyarıları yöneticileri de inceler.
Application Gateway, tehdit modelinde bulunan DDoS ve SQL ekleme tehditleri olasılığını azaltır.

### <a name="identity"></a>Kimlik
Portalda oturum açmak için örnek uygulama, kaynaklara erişim atanan Azure Active Directory (Azure AD) yöneticileri için Multi-Factor Authentication kullanır.
Örnek uygulama, Azure Key Vault gizli dizileri okuma ve alma izinleri elde etmek için Yönetilen kimlikler kullanır ve bu da uygulamanın gizli dizileri okumak için sabit kod kimlik bilgileri ve belirteçleri olması gerekmez. Azure AD, Yönetilen kimlikler kullanıldığında uygulamanın, gizli dizileri okuması ve değiştirmesi gereken hizmet sorumlularını otomatik olarak oluşturur.

Azure kaynakları ve MFA için Yönetilen kimlikler, öngörülerin sistemde ayrıcalık kazanmasına ve ayrıcalıklarını uygulamasına zorlamalarını zorlaştırır. Bu tehdit tehdit modelinde kullanıma alındı.
Uygulama OAuth kullanır ve bu, OAuth uygulamasında kayıtlı kullanıcıların uygulamada oturum açmasına olanak tanır.

### <a name="storage"></a>Depolama
PostgreSQL veritabanındaki veriler, PostgreSQL için Azure veritabanı tarafından REST 'te otomatik olarak şifrelenir. Veritabanı, yalnızca dağıtılan App Service Web uygulamasının doğru kimlik doğrulama kimlik bilgileriyle veritabanı kaynaklarına erişebilmesi için App Service IP adreslerini yetkilendirir.

### <a name="logging-and-auditing"></a>Günlüğe kaydetme ve denetleme
Uygulama, oluşan ölçümleri, günlükleri ve özel durumları izlemek için Application Insights kullanarak günlüğe kaydetmeyi uygular. Bu günlüğe kaydetme, geliştiriciler ve işlemler ekip üyelerine uygulamanın durumu hakkında bilgi vermek için yeterli uygulama meta verileri sağlar. Ayrıca güvenlik olayları durumunda geri izlemek için yeterli veri sağlar.

## <a name="cost-considerations"></a>Maliyetle ilgili konular
Henüz bir Azure hesabınız yoksa, ücretsiz bir hesap oluşturabilirsiniz. Kullanmaya başlamak için [ücretsiz hesap sayfasına](https://azure.microsoft.com/free/) gidin, ücretsiz bir Azure hesabıyla neler yapabileceğinizi öğrenin ve 12 ay boyunca hangi ürünlerin ücretsiz olduğunu öğrenebilirsiniz.

Örnek uygulamadaki kaynakları güvenlik özellikleriyle dağıtmak için bazı Premium özellikler için ödeme yapmanız gerekir. Uygulamanın ölçeklendirilen ve Azure tarafından sunulan ücretsiz katmanların ve denemelerin uygulama gereksinimlerini karşılayacak şekilde yükseltilmesi gerekir, maliyetleriniz artırılabilir. Maliyetlerinizi tahmin etmek için Azure [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanın.

## <a name="deploy-the-solution"></a>Çözümü dağıtma
### <a name="prerequisites"></a>Önkoşullar
Uygulamayı çalışır duruma getirmek için şu araçları yüklemeniz gerekir:

- Uygulama kodunu değiştirmek ve görüntülemek için bir kod Düzenleyicisi. [Visual Studio Code](https://code.visualstudio.com/) açık kaynak bir seçenektir.
- Geliştirme bilgisayarınızda [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) .
- Sisteminizde [Git](https://git-scm.com/) . Git, kaynak kodu yerel olarak kopyalamak için kullanılır.
- [JQ](https://stedolan.github.io/jq/), JSON 'u Kullanıcı dostu bir şekilde sorgulamak IÇIN bir UNIX aracıdır.

Örnek uygulamanın kaynaklarını dağıtmak için bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa, örnek uygulamayı test etmek için [ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/) .

Bu araçları yükledikten sonra, uygulamayı Azure 'da dağıtmaya hazırsınız demektir.

### <a name="environment-setup"></a>Ortam kurulumu
Ortamı ve aboneliği ayarlamak için Dağıtım betiklerini çalıştırın:

1. Kaynak kodu deposunu kopyalamak için şu git komutunu kullanın:

   ``` git
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```
2. Dizine geçiş yapmak için şu komutu kullanın:

   ```
   cd tutorial-project/scripts
   ```

3. Betikler klasöründe, kullanmakta olduğunuz platforma özgü dosyalar vardır (Windows veya Linux). Azure CLı zaten yüklü olduğundan, bu CLı komutunu çalıştırarak komut isteminde Azure hesabında oturum açın:

   ``` azurecli
   az login
   ```

Tarayıcı açılır, kimlik bilgilerinizle oturum açın. Oturum açtıktan sonra, kaynakları komut isteminden dağıtmaya başlayabilirsiniz.

Dağıtım betikleri `deploy-powershell.ps1` ve `deploy-bash.sh` tüm uygulamayı dağıtan kodu içerir.
Çözümü dağıtmak için:

1. PowerShell kullanıyorsanız, bölge ve kaynak grubu `deploy-powershell.ps1` adını uygun Azure `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` bölgeleriyle ve kaynak grubu için bir adla değiştirme yazarak dosyayı çalıştırın.
2. Linux `deploy-bash.sh` üzerinde çalışıyorsanız dosyayı yazarak `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`dosyayı yürütülebilir yapmanız gerekebilir.`chmod +x deploy-bash.sh`

Aşağıdaki örneklerde, anahtar bileşenlerinin parçacıkları gösterilmektedir. Dağıtım dosyalarını çalıştırarak örnekleri tek tek veya bileşenlerin geri kalanı ile dağıtabilirsiniz.

### <a name="implementation-guidance"></a>Uygulama kılavuzu
Dağıtım betiği dört aşamaya bölünebilir bir betiktir. Her aşama, [mimari diyagramında](#architecture)olan bir Azure kaynağını dağıtır ve yapılandırır.

Dört aşama şunlardır:

- Azure Key Vault dağıtın.
- PostgreSQL için Azure veritabanı 'nı dağıtın.
- Linux üzerinde Azure Web Apps dağıtın.
- Web uygulaması güvenlik duvarı ile Application Gateway dağıtın.

Her aşama, daha önce dağıtılan kaynaklardaki yapılandırmayı kullanarak bir önceki sürümünden sonra oluşturulur.

Uygulama adımlarını tamamlayabilmeniz için, [Önkoşullar](#prerequisites)altında listelenen araçları yüklediğinizden emin olun.

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault dağıt
Bu bölümde, gizli dizileri ve sertifikaları depolamak için kullanılan bir Azure Key Vault örneği oluşturup dağıtırsınız.

Dağıtımı tamamladıktan sonra, Azure üzerinde dağıtılan bir Azure Key Vault örneğiniz vardır.

Azure CLı kullanarak Azure Key Vault dağıtmak için:

1. Azure Key Vault değişkenlerini bildirin.
2. Azure Key Vault sağlayıcıyı kaydedin.
3. Örnek için kaynak grubu oluşturun.
4. Adım 3 ' te oluşturulan kaynak grubunda Azure Key Vault örneğini oluşturun.

   ``` azurecli

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```
Kaynaklara erişmek için Key Vault kullanan uygulamalarda Azure kaynakları için Yönetilen kimlikler kullanmak en iyi uygulamadır. Key Vault erişim tuşları kodda veya yapılandırmada depolanmıyorsa güvenlik sonrası artar.

#### <a name="deploy-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı 'nı dağıtma
PostgreSQL için Azure veritabanı aşağıdaki şekilde çalışmaktadır, önce veritabanı sunucusunu oluşturun, ardından şema ve verilerin depolandığı veritabanını oluşturun.

Dağıtımı tamamladıktan sonra, Azure üzerinde çalışan bir PostgreSQL sunucunuz ve veritabanınız vardır.

Azure CLı kullanarak PostgreSQL için Azure veritabanı 'nı dağıtmak için:

1. Azure CLı ve Azure abonelik kurulumunuzu içeren bir Terminal açın.
2. Veritabanına erişmek için kullanılan güvenli bir Kullanıcı adı ve parola kombinasyonu oluşturun. (Bunlar, bunları kullanan uygulamalar için Azure Key Vault depolanmalıdır.)
3. PostgreSQL sunucu örneğini oluşturun.
4. Adım 3 ' te oluşturduğunuz sunucu örneğinde bir veritabanı oluşturun.
5. PostgreSQL örneğinde PostgreSQL betikleri çalıştırın.

Aşağıdaki kod, yukarıdaki Keykasasını dağıtma adımında Azure Keykasasında depolanan PGUSERNAME ve PGPASSWORD gizli dizilerini kullanır.

   ``` azurecli
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Veritabanını dağıttıktan sonra, kimlik bilgilerini ve bağlantı dizesini Azure Key Vault depolamanız gerekir.
Betikler klasöründe, çalıştırdığınızda depolanan işlevleri oluşturan pl `functions.sql` /pgsql kodunu içeren bir dosya vardır. Bu dosyayı çalıştırmak, SQL ekleme işlemini sınırlamaya yönelik girişleri sıralar.

PostgreSQL, veritabanına bağlanmak için kullanılan adlı `psql` bir araçla birlikte paketlenmiştir. Çalıştırmak `functions.sql`için yerel makinenizden PostgreSQL için Azure veritabanı örneğine bağlanmanız ve oradan çalıştırmanız gerekir. Psql aracının yüklenmesi, her işletim sisteminde PostgreSQL için varsayılan yüklemeye dahildir.
Daha fazla bilgi için bkz. [psql belgeleri](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell `psql` araç da içerir. Cloud Shell simgesini seçerek doğrudan Azure portal Cloud Shell kullanabilirsiniz.

PostgreSQL örneğine uzaktan erişimi etkinleştirmek için PostgreSQL içindeki IP adresini yetkilendirmeniz gerekir.
**Bağlantı güvenliği** sekmesine giderek, **Istemci IP 'si Ekle**seçeneğini belirleyerek ve yeni ayarları kaydederek bu erişimi etkinleştirirsiniz.

![İstemci IP 'yi yetkilendir](./media/secure-web-app/add-client-ip-postgres.png)

Yerel psql aracı yerine Cloud Shell kullanıyorsanız, Cloud Shell erişime izin vermek için **Azure hizmetlerine erişime Izin ver** ' i ve değerini **Açık** olarak değiştirin ' i seçin.

Daha sonra, Azure portal PostgreSQL örneğinin **bağlantı dizeleri** sekmesinden bağlantı dizesi parametreleriyle aşağıdaki psql komutunu çalıştırarak örneğe bağlanın.
Boş küme ayraçlarını, veritabanının bağlantı dizesi dikey penceresindeki parametrelerle ve parola Azure Key Vault parolasıyla değiştirin.

```sql
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Veritabanına bağlı olduğunuzdan emin olduktan sonra aşağıdaki PL/pgSQL betiğini çalıştırın. Betik, veritabanına veri eklemek için kullanılan saklı işlevleri oluşturur.

```sql
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;


CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```


PostgreSQL için SSL ve sertifika yetkilisi (CA) doğrulamasını ayarlama hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı 'NDA SSL bağlantısını yapılandırma](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).

Kapsayıcıda bir kök sertifika bulunur. Sertifikayı elde etmek için uygulanan adımlar şunlardır:

1. Sertifika [yetkilisinden](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)sertifika dosyasını indirin.
2. [Makinenizde OpenSSL indirin ve yükleyin](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).
3. Sertifika dosyanızın kodunu çözün:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

PostgreSQL için SSL güvenliğini yapılandırma hakkında daha fazla bilgi için bkz. [SSL bağlantısı güvenliği yapılandırma](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Linux üzerinde Azure Web Apps dağıtma
Azure, Python, Ruby, C#ve Java gibi yaygın olarak kullanılan diller için önceden oluşturulmuş bir kapsayıcı ve resim kümesi sağladığından Azure App Service en üstünde Linux hizmetlerini kolayca oluşturabilirsiniz. Azure Ayrıca, Azure App Service platformunda neredeyse tüm programlama dillerinin çalıştırılmasına izin veren özel kapsayıcıları destekler.

Dağıtılan uygulama, en son Ubuntu Linux dağıtımında çalışan basit bir Python uygulamasıdır. Kimlik bilgileri yönetimi ve veri depolama için önceki bölümlerde oluşturulan Azure Key Vault ve PostgreSQL örneklerine bağlanır.

Aşağıdaki Docker dosyası, uygulamanın kök klasöründe verilmiştir:

``` docker
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

Yukarıdaki Dockerfile, üzerinde Azure Container Registry `mcr.microsoft.com/samples/basic-linux-app`barındırılan kapsayıcıyı oluşturmak için kullanılır.

Aşağıdaki kod:

1. App Service örneğinin değişkenlerini ve adlarını bildirir.
2. App Service planı için kaynak grubunu oluşturur.
3. Linux kapsayıcıları örneği üzerinde bir Azure Web Apps sağlar.
4. Web uygulaması kapsayıcısı için günlüğe kaydetmeyi etkinleştir.
5. Kapsayıcının uygulama ayarlarındaki bazı uygulama yapılandırmasını ayarlar.

   ```
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }

   ```

Bu betik, kod veya yapılandırmada gizli dizileri kodlamadan Azure Key Vault etkileşimde bulunmak için MSI ile birlikte kullanılabilecek App Service örneği için atanmış bir kimlik oluşturur.

Erişim ilkesi sekmesinde atanan kimliği yetkilendirmek için portalda Azure Key Vault örneğine gidin. **Yeni erişim Ilkesi Ekle**' yi seçin. **Asıl seçin**altında, oluşturulan App Service örneğinin adına benzer bir uygulama adı arayın.
Uygulamaya bağlı bir hizmet sorumlusu görünür olmalıdır. Aşağıdaki ekran görüntüsünde gösterildiği gibi, seçin ve erişim ilkesi sayfasını kaydedin.

Uygulamanın yalnızca anahtarları alması gerektiğinden gizli dizi seçeneklerinde **Al** iznini seçin ve verilen ayrıcalıkları azaltırken erişime izin verir.

![Key Vault erişim Ilkesi](./media/secure-web-app/kv-access-policy.png)

*Key Vault erişim ilkesi oluşturma*

Erişim ilkesini kaydedin ve sonra ilkeleri güncelleştirmek için **erişim ilkeleri** sekmesindeki yeni değişikliği kaydedin.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Web uygulaması güvenlik duvarı etkinken Application Gateway dağıtma
Web Apps 'te, Hizmetleri doğrudan Internet 'teki dış dünya için kullanıma sunmanın önerilmez.
Yük Dengeleme ve güvenlik duvarı kuralları, gelen trafik üzerinde daha fazla güvenlik ve denetim sağlar ve bunu yönetmenize yardımcı olur.

Application Gateway örneği dağıtmak için:

1. Uygulama ağ geçidini barındırmak için kaynak grubu oluşturun.
2. Ağ geçidine iliştirilecek bir sanal ağ sağlayın.
3. Sanal ağda ağ geçidi için bir alt ağ oluşturun.
4. Genel bir IP adresi sağlayın.
5. Uygulama ağ geçidini sağlayın.
6. Ağ geçidinde Web uygulaması güvenlik duvarını etkinleştirin.

   ``` azurecli
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Önceki komut dosyası:

1. Azure 'da otomatik olarak imzalanan yeni bir sertifika oluşturur.
2. Otomatik olarak imzalanan sertifikayı Base64 ile kodlanmış bir dosya olarak indirir.
3. Otomatik olarak imzalanan sertifika için bir parola oluşturur.
4. Sertifikayı parolayla imzalanmış bir PFX dosyası olarak dışarı aktarır.
5. Sertifikanın parolasını Azure Key Vault depolar.

Bu bölüm, uygulama ağ geçidini dağıtır:

```powershell
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Dağıtımı tamamladıktan sonra, Web uygulaması güvenlik duvarı 'nın etkin olduğu bir uygulama ağ geçidiniz vardır.

Ağ Geçidi örneği, HTTPS için 443 bağlantı noktasını kullanıma sunar. Bu yapılandırma, uygulamamıza yalnızca HTTPS üzerinden 443 numaralı bağlantı noktası üzerinden erişilebilmesini sağlar.

Kullanılmayan bağlantı noktalarını engelleme ve saldırı yüzeyi pozlamasını sınırlandırma en iyi güvenlik yöntemidir.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>App Service örneğine ağ güvenlik grupları ekleme

App Service örnekleri, sanal ağlarla tümleştirilebilir. Bu tümleştirme, uygulamanın gelen ve giden trafiği yöneten ağ güvenlik grubu ilkeleriyle yapılandırılmasını sağlar.

1. Bu özelliği etkinleştirmek için, Azure App Service örneği dikey penceresinde **Ayarlar**' ın altında **ağ**' ı seçin. Sağ bölmede, **VNET tümleştirmesi**altında, **yapılandırmak Için buraya tıklayın ' ı**seçin.

   ![Yeni sanal ağ tümleştirmesi](./media/secure-web-app/app-vnet-menu.png)

    *App Service için yeni sanal ağ tümleştirmesi*
1. Sonraki sayfada **VNET Ekle (Önizleme)** öğesini seçin.

1. Sonraki menüde, dağıtımda `hello-vnet`oluşturulan sanal ağı seçin. Yeni bir alt ağ oluşturabilir veya var olan bir alt ağı seçebilirsiniz.
   Bu durumda yeni bir alt ağ oluşturun. **Adres aralığını** **10.0.3.0/24** olarak ayarlayın ve alt ağ **App-subnet**olarak adlandırın.

   ![App Service sanal ağ yapılandırması](./media/secure-web-app/app-vnet-config.png)

    *App Service için sanal ağ yapılandırması*

Sanal ağ tümleştirmesini etkinleştirmiş olduğunuza göre, uygulamamıza ağ güvenlik grupları ekleyebilirsiniz.

1. Arama kutusunu kullanarak **ağ güvenlik grupları**' nı arayın. Sonuçlarda **ağ güvenlik grupları** ' nı seçin.

    ![Ağ güvenlik grupları ara](./media/secure-web-app/nsg-search-menu.png)

    *Ağ güvenlik grupları ara*

2. Sonraki menüde **Ekle**' yi seçin. NSG ve bulunduğu **kaynak grubunun** **adını** girin. Bu NSG, uygulama ağ geçidinin alt ağına uygulanır.

    ![NSG oluşturma](./media/secure-web-app/nsg-create-new.png)

    *NSG oluşturma*

3. NSG oluşturulduktan sonra, bunu seçin. Dikey penceresinde, **Ayarlar**altında **gelen güvenlik kuralları**' nı seçin. 443 numaralı bağlantı noktası üzerinden Application Gateway 'e gelen bağlantılara izin vermek için bu ayarları yapılandırın.

   ![NSG 'yi yapılandırma](./media/secure-web-app/nsg-gateway-config.png)

   *NSG 'yi yapılandırma*

4. Ağ Geçidi NSG için giden kuralları ' nda, hizmet etiketini `AppService`hedefleyen bir kural oluşturarak App Service örneğine giden bağlantılara izin veren bir kural ekleyin:

   ![NSG için giden kuralları ekleme](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *NSG için giden kuralları ekleme*

    Ağ geçidinin bir sanal ağa giden kuralları göndermesini sağlamak için başka bir giden kuralı ekleyin.

   ![Başka bir giden kuralı ekle](./media/secure-web-app/nsg-outbound-vnet.png)

    *Başka bir giden kuralı ekle*

5. NSG alt ağları dikey penceresinde **ilişkilendir**' i seçin, dağıtımda oluşturulan sanal ağı seçin ve **GW-subnet**adlı ağ geçidi alt ağını seçin. NSG alt ağa uygulanır.

6. App Service örneği için bu kez, önceki adımda olduğu gibi başka bir NSG oluşturun. Bir ad verin. Application Gateway NSG için yaptığınız gibi 443 numaralı bağlantı noktası için gelen kuralını ekleyin.

   Bu uygulama için durum bilgisi olmayan bir App Service Ortamı örneği üzerinde dağıtılan bir App Service örneğiniz varsa, App Service NSG 'nizin gelen güvenlik gruplarında 454-455 bağlantı noktalarını açarak Azure hizmet durumu araştırmalarını sağlamak için gelen kuralları ekleyebilirsiniz. Yapılandırma şu şekildedir:

   ![Azure hizmet durumu araştırmaları için kurallar ekleme](./media/secure-web-app/nsg-create-healthprobes.png)

    *Azure hizmet durumu araştırmaları için kurallar ekleme (yalnızca App Service Ortamı)*

7. Giden güvenlik kurallarında, App Service örneğinin PostgreSQL veritabanıyla iletişim kurmasına izin veren yeni bir giden güvenlik kuralı oluşturun. Bunu şu şekilde yapılandırın:

   ![Giden PostgreSQL bağlantılarına izin verme kuralı](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Giden PostgreSQL bağlantılarına izin vermek için bir kural ekleyin*

Saldırı yüzeyini sınırlandırmak için App Service ağ ayarlarını yalnızca uygulama ağ geçidinin uygulamaya erişmesine izin verecek şekilde değiştirin.
Bunu, App Service ağı sekmesine giderek, **IP kısıtlamaları** sekmesini seçerek ve yalnızca uygulama ağ geçidi 'nin IP 'si hizmete doğrudan erişmesini sağlayan bir izin verme kuralı oluşturarak yapabilirsiniz.

Ağ geçidinin IP adresini genel bakış sayfasından alabilirsiniz. **IP adresı CIDR** sekmesinde IP adresini şu biçimde girin: `<GATEWAY_IP_ADDRESS>/32`.

![Yalnızca ağ geçidine izin ver](./media/secure-web-app/app-allow-gw-only.png)

*Yalnızca ağ geçidi IP 'nin App Service erişmesine izin ver*


#### <a name="implement-azure-active-directory-oauth"></a>Azure Active Directory OAuth 'ı Uygula

Örnek Web uygulaması sayfasında dağıtılmış olan Azure belgeleri, uygulamamızda koruma gerektiren kaynaklardır. Farklı kimlik doğrulama akışlarını kullanarak Web, masaüstü ve mobil uygulamalar için kimlik doğrulaması uygulamak üzere Azure Active Directory (Azure AD) kullanabilirsiniz.
Uygulama, **Microsoft Ile oturum açma**kullanır ve uygulamanın, tek KIRACıLı Azure AD kullanıcısının listesine eklenmiş kullanıcıların profillerini okumasına olanak tanır.

Azure portal, uygulamayı gerekli kimlik bilgilerini kullanacak şekilde yapılandırın:

1. **Azure Active Directory**' yi seçin veya arama kutusunu kullanarak arayın.

2. **Yeni kayıt**seçin:

   ![Kayıt oluştur](./media/secure-web-app/ad-auth-create.png)

   *Azure AD uygulama kaydı oluşturma*

3. Sonraki sayfada, uygulama adını girin. **Desteklenen hesap türleri**altında **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.
    **Yeniden yönlendirme URI 'si**altında, uygulamanın, belirteç uç noktasıyla birlikte çalıştırılacağı temel etki alanını girin. Örneğin: *GATEWAY_HASH*. cloudapp.net/Token.

   ![Azure AD uygulama kaydını yapılandırma](./media/secure-web-app/ad-auth-type.png)

   *Azure AD uygulama kaydını yapılandırma*

4. Kayıtlı uygulamayı ve bilgilerini gösteren bir ekran görüntülenir. Bu bilgileri Azure Key Vault örneğine eklemeniz gerekir.
   1. Uygulama (istemci) KIMLIĞINI kopyalayın ve Key Vault olarak `CLIENTID`kaydedin.
   2. Önceki adımda girdiğiniz yeniden yönlendirme URI 'sini kopyalayın ve farklı `REDIRECTURI`kaydedin.
   3. *Name*. microsoftonline.com biçiminde olan Azure AD varsayılan dizin adını kopyalayın ve Key Vault olarak `TENANT`kaydedin.
   4. Daha önce oluşturduğunuz Azure AD uygulamasının **sertifikalar & gizlilikler** sekmesine gidin ve aşağıdaki ekran görüntüsünde gösterildiği gibi **yeni istemci parolası**' nı seçin. Bir sona erme tarihi ayarlayın ve ardından oluşturulan değeri kopyalayın ve Key Vault olarak `CLIENTSECRET`kaydedin.

      ![Azure AD yetkilendirme parolası](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD yetkilendirme parolası*

   5. Herhangi bir komut satırı/çevrimiçi aracı kullanarak güvenli bir rastgele gizli anahtar oluşturun. Key Vault olarak `FLASKSECRETKEY`kaydedin. Uygulama çerçevesi, oturum oluşturmak için bu anahtarı kullanır.
        Gizli anahtar oluşturmayı öğrenmek için bkz. [Flask oturumları](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Oturum açma 'yı yapılandırdıktan sonra, kaynakta oturum açmalarına olanak tanımak için Azure AD bağlantısına Kullanıcı eklemeniz gerekir. Bunları eklemek için Azure AD 'deki **Kullanıcılar** sekmesine gidin, **tüm kullanıcılar**' ı seçin ve ardından **Yeni Kullanıcı** veya **Yeni Konuk Kullanıcı**' yı seçin. Sınama için, Konuk kullanıcı ekleyebilir ve kullanıcıyı dizine davet edebilirsiniz. Ya da uygulamanın üzerinde çalıştığı etki alanı doğrulandıktan sonra yeni bir kullanıcı ekleyebilirsiniz. Bu örnekte, yalnızca Azure AD kiracısında kayıtlı olan kullanıcılar erişim için kaydedilebilir. Çoklu kiracılı oturum açma erişimi hakkında daha fazla bilgi için belgelerine bakın.

   ![Kullanıcıları varsayılan etki alanına ekleme](./media/secure-web-app/ad-auth-add-user.png)

   *Varsayılan Azure Active Directory etki alanına kullanıcı ekleme*

Key Vault için Azure AD yapılandırma ve gizli dizileri ekledikten sonra, kullanıcıların Azure OAuth kimlik doğrulaması kullanılarak uygulama üzerinde kimlik doğrulaması yapılabilir.
Uygulama kodunda, bu Azure Active Directory kimlik doğrulama kitaplığı (ADAL) tarafından işlenir.

Gizli dizileri Key Vault ve uygulamanın gizli dizileri ve veritabanına erişimi varsa, uygulama hizmetine ağ geçidinin uygulama URL 'si aracılığıyla ulaşılırsa (https://GATEWAY_HASH.cloudapp.net), dikey penceresinden alabilirsiniz.

Azure AD 'de oturum açtığınızda, "Kullanıcı oturum açmaya çalıştığınız dizinde kayıtlı değil" ifadesini içeren bir hata alırsınız ve Kullanıcı eklemeniz gerekir. Kullanıcı eklemek için, Azure AD 'nin **Kullanıcılar** sekmesine gidin ve Kullanıcı bilgilerini davet ederek kullanıcıyı bir Konuk Kullanıcı olarak e-posta adresini **davet et** dikey penceresinde Azure AD 'ye girerek kullanıcıyı el ile ekleyin.

#### <a name="deploy-application-insights"></a>Application Insights dağıt
Uygulama dağıtıldığına ve çalışır durumda olduğuna göre, uygulama içinde oluşan hataları günlüğe kaydetme ve izleme veri toplama ile birlikte işlemeniz gerekir.
Günlüğe kaydetme ve izleme veri koleksiyonu, uygulamada gerçekleşen denetim olaylarına bir görünüm sağlar.

Application Insights, kullanıcılar veya sistem tarafından oluşturulabilecek günlükleri toplayan bir hizmettir.

Application Insights bir örnek oluşturmak için:

1. Azure portal arama kutusunu kullanarak **Application Insights** arayın.
2. **Application Insights**seçin. Örnek oluşturmak için burada gösterilen ayrıntıları sağlayın.

   ![Application Insights örneği oluşturma](./media/secure-web-app/app-insights-data.png)

Dağıtım tamamlandıktan sonra bir Application Insights örneğiniz olur.

Uygulama öngörüleri örneğini oluşturduktan sonra, uygulamanın buluta Günlükler göndermesini sağlayan izleme anahtarına sahip olması gerekir. Bunu, Application Insights anahtarını alarak ve Azure 'un Application Insights için sağladığı uygulama kitaplıklarında kullanarak yapabilirsiniz. En iyi yöntem, anahtarları ve gizli dizileri güvenli tutmak için Azure Key Vault depokullanmaktır.

Temel örnek uygulama için, Application Insights örneğini oluşturduktan sonra, uygulamanın buluta Günlükler göndermesini sağlayan izleme anahtarına sahip olması gerekir.
Key Vault ' de bir `APPINSIGHTSKEY` gizli dizi belirleyin ve değerini izleme anahtarı olarak ayarlayın. Bunun yapılması uygulamanın Application Insights Günlükler ve ölçümler göndermesini sağlar.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Azure Active Directory için çok faktörlü kimlik doğrulaması uygulama
Yöneticiler, portaldaki abonelik hesaplarının korunduğundan emin olmalıdır. Abonelik, oluşturduğunuz kaynakları yönettiği için saldırılara açıktır. Aboneliği korumak için, aboneliğin **Azure Active Directory** sekmesinde Multi-Factor Authentication 'ı etkinleştirin.

Azure AD, belirli ölçütlere uyan bir kullanıcıya veya kullanıcı grubuna uygulanan ilkelere göre çalışır.
Azure, portalda oturum açmak için yöneticilerin iki öğeli kimlik doğrulamasının gerekli olduğunu belirten bir varsayılan ilke oluşturur.
Bu ilkeyi etkinleştirdikten sonra, Azure portal oturumunuzu kapatıp yeniden açmanız istenebilir.

Yönetici oturum açma işlemleri için MFA 'yı etkinleştirmek için:

1. Azure portal **Azure Active Directory** sekmesine gidin
2. Güvenlik kategorisi altında koşullu erişim ' i seçin. Şu ekranı görürsünüz:

   ![Koşullu erişim-Ilkeler](./media/secure-web-app/ad-mfa-conditional-add.png)

Yeni bir ilke oluşturamıyoruz:

1. **MFA** sekmesine gidin.
2. Ücretsiz denemeye abone olmak için Azure AD Premium **ücretsiz deneme** bağlantısını seçin.

   ![Azure AD Premium ücretsiz deneme sürümü](./media/secure-web-app/ad-trial-premium.png)

Koşullu erişim ekranına geri dönün.

1. Yeni ilke sekmesini seçin.
2. İlke adını girin.
3. MFA 'yı etkinleştirmek istediğiniz kullanıcıları veya grupları seçin.
4. **Erişim denetimleri**altında, **izin** sekmesini seçin ve ardından **Multi-Factor Authentication** (isterseniz diğer ayarlar) iste ' yi seçin.

   ![MFA gerektirme](./media/secure-web-app/ad-mfa-conditional-add.png)

Ekranın üst kısmındaki onay kutusunu seçerek ilkeyi etkinleştirebilir veya **koşullu erişim** sekmesinden bunu yapabilirsiniz. İlke etkinleştirildiğinde, kullanıcıların portalda oturum açması için MFA gerekir.

Tüm Azure yöneticileri için MFA gerektiren bir temel ilke vardır. Bunu portalda hemen etkinleştirebilirsiniz. Bu ilkeyi etkinleştirmek, geçerli oturumu geçersiz kılabilir ve yeniden oturum açmanızı zorlayabilir.

Temel ilke etkinleştirilmemişse:
1.  **Yöneticiler IÇIN MFA gerektir**' i seçin.
2.  **İlkeyi hemen kullan**' ı seçin.

   ![İlkeyi hemen kullan ' ı seçin](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel kullanarak uygulama ve kaynakları izleme

Bir uygulama büyüdükçe, kaynaklardan alınan tüm güvenlik sinyallerinin ve ölçümlerin toplanmasını ve bunları eyleme dayalı bir şekilde yararlı hale getirmek zor olur.

Azure Sentinel, verileri toplamak, olası tehdit türlerini algılamak ve güvenlik olaylarına görünürlük sağlamak için tasarlanmıştır.
El ile müdahale beklerken, Azure Sentinel, uyarıları ve olay yönetimi süreçlerini açmak için önceden yazılmış PlayBook 'ları kullanabilir.

Örnek uygulama, Azure Sentinel 'in izleye, birkaç kaynaktan oluşur.
Azure Sentinel 'i ayarlamak için, önce çeşitli kaynaklardan toplanan tüm verileri depolayan bir Log Analytics çalışma alanı oluşturmanız gerekir.

Bu çalışma alanını oluşturmak için:

1. Azure portal arama kutusunda, **Log Analytics**aratın. **Log Analytics çalışma alanlarını**seçin.

   ![Log Analytics çalışma alanlarını ara](./media/secure-web-app/sentinel-log-analytics.png)

    *Log Analytics çalışma alanlarını ara*

2. Sonraki sayfada **Ekle** ' yi seçin ve ardından çalışma alanı için bir ad, kaynak grubu ve konum sağlayın.
   ![Log Analytics çalışma alanı oluşturma](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Log Analytics çalışma alanı oluşturma*

3. **Azure Sentinel**aramak için arama kutusunu kullanın.

   ![Azure Sentinel 'i arayın](./media/secure-web-app/sentinel-add.png)

    *Azure Sentinel 'i arayın*

4. **Ekle** ' yi seçin ve daha önce oluşturduğunuz Log Analytics çalışma alanını seçin.

   ![Log Analytics çalışma alanı ekleme](./media/secure-web-app/sentinel-workspace-add.png)

    *Log Analytics çalışma alanı ekleme*

5. **Azure Sentinel-veri bağlayıcıları** sayfasında, **yapılandırma**altında, **veri bağlayıcıları**' nı seçin. Azure Sentinel 'de analize yönelik Log Analytics depolama örneğine bağlayabileceğiniz bir Azure hizmetleri dizisi görürsünüz.

   ![Log Analytics veri bağlayıcıları](./media/secure-web-app/sentinel-connectors.png)

    *Azure Sentinel 'e veri Bağlayıcısı ekleme*

   Örneğin, uygulama ağ geçidini bağlamak için şu adımları uygulayın:

   1. Azure Application Gateway örneği dikey penceresini açın.
   2. Altında **izleme**seçin **tanılama ayarları**.
   3. **Tanılama ayarı Ekle**' yi seçin.

      ![Application Gateway tanılamayı Ekle](./media/secure-web-app/sentinel-gateway-connector.png)

      *Application Gateway tanılamayı Ekle*

   4. **Tanılama ayarları** sayfasında, oluşturduğunuz Log Analytics çalışma alanını seçin ve ardından toplamak istediğiniz tüm ölçümleri seçin ve Azure Sentinel 'e gönderin. **Kaydet**’i seçin.

        ![Azure Sentinel bağlayıcı ayarları](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure Sentinel bağlayıcı ayarları*

  Kaynaktaki ölçümler Azure Sentinel ' dir; burada bunları sorgulayabilir ve araştırabilirsiniz.

   Azure Key Vault, genel IP adresi, PostgreSQL için Azure veritabanı ve hesabınızdaki tanılama günlüklerini destekleyen hizmetlerin yanı sıra aynı ölçümleri ekleyin.

Ölçümleri ayarladıktan sonra Azure Sentinel 'in analiz edilecek verileri vardır.

## <a name="evaluate-and-verify"></a>Değerlendirin ve doğrulayın
Mimariyi geliştirip dağıttıktan sonra, kodun ve dağıtılan hizmetlerin güvenlik standartlarını karşıladığından emin olmanız gerekir. Yazılım doğrulamak için uygulayabileceğiniz bazı adımlar şunlardır:

- Statik kod analizi
- Güvenlik açığı taraması
- Uygulama bağımlılıklarında güvenlik açıklarını bulma ve düzeltme

Bunlar, güvenli geliştirmede en iyi uygulamalar için temel yapı taşlarıdır.

### <a name="static-code-analysis"></a>Statik kod analizi
Örnek uygulama için, statik analiz araçlarıyla doğrulama, taınt denetimi ve veri akışı analizi gibi teknikleri kullanarak uygulama kodunda güvenlik açıklarını bulmayı içerir. Python statik analiz araçları, uygulamanızın güvenli olmasını sağlar.

**Linting**

Python bir kitaplık olan PyFlakes, burada gösterildiği gibi uygulamalardan ölü kodu ve kullanılmayan işlevleri kaldırmanıza yardımcı olur:

![PyFlakes](./media/secure-web-app/pyflakes.png)

Çalışma zamanı sırasında kodunuzu temizleyicinizi ve daha az hata haline getirmek için ipuçları ve olası değişiklikler sağlar.

**PyLint**

PyLint bu proje için en çok değer sağladı. Sunucu üzerinde çalışan kodun güvenli olduğundan emin olmak için kod standardı denetimleri, hata denetimi ve yeniden düzenleme ipuçları gerçekleştirir. Kodunuzu güncelleştirmek için PyLint kullanarak, aşağıdaki görüntülerde gösterildiği gibi hataları ortadan kaldırabilir ve Pylınt derecelendirmesini geliştirebilirsiniz.

![PyLint öncesinde](./media/secure-web-app/before-pylint.png)

*PyLint öncesinde*

Yardım araçları tarafından bulunan kod hatalarından bazılarını düzelttikten sonra, kodun hatalara açık olmadığından daha fazla güvenleriniz vardır. Hataların düzeltilmesi, kod üretim ortamlarına dağıtıldığında oluşabilecek güvenlik risklerini önemli ölçüde azaltır.

![Pylint sonrasında](./media/secure-web-app/after-pylint.png)

*PyLint sonrasında*

### <a name="vulnerability-scanning"></a>Güvenlik açığı taraması
[OWASP 'nın zap](https://www.zaproxy.org/) Aracı, güvenlik açıklarına yönelik örnek uygulamayı denetlemek için kullanabileceğiniz açık kaynaklı bir Web uygulaması güvenlik açığı tarayıcılarıdır. Aracı örnek uygulamada çalıştırmak, bazı olası hataları ve saldırı vektörlerini ortaya çıkarır.

![ZAP aracı](./media/secure-web-app/zap-tool.png)

*ZAP aracı*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Uygulama bağımlılıklarındaki güvenlik açıklarını bulma ve çözme
Uygulama bağımlılıklarını bulmak ve onarmak için [OWASP 'Nin bağımlılık denetimini](https://www.owasp.org/index.php/OWASP_Dependency_Check)kullanabilirsiniz.

Güvenlik, bağımlılıkları denetleyen benzer bir uygulamadır. [GitHub](https://github.com/pyupio/safety)'da bulabilirsiniz. İyi bilinen güvenlik açığı veritabanlarında bulunan güvenlik açıkları için güvenlik taramaları.

![Stok](./media/secure-web-app/pysafety.png)

*Stok*

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleler güvenli uygulamalar tasarlamanıza, geliştirmenize ve dağıtmanıza yardımcı olabilir.

- [Tasarıma](secure-design.md)
- [Geliştirme](secure-develop.md)
- [Dağıt](secure-deploy.md)
