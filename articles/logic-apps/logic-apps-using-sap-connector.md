---
title: SAP sistemlerine bağlanma
description: Azure Logic Apps ile iş akışlarını otomatikleştirerek SAP kaynaklarına erişin ve yönetin
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 03/30/2021
tags: connectors
ms.openlocfilehash: ec5046e40b6fade0e4d56023c404cc736a46f105
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969113"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps’ten SAP sistemlerine bağlanma

Bu makalede [SAP bağlayıcısını](/connectors/sap/)kullanarak Logic Apps SAP kaynaklarınıza nasıl erişebileceğiniz açıklanır.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Henüz bir Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* SAP kaynaklarınıza erişmek istediğiniz mantıksal uygulama. Logic Apps yeni başladıysanız [Azure Portal ilk mantıksal uygulamanızı oluşturmaya yönelik](../logic-apps/quickstart-create-first-logic-app-workflow.md) [Logic Apps hizmete genel bakış](../logic-apps/logic-apps-overview.md) ve hızlı başlangıç başlığına bakın.

    * SAP bağlayıcısının kullanım dışı bırakılmış önceki bir sürümünü kullandıysanız, SAP sunucunuza bağlanabilmek için önce [geçerli bağlayıcıya geçiş](#migrate-to-current-connector) yapmanız gerekir.

    * Mantıksal uygulamanızı çok kiracılı Azure 'da çalıştırıyorsanız, [çok kiracılı önkoşullara](#multi-tenant-azure-prerequisites)bakın.

    * Mantıksal uygulamanızı Premium düzeyinde bir [tümleştirme hizmeti ortamında (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)çalıştırıyorsanız, [Ise önkoşulları](#ise-prerequisites)bölümüne bakın.

* Logic Apps erişmek istediğiniz [SAP uygulama sunucusu](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) veya [SAP ileti sunucusu](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) . Bağlayıcıyla kullanabileceğiniz SAP sunucuları ve SAP eylemleri hakkında daha fazla bilgi için bkz. [SAP uyumluluğu](#sap-compatibility).

    * SAP sunucunuzu RFC kullanımına izin verecek şekilde yapılandırmanız gerekir. Daha fazla bilgi için şu SAP notuna bakın: [460089-dış RFC programları Için en düşük yetkilendirme profilleri](https://launchpad.support.sap.com/#/notes/460089). 

* SAP sunucunuza göndermek için örnek bir IDoc dosyası gibi ileti içeriği. Bu içerik XML biçiminde olmalıdır ve kullanmak istediğiniz SAP eyleminin ad alanını içermelidir. [BIR XML zarfına sarmalayarak, düz bir dosya şeması olan IDoc 'ları gönderebilirsiniz](#send-flat-file-idocs).

* **SAP tetikleyicisinden bir ileti alındığında** kullanmak istiyorsanız, aşağıdakileri de yapmanız gerekir:

    * Bu ayarla SAP Gateway Güvenlik izinlerinizi ayarlayın: `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * Access Control listesi (ACL) bulmaya yardımcı olması için SAP Gateway Güvenlik günlüğü 'nü ayarlayın. Daha fazla bilgi için, [ağ geçidi günlüğünü ayarlamaya yönelik SAP yardım konusuna](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)bakın. Aksi takdirde şu hatayı alırsınız: `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > Bu tetikleyici, Web kancası aboneliğine hem yenileme hem de aboneliği kaldırma için aynı URI konumunu kullanır. Yenileme işlemi HTTP `PATCH` yöntemini kullanır, ancak abonelik kaldırma IŞLEMI http `DELETE` yöntemini kullanır. Bu davranış, tetikleyicisinin geçmişinde bir abonelik kaldırma işlemi olarak bir yenileme işlemi görünmesini sağlayabilir, ancak tetikleyici HTTP yöntemi olarak kullanıldığından, işlem hala bir yenilemektedir `PATCH` `DELETE` .

### <a name="sap-compatibility"></a>SAP uyumluluğu

SAP Bağlayıcısı, aşağıdaki SAP sistemleri türleriyle uyumludur:

* S/4 HANA gibi şirket içi ve bulut tabanlı HANA tabanlı SAP sistemleri.

* R/3 ve ECC gibi klasik şirket içi SAP sistemleri.

SAP Bağlayıcısı, SAP NetWeaver tabanlı sistemlerden aşağıdaki ileti ve veri tümleştirme türlerini destekler:

* Ara belge (IDoc)

* İş uygulaması programlama arabirimi (BAPı)

* Uzak Işlev çağrısı (RFC) ve Işlem RFC (tRFC)

SAP Bağlayıcısı, [sap .net Bağlayıcısı (NCo) kitaplığını](https://support.sap.com/en/product/connectors/msnet.html)kullanır. Aşağıdaki SAP eylemlerini ve bağlayıcı ile tetikleyiciyi kullanabilirsiniz:

* Şunları yapmak için kullanabileceğiniz [tRFC üzerinde IDoc 'ları göndermek](#send-idoc-action) üzere **SAP 'ye ileti gönderin** :

    * [RFC üzerinde BAPı işlevlerini çağırma](#call-bapi-action)

    * SAP sistemlerinde RFC/tRFC çağırma

    * Durum bilgisi olan oturumlar oluşturma veya kapatma

    * BAPı işlemlerini işleme veya geri alma

    * İşlem tanımlayıcısı onaylama

    * IDoc 'Ları gönderin, kendi numarasından bir IDoc 'ın durumunu alın ve bir işlem için IDoc 'Lar listesini alın

    * SAP tablosu okuma

* **SAP tetikleyicisinden bir ileti alındığında** şunları yapabilirsiniz:

    * TRFC üzerinde IDoc alma

    * TRFC üzerinde BAPı işlevlerini çağırma

    * SAP sistemlerinde RFC/tRFC çağırma

* IDoc, BAPı veya RFC için SAP yapıtları için şemalar oluşturmak üzere kullanabileceğiniz **şemalar oluşturma** eylemi.

Bu SAP eylemlerini kullanmak için önce bir Kullanıcı adı ve parolayla bağlantınızın kimlik doğrulamasını yapmanız gerekir. SAP Bağlayıcısı Ayrıca [güvenli ağ iletişimini (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)destekler. SAP NetWeaver çoklu oturum açma (SSO) için SNC veya dış ürünlerden ek güvenlik özellikleri için kullanabilirsiniz. SNC kullanıyorsanız, bkz. [SNC önkoşulları](#snc-prerequisites).

### <a name="migrate-to-current-connector"></a>Geçerli bağlayıcıya geçiş

Önceki SAP uygulama sunucusu ve SAP Ileti sunucusu bağlayıcıları, 29 Şubat 2020 ' de kullanımdan kaldırılmıştır. Geçerli SAP bağlayıcısına geçiş yapmak için şu adımları izleyin:

1. Şirket [içi veri ağ geçidinizi](https://www.microsoft.com/download/details.aspx?id=53127) güncel sürüme güncelleştirin. Daha fazla bilgi için bkz. [Azure Logic Apps için şirket içi veri ağ geçidi yüklemesi](../logic-apps/logic-apps-gateway-install.md).

1. Kullanım dışı olan SAP bağlayıcısını kullanan mantıksal uygulamanızda **SAP gönder** eylemini silin.

1. Geçerli SAP bağlayıcısından **SAP 'a Ileti gönder** eylemini ekleyin.

1. Yeni eylemde SAP sisteminize yeniden bağlanın.

1. Mantıksal uygulamanızı kaydedin.

### <a name="multi-tenant-azure-prerequisites"></a>Çok kiracılı Azure önkoşulları

Mantıksal uygulamanız çok kiracılı Azure 'da çalışıyorsa bu önkoşullar geçerlidir. Yönetilen SAP Bağlayıcısı bir [Ise](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)'de yerel olarak çalışmaz.

> [!TIP]
> Premium düzeyinde bir ıSE kullanıyorsanız, yönetilen SAP Bağlayıcısı yerine SAP ıSE bağlayıcısını kullanabilirsiniz. Daha fazla bilgi için bkz. [Ise önkoşulları](#ise-prerequisites).

Yönetilen SAP Bağlayıcısı, Şirket [içi veri ağ geçidiniz](../logic-apps/logic-apps-gateway-connection.md)aracılığıyla SAP sistemleriyle tümleşir. Örneğin, ileti gönder senaryolarında bir mantıksal uygulamadan SAP sistemine bir ileti gönderildiğinde, veri ağ geçidi bir RFC istemcisi işlevi görür ve mantıksal uygulamadan alınan istekleri SAP 'ye iletir. Benzer şekilde, ileti alma senaryolarında, veri ağ geçidi SAP 'den gelen istekleri alan ve mantıksal uygulamaya ileten bir RFC sunucusu işlevi görür.

* Şirket [içi veri ağ geçidini](../logic-apps/logic-apps-gateway-install.md) , BAĞLANMAKTA olduğunuz SAP sistemiyle aynı sanal ağda bulunan bir konak bilgisayara veya sanal makineye indirin ve yükleyin.

* Azure portal şirket içi veri ağ geçidiniz için [bir Azure ağ geçidi kaynağı oluşturun](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) . Bu ağ geçidi, şirket içi verilere ve kaynaklara güvenli bir şekilde erişmenize yardımcı olur. Ağ geçidinin desteklenen bir sürümünü kullandığınızdan emin olun.

    * Ağ geçidinizle ilgili bir sorun yaşıyorsanız, sorunu çözmek için güncelleştirmeleri içerebilen [en son sürüme yükseltmeyi](https://aka.ms/on-premises-data-gateway-installer)deneyin.

* [En son SAP istemci kitaplığını](#sap-client-library-prerequisites) şirket içi veri ağ geçidiniz ile aynı yerel bilgisayara indirip yükleyin.

### <a name="ise-prerequisites"></a>ISE önkoşulları

Mantıksal uygulamanızı Premium düzeyinde bir ıSE çalıştırıyorsanız bu önkoşullar geçerlidir. Ancak, geliştirici düzeyinde çalışan Logic Apps için uygulanmazlar. ISE, bir Azure sanal ağı tarafından korunan kaynaklara erişim sağlar ve Logic Apps 'in şirket içi veri ağ geçidini kullanmadan şirket içi kaynaklara doğrudan erişmesini sağlayan diğer ıSE-yerel bağlayıcılar sunar.

1. Blob kapsayıcısı olan bir Azure depolama hesabınız yoksa [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) veya [Azure Depolama Gezgini](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)kullanarak bir kapsayıcı oluşturun.

1. [En son SAP istemci kitaplığını yerel bilgisayarınıza indirip yükleyin](#sap-client-library-prerequisites) . Aşağıdaki derleme dosyalarına sahip olmanız gerekir:

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. Bu derleme dosyalarını içeren bir. zip dosyası oluşturun. Paketi Azure Storage 'daki blob kapsayıcınıza yükleyin.

1. Azure portal ya da Azure Depolama Gezgini,. zip dosyasını karşıya yüklediğiniz kapsayıcı konumuna gidin.

1. Kapsayıcı konumunun URL 'sini kopyalayın. SAS belirtecinin yetkilendirildiği, paylaşılan erişim Imzası (SAS) belirtecini eklediğinizden emin olun. Aksi takdirde, SAP ıSE Bağlayıcısı için dağıtım başarısız olur.

1. SAP bağlayıcısını, ıSE 'ye yükleyip dağıtın. Daha fazla bilgi için bkz. [Ise bağlayıcıları ekleme](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

   1. [Azure Portal](https://portal.azure.com), Ise 'nizi bulun ve açın.

   1. Ise menüsünde, **yönetilen bağlayıcılar** &gt; **Ekle**' yi seçin. Bağlayıcılar listesinden **SAP** bulun ve seçin.

   1. **Yeni bir yönetilen bağlayıcı Ekle** bölmesinde, **SAP paketi** kutusuna SAP derlemeleri olan. zip dosyasının URL 'sini yapıştırın. Daha sonra, SAS belirtecini eklediğinizden emin olun.
 
  1. ISE bağlayıcınızı oluşturmayı bitirmeden **Oluştur** ' u seçin.

1. SAP örneğiniz ve ıSE farklı sanal ağlardayken, [bu ağları](../virtual-network/tutorial-connect-virtual-networks-portal.md) bağlı olmaları için de eşdüzey yapmanız gerekir.

### <a name="sap-client-library-prerequisites"></a>SAP istemci kitaplığı önkoşulları

Bunlar, bağlayıcıyla kullanmakta olduğunuz SAP istemci kitaplığı için önkoşullardır.

* [.NET Framework 4,0-Windows 64-bit (x64) ile derlenen Microsoft .NET 3.0.22.0 Için SAP Bağlayıcısı (NCo 3,0)](https://support.sap.com/en/product/connectors/msnet.html)en son sürümünü yüklediğinizden emin olun. SAP NCo 'ın önceki sürümlerinde aynı anda birden fazla IDoc iletisi gönderildiğinde sorunlarla karşılaşabilirsiniz. Bu koşul, SAP hedefine gönderilen ve iletilerin zaman aşımına neden olan tüm sonraki iletileri engeller.

* Veri ağ geçidi yalnızca 64 bit sistemlerde çalıştığı için, SAP istemci kitaplığının 64 bitlik sürümünün yüklü olması gerekir. Desteklenmeyen 32 bit sürümü yüklemesi, "kötü görüntü" hatası ile sonuçlanır.

* Varsayılan yükleme klasöründeki derleme dosyalarını, senaryonuza göre aşağıdaki gibi başka bir konuma kopyalayın.

    * ISE 'de çalışan Logic Apps için, bunun yerine [Ise önkoşullarını](#ise-prerequisites) izleyin.

    * Çok kiracılı Azure 'da çalışan ve şirket içi veri ağ geçidinizi kullanan Logic Apps için, derleme dosyalarını veri ağ geçidi yükleme klasörüne kopyalayın. 

        
        * SAP bağlantınız hata iletisiyle başarısız olursa, **lütfen hesap bilgilerinizi ve/veya izinlerinizi denetleyin ve yeniden deneyin**, derleme dosyalarını veri ağ geçidi yükleme klasörüne kopyaladığınızdan emin olun.
        
        * [.NET bütünleştirilmiş kod bağlama günlük görüntüleyicisini](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)kullanarak daha fazla sorun giderme. Bu araç, derleme dosyalarınızın doğru konumda olup olmadığını kontrol etmenizi sağlar. 
        
        * İsteğe bağlı olarak, SAP istemci kitaplığını yüklerken **genel derleme önbelleği kaydı** seçeneğini belirleyin.

SAP istemci kitaplığı, .NET Framework, .NET çalışma zamanı ve ağ geçidi arasındaki aşağıdaki ilişkileri aklınızda edin:

* Hem Microsoft SAP bağdaştırıcısı hem de ağ geçidi ana bilgisayar hizmeti .NET Framework 4.7.2 kullanır.

* .NET Framework 4,0 için SAP NCo, .NET Runtime 4,8 4,0 ' i kullanan işlemlerle çalışmaktadır.

* .NET Framework 2,0 için SAP NCo, .NET Runtime 3,5 2,0 ' i kullanan işlemlerle ve en son ağ geçidiyle artık çalışmakla birlikte çalışmaktadır.

### <a name="snc-prerequisites"></a>SNC önkoşulları

İsteğe bağlı SNC ile şirket içi veri ağ geçidi kullanırsanız, yalnızca çok kiracılı Azure 'da desteklenir, bu ek ayarları yapılandırmanız gerekir.

SNC 'yi SSO ile kullanıyorsanız, veri ağ geçidi hizmetinin SAP kullanıcısına karşı eşlenmiş bir kullanıcı olarak çalıştığından emin olun. Varsayılan hesabı değiştirmek için **hesabı Değiştir**' i seçin ve Kullanıcı kimlik bilgilerini girin.

![Azure portalındaki şirket içi veri ağ geçidi ayarlarının ekran görüntüsü, seçilen ağ geçidi hizmet hesabını değiştirmek için düğme ile hizmet ayarları sayfası gösteriliyor.](./media/logic-apps-using-sap-connector/gateway-account.png)

Bir dış güvenlik ürünü aracılığıyla SNC 'yi etkinleştirirseniz, SNC kitaplığını veya dosyalarını, veri ağ geçidinizin yüklü olduğu aynı bilgisayara kopyalayın. Bazı SNC ürünlerine örnek olarak [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos ve NTLM verilebilir. Veri ağ geçidi için SNC 'yi etkinleştirme hakkında daha fazla bilgi için bkz. [güvenli ağ Iletişimini etkinleştirme](#enable-secure-network-communications).

## <a name="send-idoc-messages-to-sap-server"></a>SAP sunucusuna IDOC iletileri gönderme

SAP sunucusuna bir IDoc iletisi gönderen ve yanıt döndüren bir mantıksal uygulama oluşturmak için bu örnekleri izleyin:

1. [Bir HTTP isteği tarafından tetiklenen bir mantıksal uygulama oluşturun.](#create-http-request-trigger)

1. [SAP 'ye bir ileti göndermek için iş akışınızda bir eylem oluşturun.](#create-sap-action-to-send-message)

1. [İş akışınızda bir HTTP yanıt eylemi oluşturun.](#create-http-response-action)

1. [SAP ABAP 'ten yanıt almak için bir RFC kullanıyorsanız, bir uzak işlev çağrısı (RFC) istek-yanıt stili oluşturun.](#create-rfc-request-response)

1. [Mantıksal uygulamanızı test edin.](#test-logic-app)

### <a name="create-http-request-trigger"></a>HTTP istek tetikleyicisi oluştur

> [!NOTE]
> Bir mantıksal uygulama SAP 'den IDoc aldığında, [istek tetikleyicisi](../connectors/connectors-native-reqres.md) artık SAP düz XML biçimini desteklemektedir. IDoc 'Ları düz XML olarak almak için **SAP 'den bir ileti alındığında** tetikleyiciyi kullanın. **IDoc biçimindeki** parametreyi **sapplainxml** olarak ayarlayın.

İlk olarak, mantıksal uygulamanıza *http post* istekleri göndermek için Azure 'da bir uç nokta ile bir mantıksal uygulama oluşturun. Mantıksal uygulamanız bu HTTP isteklerini aldığında, [tetikleyici](../logic-apps/logic-apps-overview.md#logic-app-concepts) ateşlenir ve iş akışınızda bir sonraki adımı çalıştırır.

1. [Azure Portal](https://portal.azure.com), **Logic Apps tasarımcısını** açan boş bir mantıksal uygulama oluşturun.

1. Arama kutusuna `http request` filtreniz olarak yazın. **Tetikleyiciler** LISTESINDEN **bir http isteği alındığında** öğesini seçin.

   ![Mantıksal uygulamaya eklenen yeni bir HTTP isteği tetikleyicisi gösteren Logic Apps Designer 'ın ekran görüntüsü.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Mantıksal uygulamanız için bir uç nokta URL 'SI oluşturabilmeniz için mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin. Uç nokta URL 'SI şimdi Tetikleyiciniz içinde görünür. 

   ![Oluşturulmuş GÖNDERI URL 'SI ile HTTP istek tetikleyicisini gösteren Logic Apps Designer 'ın ekran görüntüsü.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>İleti göndermek için SAP eylemi oluştur

Ardından, [http isteğiniz tetiklemeniz](#create-http-request-trigger) tetiklendiğinde ıDOC iletinizi SAP 'ye göndermek için bir eylem oluşturun.

1. Logic Apps tasarımcısında tetikleyici altında **yeni adım**' ı seçin.

   ![Yeni bir adım eklemek için düzenlenmekte olan mantıksal uygulamayı gösteren Logic Apps Designer 'ın ekran görüntüsü.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Arama kutusuna `sap` filtreniz olarak yazın. **Eylemler** LISTESINDEN, **SAP 'ye ileti gönder**' i seçin.
  
   !["SAP 'ye ileti gönder" eylemini gösteren Logic Apps Designer 'ın ekran görüntüsü.](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Ya da **Kurumsal** SEKMESINI seçip SAP eylemini seçebilirsiniz.

   ![Kurumsal sekme altında "SAP 'a ileti gönder" seçeneğinin gösterildiği Logic Apps Designer 'ın ekran görüntüsü.](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Bağlantınız zaten varsa, bir sonraki adımla devam edin. Yeni bir bağlantı oluşturmanız istenirse, şirket içi SAP sunucunuza bağlanmak için aşağıdaki bilgileri sağlayın.

   1. Bağlantı için bir ad girin.

   1. Veri ağ geçidini kullanıyorsanız, şu adımları izleyin:
   
      1. **Veri ağ geçidi** bölümünde, **abonelik** altında, önce veri ağ geçidi yüklemeniz için Azure Portal oluşturduğunuz veri ağ geçidi kaynağı için Azure aboneliğini seçin.
   
      1. **Bağlantı ağ geçidi** altında Azure 'da veri ağ geçidi kaynağınızı seçin.

   1. Bağlantı bilgilerini sağlamaya devam edin. **Oturum açma türü** özelliği için, özelliğin **uygulama sunucusu** veya **Grup** olarak ayarlanmış olup olmadığına bağlı olarak adımı izleyin:
   
      * **Uygulama sunucusu** için genellikle isteğe bağlı olarak görüntülenen bu özellikler gereklidir:

        ![SAP uygulama sunucusu bağlantısı oluşturma](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **Grup** için, genellikle isteğe bağlı olarak görüntülenen bu özellikler gereklidir:

        ![SAP ileti sunucusu bağlantısı oluştur](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Varsayılan olarak, şemaya karşı XML doğrulaması gerçekleştirerek geçersiz değerleri denetlemek için tanımlayıcı yazma kullanılır. Bu davranış, daha önce sorunları tespit etmenize yardımcı olabilir. **Güvenli yazma** seçeneği geriye dönük uyumluluk için kullanılabilir ve yalnızca dize uzunluğunu denetler. [Güvenli yazma seçeneği](#safe-typing)hakkında daha fazla bilgi edinin.

   1. İşiniz bittiğinde **Oluştur**' u seçin.

      Logic Apps, bağlantının düzgün çalıştığından emin olmak için bağlantınızı kurar ve test eder.

    > [!NOTE]

    > Aşağıdaki hatayı alırsanız SAP NCo istemci kitaplığı yüklemenizin bir sorunu vardır: 
    >
    > **Sınama bağlantısı başarısız oldu. Hata ' istek işlenemedi. Hata ayrıntıları: ' dosya veya derleme ' sapnco, Version = 3.0.0.42, Culture = neutral, PublicKeyToken 50436dca5c7f7d23 ' veya bağımlılıklarından biri yüklenemedi. Sistem belirtilen dosyayı bulamıyor. '. '**
    >
    > [SAP NCo istemci kitaplığının gerekli sürümünü yüklediğinizden ve diğer tüm önkoşulları karşıladığınızdan](#sap-client-library-prerequisites)emin olun.

1. Şimdi SAP sunucunuzdaki bir eylemi bulun ve seçin.

   1. **SAP eylemi** kutusunda klasör simgesini seçin. Dosya listesinden, kullanmak istediğiniz SAP iletisini bulun ve seçin. Listede gezinmek için okları kullanın.

      Bu örnek, **Orders** türünde bir IDoc seçer.

      ![IDoc eylemini bul ve Seç](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      İstediğiniz eylemi bulamazsanız, el ile bir yol girebilirsiniz, örneğin:

      ![IDoc eyleminin yolunu el ile sağlayın](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > , İfade Düzenleyicisi aracılığıyla **SAP eylemi** değerini sağlayın. Bu şekilde, farklı ileti türleri için aynı eylemi kullanabilirsiniz.

      IDOC işlemleri hakkında daha fazla bilgi için bkz. [IDOC işlemleri Için ileti şemaları](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Dinamik içerik listesinin görünmesi için **giriş iletisi** kutusunun içine tıklayın. Bu listeden, **BIR http isteği alındığında** altında **gövde** alanını seçin.

      Bu adım, HTTP Istek tetikleyicinizden gelen gövde içeriğini içerir ve bu çıktıyı SAP sunucunuza gönderir.

      ![Tetikleyiciden "gövde" özelliğini seçin](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      İşiniz bittiğinde, SAP eyleminiz Şu örneğe benzer şekilde görünür:

      ![SAP eylemini tamamlamayı](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

#### <a name="send-flat-file-idocs"></a>Düz dosya IDoc 'Ları gönder

Dosyaları bir XML zarfına sarmanız durumunda, bir düz dosya şeması ile IDoc 'Lar kullanabilirsiniz. Düz bir dosya IDoc 'u göndermek için, aşağıdaki değişikliklerle [IDoc iletinizi göndermek üzere BIR SAP eylemi oluşturmak](#create-sap-action-to-send-message) üzere genel yönergeleri kullanın.

1. **SAP 'a Ileti gönder** EYLEMI için SAP eylem URI 'sini kullanın `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` .

1. Giriş iletinizi bir XML zarfı ile biçimlendirin. Örnek için aşağıdaki örnek XML yüküne bakın:

```xml
<ReceiveIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/"><idocData>EDI_DC 3000000001017945375750 30INVOIC011BTSVLINV30KUABCABCFPPC LDCA X004010810 4 SAPMSX LSEDI ABCABCFPPC 000d3ae4-723e-1edb-9ca4-cc017365c9fd 20210217054521INVOICINVOIC01ZINVOIC2RE 20210217054520
E2EDK010013000000001017945375000001E2EDK01001000000010 ABCABC1.00000 0060 INVO9988298128 298.000 298.000 LB Z4LR EN 0005065828 L
E2EDKA1 3000000001017945375000002E2EDKA1 000000020 RS ABCABCFPPC 0005065828 ABCABCABC ABCABC Inc. Limited Risk Distributor ABCABC 1950 ABCABCABCA Blvd ABCABAABCAB L5N8L9 CA ABCABC E ON V-ABCABC LDCA
E2EDKA1 3000000001017945375000003E2EDKA1 000000020 AG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000004E2EDKA1 000000020 RE 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000005E2EDKA1 000000020 RG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000006E2EDKA1 000000020 WE 0005001847 41 ABCABC ABCABC INC (ABCABC) DC A. ABCABCAB 88 ABCABC CRESCENT ABCABAABCAB L5R 4A2 CA ABCABC 111-111-1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000007E2EDKA1 000000020 Z3 0005533050 ABCABCABC ABCABC Inc. ABCA Bank Swift Code -ABCABCABCAB Sort Code - 1950 ABCABCABCA Blvd. Acc No -1111111111 ABCABAABCAB L5N8L9 CA ABCABC E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000008E2EDKA1 000000020 BK 1075 ABCABCABC ABCABC Inc 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDKA1 3000000001017945375000009E2EDKA1 000000020 CR 1075 CONTACT ABCABCABC 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDK02 3000000001017945375000010E2EDK02 000000020 0099988298128 20210217
E2EDK02 3000000001017945375000011E2EDK02 000000020 00140-N6260-S 20210205
E2EDK02 3000000001017945375000012E2EDK02 000000020 0026336270425 20210217
E2EDK02 3000000001017945375000013E2EDK02 000000020 0128026580537 20210224
E2EDK02 3000000001017945375000014E2EDK02 000000020 01740-N6260-S
E2EDK02 3000000001017945375000015E2EDK02 000000020 900IAC
E2EDK02 3000000001017945375000016E2EDK02 000000020 901ZSH
E2EDK02 3000000001017945375000017E2EDK02 000000020 9078026580537 20210217
E2EDK03 3000000001017945375000018E2EDK03 000000020 02620210217
E2EDK03 3000000001017945375000019E2EDK03 000000020 00120210224
E2EDK03 3000000001017945375000020E2EDK03 000000020 02220210205
E2EDK03 3000000001017945375000021E2EDK03 000000020 01220210217
E2EDK03 3000000001017945375000022E2EDK03 000000020 01120210217
E2EDK03 3000000001017945375000023E2EDK03 000000020 02420210217
E2EDK03 3000000001017945375000024E2EDK03 000000020 02820210418
E2EDK03 3000000001017945375000025E2EDK03 000000020 04820210217
E2EDK17 3000000001017945375000026E2EDK17 000000020 001DDPDelivered Duty Paid
E2EDK17 3000000001017945375000027E2EDK17 000000020 002DDPdestination
E2EDK18 3000000001017945375000028E2EDK18 000000020 00160 0 Up to 04/18/2021 without deduction
E2EDK28 3000000001017945375000029E2EDK28 000000020 CA BOFACATT Bank of ABCABAB ABCABC ABCABAB 50127217 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000030E2EDK28 000000020 CA 026000082 ABCAbank ABCABC ABCABAB 201456700OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000031E2EDK28 000000020 GB ABCAGB2L ABCAbank N.A ABCABA E14, 5LB GB63ABCA18500803115593 ABCABCABC ABCABC Inc. GB63ABCA18500803115593
E2EDK28 3000000001017945375000032E2EDK28 000000020 CA 020012328 ABCABANK ABCABC ABCABAB ON M5J 2M3 2014567007 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000033E2EDK28 000000020 CA 03722010 ABCABABC ABCABABC Bank of Commerce ABCABAABCAB 64-04812 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000034E2EDK28 000000020 IE IHCC In-House Cash Center IHCC1075 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000035E2EDK28 000000020 CA 000300002 ABCAB Bank of ABCABC ABCABAB 0021520584OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000036E2EDK28 000000020 US USCC US Cash Center (IHC) city USCC1075 ABCABCABC ABCABC Inc.
E2EDK29 3000000001017945375000037E2EDK29 000000020 0064848944US A CAD CA ABCABC CA United States US CA A Air Air
E2EDKT1 3000000001017945375000038E2EDKT1 000000020 ZJ32E EN
E2EDKT2 3000000001017945375000039E2EDKT2 000038030 GST/HST877845941RT0001 *
E2EDKT2 3000000001017945375000040E2EDKT2 000038030 QST1021036966TQ0001 *
E2EDKT1 3000000001017945375000041E2EDKT1 000000020 Z4VL
E2EDKT2 3000000001017945375000042E2EDKT2 000041030 0.000 *
E2EDKT1 3000000001017945375000043E2EDKT1 000000020 Z4VH
E2EDKT2 3000000001017945375000044E2EDKT2 000043030 *
E2EDK14 3000000001017945375000045E2EDK14 000000020 008LDCA
E2EDK14 3000000001017945375000046E2EDK14 000000020 00710
E2EDK14 3000000001017945375000047E2EDK14 000000020 00610
E2EDK14 3000000001017945375000048E2EDK14 000000020 015Z4F2
E2EDK14 3000000001017945375000049E2EDK14 000000020 0031075
E2EDK14 3000000001017945375000050E2EDK14 000000020 021M
E2EDK14 3000000001017945375000051E2EDK14 000000020 0161075
E2EDK14 3000000001017945375000052E2EDK14 000000020 962M
E2EDP010013000000001017945375000053E2EDP01001000000020 000011 2980.000 EA 298.000 LB MOUSE 298.000 Z4TN 4260
E2EDP02 3000000001017945375000054E2EDP02 000053030 00140-N6260-S 00000120210205 DFUE
E2EDP02 3000000001017945375000055E2EDP02 000053030 0026336270425 00001120210217
E2EDP02 3000000001017945375000056E2EDP02 000053030 0168026580537 00001020210224
E2EDP02 3000000001017945375000057E2EDP02 000053030 9100000 00000120210205 DFUE
E2EDP02 3000000001017945375000058E2EDP02 000053030 911A 00000120210205 DFUE
E2EDP02 3000000001017945375000059E2EDP02 000053030 912PP 00000120210205 DFUE
E2EDP02 3000000001017945375000060E2EDP02 000053030 91300 00000120210205 DFUE
E2EDP02 3000000001017945375000061E2EDP02 000053030 914CONTACT ABCABCABC 00000120210205 DFUE
E2EDP02 3000000001017945375000062E2EDP02 000053030 963 00000120210205 DFUE
E2EDP02 3000000001017945375000063E2EDP02 000053030 965 00000120210205 DFUE
E2EDP02 3000000001017945375000064E2EDP02 000053030 9666336270425 00000120210205 DFUE
E2EDP02 3000000001017945375000065E2EDP02 000053030 9078026580537 00001020210205 DFUE
E2EDP03 3000000001017945375000066E2EDP03 000053030 02920210217
E2EDP03 3000000001017945375000067E2EDP03 000053030 00120210224
E2EDP03 3000000001017945375000068E2EDP03 000053030 01120210217
E2EDP03 3000000001017945375000069E2EDP03 000053030 02520210217
E2EDP03 3000000001017945375000070E2EDP03 000053030 02720210217
E2EDP03 3000000001017945375000071E2EDP03 000053030 02320210217
E2EDP03 3000000001017945375000072E2EDP03 000053030 02220210205
E2EDP19 3000000001017945375000073E2EDP19 000053030 001418VVZ
E2EDP19 3000000001017945375000074E2EDP19 000053030 002RJR-00001 AB ABCABCABC Mouse FORBUS BLUETOOTH
E2EDP19 3000000001017945375000075E2EDP19 000053030 0078471609000
E2EDP19 3000000001017945375000076E2EDP19 000053030 003889842532685
E2EDP19 3000000001017945375000077E2EDP19 000053030 011CN
E2EDP26 3000000001017945375000078E2EDP26 000053030 00459064.20
E2EDP26 3000000001017945375000079E2EDP26 000053030 00352269.20
E2EDP26 3000000001017945375000080E2EDP26 000053030 01052269.20
E2EDP26 3000000001017945375000081E2EDP26 000053030 01152269.20
E2EDP26 3000000001017945375000082E2EDP26 000053030 0126795.00
E2EDP26 3000000001017945375000083E2EDP26 000053030 01552269.20
E2EDP26 3000000001017945375000084E2EDP26 000053030 00117.54
E2EDP26 3000000001017945375000085E2EDP26 000053030 00252269.20
E2EDP26 3000000001017945375000086E2EDP26 000053030 940 2980.000
E2EDP26 3000000001017945375000087E2EDP26 000053030 939 2980.000
E2EDP05 3000000001017945375000088E2EDP05 000053030 + Z400MS List Price 52269.20 17.54 1 EA CAD 2980
E2EDP05 3000000001017945375000089E2EDP05 000053030 + XR1 Tax Jur Code Level 6795.00 13.000 52269.20
E2EDP05 3000000001017945375000090E2EDP05 000053030 + Tax Subtotal1 6795.00 2.28 1 EA CAD 2980
E2EDP05 3000000001017945375000091E2EDP05 000053030 + Taxable Amount + TaxSubtotal1 59064.20 19.82 1 EA CAD 2980
E2EDP04 3000000001017945375000092E2EDP04 000053030 CX 13.000 6795.00 7000000000
E2EDP04 3000000001017945375000093E2EDP04 000053030 CX 0 0 7001500000
E2EDP04 3000000001017945375000094E2EDP04 000053030 CX 0 0 7001505690
E2EDP28 3000000001017945375000095E2EDP28 000053030 00648489440000108471609000 CN CN ABCAB ZZ 298.000 298.000 LB US 400 United Stat KY
E2EDPT1 3000000001017945375000096E2EDPT1 000053030 0001E EN
E2EDPT2 3000000001017945375000097E2EDPT2 000096040 AB ABCABCABC Mouse forBus Bluetooth EN/XC/XD/XX Hdwr Black For Bsnss *
E2EDS01 3000000001017945375000098E2EDS01 000000020 0011
E2EDS01 3000000001017945375000099E2EDS01 000000020 01259064.20 CAD
E2EDS01 3000000001017945375000100E2EDS01 000000020 0056795.00 CAD
E2EDS01 3000000001017945375000101E2EDS01 000000020 01159064.20 CAD
E2EDS01 3000000001017945375000102E2EDS01 000000020 01052269.20 CAD
E2EDS01 3000000001017945375000103E2EDS01 000000020 94200000 CAD
E2EDS01 3000000001017945375000104E2EDS01 000000020 9440.00 CAD
E2EDS01 3000000001017945375000105E2EDS01 000000020 9450.00 CAD
E2EDS01 3000000001017945375000106E2EDS01 000000020 94659064.20 CAD
E2EDS01 3000000001017945375000107E2EDS01 000000020 94752269.20 CAD
E2EDS01 3000000001017945375000108E2EDS01 000000020 EXT
Z2XSK010003000000001017945375000109Z2XSK01000000108030 Z400 52269.20
Z2XSK010003000000001017945375000110Z2XSK01000000108030 XR1 13.000 6795.00 CX
</idocData></ReceiveIdoc>
```

### <a name="create-http-response-action"></a>HTTP yanıtı oluşturma eylemi

Şimdi mantıksal uygulamanızın iş akışına bir yanıt eylemi ekleyin ve SAP eyleminden çıktıyı dahil edin. Bu şekilde, mantıksal uygulamanız SAP sunucunuzdaki sonuçları özgün istek sahibine döndürür.

1. Logic Apps tasarımcısında, SAP eylemi altında **yeni adım**' ı seçin.

1. Arama kutusuna `response` filtreniz olarak yazın. **Eylemler** listesinden **Yanıt**' ı seçin.

1. Dinamik içerik listesinin görünmesi için **gövde** kutusunun içine tıklayın. Bu listeden, **SAP 'ye Ileti gönder** altında **gövde** alanını seçin.

   ![Tamamlanmış SAP eylemi](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Mantıksal uygulamanızı kaydedin.

#### <a name="create-rfc-request-response"></a>RFC isteği oluşturma-yanıt

> [!NOTE]
> SAP tetikleyicisi, bir Response parametresi tasarlayarak, tRFC üzerinde IDoc 'Ları alır. 

SAP ABAP 'ten Logic Apps için uzak işlev çağrısı (RFC) kullanarak yanıt almanız gerekiyorsa bir istek ve yanıt stili oluşturmanız gerekir. Mantıksal uygulamanızda IDoc almak için ilk eylemi bir durum kodu ve içerik olmadan bir [http isteği](../connectors/connectors-native-reqres.md#add-a-response-action) yapmalısınız `200 OK` . Bu önerilen adım, SAP CPIC iletişimini yeniden kullanılabilir halde bırakan tRFC üzerinden SAP LUW zaman uyumsuz aktarımını tamamlar. Daha sonra, diğer aktarımları engellemeden alınan IDoc 'ı işlemek için mantıksal uygulamanıza daha fazla eylem ekleyebilirsiniz.

Bir istek ve yanıt düzeni uygulamak için, önce [ `generate schema` komutunu](#generate-schemas-for-artifacts-in-sap)kullanarak RFC şemasını bulmanız gerekir. Oluşturulan şemanın iki olası kök düğümü vardır: 

1. SAP 'den aldığınız çağrı olan istek düğümü.

1. SAP 'ye geri yanıt veren yanıt düğümü.

Aşağıdaki örnekte, RFC modülünden bir istek ve yanıt deseninin oluşturulması gerekir `STFC_CONNECTION` . İstek XML dosyası, SAP isteklerinin bulunduğu bir düğüm değerini ayıklamak için ayrıştırılır `<ECHOTEXT>` . Yanıt, geçerli zaman damgasını dinamik bir değer olarak ekler. `STFC_CONNECTION`Bir mantıksal uygulamadan SAP 'ye RFC gönderdiğinizde benzer bir yanıt alırsınız.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-logic-app"></a>Mantıksal uygulamayı test etme

1. Mantıksal uygulamanız zaten etkin değilse, mantıksal uygulama menüsünde **genel bakış**' ı seçin. Araç çubuğunda **Etkinleştir**' i seçin.

1. Tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Bu adım mantıksal uygulamanızı el ile başlatır.

1. HTTP Istek tetikleyicinizdeki URL 'ye bir HTTP POST isteği göndererek mantıksal uygulamanızı tetikleyin.
İsteğiniz ile ileti içeriğinizi ekleyin. İsteği göndermek için [Postman](https://www.getpostman.com/apps)gibi bir araç kullanabilirsiniz.

   Bu makalede, istek XML biçiminde olması gereken bir IDoc dosyası gönderir ve kullanmakta olduğunuz SAP eyleminin ad alanını içermelidir, örneğin:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. HTTP isteğinizi gönderdikten sonra, mantıksal uygulamanızdan gelen yanıtı bekleyin.

   > [!NOTE]
   > Yanıt için gereken tüm adımlar [istek zaman aşımı sınırı](./logic-apps-limits-and-config.md)içinde tamamlanmazsa mantıksal uygulamanız zaman aşımına uğrayabilir. Bu durum oluşursa, istekler engellenebilir. Sorunları tanılamanıza yardımcı olması için [Logic Apps 'i nasıl denetleyebilir ve izleyebileceğinizi](../logic-apps/monitor-logic-apps.md)öğrenin.

Artık, SAP sunucunuz ile iletişim kurabilen bir mantıksal uygulama oluşturdunuz. Mantıksal uygulamanız için bir SAP bağlantısı ayarladığınıza göre, BAPı ve RFC gibi diğer kullanılabilir SAP eylemlerini inceleyebilirsiniz.

## <a name="receive-message-from-sap"></a>SAP 'den ileti al

Bu örnek, uygulama SAP sisteminden bir ileti aldığında tetiklenen bir mantıksal uygulama kullanır.

### <a name="add-an-sap-trigger"></a>SAP tetikleyicisi ekleme

1. Azure portal, Logic Apps tasarımcısını açan boş bir mantıksal uygulama oluşturun.

1. Arama kutusuna `sap` filtreniz olarak yazın. **Tetikleyiciler** listesinden **SAP 'den bir ileti alındığında** öğesini seçin.

   ![SAP tetikleyicisi Ekle](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Ya da **Kurumsal** sekmesini seçip tetikleyiciyi seçebilirsiniz:

   ![Kurumsal sekmeden SAP tetikleyicisi ekleme](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Bağlantınız zaten varsa, SAP eyleminizi ayarlamak için bir sonraki adımla devam edin. Bununla birlikte, bağlantı ayrıntıları istenirse, şu anda şirket içi SAP sunucunuza bir bağlantı oluşturabilmek için bilgileri sağlayın.

   1. Bağlantı için bir ad girin.

   1. Veri ağ geçidini kullanıyorsanız, şu adımları izleyin:

      1. **Veri ağ geçidi** bölümünde, **abonelik** altında, önce veri ağ geçidi yüklemeniz için Azure Portal oluşturduğunuz veri ağ geçidi kaynağı için Azure aboneliğini seçin.

      1. **Bağlantı ağ geçidi** altında Azure 'da veri ağ geçidi kaynağınızı seçin.

   1. Bağlantıyla ilgili bilgi sağlamaya devam edin. **Oturum açma türü** özelliği için, özelliğin **uygulama sunucusu** veya **Grup** olarak ayarlanmış olup olmadığına bağlı olarak adımı izleyin:

      * **Uygulama sunucusu** için genellikle isteğe bağlı olarak görüntülenen bu özellikler gereklidir:

        ![SAP uygulama sunucusu bağlantısı oluşturma](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **Grup** için, genellikle isteğe bağlı olarak görüntülenen bu özellikler gereklidir:

        ![SAP ileti sunucusu bağlantısı oluştur](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Varsayılan olarak, şemaya karşı XML doğrulaması gerçekleştirerek geçersiz değerleri denetlemek için tanımlayıcı yazma kullanılır. Bu davranış, daha önce sorunları tespit etmenize yardımcı olabilir. **Güvenli yazma** seçeneği geriye dönük uyumluluk için kullanılabilir ve yalnızca dize uzunluğunu denetler. [Güvenli yazma seçeneği](#safe-typing)hakkında daha fazla bilgi edinin.

   1. İşiniz bittiğinde **Oluştur**' u seçin.

      Logic Apps, bağlantının düzgün çalıştığından emin olmak için bağlantınızı kurar ve test eder.

1. SAP sistem yapılandırmanıza göre [gerekli parametreleri](#parameters) sağlayın. 

   SAP [eylemlerinin bir listesini belirterek, SAP sunucunuzda aldığınız iletileri filtreleyebilirsiniz](#filter-with-sap-actions).

   Dosya seçicisinden bir SAP eylemi seçebilirsiniz:

   ![Logic App 'e SAP eylemi ekleme](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ya da bir eylemi el ile belirtebilirsiniz:

   ![Kullanmak istediğiniz SAP eylemini el ile girin](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Bu, tetikleyiciyi birden fazla ileti alacak şekilde ayarlarken eylemin nasıl göründüğünü gösteren bir örnektir.

   ![Birden çok ileti alan tetikleyici örneği](media/logic-apps-using-sap-connector/example-trigger.png)

   SAP eylemi hakkında daha fazla bilgi için bkz. [IDoc Için ileti şemaları işlemler](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. SAP sisteminizden ileti almaya başlayabilmeniz için artık mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Mantıksal uygulamanız artık SAP sisteminizden ileti almaya hazırdır.

> [!NOTE]
> SAP tetikleyicisi bir yoklama tetikleyicisi değildir, ancak bunun yerine Web kancası tabanlı bir tetikleyiciden yapılır. Veri ağ geçidini kullanıyorsanız, tetikleyici veri ağ geçidinden yalnızca bir ileti varken çağrılır, dolayısıyla hiçbir yoklama gerekmez.

**' Sapgw00 ' hizmetine** benzer bir ileti Ile **500 hatalı bir ağ geçidi** hatası alırsanız, API bağlantdaki ağ geçidi hizmet adınızı değiştirin ve yapılandırmayı bağlantı noktası numarasıyla tetikleyin. Aşağıdaki örnek hatada, `sapgw00` gerçek bir bağlantı noktası numarasıyla değiştirilmeleri gerekir, örneğin, `3300` . 

```json
{
    "body": {
        "error": {
            "code": 500,
            "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
            "clientRequestId": "00000000-0000-0000-0000-000000000000",
            "message": "BadGateway",
            "innerError": {
                "error": {
                    "code": "UnhandledException",
                    "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
                }
            }
        }
    }
}
```

#### <a name="parameters"></a>Parametreler

Basit dize ve sayı girişlerinin yanı sıra, SAP Bağlayıcısı aşağıdaki tablo parametrelerini ( `Type=ITAB` girişler) kabul eder:

* Eski SAP sürümleri için hem giriş hem de çıkış tablo yönü parametreleri.

* Daha yeni SAP sürümleri için tablo yönü parametrelerini değiştiren parametreleri değiştirme.

* Hiyerarşik tablo parametreleri

#### <a name="filter-with-sap-actions"></a>SAP eylemleri ile filtrele

İsteğe bağlı olarak, tek veya birden çok SAP eylemi içeren bir liste veya dizi sağlayarak mantıksal uygulamanızın SAP sunucusundan aldığı iletileri filtreleyebilirsiniz. Varsayılan olarak bu dizi boştur. Bu, mantıksal uygulamanızın filtre olmadan SAP sunucunuzdaki tüm iletileri alacağı anlamına gelir. 

Dizi filtresini ayarlarken, tetikleyici yalnızca belirtilen SAP Eylem türlerindeki iletileri alır ve SAP sunucunuzdaki diğer tüm iletileri reddeder. Ancak, bu filtre alınan yükün yazılıp zayıf veya güçlü olduğunu etkilemez.

Şirket içi veri ağ geçidiniz için SAP bağdaştırıcısı düzeyinde herhangi bir SAP eylemi filtrelemesi olur. Daha fazla bilgi için bkz. [SAP 'dan Logic Apps test IDoc 'ları gönderme](#test-sending-idocs-from-sap).

SAP 'den mantıksal uygulamanızın tetikleyicisine IDoc paketleri gönderememek için SAP tRFC iletişim kutusunda (T-Code SM58) Işlem RFC (tRFC) çağrı reddetme iletisine bakın. SAP arabiriminde, **durum metni** alanındaki alt dize sınırları nedeniyle kırpıldığı aşağıdaki hata iletilerini alabilirsiniz.

##### <a name="the-requestcontext-on-the-ireplychannel-was-closed-without-a-reply-being-sent"></a>IReplyChannel destekleyen desteklenecektir üzerindeki RequestContext, bir yanıt gönderilmeden kapatıldı

Bu hata iletisi, kanal için catch-all işleyicisi bir hata nedeniyle kanalı sonlandırdığında beklenmeyen hatalar meydana gelir ve diğer iletileri işlemek için kanalı yeniden oluşturur.

Mantıksal uygulamanızın IDoc 'u aldığını onaylamak için durum kodu döndüren [bir yanıt eylemi ekleyin](../connectors/connectors-native-reqres.md#add-a-response-action) `200 OK` . Gövdeyi boş bırakın ve üst bilgilere eklemeyin veya eklemeyin. IDoc, yanıt yüküne izin verilmeyen tRFC aracılığıyla taşınır.

Bunun yerine IDoc 'u reddetmek için dışında herhangi bir HTTP durum koduyla yanıtlayın `200 OK` . Daha sonra SAP bağdaştırıcısı, sizin adınıza SAP 'ye bir özel durum geri döndürür. Yalnızca, uygulamanızın işleyemeyecek hatalı yönlendirilmiş bir IDoc gibi, taşıma hatalarını SAP 'ye geri bildirmek için IDoc 'u reddetmelisiniz. IDoc 'ta bulunan verilerle ilgili sorunlar gibi uygulama düzeyindeki hatalar için bir IDoc 'u reddetmemeniz gerekir. Uygulama düzeyinde doğrulama için taşıma kabulünü ertelerseniz, diğer IDoc 'Ları taşımadan bağlantınızın engellenmesi nedeniyle olumsuz performansla karşılaşabilirsiniz.

Bu hata iletisini alıyorsanız ve Logic Apps arayan systemik hatalarıyla karşılaşırsanız, şirket içi veri ağ geçidi hizmetiniz için ağ ayarlarını belirli ortamınız için yapılandırdığınızdan emin olun. Örneğin, ağ ortamınız Azure uç noktaları çağırmak için bir proxy kullanılmasını gerektiriyorsa, şirket içi veri ağ geçidi hizmetinizi ara sunucuyu kullanacak şekilde yapılandırmanız gerekir. Daha fazla bilgi için bkz. [proxy yapılandırması](/dotnet/framework/network-programming/proxy-configuration).

Bu hata iletisini alıyorsanız ve Logic Apps çağrılırken aralıklı hatalarla karşılaşırsanız, yeniden deneme sayısını ve/veya yeniden deneme aralığı ' nı artırmanız gerekebilir. 

1. Şirket içi veri ağ geçidi hizmeti yapılandırma dosyanızdaki SAP ayarlarını kontrol edin `Microsoft.PowerBI.EnterpriseGateway.exe.config` . Yeniden deneme sayısı ayarı gibi görünür `WebhookRetryMaximumCount="2"` . Yeniden deneme aralığı ayarı şöyle görünür `WebhookRetryDefaultDelay="00:00:00.10"` ve zaman aralığı biçimidir `HH:mm:ss.ff` . 
    
1. Değişikliklerinizi kaydedin ve şirket içi veri ağ geçidinizi yeniden başlatın.

##### <a name="the-segment-or-group-definition-e2edk36001-was-not-found-in-the-idoc-meta"></a>E2EDK36001 segmenti veya grup tanımı, IDoc meta içinde bulunamadı

Bu hata iletisi, diğer hatalarda beklenen hataların gerçekleştiği anlamına gelir. Örneğin, kesimleri SAP tarafından yayımlanmadığı için bir IDoc XML yükü oluşturma hatası. Sonuç olarak, dönüştürme için gerekli olan segment türü meta verileri eksik.

Bu segmentlerin SAP tarafından serbest bırakılması için SAP sisteminizin ABAP mühendisine başvurun.
### <a name="asynchronous-request-reply-for-triggers"></a>Zaman uyumsuz istek-Tetikleyiciler için yanıtlama

SAP Bağlayıcısı Logic Apps Tetikleyicileri için Azure 'un [zaman uyumsuz istek yanıtı modelini](/azure/architecture/patterns/async-request-reply) destekler. Bu kalıbı, varsayılan zaman uyumlu istek-yanıt düzeniyle başarısız olacak başarılı istekler oluşturmak için kullanabilirsiniz. 

> [!TIP]
> Birden çok yanıt eylemi olan Logic Apps 'te, tüm yanıt eylemleri aynı istek-yanıt modelini kullanmalıdır. Örneğin, mantıksal uygulamanız birden çok olası yanıt eylemi ile bir anahtar denetimi kullanıyorsa, tüm yanıt eylemlerini, zaman uyumlu veya zaman uyumsuz olarak aynı istek yanıt modelini kullanacak şekilde yapılandırmanız gerekir. 

Yanıt eyleminiz için zaman uyumsuz yanıtı etkinleştirmek `202 Accepted` , bir istek işlenmek üzere kabul edildiğinde mantıksal uygulamanızın Yanıtla yanıt vermesini sağlar. Yanıt, isteğinizin son durumunu almak için kullanabileceğiniz bir konum üst bilgisi içerir.

SAP bağlayıcısını kullanarak mantıksal uygulamanız için zaman uyumsuz bir istek-yanıt modelini yapılandırmak için:

1. **Logic Apps tasarımcısında** mantıksal uygulamanızı açın.

1. SAP bağlayıcısının mantıksal uygulamanızın tetikleyicisi olduğunu doğrulayın.

1. Mantıksal uygulamanızın **Yanıt** eylemini açın. Eylemin başlık çubuğunda, menüyü (**...**) &gt; seçin **Ayarlar**.

1. Yanıt eyleminiz için **Ayarlar** ' da, **zaman uyumsuz yanıt**' ın altındaki geçişi açın. Bitti ' yi seçin.

1. Mantıksal uygulamanıza değişiklikleri kaydedin.

## <a name="find-extended-error-logs"></a>Genişletilmiş hata günlüklerini bulma

Tam hata iletileri için SAP bağdaştırıcınızın genişletilmiş günlüklerine bakın. Ayrıca [, SAP Bağlayıcısı için bir Genişletilmiş günlük dosyasını etkinleştirebilirsiniz](#extended-sap-logging-in-on-premises-data-gateway).

Haziran 2020 ve üzeri şirket içi veri ağ geçidi sürümleri için, [uygulama ayarlarında ağ geçidi günlüklerini etkinleştirebilirsiniz](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app). 

* Varsayılan günlük kaydı düzeyi **uyarıdır**.

* Şirket içi veri ağ geçidi uygulamasının **Tanılama** ayarlarında **ek günlüğe yazmayı** etkinleştirirseniz, günlüğe kaydetme düzeyi **bilgilendirme** düzeyine yükseltilir.

* Günlüğe kaydetme düzeyini **ayrıntılı** olarak artırmak için yapılandırma dosyanızda aşağıdaki ayarı güncelleştirin. Genellikle, yapılandırma dosyası konumunda bulunur `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` .

```json
<setting name="SapTraceLevel" serializeAs="String">
   <value>Verbose</value>
</setting>
```

2020 Nisan 'dan ve önceki sürümlerde bulunan şirket içi veri ağ geçidi sürümleri için Günlükler varsayılan olarak devre dışıdır.

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>Şirket içi veri ağ geçidinde genişletilmiş SAP günlüğü

Logic Apps için bir şirket [içi veri ağ geçidi](../logic-apps/logic-apps-gateway-install.md)KULLANıYORSANıZ, SAP Bağlayıcısı için bir Genişletilmiş günlük dosyası yapılandırabilirsiniz. Windows için olay Izleme (ETW) olaylarını, ağ geçidinizin günlüğü. zip dosyalarına dahil edilen günlük dosyalarına yönlendirmek için şirket içi veri ağ geçidinizi kullanabilirsiniz. 

Ağ [geçidinizin yapılandırma ve hizmet günlüklerinizi](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) ağ geçidi uygulamasının ayarlarından bir. zip dosyasına dışarı aktarabilirsiniz.

> [!NOTE]
> Genişletilmiş günlük kaydı, her zaman etkinleştirildiğinde mantıksal uygulamalarınızın performansını etkileyebilir. Bir sorunu çözümleme ve sorun giderme ile işiniz bittiğinde Genişletilmiş günlük dosyalarını kapatmak en iyi uygulamadır.

#### <a name="capture-etw-events"></a>ETW olaylarını yakala

İsteğe bağlı olarak, gelişmiş kullanıcılar ETW olaylarını doğrudan yakalayabilir. Daha sonra [Event Hubs Azure tanılama verilerinizi kullanabilir](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md) veya [verilerinizi Azure izleyici günlüklerine toplayabilirsiniz](../azure-monitor/agents/diagnostics-extension-logs.md). Daha fazla bilgi için bkz. [veri toplama ve depolama için en iyi uygulamalar](/azure/architecture/best-practices/monitoring#collecting-and-storing-data). Elde edilen ETL dosyalarıyla çalışmak için [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) kullanabilirsiniz veya kendi programınızı yazabilirsiniz. Bu izlenecek yol PerfView kullanır:

1. PerfView menüsünde, olayları yakalamak için **Collect topla ' yı seçin** &gt;  .

1. **Ek sağlayıcı** ALANıNA, `*Microsoft-LobAdapter` SAP BAĞDAŞTıRıCıSı olaylarını yakalayan SAP sağlayıcısını belirtmek için yazın. Bu bilgileri belirtmezseniz, izleme yalnızca genel ETW olaylarını içerir.

1. Diğer varsayılan ayarları koruyun. İsterseniz, **veri dosyası** alanındaki dosya adını veya konumunu değiştirebilirsiniz.

1. İzlemeye başlamak için **koleksiyonu Başlat** ' ı seçin.

1. Sorununuzu yeniden oluşturduktan veya yeterli analiz verisi topladıktan sonra, **toplamayı durdur**' u seçin.

Verilerinizi Azure destek mühendisleri gibi başka bir tarafla paylaşmak için ETL dosyasını sıkıştırın.

İzlemenin içeriğini görüntülemek için:

1. PerfView 'da **Dosya** &gt; **Aç** ' ı seçin ve az önce oluşturduğunuz etl dosyasını seçin.

1. PerfView kenar çubuğu ' nda, ETL dosyanızın altındaki **Olaylar** bölümü.

1. **Filtre** altında `Microsoft-LobAdapter` yalnızca ilgili olayları ve ağ geçidi süreçlerini görmek için filtre uygulayın.

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

1. Mantıksal uygulamanızı tetiklemek için SAP sisteminizden bir ileti gönderin.

1. Mantıksal uygulama menüsünde **genel bakış**' ı seçin. Mantıksal uygulamanız için tüm yeni çalıştırmalar için **çalıştırma geçmişini** gözden geçirin.

1. Tetikleyici çıkışları bölümünde SAP sisteminizden gönderilen iletiyi gösteren en son çalıştırmayı açın.

### <a name="test-sending-idocs-from-sap"></a>SAP 'den IDoc 'Ları göndermeyi sınama

SAP 'den Logic App 'e IDoc 'Ları göndermek için, aşağıdaki en düşük yapılandırmaya ihtiyacınız vardır:

> [!IMPORTANT]
> Bu adımları yalnızca, SAP yapılandırmanızı mantıksal uygulamanızla test ettiğinizde kullanın. Üretim ortamları için ek yapılandırma gerekir.

1. [SAP 'de RFC hedefi yapılandırma](#create-rfc-destination)

1. [RFC Hedefinizdeki bir ABAP bağlantısı oluşturma](#create-abap-connection)

1. [Alıcı bağlantı noktası oluşturma](#create-receiver-port)

1. [Gönderici bağlantı noktası oluşturma](#create-sender-port)

1. [Mantıksal sistem ortağı oluşturma](#create-logical-system-partner)

1. [İş ortağı profili oluşturma](#create-partner-profiles)

1. [İletileri göndermeyi sınama](#test-sending-messages)

#### <a name="create-rfc-destination"></a>RFC hedefi oluştur

1. **RFC bağlantı ayarlarının yapılandırmasını** açmak IÇIN, SAP arabiriminizdeki **sm59** Transaction Code (T-Code) ile **/n** önekini kullanın.

1. **TCP/IP bağlantıları**  >  **Oluştur**' u seçin.

1. Aşağıdaki ayarlarla yeni bir RFC hedefi oluşturun:
    
    * **RFC hedefi** için bir ad girin.
    
    * **Teknik ayarlar** sekmesinde, **etkinleştirme türü** Için, **kayıtlı sunucu programı**' nı seçin. **Program kimliğiniz** için bir değer girin. SAP 'de mantıksal uygulamanızın tetikleyicisi bu tanımlayıcı kullanılarak kaydedilir.

    > [!IMPORTANT]
    > SAP **program kimliği** büyük/küçük harfe duyarlıdır. Mantıksal uygulamanızı ve SAP sunucunuzu yapılandırırken, **Program kimliğiniz** için tutarlı olarak aynı durum biçimini kullandığınızdan emin olun. Aksi halde, SAP 'ye bir IDoc göndermeye çalıştığınızda tRFC Izleyicisinde (T-Code SM58) aşağıdaki hataları alabilirsiniz:
    >
    > * **İşlev IDOC_INBOUND_ASYNCHRONOUS bulunamadı**
    > * **ABAP olmayan RFC istemcisi (iş ortağı türü) desteklenmiyor**
    >
    > SAP hakkında daha fazla bilgi için, aşağıdaki notlara (oturum açma gerekir) ve ' a bakın <https://launchpad.support.sap.com/#/notes/2399329> <https://launchpad.support.sap.com/#/notes/353597> .
    
    * **Unicode** sekmesinde, **hedef sistemle Iletişim türü** için **Unicode**' u seçin.

1. Yaptığınız değişiklikleri kaydedin.

1. Yeni **Program kimliğinizi** Azure Logic Apps kaydedin.

1. Bağlantınızı test etmek için, SAP arabiriminde, yeni **RFC hedefi** altında **bağlantı testi**' ni seçin.

#### <a name="create-abap-connection"></a>ABAP bağlantısı oluştur

1. **RFC bağlantı ayarlarının yapılandırmasını** açmak IÇIN, SAP arabiriminizdeki **sm59** _ işlem kodunu (T-Code) _ */n** önekiyle birlikte kullanın.

1. **ABAP bağlantıları**  >  **Oluştur**' u seçin.

1. **RFC hedefi** için, [Test SAP sisteminizin](#create-rfc-destination)tanımlayıcısını girin.

1. Yaptığınız değişiklikleri kaydedin.

1. Bağlantınızı test etmek için **bağlantı testi**' ni seçin.

#### <a name="create-receiver-port"></a>Alıcı bağlantı noktası oluştur

1. **Bağlantı NOKTALARıNı IDoc işlem ayarları 'nda** açmak IÇIN, SAP arabiriminizdeki **we21** işlem kodunu (T-Code) **/n** prefix ile kullanın.

1. **Bağlantı noktaları**  >  **işlem RFC**  >  **Oluştur**' u seçin.

1. Açılan ayarlar kutusunda, **kendi bağlantı noktası adı**' nı seçin. Test bağlantı noktası için bir **ad** girin. Yaptığınız değişiklikleri kaydedin.

1. Yeni alıcı bağlantı noktanızın ayarlarında, **RFC hedefi** için, [Test RFC hedefi](#create-rfc-destination)için tanımlayıcıyı girin.

1. Yaptığınız değişiklikleri kaydedin.

#### <a name="create-sender-port"></a>Gönderici bağlantı noktası oluştur

1.  **Bağlantı NOKTALARıNı IDoc işlem ayarları 'nda** açmak IÇIN, SAP arabiriminizdeki **we21** işlem kodunu (T-Code) **/n** prefix ile kullanın.

1. **Bağlantı noktaları**  >  **işlem RFC**  >  **Oluştur**' u seçin.

1. Açılan ayarlar kutusunda, **kendi bağlantı noktası adı**' nı seçin. Test bağlantı noktası için **SAP** ile başlayan bir **ad** girin. Tüm gönderici bağlantı noktası adları **SAP** ile başlamalıdır, örneğin, **saptest**. Yaptığınız değişiklikleri kaydedin.

1. Yeni gönderici bağlantı noktanızın ayarlarında, **RFC hedefi** IÇIN, [ABAP bağlantınızın](#create-abap-connection)tanımlayıcısını girin.

1. Yaptığınız değişiklikleri kaydedin.

#### <a name="create-logical-system-partner"></a>Mantıksal sistem ortağı oluştur

1. **"Mantıksal sistemler" değişiklik görünümünü açmak için: genel bakış** ayarları, SAP arabiriminizdeki **bd54** işlem kodunu (T-Code) kullanın.

1. Görüntülenen uyarı iletisini kabul edin: **dikkat: tablo çapraz istemci**

1. Mevcut mantıksal sistemlerinizi gösteren listenin üzerinde **yeni girişler**' i seçin.

1. Yeni mantıksal sisteminiz için **Log.Sysdıtem** tanımlayıcısı ve kısa **ad** açıklaması girin. Yaptığınız değişiklikleri kaydedin.

1. Çalışma **ekranı istemi** göründüğünde, bir açıklama sağlayarak yeni bir istek oluşturun veya zaten bir istek oluşturduysanız, bu adımı atlayın.

1. Çalışma ekranı isteğini oluşturduktan sonra, bu isteği tablo güncelleştirme isteğine bağlayın. Tablonuzun güncelleştirildiğini onaylamak için değişikliklerinizi kaydedin.

#### <a name="create-partner-profiles"></a>İş ortağı profilleri oluşturma

Üretim ortamları için iki iş ortağı profili oluşturmanız gerekir. İlk profil, kuruluşunuz ve SAP sisteminiz olan gönderen içindir. İkinci profil, mantıksal uygulamanız olan alıcı içindir.

1. **Iş ortağı profilleri** ayarlarını açmak IÇIN, SAP arabiriminizdeki **we20** Transaction Code (T-Code) ile **/n** önekini kullanın.

1. **İş ortağı profilleri** altında, **iş ortağı türü**  >  **Oluştur**' u seçin.

1. Aşağıdaki ayarlarla yeni bir iş ortağı profili oluşturun:

    * **Iş ortağı No** için, [mantıksal sistem ortağınızın tanımlayıcısını](#create-logical-system-partner)girin.

    * **Partn için. Yazın**, **ls** girin.

    * **Aracı** için, Azure LOGIC Apps veya SAP olmayan diğer sistemler için program tanımlayıcıları KAYDETTIĞINIZDE kullanılacak SAP Kullanıcı hesabının tanımlayıcısını girin.

1. Yaptığınız değişiklikleri kaydedin. [Mantıksal sistem ortağını oluşturmadıysanız](#create-logical-system-partner), hatayı alırsınız, **geçerli bir Iş ortağı numarası girin**.

1. İş ortağı profilinizin ayarlarında **giden parmtrs**' ın altında, **giden parametre oluştur**' u seçin.

1. Aşağıdaki ayarlarla yeni bir giden parametre oluşturun:

    * **Ileti türünü** girin, örneğin, **Cremas**.

    * [Alıcı bağlantı noktanızın tanımlayıcısını](#create-receiver-port)girin.

    * Pack için bir IDoc boyutu girin **. Boyut**. Ya da, [SAP 'den bir kerede bir IDoc göndermek](#receive-idoc-packets-from-sap)için IDoc 'U **hemen geçir**' i seçin.

1. Yaptığınız değişiklikleri kaydedin.

#### <a name="test-sending-messages"></a>İletileri göndermeyi sınama

1. **IDoc işlem ayarları Için test aracı** 'nı açmak üzere SAP arabiriminizdeki **we19** Transaction Code (T-Code) ile **/n** önekini kullanın.

1. **Test Için şablon**' ın altında, **ileti türü aracılığıyla**' i seçin ve ileti türü, örneğin, **ekip gibi girin.** **Oluştur**’u seçin.

1. **Devam**' i seçerek **IDoc türü?** iletisini onaylayın.

1. **Edidc** düğümünü seçin. Alıcı ve gönderici bağlantı noktalarınız için uygun değerleri girin. **Devam**’ı seçin.

1. **Standart giden işleme**' yı seçin.

1. Giden IDoc işlemesini başlatmak için **devam**' ı seçin. İşlem tamamlandığında **SAP sistemine veya harici program iletisine gönderilen IDoc** görüntülenir.

1.  İşleme hatalarını denetlemek için, **/n** önekiyle birlikte **SM58** Transaction Code (T-Code) kullanın.

## <a name="receive-idoc-packets-from-sap"></a>SAP 'den IDoc paketleri alma

SAP 'yi, toplu işler veya IDoc grupları olan [paketlerdeki IDoc 'ları gönderecek](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html)şekilde ayarlayabilirsiniz. IDoc paketlerini, SAP bağlayıcısını ve özellikle tetikleyiciyi almak için ek yapılandırma gerekmez. Ancak, bir IDoc paketindeki her öğeyi tetikleme paketini aldıktan sonra işlemek için, paketi tek tek IDoc 'a bölmek için bazı ek adımlar gerekir.

İşte, [ `xpath()` işlevi](./workflow-definition-language-functions-reference.md#xpath)kullanarak bir paketten tek tek IDoc 'ları nasıl ayıklayabileceğiniz gösterilmektedir:

1. Başlamadan önce SAP tetikleyicisine sahip bir mantıksal uygulama gerekir. Bu mantıksal uygulamanızda zaten yoksa, [SAP tetikleyicisiyle bir mantıksal uygulama ayarlamak](#receive-message-from-sap)için bu konudaki önceki adımları izleyin.

    > [!IMPORTANT]
    > SAP **program kimliği** büyük/küçük harfe duyarlıdır. Mantıksal uygulamanızı ve SAP sunucunuzu yapılandırırken, **Program kimliğiniz** için tutarlı olarak aynı durum biçimini kullandığınızdan emin olun. Aksi halde, SAP 'ye bir IDoc göndermeye çalıştığınızda tRFC Izleyicisinde (T-Code SM58) aşağıdaki hataları alabilirsiniz:
    >
    > * **İşlev IDOC_INBOUND_ASYNCHRONOUS bulunamadı**
    > * **ABAP olmayan RFC istemcisi (iş ortağı türü) desteklenmiyor**
    >
    > SAP hakkında daha fazla bilgi için, aşağıdaki notlara (oturum açma gerekir) ve ' a bakın <https://launchpad.support.sap.com/#/notes/2399329> <https://launchpad.support.sap.com/#/notes/353597> .

   Örnek:

   ![Logic App 'e SAP tetikleyicisi ekleme](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. SAP isteğinizin durumuyla hemen yanıtlamak için [mantıksal uygulamanıza bir yanıt eylemi ekleyin](../connectors/connectors-native-reqres.md#add-a-response-action) . Bu eylemi, bir iletişim kanalını SAP sunucunuz ile boşaltmak için tetikleyicinizin hemen arkasına eklemek en iyi uygulamadır. `statusCode`Yanıt eyleminiz içinde kullanmak için aşağıdaki durum kodlarından birini () seçin:

    * **kabul edilen 202**, bu, isteğin işleme için kabul edildiği anlamına gelir ancak işlem henüz tamamlanmaz.

    * **204 Içerik yok**, bu, sunucunun isteği başarıyla karşıladığı ve yanıt yükü gövdesinde gönderilmesi gereken ek bir içerik olmadığı anlamına gelir. 

    * **200 Tamam**. Bu durum kodu, sunucu, sıfır uzunlukta bir yük gövdesi üretse bile her zaman bir yük içerir. 

1. Mantıksal uygulamanızın SAP 'den aldığı XML IDoc 'ından kök ad alanını alın. Bu ad alanını XML belgesinden ayıklamak için, bir yerel dize değişkeni oluşturan ve bu ad alanını bir ifade kullanarak depolayan bir adım ekleyin `xpath()` :

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![IDoc 'dan kök ad alanı al](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Tek bir IDoc 'u ayıklamak için, bir dizi değişkeni oluşturan ve IDoc koleksiyonunu başka bir ifade kullanarak depolayan bir adım ekleyin `xpath()` :

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Öğelerin dizisini al](./media/logic-apps-using-sap-connector/get-array.png)

   Dizi değişkeni, mantıksal uygulamanız için her bir IDoc 'un koleksiyon üzerinde sıralama yaparak tek tek işlemesini sağlar. Bu örnekte, Logic App her IDoc 'u bir döngü kullanarak bir SFTP sunucusuna aktarır:

   ![SFTP sunucusuna IDoc gönder](./media/logic-apps-using-sap-connector/loop-batch.png)

   Her IDoc 'un, `<Receive></Receive` Bu durumda IDoc 'un aşağı akış uygulamasına veya SFTP sunucusuna göndermeden önce kök ad alanı ile birlikte, dosya içeriğinin bir öğe içinde sarmalanması neden olan kök ad alanını içermesi gerekir.

Yeni bir mantıksal uygulama oluştururken Logic Apps tasarımcısında bu şablonu seçerek bu model için hızlı başlangıç şablonunu kullanabilirsiniz.

![Batch mantıksal uygulama şablonu seçin](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>SAP 'de yapılar için şemalar oluşturma

Bu örnek, bir HTTP isteğiyle tetikleyebileceğiniz bir mantıksal uygulama kullanır. Belirtilen IDoc ve BAPı için şemaları oluşturmak üzere, **şema oluştur** SAP EYLEMI bir SAP sistemine istek gönderir.

Bu SAP eylemi, XML belgesinin içeriğini veya verilerini değil, bir [XML şeması](#sample-xml-schemas)döndürür. Yanıtta döndürülen şemalar, Azure Resource Manager Bağlayıcısı kullanılarak bir tümleştirme hesabına yüklenir. Şemalar aşağıdaki bölümleri içerir:

* İstek iletisinin yapısı. BAPı listenizi oluşturmak için bu bilgileri kullanın `get` .

* Yanıt iletisinin yapısı. Yanıtı ayrıştırmak için bu bilgileri kullanın. 

İstek iletisini göndermek için **SAP 'a Ileti gönder** Genel SAP eylemini veya hedeflenen **çağrı BAPI** eylemlerini kullanın.

### <a name="sample-xml-schemas"></a>Örnek XML şemaları

Örnek belge oluştururken kullanılmak üzere bir XML şeması oluşturmayı öğreniyor olmanız durumunda aşağıdaki örneklere bakın. Bu örneklerde aşağıdakiler de dahil olmak üzere birçok yük türüyle nasıl çalışabilmeniz gösterilmektedir:

* [RFC istekleri](#xml-samples-for-rfc-requests)

* [BAPı istekleri](#xml-samples-for-bapi-requests)

* [IDoc istekleri](#xml-samples-for-idoc-requests)

* Basit veya karmaşık XML şeması veri türleri

* Tablo parametreleri

* İsteğe bağlı XML davranışları

XML şemanıza, isteğe bağlı bir XML girişi ile başlayabilirsiniz. SAP Bağlayıcısı, XML girişi olmadan veya ile birlikte kullanılabilir.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>RFC istekleri için XML örnekleri

Aşağıdaki örnek, temel bir RFC çağrıdır. RFC adı `STFC_CONNECTION` . Bu istek varsayılan ad alanını kullanır `xmlns=` , ancak gibi ad alanı diğer adlarını atayabilir ve kullanabilirsiniz `xmmlns:exampleAlias=` . Ad alanı değeri, Microsoft Hizmetleri için SAP 'deki tüm RFC 'Ler için ad alanıdır. İstekte basit bir giriş parametresi vardır `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

Aşağıdaki örnek, bir tablo parametresi olan bir RFC çağrıdır. Bu örnek çağrı ve test RFC 'Leri grubu, tüm SAP sistemlerinin bir parçası olarak kullanılabilir. Tablo parametresinin adı `TCPICDAT` . Tablo satırı türü `ABAPTEXT` , ve bu öğe tablodaki her satır için yinelenir. Bu örnek, adlı tek bir satır içerir `LINE` . Tablo parametresi olan istekler, sayının pozitif bir tamsayı (*n*) olduğu herhangi bir sayıda alan içerebilir. 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

Aşağıdaki örnek, anonim bir alana sahip bir tablo parametresi olan bir RFC çağrıdır. Anonim bir alan, alanın atanmış adı yoktur. Karmaşık türler, bildirimin geçerli düğüm ve tüm alt öğeleri için yeni bir varsayılan ayarladığı ayrı bir ad alanı altında bildirilmiştir. Örnek, `x002F` */* Bu sembol SAP alan adında ayrıldığından, bu simge, simgenin çıkış karakteri olarak onaltılı kodu kullanır.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

Aşağıdaki örnek ad alanları için önekleri içerir. Tüm ön ekleri aynı anda bildirebilir veya bir düğümün özniteliği olarak herhangi bir sayıda önek bildirebilirsiniz. RFC ad alanı diğer adı, `ns0` temel tür için kök ve parametreler olarak kullanılır.

> [!NOTE]
> karmaşık türler, `ns3` diğer ad ile normal RFC ad alanı yerine diğer ada sahıp RFC türleri için farklı bir ad alanı altında bildirilmiştir `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>BAPI istekleri için XML örnekleri

Aşağıdaki XML örnekleri, [BAPI yöntemini çağırmak](#call-bapi-action)için örnek isteklerdir.

> [!NOTE]
> SAP `RPY_BOR_TREE_INIT` , hiçbir giriş filtresiyle ilgili sorunları LOGIC Apps RFC 'e yanıt olarak tanımlayarak, iş nesnelerini dış sistemler için kullanılabilir hale getirir. Logic Apps çıktı tablosunu inceler `BOR_TREE` . `SHORT_TEXT`Alan, iş nesnelerinin adları için kullanılır. Çıkış tablosunda SAP tarafından döndürülmeyen iş nesnelerine Logic Apps erişilemez.
> 
> Özel iş nesneleri kullanıyorsanız, bu iş nesnelerini SAP 'de yayımlamayı ve serbest bırakmanız gerekir. Aksi takdirde SAP, özel iş nesnelerinizi çıkış tablosunda listeetmez `BOR_TREE` . İş nesneleri SAP 'den kullanıma sunana kadar Logic Apps özel iş nesneleriniz erişemez. 

Aşağıdaki örnek, BAPı metodunu kullanarak bankalar listesini alır `GETLIST` . Bu örnek, bir banka için iş nesnesini içerir `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

Aşağıdaki örnek, yöntemini kullanarak bir banka nesnesi oluşturur `CREATE` . Bu örnek, önceki örnekle aynı iş nesnesini kullanır `BUS1011` . `CREATE`Bir banka oluşturmak için yöntemini kullandığınızda, bu yöntem varsayılan olarak yürütülmediği için değişikliklerinizi yürütdiğinizden emin olun.

> [!TIP]
> XML belgenizin, SAP sisteminizde yapılandırılmış tüm doğrulama kurallarını izlediğinden emin olun. Örneğin, bu örnek belgede, banka anahtarı ( `<BANK_KEY>` ), ABD 'de de ABA numarası olarak da bilinen bir banka yönlendirme numarası olmalıdır.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

Aşağıdaki örnek banka yönlendirme numarası, için değeri kullanılarak bir bankanın ayrıntılarını alır `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>IDoc istekleri için XML örnekleri

Bir düz SAP IDoc XML şeması oluşturmak için **SAP oturum açma** uygulamasını ve T-Code ' u kullanın `WE-60` . SAP belgelerine GUI aracılığıyla erişin ve XML şemaları, IDoc türlerinizin ve uzantılarınızın XSD biçiminde oluşturun. Genel SAP biçimleri ve yükleri hakkında bir açıklama ve yerleşik iletişim kutuları için bkz. [SAP belgeleri](https://help.sap.com/viewer/index).

Bu örnek, kök düğümü ve ad alanlarını bildirir. Örnek koddaki URI, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` aşağıdaki yapılandırmayı bildirir:

* `/IDoc` Tüm IDoc 'Lar için kök notdır
* `/3` ortak kesim tanımlarının kayıt türleri sürümüdür
* `/ORDERS05` IDoc türü
* `//` , IDoc uzantısı olmadığından boş bir kesimdir
* `/700` SAP sürümüdür
* `/Send` bilgileri SAP 'ye gönderme eylemi

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

Tek bir çağrıda bir `idocData` IDoc toplu işi göndermek için düğümü yineleyebilirsiniz. Aşağıdaki örnekte, bir denetim kaydı, `EDI_DC40` ve birden çok veri kaydı vardır.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

Aşağıdaki örnek, önekini kullanan bir örnek IDoc denetim kaydıdır `EDI_DC` . Değerlerini SAP yüklemeniz ve IDoc türü ile eşleşecek şekilde güncelleştirmeniz gerekir. Örneğin, IDoc istemci kodunuz olmayabilir `800` . SAP yüklemenizin doğru değerlerini kullandığınızdan emin olmak için SAP ekibinize başvurun.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

Aşağıdaki örnek, düz kesimlere sahip bir örnek veri kaydıdır. Bu örnek, SAP tarih biçimini kullanır. Kesin yazılı belgeler, gibi yerel XML tarih biçimlerini kullanabilir `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

Aşağıdaki örnek, gruplandırılmış kesimlere sahip bir veri kaydıdır. Kayıt, ve dahil olmak üzere bir grup üst düğümü, ve `E2EDKT1002GRP` birden çok alt düğüm içerir `E2EDKT1002` `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```


Önerilen yöntem, tRFC ile kullanmak için bir IDoc tanımlayıcı oluşturmaktır. Bu işlem tanımlayıcısını, `tid` SAP Bağlayıcısı API 'Sindeki [IDoc Send işlemini](/connectors/sap/#send-idoc) kullanarak ayarlayabilirsiniz.

Aşağıdaki örnek, veya işlem tanımlayıcısı ayarlamak için alternatif bir yöntemdir `tid` . Bu örnekte, son veri kayıt kesimi düğümü ve IDoc veri düğümü kapalıdır. Ardından, GUID, `guid` yinelemeleri algılamak Için tRFC tanımlayıcısı olarak kullanılır. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>HTTP Istek tetikleyicisi ekleme

1. Azure portal, Logic Apps tasarımcısını açan boş bir mantıksal uygulama oluşturun.

1. Arama kutusuna `http request` filtreniz olarak yazın. **Tetikleyiciler** LISTESINDEN **bir http isteği alındığında** öğesini seçin.

   ![HTTP Istek tetikleyicisi Ekle](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Mantıksal uygulamanız için bir uç nokta URL 'SI oluşturabilmeniz için artık mantıksal uygulamanızı kaydedin.
Tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Uç nokta URL 'SI Şu anda tetikleyicide görüntülenir, örneğin:

   ![Uç nokta için URL Oluştur](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Şemaları oluşturmak için bir SAP eylemi ekleyin

1. Logic Apps tasarımcısında tetikleyici altında **yeni adım**' ı seçin.

   ![Mantıksal uygulamaya yeni adım Ekle](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Arama kutusuna `sap` filtreniz olarak yazın. **Eylemler** listesinden **şemalar oluştur**' u seçin.
  
   ![Logic App 'e "şema üret" eylemi ekleyin](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Ya da **Kurumsal** SEKMESINI seçip SAP eylemini seçebilirsiniz.

   ![Kurumsal sekmesinden SAP gönder eylemini seçin](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Bağlantınız zaten varsa, SAP eyleminizi ayarlamak için bir sonraki adımla devam edin. Bununla birlikte, bağlantı ayrıntıları istenirse, şu anda şirket içi SAP sunucunuza bir bağlantı oluşturabilmek için bilgileri sağlayın.

   1. Bağlantı için bir ad girin.

   1. **Veri ağ geçidi** bölümünde, **abonelik** altında, önce veri ağ geçidi yüklemeniz için Azure Portal oluşturduğunuz veri ağ geçidi kaynağı için Azure aboneliğini seçin. 
   
   1. **Bağlantı ağ geçidi** altında Azure 'da veri ağ geçidi kaynağınızı seçin.

   1. Bağlantıyla ilgili bilgi sağlamaya devam edin. **Oturum açma türü** özelliği için, özelliğin **uygulama sunucusu** veya **Grup** olarak ayarlanmış olup olmadığına bağlı olarak adımı izleyin:
   
      * **Uygulama sunucusu** için genellikle isteğe bağlı olarak görüntülenen bu özellikler gereklidir:

        ![SAP uygulama sunucusu bağlantısı oluşturma](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **Grup** için, genellikle isteğe bağlı olarak görüntülenen bu özellikler gereklidir:

        ![SAP ileti sunucusu bağlantısı oluştur](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Varsayılan olarak, şemaya karşı XML doğrulaması gerçekleştirerek geçersiz değerleri denetlemek için tanımlayıcı yazma kullanılır. Bu davranış, daha önce sorunları tespit etmenize yardımcı olabilir. **Güvenli yazma** seçeneği geriye dönük uyumluluk için kullanılabilir ve yalnızca dize uzunluğunu denetler. [Güvenli yazma seçeneği](#safe-typing)hakkında daha fazla bilgi edinin.

   1. İşiniz bittiğinde **Oluştur**' u seçin.

      Logic Apps, bağlantının düzgün çalıştığından emin olmak için bağlantınızı kurar ve test eder.

1. Şemayı oluşturmak istediğiniz yapıtın yolunu belirtin.

   Dosya seçicisinden SAP eylemini seçebilirsiniz:

   ![SAP eylemini seçin](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Ya da eylemi el ile girebilirsiniz:

   ![SAP eylemini el ile girin](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Birden fazla yapıt için şemalar oluşturmak üzere her yapıt için SAP eylem ayrıntılarını sağlayın, örneğin:

   ![Yeni öğe Ekle ' yi seçin](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![İki öğeyi göster](media/logic-apps-using-sap-connector/schema-generator-example.png)

   SAP eylemi hakkında daha fazla bilgi için bkz. [IDOC işlemleri Için ileti şemaları](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

1. Tasarımcı araç çubuğunda **Çalıştır** ' ı seçerek mantıksal uygulamanız için bir çalıştırma tetikleyin.

1. Çalıştır ' ı açın ve **şemalar oluştur** eyleminin çıkışlarını kontrol edin.

   Çıkışlar, belirtilen ileti listesi için üretilen şemaları gösterir.

### <a name="upload-schemas-to-an-integration-account"></a>Tümleştirme hesabına şemaları yükleme

İsteğe bağlı olarak, oluşturulan şemaları blob, depolama veya tümleştirme hesabı gibi depolarda indirebilir veya saklayabilirsiniz. Tümleştirme hesapları, diğer XML eylemleriyle birinci sınıf bir deneyim sağlar, bu nedenle bu örnek, Azure Resource Manager Bağlayıcısı kullanılarak aynı mantıksal uygulama için nasıl bir tümleştirme hesabına şema yükleneceğini gösterir.

1. Logic Apps tasarımcısında tetikleyici altında **yeni adım**' ı seçin.

1. Arama kutusuna `Resource Manager` filtreniz olarak yazın. **Kaynak oluştur veya Güncelleştir**' i seçin.

   ![Azure Resource Manager eylemi seçin](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Azure aboneliğiniz, Azure Kaynak grubunuz ve tümleştirme hesabınız dahil olmak üzere eyleme ilişkin ayrıntıları girin. Alanlara SAP belirteçleri eklemek için, bu alanlar için kutulara tıklayın ve görüntülenen dinamik içerik listesinden seçin.

   1. **Yeni parametre Ekle** listesini açın ve **konum** ve **Özellikler** alanlarını seçin.

   1. Bu yeni alanların ayrıntılarını bu örnekte gösterildiği gibi sağlayın.

      ![Azure Resource Manager eylemi için ayrıntıları girin](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP **oluşturma şemaları** eylemi, şemaları bir koleksiyon olarak oluşturur, böylece tasarımcı eyleme her bir döngü **için** otomatik olarak bir ekler. Bu eylemin nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir:

   !["For each" döngüsüyle Azure Resource Manager eylemi](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Şemalar Base64 kodlamalı biçim kullanır. Şemaları bir tümleştirme hesabına yüklemek için, işlevi kullanılarak kodu çözülmüş olmaları gerekir `base64ToString()` . Öğe için kodu gösteren bir örnek aşağıda verilmiştir `"properties"` :
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

1. Tasarımcı araç çubuğunda, mantıksal uygulamanızı el ile tetiklemek için **Çalıştır** ' ı seçin.

1. Başarılı bir çalıştıktan sonra, tümleştirme hesabına gidin ve oluşturulan şemaların mevcut olup olmadığını denetleyin.

## <a name="enable-secure-network-communications"></a>Güvenli ağ Iletişimini etkinleştir

Başlamadan önce, yalnızca veri ağ geçidini kullandığınızda ve mantıksal uygulamalarınızın çok kiracılı Azure 'da çalıştığı durumlarda uygulanan önceden listelenmiş [önkoşulları](#prerequisites)karşıladığınızdan emin olun:

* Şirket içi veri ağ geçidinin, SAP sisteminizle aynı ağda olan bir bilgisayarda yüklü olduğundan emin olun.

* SSO için, veri ağ geçidi, SAP kullanıcısına eşlenmiş bir kullanıcı olarak çalışır.

* Ek güvenlik işlevlerini sağlayan SNC kitaplığı, veri ağ geçidiyle aynı makineye yüklenir. Bazı örneklerde [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos ve NTLM vardır.

   SAP sistemine yönelik istekleriniz için SNC 'yi etkinleştirmek üzere SAP bağlantısı 'ndaki **SNC kullan** onay kutusunu seçin ve şu özellikleri sağlayın:

   ![Bağlantıda SAP SNC 'yi yapılandırma](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Özellik | Açıklama |
   |----------| ------------|
   | **SNC kitaplık yolu** | SNC Kitaplık adı veya yolu NCo yükleme konumuna veya mutlak yola göre. Örnekler `sapsnc.dll` veya `.\security\sapsnc.dll` veya `c:\security\sapsnc.dll` . |
   | **SNC SSO 'SU** | SNC üzerinden bağlandığınızda, genellikle çağıranın kimliğini doğrulamak için SNC kimliği kullanılır. Kullanıcı ve parola bilgilerinin çağıranın kimliğini doğrulamak için kullanılabilmesi, ancak hattın hala şifrelenmemesi için başka bir seçenek de geçersiz kılınmalıdır. |
   | **SNC My adı** | Çoğu durumda, bu özellik atlanabilir. Yüklü SNC çözümü genellikle kendi SNC adını bilir. Yalnızca birden çok kimliği destekleyen çözümler için, bu belirli hedef veya sunucu için kullanılacak kimliği belirtmeniz gerekebilir. |
   | **SNC Iş ortağı adı** | Arka uç SNC için ad. |
   | **SNC koruma kalitesi** | Bu belirli hedef veya sunucu için SNC iletişimi için kullanılacak hizmet kalitesi. Varsayılan değer, arka uç sistemi tarafından tanımlanır. En büyük değer, SNC için kullanılan güvenlik ürünü tarafından tanımlanır. |
   |||

   > [!NOTE]
   > Ortam değişkenlerini, veri ağ geçidi ve SNC kitaplığı olan makinede SNC_LIB ve SNC_LIB_64 ayarlama. Ayarlanırsa, bağlayıcı aracılığıyla geçirilen SNC kitaplık değerine göre önceliklidir.

## <a name="safe-typing"></a>Güvenli yazma

Varsayılan olarak, SAP bağlantınızı oluştururken şemaya göre XML doğrulaması gerçekleştirerek geçersiz değerleri denetlemek için güçlü yazma kullanılır. Bu davranış, daha önce sorunları tespit etmenize yardımcı olabilir. **Güvenli yazma** seçeneği geriye dönük uyumluluk için kullanılabilir ve yalnızca dize uzunluğunu denetler. **Güvenli yazma**' yı SEÇERSENIZ, SAP içindeki Bats türü ve Tims türü, XML eşdeğerleri ve, yerine dize olarak değerlendirilir `xs:date` `xs:time` `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . Güvenli yazma, tüm şema oluşturma davranışlarını, "gönderilen" yükün ve "alınmış" yanıtın ve tetikleyicisinin her ikisi için de ileti gönder ' i etkiler. 

Tanımlayıcı yazma kullanıldığında (**Güvenli yazma** etkin değil), şema, ve Tims türlerini daha basit XML türlerine eşler:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Güçlü yazma kullanarak iletiler gönderdiğinizde, izin ve TIMS yanıtı eşleşen XML türü biçimine uyar:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

**Güvenli yazma** etkin olduğunda, şema yalnızca uzunluk KıSıTLAMALARıNA sahip XML dize alanlarıyla birlikte, ve gibi, şema ve Tims türlerini eşler.

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

İletiler **Güvenli yazma** etkinken gönderildiğinde, aşağıdaki örnekteki gibi görünür:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Gelişmiş senaryolar

### <a name="change-language-headers"></a>Dil üstbilgilerini değiştirme

Logic Apps 'ten SAP 'ye bağlandığınızda, bağlantı için varsayılan dil Ingilizce 'dir. Gelen istekleriniz ile [standart http üstbilgisini `Accept-Language` ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) kullanarak bağlantınızın dilini ayarlayabilirsiniz.

> [!TIP]
> Çoğu Web tarayıcısı `Accept-Language` , kullanıcının ayarlarına bağlı olarak bir üst bilgi ekler. Web tarayıcısı, Logic Apps tasarımcısında yeni bir SAP bağlantısı oluşturduğunuzda bu üstbilgiyi uygular. Web tarayıcınızın tercih ettiği dilde SAP bağlantıları oluşturmak istemiyorsanız, Web tarayıcınızın ayarlarını tercih ettiğiniz dili kullanacak şekilde güncelleştirin ya da Logic Apps Tasarımcısı yerine Azure Resource Manager kullanarak SAP bağlantınızı oluşturun. 

Örneğin, `Accept-Language` **http istek** tetikleyicisi ' ni kullanarak üst bilgiyle bir istek ile mantıksal uygulamanıza bir istek gönderebilirsiniz. Mantıksal uygulamanızdaki tüm eylemler üstbilgiyi alır. Ardından SAP, sistem iletilerinde BAPı hata iletileri gibi belirtilen dilleri kullanır.

Bir mantıksal uygulamanın SAP bağlantı parametrelerinin dil özelliği yoktur. Bu nedenle, `Accept-Language` üstbilgiyi kullanırsanız şu hatayı alabilirsiniz: **lütfen hesap bilgilerinizi ve/veya izinlerinizi denetleyip yeniden deneyin.** Bu durumda, bunun yerine SAP bileşeninin hata günlüklerine bakın. Hata aslında üstbilgiyi kullanan SAP bileşeninde gerçekleşir, bu nedenle şu hata iletilerinden birini alabilirsiniz:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>İşlemi açıkça Onayla

Logic Apps 'dan SAP 'ye işlem gönderdiğinizde, bu Exchange SAP belgesinde, [Işlem RFC sunucu programlarında](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)açıklandığı gibi iki adımda gerçekleşir. Varsayılan olarak, **SAP gönder** eylemi, işlev aktarımına yönelik adımları ve tek bir çağrıda işlem onayını işler. SAP Bağlayıcısı, bu adımları ayırma seçeneği sunar. Bir IDoc gönderebilir ve işlemi otomatik olarak onaylamanız yerine, açıkça **Işlem kimliği Onayla** eylemini kullanabilirsiniz.

İşlem KIMLIĞI onayını ayırma özelliği, örneğin ağ sorunları gibi nedenlerle hataların gerçekleşebileceği senaryolarda, SAP 'deki işlemleri yinelemek istemediğiniz durumlarda faydalıdır. İşlem KIMLIĞINI ayrı olarak onayladıktan sonra, işlem SAP sisteminizde yalnızca bir kez tamamlanır.

Bu düzenin gösterildiği bir örnek aşağıda verilmiştir:

1. Boş bir mantıksal uygulama oluşturun ve bir HTTP tetikleyicisi ekleyin.

1. SAP bağlayıcısından, **IDoc Send** eylemini ekleyin. SAP sisteminize göndereceğiniz IDoc 'un ayrıntılarını sağlayın.

1. İşlem KIMLIĞINI ayrı bir adımda açıkça onaylamak için, **TID 'Yi Onayla** alanında **Hayır**' ı seçin. İsteğe bağlı **Işlem KIMLIĞI GUID** alanı için, değeri el ile belirtebilir ya da bağlayıcının bu GUID 'yi otomatik olarak oluşturmasını ve bu GUID 'Yi IDoc gönderme eyleminden yanıt olarak döndürmesini sağlayabilirsiniz.

   ![IDOC eylem özelliklerini gönder](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. İşlem KIMLIĞINI açıkça onaylamak için, **Işlem kimliğini onayla** eylemini ekleyın ve [SAP 'ye yinelenen IDoc 'ları göndermekten kaçındığınızdan](#avoid-sending-duplicate-idocs)emin olun. Dinamik içerik listesinin görünmesi için **Işlem kimliği** kutusunun içine tıklayın. Bu listeden, **IDoc gönder** eyleminden döndürülen **işlem kimliği** değerini seçin.

   ![İşlem KIMLIĞI eylemini Onayla](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Bu adım çalıştıktan sonra, SAP Bağlayıcısı tarafında ve SAP sistem tarafında, geçerli işlem her iki uçta da tamamlanmış olarak işaretlenir.

#### <a name="avoid-sending-duplicate-idocs"></a>Yinelenen IDoc 'Ları göndermekten kaçının

Mantıksal uygulamanızdan SAP 'ye yinelenen IDoc 'Lar gönderilirken bir sorunla karşılaşırsanız, IDoc işlem tanımlayıcısı olarak kullanılacak bir dize değişkeni oluşturmak için aşağıdaki adımları izleyin. Bu işlem tanımlayıcısının oluşturulması, geçici kesintiler, ağ sorunları veya kayıp bildirimler gibi sorunlar olduğunda yinelenen ağ iletimlerini önlemeye yardımcı olur.

> [!NOTE]
> SAP sistemleri, belirli bir süre sonra veya varsayılan olarak 24 saat sonra bir işlem tanımlayıcıyı unutur. Sonuç olarak, KIMLIK veya GUID bilinmiyorsa SAP hiçbir şekilde bir işlem tanımlayıcısı doğrulayamaz.
> Bir işlem tanımlayıcısı için onay başarısız olursa, bu hata SAP sistemiyle ilgili bir işlem olup olmadığını, SAP 'nin onayı doğrulayabilmesi için başarısız olduğunu gösterir.

1. Logic Apps tasarımcısında, mantıksal uygulamanıza eylem **başlatma değişkenini** ekleyin. 

1. Eylem **başlatma değişkeni** düzenleyicisinde, aşağıdaki ayarları yapılandırın. Sonra değişikliklerinizi kaydedin.

    1. **Ad** için, değişkeniniz için bir ad girin. Örneğin, `IDOCtransferID`.

    1. **Tür** için, değişken türü olarak **dize** ' yi seçin.

    1. **Değer** için, dinamik içerik menüsünü açmak için **ilk değeri girin** metin kutusunu seçin. **İfadeler** sekmesini seçin. İşlevler listesinde, işlevi girin `guid()` . Sonra, değişikliklerinizi kaydetmek için **Tamam** ' ı seçin. **Değer** alanı artık `guid()` bir GUID üreten işleve ayarlanır.

1. **Değişken başlatma** eyleminden sonra, **IDoc gönder** eylemini ekleyin.

1. **IDoc 'un gönderileceği** eylem için düzenleyicide aşağıdaki ayarları yapılandırın. Sonra değişikliklerinizi kaydedin.

    1. **IDoc türü** için ileti türünü seçin ve **giriş IDoc iletisi** için iletinizi belirtin.

    1. **SAP sürümü sürümü** için SAP yapılandırmanızın değerlerini seçin.

    1. **Kayıt türleri sürümü** için SAP yapılandırmanızın değerlerini seçin.

    1. **Kimliği Onayla** için **Hayır**' ı seçin.

    1. **Yeni parametre listesi**  >  **işlem kimliği GUID 'yi** seçin. Dinamik içerik menüsünü açmak için metin kutusunu seçin. **Değişkenler** sekmesinde, oluşturduğunuz değişkenin adını seçin. Örneğin, `IDOCtransferID`.

1. **IDoc gönder** eyleminin başlık çubuğunda **..**  >  . seçeneğini belirleyin. **Ayarlar**. **Yeniden deneme ilkesi** için **varsayılan** &gt; **tamamlandı**' ı seçmeniz önerilir. Bununla birlikte, belirli gereksinimleriniz için özel bir ilke yapılandırabilirsiniz. Özel ilkeler için, geçici ağ kesintilerini aşmak üzere en az bir yeniden deneme yapılandırmanız önerilir.

1. Bu işlem **IDoc 'U gönderdikten** sonra **işlem kimliğini onayla** eylemini ekleyin.

1. **Işlem kimliğini onayla** eylemi için düzenleyicide aşağıdaki ayarları yapılandırın. Sonra değişikliklerinizi kaydedin.

    1. **Işlem kimliği** için, değişkeninizin adını tekrar girin. Örneğin, `IDOCtransferID`.

1. İsteğe bağlı olarak, test ortamınızda yinelenenleri kaldırma işlemini doğrulayın. Bir önceki adımda kullandığınız **Işlem kimliği** GUID 'ı Ile **IDoc gönder** eylemini tekrarlayın. Aynı IDoc 'u iki kez gönderdiğinizde, SAP 'nin tRFC çağrısının çoğaltılmasını belirleyebildiğini ve iki çağrıyı tek bir gelen IDoc iletisine çözdiğini doğrulayabilirsiniz.

## <a name="known-issues-and-limitations"></a>Bilinen sorunlar ve sınırlamalar

Yönetilen (ıSE) SAP bağlayıcısının Şu anda bilinen sorunları ve sınırlamaları aşağıda verilmiştir: 

* Genel olarak, SAP tetikleyicisi veri ağ geçidi kümelerini desteklemez. Bazı yük devretme durumlarında, SAP sistemiyle iletişim kuran veri ağ geçidi düğümü etkin düğümden farklı olabileceğinden beklenmeyen davranışlara neden olur.

  * Gönderme senaryolarında, yük devretme modundaki veri ağ geçidi kümeleri desteklenir. 

  * Yük Dengeleme modundaki veri ağ geçidi kümeleri durum bilgisi olan SAP eylemleri tarafından desteklenmez. Bu eylemler, **durum bilgisi olan oturum oluşturma**, **BAPI IŞLEMINI işleme**, **BAPI işlemini geri alma, işlem** **durum bilgisi kapatma oturumunu** ve **oturum kimliği** değeri belirten tüm eylemleri içerir. Durum bilgisi olan iletişimler aynı veri ağ geçidi kümesi düğümünde kalmalıdır. 

  * Durum bilgisi olan SAP eylemleri için, veri ağ geçidini küme dışı modunda veya yalnızca yük devretme için ayarlanmış bir kümede kullanın.

* SAP Bağlayıcısı Şu anda SAP yönlendirici dizelerini desteklemiyor. Şirket içi veri ağ geçidi, bağlanmak istediğiniz SAP sistemiyle aynı LAN üzerinde bulunmalıdır.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bu bağlayıcı hakkında, bağlayıcının Swagger dosyasında açıklanan Tetikleyiciler, Eylemler ve sınırlar gibi daha teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/sap/)bakın. Logic Apps için ek belgeler aşağıdaki eylemler için verilmiştir:

* [BAPı çağrısı](#call-bapi-action)

* [IDOC gönder](#send-idoc-action)

> [!NOTE]
> Bir [tümleştirme hizmeti ortamındaki (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için, bu bağlayıcının Ise etiketli sürümü bunun yerine [Ise ileti sınırlarını](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) kullanır.

### <a name="call-bapi-action"></a>BAPı eylemini çağır

[Call BAPI ( `CallBapi` )](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview)) eylemi, SAP sunucunuzdaki BAPI yöntemini çağırır. 

Çağrlarınız ile aşağıdaki parametreleri kullanmanız gerekir: 

* Aranabilir bir açılan menü olan **Iş nesnesi** ( `businessObject` ).

*  `method` Bir **iş nesnesini** seçtikten sonra kullanılabilir yöntemleri dolduran yöntemi (). Kullanılabilir yöntemler, seçilen **Iş nesnesine** bağlı olarak değişir.

*  `body` Çağrı için BAPI yöntemi giriş parametre değerlerini içeren XML belgesini veya BAPI parametrelerinizi içeren depolama blobunun URI 'sini çağıran bir XML belgesi () giriş BAPI parametreleri ().

BAPı 'un çağrı eylemini kullanma hakkında ayrıntılı örnekler için bkz. [BAPI Isteklerinin XML örnekleri](#xml-samples-for-bapi-requests).

> [!TIP]
> BAPı isteğinizi düzenlemek için Logic Apps tasarımcısını kullanıyorsanız, aşağıdaki arama işlevlerini kullanabilirsiniz: 
> 
> * Kullanılabilir yöntemlerin açılan menüsünü görmek için Tasarımcıda bir nesne seçin.
> * BAPı API çağrısı tarafından sunulan aranabilir listeyi kullanarak iş nesnesi türlerini anahtar sözcüğe göre filtreleyin.

### <a name="send-idoc-action"></a>IDoc eylemi gönder

[Send IDoc ( `SendIDoc` )](/connectors/sap/) işlemi, IDoc iletisini SAP sunucunuza gönderir.

Çağrlarınız ile aşağıdaki parametreleri kullanmanız gerekir: 

* Yerleşik bir açılan menü olan **isteğe bağlı uzantıya () sahip IDoc türü** `idocType` .

    * **SAP yayın sürümü** () isteğe bağlı parametresi, `releaseVersion` IDoc türünü seçtikten sonra değerleri doldurur ve seçilen IDoc türüne bağlıdır.

*  IDoc `body` yükünü içeren XML BELGESINI veya IDoc XML belgenizi içeren depolama blobunun URI 'Sini ÇAĞıRAN bir giriş IDoc iletisi (). Bu belge, WE60 IDoc belgelerine göre SAP ıDOC XML şemasına veya eşleşen SAP IDoc eylem URI 'sine yönelik oluşturulan şemaya uymalıdır.

IDoc 'u gönder eyleminin nasıl kullanılacağına ilişkin ayrıntılı örnekler için bkz. [SAP sunucunuza IDOC iletileri gönderme](#send-idoc-messages-to-sap-server)Kılavuzu.

İsteğe bağlı ' ı ()  parametresini kullanma için `confirmTid` , [işlemi açıkça onaylama için İzlenecek yol](#confirm-transaction-explicitly)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Logic Apps 'den [Şirket içi sistemlere bağlanın](../logic-apps/logic-apps-gateway-connection.md) .

* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)ile diğer ileti işlemlerini doğrulamayı, dönüştürmeyi ve kullanmayı öğrenin.

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md)hakkında bilgi edinin.