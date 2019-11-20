---
title: Visual Studio ile uygulama sorunlarını giderme-Azure App Service
description: Visual Studio 2013 ' de yerleşik olarak bulunan uzaktan hata ayıklama, izleme ve günlük araçlarını kullanarak App Service bir uygulamayla ilgili sorunları nasıl giderebileceğinizi öğrenin.
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: ''
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 13ba1ced2d14ed22c89e7df594f3b2a44eea983f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359962"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Visual Studio 'Yu kullanarak Azure App Service uygulama sorunlarını giderme
## <a name="overview"></a>Genel Bakış
Bu öğreticide, [hata ayıklama modunda](https://docs.microsoft.com/visualstudio/debugger/) uzaktan veya uygulama günlüklerini ve Web sunucusu günlüklerini görüntüleyerek [App Service](https://go.microsoft.com/fwlink/?LinkId=529714)içindeki bir uygulamada hata ayıklamaya yardımcı olmak için Visual Studio Araçları 'nın nasıl kullanılacağı gösterilmektedir.

Şunları öğreneceksiniz:

* Visual Studio 'da hangi uygulama yönetimi işlevleri kullanılabilir.
* Uzak bir uygulamada hızlı değişiklikler yapmak için Visual Studio uzaktan görünümünü kullanma.
* Bir proje Azure 'da çalışırken hata ayıklama modunu uzaktan çalıştırma, hem uygulama hem de WebJob için.
* Uygulama izleme günlükleri oluşturma ve uygulama onları oluştururken görüntüleme.
* Ayrıntılı hata iletileri ve başarısız istek izleme dahil olmak üzere Web sunucusu günlüklerini görüntüleme.
* Tanılama günlüklerini bir Azure depolama hesabına gönderme ve burada görüntüleme.

Visual Studio Ultimate sahipseniz, hata ayıklama için [IntelliTrace](/visualstudio/debugger/intellitrace) de kullanabilirsiniz. Bu öğreticide IntelliTrace kapsamında değildir.

## <a name="prerequisites"></a>Önkoşullar
Bu öğretici, [Azure App Service bir ASP.NET uygulaması oluşturma](app-service-web-get-started-dotnet-framework.md)bölümünde ayarladığınız geliştirme ortamı, Web projesi ve App Service uygulamayla birlikte çalışır. WebJobs bölümleri için, [Azure Web İşleri SDK 'Sını kullanmaya başlama][GetStartedWJ]bölümünde oluşturduğunuz uygulamanın olması gerekir.

Bu öğreticide gösterilen kod örnekleri bir C# MVC web uygulaması içindir, ancak sorun giderme yordamları Visual Basic ve Web Forms uygulamalar için aynıdır.

Öğretici, Visual Studio 2019 kullandığınızı varsayar. 

Akış günlükleri özelliği yalnızca .NET Framework 4 veya sonraki sürümlerini hedefleyen uygulamalar için geçerlidir.

## <a name="sitemanagement"></a>Uygulama yapılandırması ve yönetimi
Visual Studio, [Azure Portal](https://go.microsoft.com/fwlink/?LinkId=529715)bulunan uygulama yönetimi işlevlerinin ve yapılandırma ayarlarının bir alt kümesine erişim sağlar. Bu bölümde **Sunucu Gezgini**kullanarak nelerin kullanılabildiğini göreceksiniz. En son Azure tümleştirme özelliklerini görmek için **Cloud Explorer 'ı** da deneyin. **Görünüm** menüsünden her iki pencere de açabilirsiniz.

1. Visual Studio 'da zaten Azure 'da oturum açmadıysanız **Azure** ' a sağ tıklayın ve **Sunucu Gezgini** **Microsoft Azure aboneliğine** Bağlan ' ı seçin.

    Alternatif olarak, hesabınıza erişim sağlayan bir yönetim sertifikası yüklemektir. Bir sertifika yüklemeyi tercih ederseniz, **Sunucu Gezgini**' de **Azure** düğümüne sağ tıklayın ve bağlam menüsünde **abonelikleri Yönet ve filtrele** ' yi seçin. **Microsoft Azure Aboneliklerini Yönet** iletişim kutusunda, **Sertifikalar** sekmesine tıklayın ve ardından **içeri aktar**' a tıklayın. Azure hesabınız için bir abonelik dosyasını ( *. publishsettings* dosyası olarak da bilinir) indirmek ve içeri aktarmak için yönergeleri izleyin.

   > [!NOTE]
   > Bir abonelik dosyası indirdiğinizde, dosyayı kaynak kodu dizinlerinizin dışında bir klasöre kaydedin (örneğin, Indirmeler klasöründe) ve içeri aktarma tamamlandıktan sonra silin. Abonelik dosyasına erişim sağlayan kötü niyetli bir Kullanıcı, Azure hizmetlerinizi düzenleyebilir, oluşturabilir ve silebilir.
   >
   >

    Visual Studio 'dan Azure kaynaklarına bağlanma hakkında daha fazla bilgi için bkz. [hesapları, abonelikleri ve yönetici rollerini yönetme](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. **Sunucu Gezgini**' de **Azure** ' u genişletin ve **App Service**' ı genişletin.
3. [Azure App Service ' de ASP.NET uygulaması oluşturma](app-service-web-get-started-dotnet-framework.md)bölümünde oluşturduğunuz uygulamayı içeren kaynak grubunu genişletin ve ardından uygulama düğümüne sağ tıklayıp **ayarları görüntüle**' ye tıklayın.

    ![Sunucu Gezgini ayarları görüntüle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    **Azure Web App** sekmesi görüntülenir ve Visual Studio 'da bulunan uygulama yönetimi ve yapılandırma görevlerinin bulunduğunu görebilirsiniz.

    ![Azure Web uygulaması penceresi](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Bu öğreticide, günlüğe kaydetme ve izleme açılan listelerini kullanacaksınız. Uzaktan hata ayıklamayı da kullanacaksınız, ancak bunu etkinleştirmek için farklı bir yöntem kullanacaksınız.

    Bu penceredeki uygulama ayarları ve bağlantı dizeleri kutuları hakkında daha fazla bilgi için bkz [. Azure App Service: Uygulama dizeleri ve bağlantı dizeleri nasıl çalışır](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)?

    Bu pencerede yapılamıyorum bir uygulama yönetimi görevi gerçekleştirmek istiyorsanız, Azure portal bir tarayıcı penceresi açmak için **Yönetim Portalı aç** ' a tıklayın.

## <a name="remoteview"></a>Sunucu Gezgini 'de uygulama dosyalarına erişme
Genellikle, Web. config dosyası olarak `customErrors` `On` ayarlanan bayrağıyla bir Web projesi dağıtın veya `RemoteOnly`bir sorun olduğunda faydalı bir hata iletisi almanız gerekmez. Birçok hata için, her şey aşağıdakilerden biri gibi bir sayfasıdır:

**'/' Uygulamasında sunucu hatası:**

![Faydalı olmayan hata sayfası](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Bir hata oluştu:**

![Faydalı olmayan hata sayfası](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Web sitesi sayfayı görüntüleyemiyor**

![Faydalı olmayan hata sayfası](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Genellikle hatanın nedenini bulmanın en kolay yolu, önceki ekran görüntülerinin ilk olarak nasıl yapılacağını açıklayan ayrıntılı hata iletilerini etkinleştirmektir. Bu, dağıtılan Web. config dosyasında bir değişiklik yapılmasını gerektirir. Projedeki *Web. config* dosyasını düzenleyebilir ve projeyi yeniden dağıtabilir ya da bir [Web. config dönüşümü](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) oluşturabilir ve bir hata ayıklama derlemesi dağıtabilirsiniz, ancak daha hızlı bir şekilde, dosyaları uzak uygulamadaki doğrudan görüntüleyebilir ve düzenleyebilirsiniz. **Çözüm Gezgini** *Uzaktan görünüm* özelliğini kullanarak.

1. **Sunucu Gezgini**' de **Azure**' ı genişletin, **App Service**' ı genişletin, uygulamanızın bulunduğu kaynak grubunu genişletin ve ardından uygulamanızın düğümünü genişletin.

    Uygulamanın içerik dosyalarına ve günlük dosyalarına erişim sağlayan düğümleri görürsünüz.
2. **Dosyalar** düğümünü genişletin ve *Web. config* dosyasına çift tıklayın.

    ![Web. config dosyasını aç](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio, uzak uygulamadaki Web. config dosyasını açar ve başlık çubuğunda dosya adının yanında [uzak] öğesini gösterir.
3. Aşağıdaki satırı `system.web` öğesine ekleyin:

    `<customErrors mode="Off"></customErrors>`

    ![Web. config dosyasını Düzenle](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Faydalı olmayan hata iletisini gösteren Tarayıcıyı yenileyin ve artık aşağıdaki örnek gibi ayrıntılı bir hata iletisi alırsınız:

    ![Ayrıntılı hata iletisi](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Gösterilen hata, kırmızı renkle *Views\home\ındex.cshtml*'ye eklenerek oluşturulmuştur.)

Web. config dosyasını düzenlemek, App Service uygulamanızdaki dosyaları okuma ve düzenleme yeteneğinin sorun gidermeyi daha kolay hale getirmek için yalnızca bir örnek senaryolar örneğidir.

## <a name="remotedebug"></a>Uzaktan hata ayıklama uygulamaları
Ayrıntılı hata iletisi yeterli bilgi sağlamıyorsa ve hatayı yerel olarak yeniden oluşturamıyoruz, sorun gidermeye yönelik başka bir yol hata ayıklama modunda uzaktan çalıştırılmalıdır. Kesme noktaları ayarlayabilir, belleği doğrudan işleyebilir, koddaki adımları görebilir ve hatta kod yolunu değiştirebilirsiniz.

Uzaktan hata ayıklama, Visual Studio 'nun Express sürümlerinde çalışmaz.

Bu bölümde [, Azure App Service ' de bir ASP.NET uygulaması oluşturma](app-service-web-get-started-dotnet-framework.md)bölümünde oluşturduğunuz projeyi kullanarak uzaktan nasıl hata ayıklaması yapılacağı gösterilmektedir.

1. [Azure App Service bir ASP.NET uygulaması oluşturma](app-service-web-get-started-dotnet-framework.md)bölümünde oluşturduğunuz Web projesini açın.

1. *Controllers\homecontroller.cs*dosyasını açın.

1. `About()` Yöntemi silin ve yerine aşağıdaki kodu ekleyin.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. `ViewBag.Message` Satırda [bir kesme noktası ayarlayın](https://docs.microsoft.com/visualstudio/debugger/) .

1. **Çözüm Gezgini**, projeye sağ tıklayın ve **Yayımla**' ya tıklayın.

1. **Profil** açılır listesinde, [Azure App Service içinde bir ASP.NET uygulaması oluşturma](app-service-web-get-started-dotnet-framework.md)bölümünde kullandığınız profili seçin. Ardından Ayarlar ' a tıklayın.

1. **Yayımla** Iletişim kutusunda **Ayarlar** sekmesine tıklayın ve ardından **yapılandırmayı** **Hata Ayıkla**olarak değiştirin ve ardından **Kaydet**' e tıklayın.

    ![Hata ayıklama modunda Yayımla](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Tıklayın **yayımlama**. Dağıtım tamamlandıktan sonra ve tarayıcınız uygulamanızın Azure URL 'SI için açıldıktan sonra tarayıcıyı kapatın.

1. **Sunucu Gezgini**, uygulamanıza sağ tıklayın ve ardından **hata ayıklayıcı Ekle**' ye tıklayın.

    ![Hata ayıklayıcı Ekle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Tarayıcı, Azure 'da çalışan giriş sayfanıza otomatik olarak açılır. Azure sunucuyu hata ayıklama için ayarlarken 20 saniye beklemeniz gerekebilir. Bu gecikme yalnızca, 48 saatlik bir dönemde bir uygulamada hata ayıklama modunda ilk kez çalıştırdığınızda gerçekleşir. Aynı dönemde hata ayıklamaya yeniden başladığınızda bir gecikme yoktur.

    > [!NOTE] 
    > Hata ayıklayıcıyı başlatırken sorun yaşarsanız **Sunucu Gezgini**yerine **Cloud Explorer** 'ı kullanarak bunu yapmayı deneyin.
    >

1. Menüde **hakkında** ' ya tıklayın.

    Visual Studio kesme noktasında durmaktadır ve kod, yerel bilgisayarınızda değil, Azure 'da çalışır.

1. Zaman değerini görmek `currentTime` için değişkenin üzerine gelin.

    ![Azure 'da çalışan hata ayıklama modunda değişkeni görüntüle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Gördüğünüz zaman, yerel bilgisayarınızdan farklı bir saat diliminde olabilecek Azure Server saati ' dir.

1. `currentTime` Değişken için "Azure 'da çalışıyor" gibi yeni bir değer girin.

1. Çalışmaya devam etmek için F5 tuşuna basın.

     Azure 'da çalışan hakkında sayfası, currentTime değişkenine girdiğiniz yeni değeri görüntüler.

     ![Yeni değer içeren sayfa hakkında](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a>Web Işlerine uzaktan hata ayıklama
Bu bölümde [, Azure WebJobs SDK Ile çalışmaya başlama](https://github.com/Azure/azure-webjobs-sdk/wiki)bölümünde oluşturduğunuz proje ve uygulamayı kullanarak uzaktan hata ayıklama işlemi gösterilmektedir.

Bu bölümde gösterilen özellikler yalnızca güncelleştirme 4 veya sonraki sürümlerde Visual Studio 2013 kullanılabilir.

Uzaktan hata ayıklama yalnızca sürekli WebJobs ile kullanılabilir. Zamanlanmış ve isteğe bağlı Web Işleri hata ayıklamayı desteklemez.

1. [Azure Web İşleri SDK 'Sını kullanmaya başlama][GetStartedWJ]bölümünde oluşturduğunuz Web projesini açın.

2. ContosoAdsWebJob projesinde *Functions.cs*öğesini açın.

3. `GnerateThumbnail` Yöntemdeki ilk ifadede [bir kesme noktası ayarlayın](https://docs.microsoft.com/visualstudio/debugger/) .

    ![Kesme noktası ayarla](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. **Çözüm Gezgini**, web projesine (WebJob projesi değil) sağ tıklayın ve **Yayımla**' ya tıklayın.

5. **Profil** açılan listesinde, [Azure WebJobs SDK ile çalışmaya başlama](https://github.com/Azure/azure-webjobs-sdk/wiki)bölümünde kullandığınız profili seçin.

6. **Ayarlar** sekmesine tıklayın ve **yapılandırmayı** **Hata Ayıkla**olarak değiştirin ve ardından **Yayımla**' ya tıklayın.

    Visual Studio Web ve WebJob projelerini dağıtır ve tarayıcınız, uygulamanızın Azure URL 'SI olarak açılır.

7. **Sunucu Gezgini**, **Azure > App Service > kaynak grubunuzu uygulamanızı > Web İşleri > sürekli**ve sonra **contosoadswebjob**öğesine sağ tıklayın.

8. **Hata ayıklayıcı Ekle**' ye tıklayın.

    ![Hata ayıklayıcı Ekle](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Tarayıcı, Azure 'da çalışan giriş sayfanıza otomatik olarak açılır. Azure sunucuyu hata ayıklama için ayarlarken 20 saniye beklemeniz gerekebilir. Bu gecikme yalnızca, 48 saatlik bir dönemde bir uygulamada hata ayıklama modunda ilk kez çalıştırdığınızda gerçekleşir. Aynı dönemde hata ayıklamaya yeniden başladığınızda bir gecikme yoktur.

9. Contoso ads giriş sayfasına açılan Web tarayıcısında yeni bir ad oluşturun.

    Bir ad oluşturmak bir kuyruk iletisinin oluşturulmasına neden olur, bu, WebJob tarafından alınır ve işlenir. WebJobs SDK, kuyruk iletisini işlemek için işlevi çağırdığında, kod kesme noktasına rastlama.

10. Hata ayıklayıcı kesme noktasına kesildiğinde, program bulutu çalıştırırken değişken değerlerini inceleyebilir ve değiştirebilirsiniz. Aşağıdaki çizimde, hata ayıklayıcı `GenerateThumbnail` metoduna geçirilen bloınfo nesnesinin içeriğini gösterir.

     ![hata ayıklayıcıda Bloınfo nesnesi](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Çalışmaya devam etmek için F5 tuşuna basın.

     Yöntemi `GenerateThumbnail` , küçük resim oluşturmayı sonlandırır.

12. Tarayıcıda, Dizin sayfasını yenileyin ve küçük resmi görürsünüz.

13. Visual Studio 'da, hata ayıklamayı durdurmak için SHIFT + F5 tuşlarına basın.

14. **Sunucu Gezgini**, ContosoAdsWebJob düğümüne sağ tıklayın ve **panoyu görüntüle**' ye tıklayın.

15. Azure kimlik bilgilerinizle oturum açın ve WebJob 'larınızın sayfasına gitmek için WebJob adına tıklayın.

     ![ContosoAdsWebJob öğesine tıklayın](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Panoda `GenerateThumbnail` işlevin yakın zamanda yürütüldüğü gösterilmektedir.

     ( **Panoyu görüntüle**' yi bir daha tıkladığınızda, oturum açmanız gerekmez ve tarayıcı doğrudan WebJob 'larınızın sayfasına gider.)

16. İşlev yürütmeye ilişkin ayrıntıları görmek için işlev adına tıklayın.

     ![İşlev ayrıntıları](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

İşleviniz [günlükleri yazsa](https://github.com/Azure/azure-webjobs-sdk/wiki), bunları görmek Için **toggleoutput** ' a tıklayabilirsiniz.

## <a name="notes-about-remote-debugging"></a>Uzaktan hata ayıklama hakkında notlar

* Üretimde hata ayıklama modunda çalıştırılması önerilmez. Üretim uygulamanız birden çok sunucu örneğine ölçeklenmez, hata ayıklama Web sunucusunun diğer isteklere yanıt vermesini engeller. Birden çok Web sunucusu örneğiniz varsa, hata ayıklayıcıya eklediğinizde rastgele bir örnek alır ve sonraki tarayıcı isteklerinin aynı örneğe gitmesini sağlamanın bir yolu yoktur. Ayrıca, genellikle üretim için bir hata ayıklama derlemesi dağıtmazsınız ve yayın yapıları için derleyici iyileştirmeleri, kaynak kodunuzda satıra göre oluşan satırı gösterme imkansızmasına neden olabilir. Üretim sorunlarını gidermek için en iyi kaynağınız, uygulama izleme ve Web sunucusu günlüklerinin bulunduğu bir uygulamadır.
* Uzun kesme noktaları uzak durur önlemek hata ayıklama. Azure, yanıt vermeyen bir işlem olarak birkaç dakikadan daha uzun süre durdurulan bir işlemi değerlendirir ve kapatır.
* Hata ayıklarken, sunucu Visual Studio 'ya veri gönderiyor ve bu da bant genişliği ücretlerini etkileyebilir. Bant genişliği ücretleri hakkında daha fazla bilgi için bkz. [Azure fiyatlandırması](https://azure.microsoft.com/pricing/calculator/).
* *Web. config* dosyasındaki `debug` `compilation` öğesinin özniteliğinin true olarak ayarlandığından emin olun. Bir hata ayıklama yapı yapılandırması yayımladığınızda varsayılan olarak true olarak ayarlanır.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Hata ayıklayıcının hata ayıklamak istediğiniz kodda yer almamasına fark ederseniz Yalnızca kendi kodum ayarını değiştirmeniz gerekebilir.  Daha fazla bilgi için bkz. [Visual Studio 'da yalnızca kendi kodum kullanarak yalnızca kullanıcı kodunda hata ayıklama yapılıp yapılmayacağını belirtme](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* Uzaktan hata ayıklama özelliğini etkinleştirdiğinizde ve 48 saat sonra özelliğin otomatik olarak kapatılması durumunda bir Zamanlayıcı başlatılır. Bu 48 saatlik sınır güvenlik ve performans nedenleriyle yapılır. Özelliği dilediğiniz zaman kolayca tekrar açabilirsiniz. Etkin bir şekilde hata ayıkladığınızda devre dışı bırakmanızı öneririz.
* Hata ayıklayıcıyı yalnızca uygulama işlemini değil, herhangi bir işleme el ile ekleyebilirsiniz (W3wp. exe). Visual Studio 'da hata ayıklama modunun nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Visual Studio 'Da hata ayıklama](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="logsoverview"></a>Tanılama günlüklerine genel bakış
App Service bir uygulamada çalışan bir ASP.NET uygulaması aşağıdaki günlük türlerini oluşturabilir:

* **Uygulama izleme günlükleri**<br/>
  Uygulama, [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) sınıfının yöntemlerini çağırarak bu günlükleri oluşturur.
* **Web sunucusu günlükleri**<br/>
  Web sunucusu, uygulamaya her HTTP isteği için bir günlük girişi oluşturur.
* **Ayrıntılı hata iletisi günlükleri**<br/>
  Web sunucusu, başarısız HTTP istekleri için bazı ek bilgiler (400 veya üzeri durum koduna neden olan istekler) içeren bir HTML sayfası oluşturur.
* **Başarısız istek izleme günlükleri**<br/>
  Web sunucusu, başarısız HTTP istekleri için ayrıntılı izleme bilgileri içeren bir XML dosyası oluşturur. Web sunucusu Ayrıca bir tarayıcıdaki XML 'yi biçimlendirmek için bir XSL dosyası sağlar.

Günlüğe kaydetme uygulama performansını etkiler, bu nedenle Azure, her türden günlüğü gerektiğinde etkinleştirebilir veya devre dışı bırakabilme olanağı sağlar. Uygulama günlükleri için yalnızca belirli bir önem düzeyi düzeyinin üzerindeki Günlüklerin yazılması gerektiğini belirtebilirsiniz. Yeni bir uygulama oluşturduğunuzda, varsayılan olarak tüm günlüğe kaydetme devre dışıdır.

Günlükler, uygulamanızın dosya sistemindeki *LogFiles* klasöründeki dosyalara YAZıLıR ve FTP aracılığıyla erişilebilir. Web sunucusu günlükleri ve uygulama günlükleri de bir Azure depolama hesabına yazılabilir. Dosya sisteminde mümkün olandan daha fazla bir depolama hesabında günlük hacimlerinizi koruyabilirsiniz. Dosya sistemini kullandığınızda en fazla 100 megabayt günlük ile sınırlı olursunuz. (Dosya sistemi günlükleri yalnızca kısa süreli saklama içindir. Azure, sınıra ulaşıldıktan sonra yeni olanlar için yer açmak üzere eski günlük dosyalarını siler.)  

## <a name="apptracelogs"></a>Uygulama izleme günlükleri oluşturma ve görüntüleme
Bu bölümde, aşağıdaki görevleri gerçekleştirebilirsiniz:

* [Azure ve ASP.NET kullanmaya başlama](app-service-web-get-started-dotnet-framework.md)bölümünde oluşturduğunuz Web projesine izleme deyimleri ekleyin.
* Projeyi yerel olarak çalıştırdığınızda günlükleri görüntüleyin.
* Azure 'da çalışan uygulama tarafından oluşturulan günlükleri görüntüleyin.

WebJobs 'larda uygulama günlükleri oluşturma hakkında daha fazla bilgi için bkz. [WEBJOBS SDK kullanarak Azure kuyruk depolama ile çalışma-günlükleri yazma](https://github.com/Azure/azure-webjobs-sdk/wiki). Günlükleri görüntülemek ve bunların Azure 'da nasıl depolandığını denetlemek için aşağıdaki yönergeler, WebJobs tarafından oluşturulan uygulama günlüklerine de uygulanır.

### <a name="add-tracing-statements-to-the-application"></a>Uygulamaya izleme deyimleri ekleme
1. *Controllers\homecontroller.cs*' yi açın ve `Index`,, `About`ve `Contact` yöntemlerini, için `System.Diagnostics`deyimler ve bir `using` deyim eklemek `Trace` üzere aşağıdaki kodla değiştirin:

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Dosyanın üst `using System.Diagnostics;` kısmına bir ifade ekleyin.

### <a name="view-the-tracing-output-locally"></a>İzleme çıkışını yerel olarak görüntüleme
1. Uygulamayı hata ayıklama modunda çalıştırmak için F5 tuşuna basın.

    Varsayılan izleme dinleyicisi tüm izleme çıkışını **Çıkış** penceresine yazar ve diğer hata ayıklama çıktılarıyla birlikte. Aşağıdaki çizimde `Index` yöntemine eklediğiniz Trace deyimlerinin çıktısı gösterilmektedir.

    ![Hata ayıklama penceresinde izleme](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Aşağıdaki adımlarda, izleme çıkışının hata ayıklama modunda derlenmeksizin bir Web sayfasında nasıl görüntüleneceği gösterilmektedir.
1. Uygulama Web. config dosyasını açın (proje klasöründe bulunan) ve kapanış `<system.diagnostics>` `</configuration>` öğesinden hemen önce dosyanın sonuna bir öğe ekleyin:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

, `WebPageTraceListener` ' A `/trace.axd`göz atarak izleme çıkışını görüntülemenize izin verir.
1. Web. config dosyasında aşağıdaki `<system.web>` örnek gibi bir <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">Trace öğesi</a> ekleyin:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Uygulamayı çalıştırmak için CTRL+F5'e basın.
1. Tarayıcı penceresinin Adres çubuğunda, URL 'ye *Trace. axd* ekleyin ve ardından ENTER tuşuna basın (URL şuna benzer `http://localhost:53370/trace.axd`).
1. **Uygulama izleme** sayfasında, Ilk satırdaki **Ayrıntıları görüntüle** ' ye tıklayın (browserlink satırına değil).

    ![Trace. axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    **İstek ayrıntıları** sayfası görüntülenir ve **izleme bilgileri** bölümünde, `Index` yöntemine eklediğiniz izleme deyimlerinin çıktısını görürsünüz.

    ![Trace. axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Varsayılan `trace.axd` olarak yalnızca yerel olarak kullanılabilir. Bir uzak uygulamadan kullanılabilir hale getirmek isterseniz, aşağıdaki örnekte gösterildiği gibi `localOnly="false"` *Web. config* dosyasındaki `trace` öğesine ekleyebilirsiniz:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Ancak, bir `trace.axd` üretim uygulamasında etkinleştirme, güvenlik nedenleriyle önerilmez. Aşağıdaki bölümlerde, App Service uygulamasında izleme günlüklerini okumak için daha kolay bir yol görürsünüz.

### <a name="view-the-tracing-output-in-azure"></a>Azure 'da izleme çıkışını görüntüleme
1. **Çözüm Gezgini**, web projesine sağ tıklayın ve **Yayımla**' ya tıklayın.
2. Web 'i **Yayımla** Iletişim kutusunda **Yayımla**' ya tıklayın.

    Visual Studio, güncelleştirmenizi yayımladıktan sonra, giriş sayfanız için bir tarayıcı penceresi açar ( **bağlantı** SEKMESINDE **hedef URL** 'yi temizlemediğiniz varsayılır).
3. **Sunucu Gezgini**, uygulamanıza sağ tıklayın ve **akış günlüklerini görüntüle**' yi seçin.

    ![Akış günlüklerini bağlam menüsünde görüntüle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    **Çıkış** penceresi, günlük akış hizmetine bağlı olduğunu gösterir ve bir günlük görüntülemesi gerekmeden her dakikada bir bildirim satırı ekler.

    ![Akış günlüklerini bağlam menüsünde görüntüle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Uygulama giriş sayfanızı gösteren tarayıcı penceresinde, **iletişim**' e tıklayın.

    Birkaç saniye içinde, `Contact` yönteme eklediğiniz hata düzeyi izlemenin çıktısı **Çıkış** penceresinde görüntülenir.

    ![Çıkış penceresinde hata izleme](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio yalnızca hata düzeyi izlemeleri gösterir çünkü bu, günlük izleme hizmetini etkinleştirdiğinizde varsayılan ayardır. Yeni bir App Service uygulaması oluşturduğunuzda, daha önce ayarlar sayfasını açtığınızda gördüğünüz gibi tüm günlük kaydı varsayılan olarak devre dışıdır:

    ![Uygulama oturumu kapatma](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Ancak, **akış günlüklerini görüntüle**' yi seçtiğinizde, Visual Studio **uygulama günlüğü 'Nü (dosya sistemi)** otomatik olarak **hata**olarak değiştirdi, bu da hata düzeyi günlüklerin raporlandığı anlamına gelir. Tüm izleme günlüklerinizi görmek için bu ayarı **verbose**olarak değiştirebilirsiniz. Hatadan daha düşük bir önem düzeyi seçtiğinizde, daha yüksek önem düzeyleri için tüm Günlükler de raporlanır. Ayrıntılı ' i seçtiğinizde bilgi, uyarı ve hata günlüklerini de görürsünüz.  

5. **Sunucu Gezgini**, uygulamaya sağ tıklayın ve ardından daha önce yaptığınız gibi **ayarları görüntüle** ' ye tıklayın.
6. **Uygulama günlüğünü (dosya sistemi)** **ayrıntılı**olarak değiştirin ve ardından **Kaydet**' e tıklayın.

    ![İzleme düzeyi Verbose olarak ayarlanıyor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Şimdi **iletişim** sayfanızı gösteren tarayıcı penceresinde, **giriş**' e ve ardından **hakkında**' ya ve ardından **iletişim**' e tıklayın.

    Birkaç saniye içinde, **Çıkış** penceresi tüm izleme çıktılarınızı gösterir.

    ![Ayrıntılı izleme çıkışı](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Bu bölümde, uygulama ayarlarını kullanarak günlüğü etkinleyerek ve devre dışı bırakmış olursunuz. Ayrıca, Web. config dosyasını değiştirerek izleme dinleyicilerini etkinleştirebilir ve devre dışı bırakabilirsiniz. Ancak, Web. config dosyasının değiştirilmesi, uygulama etki alanının geri dönüşüm yapmasına neden olur, ancak uygulama yapılandırması aracılığıyla günlüğü etkinleştirmek bunu yapmaz. Sorunun yeniden oluşturulması uzun sürmesi veya aralıklı olması halinde, uygulama etki alanını geri dönüştürme işlemi "düzeltir" ve yeniden gerçekleşene kadar beklemeniz zorunlu olabilir. Azure 'da tanılamayı etkinleştirmek, uygulama etki alanını geri dönüşüme gerek kalmadan hata bilgilerini hemen yakalamaya başlayabilmenizi sağlar.

### <a name="output-window-features"></a>Çıkış penceresi özellikleri
**Çıkış** penceresinin **Microsoft Azure logs** sekmesinde birkaç düğme ve bir metin kutusu vardır:

![Günlükler sekmesi düğmeleri](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Bunlar aşağıdaki işlevleri gerçekleştirir:

* **Çıkış** penceresini temizleyin.
* Sözcük kaydırmayı etkinleştirin veya devre dışı bırakın.
* İzleme günlüklerini başlatın veya durdurun.
* Hangi günlüklerin izleneceğini belirtin.
* Günlükleri indirin.
* Günlükleri bir arama dizesine veya normal ifadeye göre filtreleyin.
* **Çıkış** penceresini kapatın.

Arama dizesi veya normal ifade girerseniz, Visual Studio bilgileri istemcide günlüğe kaydetme bilgilerini filtreler. Bu, Günlükler **Çıkış** penceresinde görüntülendikten sonra ölçütleri girebileceğiniz ve günlükleri yeniden oluşturmak zorunda kalmadan filtre ölçütlerini değiştirebileceðiniz anlamına gelir.

## <a name="webserverlogs"></a>Web sunucusu günlüklerini görüntüle
Web sunucusu günlükleri, uygulamanın tüm HTTP etkinliklerini kaydeder. Bunları **Çıkış** penceresinde görmek için bunları uygulama için etkinleştirmeniz ve Visual Studio 'yu izlemek istediğiniz söylemelisiniz.

1. **Sunucu Gezgini**açtığınız **Azure Web uygulaması yapılandırması** sekmesinde, Web sunucusu günlük kaydını **Açık**olarak değiştirin ve ardından **Kaydet**' e tıklayın.

    ![Web sunucusu günlüğünü etkinleştir](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. **Çıkış** penceresinde, **izlenecek Microsoft Azure günlüğe kaydedileceği** ' ı seçin.

    ![Hangi Azure günlüklerinin izleneceğini belirtin](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. **Microsoft Azure günlük seçenekleri** iletişim kutusunda, **Web sunucusu günlükleri**' ni seçin ve ardından **Tamam**' a tıklayın.

    ![Web sunucusu günlüklerini izleme](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Uygulamayı gösteren tarayıcı penceresinde, **giriş**' e ve ardından **hakkında**' ya ve ardından **iletişim**' e tıklayın.

    Uygulama günlükleri genellikle önce ve ardından Web sunucusu günlükleri görünür. Günlüklerin görünmesi için bir süre beklemeniz gerekebilir.

    ![Çıkış penceresinde Web sunucusu günlükleri](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Varsayılan olarak, Visual Studio 'Yu kullanarak Web sunucusu günlüklerini ilk kez etkinleştirdiğinizde, Azure günlükleri dosya sistemine yazar. Alternatif olarak, bir depolama hesabındaki bir blob kapsayıcısına Web sunucusu günlüklerinin yazılması gerektiğini belirtmek için Azure portal kullanabilirsiniz.

Portalı kullanarak bir Azure depolama hesabına Web sunucusu günlüğünü etkinleştirebilir ve sonra Visual Studio 'da günlüğü devre dışı bıraktığınızda, Visual Studio 'da günlüğü yeniden etkinleştirdiğinizde depolama hesabınızın ayarları geri yüklenir.

## <a name="detailederrorlogs"></a>Ayrıntılı hata iletisi günlüklerini görüntüle
Ayrıntılı hata günlükleri hata yanıt kodlarına (400 veya üzeri) neden olan HTTP istekleri hakkında bazı ek bilgiler sağlar. Bunları **Çıkış** penceresinde görmek için bunları uygulama için etkinleştirmeniz ve Visual Studio 'yu izlemek istediğiniz söylemelisiniz.

1. **Sunucu Gezgini**açtığınız **Azure Web uygulaması yapılandırması** sekmesinde, **ayrıntılı hata iletilerini** **Açık**olarak değiştirin ve ardından **Kaydet**' e tıklayın.

    ![Ayrıntılı hata iletilerini etkinleştir](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. **Çıkış** penceresinde, **izlenecek Microsoft Azure günlüğe kaydedileceği** ' ı seçin.

3. **Microsoft Azure günlük seçenekleri** iletişim kutusunda, **Tüm Günlükler**' e ve ardından **Tamam**' a tıklayın.

    ![Tüm günlükleri izle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Tarayıcı penceresinin Adres çubuğunda, URL 'ye bir 404 hatasına (örneğin, `http://localhost:53370/Home/Contactx`) neden olacak ek bir karakter ekleyin ve ENTER tuşuna basın.

    Birkaç saniye sonra, ayrıntılı hata günlüğü Visual Studio **çıktı** penceresinde görüntülenir.

    ![Ayrıntılı hata günlüğü-çıkış penceresi](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Denetim + bir tarayıcıda biçimlendirilen günlük çıktısını görmek için bağlantıya tıklayın:

    ![Ayrıntılı hata günlüğü-tarayıcı penceresi](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Dosya sistemi günlüklerini indir
**Çıkış** penceresinde izleyebilmeniz gereken Günlükler de bir *. zip* dosyası olarak indirilebilir.

1. **Çıkış** penceresinde, **akış günlüklerini indir**' e tıklayın.

    ![Günlükler sekmesi düğmeleri](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Dosya Gezgini, indirilen dosya seçiliyken *indirmeler* klasörünüze açılır.

    ![İndirilen dosya](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. *. Zip* dosyasını ayıklayın ve aşağıdaki klasör yapısını görürsünüz:

    ![İndirilen dosya](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Uygulama izleme günlükleri, *logfiles\application* klasöründeki *. txt* dosyalarıdır.
   * Web sunucusu günlükleri, *Logfiles\http\rawlogs* klasöründeki *. log* dosyalarıdır. Bu dosyaları görüntülemek ve işlemek için [günlük ayrıştırıcısı](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) gibi bir araç kullanabilirsiniz.
   * Ayrıntılı hata iletisi günlükleri, *Logfiles\detailederrors* klasöründeki *. html* dosyalarıdır.

     ( *Dağıtımlar* klasörü, kaynak denetimi yayımlama tarafından oluşturulan dosyalara yöneliktir; Visual Studio yayımlamayla ilgili hiçbir şey yoktur. *Git* klasörü, kaynak denetimi yayımlama ve günlük dosyası akış hizmeti ile ilgili izlemeler içindir.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="failedrequestlogs"></a>Başarısız istek izleme günlüklerini görüntüle
Başarısız istek izleme günlükleri, URL yeniden yazma veya kimlik doğrulama sorunları gibi senaryolarda IIS 'nin bir HTTP isteğini nasıl ele aldığı hakkındaki ayrıntıları anlamanız gerektiğinde kullanışlıdır.

App Service uygulamalar, IIS 7,0 ve üzeri sürümlerde bulunan başarısız istek izleme işlevini kullanır. Ancak hangi hataların günlüğe alınacağını yapılandıran IIS ayarlarına erişiminiz yok. Başarısız istek izlemeyi etkinleştirdiğinizde, tüm hatalar yakalanır.

Visual Studio 'Yu kullanarak başarısız istek izlemeyi etkinleştirebilirsiniz, ancak bunları Visual Studio 'da görüntüleyemezsiniz. Bu Günlükler XML dosyalarıdır. Akış günlüğü hizmeti yalnızca düz metin modu: *. txt*, *. html*ve *. log* dosyalarında okunabilir kabul edilen dosyaları izler.

Başarısız istek izleme günlüklerini bir tarayıcıda doğrudan FTP aracılığıyla veya yerel bilgisayarınıza indirmek için FTP aracını kullanarak yerel olarak görüntüleyebilirsiniz. Bu bölümde, bunları doğrudan bir tarayıcıda görüntüleyebilirsiniz.

1. **Sunucu Gezgini**açtığınız **Azure Web uygulaması** penceresinin **yapılandırma** sekmesinde, **başarısız Istek izlemeyi** **Açık**olarak değiştirin ve ardından **Kaydet**' e tıklayın.

    ![Başarısız istek izlemeyi etkinleştir](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Uygulamayı gösteren tarayıcı penceresinin Adres çubuğunda, URL 'ye fazladan bir karakter ekleyin ve 404 hatasına yol açabilecek şekilde ENTER ' a tıklayın.

    Bu, bir başarısız istek izleme günlüğünün oluşturulmasına neden olur ve aşağıdaki adımlarda günlüğün nasıl görüntüleneceği veya indirileceği gösterilmektedir.

3. Visual Studio 'da, **Azure Web uygulaması** penceresinin **yapılandırma** sekmesinde **Yönetim Portalı aç**' a tıklayın.

4. Uygulamanızın [Azure Portal](https://portal.azure.com) **ayarları** sayfasında, **dağıtım kimlik bilgileri**' ne tıklayın ve ardından yeni bir Kullanıcı adı ve parola girin.

    ![Yeni FTP Kullanıcı adı ve parolası](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Oturum açtığınızda, tam Kullanıcı adını, ön eki olan uygulama adıyla birlikte kullanmanız gerekir. Örneğin, Kullanıcı adı olarak "MyID" girerseniz ve site "myExample" ise, "myexample\myıd" olarak oturum açın.
    >

5. Yeni bir tarayıcı penceresinde, uygulamanızın **genel bakış** sayfasında **FTP konak** adı veya **FTPS ana bilgisayar adı** altında gösterilen URL 'ye gidin.

6. Daha önce oluşturduğunuz FTP kimlik bilgilerini (Kullanıcı adı için uygulama adı öneki dahil) kullanarak oturum açın.

    Tarayıcı, uygulamanın kök klasörünü gösterir.

7. *LogFiles* klasörünü açın.

    ![LogFiles klasörünü aç](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. W3SVC ve bir sayısal değer adlı klasörü açın.

    ![W3SVC klasörünü aç](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Klasörü, başarısız istek izlemeyi etkinleştirdikten sonra günlüğe kaydedilen hatalar için XML dosyalarını ve bir tarayıcının XML 'yi biçimlendirmek için kullanabileceği bir XSL dosyasını içerir.

    ![W3SVC klasörü](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. İzleme bilgilerini görmek istediğiniz başarısız isteğin XML dosyasına tıklayın.

    Aşağıdaki çizimde bir örnek hata için izleme bilgilerinin bir kısmı gösterilmektedir.

    ![Tarayıcıda başarısız istek izleme](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Sonraki Adımlar
Visual Studio 'Nun bir App Service uygulaması tarafından oluşturulan günlükleri görüntülemeyi nasıl kolaylaştırdığını gördünüz. Aşağıdaki bölümler ilgili konularda daha fazla kaynağa bağlantılar sağlar:

* App Service sorunlarını giderme
* Visual Studio'da Hata Ayıklama
* Azure 'da uzaktan hata ayıklama
* ASP.NET uygulamalarında izleme
* Web sunucusu günlüklerini çözümleme
* Başarısız istek izleme günlükleri çözümleniyor
* Hata ayıklama Cloud Services

### <a name="app-service-troubleshooting"></a>App Service sorunlarını giderme
Azure App Service uygulamalarla ilgili sorun giderme hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Uygulamaları izleme](web-sites-monitor.md)
* [Visual Studio 2013 ile Azure App Service bellek sızıntılarını araştırma](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Yönetilen bellek sorunlarını analiz etmek için Visual Studio ile ilgili Microsoft ALM blog gönderisi özellikleri.
* [Bilmeniz gereken çevrimiçi araçları Azure App Service](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Amit Apple tarafından blog gönderisi.

Belirli bir sorun giderme sorusu hakkında yardım için aşağıdaki forumlardan birinde bir iş parçacığı başlatın:

* [ASP.NET sitesindeki Azure Forumu](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [MSDN 'Deki Azure Forumu](https://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Visual Studio'da Hata Ayıklama
Visual Studio 'da hata ayıklama modunun nasıl kullanılacağı hakkında daha fazla bilgi için bkz. Visual [Studio 'Da hata](/visualstudio/debugger/debugging-in-visual-studio) ayıklama ve [Visual Studio 2010 Ile hata ayıklama ipuçları](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Azure 'da uzaktan hata ayıklama
App Service uygulamalar ve Web Işleri için uzaktan hata ayıklama hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Uzaktan hata ayıklama Azure App Service giriş](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Uzaktan hata ayıklama Azure App Service Bölüm 2 ' ye giriş-uzak hata ayıklama Içinde](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Azure App Service Bölüm 3 ' te uzaktan hata ayıklamaya giriş-çok örnekli ortam ve GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Web Işleri hata ayıklaması (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Uygulamanız bir Azure Web API 'SI veya arka uç Mobile Services kullanıyorsa ve hata ayıklaması yapmanız gerekiyorsa, bkz. [Visual Studio 'da .net arka uca hata ayıklama](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>ASP.NET uygulamalarında izleme
ASP.NET izlemeye yönelik kapsamlı ve güncel tanıtımları Internet 'te bulunabilir. En iyi yöntem, MVC henüz olmadığı için Web Forms yazılmış eski giriş malzemeleri ile çalışmaya başlayın ve bu, belirli sorunlara odaklanarak daha yeni blog gönderileriyle birlikte ek niteliğindedir. Başlamak için bazı iyi konumlar aşağıdaki kaynaklardır:

* [İzleme ve telemetri (Azure Ile gerçek hayatta bulut uygulamaları oluşturma)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Azure bulut uygulamalarında izlemeye yönelik önerilere sahip E-kitap bölümü.
* [ASP.NET Izleme](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Konunun temel tanıtımı için eski ancak hala iyi bir kaynak.
* [İzleme dinleyicileri](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  İzleme dinleyicileri hakkında bilgiler, ancak [Webpagetkıcelistener](/dotnet/api/system.web.webpagetracelistener)'tan bahsetmez.
* [Gidiş System. Diagnostics Izleme ile ASP.NET Izlemesini tümleştirme](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Bu makale de Eskiler, ancak giriş makalesinin kapsamadığında bazı ek bilgiler içerir.
* [ASP.NET MVC Razor görünümlerinde izleme](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Razor görünümlerinde izlemenin yanı sıra, bu gönderi bir MVC uygulamasında işlenmeyen tüm özel durumları günlüğe kaydetmek için bir hata filtresi oluşturmayı da açıklar. Web Forms uygulamasındaki tüm işlenmemiş özel durumların nasıl günlüğe kaydedilecek hakkında daha fazla bilgi için, MSDN 'de [hata işleyicileri için](/previous-versions/bb397417(v=vs.140)) bkz. Global. asax örneği. MVC veya Web Forms, bazı özel durumları günlüğe kaydetmek, ancak varsayılan çerçeve işleme için geçerli olmasını sağlamak istiyorsanız, aşağıdaki örnekte olduğu gibi yakalayabilir ve yeniden oluşturabilirsiniz:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Azure komut satırından akış tanılama Izleme günlüğü (ek olarak, göz at!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Bu öğreticinin Visual Studio 'da nasıl yapılacağını gösteren komut satırını kullanma. [Göz at](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) ASP.NET uygulamalarında hata ayıklamak için bir araçtır.
* David Ebbo ile [Web Apps ve akış günlükleri](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/) [Ile Web Apps günlüğe kaydetme ve tanılama kullanma](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/)<br>
  Scott Hanselman ve David Ebbo tarafından videolar.

Hata günlüğü için, kendi izleme kodunuzu yazmanın alternatifi, [ELMAH](https://nuget.org/packages/elmah/)gibi açık kaynaklı bir günlük çerçevesini kullanmaktır. Daha fazla bilgi için bkz. [Scott Hanselman 'ıN ELMAH hakkındaki blog gönderileri](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Ayrıca, Azure 'dan akış günlükleri almak için ASP.net `System.Diagnostics` veya izleme kullanmanıza gerek yoktur. App Service uygulama akış günlüğü hizmeti, *LogFiles* klasöründe bulduğu tüm *. txt*, *. html*veya *. log* dosyalarını akışlar. Bu nedenle, uygulamanın dosya sistemine yazan kendi günlük sisteminizi oluşturabilirsiniz ve dosyanız otomatik olarak akışa ve indirilir. Tüm yapmanız gereken, *d:\home\logfiles* klasöründe dosya oluşturan bir uygulama kodu yazmak.

### <a name="analyzing-web-server-logs"></a>Web sunucusu günlüklerini çözümleme
Web sunucusu günlüklerinin çözümlenmesi hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Web sunucusu günlüklerinde ( *. log* dosyaları) veri görüntülemeye yönelik bir araç.
* [LogParser kullanarak IIS performans sorunlarını veya uygulama hatalarını giderme](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Web sunucusu günlüklerini çözümlemek için kullanabileceğiniz, Günlük ayrıştırıcı aracına giriş.
* [LogParser kullanarak Robert McMurray tarafından blog gönderileri](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [IIS 7,0, IIS 7,5 ve IIS 8,0 'deki HTTP durum kodu](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Başarısız istek izleme günlükleri çözümleniyor
Microsoft TechNet Web sitesi, bu günlüklerin nasıl kullanılacağını anlamak için faydalı olabilecek bir [başarısız Istek izleme](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) bölümü içerir. Ancak, bu belge temel olarak, Azure App Service ' de yapamıyorsanız, IIS 'de başarısız istek izlemeyi yapılandırmaya odaklanmaktadır.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
