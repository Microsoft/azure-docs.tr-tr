---
title: Mimari IoT Central Digital Distribution Center | Microsoft Docs
description: IoT Central için dijital dağıtım merkezi uygulama şablonu mimarisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 478ebde1de5624796ebf1dde5cf89f4e1f9d9104
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890727"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>IoT Central Digital Distribution Center uygulama şablonu mimarisi

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

& Müşteri ortakları, uçtan uca **dijital dağıtım merkezi** çözümlerini geliştirmek için aşağıdaki kılavuzdan yararlanarak uygulama şablonundan yararlanabilir &.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Bir ağ geçidi cihazına telemetri verileri gönderen IoT sensörleri kümesi
2. IoT Central telemetri ve toplu Öngörüler gönderen ağ geçidi cihazları
3. Veriler, işleme için istenen Azure hizmetine yönlendirilir
4. ASA veya Azure Işlevleri gibi Azure Hizmetleri, veri akışlarını yeniden biçimlendirmek ve istenen depolama hesaplarına göndermek için yararlanılabilir olabilir 
5. İşlenen veriler, ML veya Batch analizine dayalı ek öngörü geliştirmeleri için neredeyse gerçek zamanlı eylemler veya soğuk depolama için sık erişimli depolama alanında depolanır. 
6. Logic Apps, son kullanıcı iş uygulamalarında çeşitli iş iş akışlarını desteklemek için kullanılabilir

## <a name="details"></a>Ayrıntılar
Aşağıdaki bölümde, kavramsal mimarinin her bir bölümü özetlenmektedir

## <a name="video-cameras"></a>Video kameraları 
Video kameraları, bu dijital bağlantılı kurumsal ölçekte ekosistemdeki birincil sensörler. Makine öğrenimi ve yapay zeka ile videonun, buluta gönderilmeden önce yapılandırılmış veriler üzerinde açılıp açılmasına imkan tanıyan yapay zeka geliştirmeleri. Görüntüleri yakalamak, kameraya sıkıştırmak ve sonra da bu görüntüleri sensördeki görüntüleri yakalamak ve ardından bu görüntüleri doğrudan Azure IoT Edge göndermek için, IP kameralarını kullanarak resimleri yakalayabilir, kamerayı açabilir veya GigE Vision kameralarını kullanabilirsiniz , daha sonra video analizi ardışık düzeninde işlemeden önce sıkıştırır. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Ağ Geçidi
"Sensörler-algılayıcılar" ve Edge iş yükleri Azure IoT Edge tarafından yerel olarak yönetilir ve kamera akışı analiz işlem hattı tarafından işlenir. Azure IoT Edge adresindeki video analizi işleme işlem hattı, azaltılmış yanıt süresi de dahil olmak üzere çok sayıda avantaj sunar, düşük bant genişliği tüketimi, hızlı veri işleme için düşük gecikme süresine neden olur Daha fazla eylem veya araştırma için buluta yalnızca en önemli meta veriler, Öngörüler veya eylemler gönderilir. 

## <a name="device-management-with-iot-central"></a>IoT Central ile cihaz yönetimi 
Azure IoT Central, IoT cihaz & Azure IoT Edge Ağ Geçidi bağlantısı, yapılandırma ve yönetimi kolaylaştıran bir çözüm geliştirme platformudur. Platform IoT cihaz yönetimi, işlemler ve ilgili geliştirmelerin yükünü ve maliyetlerini önemli ölçüde azaltır. Müşteriler & iş ortakları, dağıtım merkezlerindeki dijital bir geri bildirim döngüsüne ulaşmak için uçtan uca kurumsal çözümler oluşturabilir.

## <a name="business-insights--actions-via-data-egress"></a>Veri çıkışı aracılığıyla iş öngörüleri & eylemleri 
IoT Central platform sürekli veri dışa aktarma (CDE) ve API 'Ler aracılığıyla zengin genişletilebilirlik seçenekleri sağlar. Telemetri veri işleme veya ham telemetri temelinde iş öngörüleri genellikle tercih edilen iş kolu uygulamasına aktarılabilir. Bu, daha zengin Öngörüler & makine öğrenimi modellerini derlemek, eğitme ve dağıtmak için Web kancası, hizmet veri yolu, Olay Hub 'ı veya blob depolama aracılığıyla elde edilebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Dijital dağıtım merkezi şablonunu](./tutorial-iot-central-digital-distribution-center-pnp.md) dağıtmayı öğrenin
* [IoT Central perakende şablonları](./overview-iot-central-retail-pnp.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinin [IoT Central genel bakış](../preview/overview-iot-central.md)
