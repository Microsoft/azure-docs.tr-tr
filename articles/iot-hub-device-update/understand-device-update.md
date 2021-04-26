---
title: Azure IoT Hub cihaz güncelleştirmesine giriş | Microsoft Docs
description: IoT Hub cihaz güncelleştirmesi, IoT cihazlarınız için kablosuz güncelleştirmeleri (OTA) dağıtmanıza olanak sağlayan bir hizmettir.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 95cecd66f4d0164594eb4e8da9efe765b5833e5e
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120784"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>IoT Hub için cihaz Güncelleştirmesi (Önizleme) genel bakış

IoT Hub cihaz güncelleştirmesi, IoT cihazlarınız için kablosuz güncelleştirmeleri (OTA) dağıtmanıza olanak sağlayan bir hizmettir.

Kuruluşlar üretkenlik ve operasyonel verimliliği daha da olanaklı hale getirdiklerinden, Nesnelerin İnterneti (IoT) çözümleri artan oranlarda benimsenmeye devam eder. Bu, bu çözümleri oluşturan cihazların bir güvenilirlik ve güvenlik temel alınarak oluşturulması ve daha kolay bağlantı kurmak ve ölçeklenebilir şekilde yönetilmesi açısından önemli hale gelir. IoT Hub cihaz güncelleştirmesi, müşterilerin küçük algılayıcılardan ağ geçidi düzeyindeki cihazlara kadar her şey için kablosuz güncelleştirmeleri yayımlamak, dağıtmak ve yönetmek için kullanabileceği uçtan uca bir platformdur. 

IoT özellikli dijital dönüştürmenin tüm avantajlarından yararlanmak için, müşterilerin cihazları ölçekli olarak işletmek, bakımını yapmak ve güncelleştirmek için bu yeteneği olması gerekir. Güvenlik tehditlerine hızla yanıt verebilmenin yanı sıra, kendi güncelleştirme Platformlarınızı oluşturmaya yönelik ek geliştirme ve bakım maliyetlerini oluşturmadan iş amaçları elde etmek için yeni özellikler dağıtmak üzere IoT Hub için cihaz güncelleştirmesi uygulama avantajlarını göz atın.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Çok çeşitli IoT cihazları için destek


