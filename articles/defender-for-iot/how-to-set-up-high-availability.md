---
title: Yüksek kullanılabilirliği ayarlama
description: Şirket içi yönetim konsolu yüksek kullanılabilirlik gereci yükleyerek Defender 'ın IoT dağıtımı esnekliğini artırın. Yüksek kullanılabilirlik dağıtımları, yönetilen sensörlerinizin etkin bir şirket içi yönetim konsoluna sürekli olarak raporlabilirliğini sağlar.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: d0e09cd37fbae91d1903ca8f175c0592b567da6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781662"
---
# <a name="about-high-availability"></a>Yüksek kullanılabilirlik hakkında

Şirket içi yönetim konsolu yüksek kullanılabilirlik gereci yükleyerek Defender 'ın IoT dağıtımı esnekliğini artırın. Yüksek kullanılabilirlik dağıtımları, yönetilen sensörlerinizin etkin bir şirket içi yönetim konsoluna sürekli olarak raporlabilirliğini sağlar.

Bu dağıtım, birincil ve ikincil gereç içeren bir şirket içi yönetim konsolu çifti ile uygulanır.

## <a name="about-primary-and-secondary-communication"></a>Birincil ve ikincil iletişim hakkında

Birincil ve ikincil bir şirket içi yönetim konsolu eşlendiğinde:

- Birincil ve ikincil gereçler arasında güvenli bir bağlantı oluşturmak için bir şirket içi yönetim konsolu SLL sertifikası uygulanır. SLL, varsayılan olarak yüklenen otomatik olarak imzalanan sertifika veya müşteri tarafından yüklenen bir sertifika olabilir.

- Birincil şirket içi yönetim konsolu verileri, her 10 dakikada bir ikincil şirket içi yönetim konsoluna otomatik olarak yedeklenir. Şirket içi yönetim konsolu konfigürasyonları ve cihaz verileri yedeklenir. PCAP dosyaları ve günlükleri yedeklemeye dahil edilmez. PCAP 'leri ve günlükleri el ile yedekleyebilir ve geri yükleyebilirsiniz.

- Yönetim konsolundaki birincil Kurulum ikincil üzerinde yinelenir; Örneğin, sistem ayarları. Bu ayarlar birincil üzerinde güncelleştirilirse, ikincil üzerinde de güncelleştirilir.

- İkincil lisansın lisansı sona ermeden önce, lisansı güncelleştirmek için onu birincil olarak tanımlamanız gerekir.

## <a name="about-failover-and-failback"></a>Yük devretme ve yeniden çalışma hakkında

Bir algılayıcı birincil şirket içi yönetim konsoluna bağlanamıyorsa, otomatik olarak ikincil ağa bağlanır. Sistem hem birincil hem de ikincil tarafından aynı anda, algılayıcıların yarısını ikincille iletişim kurduğundan her ikisi de desteklenecektir. Sensörlerden fazla yarısı ile iletişim kurduğundan ikincili üzerinde sürer. Birincili ikinciye yük devretmek yaklaşık üç dakika sürer. Yük devretme gerçekleştiğinde, birincil şirket içi yönetim konsolu donuyor. Bu durumda, aynı oturum açma kimlik bilgilerini kullanarak ikincil üzerinde oturum açabilirsiniz.

Yük devretme sırasında sensörler, birincil gereç ile iletişim kurmaya çalışmaya devam eder. Yönetilen sensörlerden fazla yarısı birincil ile iletişim kurmaya çalıştığında, birincil geri yüklenir. Birincil geri yüklendiğinde, ikincil konsolda aşağıdaki ileti görünür.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Birincil geri yüklendiğinde ikincil konsolda görünen bir iletinin ekran görüntüsü.":::

Yeniden yönlendirmeden sonra birincil gereç oturumunu yeniden açın.

## <a name="high-availability-setup-overview"></a>Yüksek kullanılabilirlik kurulumuna genel bakış

Yükleme ve yapılandırma yordamları dört ana aşamada gerçekleştirilir:

1. Şirket içi yönetim konsolu birincil gereci yükler. 

2. Şirket içi yönetim konsolu birincil gereci yapılandırın. Örneğin, zamanlanmış yedekleme ayarları, VLAN ayarları. Ayrıntılar için bkz. Şirket içi yönetim konsolu Kullanıcı Kılavuzu. Tüm ayarlar, eşleştirmeden sonra otomatik olarak ikincil gereç öğesine uygulanır.

3. Şirket içi yönetim konsolu ikincil gereci yükler. Daha fazla bilgi için bkz. [IoT Için Defender yüklemesi hakkında](how-to-install-software.md).

4. Birincil ve ikincil şirket içi yönetim konsolu gereçlerini [burada](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console)açıklandığı gibi eşleştirin. Birincil şirket içi yönetim konsolunun kurulumu gerçekleştirebilmesi için en az iki algılayıcı yönetmesi gerekir.

