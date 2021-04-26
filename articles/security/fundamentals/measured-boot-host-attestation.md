---
title: Önyükleme ve konak kanıtlama ile ölçülen bellenim-Azure güvenliği
description: Azure üretici yazılımının önyükleme ve konak kanıtlama ile ölçüğü hakkında teknik genel bakış.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f4906d4e0590df047bac4ee15cb0e996a59c3d5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498872"
---
# <a name="measured-boot-and-host-attestation"></a>Ölçülen önyükleme ve konak kanıtlama
Bu makalede, Microsoft 'un ölçülen önyükleme ve konak kanıtlama aracılığıyla konakların bütünlüğünü ve güvenliğini nasıl sağladığını nasıl sağladığını anlatmaktadır.

## <a name="measured-boot"></a>Ölçülen önyükleme

[Güvenilir Platform Modülü](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM), güvenilir bir üçüncü taraf tarafından sağlanan üretici yazılımına sahip, yetkisiz bir güvenli denetim bileşenidir. Önyükleme yapılandırma günlüğü, ana bilgisayar önyükleme sırasını son kez gerçekleştirdiğinde, bu platform yapılandırma kayıtları 'nda (PCR) kaydedilen karma zincirleme ölçümleri içerir. Aşağıdaki şekilde bu kayıt işlemi gösterilmektedir. Daha önceden karma hale getirilmiş bir ölçüyü sonraki ölçümün karmasından artımlı olarak ekleme ve birleştirme karma zinciri oluşturma üzerinde karma algoritmasını çalıştırma.

![Konak kanıtlama hizmeti karma zinciri gösteren diyagram.](./media/measured-boot-host-attestation/hash-chaining.png)

Bir ana bilgisayar, kendi önyükleme yapılandırma günlüğünü (TCGLog) kullanarak yapılandırma durumunu kanıtlaması halinde kanıtlama gerçekleştirilir. TPM, okuma ve genişletme işlemlerinden farklı PCR değerlerini kullanıma sunmadığından, bir önyükleme günlüğünün forlik değeri zordur. Ayrıca, konak kanıtlama hizmeti tarafından sağlanan kimlik bilgileri belirli PCR değerlerine mühürlenir. Karma zincirleme kullanımı, kimlik bilgilerinin bant dışı olarak sızmasını veya mühürlemesini kaldırmak açısından hesaplama yapmayı zorlaştırır.

## <a name="host-attestation-service"></a>Konak kanıtlama hizmeti

Konak kanıtlama hizmeti, ana bilgisayar makinelerinin müşteri verileriyle veya iş yükleri ile etkileşime girmesine izin verilmesi için güvenilir olup olmadığını denetleyen önleyici bir ölçüdür. Konak kanıtlama hizmeti, her ana bilgisayar tarafından bir kanıtlama ilkesine (güvenli durum tanımı) gönderilen uyumluluk bildiriminin (konağın uyumluluk sağlaması) doğrulayarak denetlenir. Bu sistemin bütünlüğü, TPM tarafından sağlanmış olan [güven köküyle](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) garanti edilir.

Konak kanıtlama hizmeti, her bir Azure kümesinde özel bir kilitli ortamda bulunur. Kilitli ortam, konak makinesi önyükleme protokolüne katılan diğer Gatekeeper hizmetlerini içerir. Ortak anahtar altyapısı (PKI), kanıtlama isteklerinin ve bir kimlik verenin (başarılı bir ana bilgisayar kanıtlaması üzerinde çalışan) kimliğini doğrulamak için bir aracı görevi görür. Kanıtlamaktan ana bilgisayarına verilen kanıtlama sonrası kimlik bilgileri, kendi kimliğiyle mühürlendi. Yalnızca istenen konak, kimlik bilgilerinin mühürden yararlanabilir ve artımlı izinler elde etmek için bunlardan yararlanabilir. Bu, ortadaki ve yanıltmaya karşı adam saldırıları önler.

