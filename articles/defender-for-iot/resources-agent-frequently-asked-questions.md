---
title: IoT Aracısı için Azure Defender sık sorulan sorular
description: IoT Aracısı için Azure Defender hakkında en sık sorulan soruların yanıtlarını bulun.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4bd7d3c9b76dfb37e53cc51e5e16b660545cb7f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778670"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>IoT Aracısı için Azure Defender sık sorulan sorular

Bu makalede, IoT aracısına yönelik Defender ile ilgili sık sorulan soruların ve yanıtların bir listesi sunulmaktadır.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>Ekli bir güvenlik Aracısı yüklemem gerekir mi?

IoT için Defender 'ı etkinleştirmek üzere IoT cihazlarınızdaki aracı yüklemesi zorunlu değildir. Aşağıdaki üç seçenekten birini seçerek seçiminize göre farklı güvenlik izleme ve yönetim özellikleri elde edebilirsiniz:

- Ağ ve cihazlarda sıfır performans etkisi ile IoT/OT riskini izlemek ve ayrıntılı bir şekilde görünürlüğünü sağlamak için NTA (ağ trafiği analizi) sensörlerini kullanan pasif, açık olmayan (aracısız) dağıtım
- IoT için Defender Embedded güvenlik aracısını, değişiklikler olmadan veya bunlarla birlikte yükler. Bu seçenek, en yüksek düzeyde gelişmiş güvenlik öngörülerini cihaz davranışı ve erişimi sağlar.

- Kendi aracınızı oluşturun ve IoT için Defender güvenlik iletisi şemasını uygulayın. Bu seçenek, cihaz güvenlik aracınızın en üstünde IoT çözümleme araçları için Defender kullanımını mümkün bir şekilde sunar.

- IoT cihazlarınıza güvenlik Aracısı yüklemesi yok. Bu seçenek, güvenlik izleme ve yönetim özelliklerini azaltmak için IoT Hub iletişim izlemeyi mümkün bir şekilde sunar.

## <a name="what-does-the-defender-for-iot-agent-do"></a>IoT aracısının Defender 'ı ne yapar?

IoT Aracısı için Defender, cihaz yapılandırması, davranış ve erişim (yapılandırmayı tarayarak) için cihaz düzeyinde tehdit kapsamı sağlar, & bağlantısını işler. IoT güvenlik Aracısı için Defender, işle ilgili verileri veya etkinliği taramaz.

IoT güvenlik Aracısı için Defender, 32 bit ve 64 bit Windows ve Linux sürümlerindeki GitHub 'da açık kaynaktır ve kullanılabilir: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Aracının bağımlılıkları ve önkoşulları nelerdir?

IoT için Defender, çok çeşitli platformları destekler. Belirli cihazlarınızla ilgili desteği doğrulamak için [desteklenen cihaz platformları](how-to-deploy-agent.md) bölümüne bakın.

## <a name="which-data-is-collected-by-the-agent"></a>Aracı hangi verileri topladı?

Bağlantı, erişim, güvenlik duvarı yapılandırması, işlem listesi & işletim sistemi taban çizgisi aracı tarafından toplanır.

## <a name="how-much-data-will-the-agent-generate"></a>Aracı ne kadar veri oluşturacak?

Aracı verileri oluşturma cihaz, uygulama, bağlantı türü ve müşteri Aracısı yapılandırması tarafından çalıştırılır. Cihazlar ve IoT çözümleri arasındaki yüksek değişkenlik nedeniyle,, oluşturulan verilerin miktarını ölçirken, gereksinimlerinize uyan belirli yapılandırmayı gözlemlemek, öğrenmek ve ayarlamak için önce aracıyı bir laboratuvar veya test ayarında dağıtmanız önerilir. Hizmet başladıktan sonra, IoT Aracısı için Defender, yapılandırma ve özelleştirme sürecinde size yardımcı olmak üzere aracı aktarım hızını iyileştirmek için işlemsel öneriler sağlar.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Aracı iletileri IoT Hub kota kullanıyor mu?

Evet. Aracı iletilen veriler IoT Hub kotasında sayılır.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Ne kadar ileri? Bir aracı yükledim ve hiç etkinlik veya günlük görmüyorum...

1. [Aracı türünün cihazınızın belirtilen işletim sistemi platformuna uygun](how-to-deploy-agent.md) olduğunu denetleyin

1. [Cihazda aracının çalıştığını](how-to-agent-configuration.md)onaylayın.

1. Hizmetin IoT Hub **güvenlik** için [başarıyla etkinleştirildiğinden](quickstart-onboard-iot-hub.md) emin olun.

1. Cihazın [, IoT Hub ' de IoT modülü Için Defender ile yapılandırıldığından](quickstart-create-security-twin.md)emin olun.

Etkinlikler veya Günlükler hala kullanılamıyorsa, ek yardım için bkz. IoT iş ortağı için Defender.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet bağlantısı çalışmayı kestiğinde ne olur?

Algılayıcılar ve aracılar, cihaz çalıştığı sürece verileri çalıştırmaya ve depolamaya devam eder. Veriler, boyut yapılandırmasına göre güvenlik iletisi önbelleğinde depolanır. Cihazın bağlantısı yapıldığında, güvenlik iletileri göndermeye sürdürülür.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Aracı, cihazın veya diğer yüklü yazılımların performansını etkileyebilir mi?

Aracı başka bir uygulama/işlem olarak makine kaynakları tüketir ve normal cihaz etkinliğini kesintiye uğramamalıdır. Aracının üzerinde çalıştığı cihazdaki kaynak tüketimi, kurulumu ve yapılandırmasıyla birlikte bulunur. Bir üretim ortamında dağıtmayı denemeden önce, diğer IoT Uygulamalarınız ve işlevlerle birlikte çalışabilirlik ile birlikte, aracı yapılandırmanızın bir kapsanan ortamda test edilmesini öneririz.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Cihazda bazı bakımda bakım yapmaya çalışıyorum. Aracıyı kapatabilir miyim?

Aracı kapatılamadı.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Aracının düzgün çalışıp çalışmadığını test etmenin bir yolu var mı?

Aracı iletişimi durduruyor veya güvenlik iletilerini gönderemezse, bir **Cihaz sessiz** uyarı oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

IoT için Defender 'ı kullanmaya başlama hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- IoT için Defender 'ı okuyun [genel bakış](overview.md)
- [Sistem önkoşullarını](quickstart-system-prerequisites.md) doğrulama
- [IoT Için Defender 'ı](getting-started.md) kullanmaya başlama hakkında daha fazla bilgi edinin
- [IoT güvenlik uyarıları Için Defender 'ı](concept-security-alerts.md) anlama
