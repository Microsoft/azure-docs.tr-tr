---
title: Azure Güvenlik Merkezi 'nde uyarı doğrulaması (EICAR test dosyası) | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi'nde güvenlik uyarılarını doğrulamanıza yardımcı olur.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 709ac3a9f5e7cf0bd13a6e387f0b80caf2608fe0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748469"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde uyarı doğrulaması (EICAR test dosyası)
Bu belge, sisteminizin Azure Güvenlik Merkezi uyarıları için doğru yapılandırılıp yapılandırılmadığını doğrulamayı öğrenmenize yardımcı olur.

## <a name="what-are-security-alerts"></a>Güvenlik uyarıları nedir?
Uyarılar, güvenlik merkezi 'nin kaynaklarınızın tehditleri algıladığında oluşturduğu bildirimlerdir. Sorunu hızlı bir şekilde araştırmak için gereken bilgilerle birlikte uyarıları önceliklendirir ve listeler. Güvenlik Merkezi ayrıca bir saldırıyı nasıl düzeltebileceğiniz konusunda öneriler de sağlar.
Daha fazla bilgi için bkz. Güvenlik [Merkezi 'Nde güvenlik uyarıları](security-center-alerts-overview.md) ve [güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Uyarı doğrulaması

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## Windows VM 'lerinde uyarıları doğrulama<a name="validate-windows"></a>

Güvenlik Merkezi Aracısı bilgisayarınıza yüklendikten sonra, uyarının saldırıya uğrayan kaynak olmasını istediğiniz bilgisayarda bu adımları izleyin:

1. Bir yürütülebilir dosyayı (örneğin, **Calc. exe**) bilgisayarın masaüstüne veya sizin rahatınızın diğer dizinine kopyalayın ve **ASC_AlertTest_662jfi039N. exe**olarak yeniden adlandırın.
1. Komut istemi 'ni açın ve bu dosyayı aşağıdaki gibi bir bağımsız değişkenle (yalnızca sahte bir bağımsız değişken adı) yürütün: ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5-10 dakika bekleyin ve Güvenlik Merkezi Uyarılarını açın. Aşağıdaki [örneğe](#alert-validate) benzer bir uyarı görüntülenmelidir:

> [!NOTE]
> Windows için bu test uyarısını gözden geçirirken, alan **bağımsız değişkenlerinin denetim etkin** **olduğundan emin olun.** **Yanlış**ise, komut satırı bağımsız değişken denetimini etkinleştirmeniz gerekir. Etkinleştirmek için aşağıdaki komutu kullanın:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Linux VM 'lerinde uyarıları doğrulama<a name="validate-linux"></a>

Güvenlik Merkezi Aracısı bilgisayarınıza yüklendikten sonra, uyarının saldırıya uğrayan kaynak olmasını istediğiniz bilgisayarda bu adımları izleyin:
1. Bir yürütülebilir dosyayı uygun bir konuma kopyalayın ve **./asc_alerttest_662jfi039n**olarak yeniden adlandırın, örneğin:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Komut istemi 'ni açın ve bu dosyayı yürütün:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5-10 dakika bekleyin ve Güvenlik Merkezi Uyarılarını açın. Aşağıdaki [örneğe](#alert-validate) benzer bir uyarı görüntülenmelidir:

### Uyarı örneği<a name="alert-validate"></a>

![Uyarı doğrulama örneği](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## Kubernetes üzerinde uyarıları doğrulama<a name="validate-kubernetes"></a>

Azure Kubernetes hizmetini tümleştirme güvenlik merkezi önizleme özelliğini kullanıyorsanız, uyarılarınızın çalıştığını sınamak için aşağıdaki kubectl komutunu çalıştırın:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Azure Kubernetes hizmeti ve Azure Güvenlik Merkezi 'nin tümleştirmesi hakkında daha fazla bilgi için [Bu makaleye](azure-kubernetes-service-integration.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede uyarıları doğrulama işlemine giriş yaptınız. Artık bu doğrulama hakkında bilgi sahibi olduğunuza göre, aşağıdaki makaleleri deneyebilirsiniz:

* [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) -Güvenlik Merkezi 'nde uyarıları yönetme ve güvenlik olaylarına yanıt verme hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'Nde güvenlik durumu izleme](security-center-monitoring.md) -Azure kaynaklarınızın sistem durumunu izlemeyi öğrenin.
* [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını anlama](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) -farklı güvenlik uyarısı türleri hakkında bilgi edinin.
* [Azure Güvenlik Merkezi sorun giderme kılavuzu](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) -Güvenlik Merkezi 'nde sık karşılaşılan sorunları giderme hakkında bilgi edinin.
* [Azure Güvenlik Merkezi hakkında SSS](security-center-faq.md) -hizmeti kullanma hakkında sık sorulan soruları bulun.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/) -Azure güvenliği ve uyumluluğu ile ilgili blog gönderilerini bulun.
