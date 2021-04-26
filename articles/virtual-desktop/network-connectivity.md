---
title: Windows Sanal Masaüstü Ağ bağlantısını anlama
titleSuffix: Azure
description: Windows Sanal Masaüstü Ağ bağlantısı hakkında bilgi edinin
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 378d341ee867b69fac22f5eb15952d104a7bf19d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167337"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Windows Sanal Masaüstü Ağ bağlantısını anlama

Windows sanal masaüstü, Azure üzerinde çalışan oturum konakları üzerinde istemci oturumlarını barındırmanıza olanak tanır. Microsoft, müşterinin adına hizmetlerin bölümlerini yönetir ve istemcileri ve oturum konaklarına bağlanmak için güvenli uç noktalar sağlar. Aşağıdaki diyagramda Windows sanal masaüstü tarafından kullanılan ağ bağlantılarına yönelik üst düzey bir genel bakış sunulmaktadır

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Windows Sanal Masaüstü Ağ bağlantıları diyagramı" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Oturum bağlantısı

Windows sanal masaüstü, ağ bağlantıları üzerinden uzaktan görüntüleme ve giriş özellikleri sağlamak için Uzak Masaüstü Protokolü (RDP) kullanır. RDP başlangıçta Windows NT 4,0 Terminal Server sürümü ile piyasaya sürüldü ve her Microsoft Windows ve Windows Server sürümüyle sürekli olarak gelişmiştir. Başlangıçtan itibaren, temel alınan aktarım yığınından bağımsız olarak geliştirilen RDP geliştirilmiştir ve bugün birden çok tür aktarımı destekler.

## <a name="reverse-connect-transport"></a>Ters bağlantı aktarımı

Windows sanal masaüstü, uzak oturumu oluşturmak ve RDP trafiğini taşımak için ters bağlantı aktarımını kullanıyor. Şirket içi Uzak Masaüstü Hizmetleri dağıtımlarından farklı olarak, ters bağlantı aktarımı gelen RDP bağlantıları almak için TCP dinleyicisi kullanmaz. Bunun yerine, HTTPS bağlantısı üzerinden Windows sanal masaüstü altyapısına giden bağlantıyı kullanıyor.

## <a name="session-host-communication-channel"></a>Oturum Ana Bilgisayarı iletişim kanalı

Windows sanal masaüstü oturumu ana bilgisayarı başlatıldığında, uzak masaüstü aracı Yükleyicisi hizmeti Windows sanal masaüstü aracısının kalıcı iletişim kanalını belirler. Bu iletişim kanalı, güvenli bir Aktarım Katmanı Güvenliği (TLS) bağlantısının üzerine katmanlanmış ve oturum ana bilgisayarı ile Windows sanal masaüstü altyapısı arasında hizmet ileti değişimi için bir veri yolu görevi görür.

## <a name="client-connection-sequence"></a>İstemci bağlantı sırası

İstemci bağlantı sırası aşağıda açıklanmıştır:

1. Desteklenen Windows sanal masaüstü istemci kullanıcısının kullanılması Windows sanal masaüstü çalışma alanına abone olur
2. Azure Active Directory kullanıcının kimliğini doğrular ve bir kullanıcının kullanabileceği kaynakları numaralandırmak için kullanılan belirteci döndürür
3. İstemci belirteci Windows sanal masaüstü akışı abonelik hizmeti 'ne geçirir
4. Windows sanal masaüstü akışı abonelik hizmeti belirteci doğrular
5. Windows sanal masaüstü akışı abonelik hizmeti, kullanılabilir masaüstleri ve RemoteApps listesini, dijital olarak imzalanan bağlantı yapılandırması biçiminde istemciye geri geçirir
6. İstemci, kullanılabilir her kaynak için bağlantı yapılandırmasını bir. rdp dosyası kümesinde depolar
7. Bir Kullanıcı bağlanacak kaynağı seçtiğinde, istemci ilişkili. rdp dosyasını kullanır ve en yakın Windows Sanal Masaüstü Ağ Geçidi örneğine güvenli TLS 1,2 bağlantısı kurar ve bağlantı bilgilerini geçirir
8. Windows Sanal Masaüstü Ağ Geçidi, isteği doğrular ve Windows sanal masaüstü aracısıdır bağlantıyı düzenlemenizi ister
9. Windows sanal masaüstü Aracısı, oturum konağını tanımlar ve bağlantıyı başlatmak için önceden oluşturulmuş kalıcı iletişim kanalını kullanır
10. Uzak Masaüstü yığını, istemci tarafından kullanılan aynı Windows Sanal Masaüstü Ağ Geçidi örneğine TLS 1,2 bağlantısını başlatır
11. Hem istemci hem de oturum ana bilgisayarı ağ geçidine bağlandıktan sonra, ağ geçidi her iki uç nokta arasındaki ham verileri geçişe başlar, bu, RDP için temel ters bağlantı aktarımını belirler
12. Temel taşıma ayarlandıktan sonra istemci RDP anlaşmasını başlatır

## <a name="connection-security"></a>Bağlantı güvenliği

TLS 1,2, istemcilerden ve oturum konaklarından başlatılan tüm bağlantılar için Windows sanal masaüstü altyapısı bileşenlerine kullanılır. Windows sanal masaüstü, [Azure ön kapısının](../frontdoor/front-door-faq.yml#what-are-the-current-cipher-suites-supported-by-azure-front-door-)kullandığı TLS 1,2 şifrelemeleri kullanır. Hem istemci bilgisayarlarının hem de oturum ana bilgisayarlarının bu şifrelemeleri kullanabilmesini sağlamak önemlidir.
Ters bağlantı taşıması için hem istemci hem de oturum ana bilgisayarı Windows sanal masaüstü ağ geçidine bağlanır. TCP bağlantısı kurulduktan sonra, istemci veya oturum ana bilgisayarı Windows Sanal Masaüstü Ağ geçidinin sertifikasını doğrular.
Temel taşıma kurulduktan sonra, RDP, oturum ana bilgisayarı sertifikalarını kullanarak istemci ile oturum ana bilgisayarı arasında iç içe geçmiş bir TLS bağlantısı kurar. Varsayılan olarak, RDP şifrelemesi için kullanılan sertifika, dağıtım sırasında işletim sistemi tarafından kendi kendine oluşturulmuştur. İsterseniz, müşteriler kuruluş sertifika yetkilisi tarafından verilen merkezi olarak yönetilen sertifikalar dağıtabilir. Sertifikaları yapılandırma hakkında daha fazla bilgi için bkz. [Windows Server belgeleri](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Sonraki adımlar

* Windows sanal masaüstü için bant genişliği gereksinimleri hakkında bilgi edinmek için bkz. [Windows sanal masaüstü için Uzak Masaüstü Protokolü (RDP) bant genişliği gereksinimlerini anlama](rdp-bandwidth.md).
* Windows sanal masaüstü için hizmet kalitesi 'ni (QoS) kullanmaya başlamak için bkz. [Windows sanal masaüstü Için hizmet kalitesi (QoS) uygulama](rdp-quality-of-service-qos.md).