IoT Hub cihaz güncelleştirmesi, [Azure IoT Hub](https://azure.microsoft.com/en-us/services/iot-hub/)tümleştirmesiyle iyileştirilmiş güncelleştirme dağıtımı ve kolaylaştırılmış işlemler sunacak şekilde tasarlanmıştır. Bu tümleştirme, mevcut herhangi bir çözümde cihaz güncelleştirmesini benimsemeyi kolaylaştırır. Neredeyse tüm cihazları bağlamak için bulutta barındırılan bir çözüm sağlar. Cihaz güncelleştirme, Linux ve [Azure RTOS](https://azure.microsoft.com/en-us/services/rtos/) (gerçek zamanlı işletim sistemi) dahil olmak üzere çok çeşitli IoT işletim sistemlerini destekler ve açık kaynak aracılığıyla Genişletilebilir. Stmikro elektronik, NXP, Renesas ve mikro yonga dahil olmak üzere, yarı iletken iş ortaklarıyla IoT Hub teklifleri için cihaz güncelleştirmesi geliştiriyoruz. Bkz. Başlarken kılavuzlarını içeren temel yarı iletken değerlendirme panoları [örneklerine](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) bakın. Bu arada,-AIR (OTA) güncelleştirmelerini MCU sınıf cihazlarına nasıl yapılandıracağınızı, oluşturacağınızı ve dağıtacağınızı öğrenin. 

Hem cihaz Güncelleştirme Aracısı simülatörü ikili dosyası hem de Raspberry PI başvuru yocto görüntüleri sağlanır.
IoT Hub cihaz güncelleştirmesi Azure IoT Edge cihazların güncelleştirilmesini de destekler. Ubuntu Server 18,04 amd64 platformu için bir cihaz Güncelleştirme Aracısı sağlanır. IoT Hub cihaz güncelleştirmesi, yukarıdaki platformlardan birini çalıştırmıyorsanız açık kaynaklı kod de sağlar. Aracının çalıştırdığınız dağıtıma bağlantı noktası oluşturabilirsiniz.

Cihaz güncelleştirme IoT Tak ve Kullan (PnP) ile birlikte çalışarak, gerekli PnP arabirimlerini destekleyen herhangi bir cihazı yönetebilir. Daha fazla bilgi için bkz. [IoT Hub ve ıot Tak ve kullan Için cihaz güncelleştirme](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Çok çeşitli güncelleştirme yapıtları desteği

IoT Hub cihaz güncelleştirmesi iki güncelleştirme biçimini destekler: görüntü tabanlı ve paket tabanlı.

Paket tabanlı güncelleştirmeler, cihazdaki yalnızca belirli bir bileşeni veya uygulamayı değiştirecek olan güncelleştirmeler için geçerlidir. Bu, bant genişliği tüketimini azaltmaya ve güncelleştirmeyi indirme ve yükleme süresini azaltmaya yardımcı olur. Paket güncelleştirmeleri genellikle bir güncelleştirme uygulanırken cihazların daha az kapalı kalma süresine izin verir ve görüntü oluşturma yükünden kaçınır.

Görüntü güncelleştirmeleri, cihazın bitiş durumunda daha yüksek bir güven düzeyi sağlar. Paket ve bağımlılıklarıyla aynı zorluklara sahip olmadığından, bir üretim öncesi ortamı ve üretim ortamı arasında görüntü güncelleştirme sonuçlarını çoğaltmak genellikle daha kolay olur.
Atomik doğası nedeniyle, bir A/B yük devretme modelini de kolayca benimseyebilirler.

Doğru bir yanıt yoktur ve özel kullanım çalışmalarınız temelinde farklı şekilde seçim yapabilirsiniz. IoT Hub cihaz güncelleştirmesi hem görüntü hem de güncelleştirme paket biçimini destekler, böylece cihaz ortamınız için doğru güncelleştirme modelini seçebilirsiniz.

## <a name="flexible-features-for-updating-devices"></a>Cihazları güncelleştirmek için esnek Özellikler

IoT Hub özellikler için cihaz güncelleştirmesi aşağıdakiler de dahil olmak üzere güçlü ve esnek bir deneyim sağlar:

* Azure IoT Hub ile tümleştirilmiş güncelleştirme yönetimi UX
* Cihaz Gruplandırma ve güncelleştirme zamanlama denetimleriyle aşamalı güncelleştirme dağıtımı
* Otomasyon ve özel Portal deneyimlerini etkinleştirmek için programlı API 'Ler
* Heterojen cihaz fillerine yönelik bir bakışta güncelleştirme uyumluluğu ve durum görünümleri
* Sorunsuz geri alma sağlamak için dayanıklı cihaz güncelleştirmeleri (A/B) desteği
* Azure.com portalı aracılığıyla kullanılabilen abonelik ve rol tabanlı erişim denetimleri
* Bulut bağlantısı kesilen cihazların güncelleştirilmesini sağlamak için şirket içi içerik önbelleği ve Iç Içe geçmiş uç desteği
* Ayrıntılı güncelleştirme yönetimi ve raporlama araçları 

IoT Hub yönetimi ve dağıtım denetimleri için cihaz güncelleştirmesiyle, kullanıcılar üretkenliği en üst düzeye çıkarabilir ve değerli zamandan tasarruf edebilir. IoT Hub cihaz güncelleştirmesi, cihazları gruplandırma ve hangi cihazlara bir güncelleştirme dağıtılması gerektiğini belirtme imkanını içerir. Kullanıcılar ayrıca güncelleştirme dağıtımlarının durumunu görüntüleyebilir ve her cihazın güncelleştirmeleri başarıyla uygulayacağından emin olabilir.

Bir güncelleştirme hatası oluştuğunda, IoT Hub için cihaz güncelleştirmesi kullanıcıların güncelleştirmeyi uygulayamadıkları cihazları belirlemesine de izin verir ve ilgili hata ayrıntılarına bakın. Hangi cihazların güncelleştiremadığını belirleyebilme özelliği, kaynağı belirlemeye çalışan el ile gerçekleştirilen saat sayısı ile aynı sürede tasarruf sağlar.

### <a name="best-in-class-security-at-global-scale"></a>Küresel ölçekte en iyi sınıf güvenliği

Microsoft Azure, dünyanın dört bir yanındaki bir milyardan çok IoT cihazını destekler ve günde hızlı bir şekilde büyümekte olan bir sayıdır. IoT Hub için cihaz güncelleştirmesi, bu deneyimden ve Windows Update platformu tarafından gösterilen kanıtlanmış güvenilirlikten yararlanarak, cihazların küresel ölçekte sorunsuz bir şekilde güncelleştirilmesini sağlayabilir.

IoT Hub cihaz güncelleştirmesi, Microsoft Azure için geliştirilen kapsamlı uçtan uca güvenlik kullanır, böylece müşterilerin baştan sona nasıl derlendiğini öğrenmek için zaman harcamaları gerekmez.


## <a name="device-update-workflows"></a>Cihaz güncelleştirme iş akışları

Cihaz güncelleştirme işlevselliği üç alana ayrılabilir: aracı tümleştirme, Içeri aktarma ve yönetim.

### <a name="device-update-agent"></a>Cihaz Güncelleştirme Aracısı

Bir cihazda güncelleştirme komutu alındığında, bu, güncelleştirmenin istenen aşamasını yürütür (Indir, yükle ve Uygula). Her aşamada durum, IoT Hub aracılığıyla cihaz güncelleştirmesine döndürülür, böylece bir dağıtımın geçerli durumunu görüntüleyebilirsiniz. Devam eden bir güncelleştirme yoksa, durum "boşta" olarak döndürülür. Dağıtım herhangi bir zamanda iptal edilebilir.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Cihaz Güncelleştirme Aracısı iş akışının diyagramı." lightbox="media/understand-device-update/client-agent-workflow.png":::

Cihaz Güncelleştirme Aracısı hakkında [daha fazla bilgi edinin](device-update-agent-overview.md) . 

### <a name="importing"></a>İçeri aktarma

İçeri aktarma, güncelleştirmelerin cihaza dağıtılabilmesi için cihaz güncelleştirmesine nasıl alınacağını aşağıda bulabilirsiniz. Cihaz güncelleştirme, cihaz başına tek bir güncelleştirme kullanıma alınmasını destekler. Bu, bir işletim sistemi bölümünün tamamını veya cihazınızda güncelleştirmek istediğiniz tüm paketleri açıklayan bir apt bildirimini güncelleştiren tam görüntü güncelleştirmelerini ideal hale getirir. Güncelleştirmeleri cihaz güncelleştirmesine aktarmak için öncelikle güncelleştirmeyi açıklayan bir içeri aktarma bildirimi oluşturun ve ardından güncelleştirme dosyalarını ve içeri aktarma bildirimini Internet erişimli bir konuma yükleyin. Bundan sonra, güncelleştirme içeri aktarma işleminin zaman uyumsuz işlemini başlatmak için Azure portal veya [cihaz güncelleştirme REST API](https://docs.microsoft.com/rest/api/deviceupdate/) kullanabilirsiniz. Cihaz güncelleştirme, dosyaları karşıya yükler, işler ve IoT cihazlarına dağıtım için kullanılabilir hale getirir.

Hassas içerik için, Azure Blob depolama için geçici SAS gibi paylaşılan erişim imzasını (SAS) kullanarak indirmeyi koruyun. [SAS hakkında daha fazla bilgi](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="IoT Hub iş akışını içeri aktarmaya yönelik cihaz güncelleştirmesi diyagramı." lightbox="media/understand-device-update/import-update.png":::

Güncelleştirmeleri içeri aktarma hakkında [daha fazla bilgi edinin](import-concepts.md) . 

### <a name="grouping-and-deployment"></a>Gruplandırma ve dağıtım

Bir güncelleştirmeyi içeri aktardıktan sonra, cihazlarınız ve cihaz sınıflarınız için uyumlu güncelleştirmeleri görüntüleyebilirsiniz.

Cihaz güncelleştirme, IoT Hub Etiketler aracılığıyla **Grup** kavramını destekler. İlk olarak bir güncelleştirmeyi test grubuna dağıtmak, bir üretim dağıtımı sırasında sorun riskini azaltmak için iyi bir yoldur.

Cihaz Güncelleştirme ' de dağıtımlar, doğru içeriği belirli bir uyumlu cihaz kümesine bağlama yöntemidir. Cihaz güncelleştirme, her cihaza komut gönderme işlemini düzenleyerek güncelleştirmeleri indirip yüklemeleri ve durumu geri almayı ister.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="IoT Hub gruplandırma ve dağıtım iş akışı için cihaz güncelleştirme diyagramı." lightbox="media/understand-device-update/manage-deploy-updates.png":::

Dağıtım kavramları hakkında [daha fazla bilgi edinin](device-update-compliance.md)

Cihaz güncelleştirme grupları hakkında [daha fazla bilgi](device-update-groups.md)


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Cihaz güncelleştirme hesabı ve örneği oluştur](create-device-update-account.md)