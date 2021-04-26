---
title: Windows sanal masaüstü MALTı uygulama iliştirme sözlüğü-Azure
description: Bir MALTı uygulama sözlüğü, hüküm ve kavram ekler.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c5c596735ad91f38d5ba4217135a9373d2856182
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538450"
---
# <a name="msix-app-attach-glossary"></a>MSIX uygulama iliştirme sözlüğü

Bu makale, MSIX uygulama iliştirme ile ilgili anahtar hüküm ve kavramlar için bir tanım listesidir.

## <a name="msix-container"></a>MALTıKABı

MALTıLı bir kapsayıcı, MALTıLı uygulamaların çalıştırıldığı yerdir. Daha fazla bilgi için bkz. [Msix kapsayıcıları](/windows/msix/msix-container).

## <a name="msix-application"></a>MSIX uygulaması 

İçinde depolanan bir uygulama. MSIX dosyası.

## <a name="msix-package"></a>MSIX paketi 

Bir MSIX paketi, bir MSIX dosyası veya uygulamasıdır.

## <a name="msix-share"></a>MSIX paylaşma

MALTÝ Share, genişletilmiş MSIX paketlerini tutan bir ağ paylaşımıdır. MSIX paylaşımlarının SMB 3 veya sonraki bir sürümünü desteklemesi gerekir. Paylaşımlar, konak havuzu sistem hesabındaki sanal makineler (VM) için de erişilebilir olmalıdır. MSIX paketleri, uygulama dosyalarını sistem sürücüsüne taşımaya gerek kalmadan MSIX paylaşımından hazırlanmalıdır. 

## <a name="msix-image"></a>MSIX resmi

MSIX görüntüsü bir veya daha fazla MSIX paketlenmiş uygulama içeren bir VHD, VHDx veya CıM dosyasıdır. Her uygulama, MSıXMGR Aracı kullanılarak MSIX görüntüsünde dağıtılır.

## <a name="repackage"></a>Yeniden paketlemenize

Yeniden paketleme, MSIX olmayan bir uygulama alır ve MSIX paketleme aracı 'Nı (MPT) kullanarak MALTıYA dönüştürür. Daha fazla bilgi için bkz. [Msix paketleme aracına genel bakış](/windows/msix/packaging-tool/tool-overview).

## <a name="expand-an-msix-package"></a>Bir MSIX paketini Genişlet

Bir MSIX paketinin genişletilmesi çok adımlı bir işlemdir. Genişletme, MSIX dosyasını alır ve içeriğini bir VHD (x) veya CıM dosyasına koyar. 

Bir MSIX paketini genişletmek için:

