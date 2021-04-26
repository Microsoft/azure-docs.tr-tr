---
title: E-posta Azure Backup raporları
description: Düzenli raporları e-posta ile almak için otomatikleştirilmiş görevler oluşturma
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: d3ec76fa1a23657578979e65c0e54bb428722244
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561347"
---
# <a name="email-azure-backup-reports"></a>E-posta Azure Backup raporları

Yedekleme raporlarında kullanılabilen **e-posta raporu** özelliğini kullanarak, e-posta yoluyla düzenli raporlar almak için otomatikleştirilmiş görevler oluşturabilirsiniz. Bu özellik, Azure ortamınızda, sağladığınız girişlere göre seçtiğiniz Log Analytics (LA) çalışma alanlarından verileri sorgulayan bir mantıksal uygulama dağıtarak işe yarar. [Logic Apps ve fiyatları hakkında daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Kullanmaya Başlama

Yedekleme raporları aracılığıyla e-posta görevlerini yapılandırmak için aşağıdaki adımları uygulayın:

1.  **Yedekleme merkezi**  >  **yedekleme raporları** ' na gidin ve **e-posta raporu** sekmesine tıklayın.
2.  Aşağıdaki bilgileri belirterek bir görev oluşturun:
    * **Görev ayrıntıları** -oluşturulacak mantıksal uygulamanın adı ve abonelik, kaynak grubu ve oluşturulacağı konum. Mantıksal uygulamanın verileri birden çok abonelik, kaynak grubu ve konumda (rapor filtreleri bölümünde seçildiği şekilde) sorgulayabileceğini, ancak tek bir abonelik, kaynak grubu ve konum bağlamında oluşturulduğunu unutmayın.
    * **Dışarı aktarılacak veriler** -dışarı aktarmak istediğiniz sekme. Her sekme için tek bir görev uygulaması oluşturabilir ya da tüm **Sekmeler** seçeneğini belirleyerek tek bir görev kullanarak tüm sekmeleri e-posta ile gönderebilirsiniz.
    * **E-posta seçenekleri**: e-posta frekansı, alıcı e-posta kimliği ve e-posta konusu.

    ![E-posta sekmesi](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  **Gönder** ve **Onayla**' ya tıkladıktan sonra mantıksal uygulama oluşturulur. Mantıksal uygulama ve ilişkili API bağlantıları, kolay bulunabilirlik için **Usedbybackupreports: true** etiketiyle oluşturulur. Aşağıdaki bölümde açıklandığı gibi mantıksal uygulamanın başarıyla çalışması için bir kerelik bir yetkilendirme adımı gerçekleştirmeniz gerekir.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Azure Izleyici günlüklerine ve Office 365 bağlantı yetkisini verme

Mantıksal uygulama, LA çalışma alanlarını sorgulamak için [azuremonitorlogs](/connectors/azuremonitorlogs/) bağlayıcısını kullanır ve e-posta göndermek Için [Office365 Outlook](/connectors/office365connector/) bağlayıcısını kullanır. Bu iki bağlayıcı için bir kerelik yetkilendirme gerçekleştirmeniz gerekir. 
 
Yetkilendirmeyi gerçekleştirmek için aşağıdaki adımları izleyin:

1.  Azure portal **Logic Apps** gidin.
2.  Oluşturduğunuz mantıksal uygulamanın adını arayın ve kaynağa gidin.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  **API bağlantıları** menü öğesine tıklayın.

    ![API bağlantıları](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Biçiminde iki bağlantı görürsünüz: `<location>-azuremonitorlogs` `<location>-office365` , _eastus-azuremonitorlogs_ ve _eastus-Office365_.
5.  Bu bağlantıların her birine gidin ve **API bağlantısını Düzenle** menü öğesini seçin. Görüntülenen ekranda **Yetkilendir**' i seçin ve yetkilendirme tamamlandıktan sonra bağlantıyı kaydedin.

    ![Bağlantı yetkilendirme](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Mantıksal uygulamanın yetkilendirme sonrasında çalışıp çalışmadığını sınamak için, mantıksal uygulamaya geri gidebilirsiniz, **genel bakış** ' ı açın ve üst bölmedeki **tetikleyiciyi Çalıştır** ' ı seçerek bir e-postanın başarıyla oluşturulup oluşturulmayacağını test edebilirsiniz.

## <a name="contents-of-the-email"></a>E-postanın içeriği

* Portalda gösterilen tüm grafikler ve grafikler e-postada satır içi içerik olarak kullanılabilir.
* Portalda gösterilen kılavuzlar, e-postada *. csv ekleri olarak kullanılabilir.
* E-postada gösterilen veriler, e-posta görevinin oluşturulması sırasında raporda kullanıcı tarafından seçilen tüm rapor düzeyi filtrelerini kullanır.
* **Yedekleme örneği adı**, **ilke adı** vb. gibi sekme düzeyi filtreler uygulanmaz. Bu, **günlük**, **haftalık**, **aylık** ve **yıllık** RP saklama Için filtrelerin uygulandığı **en iyi duruma** getirme sekmesindeki **bekletme iyileştirmeleri** ızgarasının tek istisnası olur.
* Zaman aralığı ve toplama türü (grafikler için), kullanıcıların raporlardaki zaman aralığı seçimine göre değişir. Örneğin, zaman aralığı seçimi son 60 gün ise (haftalık toplama türüne çevriliyor) ve e-posta sıklığı günlük ise, alıcı son 60 günlük süre boyunca alınan verileri haftalık bir düzeyde toplanmış grafiklerle her gün bir e-posta alır.

## <a name="troubleshooting-issues"></a>Sorun giderme

Mantıksal uygulamanın başarılı dağıtımından sonra bile e-posta alıp almadıysanız, yapılandırmanın sorunlarını gidermek için aşağıdaki adımları izleyebilirsiniz:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Senaryo 1: başarılı bir e-posta veya hata e-postası alma

* Bu sorun, Outlook API Bağlayıcısı yetkilendirilmediği için meydana gelebilir. Bağlantıyı yetkilendirmek için yukarıda belirtilen yetkilendirme adımlarını izleyin.

* Bu sorun, mantıksal uygulamayı oluştururken yanlış bir e-posta alıcısı belirttiyseniz de meydana gelebilir. E-posta alıcısının doğru şekilde belirtildiğinden emin olmak için Azure portal mantıksal uygulamaya gidebilir, Logic App Designer ' ı açıp e-posta adımı ' nı seçerek doğru e-posta kimliklerinin kullanılıp kullanılmadığını görebilirsiniz.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Senaryo 2: mantıksal uygulamanın tamamlanmasını yürütme başarısız olduğunu belirten bir hata e-postası alma

Bu sorunu gidermek için:
1.  Azure portal mantıksal uygulamaya gidin.
2.  **Genel bakış** ekranının alt tarafında bir **çalıştırma geçmişi** bölümü görürsünüz. En son çalıştırma üzerinde açabilir ve iş akışındaki hangi adımların başarısız olduğunu görüntüleyebilirsiniz. Olası bazı nedenler:
    * **Azure Izleyici günlükleri bağlayıcısının yetkisi yok**: Bu sorunu onarmak için yukarıda belirtilen yetkilendirme adımlarını izleyin.
    * **La sorgusunda hata**: mantıksal uygulamayı kendi sorgularınız ile ÖZELLEŞTIRDIYSENIZ, La sorgularının hiçbirinde bir hata, mantıksal uygulamanın başarısız olmasına neden olabilir. İlgili adımı seçip sorgunun yanlış çalışmasına neden olan hatayı görüntüleyebilirsiniz.

Sorunlar devam ederse, Microsoft destek 'e başvurun.

## <a name="next-steps"></a>Sonraki adımlar
[Yedekleme raporları hakkında daha fazla bilgi edinin](./configure-reports.md)