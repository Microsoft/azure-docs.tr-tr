---
title: IBM ana bilgisayarları üzerinde 3270 uygulamalarına bağlanma-Azure Logic Apps
description: Azure Logic Apps ve IBM 3270 bağlayıcısını kullanarak Azure ile 3270 ekran odaklı uygulamayı tümleştirin ve otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: f039d0cbfa2b34fecbcdee53ebe2b56b6e9b6d69
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787566"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Azure Logic Apps ve IBM 3270 bağlayıcısını kullanarak Azure ile IBM ana bilgisayarları 'nda 3270 ekran odaklı uygulamayı tümleştirin

> [!NOTE]
> Bu bağlayıcı [*genel önizlemede*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Azure Logic Apps ve IBM 3270 Bağlayıcısı sayesinde, genellikle 3270 öykünücü ekranlarda gezinerek sürücünüzdeki IBM ana bilgisayar uygulamalarına erişebilir ve bunları çalıştırabilirsiniz. Bu şekilde, Azure Logic Apps ile otomatik iş akışları oluşturarak IBM ana bilgisayar uygulamalarınızı Azure, Microsoft ve diğer uygulamalar, hizmetler ve sistemlerle tümleştirebilirsiniz. Bağlayıcı, TN3270 protokolünü kullanarak IBM ana bilgisayarları ile iletişim kurar ve Azure Kamu ve Azure Çin 21Vianet hariç tüm Azure Logic Apps bölgelerde kullanılabilir. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

Bu makalede 3270 bağlayıcısını kullanmanın şu yönleri açıklanmaktadır: 

* Azure Logic Apps 'de IBM 3270 bağlayıcısını neden kullanmalısınız ve bağlayıcının 3270 ekran odaklı uygulamaları nasıl çalıştırması

* 3270 bağlayıcısını kullanmaya yönelik önkoşullar ve kurulum

* Mantıksal uygulamanıza 3270 bağlayıcı eylemleri ekleme adımları

## <a name="why-use-this-connector"></a>Bu bağlayıcı neden kullanılmalıdır?

IBM ana bilgisayarları uygulamalarına erişmek için genellikle "yeşil ekran" olarak adlandırılan 3270 Terminal öykünücüsünü kullanırsınız. Bu yöntem saat ile test edilmiş bir yoldur, ancak sınırlamaları vardır. Host Integration Server (HıS), bu uygulamalarla doğrudan çalışmanıza yardımcı olmakla birlikte, bazen ekran ve iş mantığını ayırmak mümkün olmayabilir. Ya da, ana bilgisayar uygulamalarının nasıl çalıştığı hakkında daha fazla bilgi sahibi olabilirsiniz.

Bu senaryoları genişletmek için Azure Logic Apps 'deki IBM 3270 Bağlayıcısı, belirli bir görevde kullanılan ana bilgisayar ekranlarını kaydetmek veya "yakalamak" için kullandığınız 3270 tasarım aracı ile birlikte çalışarak, anabilgisayar uygulamanız aracılığıyla bu görevin gezinti akışını tanımlayabilir ve Bu görev için giriş ve çıkış parametrelerine sahip Yöntemler. Tasarım Aracı, mantıksal uygulamanızdan bu görevi temsil eden bir eylemi çağırırken, bu bilgileri 3270 bağlayıcısının kullandığı meta verilere dönüştürür.

Tasarım aracından meta veri dosyası oluşturduktan sonra, bu dosyayı Azure 'da bir tümleştirme hesabına eklersiniz. Bu şekilde, bir 3270 bağlayıcı eylemi eklediğinizde mantıksal uygulamanız uygulamanızın meta verilerine erişebilir. Bağlayıcı, tümleştirme hesabınızdan meta veri dosyasını okur, 3270 ekranlarıyla gezinmeyi işler ve 3270 bağlayıcı eyleminin parametrelerini dinamik olarak gösterir. Daha sonra konak uygulamasına veri sağlayabilirsiniz ve bağlayıcı sonuçları mantıksal uygulamanıza geri döndürür. Bu şekilde, eski uygulamalarınızı Azure, Microsoft ve Azure Logic Apps desteklediği diğer uygulamalar, hizmetler ve sistemlerle tümleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Önerilen: bir [tümleştirme hizmeti ortamı (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Mantıksal uygulamanızı oluşturmak ve çalıştırmak için konum olarak bu ortamı seçebilirsiniz. ISE, mantıksal uygulamanızdan Azure sanal ağları içinde korunan kaynaklara erişim sağlar.

* 3270 ekran odaklı uygulamanızı otomatik hale getirmek ve çalıştırmak için kullanılacak mantıksal uygulama

  IBM 3270 bağlayıcısının tetikleyicisi yoktur, bu nedenle, **yineleme** tetikleyicisi gibi mantıksal uygulamanızı başlatmak için başka bir tetikleyici kullanın. Daha sonra 3270 bağlayıcı eylemi ekleyebilirsiniz. Başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  ISE kullanıyorsanız, mantıksal uygulamanızın konumu olarak o ıSE 'yi seçin.

* [3270 tasarım aracını indirip yükleyin](https://aka.ms/3270-design-tool-download).
Tek önkoşul [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Bu araç, uygulamanızda 3270 bağlayıcı eylemleri ekleyip çalıştırdığınız görevler için ekranları, gezinti yollarını, yöntemleri ve parametreleri kaydetmenize yardımcı olur. Araç, ana bilgisayar uygulamanızı yönlendirmede kullanılacak bağlayıcı için gerekli meta verileri sağlayan bir Host Integration Designer XML (HIDX) dosyası oluşturur.
  
  Bu aracı indirip yükledikten sonra, ana bilgisayarınıza bağlanmak için şu adımları izleyin:

  1. 3270 tasarım aracını açın. **Oturum** menüsünden **konak oturumları**' nı seçin.
  
  1. TN3270 ana bilgisayar sunucunuzun bilgilerini sağlayın.

* Mantıksal uygulamanızın bu dosyadaki meta verilere ve Yöntem tanımlarına erişebilmesi için HIDX dosyanızı eşleme olarak depoladığınız yer olan bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). 

  Tümleştirme hesabınızın, kullanmakta olduğunuz mantıksal uygulamayla bağlantılı olduğundan emin olun. Ayrıca, bir ıSE kullanıyorsanız, tümleştirme hesabınızın konumunun mantıksal uygulamanızın kullandığı aynı ıSE olduğundan emin olun.

* Ana bilgisayar uygulamanızı barındıran TN3270 sunucusuna erişim

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Meta verilere genel bakış oluştur

3270 ekran odaklı bir uygulamada ekranlar ve veri alanları senaryolarınız için benzersizdir, bu nedenle 3270 Bağlayıcısı, uygulamanız hakkında, meta veri olarak sağlayabilmeniz için bu bilgilere ihtiyaç duyuyor. Bu meta veriler, mantıksal uygulamanızın ekranları tanımlamasına ve tanımasına yardımcı olan bilgileri açıklar, ekranlar arasında nasıl gezindiğini, veri girişi ve sonuçların nerede beklendiğini açıklar. Bu meta verileri belirtmek ve oluşturmak için, daha sonra daha fazla ayrıntı bölümünde açıklandığı gibi, bu belirli *modlarda*veya aşamada size kılavuzluk eden 3270 tasarım aracını kullanın:

* **Yakala**: Bu modda, ana bilgisayar uygulamanızla belirli bir görevi tamamlamak için gereken ekranları kaydeder, örneğin bir banka bakiyesi alma.

* **Gezinti**: Bu modda, belirli bir görev için ana bilgisayar uygulamanızın ekranlarında gezinme planını veya yolunu belirtirsiniz.

* **Yöntemler**: Bu modda, bir yöntemi, örneğin `GetBalance`, ekran gezinti yolunu açıklayan tanımlar. Her ekranda, yöntemin giriş ve çıkış parametreleri haline gelen alanları da seçersiniz.

### <a name="unsupported-elements"></a>Desteklenmeyen öğeler

Tasarım aracı bu öğeleri desteklemez:

* Kısmi IBM temel eşleme desteği (BMS) eşlemeleri: bir BMS haritasını içeri aktarırsanız, tasarım aracı kısmi ekran tanımlarını yoksayar.
* In/out parametreleri: ın/out parametrelerini tanımlayamazsınız.
* Menü işleme: Önizleme sırasında desteklenmez
* Dizi işleme: Önizleme sırasında desteklenmez

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Ekranları yakala

Bu modda, bu ekranı benzersiz bir şekilde tanımlayan her bir 3270 ekranındaki bir öğeyi işaretlersiniz. Örneğin, belirli metin ve boş olmayan bir alan gibi bir metin satırı veya daha karmaşık bir koşullar kümesi belirtebilirsiniz. Bu ekranları ana bilgisayar sunucusuna canlı bir bağlantı üzerinden kaydedebilir veya bu bilgileri bir IBM Basic eşleme desteği (BMS) eşlemesinden içeri aktarabilirsiniz. Canlı bağlantı, konağa bağlanmak için bir TN3270 öykünücüsü kullanır. Her bağlayıcı eylemi, oturumunuza bağlanarak başlayan tek bir göreve eşlenmelidir ve oturumunuzla bağlantıyı kesmeden sona ermelidir.

1. Henüz yapmadıysanız, 3270 tasarım aracını açın. Yakalama moduna girmek için araç çubuğunda **yakala** ' yı seçin.

1. Kaydı başlatmak için F5 tuşuna basın veya **kayıt** menüsünde **kaydı Başlat**' ı seçin. 

1. **Oturum** menüsünden **Bağlan**' ı seçin.

1. **Yakalama** bölmesinde, uygulamanızdaki ilk ekrandan başlayarak, kaydettiğiniz belirli bir görev için uygulamanızdaki adımları izleyin.

1. Görevi tamamladıktan sonra, genellikle yaptığınız gibi uygulamanızda oturumunuzu kapatın.

1. **Oturum** menüsünden **bağlantıyı kes**' i seçin.

1. Kaydı durdurmak için SHIFT + F5 tuşlarına basın ya da **kayıt** menüsünde **Kaydı Durdur**' u seçin.

   Bir görev için ekranları yakaladıktan sonra tasarımcı aracı Bu ekranları temsil eden küçük resimleri gösterir. Bu küçük resimler hakkında bazı notlar:

   * Yakalanan ekranlarınız dahil olmak üzere, "Empty" adlı bir ekranınız vardır.

     [CICS](https://www.ibm.com/it-infrastructure/z/cics)'e ilk kez bağlandığınızda, çalıştırmak istediğiniz işlemin adını girmeden önce "Clear" tuşunu göndermeniz gerekir. "Clear" tuşunun gönderileceği ekran, ekran adı gibi herhangi bir *tanıma özniteliği*(ekran tanıma Düzenleyicisi kullanarak ekleyebileceğiniz) içermez. Bu ekranı göstermek için, küçük resimler "Empty" adlı bir ekran içerir. Daha sonra bu ekranı, işlem adını girdiğiniz ekranı göstermek için kullanabilirsiniz.

   * Varsayılan olarak, yakalanan bir ekranın adı ekrandaki ilk sözcüğü kullanır. Bu ad zaten varsa, tasarım aracı adı bir alt çizgi ve sayı ile ekler; örneğin, "ILEGB" ve "WBGB_1".

1. Yakalanan bir ekrana daha anlamlı bir ad vermek için aşağıdaki adımları izleyin:

   1. **Konak ekranları** bölmesinde, yeniden adlandırmak istediğiniz ekranı seçin.

   1. Aynı bölmede, aynı bölmedeki en alt kısımdaki **ekran adı** özelliğini bulun.

   1. Geçerli ekran adını daha açıklayıcı bir adla değiştirin.

1. Şimdi her ekranı tanımlamaya yönelik alanları belirtin.

   3270 veri akışıyla, ekranların varsayılan tanımlayıcıları yoktur, bu nedenle her ekranda benzersiz metin seçmeniz gerekir. Karmaşık senaryolar için, örneğin benzersiz metin ve belirli bir koşula sahip bir alan gibi birden çok koşul belirtebilirsiniz.

Tanıma alanlarını seçmeyi tamamladıktan sonra, bir sonraki moda geçin.

### <a name="conditions-for-identifying-repeated-screens"></a>Yinelenen ekranları tanımlamaya yönelik koşullar

Bağlayıcının ekranlarda gezinme ve aralarında ayrım yapmak için genellikle yakalanan ekranlar arasında tanımlayıcı olarak kullanabileceğiniz bir ekranda benzersiz metin bulursunuz. Yinelenen ekranlar için daha fazla tanımlama yöntemi gerekebilir. Örneğin, bir ekran farklı bir değer döndürdüğünde, diğer ekran bir hata mesajı döndürdüğünde, aynı görünen iki ekranınızın olduğunu varsayalım.

Tasarım aracında, ekran tanıma düzenleyicisini kullanarak, örneğin "hesap bakiyesini al" gibi bir ekran başlığı gibi *tanıma öznitelikleri*ekleyebilirsiniz. Dallanmış bir yolunuz varsa ve her iki dal de aynı ekranı, ancak farklı sonuçlarla döndürürler, diğer tanıma özniteliklerine ihtiyacınız vardır. Çalışma zamanında, bağlayıcı geçerli dalı ve çatalı belirlemek için bu öznitelikleri kullanır. Kullanabileceğiniz koşullar aşağıda verilmiştir:

* Belirli değer: Bu değer belirtilen konumdaki belirtilen dizeyle eşleşir.
* Belirli bir değer DEĞIL: Bu değer belirtilen konumda belirtilen dize ile eşleşmiyor.
* Boş: Bu alan boştur.
* Boş DEĞIL: Bu alan boş değil.

Daha fazla bilgi için bu konunun ilerleyen kısımlarında [örnek gezinti planına](#example-plan) bakın.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Gezinti planlarını tanımlama

Bu modda, belirli bir görevde ana bilgisayar uygulamanızın ekranlarıyla gezinmek için akışı veya adımları tanımlarsınız. Örneğin, bazen uygulamanızın bir yolun doğru sonucu ürettiği yere gidebileceği birden fazla yolunuz olabilir, ancak diğer yol bir hata oluşturur. Her ekran için, `CICSPROD <enter>`gibi bir sonraki ekrana geçmek için gereken tuş vuruşlarını belirtin.

> [!TIP]
> Aynı Connect ve Disconnect ekranlarını kullanan çeşitli görevleri otomatikleştirmeye başladıysanız, tasarım aracı özel bağlantı ve kesme planı türleri sağlar. Bu planları tanımlarken, bunları gezinti planınızın başlangıcına ve sonuna ekleyebilirsiniz.

### <a name="guidelines-for-plan-definitions"></a>Plan tanımlarına yönelik yönergeler

* Bağlantı kesme ile başlayıp sona ermek üzere tüm ekranları dahil edin.

* Tek başına bir plan oluşturabilir veya bağlantı ve bağlantı kesme planlarını kullanarak tüm işlemleriniz için ortak bir dizi ekranı yeniden kullanmanızı sağlayabilirsiniz.

  * Bağlama planınızdaki son ekran, gezinti planınızdaki ilk ekranla aynı ekran olmalıdır.

  * Bağlantı kesme planınızdaki ilk ekran, gezinti planınızdaki son ekranla aynı ekran olmalıdır.

* Yakalanan ekranlarınız çok sayıda yinelenen ekran içerebilir, bu nedenle planınızda yinelenen ekranların yalnızca bir örneğini seçin ve kullanın. Yinelenen ekranların bazı örnekleri aşağıda verilmiştir:

  * Oturum açma ekranı, örneğin, **msg-10** ekranı
  * CICS için hoş geldiniz ekranı
  * "Temizle" veya **boş** ekran

<a name="create-plans"></a>

### <a name="create-plans"></a>Plan oluşturma

1. Gezinti moduna girmek için 3270 Tasarım aracının araç çubuğunda **Gezinti** ' yi seçin.

1. Planınızı başlatmak için, **Gezinti** bölmesinde **Yeni plan**' ı seçin.

1. **Yeni plan adı Seç**' in altında planınız için bir ad girin. **Tür** listesinden plan türünü seçin:

   | Plan türü | Açıklama |
   |-----------|-------------|
   | **İşle** | Tek başına veya Birleşik planlar için |
   | **Bağlanma** | Bağlantı planları için |
   | **Ayır** | Bağlantı kesme planları için |
   |||

1. **Ana bilgisayar ekranları** bölmesinden, yakalanan küçük resimleri **Gezinti** bölmesindeki gezinti planı yüzeyine sürükleyin.

   İşlem adını girdiğiniz boş ekranı göstermek için "boş" ekranını kullanın.

1. Ekranları, tanımladığınız görevi açıklayan sırada düzenleyin.

1. Çatallar ve birleşimler dahil olmak üzere ekranlar arasında akış yolunu tanımlamak için, Tasarım aracının araç çubuğunda **Flow**' u seçin.

1. Akıştaki ilk ekranı seçin. Akışta bir sonraki ekrana bir bağlantı sürükleyip bırakın.

1. Her ekran için, **Yardım anahtarı** özelliği (dikkat tanımlayıcısı) ve **sabit metin** özelliği için, akışı bir sonraki ekrana taşınan değerleri girin.

   Yalnızca yardım anahtarınız veya hem yardım anahtarı hem de sabit metin olabilir.

Gezinti planınızı tamamladıktan sonra, [sonraki modda yöntemleri tanımlayabilirsiniz](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Örnek

Bu örnekte, aşağıdaki adımlara sahip "WBGB" adlı bir CICS Transaction çalıştırdığınızı varsayalım: 

* İlk ekranda bir ad ve hesap girersiniz.
* İkinci ekranda hesap bakiyesini alırsınız.
* "Boş" ekranına çıkmalısınız.
* CICS ' den "MSG-10" ekranına oturumunuzu kapatın.

Ayrıca, bu adımları yinelediğinizi, ancak hatayı gösteren ekranı yakalamak için hatalı veriler girdiğinizi varsayalım. Yakaladığınız ekranlar aşağıda verilmiştir:

* ILETI-10
* CICS hoş geldiniz
* Olmamalıdır
* WBGB_1 (giriş)
* WBGB_2 (hata)
* Empty_1
* MSG-10_1

Burada birçok ekran benzersiz adlara sahip olsa da, bazı ekranlar aynı ekrandır, örneğin "MSG-10" ve "Empty". Yinelenen bir ekran için, planınızdaki bu ekran için yalnızca bir örnek kullanın. Bir tek başına plan, bağlanma planı, bağlantı kesme planı ve Birleşik bir planın nasıl görünebileceğini gösteren örnekler şunlardır:

* Tek başına plan

  ![Tek başına gezinti planı](./media/connectors-create-api-3270/standalone-plan.png)

* Bağlantı planı

  ![Bağlantı planı](./media/connectors-create-api-3270/connect-plan.png)

* Planın bağlantısını kes

  ![Planın bağlantısını kes](./media/connectors-create-api-3270/disconnect-plan.png)

* Birleşik plan

  ![Birleşik plan](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Örnek: yinelenen ekranları tanımla

Bağlayıcının gezinme ve ayırt edilebilmesi için, genellikle yakalanan ekranlarda tanımlayıcı olarak kullanabileceğiniz bir ekranda benzersiz metin bulursunuz. Yinelenen ekranlar için daha fazla tanımlama yöntemi gerekebilir. Örnek planda, benzer ekranları alabileceğiniz bir çatal vardır. Bir ekran hesap dengelenmesi, diğer ekranda ise bir hata iletisi döndürülür.

Tasarım Aracı, ekran tanıma düzenleyicisini kullanarak "hesap bakiyesini al" adlı bir ekran başlığı gibi tanıma öznitelikleri eklemenize olanak tanır. Benzer ekranlarda bu durumda diğer özniteliklere de ihtiyacınız vardır. Çalışma zamanında, bağlayıcı dalı ve çatalı belirlemek için bu öznitelikleri kullanır.

* Hesap bakiyesine sahip ekran olan geçerli giriş döndüren dalda, "boş değil" koşuluna sahip bir alan ekleyebilirsiniz.

* Bir hata ile döndürülen dalda, "boş" koşuluna sahip bir alan ekleyebilirsiniz.

<a name="define-method"></a>

## <a name="define-methods"></a>Yöntemleri tanımlama

Bu modda, gezinti planınızla ilişkili bir yöntemi tanımlarsınız. Her yöntem parametresi için, bir dize, tamsayı, tarih veya saat gibi veri türünü belirtirsiniz. İşiniz bittiğinde, yönteminizi canlı konakta test edebilir ve yöntemin beklendiği gibi çalışıp çalışmadığını doğrulayabilirsiniz. Ardından, artık IBM 3270 Bağlayıcısı için bir eylem oluşturmak ve çalıştırmak için kullanılacak yöntem tanımlarına sahip olan meta veri dosyasını veya Host Integration Designer XML (HIDX) dosyasını oluşturursunuz.

1. 3270 Tasarım aracının araç çubuğunda Yöntemler modunu girmeniz için **Yöntemler** ' i seçin. 

1. **Gezinti** bölmesinde, istediğiniz giriş alanlarının bulunduğu ekranı seçin.

1. Yönteminizin ilk giriş parametresini eklemek için aşağıdaki adımları izleyin:

   1. **Yakala** bölmesinde, 3270 öykünücü ekranında, yalnızca alanın içinde değil, ilk giriş olarak istediğiniz alanın tamamını seçin.

      > [!TIP]
      > Tüm alanları görüntülemek ve tüm alanını seçtiğinizden emin olmak için, **Görünüm** menüsünde **tüm alanlar**' ı seçin.

   1. Tasarım aracının araç çubuğunda **giriş alanı**' nı seçin. 

   Daha fazla giriş parametresi eklemek için, önceki adımları her parametre için tekrarlayın.

1. Yönteminizin ilk çıkış parametresini eklemek için aşağıdaki adımları izleyin:

   1. **Yakalama** bölmesinde, 3270 öykünücü ekranında, yalnızca alanın içinde değil, ilk çıkış olarak kullanmak istediğiniz alanın tamamını seçin.

      > [!TIP]
      > Tüm alanları görüntülemek ve tüm alanını seçtiğinizden emin olmak için, **Görünüm** menüsünde **tüm alanlar**' ı seçin.

   1. Tasarım aracının araç çubuğunda **Çıkış alanı**' nı seçin.

   Daha fazla çıkış parametresi eklemek için, önceki adımları her parametre için tekrarlayın.

1. Tüm yönteminizin parametrelerini ekledikten sonra, her bir parametre için bu özellikleri tanımlayın:

   | Özellik adı | Olası değerler | 
   |---------------|-----------------|
   | **Veri türü** | Byte, tarih saat, Decimal, INT, Long, Short, String |
   | **Alan dolgusu tekniği** | Parametreler, gerekirse boşluklar ile doldurarak bu doldurma türlerini destekler: <p><p>- **türü**: alana sırayla karakterleri girin. <p>- **dolgusu**: alanın içeriğini, gerekirse boşluklar ile doldurarak karakterler ile değiştirin. <p>- **silinebilir OfType**: alanı temizleyin ve ardından alana sırayla karakterler girin. |
   | **Biçim dizesi** | Bazı parametre veri türleri bir biçim dizesi kullanır ve bu, 3270 bağlayıcısından metni ekrandan bir .NET veri türüne nasıl dönüştürmeye bildirir: <p><p>- **DateTime**: DateTime biçim dizesi, [.NET özel tarih ve saat biçimi dizelerini](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)izler. Örneğin, tarih `06/30/2019` `MM/dd/yyyy`biçim dizesini kullanır. <p>- **Decimal**: Decimal biçim dizesi [COBOL Picture yan tümcesini](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html)kullanır. Örneğin sayı `100.35` `999V99`biçim dizesini kullanır. |
   |||

## <a name="save-and-view-metadata"></a>Meta verileri kaydetme ve görüntüleme

Yönteminizi tanımladıktan sonra, ancak yönteminizi test etmeden önce, şimdiye kadar tanımladığınız tüm bilgileri bir RAP (. rap) dosyasına kaydedin.
Dilediğiniz zaman herhangi bir modda bu RAP dosyasına kaydedebilirsiniz. Tasarım aracı Ayrıca, bu konumdaki Tasarım aracının yükleme klasörüne gidip "WoodgroveBank. rap" dosyasını açarak açabileceğiniz ve gözden geçirebilmeniz gereken örnek bir RAP dosyası içerir:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Ancak, örnek rap dosyasında yapılan değişiklikleri kaydetmeyi veya dosya tasarım aracının yükleme klasöründe kaldığında örnek RAP dosyasından bir HIDX dosyası oluşturmayı denerseniz, "erişim reddedildi" hatası alabilirsiniz. Varsayılan olarak, tasarım aracı, Program Files klasörünüze yükseltilmiş izinler olmadan yüklenir. Bir hata alırsanız şu çözümlerden birini deneyin:

* Örnek dosyayı farklı bir konuma kopyalayın.
* Tasarım aracını yönetici olarak çalıştırın.
* SDK klasörü için kendinize sahip olun.

## <a name="test-your-method"></a>Yönteminizi test etme

1. Yönteminizi canlı ana bilgisayara karşı çalıştırmak için, hala Yöntemler modundayken F5 tuşuna basın veya Tasarım aracının araç çubuğundan **Çalıştır**' ı seçin.

   > [!TIP]
   > Modları dilediğiniz zaman değiştirebilirsiniz. **Dosya** menüsünde **mod**' u seçin ve istediğiniz modu seçin.

1. Parametrelerinizin değerlerini girin ve **Tamam**' ı seçin.

1. Sonraki ekrana devam etmek için **İleri**'yi seçin.

1. İşiniz bittiğinde, çıkış parametresi değerlerinizi gösteren **bitti**' yi seçin.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>HIDX dosyası oluşturma ve karşıya yükleme

Hazırsanız, tümleştirme hesabınıza yükleyebilmeniz için HIDX dosyasını oluşturun. 3270 tasarım aracı, HDX dosyasını, RAP Dosyanızı kaydettiğiniz yeni bir alt klasörde oluşturur.

1. 3270 Tasarım aracının araç çubuğunda **kod oluştur**' u seçin.

1. RAP dosyanızı içeren klasöre gidin ve HIDX dosyanız üretildikten sonra aracın oluşturduğu alt klasörü açın. Aracın HIDX dosyası oluşturduğunu doğrulayın.

1. [Azure Portal](https://portal.azure.com)oturum açın ve tümleştirme hesabınızı bulun.

1. CDX Dosyanızı tümleştirme hesabınıza bir eşleme olarak ekleyin, [haritalar eklemek için bu benzer adımları izleyin](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), ancak harita türünü seçtiğinizde, **hidx**' i seçin.

Bu konunun ilerleyen kısımlarında, mantıksal uygulamanıza ilk kez bir IBM 3270 eylemi eklediğinizde, tümleştirme hesabınız ve ana bilgisayar sunucunuzun adları gibi bağlantı bilgilerini sağlayarak mantıksal uygulamanız ve ana bilgisayar sunucusu arasında bir bağlantı oluşturmanız istenir. . Bağlantıyı oluşturduktan sonra, önceden eklemiş olan HIDX dosyanızı, çalıştırılacak yöntemi ve kullanılacak parametreleri seçebilirsiniz.

Tüm bu adımları tamamladığınızda, IBM ana bilgisayarınıza bağlanmak için mantıksal uygulamanızda oluşturduğunuz eylemi kullanabilirsiniz, uygulamanıza yönelik sürücü ekranları, veri girme, sonuçları geri döndürme gibi işlemler yapabilirsiniz. Ayrıca diğer uygulamalar, hizmetler ve sistemlerle tümleştirmek için mantıksal uygulamanıza başka eylemler eklemeye devam edebilirsiniz.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>IBM 3270 eylemlerini çalıştır

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin ve **Eylem Ekle**' yi seçin. 

1. Arama kutusunda **kuruluş**' u seçin. Arama kutusuna filtreniz olarak "3270" yazın. Eylemler listesinden şu eylemi seçin: bir **TN3270 bağlantısı üzerinden bir anabilgisayar programı çalıştırır**

   ![3270 eylemini seçin](./media/connectors-create-api-3270/select-3270-action.png)

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. 
   Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Henüz bağlantı yoksa, bağlantınız için gerekli bilgileri sağlayın ve **Oluştur**' u seçin.

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Bağlantı Adı** | Yes | <*bağlantı-adı*> | Bağlantınızın adı |
   | **Tümleştirme hesabı KIMLIĞI** | Yes | <*tümleştirme-hesap-adı* > | Tümleştirme hesabınızın adı |
   | **Tümleştirme hesabı SAS URL 'SI** | Yes | <*tümleştirme-hesap-SAS-URL*> | Tümleştirme hesabınızın Azure portal, tümleştirme hesabınızın ayarlarından oluşturabileceğiniz paylaşılan erişim Imzası (SAS) URL 'SI. <p>1. tümleştirme hesabınız menüsünde, **Ayarlar**altında **geri çağırma URL 'si**' ni seçin. <br>2. sağ bölmedeki **oluşturulan geri çağırma URL 'si** değerini kopyalayın. |
   | **Sunucu** | Yes | <*TN3270-sunucu adı*> | TN3270 hizmetinizin sunucu adı |
   | **Bağlantı Noktası** | Hayır | <*TN3270-sunucu bağlantı noktası*> | TN3270 sunucunuz tarafından kullanılan bağlantı noktası. Boş bırakılırsa, bağlayıcı varsayılan değer olarak `23` kullanır. |
   | **Cihaz türü** | Hayır | <*IBM-Terminal-model*> | Benzetimi yapılacak IBM terminalinin model adı veya numarası. Boş bırakılırsa, bağlayıcı varsayılan değerleri kullanır. |
   | **Kod sayfası** | Hayır | <*kodu-sayfa numarası*> | Ana bilgisayar için kod sayfası numarası. Boş bırakılırsa, bağlayıcı varsayılan değer olarak `37` kullanır. |
   | **Mantıksal birim adı** | Hayır | <*mantıksal birim adı*> | Konaktan istek yapılacak belirli mantıksal birim adı |
   | **SSL etkinleştirilsin mi?** | Hayır | Açık veya kapalı | SSL şifrelemesini açın veya kapatın. |
   | **Ana bilgisayar SSL sertifikası mı doğrula?** | Hayır | Açık veya kapalı | Sunucunun sertifikası için doğrulamayı açın veya devre dışı bırakın. |
   ||||

   Örnek:

   ![Bağlantı özellikleri](./media/connectors-create-api-3270/connection-properties.png)

1. Eylem için gerekli bilgileri sağlayın:

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Hidx adı** | Yes | <*Hidx-dosya adı*> | Kullanmak istediğiniz 3270 HIDX dosyasını seçin. |
   | **Yöntem adı** | Yes | <*yöntemi-adı*> | Kullanmak istediğiniz HIDX dosyasındaki yöntemi seçin. Bir yöntemi seçtikten sonra, bu yöntemle kullanılacak parametreleri seçebilmeniz için **yeni parametre Ekle** listesi görüntülenir. |
   ||||

   Örnek:

   **HIDX dosyasını seçin**

   ![HIDX dosyası seçin](./media/connectors-create-api-3270/select-hidx-file.png)

   **Yöntemi seçin**

   ![Select yöntemi](./media/connectors-create-api-3270/select-method.png)

   **Parametreleri seçin**

   ![Parametreleri seç](./media/connectors-create-api-3270/add-parameters.png)

1. İşiniz bittiğinde mantıksal uygulamanızı kaydedin ve çalıştırın.

   Mantıksal uygulamanız çalışmayı bitirdikten sonra, çalıştırma adımları görüntülenir. 
   Başarılı adımlarda onay işaretleri gösterilmektedir, ancak başarısız adımlar "X" harfini gösterir.

1. Her adımın giriş ve çıkışlarını gözden geçirmek için bu adımı genişletin.

1. Çıkışları gözden geçirmek için **Ham çıkışları göster**' i seçin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için [bağlayıcının başvuru sayfasını](/connectors/si3270/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
