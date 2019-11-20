---
title: Tahmine Dayalı Bakım çözüm hızlandırıcısına genel bakış - Azure | Microsoft Docs
description: Bir iş senaryosunda bir hatanın oluşma noktasını tahmin eden Azure IoT tahmine dayalı bakım çözüm hızlandırıcısına genel bakış.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827425"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Tahmine Dayalı Bakım çözüm hızlandırıcısına genel bakış

Tahmine Dayalı Bakım çözümü, arızanın oluşma ihtimali olan anı tahmin eden iş senaryosu için uçtan uca bir çözümdür. Bu çözüm hızlandırıcısını, bakım iyileştirmesi gibi etkinlikler için proaktif olarak kullanabilirsiniz. Çözüm, IoT Hub ve bir [Azure Machine Learning][lnk-machine-learning] çalışma alanı gibi temel Azure IoT Çözüm Hızlandırıcıları hizmetlerini birleştirir. Bu çalışma alanı, bir uçak motorunun Kalan Kullanım Ömrü’nü (RUL) öngörmek için genel bir örnek veri kümesini temel alan bir model içerir. Bu çözüm, kendinize özel iş gereksinimlerinizi karşılayacak bir çözümü planlamanız ve uygulamanız amacıyla sizin için bir başlangıç noktası olarak IoT iş senaryosunu tam olarak uygular.

