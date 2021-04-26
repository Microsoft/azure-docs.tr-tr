---
title: ARM ile uygulamaları öngörülebilir bir şekilde dağıtın
description: Birden çok Azure App Service uygulamayı tek bir birim olarak ve Azure Kaynak Yönetimi şablonlarını ve PowerShell komut dosyasını kullanarak öngörülebilir bir şekilde dağıtmayı öğrenin.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: aec23c28e075dd38fa65f1315f9abd9e21cdc9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951479"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Azure 'da mikro hizmetleri öngörülebilir bir şekilde sağlayın ve dağıtın
Bu öğreticide, tek bir birim olarak [Azure App Service](https://azure.microsoft.com/services/app-service/) [mikro hizmetlerden](https://en.wikipedia.org/wiki/Microservices) oluşan bir uygulamanın nasıl SAĞLANACAĞı ve dağıtılacağı ve JSON kaynak grubu şablonlarının ve PowerShell betiği kullanılarak öngörülebilir bir şekilde dağıtılması gösterilmektedir. 

Yüksek düzeyde ayrılmış mikro hizmetlerden oluşan, yinelenebilirlik ve tahmine dayalı uygulamalar sağlarken ve bu uygulamaların dağıtılmasının başarılı olması çok önemlidir. [Azure App Service](https://azure.microsoft.com/services/app-service/) , Web uygulamaları, mobil arka uçlar ve API uygulamaları içeren mikro hizmetler oluşturmanıza olanak sağlar. [Azure Resource Manager](../azure-resource-manager/management/overview.md) , tüm mikro hizmetleri, veritabanı ve kaynak denetimi ayarları gibi kaynak bağımlılıklarıyla birlikte bir birim olarak yönetmenizi sağlar. Artık, bu tür bir uygulamayı JSON şablonları ve basit PowerShell betiği kullanarak da dağıtabilirsiniz. 

## <a name="what-you-will-do"></a>Yapabilecekleriniz
Öğreticide şunları içeren bir uygulama dağıtacaksınız:

* İki App Service uygulaması (Yani iki mikro hizmet)
* Arka uç SQL veritabanı
* Uygulama ayarları, bağlantı dizeleri ve kaynak denetimi
* Application Insights, uyarılar, otomatik ölçeklendirme ayarları

## <a name="tools-you-will-use"></a>Kullanacağınız Araçlar
Bu öğreticide, aşağıdaki araçları kullanacaksınız. Araçlarla ilgili kapsamlı bir tartışma olmadığından, uçtan uca senaryoya giriş yapacağım ve yalnızca her birine kısa bir giriş ve bu konuda daha fazla bilgi bulabilirsiniz. 

### <a name="azure-resource-manager-templates-json"></a>Azure Resource Manager şablonları (JSON)
Azure App Service bir uygulama oluşturduğunuzda, örneğin, Azure Resource Manager kaynak grubunun tamamını bileşen kaynaklarıyla oluşturmak için bir JSON şablonu kullanır. [Azure Marketi](../marketplace/index.yml) 'ndeki karmaşık bir şablon, veritabanı, depolama hesapları, App Service planı, uygulamanın kendisi, uyarı kuralları, uygulama ayarları, otomatik ölçeklendirme ayarları ve daha fazlasını içerebilir ve tüm bu şablonlar PowerShell üzerinden kullanılabilir. Azure Resource Manager şablonları hakkında daha fazla bilgi için bkz. [yazma Azure Resource Manager şablonları](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Visual Studio için Azure SDK 2,6
En yeni SDK, JSON düzenleyicisinde Kaynak Yöneticisi şablonu desteğiyle iyileştirmeler içerir. Bunu, sıfırdan hızlı bir şekilde bir kaynak grubu şablonu oluşturmak veya var olan bir JSON şablonunu (indirilen Galeri şablonu gibi) açmak, parametreler dosyasını doldurmak ve hatta kaynak grubunu doğrudan bir Azure Kaynak grubu çözümünden dağıtmak için kullanabilirsiniz.

Daha fazla bilgi için bkz. [Visual Studio Için Azure SDK 2,6](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 veya üzeri
Sürüm 0.8.0 ' den başlayarak Azure PowerShell yüklemesi, Azure modülüne ek olarak Azure Resource Manager modülünü içerir. Bu yeni modül, kaynak gruplarının dağıtımını betiğe olanak sağlar.

Daha fazla bilgi için bkz. [Azure Resource Manager ile Azure PowerShell kullanma](../azure-resource-manager/management/manage-resources-powershell.md)

### <a name="azure-resource-explorer"></a>Azure Resource Manager
Bu [Önizleme Aracı](https://resources.azure.com) , aboneliğinizdeki tüm kaynak gruplarının JSON tanımlarını ve bireysel kaynakları araştırmanıza olanak sağlar. Araçta, bir kaynağın JSON tanımlarını düzenleyebilir, kaynakların tüm hiyerarşisini silebilir ve yeni kaynaklar oluşturabilirsiniz.  Bu araçta kullanıma hazır olan bilgiler, şablon yazma için çok yararlıdır, çünkü belirli bir kaynak türü için ayarlamanız gereken özellikleri, doğru değerleri vb. gösterir. Kaynak grubunuzu [Azure portalında](https://portal.azure.com/)da oluşturabilir, ardından kaynak grubunu şablonlarınıza yardımcı olması için gezgın aracında JSON tanımlarını inceleyebilirsiniz.

### <a name="deploy-to-azure-button"></a>Azure'a Dağıt düğmesi
Kaynak denetimi için GitHub kullanıyorsanız, BENIOKU 'nize bir [Azure 'A dağıtım düğmesi](../azure-resource-manager/templates/deploy-to-azure-button.md) koyabilirsiniz. Bu, Azure 'a bir anahtar dağıtımı Kullanıcı arabirimi sağlayan MD. Bunu herhangi bir basit uygulama için yapabilmeniz mümkün olsa da, depo kökündeki bir azuredeploy.jsdosyaya yerleştirerek bir kaynak grubunun tamamını dağıtmaya olanak tanımak için bunu genişletebilirsiniz. Kaynak grubu şablonunu içeren bu JSON dosyası, kaynak grubu oluşturmak için Azure 'a dağıt düğmesine göre kullanılacaktır. Bir örnek için, bu öğreticide kullanacağınız [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) örneğine bakın.

## <a name="get-the-sample-resource-group-template"></a>Örnek kaynak grubu şablonunu al
Şimdi ona hemen bakalım.

1. [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service örneğine gidin.
2. Readme.md ' de **Azure 'A dağıt**' a tıklayın.
3. [Dağıtım parametrelerine](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-appservice-samples%2FToDoApp%2Fmaster%2Fazuredeploy.json) yönlendirilirsiniz ve dağıtım parametrelerini oluşturmanız istenir. Alanların çoğunun Depo adı ve sizin için bazı rastgele dizeler doldurulduğuna dikkat edin. İsterseniz tüm alanları değiştirebilirsiniz, ancak girmeniz gereken tek şey SQL Server yönetici oturum açma ve parola ' **nun ardından İleri**' ye tıklayın.
   
   ![Dağıtım-Azure sitesinde giriş dağıtım parametrelerini gösterir.](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Sonra dağıtım işlemini başlatmak için **Dağıt** ' a tıklayın. İşlem tamamlanana kadar çalıştıktan sonra, http://todoapp dağıtılan uygulamaya gitmek için *XXXX*. azurewebsites.net bağlantısına tıklayın. 
   
   ![Uygulamanızın dağıtım sürecini gösterir.](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Uygulamalar yeni başladığından ve bunu tam işlevli bir uygulama olduğunu ikna ederken Kullanıcı arabirimi biraz daha yavaş olabilir.
5. Dağıtım sayfasına geri döndüğünüzde, yeni uygulamayı Azure portalında görmek için **Yönet** bağlantısına tıklayın.
6. **Essentials** açılan menüsünde kaynak grubu bağlantısına tıklayın. Ayrıca uygulamanın, **dış proje** altında GitHub deposuna zaten bağlı olduğunu unutmayın. 
   
   ![Essentials açılan bölümünde kaynak grubu bağlantısını gösterir.](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Kaynak grubu dikey penceresinde, kaynak grubunda zaten iki uygulama ve bir SQL veritabanı olduğunu unutmayın.
   
   ![Kaynak grubunuzda kullanılabilir kaynakları gösterir.](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Kısa bir süre içinde az önce gördüğünüz her şey, tüm bileşenler, bağımlılıklar, ayarlar, veritabanı ve sürekli yayımlama ile, Azure Resource Manager bir otomatik düzenleme tarafından ayarlanan tamamen dağıtılan iki mikro hizmet uygulamasıdır. Bunların hepsi iki şey tarafından gerçekleştirildi:

* Azure 'a dağıt düğmesi
* Depo kökünde azuredeploy.js

Aynı uygulamayı onlarca, yüzlerce veya binlerce kez dağıtabilir ve her seferinde tam aynı yapılandırmaya sahip olabilirsiniz. Bu yaklaşımın yinelenebilirlik ve öngörülebilirlik, kolay ve güvenle yüksek ölçekli uygulamalar dağıtmanıza olanak sağlar.

## <a name="examine-or-edit-azuredeployjson"></a>AZUREDEPLOY.JSinceleyin (veya düzenleyin)
Şimdi GitHub deposunun nasıl ayarlandığına göz atalım. Azure .NET SDK 'sında JSON düzenleyicisini kullanacaksınız, bu nedenle [Azure .net sdk 2,6](https://azure.microsoft.com/downloads/)' i henüz yüklemediyseniz, şimdi yapın.

1. En sevdiğiniz git aracını kullanarak [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) deposunu kopyalayın. Aşağıdaki ekran görüntüsünde, bunu Visual Studio 2013 Takım Gezgini yapacağım.
   
   ![ToDoApp deposunu kopyalamak için bir git aracının nasıl kullanılacağını gösterir.](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Depo kökünden, Visual Studio 'da azuredeploy.jsaçın. JSON ana hat bölmesini görmüyorsanız Azure .NET SDK 'yı yüklemeniz gerekir.
   
   ![Visual Studio 'da JSON ana hat bölmesini gösterir.](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

JSON biçiminin her ayrıntısını anlatmak istemiyorum, ancak [daha fazla kaynak](#resources) bölümünde kaynak grubu şablon dilini öğrenmek için bağlantılar vardır. Burada, uygulama dağıtımı için kendi özel şablonunuzu yapmaya başlamanıza yardımcı olabilecek ilginç özellikleri göstereceğiz.

### <a name="parameters"></a>Parametreler
Bu parametrelerin büyük bir kısmını **Azure 'A dağıtma** düğmesinin giriş yapmanızı isteyip istemediğinizi görmek için parametreler bölümüne göz atın. **Azure 'A dağıt** düğmesinin arkasındaki site giriş Kullanıcı arabirimini, üzerinde azuredeploy.jstanımlanan parametreleri kullanarak doldurur. Bu parametreler, kaynak adları, özellik değerleri vb. gibi kaynak tanımları boyunca kullanılır.

### <a name="resources"></a>Kaynaklar
Kaynaklar düğümünde, SQL Server bir örnek, App Service planı ve iki uygulama dahil olmak üzere 4 üst düzey kaynağın tanımlandığını görebilirsiniz. 

#### <a name="app-service-plan"></a>App Service planı
JSON içinde basit bir kök düzeyi kaynakla başlayalım. JSON ana hattının karşılık gelen JSON kodunu vurgulamak için **[Hostingplanname]** adlı App Service planına tıklayın. 

![JSON kodunun [hostingPlanName] bölümünü gösterir.](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

`type`Öğesinin bir App Service planı için dizeyi (bir sunucu grubu uzun, uzun bir süre önce denir) belirtir ve diğer öğeler ve ÖZELLIKLER json dosyasında tanımlanan parametreleri kullanarak doldurulmuştur ve bu kaynak, iç içe kaynak içermez.

> [!NOTE]
> Ayrıca değeri, `apiVersion` Azure 'un hangi REST API sürümünün Ile JSON kaynak tanımını kullandığını söyler ve kaynağın içinde nasıl biçimlendirilmesi gerektiğini etkileyebilir `{}` . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Ardından, JSON ana hattından **SqlServer** adlı SQL Server kaynağına tıklayın.

![JSON ana hattından SQLServer adlı SQL Server kaynağını gösterir.](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Vurgulanan JSON kodu hakkında aşağıdakilere dikkat edin:

* Parametrelerin kullanımı, oluşturulan kaynakların bir diğeri ile tutarlı hale gelmesini sağlamak için adlandırılmış ve yapılandırılmış olmasını sağlar.
* SQLServer kaynağında iki iç içe kaynak bulunur, her biri için farklı bir değere sahiptir `type` .
* `“resources”: […]`Veritabanının ve Güvenlik Duvarı kurallarının tanımlandığı iç içe geçmiş kaynaklar, `dependsOn` kök düzeyindeki SqlServer KAYNAĞıNıN kaynak kimliğini belirten bir öğesi vardır. Bu, "Bu kaynağı oluşturmadan önce diğer kaynağın zaten mevcut olması gerektiğini Azure Resource Manager söyler. daha sonra bu diğer kaynak şablonda tanımlanmazsa, önce bunu oluşturun.
  
  > [!NOTE]
  > İşlevini kullanma hakkında ayrıntılı bilgi için `resourceId()` bkz. [Azure Resource Manager şablon işlevleri](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* `dependsOn`Öğesinin etkisi Azure Resource Manager paralel olarak hangi kaynakların oluşturulabileceklerini ve hangi kaynakların sırayla oluşturulması gerektiğini bilmez. 

#### <a name="app-service-app"></a>App Service uygulaması
Şimdi de gerçek uygulamaların kendisine geçelim, daha karmaşıktır. JSON ana hattından JSON kodunu vurgulamak için [Variables (' apiSiteName ')] uygulamasına tıklayın. İşlerin çok daha ilgi çekici olduğunu fark edeceksiniz. Bu amaçla, tek tek özellikler hakkında konuşacağız:

##### <a name="root-resource"></a>Kök kaynak
Uygulama iki farklı kaynağa bağlıdır. Bu, Azure Resource Manager uygulamayı yalnızca App Service planı ve SQL Server örneği oluşturulduktan sonra oluşturacağı anlamına gelir.

![App Service planında ve SQL Server örneğinde uygulama bağımlılıklarını gösterir.](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Uygulama ayarları
Uygulama ayarları, iç içe geçmiş bir kaynak olarak da tanımlanır.

![JSON kodunda iç içe geçmiş kaynak olarak tanımlanan uygulama ayarlarını gösterir.](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

`properties`İçin öğesinde `config/appsettings` , biçiminde iki uygulama ayarı vardır `"<name>" : "<value>"` .

* `PROJECT` , Azure dağıtımına bir çoklu proje Visual Studio çözümünde hangi projenin kullanılacağını söyleyen bir [kudu ayarıdır](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) . Kaynak denetiminin nasıl yapılandırıldığını daha sonra göstereceğiz, ancak ToDoApp kodu çok projeli bir Visual Studio çözümünde olduğundan, bu ayara ihtiyacımız var.
* `clientUrl` yalnızca uygulama kodunun kullandığı bir uygulama ayarıdır.

##### <a name="connection-strings"></a>Bağlantı dizeleri
Bağlantı dizeleri de iç içe geçmiş bir kaynak olarak tanımlanır.

![Bağlantı dizelerinin JSON kodunda iç içe geçmiş kaynak olarak nasıl tanımlandığını gösterir.](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

`properties`İçin öğesinde `config/connectionstrings` , her bağlantı dizesi, belirli biçimiyle bir ad: değer çifti olarak da tanımlanır `"<name>&quot; : {&quot;value&quot;: &quot;…&quot;, &quot;type&quot;: &quot;…&quot;}` . Öğesi için `type` olası değerler,, ve ' dir `MySql` `SQLServer` `SQLAzure` `Custom` .

> [!TIP]
> Bağlantı dizesi türlerinin kesin bir listesi için şu komutu Azure PowerShell: \[ enum]:: GetNames (&quot;Microsoft. WindowsAzure. Commands. Utilities. Web siteleri. Services. WebEntities. DatabaseType") içinde çalıştırın
> 
> 

##### <a name="source-control"></a>Kaynak denetimi
Kaynak denetimi ayarları, iç içe geçmiş kaynak olarak da tanımlanır. Azure Resource Manager, sürekli yayımlamayı yapılandırmak için bu kaynağı kullanır ( `IsManualIntegration` daha sonra bkz. desteklenmediği uyarısıyla) ve ayrıca, JSON dosyasının işlenmesi sırasında uygulama kodu dağıtımını otomatik olarak başlatma.

![Kaynak denetimi ayarlarının JSON kodunda iç içe geçmiş kaynak olarak nasıl tanımlandığını gösterir.](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` ve `branch` oldukça sezgisel olmalıdır ve git deposuna ve yayımlanacak dalın adına işaret etmelidir. Bunlar, giriş parametrelerine göre tanımlanır. 

`dependsOn`Öğesinde, uygulama kaynağının yanı sıra ve ' a `sourcecontrols/web` bağlı olarak da değişir `config/appsettings` `config/connectionstrings` . Bunun nedeni, bir kez `sourcecontrols/web` yapılandırıldıktan sonra Azure dağıtım işleminin uygulama kodunu otomatik olarak dağıtmayı, derlemeyi ve başlatmasını deneyecektir. Bu nedenle, bu bağımlılığı eklemek uygulamanın, uygulama kodu çalıştırılmadan önce gerekli uygulama ayarlarına ve bağlantı dizelerine erişimi olduğundan emin olmanıza yardımcı olur. 

> [!NOTE]
> Ayrıca `IsManualIntegration` , olarak ayarlanır `true` . Bu öğreticide, GitHub deposuna sahip olmadığınız ve bu nedenle [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 'den sürekli yayımlamayı yapılandırmak üzere Azure 'a izin vermediği için bu özellik gereklidir (yani, Azure 'a otomatik depo güncelleştirmeleri gönderin). Belirtilen depo için varsayılan değeri, `false` daha önce [Azure Portal](https://portal.azure.com/) sahibinin GitHub kimlik bilgilerini yapılandırdıysanız kullanabilirsiniz. Diğer bir deyişle, [Azure portalında](https://portal.azure.com/) daha önce Kullanıcı kimlik bilgilerinizi kullanarak herhangi bir uygulama için GitHub veya Bitbucket 'a kaynak denetimi ayarladıysanız, Azure kimlik bilgilerini hatırlayacaksınız ve gelecekte herhangi bir uygulamayı GitHub veya Bitbucket 'ten dağıttığınızda kullanacaktır. Bununla birlikte, bunu yapmadıysanız, Azure Resource Manager uygulamanın kaynak denetimi ayarlarını yapılandırmayı denediğinde, depolama sahibinin kimlik bilgileriyle GitHub veya BitBucket 'ta oturum açabildiğinden JSON şablonunun dağıtılması başarısız olur.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>JSON şablonunu dağıtılan kaynak grubuyla karşılaştırın
Burada, [Azure portalındaki](https://portal.azure.com/)tüm uygulamaların dikey pencerelerini izleyebilirsiniz, ancak daha fazla olmasa da kullanışlı olan başka bir araç vardır. Azure arka ucunda gerçekten mevcut olduğu için aboneliklerinizde bulunan tüm kaynak gruplarının JSON temsilini sağlayan [Azure Kaynak Gezgini](https://resources.azure.com) önizleme aracına gidin. Ayrıca, kaynak grubunun Azure 'daki JSON hiyerarşisinin, onu oluşturmak için kullanılan şablon dosyasındaki hiyerarşiye nasıl karşılık geldiğini de görebilirsiniz.

Örneğin, [Azure Kaynak Gezgini](https://resources.azure.com) aracına gidip gezgin içindeki düğümleri genişlettikten sonra kaynak grubunu ve ilgili kaynak türleri altında toplanan kök düzeyi kaynakları görebiliyorum.

![Genişletilmiş Azure kaynakları gezgin aracında kaynak grubu ve kök düzeyi kaynakları görüntüleyin.](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Bir uygulamanın detayına inmek isterseniz, aşağıdaki ekran görüntüsüne benzer şekilde uygulama yapılandırma ayrıntılarını görmeniz gerekir:

![Uygulamada yapılandırma ayrıntılarını görüntülemek için detaya gidin.](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Ayrıca, iç içe geçmiş kaynaklar JSON şablon dosyanızda bunlara benzer bir hiyerarşiye sahip olmalıdır ve uygulama ayarları, bağlantı dizeleri vb. gibi JSON bölmesine doğru şekilde yansıtılmalıdır. Buradaki ayarların yokluğu, JSON dosyanızda bir sorun olduğunu gösterebilir ve JSON şablon dosyanızda sorun gidermenize yardımcı olabilir.

## <a name="deploy-the-resource-group-template-yourself"></a>Kaynak grubu şablonunu kendiniz dağıtın
**Azure 'A dağıt** düğmesi harika, ancak azuredeploy.jsiçindeki kaynak grubu şablonunu yalnızca GitHub 'a azuredeploy.jszaten göndermiş olmanız halinde dağıtmanıza olanak tanır. Azure .NET SDK 'Sı Ayrıca, tüm JSON şablon dosyalarını doğrudan yerel makinenizden dağıtmanıza yönelik araçlar da sağlar. Bunu yapmak için aşağıdaki adımları izleyin:

1. Visual Studio 'da **Dosya**  >  **Yeni**  >  **Proje**' ye tıklayın.
2. **Visual C#**  >  **Cloud**  >  **Azure Kaynak grubu**' na tıklayın ve ardından **Tamam**' a tıklayın.
   
   ![Azure .NET SDK 'sında Azure Kaynak grubu olarak yeni bir proje oluşturun.](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. **Azure şablonu seç**' te **boş şablon** ' u seçin ve **Tamam**' ı tıklatın.
4. azuredeploy.jsüzerine yeni projenizin **şablon** klasörüne sürükleyin.
   
   ![Dosyadaki azuredeploy.js, projenizin şablon klasörüne sürüklenmesi sonucunu gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Çözüm Gezgini, kopyalanmış azuredeploy.jsüzerinde açın.
6. Yalnızca tanıtım amacıyla, **Kaynak Ekle**' ye tıklayarak JSON dosyanıza bazı standart Application Insight kaynakları ekleyelim. JSON dosyasını dağıtmaya yalnızca ilgileniyorsanız, dağıtma adımlarına atlayın.
   
   ![JSON dosyanıza standart uygulama Insight kaynakları eklemek için kullanabileceğiniz Kaynak Ekle düğmesini gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. **Web Apps için Application Insights** seçin, ardından mevcut bir App Service planının ve uygulamanın seçildiğinden emin olun ve ardından **Ekle**' ye tıklayın.
   
   ![Web Apps, ad, App Service planı ve Web uygulaması için Application Insights seçimini gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Artık, kaynağa ve ne işe bağlı olarak, App Service planında veya uygulamada bağımlılıklar içeren birkaç yeni kaynak görebileceksiniz. Bu kaynaklar mevcut tanımlarıyla etkinleştirilmemiştir ve bunu değiştirirsiniz.
   
   ![App Service planında veya uygulamada bağımlılıklara sahip yeni kaynakları görüntüleyin.](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. JSON ana hattından, JSON kodunu vurgulamak için **Appınsights otomatik ölçeklendirme** ' ye tıklayın. Bu, App Service planınız için ölçeklendirme ayarıdır.
9. Vurgulanan JSON kodunda, `location` ve `enabled` özelliklerini bulun ve aşağıda gösterildiği gibi ayarlayın.
   
   ![Appınsights otomatik ölçeklendirme JSON kodundaki ve ' i ayarlamanız gereken değerlerin konumunu ve etkin özelliklerini gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. JSON ana hattından, JSON kodunu vurgulamak için **Cpuhigh Appınsights** ' a tıklayın. Bu bir uyarıdır.
11. `location`Ve özelliklerini bulun `isEnabled` ve aşağıda gösterildiği gibi ayarlayın. Diğer üç uyarı (mor bulbs) için de aynısını yapın.
    
    ![CPUHigh Appınsights JSON kodundaki ve bunları ayarlamanız gereken değerler için konumu ve IsEnabled özelliklerini gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Şimdi dağıtıma hazırsınız. Projeye sağ tıklayın ve   >  **yeni dağıtım** Dağıt ' ı seçin.
    
    ![Yeni projenizin nasıl dağıtılacağını gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Henüz yapmadıysanız Azure hesabınızda oturum açın.
14. Aboneliğinizde var olan bir kaynak grubu seçin veya yeni bir tane oluşturun, **üzerindeazuredeploy.js** seçin ve ardından **parametreleri Düzenle**' ye tıklayın.
    
    ![Dosyadaki azuredeploy.jsparametrelerin nasıl düzenleneceğini gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Artık, şablon dosyasında tanımlanan tüm parametreleri iyi bir tabloda düzenleyebileceksiniz. Varsayılanları tanımlayan parametreler varsayılan değerlerine sahip olur ve izin verilen değerlerin bir listesini tanımlayan parametreler açılan olarak gösterilir.
    
    ![Açılır listeler olarak izin verilen değerlerin bir listesini tanımlayan parametreleri gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Tüm boş parametreleri doldurup, **Repourl**'de [ToDoApp için GitHub depo adresini](https://github.com/azure-appservice-samples/ToDoApp.git) kullanın. Ardından **Kaydet**' e tıklayın.
    
    ![Dosyadaki azuredeploy.jsiçin yeni doldurulmuş parametreleri gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Otomatik ölçeklendirme, **Standart** katmanda veya daha yüksek bir özelliktir ve plan düzeyi uyarılar **temel** katmanda veya daha yüksek sürümlerde sunulan özelliklerdir, tüm yeni App Insights kaynaklarınızın açık olduğunu görmek için **SKU** parametresini **Standart** veya **Premium** olarak ayarlamanız gerekir.
    > 
    > 
16. **Dağıt**’a tıklayın. **Parolaları kaydet**' i seçtiyseniz, parola parametre dosyasına **düz metin olarak** kaydedilir. Aksi takdirde, dağıtım işlemi sırasında veritabanı parolasını girmek isteyip istemediğiniz sorulur.

Bu kadar! Şimdi, JSON dağıtılan uygulamanıza eklenen yeni uyarıları ve otomatik ölçeklendirme ayarlarını görmek için [Azure portalına](https://portal.azure.com/) ve [Azure Kaynak Gezgini](https://resources.azure.com) aracına gitmeniz yeterlidir.

Bu bölümdeki adımlar genellikle aşağıdakileri yerine gerçekleştirdi:

1. Şablon dosyası hazırlandı
2. Şablon dosyası ile gitmek için bir parametre dosyası oluşturuldu
3. Şablon dosyası parametre dosyasıyla dağıtıldı

Son adım bir PowerShell cmdlet 'i tarafından kolayca yapılır. Uygulamanızı dağıtırken hangi Visual Studio 'Nun olduğunu görmek için Scripts\Deploy-AzureResourceGroup.ps1 açın. Burada çok fazla kod vardır, ancak şablon dosyasını parametre dosyasıyla birlikte dağıtmanız için ihtiyacınız olan tüm ilgili kodu vurgulayacağım.

![Komut dosyasında, şablon dosyasını parametre dosyası ile dağıtmak için kullanmanız gereken ilgili kodu gösterir.](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Son cmdlet 'i, `New-AzureResourceGroup` eylemi gerçekten gerçekleştiren bir şeydir. Bu, araç yardımıyla, bulut uygulamanızı öngörülebilir şekilde dağıtmak için oldukça basittir. Cmdlet 'i aynı şablonda aynı parametre dosyası ile her çalıştırdığınızda aynı sonucu elde edersiniz.

## <a name="summary"></a>Özet
DevOps 'da, yinelenebilirlik ve öngörülebilirlik, mikro hizmetlerden oluşan yüksek ölçekli bir uygulamanın başarılı bir dağıtımına yönelik anahtarlardır. Bu öğreticide, Azure 'da Azure Resource Manager şablonunu kullanarak tek bir kaynak grubu olarak iki mikro bir hizmet uygulaması dağıttı. Azure 'da uygulamanızı bir şablona dönüştürmeye başlamak için ihtiyacınız olan bilgileri size vermiş ve tahmin edilebilir hale getirmek ve dağıtabilir. 

<a name="resources"></a>

## <a name="more-resources"></a>Diğer kaynaklar
* [Azure Resource Manager şablonu dili](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager şablonları yazma](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager şablon Işlevleri](../azure-resource-manager/templates/template-functions.md)
* [Azure Resource Manager şablonuyla uygulama dağıtma](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure PowerShell’i Azure Resource Manager ile kullanma](../azure-resource-manager/management/manage-resources-powershell.md)
* [Azure 'da kaynak grubu dağıtımlarının sorunlarını giderme](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede dağıtılan kaynak türlerinin JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz.:

* [Microsoft. SQL/sunucuları](/azure/templates/microsoft.sql/servers)
* [Microsoft. SQL/Servers/veritabanları](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft. SQL/Servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft. Web/sunucugrupları](/azure/templates/microsoft.web/serverfarms)
* [Microsoft. Web/siteler](/azure/templates/microsoft.web/sites)
* [Microsoft. Web/Sites/Yuvaları](/azure/templates/microsoft.web/sites/slots)
* [Microsoft. Insights/oto scalesettings](/azure/templates/microsoft.insights/autoscalesettings)