Bir Azure ana bilgisayarı, bir güvenlik yanlış yapılandırılmış veya veri merkezinde oynanmış bir güvenlik hatalı olduğunda, TCGLog, bir kanıtlama hatasına neden olan sonraki kanıtlama sırasında konak kanıtlama hizmeti tarafından işaretlenen güvenlik açığı göstergelerini içerir. Kanıtlama arızaları, Azure filo 'in sorunlu konağa güvenmesine engel oldu. Bu önleme, konaktan ve ana bilgisayardan gelen tüm iletişimleri etkin bir şekilde engeller ve bir olay iş akışını tetikler. Araştırma ve ayrıntılı bir post-mordıtem analizi, ana nedenleri ve olası bir uzlaşma durumunu tespit etmek için yürütülür. Bu, yalnızca bir konağın düzeltilme ve müşteri iş yüklerine katılması ve müşteri iş yüklerini alma fırsatına sahip olmak için analiz tamamlandıktan sonra olur.

Aşağıda konak kanıtlama hizmetinin üst düzey mimarisi verilmiştir:

![Konak kanıtlama hizmeti mimarisini gösteren diyagram.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Kanıtlama ölçümleri

Aşağıda, bugün yakalanan birçok ölçüm örnekleri verilmiştir.

### <a name="secure-boot-and-secure-boot-keys"></a>Güvenli önyükleme ve güvenli önyükleme anahtarları
İmza veritabanının ve iptal edilen imzalar veritabanı kararlarının doğru olduğunu doğrulayarak, konak kanıtlama hizmeti istemci aracısının doğru yazılımın güvenilir olduğunu kabul eder. Ortak anahtar kayıt anahtarı veritabanı ve ortak platform anahtarının imzalarını doğrulayarak, konak kanıtlama hizmeti yalnızca güvenilen tarafların hangi yazılımın güvenilir kabul edildiği tanımlarını değiştirme iznine sahip olduğunu onaylar. Son olarak, güvenli önyüklemenin etkin olmasını sağlayarak konak kanıtlama hizmeti bu tanımlarının zorlandığını doğrular.

### <a name="debug-controls"></a>Hata ayıklama denetimleri
Hata ayıklayıcıları geliştiricilere yönelik güçlü araçlardır. Ancak, belleğe ve diğer hata ayıklama komutlarına sınırsız erişimi, güvenilir olmayan bir tarafa verildiyse veri koruma ve sistem bütünlüğünü zayıflatabilir. Konak kanıtlama hizmeti, üretim makinelerinde önyüklemede her türlü hata ayıklamayı devre dışı bırakma işlemini sağlar.

### <a name="code-integrity"></a>Kod bütünlüğü
UEFı [Güvenli önyükleme](secure-boot.md) , önyükleme sırası sırasında yalnızca güvenilen alt düzey yazılımların çalışmasını sağlar. Aynı denetim, ancak önyükleme öncesi ortamda, çekirdek modu erişimiyle sürücülere ve diğer yürütülebilir dosyalara da uygulanmalıdır. Bu uçta, geçerli ve geçersiz imzalar belirtilerek hangi sürücülerin, ikililerin ve diğer yürütülebilir dosyaların güvenilir kabul edileceğini tanımlamak için bir kod bütünlüğü (CI) ilkesi kullanılır. Bu ilkeler zorlanır. İlke ihlalleri, araştırma için güvenlik olay yanıtı ekibine uyarı oluşturur.

## <a name="next-steps"></a>Sonraki adımlar
Platform bütünlüğünü ve güvenliğini sağlamak için yaptığımız hakkında daha fazla bilgi edinmek için bkz.:

- [Üretici yazılımı güvenliği](firmware.md)
- [Güvenli önyükleme](secure-boot.md)
- [Project Cerberus](project-cerberus.md)
- [Bekleme sırasında şifreleme](encryption-atrest.md)
- [Hiper yönetici güvenliği](hypervisor.md)