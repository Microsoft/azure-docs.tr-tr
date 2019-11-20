---
title: Node.js Başlangıç kılavuzu
description: Basit bir Node.js web uygulaması oluşturma ve Azure bulut hizmetine dağıtma hakkında bilgi edinin.
services: cloud-services
documentationcenter: nodejs
author: georgewallace
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/17/2017
ms.author: gwallace
ms.openlocfilehash: 81ad3bce6c7e331fd67fba60b35de6b8a37c5da9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359536"
---
# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Bir Node.js uygulaması derleme ve Azure Cloud Service’e dağıtma

Bu öğreticide Azure Cloud Service’te çalışan basit bir Node.js uygulamasını oluşturma işlemi gösterilmektedir. Cloud Services, Azure’daki ölçeklenebilir bulut uygulamalarının yapı taşlarıdır. Uygulamanızın ön uç ve arka uç bileşenlerinin ayrılmasına ve bağımsız yönetimi ile ölçek artırımına imkan tanır.  Cloud Services her bir rolü güvenilir bir şekilde barındırmaya yönelik sağlam bir özel sanal makine sağlar.

Cloud Services ve Azure Websites ile Virtual machines hizmetlerine benzerlikleri hakkında daha fazla bilgi için bkz. [Azure Websites, Cloud Services ve Virtual Machines karşılaştırması].

> [!TIP]
> Basit bir web sitesi tasarlamak mı istiyorsunuz? Senaryonuz yalnızca basit bir web sitesi ön ucu içeriyorsa, [basit bir web uygulaması kullanmayı] düşünün. Web uygulamanız büyüdükçe ve gereksinimleriniz değiştikçe kolayca Cloud Services’e yükseltebilirsiniz.

Bu öğreticiyi izleyerek bir web rolünün içinde barındırılan basit bir web uygulaması oluşturacaksınız. Uygulamanızı yerel olarak test etmek ve ardından PowerShell komut satırı araçlarını kullanarak dağıtmak için işlem öykünücüsünü kullanacaksınız.

Uygulama basit bir "hello world" uygulamasıdır:

