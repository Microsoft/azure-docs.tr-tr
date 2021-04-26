---
title: Desteklenen işletim sistemleri, kapsayıcı altyapıları-Azure IoT Edge
description: Hangi işletim sistemlerinin Azure IoT Edge Daemon ve çalışma zamanını çalıştırabileceğinizi ve üretim cihazlarınız için desteklenen kapsayıcı altyapılarını öğrenin
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67532fce2cac0ec9d05b4caa069e63014b813bd8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576355"
---
# <a name="azure-iot-edge-supported-systems"></a>Desteklenen sistemleri Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Bu makalede, resmi olarak veya önizlemeye bakılmaksızın IoT Edge tarafından desteklenen sistemler ve bileşenler hakkında ayrıntılı bilgi sağlanır.

## <a name="get-support"></a>Destek alın

Azure IoT Edge hizmetini kullanırken sorunlarla karşılaşırsanız, destek almak için çeşitli yollar vardır. Destek için aşağıdaki kanallardan birini deneyin:

**Raporlama hataları** – Azure IoT Edge ürüne giden geliştirmenin büyük bölümü IoT Edge açık kaynaklı projede gerçekleşir. Hatalar, projenin [sorunlar sayfasında](https://github.com/azure/iotedge/issues) bildirilebilir. Windows üzerinde Linux için Azure IoT Edge ilgili hatalar, [ıotedge-eFlow sorunları sayfasında](https://github.com/azure/iotedge-eflow/issues)bildirilebilir. Düzeltmeler, içindeki projelerden ürün güncelleştirmelerine kadar hızlı bir şekilde yapılır.

**Microsoft müşteri destek ekibi** – [Destek planına](https://azure.microsoft.com/support/plans/) sahip olan kullanıcılar, doğrudan [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)bir destek bileti oluşturarak Microsoft müşteri destek ekibine geçebilir.

**Özellik istekleri** – Azure IoT Edge ürünü, ürünün [Kullanıcı sesi sayfası](https://feedback.azure.com/forums/907045-azure-iot-edge)aracılığıyla özellik isteklerini izler.

## <a name="container-engines"></a>Kapsayıcı altyapıları

Azure IoT Edge modüller kapsayıcı olarak uygulanır, bu nedenle IoT Edge bir kapsayıcı altyapısının bunları başlatması gerekir. Microsoft, bu gereksinimi karşılamak için Moby-Engine kapsayıcı altyapısı sağlar. Bu kapsayıcı altyapısı, Moby açık kaynaklı projeyi temel alır. Docker CE ve Docker EE, diğer popüler kapsayıcı altyapılardır. Ayrıca, Moby açık kaynaklı projeye dayalıdır ve Azure IoT Edge uyumludur. Microsoft bu kapsayıcı altyapılarını kullanan sistemler için en iyi çaba desteğini sağlar; Bununla birlikte, Microsoft bu sorunları gidermeye yönelik düzeltmeler de teslim edemiyor. Bu nedenle, Microsoft, üretim sistemlerinde Moby-Engine kullanılmasını önerir.

<br>
<center>

![Kapsayıcı çalışma zamanı olarak Moby altyapısı](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>İşletim sistemleri

Azure IoT Edge, kapsayıcıları çalıştıratabilen çoğu işletim sisteminde çalışır; Ancak, bu sistemlerin hepsi eşit olarak desteklenmez. İşletim sistemleri, kullanıcıların tahmin edebilecekleri destek düzeyini temsil eden katmanlar halinde gruplandırılır.

* Katman 1 sistemleri desteklenir. Katman 1 sistemleri için, Microsoft:
  * Bu işletim sistemine otomatik testlerde sahiptir
  * Bunlar için yükleme paketleri sağlar
* Katman 2 sistemleri Azure IoT Edge uyumludur ve görece kolay bir şekilde kullanılabilir. Katman 2 sistemleri için:
  * Microsoft, platformlar üzerinde resmi olmayan test yaptık veya platformda başarıyla çalışan bir iş ortağı biliyor Azure IoT Edge
  * Diğer platformlar için yükleme paketleri, bu platformlarda çalışabilir

Ana bilgisayar işletim sistemi ailesi her zaman bir modülün kapsayıcısı içinde kullanılan Konuk işletim sisteminin ailesiyle aynı olmalıdır.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Diğer bir deyişle, yalnızca Windows 'da Linux ve Windows kapsayıcılarında Linux kapsayıcıları kullanabilirsiniz. Windows kapsayıcıları kullanılırken, Hyper-V yalıtılmış kapsayıcılar değil yalnızca işlem yalıtılmış kapsayıcılar desteklenir.  

Windows üzerinde Linux için IoT Edge, Windows ana bilgisayarında çalışan bir Linux sanal makinesinde IoT Edge kullanır. Bu şekilde, Linux modüllerini bir Windows cihazında çalıştırabilirsiniz.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Katman 1

Aşağıdaki tablolarda listelenen sistemler Microsoft tarafından desteklenir, genel olarak kullanılabilir ya da genel önizleme aşamasındadır ve her yeni sürümle test edilmiştir.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge, Linux veya Windows kapsayıcıları olarak oluşturulan modülleri destekler. Linux kapsayıcıları, Linux cihazlarına dağıtılabilir veya Windows üzerinde Linux için IoT Edge kullanılarak Windows cihazlarına dağıtılabilir. Windows kapsayıcıları yalnızca Windows cihazlarına dağıtılabilir.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge 1,2 sürümü yalnızca Linux kapsayıcıları olarak oluşturulan modülleri destekler.

Şu anda Windows cihazlarda IoT Edge sürüm 1,2 ' i çalıştırmanın desteklenen bir yolu yoktur. [Windows 'Da Linux için IoT Edge](iot-edge-for-linux-on-windows.md) , windows cihazlarında IoT Edge çalıştırmanın önerilen yoludur, ancak şu anda yalnızca IoT Edge 1,1 ' i çalıştırıyor. Daha fazla bilgi için bu makalenin [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) sürümüne bakın.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Linux kapsayıcıları

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Linux kapsayıcıları olarak oluşturulan modüller, Linux veya Windows cihazlarına dağıtılabilir. Linux cihazlarında, IoT Edge çalışma zamanı doğrudan konak cihaza yüklenir. Windows cihazlarında, IoT Edge çalışma zamanına göre önceden oluşturulmuş bir Linux sanal makinesi, konak cihazında çalışır.

[Windows üzerinde Linux için IoT Edge](iot-edge-for-linux-on-windows.md) Şu anda genel önizlemededir, ancak windows cihazlarda IoT Edge çalıştırmanın önerilen yoludur.

| Operating System | 'TÜR | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry PI OS Esnetme |  | ![Raspberry PI OS Esneti + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Genel Önizleme |
| Windows 10 Pro | Genel Önizleme |  |  |
| Windows 10 Enterprise | Genel Önizleme |  |  |
| Windows 10 IoT Enterprise | Genel Önizleme |  |  |
| Windows Server 2019 | Genel Önizleme |  |  |

Tüm Windows işletim sistemleri sürüm 1809 (derleme 17763) veya üzeri olmalıdır.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Operating System | 'TÜR | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry PI OS Esnetme |  | ![Raspberry PI OS Esneti + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Genel Önizleme |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Ubuntu Server 16,04 desteği IoT Edge sürüm 1,1 sürümüyle sona erdi.

#### <a name="windows-containers"></a>Windows kapsayıcıları

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1,1 LTS, Windows kapsayıcılarını destekleyecek son sürüm kanaldır. Sürüm 1,2 ' den başlayarak Windows kapsayıcıları desteklenmeyecektir. Windows cihazlarda IoT Edge çalıştırmak için [Windows 'Ta Linux için IoT Edge](iot-edge-for-linux-on-windows.md) kullanmayı veya taşımayı düşünün.

Windows kapsayıcıları olarak oluşturulan modüller yalnızca Windows cihazlarına dağıtılabilir.

| Operating System | 'TÜR | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Tüm Windows işletim sistemleri sürüm 1809 (derleme 17763) olmalıdır. Windows kapsayıcıları sürümü, ana bilgisayar Windows cihazının sürümü ile tam olarak eşleşmesi gerektiğinden Windows için belirli bir Windows derlemesi IoT Edge gerekir. Windows kapsayıcıları Şu anda yalnızca 17763 derlemesini kullanır.

>[!NOTE]
>Windows 10 IoT Core desteği, IoT Edge sürüm 1,1 sürümü ile sona erdi.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1,1 LTS, Windows kapsayıcılarını destekleyen son sürüm kanaldır. Sürüm 1,2 ' den başlayarak Windows kapsayıcıları desteklenmez.

Windows kapsayıcıları için desteklenen işletim sistemleri hakkında bilgi için, bu makalenin [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) sürümüne bakın.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Katman 2

Aşağıdaki tabloda listelenen sistemler Azure IoT Edge uyumlu olarak değerlendirilir, ancak Microsoft tarafından etkin bir şekilde sınanmamıştır veya korunmaz.

| Operating System | 'TÜR | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Dete 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debir 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debir 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![De, 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![De, 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![De, 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux OMNI OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux OMNI OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux OMNI OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rüzgar River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry PI OS Buster |  | ![Raspberry PI OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry PI OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> [Linux için yükleme veya kaldırma Azure IoT Edge](how-to-install-iot-edge.md) , ubuntu Server 18,04 yükleme adımları Ubuntu 20,04 üzerinde hiçbir değişiklik yapılmadan çalışmalıdır.

## <a name="releases"></a>Yayınlar

IoT Edge sürüm varlıkları ve sürüm notları, [Azure-ıotedge yayınları](https://github.com/Azure/azure-iotedge/releases) sayfasında bulunabilir. Bu bölüm, her bir sürümün bileşenlerini daha kolay görselleştirmenize yardımcı olmak için bu sürüm notlarındaki bilgileri yansıtır.

Aşağıdaki tabloda, 1.2.0 ile başlayan her sürümde yer alan bileşenler listelenmiştir. Bu tabloda listelenen bileşenler tek başına yüklenebilir veya güncelleştirilir ve eski sürümlerle geriye dönük olarak uyumludur.

| Yayınla | azıot-Edge | edgeHub<br>edgeAgent | azıot-kimlik hizmeti |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0      | 1.2.0                | 1.2.0                  |

Aşağıdaki tabloda, 1,1 LTS sürümüne kadar her bir yayında yer alan bileşenler listelenmiştir. Bu tabloda listelenen bileşenler tek başına yüklenebilir veya güncelleştirilir ve eski sürümlerle geriye dönük olarak uyumludur.

| Yayınla | iotedge | edgeHub<br>edgeAgent | libiothsm | Moby |
|--|--|--|--|--|
| **1,1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1<br><br> | 1.1.0<br>1.1.1<br>1.1.2 | 1.1.0<br>1.1.1<br><br> |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1,1, ilk uzun süreli destek (LTS) yayın kanaldır. Bu sürüm yeni özellikler sunmadı, ancak gerilemeler için güvenlik güncelleştirmeleri ve düzeltmeler alacak. IoT Edge 1,1 LTS, .NET Core 3,1 kullanır ve [.NET Core ve .NET 5 sürüm yaşam döngüsü](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)ile eşleşecek şekilde 3 Aralık 2022 tarihine kadar desteklenecektir.

>[!IMPORTANT]
>Uzun süreli bir destek kanalının yayımlanması sayesinde, 1.0. x çalıştıran tüm geçerli müşterilerin cihazlarını 1.1. x sürümüne yükselterek devam eden destek almasını öneririz.

IoT Edge, Microsoft. Azure. Devices. Client SDK 'sını kullanır. Daha fazla bilgi için bkz. [Azure ıOT C# SDK GitHub deposu](https://github.com/Azure/azure-iot-sdk-csharp) veya [.NET için Azure SDK başvuru içeriği](/dotnet/api/overview/azure/iot/client). Aşağıdaki listede, her sürümün test ettiği istemci SDK sürümü gösterilmektedir:

| IoT Edge sürümü | Microsoft. Azure. Devices. Client SDK sürümü |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1,1 (LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Sanal Makineler

Azure IoT Edge, sanal makinelerde çalıştırılabilir. Bir sanal makinenin IoT Edge cihaz olarak kullanılması, müşteriler var olan altyapıyı Edge zekası ile genişletmek istediğinizde yaygındır. Konak VM işletim sistemi ailesi, bir modülün kapsayıcısı içinde kullanılan Konuk işletim sisteminin ailesiyle eşleşmelidir. Bu gereksinim, Azure IoT Edge doğrudan bir cihazda çalıştırıldığı zaman ile aynıdır. Azure IoT Edge, temel alınan sanallaştırma teknolojisinin belirsiz olması ve Hyper-V ve vSphere gibi platformlar tarafından desteklenen VM 'lerde çalışır.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Bir VM 'de Azure IoT Edge](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Bir VM 'de Azure IoT Edge](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>En düşük sistem gereksinimleri

Azure IoT Edge, sunucu sınıfı donanımlarına Raspberry pi3 kadar küçük cihazlarda harika bir şekilde çalışır. Senaryonuz için doğru donanımın seçilmesi, çalıştırmak istediğiniz iş yüklerine bağlıdır. Son cihaz kararının yapılması karmaşık olabilir; Ancak, bir çözümü geleneksel dizüstü bilgisayarlarda veya masaüstleri üzerinde kolayca başlatabilirsiniz.

Prototipleme, son cihazınızın seçimine kılavuzluk etmenize yardımcı olacak deneyimdir. Göz önünde bulundurmanız gereken sorular şunlardır:

* İş yükünüze kaç modül var?
* Modüllerinizin kapsayıcıları paylaştığı katman sayısı nedir?
* Modülleriniz hangi dilde yazılmıştır?
* Modüllerinizin ne kadar veri işlemesi gerekir?
* Modüllerinizin iş yüklerini hızlandırarak özel donanımlar mi gerekiyor?
* Çözümünüzün istenen performans özellikleri nelerdir?
* Donanım bütçeniz nedir?
