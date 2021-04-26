---
title: Azure Güvenlik Merkezi 'nde uyarı doğrulaması | Microsoft Docs
description: Azure Güvenlik Merkezi 'nde güvenlik uyarılarınızın doğru şekilde yapılandırıldığını nasıl doğrulayacağınızı öğrenin
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: b90ca39d7bf01b84400464240bb581a5e7bc922a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602392"
---
# <a name="alert-validation-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde uyarı doğrulama
Bu belge, sisteminizin Azure Güvenlik Merkezi uyarıları için doğru yapılandırılıp yapılandırılmadığını doğrulamayı öğrenmenize yardımcı olur.

## <a name="what-are-security-alerts"></a>Güvenlik uyarıları nedir?
Uyarılar, Güvenlik Merkezi’nin kaynaklarınızda tehditler algıladığında oluşturduğu bildirimlerdir. Sorunu hızlı bir şekilde araştırmak için gereken bilgilerle birlikte uyarıları önceliklendirir ve listeler. Güvenlik Merkezi ayrıca bir saldırıyı nasıl düzeltebileceğiniz konusunda öneriler sağlar.
Daha fazla bilgi için bkz. Güvenlik [Merkezi 'Nde güvenlik uyarıları](security-center-alerts-overview.md) ve [güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)


## <a name="generate-sample-azure-defender-alerts"></a>Örnek Azure Defender uyarıları oluşturma

[Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve bunlara yanıt verme](security-center-managing-and-responding-alerts.md)bölümünde açıklandığı gibi yeni, önizleme uyarıları deneyimini kullanıyorsanız, Azure Portal güvenlik uyarıları sayfasında birkaç tıklamayla örnek uyarılar oluşturabilirsiniz.

Örnek uyarıları kullanarak şunları yapın:

- Azure Defender 'ın değer ve yeteneklerini değerlendirin
- Güvenlik uyarılarınız için yaptığınız tüm yapılandırmaların (SıEM tümleştirmeleri, iş akışı otomasyonu ve e-posta bildirimleri gibi) doğrulanması

Örnek uyarılar oluşturmak için:

1. Rol **Güvenlik Yöneticisi** veya **abonelik katılımcısı** olan bir kullanıcı olarak, uyarılar sayfasındaki araç çubuğundan **örnek Uyarılar Oluştur**' u seçin.
1. Aboneliği seçin.
1. Uyarılarını görmek istediğiniz ilgili Azure Defender planını seçin. 
1. **Örnek Uyarılar Oluştur**' u seçin.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Azure Güvenlik Merkezi 'nde örnek uyarılar oluşturma adımları":::
    
    Örnek uyarıların oluşturulduğunu bildiren bir bildirim görüntülenir:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Örnek uyarıların oluşturulmakta olduğunu belirten bildirim.":::

    Birkaç dakika sonra uyarılar güvenlik uyarıları sayfasında görüntülenir. Ayrıca, Azure Güvenlik Merkezi güvenlik uyarılarınızı (bağlı Sıems, e-posta bildirimleri vb.) almak için yapılandırdığınız her yerde de görünürler.

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Güvenlik uyarıları listesindeki örnek uyarılar":::

    > [!TIP]
    > Uyarılar, sanal kaynaklara yöneliktir.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Azure sanal makinelerinizdeki uyarıların benzetimini yapın (Windows) <a name="validate-windows"></a>

Güvenlik Merkezi Aracısı bilgisayarınıza yüklendikten sonra, uyarının saldırıya uğrayan kaynak olmasını istediğiniz bilgisayarda bu adımları izleyin:

1. Bir yürütülebilir dosyayı (örneğin **calc.exe**) bilgisayarın masaüstüne veya sizin rahatınızın diğer dizinine kopyalayın ve **ASC_AlertTest_662jfi039N.exe** olarak yeniden adlandırın.
1. Komut istemi ' ni açın ve bu dosyayı aşağıdaki gibi bir bağımsız değişkenle (yalnızca sahte bir bağımsız değişken adı) yürütün: ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5-10 dakika bekleyin ve Güvenlik Merkezi Uyarılarını açın. Bir uyarı görüntülenmelidir.

> [!NOTE]
> Windows için bu test uyarısını gözden geçirirken, alan **bağımsız değişkenlerinin denetim etkin** **olduğundan emin olun.** **Yanlış** ise, komut satırı bağımsız değişken denetimini etkinleştirmeniz gerekir. Etkinleştirmek için şu komutu kullanın: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Azure sanal makinelerinizdeki uyarıların benzetimini yapma (Linux) <a name="validate-linux"></a>

Güvenlik Merkezi Aracısı bilgisayarınıza yüklendikten sonra, uyarının saldırıya uğrayan kaynak olmasını istediğiniz bilgisayarda bu adımları izleyin:
1. Bir yürütülebilir dosyayı uygun bir konuma kopyalayın ve **./asc_alerttest_662jfi039n** olarak yeniden adlandırın, örneğin:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Komut istemi 'ni açın ve bu dosyayı yürütün:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5-10 dakika bekleyin ve Güvenlik Merkezi Uyarılarını açın. Bir uyarı görüntülenmelidir.


## <a name="simulate-alerts-on-kubernetes"></a>Kubernetes üzerinde uyarıların benzetimini yapma <a name="validate-kubernetes"></a>

Azure Kubernetes hizmetini Güvenlik Merkezi ile tümleştirdiyseniz, uyarılarınızın aşağıdaki kubectl komutuyla çalıştığını test edebilirsiniz:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Kubernetes düğümlerini ve kümelerini erteleme hakkında daha fazla bilgi için bkz. [Kubernetes Için Azure Defender 'A giriş](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede uyarıları doğrulama işlemine giriş yaptınız. Artık bu doğrulama hakkında bilgi sahibi olduğunuza göre, aşağıdaki makaleleri deneyebilirsiniz:

* [Azure Güvenlik Merkezi'nde Azure Key Vault Tehdit Algılamasını Doğrulama](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -Güvenlik Merkezi 'nde uyarıları yönetme ve güvenlik olaylarına yanıt verme hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını anlama](./security-center-alerts-overview.md) -farklı güvenlik uyarısı türleri hakkında bilgi edinin.