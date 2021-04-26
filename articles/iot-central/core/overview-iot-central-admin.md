---
title: Azure IoT Central Yönetici Kılavuzu
description: Azure IoT Central, IoT çözümlerinin oluşturulmasını kolaylaştıran bir IoT uygulama platformudur. Bu makale IoT Central içindeki yönetici rolüne genel bir bakış sağlar.
author: philmea
ms.author: philmea
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 658ebe027565a3acaf427a7b3dbf3963701069d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868654"
---
# <a name="iot-central-administrator-guide"></a>IoT Central Yönetici Kılavuzu

*Bu makale Yöneticiler için geçerlidir.*

IoT Central bir uygulama, yaşam döngüsü boyunca milyonlarca cihazı izlemenize ve yönetmenize olanak sağlar. Bu kılavuz, IoT Central uygulamalarını yöneten yöneticiler içindir.

IoT Central, yönetici:

- Uygulamadaki kullanıcıları ve rolleri yönetir.
- Cihaz kimlik doğrulaması gibi güvenliği yönetir.
- Uygulama ayarlarını yapılandırır.
- Uygulamaları yükseltir.
- Uygulamaları dışa aktarır ve paylaşır.
- Uygulama sistem durumunu izler.

## <a name="users-and-roles"></a>Kullanıcılar ve roller

IoT Central, bir uygulama içindeki kullanıcı izinlerini yönetmek için rol tabanlı erişim denetimi sistemi kullanır. IoT Central Yöneticiler, çözüm oluşturucular ve işleçler için üç yerleşik rol bulunur. Yönetici, belirli izin kümeleriyle özel roller oluşturabilir. Yönetici, bir uygulamaya kullanıcı eklemekten ve bunları rollere atamasından sorumludur.

Daha fazla bilgi edinmek için bkz. [IoT Central uygulamanızdaki kullanıcıları ve rolleri yönetme](howto-manage-users-roles.md).

## <a name="application-security"></a>Uygulama güvenliği

IoT Central uygulamanıza bağlanan cihazlar genellikle kimlik bilgileri olarak X. 509.440 sertifikaları veya paylaşılan erişim imzaları (SAS) kullanır. Yönetici, cihaz kimlik bilgilerinin türetildiği grup sertifikalarını veya anahtarlarını yönetir.

Daha fazla bilgi edinmek için bkz. [x. 509.440 grup kaydı](concepts-get-connected.md#x509-group-enrollment), [SAS grubu kaydı](concepts-get-connected.md#sas-group-enrollment)ve [x. 509.440 cihaz sertifikalarını alma](how-to-roll-x509-certificates.md).

Yönetici ayrıca, bir istemci uygulamanın IoT Central uygulamanızla kimlik doğrulaması için kullandığı API belirteçlerini oluşturabilir ve yönetebilir. İstemci uygulamaları, IoT Central etkileşimde bulunmak için REST API kullanır.

## <a name="configure-an-application"></a>Uygulama yapılandırma

Yönetici, bir IoT Central uygulamasının davranışını ve görünümünü yapılandırabilir. Daha fazla bilgi edinmek için şu makalelere bakın:

- [Uygulama adını ve URL 'YI değiştirme](howto-administer.md#change-application-name-and-url)
- [Kullanıcı arabirimini özelleştirme](howto-customize-ui.md)
- [Uygulamayı farklı bir fiyatlandırma planına taşıma](howto-view-bill.md)
- [Karşıya dosya yüklemeyi yapılandırma](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Bir uygulamayı dışarı aktarma

Yönetici şunları yapabilir:

- Yalnızca uygulamanızın yinelenen bir kopyasına ihtiyacınız varsa uygulamanın bir kopyasını oluşturun. Örneğin, test için yinelenen bir kopyaya ihtiyacınız olabilir.
- Birden çok kopya oluşturmayı planlıyorsanız var olan uygulamadan bir uygulama şablonu oluşturun.

Daha fazla bilgi için bkz. [Azure IoT uygulamanızı dışarı aktarma](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Yeni bir sürüme geçir

Yönetici, bir uygulamayı daha yeni bir sürüme geçirebilir. Şu anda yeni oluşturulan bir uygulama v3 uygulamasıdır. Bir yöneticinin bir v2 uygulamasını bir v3 uygulamasına geçirilmesi gerekebilir.

Daha fazla bilgi edinmek için bkz. [V2 IoT Central uygulamanızı v3 'e geçirme](howto-migrate.md).

## <a name="monitor-application-health"></a>Uygulamanın durumunu izleme

Yönetici, bağlı cihazların sistem durumunu ve veri dışarı aktarmaları çalıştırma durumunu değerlendirmek için IoT Central ölçümleri kullanabilir.

Bir yönetici, ölçümleri görüntülemek için Azure portal, bir REST API veya PowerShell veya Azure CLı sorguları içindeki grafikleri kullanabilir.

Daha fazla bilgi için bkz. [bir IoT Central uygulamasının genel sistem durumunu izleme](howto-monitor-application-health.md).

## <a name="tools"></a>Araçlar

Yönetici olarak kullandığınız araçların birçoğu, her bir IoT Central uygulamasının **Yönetim** bölümünde bulunur. Bazı yönetim görevlerini gerçekleştirmek için aşağıdaki araçları da kullanabilirsiniz:

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure portalı](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızı yönetme hakkında bilgi edindiğinize göre, önerilen sonraki adım Azure IoT Central [kullanıcıları ve rolleri yönetme](howto-manage-users-roles.md) hakkında bilgi almanız gerekir.
