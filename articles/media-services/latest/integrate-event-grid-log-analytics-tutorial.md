---
title: Azure 'da Media Services olaylarını depolama Log Analytics
description: Azure Media Services olaylarını Azure Log Analytics depolama hakkında bilgi edinin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: a10625f6f5bec18528e56bf8f607f5c1b25aa6e3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282393"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Öğretici: Azure 'da Azure Media Services olaylarını depolama Log Analytics

## <a name="azure-media-services-events"></a>Azure Media Services olaylar

Azure Media Services v3, olayları [Azure Event Grid](monitoring/media-services-event-schemas.md)yayar. Olaylara birçok şekilde abone olabilir ve bunları veri depolarında saklayabilirsiniz. Bu öğreticide, bir [günlük uygulama akışı](https://azure.microsoft.com/services/logic-apps/)kullanarak Media Services olaylarına abone olursunuz. Mantıksal uygulama her olay için tetiklenecek ve olayın gövdesini Azure Log Analytics depolayacak. Olaylar Azure Log Analytics ' de olduktan sonra, bu etkinliklerde bir pano oluşturmak, izlemek ve uyarı oluşturmak için diğer Azure hizmetlerini kullanabilirsiniz, ancak bu öğreticide bunu kapsamayacağız.

> [!NOTE]
> Şirket içi Kodlayıcınız olarak FFmpeg 'yi kullanmayı zaten biliyorsanız faydalı olur.  Aksi takdirde, bu sorun devam etmez. Video akışı için komut satırı ve yönergeler aşağıda verilmiştir.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Kod mantıksal uygulama akışı oluşturma
> * Azure Media Services olayına abone olma konuları
> * Olayları ayrıştırma ve Azure Log Analytics depolama
> * Olayları Azure Log Analytics sorgula

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

> * [Azure aboneliği](setup-azure-subscription-how-to.md)
> * [Media Services](account-create-how-to.md) hesabı ve kaynak grubu.
> * IŞLETIM sisteminiz için [FFmpeg](https://ffmpeg.org/download.html) yüklemesi.
> * [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md) çalışma alanı

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Logic App ile Media Services olayına abone olma

1. Azure portal, daha önce yapmadıysanız, bir [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md) çalışma alanı oluşturun. Çalışma alanı KIMLIĞI ve anahtarlardan biri gerekir, bu nedenle bu tarayıcı penceresini açık tutun. Ardından, portalı başka bir sekmede veya pencerede açın.

1. Azure Media Services hesabınıza gidin ve **Olaylar**' ı seçin. Bu, Azure Media Services olaylarına abone olmak için tüm yöntemleri gösterir.
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services portalı](media/tutorial-events-log-analytics/select-events-01a.png)

1. Bir mantıksal uygulama oluşturmak için **Logic Apps simgesini** seçin. Bu işlem, olayları yakalamak ve Log Analytics göndermek için akış oluşturabileceğiniz mantıksal uygulama Tasarımcısını açar. 
    > [!div class="mx-imgBorder"]
    > ![Mantıksal uygulama oluşturma](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. **+ Simgesini** seçin, kullanmak istediğiniz kiracıyı seçin ve oturum aç ' ı seçin. Bir Microsoft oturum açma istemi görürsünüz.
    > [!div class="mx-imgBorder"]
    > ![](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
 ![ Kiracıyı seçmek Azure Event Grid bağlanın](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. Media Services olaylarına abone olmak için **devam** ' ı seçin.
    > [!div class="mx-imgBorder"]
    > ![Azure Event Grid bağlı](media/tutorial-events-log-analytics/select-continue-04.png)

1. **Kaynak türü** listesinde "Microsoft. Media. mediaservices" öğesini bulun.
    > [!div class="mx-imgBorder"]
    >![Kaynak olaylarını Azure Media Services](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. **Olay türü öğesini** seçin. Yayar Azure Media Services tüm olayların bir listesi olacaktır. İzlemek istediğiniz olayları seçebilirsiniz. Birden çok olay türü ekleyebilirsiniz. (Daha sonra, her olay türünü ayrı bir Log Analytics günlüğünde depolamak ve olay türü adını dinamik olarak Log Analytics günlük adına yaymak için mantıksal uygulama akışında küçük bir değişiklik yaparsınız.)
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services olay türü](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. **Farklı Kaydet**’i seçin.

1. Mantıksal uygulamanıza bir ad verin.  Kaynak grubu varsayılan olarak seçilidir. Diğer ayarları olduğu gibi bırakın, sonra **Oluştur**' u seçin.  Azure giriş ekranına döndürülecektir.
    > [!div class="mx-imgBorder"]
    > ![Mantıksal uygulama adlandırma arabirimi](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Eylem oluşturma

Etkinliğe abone olduğunuza göre artık bir eylem oluşturun.

1. Portal giriş ekranına geri döndüğünüzde, uygulama adı için tüm kaynakları arayarak yeni oluşturduğunuz mantıksal uygulamaya geri gidin.

1. Uygulamanızı seçin, sonra **mantıksal uygulama Tasarımcısı**' nı seçin. Tasarımcı bölmesi açılır.

1. **+ Yeni adım**' ı seçin.

1. Olayları Azure Log Analytics hizmetine göndermek istediğinizden sonra, "Azure Log Analytics" araması yapın ve "Azure Log Analytics veri toplayıcısı" nı seçin.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics Veri Toplayıcı](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Log Analytics çalışma alanına bağlanmak için çalışma alanı KIMLIĞI ve bir aracı anahtarı gerekir. Yeni bir sekmede veya pencerede Azure portal açın, Bu öğreticinin başlangıcından önce oluşturduğunuz Log Analytics çalışma alanına gidin.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics çalışma alanı KIMLIĞI](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. Sol taraftaki menüde **aracılar yönetimini** bulun ve seçin. Bu, size oluşturulan aracı anahtarlarını gösterir.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics Agents yönetimi](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. *Çalışma alanı kimliğini* kopyalayın.
    > [!div class="mx-imgBorder"]
    > ![Çalışma alanı KIMLIĞINI Kopyala](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. Diğer tarayıcı sekmesinde veya penceresinde, Azure Log Analytics veri toplayıcısı altında **veri Gönder**' i seçin, bağlantıya bir ad verin ve **çalışma alanı kimliği ALANıNA çalışma** alanı *kimliğini* yapıştırın.

1. Çalışma alanı tarayıcısı sekmesine veya penceresine dönün ve *çalışma alanı anahtarını* kopyalayın.
    > [!div class="mx-imgBorder"]
    > ![Aracı yönetimi birincil anahtarı](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. Diğer tarayıcı sekmesinde veya *penceresinde çalışma alanı* anahtarını **çalışma alanı anahtarı** alanına yapıştırın.

1. **Oluştur**’u seçin. Artık JSON istek gövdesini ve özel günlük adını oluşturacaksınız.

1. **Json istek gövdesi** alanını seçin.  **Dinamik Içerik ekleme** bağlantısı görüntülenir.

1. **Dinamik Içerik Ekle** ' yi seçin ve **Konu**' ı seçin.

1. **Özel günlük adı** için aynısını yapın.
    > [!div class="mx-imgBorder"]
    > ![Konu seçildi](media/tutorial-events-log-analytics/topic-selected.png)

1. Mantıksal uygulamanın **kod görünümünü** seçin. Giriş ve Log-Type satırları ' na bakın.
    > [!div class="mx-imgBorder"]
    > ![İki satırın kod görünümü](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. `body`Değerini `"@triggerBody()?['topic']"` olarak değiştirin `"@{triggerBody()}"` . Bu, iletinin tamamını Log Analytics olarak ayrıştırmaya yöneliktir.

1. Öğesini `Log-Type` olarak değiştirin `"@triggerBody()?['topic']"` `"@replace(triggerBody()?['eventType'],'.','')"` . (Bu, Log Analytics günlük adlarında izin verilmediğinden "." yerini alır.)
    > [!div class="mx-imgBorder"]
    > ![Değişiklikten sonra mantıksal uygulama JSON 'ı](media/tutorial-events-log-analytics/changed-lines.png)

1. **Kaydet**’i seçin.

1. Doğrulamak için **mantıksal uygulama Tasarımcısı**' nı seçin.
    > [!div class="mx-imgBorder"]
    > ![Gövde ve Işlev adımlarını doğrulama](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Kaynak grubundaki tüm kaynakları incelediğinizde, biri olaylar ve diğeri Log Analytics olarak listelenen bir mantıksal uygulama ve iki mantıksal uygulama API Bağlayıcısı vardır. Event Grid sistem konuları hakkında daha fazla bilgi için [Event Grid sistem konuları](../../event-grid/system-topics.md)makalesini okuyun.
    > [!div class="mx-imgBorder"]
    > ![Kaynak grubundaki tüm yeni kaynakları görüntüle](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Test

Gerçekten nasıl çalıştığını test etmek için Azure Media Services ' de canlı bir olay oluşturun. Bir RTMP canlı olayı oluşturun ve bir. mp4 örnek dosyasına dayalı bir "canlı" akış göndermek için FFmpeg kullanın. Olay oluşturulduktan sonra, RTMP alma URL 'sini alın.

1. Media Services hesabınızdan **canlı akış**' ı seçin.
    > [!div class="mx-imgBorder"]
    > ![Canlı Azure Media Services olayı oluşturma](media/tutorial-events-log-analytics/live-event.png)

1. **Canlı etkinlik Ekle**' yi seçin.

1. **Canlı olay adı** alanına bir ad girin. ( **Açıklama** alanı isteğe bağlıdır.)

1. **Standart** bulut kodlamasını seçin.

1. Kodlama ön ayarı için **varsayılan 720p** ' yi seçin.

1. **RTMP** giriş protokolünü seçin.

1. Kalıcı giriş URL 'SI için **Evet** ' i seçin.

1. Olay oluşturulduğunda olayı başlatmak için **Evet** ' i seçin.

    > [!WARNING]
    > Faturalandırma, Evet ' i seçerseniz başlar.  FFmpeg ile akışa *başlamadan önce* akışın başlamasını beklemek istiyorsanız **Hayır** ' ı seçin ve ardından canlı olaylarınızı başlatıp unutmayın.

    > [!div class="mx-imgBorder"]
    > ![Canlı olay ayarları](media/tutorial-events-log-analytics/live-event-settings.png)

1. **İçerik/dosya... onay kutusunu kullanma haklarım ' ı** seçin.

1. **Gözden geçir ve oluştur**’u seçin.

1. Ayarlarınızı gözden geçirin ve **Oluştur**' u seçin.  Canlı olay listesi görüntülenir ve canlı olay alma URL 'SI gösterilir.

1. **Inest URL** 'sini panonuza kopyalayın.

1. Üretici görünümünü görmek için listede **canlı olayını** seçin.

### <a name="stream-with-ffmpeg-cli"></a>FFmpeg CLı ile akış

1. Aşağıdaki komut satırını kullanın.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. `<ingestURL>`Panonuza kopyaladığınız alma URL 'siyle değiştirin.
1. `<localpathtovideo>`FFmpeg 'den akışa almak istediğiniz dosyanın yerel yolunu değiştirin.
1. Sonuna benzersiz bir ad ekleyin, örneğin, `mystream` .
1. Komut satırını, test kaynak dosyanızı ve diğer sistem değişkenlerini yansıtacak şekilde ayarlayın.
1. Komutu çalıştırın. Birkaç saniye sonra, "üretici görünümü" oynatıcı akış başlatmalıdır. (Video otomatik olarak gösterilmezse oynatıcıyı yenileyin.)

    > [!div class="mx-imgBorder"]
    > ![Üretici önizleme yürütücüsünde doğru video alma işlemi doğrulama](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Olayları doğrulama

Canlı akışla Azure Media Services mantıksal uygulama akışını tetikleyen çeşitli olayları yayın. Doğrulamak için, mantıksal uygulamaya gidin ve Media Services olaylar tarafından tetiklenen Tetikleyiciler olup olmadığını saptayın.

1. Mantıksal uygulamaya genel bakış sayfasına gidin, başarıyla tamamlanan "çalıştırma geçmişi" listesini görmeniz gerekir.
    > [!div class="mx-imgBorder"]
    > ![Mantıksal uygulamada başarılı iş yürütmesini doğrulama](media/tutorial-events-log-analytics/run-history.png)

1. Başarılı bir iş seçin. Çalışma zamanı sırasında işin ayrıntıları gösterilir.
1. Genişletmek için **veri Gönder** ' i seçin. Bu durumda `MicrosoftMediaLiveEventEncoderConnected` olay, ayrıştırılmış gövdenin yanı sıra yakalandığını gösterir. Bu, Azure Log Analytics çalışma alanına gönderilen şeydir.
    > [!div class="mx-imgBorder"]
    > ![Bkz. veri gönderme](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>Günlükleri doğrulama

1. Daha önce oluşturduğunuz Log Analytics çalışma alanına gidin.

1. **Günlükleri** seçin.
1. Örnek sorgular açılan penceresini kapatın.
1. Özel bir Günlükler listesi görüntülenir. Genişletmek için aşağı oku seçin. Olay adını görürsünüz `MicrosoftMediaLiveEventEncoderConnected` .
1. Genişletilecek olay adını seçin.
1. "Göz" simgesini seçtiğinizde, sorgu sonucunun önizlemesi gösterilir.
1. **Sorgu Düzenleyicisi 'Nde göster** ' i seçin ve ardından **TimeGenerated UTC** listesinin altındaki öğeyi seçerek genişletin ve ham verileri görüntüleyin.

![Log Analytics ayrıntılı olay çıktısını görün](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Kaynakları silme

Bu öğreticide oluşturduğunuz kaynakları kullanmaya devam etmek istemiyorsanız, kaynak grubundaki tüm kaynakları sildiğinizden emin olun, aksi takdirde ücretlendirilmeye devam edersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Farklı sorgular oluşturabilir ve bunları kaydedebilirsiniz. Bunlar [Azure panosuna](../../azure-monitor/visualize/tutorial-logs-dashboards.md)eklenebilir.