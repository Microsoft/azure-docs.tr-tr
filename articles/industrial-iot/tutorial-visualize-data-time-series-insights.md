---
title: Azure Time Series Insights 'da OPC UA verilerini görselleştirin
description: Bu öğreticide, Time Series Insights ile verileri görselleştirmeyi öğreneceksiniz.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787886"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Öğretici: Time Series Insights (TSI) ile verileri görselleştirme

OPC yayımcı modülü OPC UA sunucularına bağlanır ve bu sunuculardan IoT Hub veri yayımlar. Endüstriyel IoT platformundaki telemetri işlemcisi bu olayları işler ve contextulaştırılan örnekleri ve diğer tüketicilere iletir.  

Bu nasıl yapılır kılavuzunda, bu Time Series Insights ortamını kullanarak OPC UA telemetrisini görselleştirme ve çözümleme işlemi gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Tüm Öğreticiler, tamamlama adımlarını özetleyen bir liste içerir
> * Bu madde işareti noktalarının her biri bir Key H2 'e hizalanır
> * Öğreticide bu yeşil onay kutularını kullanın

## <a name="prerequisite"></a>Önkoşul

* Time Series Insights ortamının otomatik olarak oluşturulmasını sağlamak için IIoT platformunu dağıtın
* Veriler IoT Hub yayımlanıyor

## <a name="time-series-insights-explorer"></a>Time Series Insights gezgini

Time Series Insights Gezgini, telemetrinizi görselleştirmek için kullanabileceğiniz bir Web uygulamasıdır. Uygulamanın URL 'sini almak için, `.env` dağıtımın sonucu olarak kaydedilen dosyayı açın.  Değişkende URL 'ye bir tarayıcı açın `PCS_TSI_URL` .  

Time Series Insights Gezginini kullanmadan önce, verileri görselleştirmeye yönelik kullanıcılar için TSI verilerine erişim vermeniz gerekir. Yeni bir dağıtımda hiçbir veri erişim ilkesi varsayılan olarak ayarlanmadığını ve bu nedenle hiçbir kimsenin verileri görmediğini unutmayın. Veri erişim ilkelerinin Azure portal, IIoT 'nin platform tarafından dağıtılan kaynak grubunda dağıtılan Time Series Insights ortamında aşağıdaki gibi ayarlanması gerekir:

   ![Time Series Insights Gezgini 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Veri erişim Ilkelerini seçin:

   ![Time Series Insights Explorer 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Gerekli kullanıcıları ata:

   ![Time Series Insights Explorer 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


TSI Gezgininde, atanmamış zaman serisi örneklerine göz önünde olun. Bir TSI örneği, OPC sunucusunda yayınlanan bir düğümden kaynaklanan belirli bir veri noktası için zaman/değer serisine karşılık gelir. OPC UA veri noktasının sırasıyla altındaki TSI örneği, EndpointId, SubscriptionID ve NodeId tarafından benzersiz şekilde tanımlanır. TSI örnekleri modelleri otomatik olarak algılanır ve IIoT platformu telemetri işlemcisinin Olay Hub 'ından alınan telemetri verilerine göre gezgin 'de görüntülenir.

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

Telemetri verileri, TSI örneğine sağ tıklayıp değeri seçerek grafik içinde görselleştirilir. Grafikte kullanılacak zaman dilimi sağ üst köşeden ayarlanabilir. Birden çok örneğinin değeri aynı zamanda seçilebilir.

Daha fazla bilgi için bkz [. hızlı başlangıç: Azure Time Series Insights önizlemeyi araştırma](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>Yeni model tanımlama ve uygulama

Telemetri örnekleri artık ham biçimde olduğundan, bu, uygun 

TSI modelleriyle ilgili ayrıntılı bilgi için [Azure Time Series Insights önizlemede zaman serisi modeline](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm) bakın

1. 1. adım-gezgin 'in model sekmesinde, alınan telemetri verileri için yeni bir hiyerarşi tanımlayın. Hiyerarşi, kullanıcının TSI örnekleri aracılığıyla daha sezgisel bir gezinti için gereken meta bilgileri eklemesini sağlayan mantıksal ağaç yapısıdır. bir kullanıcı çeşitli TSI örnekleri için örneklendirilmiş olabilecek hiyerarşi şablonları oluşturabilir/silebilir/değiştirebilir.

   ![1. Adım](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. 2. adım-değerler için yeni bir tür tanımlayın. Örneğimizde yalnızca sayısal veri türlerini işleyeceğiz

   ![2. Adım](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. 3. adım-daha önce tanımlanan hiyerarşide kategorilere ayrılması gereken yeni TSI örneğini seçin

   ![3. Adım](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. 4. adım-mantıksal yapıyla eşleşecek şekilde örneklerin özelliklerini-ad, açıklama, veri değeri ve hiyerarşi alanlarını girin 

   ![4. Adım](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. 5. adım-kategorilere ayrılmamış tüm TSI örnekleri için 5. adımı yineleyin

   ![5. Adım](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. 6. adım-TSI Explorer 'ın ana sayfasında, kategorilere ayrılan örnekler hiyerarşisini gözden geçir ve Çözümlenecek veri noktalarının değerlerini seçin

   ![Step6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Time Series Insights Power BI bağlama

Ayrıca Time Series Insights ortamını Power BI bağlayabilirsiniz.  Daha fazla bilgi için bkz. TSI ' ı bağlama ve [Power BI TSİ 'tan verileri görselleştirme](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi) [Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) .


## <a name="next-steps"></a>Sonraki adımlar
Artık TSI 'daki verileri görselleştirmeyi öğrendiğinize göre, endüstriyel IoT GitHub deposuna bakabilirsiniz:

> [!div class="nextstepaction"]
> [IIoT platformu GitHub deposu](https://github.com/Azure/iot-edge-opc-publisher)