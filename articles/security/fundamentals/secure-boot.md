---
title: Bellenim güvenli önyükleme-Azure güvenliği
description: Azure bellenim güvenli önyüklemesinde teknik genel bakış.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94558011"
---
# <a name="secure-boot"></a>Güvenli Önyükleme

Güvenli önyükleme, yüklemeden önce tüm düşük düzey bellenim ve yazılım bileşenlerinin doğrulanmasını gerektiren [Birleşik Genişletilebilir Bellenim Arabirimi](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) özelliğidir. Önyükleme sırasında UEFı güvenli önyükleme, UEFı bellenim sürücüleri (seçenek ROM 'ları olarak da bilinir), Genişletilebilir Bellenim Arabirimi (EFı) uygulamaları ve işletim sistemi sürücüleri ve ikili dosyaları dahil olmak üzere her bir önyükleme yazılımı parçasının imzasını denetler. İmzalar özgün donanım üreticisi (OEM) tarafından geçerliyse veya güveniliyorsa, makine önyüklenir ve bellenim işletim sistemine denetim sağlar.

## <a name="components-and-process"></a>Bileşenler ve işlemler

Güvenli önyükleme şu kritik bileşenleri kullanır:

- Platform anahtarı (PK)-Platform sahibi (Microsoft) ve üretici yazılımı arasında güven oluşturur. Ortak yarı, PIN ve özel yarı ise Pprıv 'dir.
- Anahtar kayıt anahtarı veritabanı (KEK)-işletim sistemi ve platform üretici yazılımı arasında güven oluşturur. Ortak yarı, KEKpub ve özel yarı ise Kekprıv 'dir.
- İmza veritabanı (DB)-platform bellenimi ile etkileşime geçmek için yetkilendirilmiş bellenim ve yazılım kodu modüllerinin güvenilen imzalayanlar (ortak anahtarlar ve sertifikalar) için özetleri 'leri barındırır.
- İptal edilen imzalar veritabanı (dbx) – kötü amaçlı, güvenlik açığı bulunan, güvenliği aşılmış veya güvenilmeyen olarak tanımlanmış kod modüllerinin iptal edildiğini tutar. Bir karma imza DB ve iptal edilen imzalar DB 'de ise, iptal edilen imzalar veritabanı bu şekilde belirlenir.

Aşağıdaki şekil ve işlem bu bileşenlerin nasıl güncelleştirileceğini açıklar:

![Güvenli önyükleme bileşenlerini gösteren diyagram.](./media/secure-boot/secure-boot.png)

OEM, üretim sırasında makinenin kalıcı RAM (NV-RAM) üzerinde güvenli önyükleme özetleri depolar.

1. İmza veritabanı (DB), UEFı uygulamalarının, işletim sistemi yükleyicilerinin (Microsoft Işletim sistemi yükleyicisi veya önyükleme yöneticisi gibi) ve güvenilen UEFı sürücülerinden oluşan hedef veya görüntü karmaları ile doldurulur.
2. İptal edilen imzalar veritabanı (dbx), artık güvenilir olmayan modüllerin derinlemesine doldurulmuş halidir.
3. Anahtar kayıt anahtarı (KEK) veritabanı, imza veritabanını güncelleştirmek ve imzaları veritabanını iptal etmek için kullanılabilecek imzalama anahtarlarıyla doldurulur. Veritabanları, doğru anahtarla imzalanmış güncelleştirmeler aracılığıyla veya bellenim menülerini kullanarak fiziksel olarak bulunan yetkili bir kullanıcı tarafından güncelleştirmeler aracılığıyla düzenlenebilir.
4. DB, DBX ve KEK veritabanları eklendikten ve son bellenim doğrulaması tamamlandıktan ve test tamamlandıktan sonra OEM, üretici yazılımını düzenlemeden kilitler ve bir platform anahtarı (PK) oluşturur. PK, KEK güncelleştirmelerini imzalamak veya güvenli önyüklemeyi devre dışı bırakmak için kullanılabilir.

Önyükleme işlemindeki her aşamada, üretici yazılımının, önyükleme yükleyicilerinin, işletim sisteminin, çekirdek sürücülerin ve diğer önyükleme zinciri yapılarının özetleri hesaplanır ve kabul edilebilir değerlerle karşılaştırılır. Güvenilmeyen olarak bulunan bellenim ve yazılımların yüklenmesine izin verilmez. Bu nedenle, düşük düzey kötü amaçlı yazılım ekleme veya önyükleme öncesi kötü amaçlı yazılım saldırıları engellenebilir.

## <a name="secure-boot-on-the-azure-fleet"></a>Azure filo 'de güvenli önyükleme
Günümüzde, eklendi ve Azure işlem filklerine, müşteri iş yüklerini barındırmak için dağıtılan tüm makineler, güvenli önyükleme etkin olan fabrika katlarından gelir. Güvenli önyükleme etkinleştirmesi 'nin yanlışlıkla veya kötü amaçlı olarak döndürülmediğinden emin olmak için, donanım oluşturma ve tümleştirme ardışık düzeninde her aşamada hedeflenen araç ve süreçler yerinde yapılır.

DB ve dbx dıgests 'nin doğru olduğu doğrulanıyor:

- Önyükleme yükleyicisi, DB girişlerinden birinde bulunur
- Önyükleme yükleyicisine ait imza geçerli
- Güvenilen yazılımlarla konak önyüklemeleri

 KEKpub ve PKpub imzalarını doğrulayarak, yalnızca güvenilen tarafların hangi yazılımın güvenilir kabul edildiği tanımlarını değiştirme izni olduğunu doğrulayabiliriz. Son olarak, güvenli önyüklemenin etkin olmasını sağlayarak bu tanımların zorlandığını doğrulayabiliriz.

## <a name="next-steps"></a>Sonraki adımlar
Platform bütünlüğünü ve güvenliğini sağlamak için yaptığımız hakkında daha fazla bilgi edinmek için bkz.:

- [Üretici yazılımı güvenliği](firmware.md)
- [Ölçülen önyükleme ve konak kanıtlama](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Bekleme sırasında şifreleme](encryption-atrest.md)
- [Hiper yönetici güvenliği](hypervisor.md)