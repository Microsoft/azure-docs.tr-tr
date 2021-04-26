---
title: Azure Otomasyonu 'nda Değişiklik İzleme ve stoku yönetme
description: Bu makalede, ortamınızdaki yazılım ve Microsoft hizmet değişikliklerini izlemek için Değişiklik İzleme ve envanterin nasıl kullanılacağı açıklanır.
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: dff314f3c9fb72c565a7c2d522694d533c487895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572642"
---
# <a name="manage-change-tracking-and-inventory"></a>Değişiklik İzleme ve Stok yönetimi

İzlemek üzere yeni bir dosya veya kayıt defteri anahtarı eklediğinizde Azure Otomasyonu, [değişiklik izleme ve envanter](overview.md)için bunu sağlar. Bu makalede izleme yapılandırma, izleme sonuçlarının incelenmesi ve değişiklikler algılandığında uyarıları işlemek açıklanır.

Bu makaledeki yordamları kullanmadan önce, aşağıdaki tekniklerden birini kullanarak sanal makinelerinizdeki Değişiklik İzleme ve envanteri etkinleştirdiğinizden emin olun:

* [Otomasyon hesabından Değişiklik İzleme ve Stok özelliğini etkinleştirme](enable-from-automation-account.md)
* [Azure portal göz atarak Değişiklik İzleme ve envanteri etkinleştirin](enable-from-portal.md)
* [Runbook’tan Değişiklik İzleme ve Stok özelliğini etkinleştirme](enable-from-runbook.md)
* [Azure VM’den Değişiklik İzleme ve Stok özelliğini etkinleştirme](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Dağıtımın kapsamını sınırlandırma

Değişiklik İzleme ve stok, değişiklikleri almak üzere bilgisayarları hedeflemek için çalışma alanındaki kapsam yapılandırmasını kullanır. Daha fazla bilgi için bkz. [Limit değişiklik izleme ve Inventory Deployment Scope](manage-scope-configurations.md).

## <a name="track-files"></a>Dosyaları izle

Dosya ve klasör/dizinlerdeki değişiklikleri izlemek için Değişiklik İzleme ve envanteri kullanabilirsiniz. Bu bölümde, Windows 'da ve Linux 'ta dosya izlemenin nasıl yapılandırılacağı açıklanır.

### <a name="configure-file-tracking-on-windows"></a>Windows 'da dosya izlemeyi yapılandırma

Windows bilgisayarlarda dosya izlemeyi yapılandırmak için aşağıdaki adımları kullanın:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Azure portal, **tüm hizmetler**' i seçin. Kaynak listesinde **Otomasyon** yazın. Yazmaya başladığınızda liste, giriş listenize göre öneriler uygular. **Automation Hesapları**’nı seçin.

3. Otomasyon hesapları listenizde, Değişiklik İzleme ve envanteri etkinleştirildiğinde seçtiğiniz hesabı seçin.

4. Otomasyon hesabınızda **yapılandırma yönetimi** altında **değişiklik izleme** ' yi seçin.

5. **Ayarları Düzenle** (dişli simgesi) seçeneğini belirleyin.

6. Çalışma alanı yapılandırması sayfasında **Windows dosyaları**' nı seçin ve ardından **+ Ekle** ' ye tıklayarak izlenecek yeni bir dosya ekleyin.

7. Değişiklik İzleme için Windows dosyası Ekle bölmesinde, izlenecek dosya veya klasör için bilgileri girin ve **Kaydet**' e tıklayın. Aşağıdaki tabloda, bilgiler için kullanabileceğiniz özellikler tanımlanmaktadır.

    |Özellik  |Açıklama  |
    |---------|---------|
    |Etkin     | Ayar uygulanmışsa true, aksi takdirde false.        |
    |Öğe Adı     | İzlenecek dosyanın kolay adı.        |
    |Grup     | Dosyaları mantıksal olarak gruplandırmak için bir grup adı.        |
    |Yolu girin     | Dosyanın denetlenecek yol (örneğin, **c:\Temp \\ \* . txt**). Ayrıca, gibi ortam değişkenlerini de kullanabilirsiniz `%winDir%\System32\\\*.*` .       |
    |Yol Türü     | Yolun türü. Olası değerler dosya ve klasördür.        |    
    |Özyineleme     | Bu öğeyi izlenecek öğe ararken, aksi takdirde false ise true.        |    
    |Dosya içeriğini karşıya yükle | İzlenen değişikliklerle dosya içeriğini karşıya yüklemek için true, aksi durumda false.|

    Joker karakterler kullanarak dosya ve klasörlerin izlenmesini yapılandırmayı planlıyorsanız şunları göz önünde bulundurun:

    - Birden çok dosyayı izlemek için joker karakterler gereklidir.
    - Joker karakterler yalnızca bir yolun son kesiminde kullanılabilir (örneğin, *C:\folder\file* veya */etc/*. conf *)
    - Bir ortam değişkeni geçerli olmayan bir yol içeriyorsa, doğrulama başarılı olur ancak sayım çalıştırıldığında yol başarısız olur.
    - Yolu ayarlarken, *c:*. * * gibi genel yollardan kaçının ve çok fazla klasör çapraz olarak sonuçlanır.

8. **Dosya Içeriğini karşıya yüklemek** Için true değerini belirttiğinizden emin olun. Bu ayar, belirtilen dosya yolu için dosya içeriği izlemeyi etkinleştirilir.

### <a name="configure-file-tracking-on-linux"></a>Linux 'ta dosya izlemeyi yapılandırma

Linux bilgisayarlarda dosya izlemeyi yapılandırmak için aşağıdaki adımları kullanın:

1. **Ayarları Düzenle** (dişli simgesi) seçeneğini belirleyin.

2. Çalışma alanı yapılandırma sayfasında, **Linux dosyaları**' nı seçin ve ardından **+ Ekle** ' yi seçerek izlenecek yeni bir dosya ekleyin.

3. **Değişiklik izleme Için Linux dosyası Ekle** sayfasında, izlenecek dosya veya dizin bilgilerini girin ve ardından **Kaydet**' i seçin. Aşağıdaki tabloda, bilgiler için kullanabileceğiniz özellikler tanımlanmaktadır.

    |Özellik  |Açıklama  |
    |---------|---------|
    |Etkin     | Ayar uygulanmışsa true, aksi takdirde false.        |
    |Öğe Adı     | İzlenecek dosyanın kolay adı.        |
    |Grup     | Dosyaları mantıksal olarak gruplandırmak için bir grup adı.        |
    |Yolu girin     | Dosyayı denetlenecek yol, örneğin, **/etc/*. conf**.       |
    |Yol Türü     | Yolun türü. Olası değerler dosya ve dizindir.        |
    |Özyineleme     | Bu öğeyi izlenecek öğe ararken, aksi takdirde false ise true.        |
    |Sudo Kullan     | Öğe denetlenirken sudo 'ı kullanmak için true, aksi durumda false.         |
    |Bağlantılar     | Dizinler arasında geçiş yaparken sembolik bağlantılarla nasıl başa çıkılacağını belirleyen ayar. Olası değerler şunlardır:<br> Ignore-sembolik bağlantıları yoksayar ve başvurulan dosya/dizinleri içermez.<br>Takip et-özyineleme sırasında sembolik bağlantıları Izler ve başvurulan dosya/dizinleri de içerir.<br>Yönet-sembolik bağlantıları Izler ve döndürülen içeriğin değiştirilmesine izin verir.<br>**Note:** Dosya içeriğini almayı desteklemediğinden Yönet seçeneği önerilmez.    |
    |Dosya içeriğini karşıya yükle | İzlenen değişikliklerle dosya içeriğini karşıya yüklemek için true, aksi durumda false. |

4. **Dosya Içeriğini karşıya yüklemek** Için **true değerini** belirttiğinizden emin olun. Bu ayar, belirtilen dosya yolu için dosya içeriği izlemeyi etkinleştirilir.

   ![Linux dosyası Ekle](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Dosya içeriğini izleme

Dosya içeriği izleme, izlenen bir değişiklikten önceki ve sonraki bir dosyanın içeriğini görüntülemenize izin verir. Özellik, her değişiklik oluştuktan sonra dosya içeriğini bir [depolama hesabına](../../storage/common/storage-account-overview.md) kaydeder. Dosya içeriğini izlemek için izlenecek bazı kurallar aşağıda verilmiştir:

* Dosya içeriğini depolamak için Kaynak Yöneticisi dağıtım modelini kullanan standart bir depolama hesabı gerekir.
* Varsayılan olarak, depolama hesapları herhangi bir ağ üzerindeki istemcilerden gelen bağlantıları kabul eder. Depolama hesabınızı yalnızca belirli trafiğe izin verecek şekilde güvenli hale getirmeniz durumunda, Otomasyon hesabınızın buna bağlanmasına izin vermek için yapılandırma kurallarınızı değiştirmeniz gerekir. Bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../../storage/common/storage-network-security.md).
* Premium ve klasik dağıtım modeli depolama hesapları kullanmayın. Bkz. [Azure depolama hesapları hakkında](../../storage/common/storage-account-create.md).
* Depolama hesabını yalnızca bir Otomasyon hesabına bağlayabilirsiniz.
* Otomasyon hesabınızda Değişiklik İzleme ve envanter etkinleştirilmelidir.

### <a name="enable-tracking-for-file-content-changes"></a>Dosya içeriği değişiklikleri için izlemeyi etkinleştir

Dosya içeriklerinde yapılan değişikliklere yönelik izlemeyi etkinleştirmek için aşağıdaki adımları kullanın:

1. **Ayarları Düzenle** (dişli simgesi) seçeneğini belirleyin.

2. **Dosya içeriği** ' ni seçin ve ardından **bağlantı**' yı seçin. Bu seçim, **değişiklik izleme Için Içerik konumu Ekle** sayfasını açar.

   ![İçerik konumu Ekle](./media/manage-change-tracking/enable.png)

3. Dosya içeriğini depolamak için kullanılacak aboneliği ve depolama hesabını seçin.

5. Tüm mevcut izlenen dosyalar için dosya içeriği izlemeyi etkinleştirmek istiyorsanız, **Tüm ayarlar için dosya Içeriğini karşıya yükle** **' yi seçin.** Bu ayarı, her dosya yolu için daha sonra değiştirebilirsiniz.

   ![Depolama hesabı ayarla](./media/manage-change-tracking/storage-account.png)

6. Değişiklik İzleme ve Inventory, dosya içeriği değişiklik izlemeyi etkinleştirdiğinden depolama hesabı ve paylaşılan erişim Imzası (SAS) URI 'Lerini gösterir. İmzaların kullanım süreleri 365 gün sonra doluyor ve yeniden **Oluştur**' u seçerek yeniden oluşturabilirsiniz.

   ![Hesap anahtarlarını listeleme](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>İzlenen bir dosyanın içeriğini görüntüleme

Değişiklik İzleme ve envanter izlenen bir dosya için değişiklik algıladığında, değişiklik ayrıntıları bölmesinde dosya içeriğini görüntüleyebilirsiniz.  

![Liste değişiklikleri](./media/manage-change-tracking/change-list.png)

1. Otomasyon hesabınızdan **değişiklik izleme** sayfasında, değişiklikler listesinden bir dosya seçin ve sonra dosyanın içeriğini görmek Için **dosya içeriği değişikliklerini görüntüle** ' yi seçin. Değişiklik ayrıntıları bölmesi, her bir özellik için dosya bilgilerinin önüne ve sonrasına ilişkin standart bir araç görüntüler.

   ![Değişiklik ayrıntıları](./media/manage-change-tracking/change-details.png)

2. Dosya içeriğini yan yana görünümde görüntülüyorsunuz. Değişikliklerin satır içi görünümünü görmek için **satır içi** seçeneğini belirleyebilirsiniz.

## <a name="track-registry-keys"></a>Kayıt defteri anahtarlarını izleme

Windows bilgisayarlarda kayıt defteri anahtarı izlemeyi yapılandırmak için aşağıdaki adımları kullanın:

1. Otomasyon hesabınızdan **değişiklik izleme** sayfasında **Ayarları Düzenle** (dişli simgesi) seçeneğini belirleyin.

2. Çalışma alanı yapılandırması sayfasında **Windows kayıt defteri**' ni seçin.

3. İzlemek üzere yeni bir kayıt defteri anahtarı eklemek için **+ Ekle** ' yi seçin.

4. **Değişiklik izleme Için Windows kayıt defteri Ekle** sayfasında, izlenecek anahtarın bilgilerini girin ve ardından **Kaydet**' i seçin. Aşağıdaki tabloda, bilgiler için kullanabileceğiniz özellikler tanımlanmaktadır. Bir kayıt defteri yolu belirtirken, bir değer değil, anahtar olması gerekir.

    |Özellik  |Açıklama  |
    |---------|---------|
    |Etkin     | Bir ayar uygulanmışsa true, aksi takdirde false.        |
    |Öğe Adı     | İzlenecek kayıt defteri anahtarının kolay adı.        |
    |Grup     | Kayıt defteri anahtarlarını mantıksal olarak gruplandırmak için Grup adı.        |
    |Windows Kayıt Defteri Anahtarı   | Yol içeren anahtar adı, örneğin, `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Değişiklik kayıtları için arama günlükleri

Değişiklik kayıtları için Azure Izleyici günlüklerinde çeşitli aramalar yapabilirsiniz. Değişiklik izleme sayfası açıkken, Günlükler sayfasını açmak için **Log Analytics** ' a tıklayın. Aşağıdaki tabloda değişiklik kayıtları için örnek günlük aramaları sunulmaktadır.

|Sorgu  |Description  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Otomatik olarak ayarlanan ancak durdurulma olarak bildirilen Microsoft Hizmetleri için en son envanter kayıtlarını gösterir. Sonuçlar, belirtilen yazılım adı ve bilgisayar için en son kayıtla sınırlıdır.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Kaldırılan yazılım için değişiklik kayıtlarını gösterir.|

## <a name="next-steps"></a>Sonraki adımlar

* Kapsam konfigürasyonları hakkında daha fazla bilgi için bkz. [Limit değişiklik izleme and Inventory Deployment Scope](manage-scope-configurations.md).
* Azure Izleyici günlüklerinde depolanan günlüklerde arama yapmanız gerekiyorsa bkz. [Azure Izleyici günlüklerinde günlük aramaları](../../azure-monitor/logs/log-query-overview.md).
* Dağıtımlar ile tamamlandıysa, bkz. [değişiklik izleme ve envanteri kaldırma](remove-feature.md).
* VM 'lerinizi Değişiklik İzleme ve envanterden silmek için bkz. [VM 'leri değişiklik izleme ve envanterden kaldırma](remove-vms-from-change-tracking.md).
* Özellik hatalarını gidermek için bkz. [değişiklik izleme ve envanter sorunlarını giderme](../troubleshoot/change-tracking.md).