![Hello World web sayfasını gösteren bir web tarayıcısı][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Önkoşullar
> [!NOTE]
> Bu öğretici Windows gerektiren Azure PowerShell’i kullanır.

* [Azure PowerShell]'i yükleyip yapılandırın.
* [.NET 2.7 için Azure SDK’sını] indirip yükleyin. Yükleme kurulumunda şunları seçin:
  * MicrosoftAzureAuthoringTools
  * MicrosoftAzureComputeEmulator

## <a name="create-an-azure-cloud-service-project"></a>Azure Cloud Service projesi oluşturma
Temel Node.js iskelesiyle birlikte yeni bir Azure Cloud Service projesi oluşturmak için aşağıdaki görevleri gerçekleştirin:

1. **Windows PowerShell**’i Yönetici olarak çalıştırın; **Başlat Menüsü** veya **Başlangıç Ekranı**’ndan **Windows PowerShell** araması yapın.
2. Aboneliğinize [PowerShell’i bağlayın].
3. Projeyi oluşturmak için aşağıdaki PowerShell cmdlet'ini girin:

        New-AzureServiceProject helloworld

    ![New-AzureService helloworld komutunun sonucu][The result of the New-AzureService helloworld command]

    **New-AzureServiceProject** cmdlet’i bir Node.js uygulamasını Cloud Service’te yayımlamaya yönelik basit bir yapı oluşturur. Azure’da yayımlamak için gerekli yapılandırma dosyalarını içerir. Cmdlet ayrıca çalışma dizininizi hizmetin diziniyle değiştirir.

    Cmdlet aşağıdaki dosyaları oluşturur:

   * **ServiceConfiguration. Cloud. cscfg**, **ServiceConfiguration. Local. cscfg** ve **ServiceDefinition. csdef**: Uygulamanızı yayımlamak için gerekli olan Azure 'a özgü dosyalar. Daha fazla bilgi için bkz. [Azure için Barındırılan Hizmet Oluşturmaya Genel Bakış].
   * **Deploymentsettings. JSON**: Azure PowerShell dağıtım cmdlet 'leri tarafından kullanılan yerel ayarları depolar.
4. Yeni bir web rolü eklemek için aşağıdaki komutu girin:

       Add-AzureNodeWebRole

   ![The output of the Add-AzureNodeWebRole command][The output of the Add-AzureNodeWebRole command]

   **Add-AzureNodeWebRole** cmdlet’i basit bir Node.js uygulaması oluşturur. Ayrıca yeni rol için yapılandırma girdileri eklemek üzere **.csfg** ve **.csdef** dosyalarını değiştirir.

   > [!NOTE]
   > Bir rol adı belirtmezseniz varsayılan ad kullanılır. Birinci cmdlet parametresi olarak bir ad sağlayabilirsiniz: `Add-AzureNodeWebRole MyRole`

Node.js uygulaması web rolünün dizininde (varsayılan olarak **WebRole1**) bulunan **server.js** dosyasında tanımlanır. Kod aşağıdaki gibidir:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Bu kod temelde [nodejs.org] web sitesindeki "Hello World" örneğiyle aynıdır, ancak bulut ortamı tarafından atanan bağlantı noktası numarasını kullanır.

## <a name="deploy-the-application-to-azure"></a>Uygulamayı Azure’a dağıtma

> [!NOTE]
> Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ya da [ücretsiz hesap için kaydolabilirsiniz](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="download-the-azure-publishing-settings"></a>Azure yayımlama ayarlarını indirme
Uygulamanızı Azure’a dağıtmak için öncelikle Azure aboneliğinizin yayımlama ayarlarını indirmeniz gerekir.

1. Aşağıdaki Azure PowerShell cmdlet'ini çalıştırın:

       Get-AzurePublishSettingsFile

   Bu işlem, yayımlama ayarları indirme sayfasına gitmek için tarayıcınızı kullanır. Bir Microsoft Hesabı ile oturum açmanız istenebilir. İstenirse Azure aboneliğinizle ilişkili olan hesabı kullanın.

   İndirilen profili kolayca erişebileceğiniz bir dosya konumuna kaydedin.
2. İndirdiğiniz yayımlama profilini içeri aktarmak için aşağıdaki cmdlet'i çalıştırın:

       Import-AzurePublishSettingsFile [path to file]

    > [!NOTE]
    > Yayımlama ayarlarını indirdikten sonra, başka bir kişinin hesabınıza erişmesine imkan tanıyabilecek bilgiler içerdiğinden indirdiğiniz .publishSettings dosyasını silmeyi düşünün.

### <a name="publish-the-application"></a>Uygulamayı yayımlama
Yayımlamak için aşağıdaki komutu çalıştırın:

      $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

* **-ServiceName** dağıtımın adını belirtir. Bu bir benzersiz ad olmalıdır, aksi takdirde yayımlama işlemi başarısız olur. **Get-Date** komutu, adı benzersiz hale getirmesi gereken bir tarih/saat dizesine eklenir.
* **-Location**, uygulamanın barındırılacağı veri merkezini belirtir. Kullanılabilir veri merkezlerinin listesini görmek için **Get-AzureLocation** cmdlet'ini kullanın.
* **-Launch** bir tarayıcı penceresi açar ve dağıtım tamamlandıktan sonra barındırılan hizmete gider.

Yayımlama başarılı olduktan sonra aşağıdakine benzer bir yanıt görürsünüz:

![The output of the Publish-AzureService command][The output of the Publish-AzureService command]

> [!NOTE]
> Uygulamanın dağıtılması ve ilk kez yayımlandığında kullanılabilir olması birkaç dakika sürebilir.

Dağıtım tamamlandıktan sonra bir tarayıcı penceresi açın ve bulut hizmetine gidin.

![Hello world sayfasını gösteren bir tarayıcı penceresi; URL sayfanın Azure’da barındırıldığını gösterir.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Uygulamanız artık Azure üzerinde çalışıyor.

**Publish-AzureServiceProject** cmdlet’i aşağıdaki adımları uygular:

1. Dağıtacağınız bir paket oluşturur. Paket, uygulama klasörünüzdeki tüm dosyaları içerir.
2. Mevcut değilse yeni bir **depolama hesabı** oluşturur. Azure depolama hesabı, dağıtım sırasında uygulama paketini depolamak için kullanılır. Dağıtım bittikten sonra depolama hesabını güvenli bir şekilde silebilirsiniz.
3. Henüz mevcut değilse yeni bir **bulut hizmeti** oluşturur. **Bulut hizmeti** uygulamanızın Azure’a dağıtıldığında barındırıldığı kapsayıcıdır. Daha fazla bilgi için bkz. [Azure için Barındırılan Hizmet Oluşturmaya Genel Bakış].
4. Dağıtım paketini Azure’da yayımlar.

## <a name="stopping-and-deleting-your-application"></a>Uygulamanızı durdurma ve silme
Uygulamanızı dağıttıktan sonra ek maliyetlerden kaçınmak için devre dışı bırakmak isteyebilirsiniz. Azure web rolü örneklerini harcanan sunucu saati başına faturalandırır. Uygulamanız dağıtıldıktan sonra örnekler çalışmadığında ve durdurulmuş halde olduğunda bile sunucu saati harcanır.

1. Windows PowerShell penceresinde önceki bölümde oluşturulan hizmet dağıtımını aşağıdaki cmdlet ile durdurun:

       Stop-AzureService

   Hizmetin durdurulması birkaç dakika sürebilir. Hizmet durdurulduğunda bunu belirten bir ileti alırsınız.

   ![The status of the Stop-AzureService command][The status of the Stop-AzureService command]
2. Hizmeti silmek için aşağıdaki cmdlet'i çağırın:

       Remove-AzureService

   İstendiğinde hizmeti silmek için **Y** yazın.

   Hizmetin silinmesi birkaç dakika sürebilir. Hizmet silindikten sonra bunu belirten bir ileti alırsınız.

   ![The status of the Remove-AzureService command][The status of the Remove-AzureService command]

   > [!NOTE]
   > Hizmetin silinmesi, hizmet ilk kez yayımlandığında oluşturulan depolama hesabını silmez ve kullanılan depolama alanı için faturalandırılmaya devam edersiniz. Depolama alanı başka bir işlem tarafından kullanılmıyorsa silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [Node.js Geliştirici Merkezi].

<!-- URL List -->

[Azure Websites, Cloud Services ve Virtual Machines karşılaştırması]: /azure/architecture/guide/technology-choices/compute-decision-tree
[basit bir web uygulaması kullanmayı]: ../app-service/app-service-web-get-started-nodejs.md
[Azure PowerShell]: /powershell/azureps-cmdlets-docs
[.NET 2.7 için Azure SDK’sını]: https://www.microsoft.com/en-us/download/details.aspx?id=48178
[PowerShell’i bağlayın]: /powershell/azureps-cmdlets-docs
[nodejs.org]: https://nodejs.org/
[Azure için Barındırılan Hizmet Oluşturmaya Genel Bakış]: https://azure.microsoft.com/documentation/services/cloud-services/
[Node.js Geliştirici Merkezi]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
