---
title: Azure tablosu | Azure Marketi
description: Azure tablosu için lider yönetimini yapılandırın.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 9b24e6eb714c531b49ba08591bf4ed33d0f10101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812332"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Azure tablosu kullanarak müşteri adayı yönetimini yapılandırma

Müşteri Ilişkileri yönetimi (CRM) sisteminiz, Azure Marketi ve AppSource müşteri adaylarını almak üzere Iş Ortağı Merkezi 'nde açık bir şekilde desteklenmiyorsa, bu müşteri adaylarını işlemek için bir Azure tablosu kullanabilirsiniz. Sonra verileri dışarı aktarıp CRM sisteminize aktarabilirsiniz. Bu makaledeki yönergeler, Azure depolama hesabı oluşturma ve bu hesap altında bir Azure tablosu oluşturma sürecinde size yol gösterecektir. Ayrıca, teklifiniz bir müşteri adayı aldığında e-posta bildirimi göndermek için Microsoft Flow kullanarak yeni bir akış oluşturabilirsiniz.

## <a name="configure-azure-table"></a>Azure tablosunu yapılandırma

1. Azure hesabınız yoksa [ücretsiz bir deneme hesabı oluşturabilirsiniz](https://azure.microsoft.com/pricing/free-trial/).
2. Azure hesabınız etkin olduktan sonra [Azure Portal](https://portal.azure.com)oturum açın.
3. Azure portal, aşağıdaki yordamı kullanarak bir depolama hesabı oluşturun.  
    1. Sol menü çubuğunda **+ kaynak oluştur** ' u seçin.  **Yeni** bölme (dikey pencere) sağa görüntülenecektir.
    2. **Yeni** bölmesinde **depolama** ' yı seçin.  **Öne çıkan** bir liste sağ tarafta görüntülenir.
    3. Hesap oluşturmaya başlamak için **depolama hesabı** ' nı seçin.  [Depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)makalesindeki yönergeleri izleyin.

        ![Azure depolama hesabı oluşturma adımları](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Depolama hesapları hakkında daha fazla bilgi için [Hızlı Başlangıç Öğreticisi](https://docs.microsoft.com/azure/storage/)' ni seçin.  Depolama fiyatlandırması hakkında daha fazla bilgi için bkz. [Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

4. Depolama Hesabınız sağlanana kadar bekleyin, genellikle birkaç dakika sürer.  Ardından, **Tüm kaynaklarınızı gör** ' i seçerek veya Azure Portal sol gezinti menü çubuğundan **tüm kaynaklar** ' ı seçerek depolama hesabınıza Azure Portal.

    ![Azure depolama hesabınıza erişin](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. Depolama hesabı bölmesinizden **erişim anahtarları** ' nı seçin ve anahtar için *bağlantı dizesi* değerini kopyalayın. Bu değeri, Market teklifinizin müşteri adaylarını almak için Yayımlama portalında sağlamanız gereken *depolama hesabı bağlantı dizesi* değeridir. 

    Bağlantı almaya bir örnek:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure depolama anahtarı](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. Depolama hesabı bölmesinizden **Tablolar** ' ı seçin ve tablo oluşturmak Için **+ tablo** ' yı seçin. Tablonuz için bir ad girin ve **Tamam**' ı seçin. Bir MS akışını, müşteri adayları alındığında e-posta bildirimleri alacak şekilde yapılandırmak istiyorsanız bu değeri kaydedin.

    ![Azure tabloları](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Depolama tablonuzdaki verileri görmek için [Azure Depolama Gezgini](https://archive.codeplex.com/?p=azurestorageexplorer) 'ni veya başka bir aracı kullanabilirsiniz. Azure tablosundaki verileri de dışarı aktarabilirsiniz. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>Seçim Azure tablosu ile Microsoft Flow kullanma  

Azure tablosuna bir müşteri adayı her eklendiğinde bildirimleri otomatikleştirmek için [Microsoft Flow](https://docs.microsoft.com/flow/) kullanabilirsiniz. Hesabınız yoksa [ücretsiz bir hesap için kaydolabilirsiniz](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Müşteri adayı bildirimi örneği

Bu örneği, bir Azure tablosuna yeni bir müşteri adayı eklendiğinde otomatik olarak bir e-posta bildirimi gönderen basit bir akış oluşturmak için kılavuz olarak kullanın. Bu örnek, tablo depolaması güncelleştirilirse her saat için müşteri adayı bilgilerini göndermek üzere bir yinelenme belirler.

1. Microsoft Flow hesabınızda oturum açın.
2. Sol gezinti çubuğunda **Akışlarım**' ı seçin.
3. Üst gezinti çubuğunda **+ Yeni**' yi seçin.  
4. Açılan listede **+ zamanlandı** ' i seçin.

   ![Akışlarım * * + zamanlanan-boş * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  *Zamanlanan akış oluştur* penceresinde, Aralık için "1" aralığı ve sıklık için "saat *" seçeneğini belirleyin* . Ayrıca, isterseniz akışa bir ad verin. **Oluştur**'u seçin.

    >[!Note]
    >Bu örnekte 1 saatlik bir Aralık kullanılabilse de, iş gereksinimleriniz için en iyi aralığı ve sıklığı seçebilirsiniz.

    ![Zamanlanan bir akış oluşturun.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. **+ Yeni adım**' ı seçin.
7. *Eylem Seç* penceresinde "geçmiş zamanı al" ifadesini arayın ve ardından Eylemler altında **geçmiş zamanı al** ' ı seçin.

   ![Bir eylem seçin.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. **Son zaman al** penceresinde, **aralığı** 1 olarak ayarlayın. **Zaman birimi** açılan listesinden **saat**' i seçin.

    >[!Important]
    >Bu Aralık ve zaman biriminin 5. adımda yinelenme için yapılandırdığınız Aralık ve sıklık ile eşleştiğinden emin olun.

    ![Geçmiş zaman aralığını ayarla](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Her adımın doğru yapılandırıldığını doğrulamak için akışınızı dilediğiniz zaman kontrol edebilirsiniz. Akışınızı denetlemek için akış menü çubuğundan **Flow denetleyicisi** ' ni seçin.

Sonraki adımlarda, Azure tablonuza bağlanırsınız ve yeni müşteri adaylarını işlemek için işleme mantığını ayarlamanız gerekir.

9. Geçmiş zamanı al adımından sonra **+ yeni adım**' ı seçin ve ardından *Eylem Seç* penceresinde "varlıkları al" ifadesini arayın.
10. **Eylemler**altında **varlıkları al (Azure Tablo Depolama)** seçeneğini belirleyin.
11. **Azure Tablo Depolaması** penceresinde, aşağıdaki alanlar için bilgi sağlayın ve **Oluştur**' u seçin:

    * *Bağlantı adı* -bu akış ve Azure tablosu arasında oluşturduğunuz bağlantı için anlamlı bir ad sağlayın.
    * *Depolama hesabı adı* -Azure tablonuz için depolama hesabının adını belirtin. Bunu, depolama hesabının **erişim anahtarları** sayfasında bulabilirsiniz.
    * *Paylaşılan depolama anahtarı* -Azure tablonuz için mağaza hesabınızın anahtar değerini sağlayın. Bunu, depolama hesabının **erişim anahtarları** sayfasında bulabilirsiniz.

        ![Azure Tablo depolaması.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Oluştur 'a tıkladıktan sonra *varlıkları al* penceresi görüntülenir. Burada **Gelişmiş seçenekleri göster** ' i seçin ve aşağıdaki alanlar için bilgi sağlayın:

       * *Tablo* -Azure Tablo depolama alanınızı seçin (Azure tablosunun nasıl yapılandırılacağı konusunda adım 6 ' dan itibaren yönergeler). Sonraki ekran yakalama, bu örnek için "marketplaceliderleri" tablosu seçildiğinde istemi gösterir.

            ![Azure tablosu varlıkları al.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Filtre sorgusu* -bu alanı seçin ve bu işlevi alana yapıştırın: `Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure Tablo Get varlıkları-filtre Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Azure tablosu bağlantısını ayarlamayı tamamladığınıza göre, yeni müşteri adayları için Azure tablosunu taramak üzere koşul eklemek üzere **yeni adım** ' ı seçin. 

13. **Eylem seçin** penceresinde **Eylemler**' i seçin ve ardından **koşul denetimini**seçin.

    ![Azure tablosu-bir eylem seçin.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. **Koşul** penceresinde, **bir değer seçin** alanını seçin ve ardından açılan pencerede **ifade** ' ı seçin.

15. `length(body('Get_entities')?['value'])` ***FX*** alanına yapıştırın. Bu işlevi eklemek için **Tamam ' ı** seçin. 

16. Koşulu ayarlamayı tamamlaması için:
    1. Açılan listeden "büyüktür" i seçin.
    2. Değer olarak 0 girin

        ![Azure Tablo koşulu.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

Sonraki birkaç adımda, koşulun sonucuna göre gerçekleştirilecek eylemi ayarlayacaksınız.

* Koşul **Hayır**olarak çözümlenirse, hiçbir şey yapmayın.
* Koşul **Evet ise**, bir e-posta göndermek için Office 365 hesabınızı bağlayan bir eylem tetikleyin. 

17. **Evet ise** **Eylem Ekle** ' yi seçin.

    ![Azure Tablo-koşulu, * * evet * *.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. **E-posta gönder (Office 365 Outlook)** seçeneğini belirleyin.

    ![Azure Tablo-koşul, * * Evet * *, e-posta gönder.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Farklı bir e-posta sağlayıcısı araması kullanmak istiyorsanız, bunun yerine eylem olarak bir e-posta bildirimi (posta) Gönder ' i seçin. Yönergeler Office 365 Outlook kullanarak nasıl yapılandırılacağını gösterir, ancak yönergeler farklı bir e-posta sağlayıcısına benzer.

19. **Office 365 Outlook** penceresinde, aşağıdaki alanlar için bilgi sağlayın:

    1. Bu bildirimi alacak **herkes için bir** e-posta adresi girin.
    1. **Konu** -e-posta için bir konu girin. Örneğin: yeni müşteri adayları!
    1. **Gövde** -her bir e-postaya eklemek istediğiniz metni ekleyin (isteğe bağlı) ve sonra gövde `body('Get_entities')?['value']`yapıştırın.

    >[!Note]
    >Bu e-postanın gövdesine ek statik veya dinamik veri noktaları ekleyebilirsiniz.

    ![Azure Tablo-koşulu, * * Evet, Office 365 Outlook penceresi.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Akışı kaydetmek için **Kaydet** ' i seçin. Microsoft Flow, akışı otomatik olarak hatalara karşı test eder. Herhangi bir hata yoksa, akışınız kaydedildikten sonra çalışmaya başlar.

Sonraki ekranda yakalama, son akışın nasıl görüneceğine ilişkin bir örnek gösterir.

![Son akışa bir örnek.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Akışınızı yönetin

Flow 'un çalışmaya başladıktan sonra kolayca yönetilmesi kolaydır. Akışınız üzerinde tamamen denetim sahibi olursunuz. Örneğin, bunu durdurabilir, düzenleyebilir, çalıştırma geçmişine bakabilirsiniz ve analiz alabilirsiniz. Sonraki ekran yakalama, bir akışı yönetmek için kullanılabilen seçenekleri gösterir. 

 ![Akış yönetme](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Akış, **devre dışı bırak** seçeneğini kullanarak durduruncaya kadar çalışmaya devam eder.

Hiçbir müşteri adayı e-posta bildirimi almıyorsanız, Azure tablosuna yeni müşteri adayları eklenmemiş demektir. Herhangi bir akış başarısızlığı varsa, sonraki ekran yakalamadaki örnek gibi bir e-posta alırsınız.

 ![Flow hata e-posta bildirimi](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Teklifinizi Azure tablosuna müşteri adayları gönderecek şekilde yapılandırma

Yayımlama portalında teklifiniz için lider yönetimi bilgilerini yapılandırmaya hazırsanız aşağıdaki adımları izleyin:

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.
2. Müşteri adayı Yönetimi bölümünde **Bağlan** ' ı seçin.
3. Bağlantı ayrıntıları açılır penceresinde, **müşteri adayı hedefi**Için **Azure tablosu** ' nu seçin ve önceki adımları izleyerek oluşturduğunuz Azure depolama hesabındaki bağlantı dizesini **depolama hesabı bağlantı dizesine** yapıştırın alanla.
4. **Kaydet**’i seçin. 

>[!Note]
>Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.

Müşteri adayları oluşturulduğunda, Microsoft müşteri adaylarını Azure tablosuna gönderir. Bir akış yapılandırdıysanız, yapılandırdığınız e-posta adresine de bir e-posta gönderilir.

![Müşteri adayı yönetimi](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Lider yönetimi-bağlantı ayrıntıları](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Lider yönetimi-bağlantı ayrıntıları depolama hesabı](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

