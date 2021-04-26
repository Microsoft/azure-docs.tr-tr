---
title: Windows sanal masaüstü-Azure 'da Microsoft Office FSLogix uygulama kapsayıcıları 'nı yükler
description: Windows sanal masaüstü 'nde Office ile bir FSLogix uygulama kapsayıcısı oluşturmak için uygulama kuralı Düzenleyicisi 'ni kullanma.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a144adcfbf6c7cefc6b946f95bdb734868de801f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446817"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>FSLogix uygulama kapsayıcılarını kullanarak Microsoft Office yükler

Bir FSLogix uygulama kapsayıcısını konak havuzunuzdaki diğer sanal makineler (VM 'Ler) için şablon olarak kullanarak hızlı ve verimli bir şekilde Microsoft Office yükleyebilirsiniz.

Aşağıda, bir FSLogix uygulama kapsayıcısının kullanılması yüklemenin daha hızlı olmasına yardımcı olabilir:

- Office uygulamalarınızı bir uygulama kapsayıcısına devreetmek, C sürücüsü boyutunuz için gereksinimleri azaltır.
- SANAL makinenizin anlık görüntüleri veya yedeklemeleri daha az kaynak alır.
- Tek bir görüntünün güncelleştirilmesi yoluyla otomatikleştirilmiş bir işlem hattının olması, sanal makinelerinizin güncelleştirilmesini kolaylaştırır.
- Windows sanal masaüstü dağıtımınızdaki tüm VM 'lere Office (ve diğer uygulamalar) yüklemek için yalnızca bir görüntüye ihtiyacınız vardır.

Bu makalede, bir FSLogix uygulama kapsayıcısının Office ile nasıl ayarlanacağı gösterilmektedir.

## <a name="requirements"></a>Gereksinimler

Kural düzenleyicisini ayarlamak için aşağıdaki işlemleri yapmanız gerekir:

- Office yüklü olmayan Windows çalıştıran bir VM
- Office 'in bir kopyası
- dağıtımınızda yüklü olan FSLogix 'in bir kopyası
- Konak Havuzunuzdaki tüm VM 'Lerde salt okuma erişimi olan bir ağ paylaşımının

## <a name="install-office"></a>Office 'i yükler

VHD veya VHDX 'e Office yüklemek için sanal makinenizde Uzak Masaüstü Protokolü etkinleştirin ve ardından, [BIR VHD ana görüntüsüne Office 'ı yüklemek](install-office-on-wvd-master-image.md)içindeki yönergeleri izleyin. Yüklerken, [doğru lisansları](overview.md#requirements)kullandığınızdan emin olun.

>[!NOTE]
>Windows sanal masaüstü, bilgisayar etkinleştirmeyi (SCA) gerektirir.

## <a name="install-fslogix"></a>FSLogix 'i yükler

FSLogix ve kural Düzenleyicisi 'ni yüklemek için [Fslogix indirme ve yükleme](/fslogix/install-ht)bölümündeki yönergeleri izleyin.

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Office 'i depolamak için bir VHD oluşturma ve hazırlama

Daha sonra, üzerinde kural düzenleyicisini kullanmak için bir VHD görüntüsü oluşturmanız ve hazırlamanız gerekir:

1. Yönetici olarak bir komut istemi açın. ve şu komutu çalıştırın:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Bu komutta gördüğünüz boş alanları kaydettiğinizden emin olun.

2. Sonra, aşağıdaki komutu çalıştırın:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Hizmeti bulursanız, adım 3 ' te devam etmeden önce VM 'yi yeniden başlatın.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Bundan sonra, **Program Files**  >  **fslogix**  >  **uygulamalarına** gidin ve hedef VHD 'yi oluşturmak için aşağıdaki komutu çalıştırın:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    Bu komutla oluşturduğunuz VHD, C: \\ Program Files \\ Microsoft Office klasörünü içermelidir.

    >[!NOTE]
    >Herhangi bir hata görürseniz Office 'i kaldırın ve adım 1 ' den baştan başlayın.

## <a name="configure-the-rule-editor"></a>Kural düzenleyicisini yapılandırma

Görüntünüzü hazırladığınıza göre, kural düzenleyicisini yapılandırmanız ve kurallarınızın depolanması için bir dosya oluşturmanız gerekir.

1. **Program Files**  >  **fslogix**  >  **uygulamalarına** gidin ve **RuleEditor.exe** çalıştırın.

2.   >    >  Yeni bir kural kümesi **oluşturmak** için dosya yeni oluştur ' u seçin, ardından bu kural kümesini yerel bir klasöre kaydedin.

3. **Boş kural kümesi**' ni seçin ve ardından **Tamam**' ı seçin.

4. **+** düğmesini seçin. Bu işlem **Kural Ekle** penceresini açar. Bu, **Kural Ekle** iletişim kutusundaki seçenekleri değiştirir.

5. Açılan menüden **uygulama kapsayıcısı (VHD) kuralı**' nı seçin.

6. **Klasör** alanına **\\ \\ Microsoft Office C: Program Files** yazın.

7. **Disk dosyası** alanı için, **hedef VHD oluştur** bölümünden **\<path\> \\ Office. vhd** ' yi seçin.

8. **Tamam**’ı seçin.

9. **C: \\ Users \\ \<username\> \\ belgeleri \\ fslogix kural kümelerinde** çalışma klasörüne gidin ve. FRx ve. fxa dosyalarını arayın. Kuralların çalışmaya başlaması için bu dosyaları **C: \\ Program Files \\ fslogix \\ Apps \\ kuralları** konumunda bulunan Rules klasörüne taşımanız gerekir.

10. Kuralların etkili olması için **kuralları sisteme Uygula** ' yı seçin.

     >[!NOTE]
     > Tüm oturum konaklarına ihtiyaç duyacağınız uygulama kuralı dosyalarını uygulamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

FSLogix hakkında daha fazla bilgi edinmek istiyorsanız [fslogix belgelerinize](/fslogix/)göz atın.