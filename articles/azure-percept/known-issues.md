---
title: Azure Percept bilinen sorunlar
description: Azure Percept bilinen sorunlar ve bunların geçici çözümleri hakkında daha fazla bilgi edinin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 49ac497505930d82a3ab8e90fb3f386cc1741dc7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605068"
---
# <a name="known-issues"></a>Bilinen sorunlar

Bu sorunlardan biriyle karşılaşırsanız, bir hatayı açmak gerekli değildir. Geçici çözümlerden herhangi biriyle ilgili sorun yaşıyorsanız lütfen bir sorun açın.

|Alan|Sorun açıklaması|Geçici çözüm|
|-------|---------|---------|
| Ekleme deneyimi | Cihazın Wi-Fi yapılandırılmadığı müddetçe ekleme deneyimi tamamlanamıyor (Azure oturum açma başarısız olur). | 1. [Azure PERCEPT dk 'ye SSH](./how-to-ssh-into-percept-dk.md). <br> 2. Cihazın Ethernet IP adresini belirleyip kopyalayın. <br> 3. ekleme deneyimine Ethernet IP tabanlı URL 'YI kullanarak bağlanın. |
| Ekleme deneyimi | EULA 'daki bağlantılara tıklamak (Lisans Sözleşmesi) bazen yeni bir Web sayfası açmaz. | Bağlantıyı kopyalayın ve ayrı bir tarayıcı penceresinde açın. |
| Ekleme deneyimi | Bir mobil Wi-Fi etkin noktaya bağlıyken ekleme deneyimi üzerinden çalışılamıyor. | Cihazınızı doğrudan SoftAP 'a, bir Wi-Fi ağa veya Ethernet üzerinden bir ağa bağlayın. |
| Wi-Fi | SoftAP bazen bağlantıyı kesebilir veya kaybolur. | Araştırıyoruz.  Cihazın yeniden başlatılması genellikle geri alınacaktır. |
| Wi-Fi | Wi-Fi SoftAP açık ve kapalı olarak geçiş yapan donanım düğmesi bazen çalışmaz. | Düğmeye basmak veya cihazı yeniden başlatmak için devam edin. |
| Wi-Fi | Kullanıcılar, Wi-Fi ' y e bağlandıktan sonra bir ileti görebilir: <br> "Bu Wi-Fi ağı eski bir güvenlik standardı kullanıyor." | Devkit SoftAP, WEP şifreleme algoritmasını kullanır. |
| Wi-Fi | Windows 10 PC 'den SoftAP 'a şu hata iletisiyle bağlanılamıyor: <br> "Bu ağa bağlanılamıyor" | Hem devkit hem de bilgisayarı yeniden başlatın. |
| Cihaz güncelleştirme | Kapsayıcılar bir OTA güncelleştirmesinden sonra çalışmaz. | Cihaza SSH ekleyin ve IoT Edge kapsayıcısını şu komutla yeniden başlatın: `systemctl restart iotedge` . Bu, tüm kapsayıcıları yeniden başlatacak. |
| Cihaz güncelleştirme | Kullanıcılar, başarılı olsa bile güncelleştirmenin başarısız olduğunu belirten bir ileti alabilir. | IoT Hub ' de devkit 'in cihaz Ikizi giderek ve değerini denetleyerek güncelleştirmeyi onaylayın `swVersion` . Bu, ilk güncelleştirmeden sonra düzeltilir. |
| Cihaz güncelleştirme | Kullanıcılar, ilk güncelleştirmesinden sonra Wi-Fi bağlantı ayarlarını kaybedebilir. | Wi-Fi bağlantısını ayarlamak için güncelleştirmeden sonra ekleme deneyimini çalıştırın. Bu, ilk güncelleştirmeden sonra düzeltilir. |
| Cihaz güncelleştirme | OTA güncelleştirmesini gerçekleştirdikten sonra, kullanıcılar daha önce oluşturulan kullanıcı hesaplarını kullanarak SSH aracılığıyla oturum açabilirler ve yeni SSH kullanıcıları ekleme deneyimi aracılığıyla oluşturulamaz. Bu sorun, aşağıdaki önceden yüklenmiş görüntü sürümlerinden OTA güncelleştirmeleri gerçekleştiren sistemleri etkiler: 2020.110.114.105 ve 2020.109.101.105. | Kullanıcı profillerinizi kurtarmak için, OTA güncelleştirmesinden sonra şu adımları uygulayın: <br> Kullanıcı adı olarak "root" kullanarak [devkit 'e SSH](./how-to-ssh-into-percept-dk.md) . Ekleme deneyimi aracılığıyla SSH "root" Kullanıcı oturum açma kimliğini devre dışı bırakırsanız, yeniden etkinleştirmeniz gerekir. Başarıyla bağlandıktan sonra bu komutu Çalıştır: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Önceki Kullanıcı giriş verilerini kurtarmak için aşağıdaki komutu çalıştırın: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Cihaz güncelleştirme | OTA güncelleştirmesini gerçekleştirdikten sonra, güncelleştirme grupları kaybedilir. | [Bu yönergeleri](./how-to-update-over-the-air.md#create-a-device-update-group)izleyerek cihazın etiketini güncelleştirin. |
| Geliştirme araçları paketi yükleyicisi | Docker sistemde düzgün çalışmıyorsa isteğe bağlı Caffe yüklemesi başarısız olabilir. | Docker 'ın yüklü olduğundan ve çalıştığından emin olun ve ardından Caffe yüklemesini yeniden deneyin. |
| Geliştirme araçları paketi yükleyicisi | İsteğe bağlı CUDA yüklemesi uyumsuz sistemlerde başarısız olur. | Yükleyiciyi çalıştırmadan önce CUDA ile sistem uyumluluğunu doğrulayın. |
| Docker, ağ, IoT Edge | İç ağınız 172. x. x. x kullanıyorsa Docker Kapsayıcıları IoT Edge bağlantı kurmak için başarısız olur. | Aşağıdaki gibi,/etc/Docker/daemon.jsdosyasına özel bir bip bölümü ekleyin: `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Azure Percept Studio 'daki "akışı görüntüleme" bağlantıları, cihazın Web akışını gösteren yeni bir pencere açmayın. | 1. [Azure Portal](https://portal.azure.com) açın ve **IoT Hub**' ı seçin. <br> 2. cihazınızın bağlı olduğu IoT Hub tıklayın. <br> 3. IoT Hub sayfanızda **otomatik cihaz yönetimi** altında **IoT Edge** seçin. <br> 4. listeden cihazınızı seçin. <br> 5. cihaz sayfanızın en üstündeki **modülleri ayarla** ' yı seçin. <br> 6. modülünü silmek için **Hostipmodule** ' un yanındaki trakıcan simgesine tıklayın. <br> 7. eylemi onaylamak için, **gözden geçir + oluştur** ' a ve ardından **Oluştur**' a tıklayın. <br> 8. [Azure Percept Studio 'yu](https://go.microsoft.com/fwlink/?linkid=2135819) açın ve sol taraftaki menü panelinde **cihazlar** ' a tıklayın. <br> 9. listeden cihazınızı seçin. <br> 10. **vizyon** sekmesinde **cihaz akışınızı görüntüleyin**' e tıklayın. Cihazınız, barındırma tipl modülünün yeni bir sürümünü indirir ve cihazınızın web akışıyla bir tarayıcı sekmesi açar. |