Tahmine dayalı bakım çözümü hızlandırıcı [kodu GitHub ' da kullanılabilir](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Mantıksal mimari

Aşağıdaki diyagram, çözüm hızlandırıcısının mantıksal bileşenlerinin ana hatlarını vermektedir:

![Mantıksal mimari][img-architecture]

Mavi öğeler, çözüm hızlandırıcısını dağıttığınız bölgede sağlanan Azure hizmetleridir. [Sağlama sayfasında][lnk-azureiotsolutions]Çözüm Hızlandırıcısı ekranları dağıtabileceğiniz bölgelerin listesi.

Yeşil öğe, sanal bir uçak altyapısıdır. [Sanal cihazlar](#simulated-devices) bölümde bu sanal cihazlarla ilgili daha fazla bilgiye ulaşabilirsiniz.

Gri öğeler, *cihaz yönetimi* özelliklerini uygulayan bileşenlerdir. Tahmine Dayalı Bakım çözüm hızlandırıcısının geçerli sürümü, bu kaynakları sağlamaz. Cihaz yönetimi hakkında daha fazla bilgi edinmek için [Uzaktan izleme çözüm hızlandırıcısına][lnk-remote-monitoring]bakın.

## <a name="azure-resources"></a>Azure kaynakları

Azure portalda sağlanan kaynaklarınızı görüntülemek için seçtiğiniz çözüm adına sahip kaynak grubuna gidin.

![Hızlandırıcı kaynakları][img-resource-group]

Çözüm hızlandırıcısını sağladığınızda, Machine Learning çalışma alanına bağlantısı da olan bir e-posta alırsınız. Ayrıca, [Microsoft Azure IoT Çözüm Hızlandırıcıları][lnk-azureiotsolutions] sayfasından Machine Learning çalışma alanına gidebilirsiniz. Çözüm **Hazır** durumda olduğunda bu sayfada bir kutucuk kullanılabilir.

![Machine Learning modeli][img-machine-learning]

## <a name="simulated-devices"></a>Sanal cihazlar

Çözüm hızlandırıcısında, sanal cihaz uçak altyapısıdır. Çözüm, tek bir uçakla eşlenen 2 motorla sağlanır. Her motor dört tür telemetri yayar: Algılayıcı 9, Algılayıcı 11, Algılayıcı 14 ve Algılayıcı 15, Machine Learning modelinin bu motorun RUL değerini hesaplaması için gereken verileri sağlar. Her sanal cihaz IoT Hub'ına şu telemetri iletilerini gönderir:

*Döngü sayısı*. Bir döngüyle iki ila on saat arasında bir süre ile tamamlanan bir uçuş vardır. Uçuş sırasında telemetri verileri yarım saatte bir yakalanır.

*Telemetri*. Altyapı özniteliklerini kaydeden dört algılayıcı vardır. Bu algılayıcılar genel olarak Algılayıcı 9, Algılayıcı 11, Algılayıcı 14 ve Algılayıcı 15 olarak etiketlenir. Bu dört algılayıcı, RUL modelinden yararlı sonuçlar elde etmek için telemetri gönderir. Çözüm hızlandırıcısında kullanılan model, gerçek altyapı algılayıcı verilerinin bulunduğu ortak bir veri kümesinden oluşturulur. Modelin özgün veri kümesinden nasıl oluşturulduğu hakkında daha fazla bilgi için, [Cortana Intelligence Gallery tahmine dayalı bakım şablonu][lnk-cortana-analytics]' na bakın.

Sanal cihazlar, çözümde IoT hub'ı tarafından gönderilen aşağıdaki komutları işleyebilir:

| Komut | Açıklama |
| --- | --- |
| StartTelemetry |Benzetim durumunu denetler.<br/>Cihazın telemetri göndermesini başlatır |
| StopTelemetry |Benzetim durumunu denetler.<br/>Cihazın telemetri göndermesini durdurur |

IoT hub'ı cihaz komut bildirim sağlar.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics işi

**İş: Telemetri**, gelen cihaz telemetrisi akışını iki durumu kullanarak çalıştırır:

* İlki, cihazlardan tüm telemetriyi seçer ve bu verileri blob depolamaya gönderir. Buradan, Web uygulamasında görselleştirilir.
* İkinciyse iki dakikalık kayan pencere üzerinde ortalama algılayıcı değerlerini ölçer ve bu verileri Olay hub'ı aracılığıyla **olay işlemcisi**’ne gönderir.

## <a name="event-processor"></a>Olay işlemcisi
**Olay işleyicisi konağı** bir Azure Web İşi’nde çalıştırır. **Olay işlemcisi**, tamamlanan bir döngü için ortalama algılayıcı değerlerini alır. Daha sonra bu değerleri bir altyapının RUL 'sini hesaplayan eğitilen bir modele geçirir. Bir API, çözümün parçası olan bir Machine Learning çalışma alanında modele erişim sağlar.

## <a name="machine-learning"></a>Machine Learning
Machine Learning bileşeni gerçek uçak motorlarından toplanan verilerden türetilmiş bir model kullanır. [Azureiotsolutions.com][lnk-azureiotsolutions] sayfasındaki çözümünüzün kutucuğundaki Machine Learning çalışma alanına gidebilirsiniz. Çözüm **Hazır** durumda olduğunda kutucuk kullanılabilir.

Machine Learning modeli, IoT Çözüm Hızlandırıcısı Hizmetleri aracılığıyla toplanan telemetriyle çalışmayı gösteren bir şablon olarak kullanılabilir. Microsoft, genel olarak kullanılabilir verileri<sup>\[1\]</sup>ve modelin nasıl kullanılacağına ilişkin adım adım yönergeler temel alınarak bir uçak altyapısının [regresyon modelini][lnk_regression_model] oluşturdu.

Azure IoT Tahmine Dayalı Bakım çözümü, bu şablondan oluşturulan regresyon modelini kullanır. Model Azure aboneliğinize dağıtılır ve otomatik olarak oluşturulan bir API aracılığıyla kullanılabilir hale getirilir. Çözüm, 4 (Toplam 100 toplam) altyapıya yönelik test verilerinin bir alt kümesini ve 4 (Toplam 21 toplam) algılayıcı veri akışı içerir. Bu veriler, eğitilmiş modelden doğru bir sonuç elde etmek için yeterlidir.

*\[1\] A. Saxena ve K. Goebel (2008). "Turbofan motor düşme simülasyonu veri kümesi", NASA Ames Prognostics veri deposu (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett alanı, CA*

## <a name="next-steps"></a>Sonraki adımlar
Tahmine Dayalı Bakım çözüm hızlandırıcısının temel bileşenlerini gördüğünüze göre bunları özelleştirmek isteyebilirsiniz.

IoT çözüm hızlandırıcılarına ait diğer özelliklerden bazılarını da inceleyebilirsiniz:

* [IoT Çözüm Hızlandırıcıları için sık sorulan sorular][lnk-faq]
* [Sıfırdan IoT güvenliği][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
