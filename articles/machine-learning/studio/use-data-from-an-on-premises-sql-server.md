---
title: Şirket içi SQL Server
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio klasik sürümü ile gelişmiş analizler gerçekleştirmek için bir şirket içi SQL Server veritabanındaki verileri kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 074a3e4521660f8f1ea905ddab1d3b13f48a0680
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839501"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Şirket içi SQL Server veritabanı kullanarak Azure Machine Learning Studio (klasik) analiz gerçekleştirme

Genellikle şirket içi verilerle çalışan kuruluşlar, makine öğrenimi iş yükleri için bulutun ölçeklendirilmesi ve çeviklerinden yararlanmak ister. Ancak, şirket içi verilerini buluta taşıyarak geçerli iş süreçlerini ve iş akışlarını kesintiye uğramasını istemiyor. Azure Machine Learning Studio (klasik) artık verilerinizi şirket içi SQL Server veritabanından okumayı ve ardından bu verilerle bir modeli eğitmek ve Puanlama destekler. Artık bulut ve şirket içi sunucunuz arasında verileri el ile kopyalamanız ve eşitlemeniz gerekmez. Bunun yerine, Azure Machine Learning Studio klasik sürümünde **verileri Içeri aktarma** modülü artık eğitim ve Puanlama işleriniz için şirket içi SQL Server veritabanından doğrudan okuyabilir.

Bu makalede, şirket içi SQL Server verilerini Azure Machine Learning Studio klasik sürümüne alma konusuna genel bakış sunulmaktadır. Çalışma alanları, modüller, veri kümeleri, denemeleri *vb.* gibi Studio kavramlarının klasik sürümü hakkında bilgi sahibi olduğunuzu varsayar.

