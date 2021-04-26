---
title: Verileri veya zamanlamayı temel alarak Azure kaynaklarını otomatik ölçeklendirme
description: Ölçüm verilerini ve bir zamanlamayı kullanarak bir uygulama hizmeti planı için otomatik ölçeklendirme ayarı oluşturma
author: anirudhcavale
services: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.subservice: autoscale
ms.openlocfilehash: b5bcc68ddf26dd1fb8421224307c5aa20179afab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100628295"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Azure kaynakları için performans verilerini veya bir zamanlamayı temel alan bir Otomatik Ölçeklendirme Ayarı oluşturma

Otomatik ölçeklendirme ayarları, önceden ayarlı koşullara göre hizmet örneği eklemenize/kaldırmanıza imkan tanır. Bu ayarlar portal aracılığıyla oluşturulabilir. Bu yöntem, bir otomatik ölçeklendirme ayarı oluşturup yapılandırmak için tarayıcı tabanlı bir kullanıcı arabirimi sağlar. 

Bu öğreticide şunları yapacaksınız: 
> [!div class="checklist"]
> * Web Uygulaması ve App Service Planı oluşturma
> * Bir Web Uygulamasının aldığı istek sayısına bağlı olarak ölçek daraltma ve genişletme için otomatik ölçeklendirme kuralları yapılandırma
> * Bir ölçek genişletme eylemi tetikleyin ve örnek sayısının artışını izleyin
> * Bir ölçek daraltma eylemi tetikleyin ve örnek sayısının azalışını izleyin
> * Kaynaklarınızı temizleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="log-in-to-the-azure-portal"></a>Azure portalında oturum açma

