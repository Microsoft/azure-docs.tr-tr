---
title: Chef ile Azure sanal makine dağıtımı
description: Chef kullanarak Microsoft Azure otomatik sanal makine dağıtımı ve yapılandırması yapma hakkında bilgi edinin
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 58642cdbf164523390d5e4925290b43f6c05549b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039555"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Chef ile Azure sanal makine dağıtımını otomatikleştirme

Chef, otomasyon ve istenen durum yapılandırmalarının gönderimi için harika bir araçtır.

En son bulut API sürümü sayesinde Chef, Azure ile sorunsuz tümleştirme sağlarken, tek bir komut aracılığıyla yapılandırma durumlarını sağlama ve dağıtma olanağı sunar.

Bu makalede, Azure sanal makinelerini sağlamak ve bir ilke veya "tanımlama kitabı" oluşturup bu tanıtım rehberini bir Azure sanal makinesine dağıtmak için Chef ortamınızı ayarlarsınız.

## <a name="chef-basics"></a>Chef temelleri
Başlamadan önce, [Chef 'in temel kavramlarını gözden geçirin](https://www.chef.io/chef).

Aşağıdaki diyagramda, üst düzey Chef mimarisi gösterilmektedir.

![][2]

Chef 'in üç ana mimari bileşeni vardır: Chef Server, Chef Client (node) ve Chef Workstation.

Chef sunucusu yönetim noktasıdır ve Chef sunucusu için iki seçenek vardır: barındırılan bir çözüm veya şirket içi bir çözüm.

Chef Istemcisi (node), yönettiğiniz sunucularda bulunan aracıdır.

İlke oluşturduğunuz ve yönetim komutlarını ve Chef araçlarının yazılım paketini yürütebileceğiniz yönetici iş istasyonunun adı olan Chef Iş Istasyonu.

Genellikle, _iş istasyonunuzu_ yazılım paketi için eylemleri ve _Chef iş istasyonunu_ gerçekleştirdiğiniz konum olarak görürsünüz.
Örneğin, bıis komutunu _Chef Iş istasyonunun_bir parçası olarak indirirsiniz, ancak altyapıyı yönetmek için _iş istasyonunuzdan_ bıı komutlarını çalıştırırsınız.

Chef Ayrıca, tanımlandığımız ve sunucular için uygulama yaptığımız ilkeler olan "yemek defterleri" ve "yemek tarifleri" kavramlarını da kullanır.

## <a name="preparing-your-workstation"></a>İş istasyonunuzu hazırlama

İlk olarak, Chef yapılandırma dosyalarını ve tanıtım defterlerini depolamak için bir dizin oluşturarak iş istasyonunuzu hazırlayın.

C:\chefadlı bir dizin oluşturun.

En son [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) sürümünü iş istasyonunuza indirin ve yükleyin.

## <a name="configure-azure-service-principal"></a>Azure hizmet sorumlusunu yapılandırma

En basit şartlar ve Azure hizmet sorumlusu bir hizmet hesabıdır.   Chef Iş Istasyonumuzdan Azure kaynakları oluşturmamıza yardımcı olmak için bir hizmet sorumlusu kullanacağız.  Gerekli izinlerle ilgili hizmet sorumlusunu oluşturmak için PowerShell 'de aşağıdaki komutları çalıştırmanız gerekir:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Lütfen SubscriptionID, Tenantıd, ClientID ve Istemci gizli anahtarını (yukarıdaki ayarladığınız parola) bir yere göz atın. bu işlemin daha sonra olması gerekir. 

## <a name="setup-chef-server"></a>Chef sunucusu kurma

Bu kılavuzda, barındırılan Chef 'e kaydolmanız varsayılmaktadır.

Zaten bir Chef sunucusu kullanmıyorsanız şunları yapabilirsiniz:

* Chef ile çalışmaya başlamanın en hızlı yolu olan [barındırılan Chef](https://manage.chef.io/signup)'e kaydolun.
* [Chef belgelerinden](https://docs.chef.io/) [yükleme yönergelerini](https://docs.chef.io/install_server.html) izleyerek Linux tabanlı makineye tek başına Chef sunucusu yükleme.

### <a name="creating-a-hosted-chef-account"></a>Barındırılan Chef hesabı oluşturma

[Burada](https://manage.chef.io/signup)barındırılan bir Chef hesabına kaydolun.

Kaydolma işlemi sırasında yeni bir kuruluş oluşturmanız istenir.

![][3]

Kuruluşunuz oluşturulduktan sonra, başlangıç paketini indirin.

![][4]

> [!NOTE]
> Anahtarlarınızın sıfırlandığını bildiren bir uyarı alırsanız, henüz var olan bir altyapı yapılandırıldığımızda devam etmeniz normaldir.
>

Bu başlatıcı seti ZIP dosyası `.chef` dizinindeki kuruluş yapılandırma dosyalarınızı ve Kullanıcı anahtarınızı içerir.

`organization-validator.pem`, özel bir anahtar olduğu ve özel anahtarların Chef sunucusunda depolanmamalıdır olması nedeniyle ayrı olarak indirilmelidir. [Chef Yönet](https://manage.chef.io/)'ten Yönetim bölümüne gidin ve ayrı olarak indirmeniz için bir dosya sağlayan "doğrulama anahtarını Sıfırla" yı seçin. Dosyayı c:\chefdizinine kaydedin.

### <a name="configuring-your-chef-workstation"></a>Chef iş istasyonunuzu yapılandırma

Chef-Starter. zip içeriğini c:\chefdizinine ayıklayın.

Chef-starter\chef-repo\.Chef altındaki tüm dosyaları c:\chef dizininize kopyalayın.

C:\Downloads klasörüne kaydedilirse `organization-validator.pem` dosyasını c:\chef dizinine kopyalayın

Dizininiz artık aşağıdaki örneğe benzer şekilde görünmelidir.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Artık c:\chefroot dizininde beş dosya ve dört dizin (boş Chef-repo dizini dahil) olmalıdır.

### <a name="edit-kniferb"></a>Bıçak. RB 'yi Düzenle

PEK dosyaları, iletişim için kuruluşunuzu ve yönetim özel anahtarlarını içerir ve bıçak. rb dosyası bıçak yapılandırmanızı içerir. Bıçak. RB dosyasını düzenlememiz gerekir.

Bıçak. RB dosyasını istediğiniz düzenleyicide açın. Değiştirilmemiş dosya şuna benzemelidir:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Aşağıdaki bilgileri bıçak. RB 'nize ekleyin:

validation_client_name "MyOrg-Validator"

validation_key "# {current_dir}/MyOrg.exe"

bıçak [: azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"

bıçak [: azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"

bıçak [: azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"

bıçak [: azure_client_secret] = "#1234p $wdchef 19"


Bu satırlar, bıçak 'nin c:\chef\pişirbook altında bulunan kitaplar dizinine başvurduğundan ve ayrıca Azure işlemleri sırasında oluşturduğunuz Azure hizmet sorumlusunu kullandığından emin olur.

Bıçak. RB dosyanız şimdi aşağıdaki örneğe benzer şekilde görünmelidir:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Chef Iş Istasyonunu yükler

Sonra Chef Iş Istasyonunu [indirip yükleyin](https://downloads.chef.io/chef-workstation/) .
Chef Iş Istasyonunu varsayılan konum olarak yükler. Bu yükleme birkaç dakika sürebilir.

Masaüstünde, Chef ürünleriyle etkileşimde bulunmak için ihtiyacınız olan araçla yüklenmiş bir ortam olan "CW PowerShell" i görürsünüz. FA PowerShell, `chef`gibi geleneksel Chef CLı komutlarının yanı sıra `chef-run` gibi yeni geçici komutlar sağlar. Yüklü Chef Iş Istasyonu sürümünüzü ve `chef -v`ile Chef araçlarını inceleyin. Ayrıca, Chef Iş Istasyonu uygulamasındaki "Chef Workstation" seçeneğini belirleyerek Iş Istasyonu sürümünüzü kontrol edebilirsiniz.

`chef --version` şöyle bir şey döndürmelidir:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> Yolun sırası önemlidir! Opscode yollarınız doğru sırada değilse sorunlarla karşılaşacaktır.
>

Devam etmeden önce iş istasyonunuzu yeniden başlatın.

### <a name="install-knife-azure"></a>Bıçak Azure 'ı yükler

Bu öğreticide, sanal makineli etkileşimde bulunmak için Azure Resource Manager kullandığınızı varsayılmaktadır.

Bıçak Azure uzantısını yükler. Bu, "Azure eklentisi" ile bıçak sağlar.

Aşağıdaki komutu çalıştırın.

    chef gem install knife-azure ––pre

> [!NOTE]
> – Pre bağımsız değişkeni, en son API kümesine erişim sağlayan bıçak Azure eklentisinin en son RC sürümünü almanızı sağlar.
>
>

Aynı zamanda aynı anda bir dizi bağımlılığı da yüklenecektir.

![][8]

Her şeyin doğru şekilde yapılandırıldığından emin olmak için aşağıdaki komutu çalıştırın.

    knife azurerm server list

Her şey doğru yapılandırılmışsa, kullanılabilecek Azure görüntülerinin bir listesini görürsünüz.

Tebrikler. İş istasyonunuz ayarlandı!

## <a name="creating-a-cookbook"></a>Bir tanımlama kitabı oluşturma

Bir tanımlama kitabı, yönetilen istemcinizdeki yürütmek istediğiniz bir komut kümesini tanımlamak için Chef tarafından kullanılır. Bir tanımlama kitabı oluşturmak basit, tek yapmanız gereken, tanımlama kitabı şablonunu oluşturmak için **Chef tanıtım rehberini** oluşturma komutunu kullanmaktır. Bu kılavuz kitabı, IIS 'yi otomatik olarak dağıtan bir Web sunucusu içindir.

C:\Chef dizininiz altında aşağıdaki komutu çalıştırın.

    chef generate cookbook webserver

Bu komut, C:\chef\pişirbooks\webserverdizininde bir dosya kümesi oluşturur. Ardından, Chef istemcisinin yönetilen sanal makinede yürütmesi için komut kümesi tanımlayın.

Komutlar default. RB dosyasında depolanır. Bu dosyada, IIS 'yi yükleyen bir komutlar kümesi tanımlayın, IIS 'yi başlatır ve bir şablon dosyasını Wwwroot klasörüne kopyalar.

C:\chef\cookbooks\webserver\recipes\default.RB dosyasını değiştirin ve aşağıdaki satırları ekleyin.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

İşiniz bittiğinde dosyayı kaydedin.

## <a name="creating-a-template"></a>Şablon oluşturma
Bu adımda, varsayılan. html sayfası olarak kullanılacak bir şablon dosyası oluşturacaksınız.

Şablonu oluşturmak için aşağıdaki komutu çalıştırın:

    chef generate template webserver Default.htm

`C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` dosyasına gidin. Daha basit bir "Merhaba Dünya" HTML kodu ekleyerek dosyayı düzenleyin ve ardından dosyayı kaydedin.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Tanıtım rehberini Chef sunucusuna yükleme
Bu adımda, yerel makinede oluşturduğunuz tanıtım rehberinin bir kopyasını oluşturursunuz ve bu dosyayı Chef barındırılan sunucusuna yüklersiniz. Karşıya yüklendikten sonra, tanımlama kitabı **ilke** sekmesinin altında görüntülenir.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Bıçak Azure ile sanal makine dağıtma
Bir Azure sanal makinesini dağıtın ve IIS Web hizmetini ve varsayılan Web sayfasını yükleyecek "Web sunucusu" tanımlama kitabı ' nı uygulayın.

Bunu yapmak için **bıçak azurere sunucu oluştur** komutunu kullanın.

Sonraki bir komutun örneği görüntülenir.

    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"


Yukarıdaki örnekte, Batı ABD bölgesinde Windows Server 2016 yüklü Standard_DS2_v2 bir sanal makine oluşturulur. Belirli değişkenlerinizi değiştirin ve çalıştırın.

> [!NOTE]
> Komut satırı ile, – TCP-endpoints parametresini kullanarak uç nokta ağı filtre kurallarınızı otomatikleştirdim. Web sayfası ve RDP oturumuna erişim sağlamak için 80 ve 3389 bağlantı noktalarını açdım.
>
>

Komutu çalıştırdığınızda, makinenizin sağlamaya başlamasını görmek için Azure portal gidin.

![][15]

Sonraki komut istemi görüntülenir.

![][16]

Dağıtım tamamlandıktan sonra, yeni sanal makinenin genel IP adresi dağıtımın tamamlanmasında görüntülenir, bunu kopyalayabilir ve bir Web tarayıcısına yapıştırabilir ve dağıttığınız Web sitesini görüntüleyebilirsiniz. Sanal makineyi dağıttığımız için bağlantı noktası 80 ' i açtık, dışarıdan kullanılabilir olmalıdır.   

![][11]

Bu örnek, Creative HTML kodunu kullanır.

Ayrıca, düğümün durum [Chef Manage](https://manage.chef.io/)' i görüntüleyebilirsiniz. 

![][17]

3389 numaralı bağlantı noktası üzerinden Azure portal bir RDP oturumu aracılığıyla da bağlanabilirsiniz.

Teşekkür ederiz! Azure 'la bugün kod yolculuğuna gidin ve altyapınızı başlatın!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