> [!NOTE]
> Bu özellik ücretsiz çalışma alanları için kullanılamaz. Machine Learning fiyatlandırması ve katmanları hakkında daha fazla bilgi için bkz. [Azure Machine Learning fiyatlandırması](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Data Factory şirket içinde barındırılan Integration Runtime 'yi yükler
Azure Machine Learning Studio klasik sürümünde şirket içi SQL Server veritabanına erişmek için, daha önce Veri Yönetimi ağ geçidi olarak bilinen Data Factory kendi kendine barındırılan Integration Runtime indirip yüklemeniz gerekir. Bağlantıyı Machine Learning Studio (klasik) ' de yapılandırdığınızda, aşağıda açıklanan **veri ağ geçidini indir ve Kaydet** iletişim kutusunu kullanarak INTEGRATION RUNTIME (IR) indirme ve yükleme şansınız vardır.


Ayrıca, [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=39717)' nden MSI kurulum paketini indirip çalıştırarak da daha önce IR yükleyebilirsiniz. MSI Ayrıca, mevcut bir IR 'yi en son sürüme yükseltmek için kullanılabilir ve tüm ayarlar korunur.

Data Factory kendinden konak Integration Runtime aşağıdaki önkoşullara sahiptir:

* Data Factory şirket içinde barındırılan tümleştirme .NET Framework 4.6.1 veya üzerini içeren 64 bitlik bir Işletim sistemi gerektirir.
* Desteklenen Windows işletim sistemi sürümleri, Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* IR makinesi için önerilen yapılandırma en az 2 GHz, 4 çekirdek CPU, 8GB RAM ve 80GB disk.
* Konak makine hazırda bekleme, IR veri isteklerine yanıt vermiyor. Bu nedenle, IR 'yi yüklemeden önce bilgisayarda uygun bir güç planı yapılandırın. Makine hazırda bekleme moduna yapılandırıldıysa, IR yüklemesinde bir ileti görüntülenir.
* Kopyalama etkinliği belirli bir sıklıkta gerçekleştiği için makinedeki kaynak kullanımı (CPU, bellek) de yoğun ve boşta zamanlarla aynı düzene uyar. Kaynak kullanımı Ayrıca, taşınmakta olan veri miktarına göre büyük ölçüde farklılık gösterir. Birden çok kopyalama işi devam ederken, yoğun saatlerde kaynak kullanımını gözlemleyeceksiniz. Yukarıda listelenen en düşük yapılandırma Teknik olarak yeterliyse, veri taşıma yüküne bağlı olarak en düşük yapılandırmadan daha fazla kaynağa sahip bir yapılandırmaya sahip olmak isteyebilirsiniz.

Data Factory kendi kendine barındırılan Integration Runtime kurarken ve kullanırken aşağıdakileri göz önünde bulundurun:

* Tek bir bilgisayara yalnızca bir IR örneği yükleyebilirsiniz.
* Birden çok şirket içi veri kaynağı için tek bir IR kullanabilirsiniz.
* Farklı bilgisayarlara birden çok IRS aynı şirket içi veri kaynağına bağlayabilirsiniz.
* Bir IRS tek seferde yalnızca bir çalışma alanı için yapılandırırsınız. Şu anda, IRS çalışma alanları arasında paylaşılamaz.
* Tek bir çalışma alanı için birden çok IRS yapılandırabilirsiniz. Örneğin, geliştirme sırasında test veri kaynaklarınıza bağlanmış bir IR ve kullanıma hazır olduğunuzda bir üretim IR kullanmak isteyebilirsiniz.
* IR 'nin veri kaynağıyla aynı makinede olması gerekmez. Ancak veri kaynağına yaklaşmak, ağ geçidinin veri kaynağına bağlanması için geçen süreyi azaltır. Ağ geçidinin ve veri kaynağının kaynaklar için rekabet etmemesi için, IR 'yi şirket içi veri kaynağını barındıran bilgisayardan farklı bir makineye yüklemenizi öneririz.
* Bilgisayarınızda Power BI veya Azure Data Factory senaryoya hizmet eden bir IR zaten varsa, başka bir bilgisayara Azure Machine Learning Studio klasik sürümü için ayrı bir IR yüklersiniz.

  > [!NOTE]
  > Data Factory kendi kendine barındırılan Integration Runtime ve Power BI Gateway aynı bilgisayarda çalıştıramazsınız.
  >
  >
* Diğer veriler için Azure ExpressRoute kullanıyor olsanız bile, Azure Machine Learning Studio klasik sürümü için Data Factory kendinden konak Integration Runtime kullanmanız gerekir. ExpressRoute kullandığınızda bile veri kaynağınızı bir şirket içi veri kaynağı (bir güvenlik duvarının arkasında) olarak değerlendirmelisiniz. Machine Learning ve veri kaynağı arasında bağlantı kurmak için şirket içinde barındırılan Integration Runtime Data Factory kullanın.

Yükleme önkoşulları, yükleme adımları ve sorun giderme ipuçları hakkında ayrıntılı bilgileri [Data Factory Integration Runtime](../../data-factory/concepts-integration-runtime.md)makalesinde bulabilirsiniz.

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Şirket içi SQL Server veritabanınızdaki verileri Azure Machine Learning olarak alma
Bu kılavuzda, bir Azure Machine Learning çalışma alanında Azure Data Factory Integration Runtime ayarlayacaksınız, yapılandırıp sonra şirket içi SQL Server veritabanından verileri okuyacaksınız.

> [!TIP]
> Başlamadan önce tarayıcınızın açılır pencere engelleyicisini `studio.azureml.net`devre dışı bırakın. Google Chrome tarayıcısını kullanıyorsanız, Google Chrome WebStore ['](https://chrome.google.com/webstore/search/clickonce?_category=extensions)da bulunan çeşitli eklentilerden birini indirip yükleyin.
>
> [!NOTE]
> Azure Data Factory kendinden konak Integration Runtime daha önce Veri Yönetimi ağ geçidi olarak bilinirdi. Adım adım öğretici, bir ağ geçidi olarak bu uygulamaya başvuracaktır.  

### <a name="step-1-create-a-gateway"></a>1\. Adım: ağ geçidi oluşturma
İlk adım, ağ geçidini şirket içi SQL veritabanınıza erişmek üzere oluşturmak ve ayarlamak için kullanılır.

1. [Azure Machine Learning Studio (klasik)](https://studio.azureml.net/Home/) oturumunu açın ve içinde çalışmak istediğiniz çalışma alanını seçin.
2. Sol taraftaki **Ayarlar** dikey penceresine tıklayın ve sonra ÜSTTEKI **veri ağ geçitleri** sekmesine tıklayın.
3. Ekranın alt kısmındaki **yenı VERI ağ geçidi** ' ne tıklayın.

    ![Yeni veri ağ geçidi](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. **Yeni veri ağ geçidi** iletişim kutusunda, **ağ geçidi adını** girin ve Isteğe bağlı olarak bir **Açıklama**ekleyin. Yapılandırmanın sonraki adımına geçmek için sağ alt köşedeki oka tıklayın.

    ![Ağ geçidi adı ve açıklaması girin](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Veri ağ geçidini Indir ve Kaydet iletişim kutusunda, ağ GEÇIDI kayıt anahtarını panoya kopyalayın.

    ![Veri ağ geçidini indir ve Kaydet](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Henüz Microsoft Veri Yönetimi ağ geçidini indirip yüklemediyseniz, **veri yönetimi ağ geçidini indir**' e tıklayın. Bu sizi, ihtiyacınız olan ağ geçidi sürümünü seçebileceğiniz, indirebileceğiniz ve yükleyebileceğiniz Microsoft Indirme Merkezi ' ne götürür. Yükleme önkoşulları, yükleme adımları ve sorun giderme ipuçları hakkında ayrıntılı bilgileri makalenin başlangıç bölümlerinde bulabilirsiniz. [veri yönetimi ağ geçidi ile şirket içi kaynaklar ve bulut arasında veri taşıma](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Ağ Geçidi yüklendikten sonra Veri Yönetimi ağ geçidi Configuration Manager açılır ve **ağ geçidi kaydet** iletişim kutusu görüntülenir. Panoya kopyaladığınız **ağ geçidi kayıt anahtarını** yapıştırın ve **Kaydet**' e tıklayın.
8. Zaten yüklü bir ağ geçidiniz varsa Veri Yönetimi ağ geçidi Configuration Manager çalıştırın. **Anahtarı Değiştir**' e tıklayın, önceki adımda panoya kopyaladığınız **ağ geçidi kayıt anahtarını** yapıştırın ve **Tamam**' a tıklayın.
9. Yükleme tamamlandığında, Microsoft Veri Yönetimi Gateway Configuration Manager için **ağ geçidi kaydet** iletişim kutusu görüntülenir. Önceki adımda panoya kopyaladığınız ağ GEÇIDI kayıt anahtarını yapıştırın ve **Kaydet**' e tıklayın.

    ![Ağ geçidini Kaydet](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Microsoft Veri Yönetimi Gateway Configuration Manager **giriş** sekmesinde aşağıdaki değerler ayarlandığında ağ geçidi yapılandırması tamamlanır:

    * **Ağ geçidi adı** ve **örnek adı** , ağ geçidinin adına ayarlanır.
    * **Kayıt** **kaydedildi**olarak ayarlandı.
    * **Durum** **başlatıldı**olarak ayarlandı.
    * Alttaki durum çubuğu, bir yeşil onay işaretiyle birlikte **veri yönetimi ağ geçidi bulut hizmetine bağlı olarak** görüntülenir.

      ![Veri Yönetimi Ağ Geçidi Yöneticisi](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio klasik sürümü, kayıt başarılı olduğunda da güncellenir.

    ![Ağ Geçidi kaydı başarılı](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. **Veri ağ geçidini indir ve Kaydet** iletişim kutusunda, kurulumu gerçekleştirmek için onay işaretine tıklayın. **Ayarlar** sayfası ağ geçidi durumunu "çevrimiçi" olarak görüntüler. Sağ bölmede durum ve diğer yararlı bilgiler bulacaksınız.

    ![Ağ Geçidi ayarları](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Microsoft Veri Yönetimi ağ geçidi Configuration Manager **sertifika** sekmesine geçin. Bu sekmede belirtilen sertifika, portalda belirttiğiniz şirket içi veri deposunun kimlik bilgilerini şifrelemek/şifrelerini çözmek için kullanılır. Bu sertifika varsayılan sertifikadır. Microsoft bunu, sertifika yönetimi sisteminizde yedeklediğiniz kendi sertifikanıza değiştirmenizi önerir. Bunun yerine kendi sertifikanızı kullanmak için **Değiştir** ' e tıklayın.

    ![Ağ Geçidi sertifikasını değiştirme](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. seçim Ağ geçidiyle ilgili sorunları gidermek için ayrıntılı günlük kaydını etkinleştirmek istiyorsanız, Microsoft Veri Yönetimi ağ geçidi Configuration Manager **Tanılama** sekmesine geçin ve **sorun giderme amacıyla ayrıntılı günlüğü etkinleştir ' i işaretleyin** seçeneğini. Günlüğe kaydetme bilgileri, **uygulama ve hizmet günlükleri** -&gt; **veri yönetimi ağ geçidi** düğümü altında Windows Olay Görüntüleyicisi bulunabilir. Ağ geçidini kullanarak şirket içi veri kaynağına bağlantıyı test etmek için **Tanılama** sekmesini de kullanabilirsiniz.

    ![Ayrıntılı günlüğü etkinleştir](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Bu işlem, Azure Machine Learning Studio klasik sürümünde ağ geçidi kurulum işlemini tamamlar.
Artık şirket içi verilerinizi kullanmaya hazırsınız.

Her çalışma alanı için Studio 'da (klasik) birden çok ağ geçidi oluşturabilir ve ayarlayabilirsiniz. Örneğin, geliştirme sırasında test veri kaynaklarınıza bağlamak istediğiniz bir ağ geçidiniz ve üretim veri kaynaklarınız için farklı bir ağ geçidi olabilir. Azure Machine Learning Studio klasik sürümü, kurumsal ortamınıza bağlı olarak birden çok ağ geçidi ayarlama esnekliği sağlar. Şu anda çalışma alanları arasında bir ağ geçidi paylaşamazsınız ve tek bir bilgisayara yalnızca bir ağ geçidi yüklenebilir. Daha fazla bilgi için bkz. [veri yönetimi ağ geçidi ile şirket içi kaynaklar ve bulut arasında veri taşıma](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>2\. Adım: şirket içi veri kaynağından veri okumak için ağ geçidini kullanma
Ağ geçidini ayarladıktan sonra, verileri şirket içi SQL Server veritabanından veren bir denemeye **Içeri aktarma verileri** modülü ekleyebilirsiniz.

1. Machine Learning Studio (klasik) içinde, **denemeleri** sekmesini seçin, sol alt köşedeki **+ Yeni** ' ye tıklayın ve **boş denemeler** ' i seçin (veya kullanılabilir örnek denemeleri birini seçin).
2. **Veri alma** modülünü bulun ve deneme tuvaline sürükleyin.
3. Tuvalin altındaki **farklı kaydet** ' e tıklayın. Deneme adı için "Azure Machine Learning Studio (klasik) Şirket Içi SQL Server öğreticisini girin, çalışma alanını seçin ve **Tamam** onay işaretine tıklayın.

   ![Denemeyi yeni bir adla kaydet](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. **Verileri Içeri aktar** modülünü seçin ve ardından tuvalin sağındaki **Özellikler** bölmesinde, **veri kaynağı** açılır listesinde "Şirket içi SQL veritabanı" ' nı seçin.
5. Yüklediğiniz ve kaydettiğiniz **veri ağ geçidini** seçin. "(Yeni veri ağ geçidi ekle...)" seçeneğini belirleyerek başka bir ağ geçidi ayarlayabilirsiniz.

   ![Veri alma modülünün veri ağ geçidini seçin](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Yürütmek istediğiniz SQL **veritabanı sorgusunun** yanı sıra SQL **veritabanı sunucu adını** ve **veritabanı adını**girin.
7. **Kullanıcı adı ve parola** altında **değer girin** ' e tıklayın ve veritabanı kimlik bilgilerinizi girin. Şirket içi SQL Server nasıl yapılandırıldığına bağlı olarak Windows tümleşik kimlik doğrulaması veya SQL Server kimlik doğrulaması kullanabilirsiniz.

   ![Veritabanı kimlik bilgilerini girin](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   "Değerler gereklidir" iletisi yeşil onay işaretiyle "değerler kümesi" olarak değişir. Veritabanı bilgileri veya parola değişmediği takdirde kimlik bilgilerini yalnızca bir kez girmeniz gerekir. Azure Machine Learning Studio klasik sürümü, Bulutta kimlik bilgilerini şifrelemek için ağ geçidini yüklediğinizde verdiğiniz sertifikayı kullanır. Azure hiçbir şekilde şifreleme olmadan şirket içi kimlik bilgilerini depolamaz.

   ![Veri alma modülü özellikleri](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Denemeyi çalıştırmak için **Çalıştır** ' a tıklayın.

Deneme çalışmayı bitirdiğinde, **veri alma** modülünün çıkış bağlantı noktasına tıklayıp **Görselleştir**' i seçerek veritabanından içeri aktardığınız verileri görselleştirebilirsiniz.

Denemenizin geliştirilmesini tamamladıktan sonra modelinizi dağıtabilir ve çalıştırabilirsiniz. Toplu yürütme hizmetini kullanarak **veri Içeri aktarma** modülünde yapılandırılan şirket içi SQL Server veritabanındaki veriler okunacaktır ve skor için kullanılacaktır. Puanlama için Istek yanıtı hizmetini şirket içi veriler için kullanabilirsiniz, Microsoft bunun yerine [Excel eklentisinin](excel-add-in-for-web-services.md) kullanılmasını önerir. Şu anda, **verileri dışarı aktarma** yoluyla şirket içi SQL Server veritabanına yazma denemeleri veya yayımlanmış Web hizmetlerinde desteklenmez.
