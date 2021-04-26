---
title: Mimari IoT Central Digital Distribution Center | Microsoft Docs
description: IoT Central için dijital dağıtım merkezi uygulama şablonu mimarisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.openlocfilehash: f5f1a9a51c4aa68c11f1843424186a727be94621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99834315"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>IoT Central Digital Distribution Center uygulama şablonu mimarisi



İş ortakları ve müşteriler uçtan uca **dijital dağıtım merkezi** çözümleri geliştirmek için aşağıdaki kılavuzdan & uygulama şablonunu kullanabilir.

> [!div class="mx-imgBorder"]
> ![Dijital dağıtım merkezi](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Bir ağ geçidi cihazına telemetri verileri gönderen IoT sensörleri kümesi
2. IoT Central telemetri ve toplu Öngörüler gönderen ağ geçidi cihazları
3. Veriler, işleme için istenen Azure hizmetine yönlendirilir
4. ASA veya Azure Işlevleri gibi Azure Hizmetleri, veri akışlarını yeniden biçimlendirmek ve istenen depolama hesaplarına göndermek için kullanılabilir
5. İşlenen veriler, ML veya Batch analizine dayalı ek Öngörüler için neredeyse gerçek zamanlı eylemler veya soğuk depolama için sık erişimli depolama alanında depolanır. 
6. Logic Apps, son kullanıcı iş uygulamalarında çeşitli iş iş akışlarını desteklemek için kullanılabilir

## <a name="details"></a>Ayrıntılar
Aşağıdaki bölümde, kavramsal mimarinin her bir bölümü özetlenmektedir

## <a name="video-cameras"></a>Video kameraları 
Video kameraları, bu dijital bağlantılı kurumsal ölçekte ekosistemdeki birincil sensörler. Makine öğrenimi ve yapay zeka ile videonun, buluta gönderilmeden önce yapılandırılmış veriler üzerinde açılıp açılmasına imkan tanıyan yapay zeka geliştirmeleri. Görüntüleri yakalamak, kameraya sıkıştırmak ve sonra da bu görüntüleri, ekran Analizi ardışık düzeninde işlemeden önce sıkıştırır ve ardından bu görüntüleri doğrudan Azure IoT Edge göndermek için, IP kameralarını kullanarak resimleri yakalayabilir, kamera üzerinde sıkıştırabilir veya GigE Vision kameralarından yararlanabilirsiniz. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Ağ Geçidi
"Sensörler-algılayıcılar" ve Edge iş yükleri Azure IoT Edge tarafından yerel olarak yönetilir ve kamera akışı analiz işlem hattı tarafından işlenir. Azure IoT Edge adresindeki video analizi işleme işlem hattı, azaltılmış yanıt süresi, düşük bant genişliği tüketimi ve hızlı veri işleme için düşük gecikme süresi gibi birçok avantaj sunar. Daha fazla eylem veya araştırma için buluta yalnızca en önemli meta veriler, Öngörüler veya eylemler gönderilir. 

## <a name="device-management-with-iot-central"></a>IoT Central ile cihaz yönetimi 
Azure IoT Central, IoT cihaz & Azure IoT Edge Ağ Geçidi bağlantısı, yapılandırma ve yönetimi kolaylaştıran bir çözüm geliştirme platformudur. Platform IoT cihaz yönetimi, işlemler ve ilgili geliştirmelerin yükünü ve maliyetlerini önemli ölçüde azaltır. Müşteriler & iş ortakları, dağıtım merkezlerindeki dijital bir geri bildirim döngüsüne ulaşmak için uçtan uca kurumsal çözümler oluşturabilir.

## <a name="business-insights-and-actions-using-data-egress"></a>Veri çıkışı kullanan iş öngörüleri ve eylemleri 
IoT Central platform sürekli veri dışa aktarma (CDE) ve API 'Ler aracılığıyla zengin genişletilebilirlik seçenekleri sağlar. Telemetri veri işleme veya ham telemetri temelinde olan iş öngörüleri genellikle tercih edilen iş kolu uygulamasına aktarılabilir. Makine öğrenimi modelleri oluşturmak, eğitme ve dağıtmak ve daha zengin Öngörüler sağlamak için Web kancası, Service Bus, Olay Hub 'ı veya blob depolama aracılığıyla elde edilebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Dijital dağıtım merkezi şablonunu](./tutorial-iot-central-digital-distribution-center.md) dağıtmayı öğrenin
* [IoT Central perakende şablonları](./overview-iot-central-retail.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinin [IoT Central genel bakış](../core/overview-iot-central.md)