1. Bir MSIX paketi (MSIX dosyası) alın.
2. MALTıDOSYAYı bir. zip dosyası olarak yeniden adlandırın.
3. Elde edilen. zip dosyasını bir klasörde açın.
4. Klasörle aynı büyüklükte bir VHD oluşturun.
5. VHD 'YI bağlayın.
6. Bir disk başlatın.
7. Bölüm oluşturun.
8. Bölümü biçimlendirin.
9. Sıkıştırunzip içeriğini VHD 'ye kopyalayın.
10. VHD 'nin içeriğine ACL 'Ler uygulamak için MSıXMGR aracını kullanın.
11. VHD (x) veya [CıM](#cim)'yi çıkarın.

## <a name="upload-an-msix-package"></a>Bir MSIX paketini karşıya yükle 

Bir MSIX paketinin karşıya yüklenmesi, MSIX paylaşımının genişletilmiş bir MSIX paketini içeren VHD (x) veya [CıM](#cim) 'yi karşıya yüklemeyi içerir.

Windows sanal masaüstü 'nde, karşıya yüklemeler MALTıYA her bir paylaşımdan bir kez gerçekleşir. Bir paketi karşıya yükledikten sonra, aynı abonelikteki tüm konak havuzları buna başvurabilir.

## <a name="add-an-msix-package"></a>MSIX paketi ekleme

Windows sanal masaüstü 'nde, bir MSIX paketi eklendiğinde onu bir konak havuzuna bağlar.

## <a name="publish-an-msix-package"></a>MSIX paketi yayımlama 

Windows sanal masaüstü 'nde yayınlanmış bir MSIX paketi bir Active Directory Etki Alanı hizmetine (AD DS) veya Azure Active Directory (Azure AD) kullanıcısına veya kullanıcı grubuna atanmalıdır.

## <a name="staging"></a>Hazırlama

Hazırlama iki şeyi içerir:

- VHD (x) veya [CIM](#cim) 'yi VM 'ye bağlama.
- İşletim sistemine MSIX paketinin kayıt için kullanılabilir olduğunu bildirme.

## <a name="registration"></a>Kayıt

Kayıt, kullanıcılarınız için hazırlanmış bir MSIX paketini kullanılabilir hale getirir. Kayıt, Kullanıcı başına esasına göre yapılır. Bu belirli kullanıcı için açıkça bir uygulama kaydetmediyseniz, uygulamayı çalıştıramazlar.

İki tür kayıt vardır: normal ve Gecikmeli.

### <a name="regular-registration"></a>Düzenli kayıt

Normal kayıtta, bir kullanıcıya atanan her uygulama tam olarak kaydedilir. Kayıt, kullanıcı oturumunda oturum açtığında gerçekleşir ve bu süre, Windows sanal masaüstü 'Nü kullanmaya başlamak için geçen süreyi etkileyebilir.

### <a name="delayed-registration"></a>Gecikmeli kayıt

Gecikmeli kayıtta, kullanıcıya atanan her uygulama yalnızca kısmen kaydedilir. Kısmi kayıt, Başlat menüsü kutucuğunun ve çift tıklama dosya ilişkilerinin kaydedildiği anlamına gelir. Kayıt, kullanıcı oturumunda oturum açtığında meydana gelir. bu nedenle, Windows sanal masaüstü kullanmaya başlamak için gereken süre üzerinde en az etkisi vardır. Kayıt yalnızca Kullanıcı uygulamayı MSIX paketinde çalıştırdığında tamamlanır.

Gecikmeli kayıt şu anda MSIX uygulama iliştirme için varsayılan yapılandırmadır.

## <a name="deregistration"></a>Eri

Kayıt silme, bir kullanıcı için kayıtlı ancak çalıştırmayan bir MSIX paketini kaldırır. Kayıt silme, Kullanıcı oturumunun oturumunu kapattığında oluşur. Kayıt silme sırasında, MALTıUYGULAMA iliştirme kullanıcıya özgü uygulama verilerini yerel kullanıcı profiline gönderir.

## <a name="destage"></a>Gerçekleştirilen

Kaldırma işlemi, işletim sistemini, şu anda çalışmayan bir MALTıYA da uygulamanın veya herhangi bir kullanıcı için hazırlanmamış olduğunu bildirir. Bu, işletim sistemindeki tüm başvuruyu kaldırır.

## <a name="cim"></a>CıM

. CıM, bileşik görüntü dosyaları sistemi (CimFS) ile ilişkili yeni bir dosya uzantısıdır. CıM dosyalarının bağlanması ve kaldırılması, VHD dosyalarının daha hızlıdır. CıM aynı zamanda VHD 'den daha az CPU ve bellek kullanır.

CıM dosyası, içeren bir dosyadır. Meta veriler ve gerçek verileri içeren en az altı ek dosya içeren CıM uzantısı. CıM dosyasındaki dosyaların uzantıları yok. Aşağıdaki tabloda, CıM içinde bulduğunuz örnek dosyaların listesi verilmiştir:

| Dosya adı | Dahili numara | Boyut |
|-----------|-----------|------|
| VSC | CıM | 1 KB |
| objectid_b5742e0b-1b98-40B3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40B3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40B3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40B3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40B3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40B3-94a6-9cb96f497e56_2 | NA | 264.132 KB |

Aşağıdaki tablo, VHD ve CimFS arasında bir performans karşılaştırmasına sahiptir. Bu sayılar, bir DSv4 makinesinde çalıştırılan her biçimdeki 500 300 MB dosya içeren bir test çalıştırmasının sonucudur.

|  Özellikler                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Ortalama bağlama süresi     | 356 MS                     | 255 MS      |
| Ortalama çıkarma zamanı   | 1615 MS                    | 36 MS       |
| Bellek tüketimi | %6 (8 GB)                      | %2 (8 GB)       |
| CPU (sayı ani)          | Birden çok kez yeniden gg | Etki yok |

## <a name="next-steps"></a>Sonraki adımlar

MSIX uygulama iliştirme hakkında daha fazla bilgi edinmek istiyorsanız [genel bakış](what-is-app-attach.md) ve [SSS bölümüne](app-attach-faq.md)göz atın. Aksi takdirde, [uygulama eklemeyi ayarlama](app-attach.md)ile çalışmaya başlayın.
