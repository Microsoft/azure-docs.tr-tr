---
title: Azure Cloud Services ve ASP.NET kullanmaya başlama | Microsoft Belgeleri
description: ASP.NET MVC ve Azure kullanarak çok katmanlı bir uygulama oluşturma hakkında bilgi edinin. Uygulama, web rolü ve çalışan rolü ile birlikte bir bulut hizmetinde çalışır. Entity Framework, SQL Database ve Azure Storage kuyruklarını ve blob’larını kullanır.
services: cloud-services, storage
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: gwallace
ms.openlocfilehash: 3f2c60be29d679d0b0d30b6bf471f083c66ba93f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827663"
---
# <a name="get-started-with-azure-cloud-services-and-aspnet"></a>Azure Cloud Services ve ASP.NET kullanmaya başlama

## <a name="overview"></a>Genel Bakış
Bu öğreticide ASP.NET MVC ön ucuyla çok katmanlı bir .NET uygulaması oluşturma ve bir [Azure bulut hizmetine](cloud-services-choose-me.md) dağıtma işlemi gösterilmektedir. Uygulama [Azure SQL Veritabanı](/previous-versions/azure/ee336279(v=azure.100)), [Azure Blob hizmeti](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) ve [Azure Queue hizmeti](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) kullanır. MSDN Kod Galerisi’nden [Visual Studio projesini](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) indirebilirsiniz.

Öğreticide, uygulamayı yerel olarak oluşturup çalıştırma, Azure’a dağıtma ve bulutta çalıştırmanın yanı sıra sıfırdan oluşturma işlemleri de gösterilmektedir. Tercih ederseniz sıfırdan oluşturmaya başlayabilir ve ardından test ve dağıtım adımlarını gerçekleştirebilirsiniz.

## <a name="contoso-ads-application"></a>Contoso Ads uygulaması
Uygulama bir reklam bülteni panosudur. Kullanıcılar metin girerek ve görüntüyü karşıya yükleyerek bir reklam oluşturur. Küçük resim görüntüleriyle birlikte bir reklam listesi görebilir ve ayrıntılarını görmek üzere bir reklam seçtiklerinde tam boyutlu görüntüyü görebilirler.

![Reklam listesi](./media/cloud-services-dotnet-get-started/list.png)

Uygulama bir arka uç işleminde küçük resim oluşturmaya yönelik CPU yoğunluklu iş yükünü azaltmak üzere [kuyruk merkezli çalışma deseni](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) kullanır.

