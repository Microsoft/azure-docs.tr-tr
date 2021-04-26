---
title: Kaynak adlandırma kısıtlamaları
description: Azure kaynaklarını adlandırmayla ilgili kuralları ve kısıtlamaları gösterir.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: e260c9055b26d82f2fd2f8458d287a35a838f40f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477800"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure kaynaklarına yönelik adlandırma kuralları ve kısıtlamaları

Bu makalede, Azure kaynakları için adlandırma kuralları ve kısıtlamalar özetlenmektedir. Kaynakları adlandırma hakkında öneriler için bkz. [Önerilen adlandırma ve etiketleme kuralları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Bu makalede kaynak sağlayıcısı ad alanına göre kaynaklar listelenir. Kaynak sağlayıcılarının Azure hizmetleriyle nasıl eşleşeceğini gösteren bir liste için bkz. [Azure hizmetleri Için kaynak sağlayıcıları](azure-services-resource-providers.md).

Geçerli karakterler sütununda belirtilmedikçe, kaynak adları büyük/küçük harfe duyarlıdır.

Aşağıdaki tablolarda, alfasayısal terimi şu şekilde ifade eder:

* **a** - **z** (küçük harfler)
* **A** - **Z** (büyük harfler)
* **0** - **9** (sayı)

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | larý | kaynak grubu | 3-63 | Küçük harfler ve rakamlar.<br><br>Küçük harfle başlayın. |

## <a name="microsoftapimanagement"></a>Microsoft. Apimanane

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | hizmet | global | 1-50 | Alfasayısal ve kısa çizgi.<br><br>Mektupla başlayın ve alfasayısal ile bitemez. |
> | hizmet/API 'ler | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API 'ler/sorunlar | api | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API 'ler/sorunlar/ekler | veriliş | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API 'ler/sorunlar/açıklamalar | veriliş | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API 'ler/işlemler | api | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API 'ler/işlemler/Etiketler | operation | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API 'ler/yayınlar | api | 1-80 | Alfasayısal, alt çizgi ve kısa çizgi.<br><br>Alfasayısal veya alt çizgi ile başlatın ve sonlandırın. |
> | hizmet/API 'ler/şemalar | api | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API/tagDescriptions | api | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API 'ler/Etiketler | api | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/API-sürüm-ayarlar | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/authorizationServers | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/arka uçlar | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/sertifikalar | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/Tanılamalar | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/gruplar | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/gruplar/kullanıcılar | group | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/IdentityProviders | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/Günlükçüler | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/bildirimler | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/bildirimler/recipientEmails | bildirim | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | Service/Openıdconnectproviders | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/ilkeler | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/ürünler | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/ürünler/API 'ler | ürün | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/ürünler/gruplar | ürün | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/ürünler/Etiketler | ürün | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/Özellikler | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/abonelikler | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/Etiketler | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/Şablonlar | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |
> | hizmet/kullanıcılar | hizmet | 1-256 | Şu kullanılamıyor:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Configurationmağazaların | kaynak grubu | 5-50 | Alfasayısal, alt çizgi ve kısa çizgi. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | kaynaktaki | atama kapsamı | 1-90 | Alfasayısal, nokta, alt çizgi, kısa çizgi ve parantez.<br><br>Süre içinde bitemez. |
> | Poliyasatamaları | atama kapsamı | 1-128 görünen adı<br><br>1-64 kaynak adı<br><br>Yönetim grubu kapsamındaki 1-24 kaynak adı | Görünen ad herhangi bir karakter içerebilir.<br><br>Kaynak adı `%` , nokta veya boşluk içeremez ve bitemez. |
> | policyDefinitions | Tanım kapsamı | 1-128 görünen adı<br><br>1-64 kaynak adı | Görünen ad herhangi bir karakter içerebilir.<br><br>Kaynak adı `%` , nokta veya boşluk içeremez ve bitemez. |
> | policySetDefinitions | Tanım kapsamı | 1-128 görünen adı<br><br>1-64 kaynak adı<br><br>Yönetim grubu kapsamındaki 1-24 kaynak adı | Görünen ad herhangi bir karakter içerebilir.<br><br>Kaynak adı `%` , nokta veya boşluk içeremez ve bitemez.  |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | automationAccounts | kaynak grubu & bölgesi <br>(Aşağıdaki nota bakın) | 6-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektupla başlayın ve alfasayısal ile bitemez. |
> | automationAccounts/Certificates | Otomasyon hesabı | 1-128 | Şu kullanılamıyor:<br> `<>*%&:\?.+/` <br><br>Boşluk ile bitemez.  |
> | automationAccounts/Connections | Otomasyon hesabı | 1-128 | Şu kullanılamıyor:<br> `<>*%&:\?.+/` <br><br>Boşluk ile bitemez. |
> | automationAccounts/kimlik bilgileri | Otomasyon hesabı | 1-128 | Şu kullanılamıyor:<br> `<>*%&:\?.+/` <br><br>Boşluk ile bitemez. |
> | automationAccounts/runbook 'lar | Otomasyon hesabı | 1-63 | Alfasayısal, alt çizgi ve kısa çizgi.<br><br>Mektup ile başlayın.  |
> | automationAccounts/zamanlamalar | Otomasyon hesabı | 1-128 | Şu kullanılamıyor:<br> `<>*%&:\?.+/` <br><br>Boşluk ile bitemez. |
> | automationAccounts/değişkenleri | Otomasyon hesabı | 1-128 | Şu kullanılamıyor:<br> `<>*%&:\?.+/` <br><br>Boşluk ile bitemez. |
> | automationAccounts/izleyicileri | Otomasyon hesabı | 1-63 |  Alfasayısal, alt çizgi ve kısa çizgi.<br><br>Mektup ile başlayın. |
> | automationAccounts/Web kancaları | Otomasyon hesabı | 1-128 | Şu kullanılamıyor:<br> `<>*%&:\?.+/` <br><br>Boşluk ile bitemez. |

> [!NOTE]
> Otomasyon hesabı adları, bölge ve kaynak grubu başına benzersizdir. Silinen Otomasyon hesaplarının adları hemen kullanılamayabilir.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3-24 | Küçük harfler ve rakamlar. |
> | batchAccounts/uygulamalar | Batch hesabı | 1-64 | Alfasayısal, alt çizgi ve kısa çizgi. |
> | batchAccounts/sertifikalar | Batch hesabı | 5-45 | Alfasayısal, alt çizgi ve kısa çizgi. |
> | batchAccounts/havuzlar | Batch hesabı | 1-64 | Alfasayısal, alt çizgi ve kısa çizgi. |

## <a name="microsoftblockchain"></a>Microsoft. Blockzinciri

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | blockchainMembers | global | 2-20 | Küçük harfler ve rakamlar.<br><br>Küçük harfle başlayın. |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. |
> | botServices/kanallar | bot hizmeti | 2-64 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. |
> | botServices/Connections | bot hizmeti | 2-64 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. |
> | enterpriseChannels | kaynak grubu | 2-64 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. |

## <a name="microsoftcache"></a>Microsoft. Cache

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. Ardışık tireler kullanılamaz. |
> | Redsıs/firewallRules | Redis | 1-256 | Alfasayısal karakterler |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | lerinize | kaynak grubu | 1-260 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | Profiller/uç noktalar | global | 1-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Sertifikadüzenleri | kaynak grubu | 3-30 | Alfasayısal karakterler. |

## <a name="microsoftcognitiveservices"></a>Microsoft. Biliveservices

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | accounts | kaynak grubu | 2-64 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | availabilitySets | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi ile biter. |
> | diskEncryptionSets | kaynak grubu | 1-80 | Alfasayısal ve alt çizgi. |
> | disklerinden | kaynak grubu | 1-80 | Alfasayısal, alt çizgi ve kısa çizgi. |
> | Galeriler | kaynak grubu | 1-80 | Alfasayısal ve dönemler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | Galeriler/uygulamalar | galeri | 1-80 | Alfasayısal karakterler, kısa çizgiler ve dönemler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | Galeriler/uygulamalar/sürümler | uygulama | 32 bit tamsayı | Sayılar ve dönemler. |
> | Galeriler/görüntüler | galeri | 1-80 | Alfasayısal, alt çizgi, kısa çizgi ve nokta.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | Galeriler/resimler/sürümler | image | 32 bit tamsayı | Sayılar ve dönemler. |
> | images | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi ile biter. |
> | anlık görüntüler | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi ile biter. |
> | virtualMachines | kaynak grubu | 1-15 (Windows)<br>1-64 (Linux)<br><br>Aşağıdaki nota bakın. | Boşluk veya şu karakterleri kullanamaz:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Windows VM 'Leri, kısa çizgi ile nokta veya bitiş içeremez.<br><br>Linux VM 'Leri nokta veya kısa çizgi ile bitemez. |
> | virtualMachineScaleSets | kaynak grubu | 1-15 (Windows)<br>1-64 (Linux)<br><br>Aşağıdaki nota bakın. | Boşluk veya şu karakterleri kullanamaz:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Alt çizgi ile başlayamaz. Nokta veya kısa çizgi ile bitemez. |

> [!NOTE]
> Azure sanal makinelerinin iki ayrı adı vardır: kaynak adı ve ana bilgisayar adı. Portalda bir sanal makine oluşturduğunuzda, her iki ad için de aynı değer kullanılır. Yukarıdaki tablodaki kısıtlamalar ana bilgisayar adına yöneliktir. Gerçek kaynak adı en fazla 64 karakter uzunluğunda olabilir.

## <a name="microsoftcommunication"></a>Microsoft. Communication

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | communicationServices | global | 1-63 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |

## <a name="microsoftcontainerinstance"></a>Microsoft. Containerınstance

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Kapsayıcı grupları | kaynak grubu | 1-63 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Kısa çizgi ile başlayamaz veya bitemez. Ardışık kısa çizgiler kullanılamaz. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | kayıt | global | 5-50 | Alfasayısal karakterler. |
> | kayıt defterleri/buildTasks | registry | 5-50 | Alfasayısal karakterler. |
> | kayıt defterleri/buildTasks/Steps | Derleme görevi | 5-50 | Alfasayısal karakterler. |
> | kayıt defterleri/çoğaltmalar | registry | 5-50 | Alfasayısal karakterler. |
> | kayıt defterleri/Kapsameşlemler | registry | 5-50 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | kayıt defterleri/görevler | registry | 5-50 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | kayıt defterleri/belirteçler | registry | 5-50 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | kayıt defterleri/Web kancaları | registry | 5-50 | Alfasayısal karakterler. |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Managedkümeler | kaynak grubu | 1-63 | Alfasayısal, alt çizgi ve kısa çizgi.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | openShiftManagedClusters | kaynak grubu | 1-30 | Alfasayısal karakterler. |

## <a name="microsoftcustomerinsights"></a>Microsoft. Customerınsights

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | hub'lar | kaynak grubu | 1-64 | Alfasayısal karakterler.<br><br>Mektup ile başlayın.  |
> | Hub/authorizationPolicies | Hub | 1-50 | Alfasayısal, alt çizgi ve nokta.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | Hub 'lar/bağlayıcılar | Hub | 1-128 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub 'lar/bağlayıcılar/eşlemeler | bağlayıcı | 1-128 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub 'lar/etkileşimler | Hub | 1-128 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub/KPI | Hub | 1-512 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub 'lar/bağlantılar | Hub | 1-512 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub/tahmin | Hub | 1-512 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub 'lar/profiller | Hub | 1-128 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub/relationshipLinks | Hub | 1-512 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub 'lar/ilişkiler | Hub | 1-512 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub/Roleatamaları | Hub | 1-128 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |
> | Hub 'lar/görünümler | Hub | 1-512 | Alfasayısal ve alt çizgi.<br><br>Mektup ile başlayın. |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | içermektedir | kaynak grubu | 1-180 | Şu kullanılamıyor:<br>`%&\\?/`<br><br>Nokta veya boşluk ile bitemez. |
> | resourceProviders | kaynak grubu | 3-64 | Şu kullanılamıyor:<br>`%&\\?/`<br><br>Nokta veya boşluk ile bitemez. |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Çizelge | kaynak grubu | 3-24 | Alfasayısal, tire, alt çizgi ve nokta. |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | çalışma alanı | kaynak grubu | 3-30 | Alfasayısal, alt çizgi ve kısa çizgi |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | larının | global | 3-63 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | fabrika/veri akışları | Çar | 1-260 | Şu kullanılamıyor:<br>`<>*#.%&:\\+?/`<br><br>Alfasayısal olarak başlayın. |
> | fabrika/veri kümeleri | Çar | 1-260 | Şu kullanılamıyor:<br>`<>*#.%&:\\+?/`<br><br>Alfasayısal olarak başlayın. |
> | Fabrika/tümleştirme çalışma zamanları | Çar | 3-63 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | Fabrika/linkedservices | Çar | 1-260 | Şu kullanılamıyor:<br>`<>*#.%&:\\+?/`<br><br>Alfasayısal olarak başlayın. |
> | Fabrika/işlem hatları | Çar | 1-260 | Şu kullanılamıyor:<br>`<>*#.%&:\\+?/`<br><br>Alfasayısal olarak başlayın. |
> | Fabrika/Tetikleyiciler | Çar | 1-260 | Şu kullanılamıyor:<br>`<>*#.%&:\\+?/`<br><br>Alfasayısal olarak başlayın. |
> | Fabrika/Tetikleyiciler/yeniden yönlendirme Tetikleyicileri | Tetikleyicinin | 1-260 | Şu kullanılamıyor:<br>`<>*#.%&:\\+?/`<br><br>Alfasayısal olarak başlayın. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Küçük harfler ve rakamlar. |
> | hesaplar/computePolicies | account | 3-60 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | hesaplar/dataLakeStoreAccounts | account | 3-24 | Küçük harfler ve rakamlar. |
> | hesaplar/firewallRules | account | 3-50 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | hesaplar/storageAccounts | account | 3-60 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Küçük harfler ve rakamlar. |
> | hesaplar/firewallRules | account | 3-50 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | hesaplar/virtualNetworkRules | account | 3-50 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | services | kaynak grubu | 2-62 | Alfasayısal, kısa çizgi, nokta ve alt çizgi.<br><br>Alfasayısal olarak başlayın. |
> | Hizmetler/Projeler | hizmet | 2-57 | Alfasayısal, kısa çizgi, nokta ve alt çizgi.<br><br>Alfasayısal olarak başlayın. |

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | larý | global | 3-63 | Küçük harfler, tireler ve sayılar.<br><br>Kısa çizgi ile başlayamaz veya bitemez. |
> | sunucular/veritabanları | larý | 1-63 | Alfasayısal karakterler ve kısa çizgiler. |
> | sunucular/firewallRules | larý | 1-128 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | sunucular/virtualNetworkRules | larý | 1-128 | Alfasayısal karakterler ve kısa çizgiler. |

## <a name="microsoftdbformysql"></a>Microsoft. Dbformyısql

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | larý | global | 3-63 | Küçük harfler, tireler ve sayılar.<br><br>Kısa çizgi ile başlayamaz veya bitemez. |
> | sunucular/veritabanları | larý | 1-63 | Alfasayısal karakterler ve kısa çizgiler. |
> | sunucular/firewallRules | larý | 1-128 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | sunucular/virtualNetworkRules | larý | 1-128 | Alfasayısal karakterler ve kısa çizgiler. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | larý | global | 3-63 | Küçük harfler, tireler ve sayılar.<br><br>Kısa çizgi ile başlayamaz veya bitemez. |
> | sunucular/veritabanları | larý | 1-63 | Alfasayısal karakterler ve kısa çizgiler. |
> | sunucular/firewallRules | larý | 1-128 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | sunucular/virtualNetworkRules | larý | 1-128 | Alfasayısal karakterler ve kısa çizgiler. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Iothubs | global | 3-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Kısa çizgi ile bitemez. |
> | IotHubs/sertifikalar | IoT Hub 'ı | 1-64 | Alfasayısal, kısa çizgi, nokta ve alt çizgi. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Alfasayısal, kısa çizgi, nokta ve alt çizgi. |
> | provisioningServices | kaynak grubu | 3-64 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal ile biter. |
> | provisioningServices/sertifikalar | provisioningServices | 1-64 | Alfasayısal, kısa çizgi, nokta ve alt çizgi. |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | larda | kaynak grubu | 1-50 | Alfasayısal, alt çizgi ve kısa çizgi. |
> | Labs/CustomImages | ın | 1-80 | Alfasayısal, alt çizgi, kısa çizgi ve parantez. |
> | Labs/formüller | ın | 1-80 | Alfasayısal, alt çizgi, kısa çizgi ve parantez. |
> | Labs/virtualmachines | ın | 1-15 (Windows)<br>1-64 (Linux) | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. Tüm sayılar olamaz. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Veritabanı hesapları | global | 3-44 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Küçük harf veya sayı ile başlayın. |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | etki alanları | kaynak grubu | 3-50 | Alfasayısal karakterler ve kısa çizgiler. |
> | etki alanları/konular | etki alanı | 3-50 | Alfasayısal karakterler ve kısa çizgiler. |
> | Eventabonelikleri | kaynak grubu | 3-64 | Alfasayısal karakterler ve kısa çizgiler. |
> | konuları | kaynak grubu | 3-50 | Alfasayısal karakterler ve kısa çizgiler. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | leriniz | kaynak grubu | 6-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. Harf veya sayı ile biter. |
> | öznitelikleri | global | 6-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. Harf veya sayı ile biter. |
> | ad alanları/AuthorizationRules | ad alanı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Harf veya sayı ile başlatın ve sonlandırın. |
> | ad alanları/Diskalrecoveryconfigs | ad alanı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Harf veya sayı ile başlatın ve sonlandırın. |
> | ad alanları/eventhubs | ad alanı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Harf veya sayı ile başlatın ve sonlandırın. |
> | ad alanları/eventhubs/authorizationRules | Olay Hub 'ı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Harf veya sayı ile başlatın ve sonlandırın. |
> | ad alanları/eventhubs/consumergroups | Olay Hub 'ı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Harf veya sayı ile başlatın ve sonlandırın. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | leriniz | global | 3-59 | Alfasayısal ve tireler<br><br>Harf veya sayı ile başlatın ve sonlandırın. |

## <a name="microsoftimportexport"></a>Microsoft. ımportexport

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Çizelge | kaynak grubu | 2-64 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | actionGroups | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`/&%\?` <br><br>Boşluk veya nokta ile bitemez.  |
> | bileşenleri | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`%&\?/` <br><br>Boşluk veya nokta ile bitemez.  |
> | scheduledQueryRules | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`*<>%{}&:\\?/#` <br><br>Boşluk veya nokta ile bitemez.  |
> | metricAlerts | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`*#&+:<>?@%{}\/` <br><br>Boşluk veya nokta ile bitemez.  |
> | activityLogAlerts | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`<>*%{}&:\\?+/#` <br><br>Boşluk veya nokta ile bitemez.  |

## <a name="microsoftiotcentral"></a>Microsoft. ıotcentral

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Iotapps | global | 2-63 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Küçük harf veya sayı ile başlayın. |

## <a name="microsoftkeyvault"></a>Microsoft. Keykasası

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | kasaları | global | 3-24 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. Harf veya rakamla biter. Ardışık kısa çizgi içeremez. |
> | kasa/gizlilikler | Kasa | 1-127 | Alfasayısal karakterler ve kısa çizgiler. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | leriniz | global | 4-22 | Küçük harfler ve rakamlar.<br><br>Mektup ile başlayın. |
> | /kümeler/veritabanları | cluster | 1-260 | Alfasayısal, tire, boşluk ve nokta. |
> | /kümeler/veritabanları/veri bağlantıları | database | 1-40 | Alfasayısal, tire, boşluk ve nokta. |
> | /kümeler/veritabanları/eventhubconnections | database | 1-40 | Alfasayısal, tire, boşluk ve nokta. |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Tümleştirme hesapları | kaynak grubu | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |
> | Tümleştirme hesapları/derlemeler | tümleştirme hesabı | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |
> | Tümleştirme hesapları/batchConfigurations | tümleştirme hesabı | 1-20 | Alfasayısal karakterler. |
> | Tümleştirme hesapları/sertifikalar | tümleştirme hesabı | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |
> | Tümleştirme hesapları/haritalar | tümleştirme hesabı | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |
> | Tümleştirme hesapları/iş ortakları | tümleştirme hesabı | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |
> | ıntegrationaccounts/rosettinganetprocessconfigurations yapılandırması | tümleştirme hesabı | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |
> | Tümleştirme hesapları/şemaları | tümleştirme hesabı | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |
> | Tümleştirme hesapları/oturumlar | tümleştirme hesabı | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |
> | ıntegrationserviceortamortamları | kaynak grubu | 1-80 | Alfasayısal, kısa çizgi, nokta ve alt çizgi. |
> | ıntegrationserviceortamortamları/managedap | tümleştirme hizmeti ortamı | 1-80 | Alfasayısal, kısa çizgi, nokta ve alt çizgi. |
> | sürdürülen | kaynak grubu | 1-80 | Alfasayısal, tire, alt çizgi, nokta ve parantez. |

## <a name="microsoftmachinelearning"></a>Microsoft. Machinöğrenim

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Commitmentplanlar | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`<>*%&:?+/\\`<br><br>Boşluk ile bitemez. |
> | Hizmetleri | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`<>*%&:?+/\\`<br><br>Boşluk ile bitemez. |
> | çalışma alanı | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`<>*%&:?+/\\`<br><br>Boşluk ile bitemez. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | çalışma alanı | kaynak grubu | 3-33 | Alfasayısal karakterler ve kısa çizgiler. |
> | çalışma alanları/hesaplar | çalışma alanı | 2-16 | Alfasayısal karakterler ve kısa çizgiler. |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Userassignedıdentities | kaynak grubu | 3-128 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler<br><br>Harf veya sayı ile başlayın. |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | accounts | kaynak grubu | 1-98 (kaynak grubu adı ve hesap adı için) | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | mediaservices | kaynak grubu | 3-24 | Küçük harfler ve rakamlar. |
> | mediaservices/liveEvents | Medya hizmeti | 1-32 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlayın. |
> | mediaservices/liveEvents/Liveçıktılar | Canlı etkinlik | 1-256 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlayın. |
> | mediaservices/streamingEndpoints | Medya hizmeti | 1-24 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlayın. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | Applicationgateway 'ler | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | applicationSecurityGroups | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | azureFirewalls | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi ile biter. |
> | Savunma Konakları | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | bağlantının | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | dnsZones | kaynak grubu | 1-63 karakter<br><br>2-34 etiketleri<br><br>Her etiket bir noktayla ayrılmış bir karakter kümesidir. Örneğin, **contoso.com** 2 etikete sahiptir. | Her etiket alfasayısal, alt çizgi ve kısa çizgi içerebilir.<br><br>Her etiket bir noktayla ayrılır. |
> | Expressroutedevreleri | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | firewallPolicies | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | firewallPolicies/ruleGroups | güvenlik duvarı ilkesi | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | Frontkapıların | global | 5-64 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | frontdoorWebApplicationFirewallPolicies | kaynak grubu | 1-128 | Alfasayısal karakterler.<br><br>Mektup ile başlayın. |
> | loadBalancers | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | loadBalancers/ınboundnatrules | yük dengeleyici | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | Localnetworkgateway 'ler | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | NetworkInterfaces | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | networkSecurityGroups | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | networkSecurityGroups/securityRules | ağ güvenlik grubu | 1-80 |  Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | networkWatchers | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | privateDnsZones | kaynak grubu | 1-63 karakter<br><br>2-34 etiketleri<br><br>Her etiket bir noktayla ayrılmış bir karakter kümesidir. Örneğin, **contoso.com** 2 etikete sahiptir. | Her etiket alfasayısal, alt çizgi ve kısa çizgi içerebilir.<br><br>Her etiket bir noktayla ayrılır. |
> | privateDnsZones/virtualNetworkLinks | özel DNS bölgesi | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | Publicıpaddresses | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | Publicıpöneklerini | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | routeFilters | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | routeFilters/routeFilterRules | yol filtresi | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | routeTables | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | routeTables/rotalar | yol tablosu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | serviceEndpointPolicies | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | trafficmanagerprofiles | global | 1-63 | Alfasayısal karakterler, kısa çizgiler ve dönemler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | virtualNetworkGateways | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | virtualNetworks | kaynak grubu | 2-64 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | virtualnetworks/alt ağları | sanal ağ | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | virtualNetworks/Virtualnetworkpeerler | sanal ağ | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | Virtualwan | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | Vpngateway 'ler | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | Vpngateway 'ler/vpnConnections | VPN ağ geçidi | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |
> | vpnSites | kaynak grubu | 1-80 | Alfasayısal, alt çizgi, nokta ve kısa çizgi.<br><br>Alfasayısal olarak başlayın. Alfasayısal veya alt çizgi sonu. |

## <a name="microsoftnotificationhubs"></a>Microsoft. Notificationhub 'Lar

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | öznitelikleri | global | 6-50 | Alfasayısal ve tireler<br><br>Mektup ile başlayın. Alfasayısal ile biter. |
> | ad alanları/AuthorizationRules | ad alanı | 1-256 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alfasayısal başlatın. |
> | ad alanları/Notificationhub 'Lar | ad alanı | 1-260 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alfasayısal başlatın. |
> | ad alanları/Notificationhub 'Lar/AuthorizationRules | Bildirim Hub 'ı | 1-256 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alfasayısal başlatın. |

## <a name="microsoftoperationalinsights"></a>Microsoft. Operationalınsights

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | leriniz | kaynak grubu | 4-63 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | çalışma alanı | global | 4-63 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | çözümler | çalışma alanı | Yok | Microsoft tarafından yazılan çözümler için, adın şu düzende olması gerekir:<br>`SolutionType(WorkspaceName)`<br><br>Üçüncü taraflar tarafından yazılan çözümler için, adın düzende olması gerekir:<br>`SolutionType[WorkspaceName]`<br><br>Örneğin, geçerli bir ad:<br>`AntiMalware(contoso-IT)`<br><br>Çözüm türü, büyük/küçük harfe duyarlıdır. |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | panolar | kaynak grubu | 3-160 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Kısıtlı karakterleri kullanmak için, kullanmak istediğiniz Pano adıyla **gizli-title** adlı bir etiket ekleyin. Portal, Panoyu gösterirken bu adı görüntüler. |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Kısa çizgi ile başlayamaz. Ardışık tireler kullanılamaz. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. Powerbiadanmış

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | kapasiteler | region | 3-63 | Küçük harfler veya rakamlar<br><br>Küçük harfle başlayın. |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | kasaları | kaynak grubu | 2-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. |
> | kasa/backupPolicies | kasa | 3-150 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. Kısa çizgi ile bitemez. |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | öznitelikleri | global | 6-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>En başta bir harf kullanın. Bir harf veya sayıyla biter. |
> | ad alanları/AuthorizationRules | ad alanı | 1-50 |  Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | ad alanları/HybridConnections | ad alanı | 1-260 | Alfasayısal, nokta, tire, alt çizgi ve eğik çizgi.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | ad alanları/HybridConnections/authorizationRules | karma bağlantı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | ad alanları/Wcfreyerleştiri | ad alanı | 1-260 | Alfasayısal, nokta, tire, alt çizgi ve eğik çizgi.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | ad alanları/Wcfreyerleştirme/authorizationRules | WCF geçişi | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | dağıtımlar | kaynak grubu | 1-64 | Alfasayısal, alt çizgi, parantezler, tireler ve dönemler. |
> | ResourceGroups | aboneliği | 1-90 | Alfasayısal [belgeleriyle](/rest/api/resources/resourcegroups/createorupdate)eşleşen alfasayısal, alt çizgi, parantezler, tire, nokta ve Unicode karakterleri.<br><br>Nokta ile bitemez. |
> | tagNames | kaynak | 1-512 | Şu kullanılamıyor:<br>`<>%&\?/` |
> | tagNames/tagValues | etiket adı | 1-256 | Tüm karakterler. |
> | Templatespec | kaynak grubu | 1-90 | Alfasayısal, alt çizgi, parantezler, tireler ve dönemler. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | öznitelikleri | global | 6-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>En başta bir harf kullanın. Bir harf veya sayıyla biter.<br><br>Daha fazla bilgi için bkz. [ad alanı oluşturma](/rest/api/servicebus/create-namespace). |
> | ad alanları/AuthorizationRules | ad alanı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alphnumeric ile başlatın ve sonlandırın. |
> | ad alanları/Diskalrecoveryconfigs | global | 6-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. Alfasayısal ile biter. |
> | ad alanları/migrationConfigurations | ad alanı |  | Her zaman **$Default** olmalıdır. |
> | ad alanları/kuyruklar | ad alanı | 1-260 | Alfasayısal, nokta, tire, alt çizgi ve eğik çizgi.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | ad alanları/kuyruklar/authorizationRules | kuyruk | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alphnumeric ile başlatın ve sonlandırın. |
> | ad alanları/konular | ad alanı | 1-260 | Alfasayısal, nokta, tire, alt çizgi ve eğik çizgi.<br><br>Alfasayısal olarak başlatın ve sonlandırın. |
> | ad alanları/konular/authorizationRules | konu başlığı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alphnumeric ile başlatın ve sonlandırın. |
> | ad alanları/konular/abonelikler | konu başlığı | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alphnumeric ile başlatın ve sonlandırın. |
> | ad alanları/konular/abonelikler/kurallar | aboneliği | 1-50 | Alfasayısal, nokta, kısa çizgi ve alt çizgi.<br><br>Alphnumeric ile başlatın ve sonlandırın. |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | leriniz | region | 4-23 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Küçük harfle başlayın. Küçük harf veya sayı ile biter. |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | signalR | global | 3-63 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. Harf veya sayı ile biter.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | ManagedInstances | global | 1-63 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Kısa çizgi ile başlayamaz veya bitemez. <br><br> Gibi özel karakterler içeremez `@` . |
> | larý | global | 1-63 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Kısa çizgi ile başlayamaz veya bitemez. |
> | sunucular/Yöneticiler | sunucu |  | Olmalıdır `ActiveDirectory` . |
> | sunucular/veritabanları | sunucu | 1-128 | Şu kullanılamıyor:<br>`<>*%&:\/?`<br><br>Nokta veya boşluk ile bitemez. |
> | sunucular/veritabanları/syncGroups | database | 1-150 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | sunucular/Elaun havuzları | sunucu | 1-128 | Şu kullanılamıyor:<br>`<>*%&:\/?`<br><br>Nokta veya boşluk ile bitemez. |
> | sunucular/failoverGroups 'lar | global | 1-63 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Kısa çizgi ile başlayamaz veya bitemez. |
> | sunucular/firewallRules | sunucu | 1-128 | Şu kullanılamıyor:<br>`<>*%&:;\/?`<br><br>Nokta ile bitemez. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | Küçük harfler ve rakamlar. |
> | storageAccounts/blobServices | depolama hesabı |  | Olmalıdır `default` . |
> | storageAccounts/blobServices/kapsayıcılar | depolama hesabı | 3-63 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Küçük harf veya sayı ile başlayın. Ardışık tireler kullanılamaz. |
> | storageAccounts/fileServices | depolama hesabı |  | Olmalıdır `default` . |
> | storageAccounts/Fıleservices/Shares | depolama hesabı | 3-63 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Kısa çizgi ile başlayamaz veya bitemez. Ardışık tireler kullanılamaz. |
> | storageAccounts/managementPolicies | depolama hesabı |  | Olmalıdır `default` . |
> | blob | kapsayıcı | 1-1024 | Herhangi bir URL karakteri, büyük/küçük harfe duyarlı |
> | kuyruk | depolama hesabı | 3-63 | Küçük harfler, rakamlar ve kısa çizgiler.<br><br>Kısa çizgi ile başlayamaz veya bitemez. Ardışık tireler kullanılamaz. |
> | tablo | depolama hesabı | 3-63 | Alfasayısal karakterler.<br><br>Mektup ile başlayın. |

## <a name="microsoftstoragesync"></a>Microsoft. Storagessync

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | storageSyncServices | kaynak grubu | 1-260 | Alfasayısal, boşluk, nokta, kısa çizgi ve alt çizgi.<br><br>Nokta veya boşluk ile bitemez. |
> | storageSyncServices/syncGroups | depolama eşitleme hizmeti | 1-260 | Alfasayısal, boşluk, nokta, kısa çizgi ve alt çizgi.<br><br>Nokta veya boşluk ile bitemez. |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | ilerinde | kaynak grubu | 2-50 | Alfasayısal karakterler ve kısa çizgiler.<br><br>Mektup ile başlayın. Alfasayısal ile biter. |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | streammingjobs | kaynak grubu | 3-63 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | streammingjobs/işlevleri | akış işi | 3-63 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | streammingjobs/girişleri | akış işi | 3-63 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | streammingjobs/çıkışlar | akış işi | 3-63 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |
> | streammingjobs/dönüşümler | akış işi | 3-63 | Alfasayısal karakterler, kısa çizgiler ve alt çizgiler. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. Timeseriesınsights

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | lý | kaynak grubu | 1-90 | Şu kullanılamıyor:<br>`'<>%&:\?/#` |
> | ortamlar/accessPolicies | ortam | 1-90 | Şu kullanılamıyor:<br> `'<>%&:\?/#` |
> | ortamlar/eventSources | ortam | 1-90 | Şu kullanılamıyor:<br>`'<>%&:\?/#` |
> | ortamlar/Referencedataset 'ler | ortam | 3-63 | Alfasayısal karakterler |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Varlık | Kapsam | Uzunluk | Geçersiz Karakterler |
> | --- | --- | --- | --- |
> | sertifikalar | kaynak grubu | 1-260 | Şu kullanılamıyor:<br>`/` <br><br>Boşluk veya nokta ile bitemez.  | 
> | serverfarms | kaynak grubu | 1-40 | Alfasayısal karakterler ve kısa çizgiler. |
> | Siteler | Genel veya etki alanı. Aşağıdaki nota bakın. | 2-60 | Alfasayısal ve kısa çizgi içerir.<br><br>Kısa çizgi ile başlayamaz veya bitemez. |
> | siteler/yuvalar | Site | 2-59 | Alfasayısal karakterler ve kısa çizgiler. |

> [!NOTE]
> Bir Web sitesi, genel olarak benzersiz bir URL 'ye sahip olmalıdır. Barındırma planı kullanan bir Web sitesi oluşturduğunuzda, URL olur `http://<app-name>.azurewebsites.net` . Uygulama adı genel olarak benzersiz olmalıdır. App Service Ortamı kullanan bir Web sitesi oluşturduğunuzda, uygulama adı [App Service ortamı etki alanı](../../app-service/environment/using-an-ase.md#app-access)içinde benzersiz olmalıdır. Her iki durumda da sitenin URL 'SI genel olarak benzersizdir.
>
> Azure Işlevleri, Microsoft. Web/Sites ile aynı adlandırma kurallarına ve kısıtlamalara sahiptir.

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları adlandırma hakkında öneriler için bkz. [Ready: önerilen adlandırma ve etiketleme kuralları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