[Azure Portal](https://portal.azure.com/)oturum açın.

## <a name="create-a-web-app-and-app-service-plan"></a>Web Uygulaması ve App Service Planı oluşturma
1. Soldaki gezinti bölmesinden **Kaynak oluştur** seçeneğine tıklayın.
2. *Web uygulaması* öğesini arayıp seçin ve **Oluştur**' a tıklayın.
3. *MyTestScaleWebApp* gibi bir uygulama adı seçin. Yeni bir kaynak grubu * myResourceGroup ' oluşturun veya bunu seçtiğiniz bir kaynak grubuna yerleştirin.

Birkaç dakika içinde kaynaklarınız sağlanmalıdır. Bu öğreticinin geri kalanında Web Uygulamasını ve ilgili App Service Planını kullanın.

   ![Portalda yeni bir uygulama hizmeti oluşturma](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Otomatik Ölçeklendirme ayarlarına gidin
1. Soldaki gezinti bölmesinden **İzleyici** seçeneğini belirleyin. Sayfa yüklendikten sonra **Otomatik ölçeklendirme** sekmesini seçin.
2. Burada, aboneliğinizde yer alan ve otomatik ölçeklendirmeyi destekleyen kaynaklar listelenir. Öğreticide daha önce oluşturulan App Service Planı’nı belirleyin ve buna tıklayın.

    ![Ekran görüntüsü, Izleyiciyle Azure portal gösterir, sonra otomatik ölçeklendirme seçilidir.](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Otomatik ölçeklendirme ayarında **Otomatik ölçeklendirmeyi etkinleştir** düğmesine tıklayın.

Sonraki birkaç adım, otomatik ölçeklendirme ekranını aşağıdaki resimdeki gibi görünecek şekilde doldurmanıza yardımcı olur:

   ![Otomatik ölçeklendirme ayarını kaydedin](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="configure-default-profile"></a>Varsayılan profili yapılandırın
1. Otomatik ölçeklendirme ayarı için bir **ad** girin.
2. Varsayılan profilde **Ölçek modu**’nun 'Belirli bir örnek sayısına ölçeklendirin' olarak ayarlandığından emin olun.
3. Örnek sayısını **1** olarak ayarlayın. Bu ayar, etkin veya geçerli olan başka bir profil yoksa varsayılan profilin örnek sayısını 1’e döndürdüğünden emin olur.

   ![Ekran görüntüsü, ayar için girilen bir ada sahip otomatik ölçeklendirme ayarı sayfasını gösterir.](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Yinelenme profili oluştur

1. Varsayılan profilin altındaki **Ölçek koşulu Ekle** bağlantısına tıklayın.

2. Bu profilin **Adını** 'Pazartesi-Cuma profili' olacak şekilde düzenleyin.

3. **Ölçek modunun** ' bir ölçüme göre ölçeklendirin ' olarak ayarlandığından emin olun.

4. **Örnek limitleri** için **En az** değerini '1', **En fazla** değerini '2' ve **Varsayılan** değeri '1' olarak ayarlayın. Bu ayar, hizmet planının 1’den az ya da 2’den çok örneğe sahip olacak şekilde otomatik olarak ölçeklendirilmesini engeller. Profilde karar vermek için yeterli veri yoksa varsayılan örnek sayısını (bu durumda 1) kullanır.

5. **Zamanlama** için ‘Belirli günlerde yinele’yi seçin.

6. Profili Pazartesi’den Cuma’ya kadar 09.00 PST ile 18.00 PST arası yinelenecek şekilde ayarlayın. Bu ayar, profilin yalnızca Pazartesi’den Cuma’ya kadar 09.00 ile 18.00 arası etkin ve geçerli olmasını sağlar. Geriye kalan tüm zamanlarda otomatik ölçeklendirme ayarının kullandığı profil ‘Varsayılan’ profildir.

## <a name="create-a-scale-out-rule"></a>Ölçek genişletme kuralı oluşturma

1. ‘Pazartesi-Cuma profili’nde.

2. **Kural Ekle** bağlantısına tıklayın.

3. **Ölçüm kaynağı**’nı ‘diğer kaynaklar’ olarak ayarlayın. **Kaynak türü**’nü ‘App Services’, **Kaynak**’ı bu öğreticide daha önce oluşturulan Web Uygulaması olarak ayarlayın.

4. **Zaman toplama**’yı ‘Toplam’, the **Ölçüm adı**’nı 'İstekler' ve **Zaman dilimi istatistiği**’ni ‘Toplam’ olarak ayarlayın.

5. **İşleç**’i ‘Büyüktür’, **Eşik**’i ‘10’ ve **Süre**’yi ‘5’ dakika olarak ayarlayın.

6. **İşlemi** ' sayıyı şu kadar Artır ', **örnek sayısı** ' 1 ' ve **soğuk aşağı doğru** ' 5 ' dakika olarak seçin.

7. **Ekle** düğmesine tıklayın.

Bu kural, Web Uygulamanız 5 dakika veya daha kısa bir sürede 10’dan fazla istek alırsa yükün işlenebilmesi için App Service Planınıza ek bir örnek eklenmesini sağlar.

   ![Ölçek genişletme kuralı oluşturma](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Ölçek daraltma kuralı oluşturma
Her zaman ölçek genişletme kuralına eşlik eden bir ölçek daraltma kuralınızın olması önerilir. Her ikisi de sahip olmanız, kaynaklarınızın aşırı sağlanmasını önler. Aşırı sağlama, çalışmakta olan örnek sayısının geçerli yükün işlenmesi için gerekenden fazla olduğu anlamına gelir. 

1. ‘Pazartesi-Cuma profili’nde.

2. **Kural Ekle** bağlantısına tıklayın.

3. **Ölçüm kaynağı**’nı ‘diğer kaynaklar’ olarak ayarlayın. **Kaynak türü**’nü ‘App Services’, **Kaynak**’ı bu öğreticide daha önce oluşturulan Web Uygulaması olarak ayarlayın.

4. **Zaman toplamasını** ' Toplam ', **ölçüm adı** ' nı ' istekler ' ve **zaman dilimi istatistiği** ' ni ' ortalama ' olarak ayarlayın.

5. **İşleç**’i ‘Küçüktür’, **Eşik**’i ‘5’ ve **Süre**’yi ‘5’ dakika olarak ayarlayın.

6. **İşlem**’i ‘Sayıyı şu kadar azalt’, **Örnek sayısı**’nı ‘1’ ve **Bekleme süresi**’ni ‘5’ dakika olarak seçin.

7. **Ekle** düğmesine tıklayın.

    ![Ölçek daraltma kuralı oluşturma](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. Otomatik ölçeklendirme ayarını **kaydedin**.

    ![Otomatik ölçeklendirme ayarını kaydedin](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Ölçek genişletme eylemi tetikleme
Az önce oluşturulan otomatik ölçeklendirme ayarındaki ölçek genişletme koşulunun tetiklenmesi için Web Uygulamasının 5 dakikadan kısa bir sürede 10’dan fazla istek alması gerekir.

1. Bir tarayıcı penceresi açın ve bu öğreticide daha önce oluşturulan Web Uygulamasına gidin. Web Uygulamanızın URL’sini Azure Portal’da Web Uygulaması kaynağınıza gidip ‘Genel Bakış’ sekmesindeki **Gözat** düğmesine tıklayarak bulabilirsiniz.

2. Sayfayı hızlı bir şekilde 10’dan fazla kez yeniden yükleyin.

3. Soldaki gezinti bölmesinden **İzleyici** seçeneğini belirleyin. Sayfa yüklenince **Otomatik Ölçeklendirme** sekmesini seçin.

4. Listeden bu öğretici boyunca kullanılan App Service Planını seçin.

5. Otomatik ölçeklendirme ayarında **çalıştırma geçmişi** sekmesine tıklayın.

6. App Service Planının zaman içindeki örnek sayısını yansıtan bir grafik görürsünüz.

7. Birkaç dakika içinde örnek sayısı 1’den 2’ye yükselmelidir.

8. Grafiğin altında, bu otomatik ölçeklendirme ayarı tarafından gerçekleştirilen her ölçeklendirme eylemine ait etkinlik günlüğü girdilerini görürsünüz.

## <a name="trigger-scale-in-action"></a>Ölçek daraltma eylemi tetikleme
Otomatik ölçeklendirme ayarındaki ölçek daraltma koşulu, 10 dakikalık bir süre içinde Web Uygulamasına yönelik 5’ten az istek olursa tetiklenir. 

1. Web Uygulamanıza istek gönderilmediğinden emin olun.

2. Azure Portal’ı yükleyin.

3. Soldaki gezinti bölmesinden **İzleyici** seçeneğini belirleyin. Sayfa yüklenince **Otomatik Ölçeklendirme** sekmesini seçin.

4. Listeden bu öğretici boyunca kullanılan App Service Planını seçin.

5. Otomatik ölçeklendirme ayarında **çalıştırma geçmişi** sekmesine tıklayın.

6. App Service Planının zaman içindeki örnek sayısını yansıtan bir grafik görürsünüz.

7. Birkaç dakika içinde örnek sayısı 2’den 1’e düşmelidir. bu işlem en az 100 dakika sürer.  

8. Grafiğin altında, bu otomatik ölçeklendirme ayarı tarafından gerçekleştirilen her ölçeklendirme eylemi için ilgili etkinlik günlüğü girdileri yer alır.

    ![Ölçek daraltma eylemlerini görüntüleme](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

1. Azure portalında sol taraftaki menüden **Tüm kaynaklar**’ı ve ardından bu öğreticide oluşturulan Web Uygulamasını seçin.

2. Kaynak sayfanızda, **Sil**’e tıklayın, metin kutusuna **yes** yazarak silme işlemini onaylayın ve sonra **Sil**’e tıklayın.

3. Ardından App Service Planı kaynağını seçip **Sil**’e tıklayın.

4. Metin kutusuna **yes** yazarak silme işlemini onaylayın ve sonra **Sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:  
> [!div class="checklist"]
> * Bir Web Uygulaması ve App Service Planı oluşturdunuz
> * Web Uygulamasının aldığı istek sayısına bağlı olarak ölçek daraltma ve genişletme için otomatik ölçeklendirme kuralları yapılandırdınız
> * Bir ölçek genişletme eylemi tetiklediniz ve örnek sayısının artışını izlediniz
> * Bir ölçek daraltma eylemi tetiklediniz ve örnek sayısının düşüşünü izlediniz
> * Kaynaklarınızı temizlediniz


Otomatik ölçeklendirme ayarları hakkında daha fazla bilgi edinmek için [otomatik ölçeklendirmeye genel bakış](../autoscale/autoscale-overview.md) konusuna geçin.

> [!div class="nextstepaction"]
> [İzleme verilerinizi arşivleme](../essentials/platform-logs-overview.md)