## <a name="alternative-architecture-app-service-and-webjobs"></a>Alternatif mimari: App Service ve Web Işleri
Bu öğreticide bir Azure bulut hizmetinde hem ön ucun hem de arka ucun nasıl çalıştırılacağı gösterilmektedir. Diğer bir seçenek de ön ucu [Azure App Service](/azure/app-service/) çalıştırmak ve arka uç Için [WebJobs](https://go.microsoft.com/fwlink/?LinkId=390226) özelliğini kullanmaktır. WebJobs kullanan bir öğretici için bkz. [Azure WebJobs SDK ile Çalışmaya Başlama](https://github.com/Azure/azure-webjobs-sdk/wiki). Senaryonuza en uygun hizmetleri seçme hakkında daha fazla bilgi için bkz. [Azure App Service, Cloud Services ve sanal makineler karşılaştırması](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="what-youll-learn"></a>Öğrenecekleriniz
* Azure SDK’sını yükleyerek Azure dağıtımı için makinenizi etkinleştirme.
* Bir ASP.NET MVC web rolü ve çalışan rolü ile Visual Studio bulut hizmeti projesi oluşturma.
* Azure Storage öykünücüsü kullanarak bulut hizmeti projesini yerel olarak test etme.
* Bulut projesini bir Azure bulut hizmetinde yayımlama ve Azure Storage hesabını kullanarak test etme.
* Dosyaları karşıya yükleme ve Azure Blob hizmetine depolama.
* Katmanlar arasında iletişim için Azure Queue hizmetini kullanma.

## <a name="prerequisites"></a>Önkoşullar
Öğretici *web rolü* ve *çalışan rolü* terminolojisi gibi [Azure bulut hizmetleri hakkında temel kavramları](cloud-services-choose-me.md) anladığınızı varsayar.  Ayrıca Visual Studio’da [ASP.NET MVC](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) veya [Web Forms](https://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) projeleri ile nasıl çalışılacağını bildiğinizi varsayar. Örnek uygulama MVC kullanır, ancak öğreticinin büyük bölümü Web Forms için de geçerlidir.

Uygulamayı bir Azure aboneliği olmadan yerel olarak çalıştırabilirsiniz, ancak uygulamayı buluta dağıtmak için bir abonelik gerekecektir. Bir hesabınız yoksa, [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) veya [ücretsiz deneme için kaydolabilirsiniz.](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A55E3C668)

Öğretici yönergeleri aşağıdaki ürünlerden biriyle çalışır:

* Visual Studio 2013
* Visual Studio 2015
* Visual Studio 2017
* Visual Studio 2019

Bunlardan birine sahip değilseniz Azure SDK'yı yüklediğinizde Visual Studio otomatik olarak yüklenebilir.

## <a name="application-architecture"></a>Uygulama mimarisi
Uygulama, tablolar oluşturmak ve verilere erişmek için Entity Framework Code First kullanarak reklamları bir SQL veritabanına depolar. Her reklam için veritabanı, biri tam boyutlu görüntü ve diğeri küçük resim olmak üzere iki URL depolar.

![Reklam tablosu](./media/cloud-services-dotnet-get-started/adtable.png)

Bir kullanıcı görüntü yüklediğinde bir web rolünde çalışan ön uç görüntüyü bir [Azure blob](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)’a depolar ve reklam bilgilerini blob’u işaret eden bir URL ile birlikte veritabanına depolar. Aynı zamanda bir Azure kuyruğuna ileti yazar. Bir çalışan rolünde çalışan arka uç işlemi, kuyruğu yeni iletiler için düzenli olarak yoklar. Yeni bir ileti görüntülendiğinde çalışan rolü bu görüntü için bir küçük resim oluşturur ve küçük resim URL'si veritabanı alanını bu reklam için güncelleştirir. Aşağıdaki diyagramda uygulama bölümlerinin nasıl etkileşim kurduğu gösterilmektedir.

![Contoso Ads mimarisi](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>Tamamlanan çözümü indirme ve çalıştırma
1. [Tamamlanan çözümü](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) indirip sıkıştırmasını açın.
2. Visual Studio’yu çalıştırın.
3. **Dosya** menüsünden **Proje Aç**’ı seçin, çözümü indirdiğiniz yere gidin ve ardından çözüm dosyasını açın.
4. Çözümü derlemek için CTRL+SHIFT+B'ye basın.

    Varsayılan olarak Visual Studio, *.zip* dosyasına dahil edilmeyen NuGet paketini otomatik olarak geri yükler. Paketler geri yüklenmezse **Çözüm için NuGet Paketlerini Yönet** iletişim kutusuna gidip sağ üst köşedeki **Geri Yükle** düğmesine tıklayarak el ile yükleyin.
5. **Çözüm Gezgini**’nde başlangıç projesi olarak **ContosoAdsCloudService** öğesinin seçildiğinden emin olun.
6. Visual Studio 2015 veya sonraki bir sürümü kullanıyorsanız ContosoAdsWeb projesinin uygulama *Web.config* dosyasında SQL Server bağlantı dizesini ve ContosoAdsCloudService projesinin *ServiceConfiguration.Local.cscfg* dosyasını değiştirin. Her iki örnekte de "(localdb)\v11.0" seçeneğini "(localdb)\MSSQLLocalDB" olarak değiştirin.
7. Uygulamayı çalıştırmak için CTRL+F5'e basın.

    Bir bulut hizmeti projesini yerel olarak çalıştırdığınızda Visual Studio, Azure *işlem öykünücüsü* ve Azure *depolama öykünücüsünü* otomatik olarak çağırır. İşlem öykünücüsü, web rolü ve çalışan rolü ortamlarını benzetmek için bilgisayarınızın kaynaklarını kullanır. Depolama öykünücüsü Azure bulut depolamayı benzetmek için bir [SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) veritabanı kullanır.

    Bir bulut hizmeti projesini ilk kez çalıştırdığınızda öykünücülerin başlatılması yaklaşık bir dakika sürer. Öykünücü başlatma tamamlandığında varsayılan tarayıcıda uygulama giriş sayfası açılır.

    ![Contoso Ads mimarisi](./media/cloud-services-dotnet-get-started/home.png)
8. **Reklam Oluştur**'a tıklayın.
9. Bazı test verilerini girin ve karşıya yüklenecek bir *.jpg* görüntüsü seçip **Oluştur**’a tıklayın.

    ![Sayfa oluşturma](./media/cloud-services-dotnet-get-started/create.png)

    Uygulama Dizin sayfasına gider, ancak işleme henüz tamamlanmadığından yeni reklam için bir küçük resim göstermez.
10. Biraz bekleyin ve ardından küçük resmi görmek için Dizin sayfasını yenileyin.

     ![Dizin sayfası](./media/cloud-services-dotnet-get-started/list.png)
11. Tam boyutlu görüntüyü görmek için reklamınıza ilişkin **Ayrıntılar**’a tıklayın.

     ![Ayrıntılar sayfası](./media/cloud-services-dotnet-get-started/details.png)

Uygulamayı herhangi bir bulut bağlantısı olmadan tamamen yerel bilgisayarınızda çalıştırıyorsunuz. Depolama öykünücüsü kuyruk ve blob verilerini bir SQL Server Express LocalDB veritabanına, uygulama ise reklam verilerini başka bir LocalDB veritabanına depolar. Entity Framework Code First, web uygulaması ilk kez erişmeye çalıştığında reklam veritabanını otomatik olarak oluşturmuştur.

Aşağıdaki bölümde çözümü bulutta çalışan kuyruklar, blob’lar ve uygulama veritabanı için Azure bulut kaynakları kullanacak şekilde yapılandıracaksınız. Yerel olarak çalıştırmaya devam ederken bulut depolama alanını ve veritabanı kaynaklarını da kullanmak istiyorsanız bunu yapabilirsiniz. Bunun için yalnızca bağlantı dizesi ayarlarını yapmanız gerekir. Bu ayarları nasıl yapacağınızı ileride öğreneceksiniz.

## <a name="deploy-the-application-to-azure"></a>Uygulamayı Azure’a dağıtma
Uygulamayı bulutta çalıştırmak için aşağıdaki adımları gerçekleştirin:

* Bir Azure bulut hizmeti oluşturun.
* Bir Azure SQL veritabanı oluşturun.
* Bir Azure Storage hesabı oluşturun.
* Çözümünüzü Azure’da çalıştığında Azure SQL veritabanınızı kullanacak şekilde yapılandırın.
* Çözümünüzü Azure’da çalıştığında Azure Storage hesabınızı kullanacak şekilde yapılandırın.
* Projeyi Azure bulut hizmetinize dağıtın.

### <a name="create-an-azure-cloud-service"></a>Bir Azure bulut hizmeti oluşturma
Azure bulut hizmeti, uygulamanın çalıştırılacağı ortamıdır.

1. Tarayıcınızda [Azure portalı](https://portal.azure.com)’nı açın.
2. **Kaynak oluştur > Hesapla > Bulut Hizmeti**’ne tıklayın.

3. DNS adı giriş kutusuna bulut hizmeti için bir URL ön eki girin.

    Bu URL benzersiz olmalıdır.  Seçtiğiniz ön ek zaten kullanılıyorsa bir hata iletisi alırsınız.
4. Hizmet için yeni bir Kaynak grubu belirtin. **Yeni oluştur**’a tıklayın ve Kaynak grubu giriş kutusuna CS_contososadsRG gibi bir ad yazın.

5. Uygulamayı dağıtmak istediğiniz bölgeyi seçin.

    Bu alan, bulut hizmetinizin hangi veri merkezinde barındırılacağını belirtir. Bir üretim uygulaması için müşterilerinize en yakın bölgeyi seçmeniz gerekir. Bu öğretici için size en yakın bölgeyi seçin.
5.           **Oluştur**'a tıklayın.

    Aşağıdaki görüntüde bulut hizmeti CSvccontosoads.cloudapp.net URL’si ile oluşturulur.

    ![Yeni Bulut Hizmeti](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-an-azure-sql-database"></a>Bir Azure SQL veritabanı oluşturma
Uygulama bulutta çalıştırıldığında bulut tabanlı bir veritabanı kullanır.

1. [Azure portalı](https://portal.azure.com)’nda **Kaynak oluştur > Veritabanları > SQL Veritabanı**’na tıklayın.
2. **Veritabanı Adı** kutusuna *contosoads* yazın.
3. **Kaynak grubu**’nda **Var olanı kullan**’a tıklayın ve bulut hizmeti için kullanılan kaynak grubunu seçin.
4. Aşağıdaki görüntüde, **Sunucu - Gerekli ayarları yapılandır**’a ve **Yeni sunucu oluştur**’a tıklayın.

    ![Veritabanı sunucusu için tünel](./media/cloud-services-dotnet-get-started/newdb.png)

    Alternatif olarak, aboneliğiniz zaten bir sunucuya sahipse aşağı açılan listeden bu sunucuyu seçebilirsiniz.
5. **Sunucu adı** kutusuna *csvccontosodbserver*’ı girin.

6. Bir yönetici için **Kullanıcı Adı** ve **Parola** girin.

    **Yeni sunucu oluştur**’u seçtiyseniz buraya mevcut bir adı ve parolayı girmezsiniz. Daha sonra veritabanına eriştiğinizde kullanmak için şu anda tanımladığınız bir adı ve parolayı girersiniz. Daha önce oluşturduğunuz bir sunucuyu seçtiyseniz önceden oluşturduğunuz yönetici kullanıcı hesabı için parola istenir.
7. Bulut hizmeti için seçtiğiniz aynı **Konum**’u seçin.

    Bulut hizmeti ve veritabanı farklı veri merkezlerinde (farklı bölgelerde) olduğunda gecikme artar ve veri merkezinin dışındaki bant genişliği için sizden ücret alınır. Bir veri merkezi içinde bant genişliği ücretsizdir.
8. **Azure hizmetlerinin sunucuya erişmesine izin ver** seçeneğini işaretleyin.
9. Yeni sunucu için **Seçin**’e tıklayın.

    ![Yeni SQL Veritabanı sunucusu](./media/cloud-services-dotnet-get-started/newdbserver.png)
10.           **Oluştur**'a tıklayın.

### <a name="create-an-azure-storage-account"></a>Azure Storage hesabı oluşturma
Azure Storage hesabı kuyruk ve blob verilerini buluta depolamaya yönelik kaynaklar sağlar.

Gerçek bir uygulamada genellikle uygulama verilerine karşı günlük verileri için ve test verilerine karşı üretim verileri için ayrı hesaplar oluşturursunuz. Bu öğreticide yalnızca tek bir hesap kullanacaksınız.

1. [Azure portalı](https://portal.azure.com)’nda **Kaynak oluştur > Depolama > Depolama hesabı - blob, dosya, tablo, kuyruk**’a tıklayın.
2. **Ad** kutusuna bir URL ön eki girin.

    Bu ön ek ile birlikte kutunun altında gördüğünüz metin, depolama hesabınızın benzersiz URL’si olacaktır. Girdiğiniz ön ek başka bir kişi tarafından zaten kullanılıyorsa farklı bir ön ek seçmeniz gerekir.
3. **Dağıtım modeli**’ni *Klasik* olarak ayarlayın.

4. **Çoğaltma** açılır listesini **Yerel olarak yedekli depolama** olarak ayarlayın.

    Bir depolama hesabı için coğrafi çoğaltma etkinleştirildiğinde, birincil konumda önemli bir olağanüstü durum oluşursa yük devretmeyi etkinleştirmek için depolanan içerik ikincil bir veri merkezine çoğaltılır. Coğrafi çoğaltma ek ücretlere neden olabilir. Test ve geliştirme hesaplarında genellikle coğrafi çoğaltma için ödeme yapmak istemezsiniz. Daha fazla bilgi için bkz. [Depolama hesabı oluşturma, yönetme veya silme](../storage/common/storage-create-storage-account.md).

5. **Kaynak grubu**’nda **Var olanı kullan**’a tıklayın ve bulut hizmeti için kullanılan kaynak grubunu seçin.
6. **Konum** açılır listesini bulut hizmeti için seçtiğiniz aynı bölgeye ayarlayın.

    Bulut hizmeti ve depolama hesabı farklı veri merkezlerinde (farklı bölgelerde) olduğunda gecikme artar ve veri merkezinin dışındaki bant genişliği için sizden ücret alınır. Bir veri merkezi içinde bant genişliği ücretsizdir.

    Azure benzeşim grupları bir veri merkezinde bulunan kaynaklar arasındaki uzaklığı en aza indirmeye yönelik bir mekanizma sağlar. Bu öğretici benzeşim gruplarını kullanmaz. Daha fazla bilgi için bkz. [Azure’da Benzeşim Grubu Oluşturma](/previous-versions/azure/reference/gg715317(v=azure.100)).
7.           **Oluştur**'a tıklayın.

    ![Yeni depolama hesabı](./media/cloud-services-dotnet-get-started/newstorage.png)

    Görüntüde `csvccontosoads.core.windows.net` URL’si ile bir depolama hesabı oluşturulmuştur.

### <a name="configure-the-solution-to-use-your-azure-sql-database-when-it-runs-in-azure"></a>Çözümünüzü Azure’da çalıştığında Azure SQL veritabanınızı kullanacak şekilde yapılandırma
Web projesi ve çalışan rolü projelerinin her biri kendi veritabanı bağlantı dizesine sahiptir ve uygulama Azure'da çalıştırıldığında her birinin Azure SQL veritabanına işaret etmesi gerekir.

Web rolü için bir [Web.config dönüşümü](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) ve çalışan rolü için bir bulut hizmet ortamı ayarı kullanacaksınız.

> [!NOTE]
> Bu ve sonraki bölümde, kimlik bilgilerini proje dosyalarında depolarsınız. [Hassas verileri ortak kaynak kodu depolarına kaydetmeyin](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).
>
>

1. ContosoAdsWeb projesinde uygulama *Web.config* dosyasının *Web.Release.config* dönüşüm dosyasını açın, bir `<connectionStrings>` öğesi içeren açıklama bloğunu silin ve aşağıdaki kodu yerine yapıştırın.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Dosyayı düzenlemek için açık bırakın.
2. [Azure portalı](https://portal.azure.com)’nda sol bölmedeki **SQL Veritabanları**’na, bu öğretici için oluşturduğunuz veritabanına ve ardından **Bağlantı dizelerini göster**’e tıklayın.

    ![Bağlantı dizelerini göster](./media/cloud-services-dotnet-get-started/showcs.png)

    Portal, parola için bir yer tutucu ile birlikte bağlantı dizelerini gösterir.

    ![Bağlantı dizeleri](./media/cloud-services-dotnet-get-started/connstrings.png)
3. *Web.Release.config* dönüşüm dosyasında `{connectionstring}` öğesini silin ve yerine Azure portalındaki ADO.NET bağlantı dizesini yapıştırın.
4. *Web.Release.config* dönüşüm dosyasının içine yapıştırdığınız bağlantı dizesinde `{your_password_here}` öğesini yeni SQL veritabanı için oluşturduğunuz parola ile değiştirin.
5. Dosyayı kaydedin.  
6. Bağlantı dizesini (çevresindeki soru işaretleri olmadan) çalışan rolünü yapılandırmaya yönelik aşağıdaki adımlarda kullanılmak üzere seçip kopyalayın.
7. **Çözüm Gezgini**’nde bulut hizmeti projesindeki **Roller** altında **ContosoAdsWorker**’a ve ardından **Özellikler**’e tıklayın.

    ![Rol özellikleri](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)
8. **Ayarlar** sekmesine tıklayın.
9. **Hizmet Yapılandırması**’nı **Bulut** olarak değiştirin.
10. `ContosoAdsDbConnectionString` ayarı için **Değer** alanını seçin ve ardından öğreticinin önceki bölümünde kopyaladığınız bağlantı dizesini yapıştırın.

     ![Çalışan rolü için veritabanı bağlantı dizesi](./media/cloud-services-dotnet-get-started/workerdbcs.png)
11. Yaptığınız değişiklikleri kaydedin.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>Çözümünüzü Azure’da çalıştığında Azure Storage hesabınızı kullanacak şekilde yapılandırma
Hem web rolü projesinin hem de çalışan rolü projesinin Azure Storage hesabı bağlantı dizeleri, bulut hizmeti projesindeki ortam ayarlarına depolanır. Her proje için uygulama yerel olarak çalıştığında ve bulutta çalıştığında kullanılacak ayrı ayarlar vardır. Hem web hem de çalışan rolü projeleri için bulut ortamı ayarlarını güncelleştirin.

1. **Çözüm Gezgini**’nde **ContosoAdsCloudService** projesindeki **Roller** altında **ContosoAdsWeb**’e sağ tıklayın ve ardından **Özellikler**’e tıklayın.

    ![Rol özellikleri](./media/cloud-services-dotnet-get-started/roleproperties.png)
2. **Ayarlar** sekmesine tıklayın. **Hizmet Yapılandırma** açılır kutusunda **Bulut**’u seçin.

    ![Bulut yapılandırması](./media/cloud-services-dotnet-get-started/sccloud.png)
3. **StorageConnectionString** girdisini seçtiğinizde satırın sağ uç kısmında bir üç nokta ( **...** ) göreceksiniz. **Depolama Hesabı Bağlantı Dizesi Oluştur** iletişim kutusunu açmak için üç nokta düğmesine tıklayın.

    ![Bağlantı Dizesi Oluştur kutusunu açma](./media/cloud-services-dotnet-get-started/opencscreate.png)
4. **Depolama Bağlantı Dizesi Oluştur** iletişim kutusunda **Aboneliğiniz**’e tıklayın, daha önce oluşturduğunuz depolama hesabını seçin ve ardından **Tamam**’a tıklayın. Henüz oturum açmadıysanız Azure hesabı kimlik bilgileriniz istenir.

    ![Depolama Bağlantı Dizesi oluşturma](./media/cloud-services-dotnet-get-started/createstoragecs.png)
5. Yaptığınız değişiklikleri kaydedin.
6. `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` bağlantı dizesini ayarlamak için `StorageConnectionString` bağlantı dizesi için kullandığınız yordamın aynısını izleyin.

    Bu bağlantı dizesi günlüğe kaydetme için kullanılır.
7. **ContosoAdsWeb** rolü için **ContosoAdsWorker** rolünün her iki bağlantı dizesini ayarlamak üzere kullandığınız yordamın aynısını izleyin. **Hizmet Yapılandırması**’nı **Bulut** olarak ayarlamayı unutmayın.

Visual Studio kullanıcı arabirimini kullanılarak yapılandırdığınız rol ortamı ayarları ContosoAdsCloudService projesinde aşağıdaki dosyalara depolanır:

* *ServiceDefinition.csdef* - Ayar adlarını tanımlar.
* *ServiceConfiguration.Cloud.cscfg* - Uygulamanın yerel olarak çalıştığı durumlar için değerler sağlar.
* *ServiceConfiguration.Local.cscfg* - Uygulamanın yerel olarak çalıştığı durumlar için değerler sağlar.

Örneğin, ServiceDefinition.csdef aşağıdaki tanımları içerir:

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

*ServiceConfiguration.Cloud.cscfg* dosyası ise Visual Studio’da bu ayarlar için girdiğiniz değerleri içerir.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

`<Instances>` ayarı Azure’un çalışan rolü kodunu çalıştıracağı sanal makine sayısını belirtir. [Sonraki adımlar](#next-steps) bölümünde bir bulut hizmetinin ölçeğini artırma hakkında daha fazla bilgi içeren bağlantılar bulunur.

### <a name="deploy-the-project-to-azure"></a>Projeyi Azure’a dağıtma
1. **Çözüm Gezgini**’nde **ContosoAdsCloudService** bulut projesine sağ tıklayın ve ardından **Yayımla** öğesini seçin.

   ![Yayımla menüsü](./media/cloud-services-dotnet-get-started/pubmenu.png)
2. **Azure Uygulaması Yayımlama** sihirbazının **Oturum aç** adımında **Sonraki** öğesine tıklayın.

    ![Oturum açma adımı](./media/cloud-services-dotnet-get-started/pubsignin.png)
3. Sihirbazın **Ayarlar** adımında **İleri**’ye tıklayın.

    ![Ayarlar adımı](./media/cloud-services-dotnet-get-started/pubsettings.png)

    **Gelişmiş** sekmesindeki varsayılan ayarlar bu öğretici için uygundur. Gelişmiş sekmesi hakkında bilgi için bkz. [Azure Uygulaması Yayımlama Sihirbazı](https://docs.microsoft.com/azure/vs-azure-tools-publish-azure-application-wizard).
4. **Özet** adımında **Yayımla** öğesine tıklayın.

    ![Özet adımı](./media/cloud-services-dotnet-get-started/pubsummary.png)

   Visual Studio’da **Azure Etkinlik Günlüğü** penceresi açılır.
5. Dağıtım ayrıntılarını genişletmek için sağ ok simgesine tıklayın.

    Dağıtımın tamamlanması 5 dakika veya daha fazla sürebilir.

    ![Azure Etkinlik Günlüğü penceresi](./media/cloud-services-dotnet-get-started/waal.png)
6. Dağıtım durumu tamamlandığında uygulamayı başlatmak için **Web uygulaması URL'si** öğesine tıklayın.
7. Uygulamayı yerel olarak çalıştırdığınızda yaptığınız gibi reklam oluşturarak, görüntüleyerek ve bazılarını düzenleyerek uygulamayı test edebilirsiniz.

> [!NOTE]
> Testi tamamladığınızda bulut hizmetini silin veya durdurun. Bulut hizmeti kullanmıyorsanız bile onun için sanal makine kaynakları ayrılmış olduğundan ücretler tahakkuk eder. Ayrıca çalışır durumda bırakırsanız, URL’nizi bulan herhangi bir kişi reklam oluşturabilir ve görüntüleyebilir. [Azure portalı](https://portal.azure.com)’nda bulut hizmetinizin **Genel Bakış** sekmesine gidin ve ardından sayfanın üstündeki **Sil** düğmesine tıklayın. Başkalarının siteye erişmesini yalnızca geçici olarak engellemek istiyorsanız bunun yerine **Durdur**’a tıklayın. Bu durumda ücretler tahakkuk etmeye devam eder. Artık ihtiyacınız kalmadığında SQL veritabanını ve depolama hesabını silmek için benzer bir yordamı izleyebilirsiniz.
>
>

## <a name="create-the-application-from-scratch"></a>Uygulamayı sıfırdan oluşturma
[Tamamlanmış uygulamayı](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) daha önce yüklemediyseniz şimdi yapın. İndirilen projedeki dosyaları yeni projeye kopyalayın.

Contoso Ads uygulamasının oluşturulması aşağıdaki adımları içerir:

* Bir bulut hizmeti Visual Studio çözümü oluşturun.
* NuGet paketlerini güncelleştirin ve ekleyin.
* Proje başvurularını ayarlayın.
* Bağlantı dizelerini yapılandırın.
* Kod dosyaları ekleyin.

Çözüm oluşturulduktan sonra bulut hizmeti projelerinde ve Azure blob'ları ile kuyruklarda benzersiz olan kodu gözden geçirin.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Bir bulut hizmeti Visual Studio çözümü oluşturma
1. Visual Studio'da **Dosya** menüsünden **Yeni Proje**’yi seçin.
2. **Yeni Proje** iletişim kutusunun sol bölmesinde **Visual C#** öğesini genişletin ve **Bulut** şablonlarını, ardından **Azure Cloud Service** şablonunu seçin.
3. Projeyi ve çözümü ContosoAdsCloudService olarak adlandırın ve ardından **Tamam**’a tıklayın.

    ![Yeni Proje](./media/cloud-services-dotnet-get-started/newproject.png)
4. **Yeni Azure Bulut Hizmeti** iletişim kutusunda bir web rolü ve bir çalışan rolü ekleyin. Web rolünü ContosoAdsWeb olarak ve çalışan rolünü ContosoAdsWorker olarak adlandırın. (Rollerin varsayılan adlarını değiştirmek için sağ bölmedeki kurşun kalem simgesini kullanın.)

    ![Yeni Bulut Hizmeti Projesi](./media/cloud-services-dotnet-get-started/newcsproj.png)
5. Web rolü için **Yeni ASP.NET Projesi** iletişim kutusunu gördüğünüzde MVC şablonunu seçin ve ardından **Kimlik Doğrulamayı Değiştir**’e tıklayın.

    ![Kimlik Doğrulamayı Değiştirme](./media/cloud-services-dotnet-get-started/chgauth.png)
6. **Kimlik Doğrulamayı Değiştir** iletişim kutusunda **Kimlik Doğrulaması Yok**’u seçin ve ardından **Tamam**’a tıklayın.

    ![Kimlik Doğrulaması Yok](./media/cloud-services-dotnet-get-started/noauth.png)
7. **Yeni ASP.NET Projesi** iletişim kutusunda **Tamam**’a tıklayın.
8. **Çözüm Gezgini**’nde çözüme (projelerden birine değil) sağ tıklayın ve **Ekle - Yeni Proje**’yi seçin.
9. **Yeni Proje Ekle** iletişim kutusunda sol bölmedeki **Visual C#** altında bulunan **Windows**’u seçin ve ardından **Sınıf Kitaplığı** şablonuna tıklayın.  
10. Projeyi *ContosoAdsCommon* olarak adlandırın ve ardından **Tamam**’a tıklayın.

    Entity Framework bağlamına ve hem web hem de çalışan rolü projelerindeki veri modeline başvurmanız gerekir. Alternatif olarak, web rolü projesinde EF ile ilgili sınıfları tanımlayabilir ve çalışan rolü projesinde bu projeye başvurabilirsiniz. Ancak, alternatif yaklaşımda çalışan rolü projeniz gerekli olmayan web bütünleştirme kodlarına başvuruda bulunur.

### <a name="update-and-add-nuget-packages"></a>NuGet paketlerini güncelleştirme ve ekleme
1. Çözüm için **NuGet Paketlerini Yönet** iletişim kutusunu açın.
2. Pencerenin en üstündeki **Güncelleştirmeler**’i seçin.
3. *WindowsAzure.Storage* paketini arayın ve listede varsa onu ve içinde güncelleştirileceği web ve çalışan projelerini seçin, ardından **Güncelleştir**’e tıklayın.

    Depolama istemcisi kitaplığı Visual Studio proje şablonlarından daha sık güncelleştirildiği için yeni oluşturulan bir projedeki sürümün güncelleştirilmesi gerektiğini sık sık görürsünüz.
4. Pencerenin en üstünde **Gözat**’ı seçin.
5. *EntityFramework* NuGet paketini bulun ve üç projenin tamamına yükleyin.
6. *Microsoft.WindowsAzure.ConfigurationManager* NuGet paketini bulun ve çalışan rolü projesine yükleyin.

### <a name="set-project-references"></a>Proje başvurularını ayarlama
1. ContosoAdsWeb projesinde ContosoAdsCommon projesine bir başvuru ayarlayın. ContosoAdsWeb projesine sağ tıklayın ve ardından **Başvurular** - **Başvuru Ekle** öğesine tıklayın. **Başvuru Yöneticisi** iletişim kutusunda sol bölmedeki **Çözüm – Projeler** öğesini seçin, **ContosoAdsCommon**’ı seçin ve ardından **Tamam**’a tıklayın.
2. ContosoAdsWorker projesinde, ContosoAdsCommon projesine bir başvuru ayarlayın.

    ContosoAdsCommon hem ön uç ve arka uç tarafından kullanılacak olan Entity Framework veri modeli ve bağlam sınıfını içerir.
3. ContosoAdsWorker projesinde bir `System.Drawing` başvurusu ayarlayın.

    Bu bütünleştirilmiş kod, görüntüleri küçük resimlere dönüştürmek için arka uç tarafından kullanılır.

### <a name="configure-connection-strings"></a>Bağlantı dizelerini yapılandırma
Bu bölümde, yerel olarak test etmek amacıyla Azure Storage ve SQL bağlantı dizelerini yapılandırırsınız. Öğreticinin önceki bölümlerinde verilen dağıtım yönergeleri, uygulamanın bulutta çalıştığı durumlar için bağlantı dizelerinin nasıl ayarlandığını açıklamaktadır.

1. ContosoAdsWeb projesinde uygulamanın Web.config dosyasını açın ve aşağıdaki `connectionStrings` öğesini `configSections` öğesinden sonra ekleyin.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Visual Studio 2015 ve sonraki bir sürümü kullanıyorsanız "v11.0" ifadesini "MSSQLLocalDB" ile değiştirin.
2. Yaptığınız değişiklikleri kaydedin.
3. ContosoAdsCloudService projesinde **Roller** altındaki ContosoAdsWeb öğesine sağ tıklayın ve ardından **Özellikler**’e tıklayın.

    ![Rol özellikleri](./media/cloud-services-dotnet-get-started/roleproperties.png)
4. **Contosoadsweb [rol]** özellikleri penceresinde, **Ayarlar** sekmesine tıklayın ve ardından **Ayar ekle**' ye tıklayın.

    **Hizmet Yapılandırma** ayarını **Tüm Yapılandırmalar** olarak bırakın.
5. *StorageConnectionString* adlı bir ayar ekleyin. **Tür** değerini *ConnectionString* olarak, **Değer** seçeneğini *UseDevelopmentStorage=true* olarak ayarlayın.

    ![Yeni bağlantı dizesi](./media/cloud-services-dotnet-get-started/scall.png)
6. Yaptığınız değişiklikleri kaydedin.
7. ContosoAdsWorker rol özelliklerine bir depolama bağlantı dizesi eklemek için aynı yordamı izleyin.
8. Hala **ContosoAdsWorker [Rolü]** özellikler penceresindeyken başka bir bağlantı dizesi ekleyin:

   * Adı: ContosoAdsDbConnectionString
   * Şunu yazın: Dize
   * Değer: Web rolü projesi için kullandığınız bağlantı dizesini yapıştırın. (Aşağıdaki örnek Visual Studio 2013 içindir. Bu örneği kopyalarsanız ve Visual Studio 2015 veya sonraki bir sürümü kullanıyorsanız Veri Kaynağını değiştirmeyi unutmayın.)

       ```
       Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
       ```

### <a name="add-code-files"></a>Kod dosyaları ekleme
Bu bölümde, indirilen çözümden yeni çözüme kod dosyaları kopyalarsınız. Aşağıdaki bölümlerde bu kodun temel kısımları gösterilmiş ve açıklanmıştır.

Bir proje veya klasöre dosya eklemek için proje veya klasöre sağ tıklayıp **Ekle** - **Mevcut Öğe** seçeneğine tıklayın. İstediğiniz dosyaları seçin ve ardından **Ekle**’ye tıklayın. Mevcut dosyaları değiştirmek isteyip istemediğiniz sorulursa **Evet**’e tıklayın.

1. ContosoAdsCommon projesinde *Class1.cs* dosyasını silin ve indirilen projedeki *Ad.cs* ve *ContosoAdscontext.cs* dosyalarını onun yerine ekleyin.
2. ContosoAdsWeb projesinde indirilen projeden aşağıdaki dosyaları ekleyin.

   * *Global.asax.cs*.  
   * *Views\shared* klasöründe: Layout. cshtml.  *\_*
   * *Views\home* klasöründe: *Index. cshtml*.
   * *Denetleyiciler* klasöründe: *AdController.cs*.
   * *Görünümler/Reklam* klasöründe (öncelikle klasörü oluşturun): beş *.cshtml* dosyası.
3. ContosoAdsWorker projesinde indirilen projeden *WorkerRole.cs* ekleyin.

Uygulamayı artık öğreticinin önceki bölümlerinde anlatılan şekilde derleyip çalıştırabilirsiniz; uygulama yerel veritabanı ve depolama öykünücüsü kaynaklarını kullanacaktır.

Aşağıdaki bölümlerde Azure ortamı, blob'ları ve kuyrukları ile çalışmayla ilgili kod açıklanmaktadır. Bu öğretici, iskele kurma kullanarak MVC denetleyicilerinin ve görünümlerin nasıl oluşturulacağını, SQL Server veritabanları ile çalışan Entity Framework kodunun nasıl yazılacağını veya ASP.NET 4.5 içinde zaman uyumsuz programlamanın temel bilgilerini açıklamaz. Bu konular hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [MVC 5 kullanmaya başlama](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [EF 6 ve MVC 5 kullanmaya başlama](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [.NET 4.5’te zaman uyumsuz programlamaya giriş](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon - Ad.cs
Ad.cs dosyası reklam kategorileri için bir numaralandırma ve reklam bilgileri için bir POCO varlık sınıfı tanımlar.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon - ContosoAdsContext.cs
ContosoAdsContext sınıfı Reklam sınıfının Entity Framework tarafından bir SQL veritabanına depolanacak olan Db koleksiyonunda kullanıldığını belirtir.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

Sınıfın iki oluşturucusu vardır. Birincisi web projesi tarafından kullanılır ve Web.config dosyasına depolanan bir bağlantı dizesinin adını belirtir. İkinci oluşturucu, Web.config dosyasına sahip olmadığı için çalışan rolü projesi tarafından kullanılan gerçek bağlantı dizesini geçirmenizi sağlar. Bu bağlantı dizesinin nereye depolandığını daha önce gördünüz ve kodun DbContext sınıfını başlattığında bağlantı dizesini nasıl aldığını daha sonra göreceksiniz.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb - Global.asax.cs
`Application_Start` yönteminden çağrılan kod, henüz yoksa bir *görüntüler* blob kapsayıcısı ve bir *görüntüler* kuyruğu oluşturur. Bunun yapılması yeni bir depolama hesabını başlattığınız veya depolama öykünücüsünü yeni bir bilgisayarda kullanmaya başladığınız her durumda gerekli blob kapsayıcısının ve kuyruğun otomatik olarak oluşturulmasını sağlar.

Kod *.cscfg* dosyasından depolama bağlantı dizesini kullanarak depolama hesabına erişim elde eder.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Ardından *görüntüler* blob kapsayıcısına bir başvuru edinir, henüz yoksa kapsayıcıyı oluşturur ve yeni kapsayıcıda erişim izinlerini ayarlar. Varsayılan olarak, yeni kapsayıcılar yalnızca depolama hesabı kimlik bilgilerine sahip istemcilerin blob’lara erişmesine izin verir. Web sitesi, görüntü blob’larını işaret eden URL’leri kullanarak görüntüleri gösterebilmek için blob’ların herkese açık olmasını gerektirir.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Benzer bir kod, *görüntüler* kuyruğuna başvuru edinir ve yeni bir kuyruk oluşturur. Bu durumda hiçbir izin değişikliğine gerek yoktur.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---_layoutcshtml"></a>ContosoAdsWeb - \_Layout.cshtml
*_Layout.cshtml* dosyası üst bilgi ve alt bilgide uygulama adını ayarlar ve bir "Reklamlar" menü girişi oluşturur.

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb - Görünümler\Giriş\Dizin.cshtml
*Görünümler\Giriş\Dizin.cshtml* dosyası, giriş sayfasında kategori bağlantılarını gösterir. Bağlantılar `Category` numaralandırmasının bir sorgu dizesi değişkeni içindeki tamsayı değerini Reklam Dizini sayfasına geçirir.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb - AdController.cs
Oluşturucu, *AdController.cs* dosyasında blob’larla ve kuyruklarla çalışmaya yönelik bir API sağlayan Azure Storage İstemci Kitaplığı nesneleri oluşturmak için `InitializeStorage` yöntemini çağırır.

Ardından kod daha önce gördüğünüz gibi *görüntüler* blob kapsayıcısı için *Global.asax.cs* içinde bir başvuru edinir. Bunu yaparken bir web uygulaması için uygun bir varsayılan [yeniden deneme ilkesi](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) ayarlar. Varsayılan üstel geri alma yeniden deneme ilkesi, web uygulamasını geçici bir hata için tekrarlanan yeniden denemelerde bir dakikadan uzun süre askıya alabilir. Burada belirtilen yeniden deneme ilkesi üç denemeye kadar her denemeden sonra en fazla üç saniye bekler.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Benzer bir kod, *görüntüler* kuyruğuna başvuru edinir.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

Denetleyici kodlarının birçoğu bir DbContext sınıfı kullanarak Entity Framework veri modeli ile çalışmak için tipiktir. Dosyayı karşıya yükleyen ve blob depolama alanına kaydeden HttpPost `Create` yöntemi bunun bir istisnasıdır. Model bağlayıcı, yönteme bir [HttpPostedFileBase](/dotnet/api/system.web.httppostedfilebase) nesnesi sağlar.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Kullanıcı karşıya yüklemek için bir dosya seçtiyse kod bu dosyayı karşıya yükler, bir blob'a kaydeder ve Ad veritabanı kaydını blob’a işaret eden bir URL ile güncelleştirir.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

Karşıya yüklemeyi yapan kod `UploadAndSaveBlobAsync` yöntemindedir. Blob için bir GUID adı oluşturur, dosyayı karşıya yükler ve kaydeder ve kaydedilmiş blob için bir başvuru döndürür.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

HttpPost `Create` yöntemi bir blob’u karşıya yükleyip veritabanını güncelleştirdikten sonra bir görüntünün küçük resme dönüştürme için hazır olduğunu ilgili arka uç işlemine bildirmek üzere bir kuyruk iletisi oluşturur.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

Kullanıcının yeni bir görüntü dosyası seçtiği durumlarda zaten mevcut blob’ların silinmesinin gerekli olması dışında HttpPost `Edit` yönteminin kodu aynıdır.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

Sonraki örnekte bir reklamı sildiğinizde blob'ları silen kod gösterilmektedir.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb - Views\Ad\Index.cshtml ve Details.cshtml
*Index.cshtml* dosyası küçük resimleri diğer reklam verileriyle birlikte gösterir.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

*Details.cshtml* dosyası tam boyutlu görüntüyü gösterir.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb - Views\Ad\Create.cshtml ve Edit.cshtml
*Create.cshtml* ve *Edit.cshtml* dosyaları denetleyicinin `HttpPostedFileBase` nesnesi almasını sağlayan form kodlamasını belirtir.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Bir `<input>` öğesi tarayıcıya bir dosya seçme iletişim kutusu açmasını söyler.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>ContosoAdsWorker - WorkerRole.cs - OnStart yöntemi
Azure çalışan rolü ortamı, çalışan rolü başlatılırken `WorkerRole` sınıfındaki `OnStart` yöntemini, `OnStart` yöntemi tamamlandığında ise `Run` yöntemini çağırır.

`OnStart` yöntemi *.cscfg* dosyasından veritabanı bağlantı dizesini alır ve Entity Framework DbContext sınıfına geçirir. Varsayılan olarak SQLClient sağlayıcısı kullanılır, bu nedenle sağlayıcının belirtilmesi gerekli değildir.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Bundan sonra yöntem, depolama hesabı için bir başvuru alır ve yoksa blob kapsayıcısı ve kuyruk oluşturur. Bunun kodu, web rolü `Application_Start` yönteminde daha önce gördüğünüzle aynıdır.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker - WorkerRole.cs - Run yöntemi
`OnStart` yöntemi başlatma işini tamamladığında `Run` yöntemi çağrılır. Yöntem yeni bir kuyruk iletisi bekleyen ve ulaşan iletileri işleyen sonsuz bir döngü yürütür.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Döngünün her yinelemesinden sonra herhangi bir kuyruk iletisi bulunmazsa program bir saniye için uyku moduna geçer. Bunun yapılması çalışan rolünün aşırı CPU süresi ve depolama işlem maliyetleri doğurmasını engeller. Microsoft Müşteri Danışma Ekibi bunu dahil etmeyi unutan, üretime dağıtan ve tatile giden bir geliştiricinin hikayesini anlatır. Geri geldiğinde, daha fazla bakış maliyeti tatilden daha fazla.

Bazı durumlarda bir kuyruk iletisinin içeriği işlemede hataya neden olur. Buna *zehir iletisi* adı verilir ve bir hatayı günlüğe kaydedip döngüyü yeniden başlatırsanız bu iletiyi sonu gelmez bir şekilde işlemeye çalışabilirsiniz.  Bu nedenle, yakalama bloğu uygulamanın geçerli iletiyi işlemeyi kaç kez denediğini denetleyen bir if deyimi içerir ve 5’ten fazla kez denediyse ileti kuyruktan silinir.

Bir kuyruk iletisi bulunduğunda `ProcessQueueMessage` çağrılır.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Bu kod, görüntü URL’sini almak için veritabanını okur, görüntüyü bir küçük resme dönüştürür, küçük resmi bir blob’a kaydeder, veritabanını küçük resim blob URL’si ile güncelleştirir ve kuyruk iletisini siler.

> [!NOTE]
> `ConvertImageToThumbnailJPG` yöntemindeki kod kolaylık için System.Drawing ad alanındaki sınıfları kullanır. Ancak, bu ad alanındaki sınıflar Windows Forms ile kullanılmak üzere tasarlanmıştır. Bir Windows veya ASP.NET hizmetinde kullanılması desteklenmez. Görüntü işleme seçenekleri hakkında daha fazla bilgi için bkz. [Dinamik Görüntü Oluşturma](https://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) ve [Görüntü Yeniden Boyutlandırmaya Ayrıntılı Bakış](https://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na).
>
>

## <a name="troubleshooting"></a>Sorun giderme
Bu öğreticideki yönergeleri izlerken bir sorun oluşması durumunda bazı yaygın hatalar ve çözümleri aşağıda verilmiştir.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException
Uygulamayı Azure’da çalıştırdığınızda ya da Azure işlem öykünücüsü kullanarak yerel olarak çalıştırdığınızda Azure tarafından `RoleEnvironment` nesnesi sağlanır.  Yerel olarak çalıştırırken bu hatayı alırsanız ContosoAdsCloudService projesini başlangıç projesi olarak ayarladığınızdan emin olun. Bunun yapılması projeyi Azure işlem öykünücüsü kullanarak çalışacak şekilde ayarlar.

Uygulamanın Azure RoleEnvironment’ı kullanmasının bir nedeni *.cscfg* dosyalarına depolanmış bağlantı dizelerinin alınmasıdır; dolayısıyla bu özel durumun başka bir nedeni de eksik bir bağlantı dizesidir. ContosoAdsWeb projesinde hem Bulut hem de Yerel yapılandırmaları için StorageConnectionString ayarını oluşturduğunuzdan ve ContosoAdsWorker projesinde her iki yapılandırma için iki bağlantı dizesini de oluşturduğunuzdan emin olun. StorageConnectionString için çözümün tamamında **Tümünü Bul** araması yaparsanız 6 dosyada 9 kez görmeniz gerekir.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>xxx bağlantı noktası için geçersiz kılınamıyor. Yeni bağlantı noktası http protokolü için izin verilen en düşük 8080 değerinin altında
Web projesi tarafından kullanılan bağlantı noktası numarasını değiştirmeyi deneyin. ContosoAdsWeb projesine sağ tıklayın ve ardından **Özellikler** öğesine tıklayın. **Web** sekmesine tıklayın ve ardından **Proje Url’si** ayarında bağlantı noktası numarasını değiştirin.

Sorunu çözebilecek başka bir alternatif için sonraki bölüme bakın.

### <a name="other-errors-when-running-locally"></a>Yerel olarak çalışırken oluşan diğer hatalar
Varsayılan olarak yeni bulut hizmeti projeleri, Azure ortamının benzetimini yapmak için Azure işlem öykünücüsü kullanır. Bu, tam işlem öykünücüsünün hafif sürümüdür ve hızlı sürümün çalışmadığı bazı koşullarda tam öykünücü çalışır.  

Projeyi tam öykünücü kullanacak şekilde değiştirmek için ContosoAdsCloudService projesine sağ tıklayın ve ardından **Özellikler**’e tıklayın. **Özellikler** penceresinde **Web** sekmesine ve ardından **Tam Öykünücü Kullan** radyo düğmesine tıklayın.

Uygulamayı tam öykünücü ile çalıştırmak için Visual Studio’yu yönetici ayrıcalıklarıyla açmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
Contoso Ads uygulaması bir başlangıç öğreticisi için kasıtlı olarak basit tutulmuştur. Örneğin, [bağımlılık ekleme](https://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) veya [depo ve iş birimi düzenleri](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo) uygulamaz, [günlüğe kaydetme arabirimi yoktur](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), veri modeli değişikliklerini yönetmek için [EF Code First Geçişleri](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) veya geçici ağ hatalarını yönetmek için [EF Bağlantı Dayanıklılığı](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) kullanmaz, vb.

Daha gerçek kodlama uygulamalarını gösteren bazı bulut hizmeti örnek uygulamaları en az karmaşık olandan en karmaşığa doğru aşağıda listelenmiştir:

* [PhluffyFotos](https://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Contoso Ads uygulamasıyla kavram olarak benzerdir, ancak daha fazla özellik ve daha gerçek kodlama uygulamaları kullanır.
* [Tablolar, Kuyruklar ve Blob’lar ile Azure Cloud Service Çok Katmanlı Uygulaması](https://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Azure Storage tablolarının yanı sıra blob’ları ve kuyrukları tanıtır. .NET için daha eski bir Azure SDK sürümünü temel alır ve geçerli sürümle çalışmak için bazı değişiklikler gerektirir.

Bulut için geliştirme hakkında genel bilgi için bkz. [Azure ile Gerçek Bulut Uygulamaları Derleme](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Azure Storage’da en iyi yöntemler ve yaklaşımlar hakkında bir tanıtım için bkz. [Microsoft Azure Storage – Yenilikler, En İyi Yöntemler ve Yaklaşımlar](https://channel9.msdn.com/Events/Build/2014/3-628).

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Cloud Services Bölüm 1: Giriş](https://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Cloud Services nasıl yönetilir?](cloud-services-how-to-manage-portal.md)
* [Azure Depolama](https://docs.microsoft.com/azure/storage/)
* [Bulut hizmeti sağlayıcısı seçme](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