## <a name="high-availability-requirements"></a>Yüksek kullanılabilirlik gereksinimleri

Aşağıdaki yüksek kullanılabilirlik gereksinimlerini karşıladığınızı doğrulayın:

- Sertifika gereksinimleri

- Yazılım ve donanım gereksinimleri

- Ağ erişim gereksinimleri

### <a name="software-and-hardware-requirements"></a>Yazılım ve donanım gereksinimleri

- Hem birincil hem de ikincil şirket içi yönetim konsolu gereçlerinin aynı donanım modellerini ve yazılım sürümlerini çalıştırıyor olması gerekir.

- Yüksek kullanılabilirlik sistemi, yalnızca IoT araçları kullanılarak yalnızca IoT kullanıcıları için Defender tarafından ayarlanabilir.

### <a name="network-access-requirements"></a>Ağ erişim gereksinimleri

Kurumsal güvenlik ilkenizin, birincil ve ikincil şirket içi yönetim konsolunda aşağıdaki hizmetlere erişmenize izin verdiğini doğrulamanız gerekir. Bu hizmetler ayrıca sensörler ve ikincil şirket içi yönetim konsolu arasında bağlantıya izin verir:

|Bağlantı noktası|Hizmet|Açıklama|
|----|-------|-----------|
|**443 veya TCP**|HTTPS|Şirket içi yönetim konsolu web konsoluna erişim izni verir.|
|**22 veya TCP**|SSH|Birincil ve ikincil şirket içi yönetim konsolu gereçlerinde verileri eşitler|
|**123 veya UDP**|NTP| Şirket içi yönetim konsolunun NTP zaman eşitlemesi. Etkin ve pasif gereçlerinin aynı saat dilimiyle tanımlandığından emin olun.|

## <a name="create-the-primary-and-secondary-pair"></a>Birincil ve ikincil çifti oluşturma

Yordamı başlatmadan önce hem birincil hem de ikincil şirket içi yönetim konsolu gereçlerinin açık olduğunu doğrulayın.  

### <a name="on-the-primary"></a>Birincil

1. IoT Kullanıcı için bir Defender olarak CLı 'da oturum açın.

2. Birincil üzerinde aşağıdaki komutu çalıştırın:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>Bu belgede, sorumlu şirket içi yönetim konsoluna birincil olarak başvurulur ve aracı ikincil olarak adlandırılır.

3. Alana ikincil gerecin IP adresini girin ```<Secondary ip>``` ve ENTER ' u seçin. IP adresi daha sonra onaylanır ve SSL sertifikası birincil adrese indirilir. IP adresi girildiğinde sensörlerle ikincil gereç de ilişkilendirilir.

4. Sertifikanın düzgün yüklendiğini doğrulamak için birincil üzerinde aşağıdaki komutu çalıştırın:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Birincil üzerinde aşağıdaki komutu çalıştırın. **Sudo ile çalıştırmayın.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Bu, birincil ve ikincil gereçlerle aralarında yedekleme ve geri yükleme amaçlarıyla bağlantı sağlar.

6. İkincil adresin IP adresini girin ve ENTER ' u seçin.

### <a name="on-the-secondary"></a>İkincil üzerinde

1. IoT Kullanıcı için bir Defender olarak CLı 'da oturum açın.

2. İkincil üzerinde aşağıdaki komutu çalıştırın. **Sudo ile çalıştırma**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Bu, birincil ve Ikincil gereçlerle aralarında yedekleme ve geri yükleme amaçlarıyla bağlantı sağlar.

3. Birincil adresin IP adresini girin ve ENTER 'a basın.

### <a name="track-high-availability-activity"></a>Yüksek kullanılabilirlik etkinliğini izle

Çekirdek uygulama günlükleri, tüm yüksek kullanılabilirlik sorunlarını işleyebilecek IoT destek ekibine yönelik Defender 'a aktarılabilir.  

Çekirdek günlüklerine erişmek için:

1. **Sistem ayarları** penceresinden **dışarı aktar** ' ı seçin.

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Şirket içi yönetim konsolunu yüksek kullanılabilirliğiyle güncelleştirme

Yüksek kullanılabilirlik güncelleştirmesini aşağıdaki sırayla gerçekleştirin. Yeni bir adımla başlamadan önce her adımın tamamlandığından emin olun.

Yüksek kullanılabilirliğiyle güncelleştirmek için:

1. Birincil şirket içi yönetim konsolunu güncelleştirin.

2. İkincil şirket içi yönetim konsolunu güncelleştirin.

3. Algılayıcıları güncelleştirin.

## <a name="see-also"></a>Ayrıca bkz.

[Şirket içi yönetim konsolunuzu etkinleştirme ve ayarlama](how-to-activate-and-set-up-your-on-premises-management-console.md)