---
title: Güvenli kod en iyi uygulamaları
titleSuffix: Azure Machine Learning
description: Azure Machine Learning, azaltmaları ve en iyi uygulamalar için geliştirme yaparken mevcut olabilecek olası güvenlik tehditleri hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322976"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Azure Machine Learning ile güvenli kod en iyi yöntemleri

Azure Machine Learning, herhangi bir kaynaktaki dosyaları ve içerikleri Azure 'a yükleyebilirsiniz. Jupi Not defterleri veya yüklediğiniz betikler içindeki içerikler, oturumlarınızdan verileri okuyabilir, Azure 'da kuruluşunuzdaki verilere erişebilir veya sizin adınıza kötü amaçlı süreçler çalıştırabilir.

> [!IMPORTANT]
> Yalnızca güvenilir kaynaklardan gelen not defterlerini veya betikleri çalıştırın. Örneğin, sizin veya güvenlik takımınız Not defterini veya betiği incelendi.

## <a name="potential-threats"></a>Olası tehditler

Azure Machine Learning geliştirmesi genellikle Web tabanlı geliştirme ortamları (Not defterleri & Azure ML Studio) içerir. Web tabanlı geliştirme ortamları kullanılırken olası tehditler şunlardır:

* [Çapraz site betiği oluşturma (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Dom ekleme__: Bu tür bir saldırı tarayıcıda görünen kullanıcı arabirimini değiştirebilir. Örneğin, çalıştırma düğmesinin bir Jupyter Notebook nasıl davranacağını değiştirerek.
    * __Belirteç/tanımlama bilgilerine erişim__: XSS saldırıları, yerel depolama ve tarayıcı tanımlama bilgilerine de erişebilir. Azure Active Directory (AAD) kimlik doğrulama belirteciniz yerel depolama alanında depolanır. Bir XSS saldırısı, bu belirteci sizin adınıza API çağrıları yapmak ve sonra verileri bir dış sisteme veya API 'ye göndermek için kullanabilir.

* [Çapraz site isteği sahteciliğini önleme (CSRF)](https://owasp.org/www-community/attacks/csrf): Bu saldırı bir görüntünün URL 'sini veya bir bağlantının URL 'sini kötü amaçlı betiğin veya API 'nin URL 'siyle değiştirebilir. Görüntü yüklendiğinde veya bağlantıya tıklandığı zaman, URL 'ye bir çağrı yapılır.

## <a name="azure-ml-studio-notebooks"></a>Azure ML Studio Not defterleri

Azure Machine Learning Studio, tarayıcınızda barındırılan bir not defteri deneyimi sağlar. Bir not defterindeki hücreler, HTML belgelerinin veya kötü amaçlı kod içeren parçaların çıktısını alabilir.  Çıktı işlendiğinde kod çalıştırılabilir.

__Olası tehditler__:
* Çapraz site betiği oluşturma (XSS)
* Çapraz site isteği sahteciliğini önleme (CSRF)

__Azure Machine Learning tarafından sunulan azaltmaları__:
* __Kod hücresi çıkışı__ bir iframe 'de korumalı değildir. İframe, betiğin üst DOM, tanımlama bilgileri veya oturum depolamasına erişmesini önler.
* __Markaşağı hücre__ içerikleri, dompurbelirt kitaplığı kullanılarak temizlenir. Bu, markaşağı hücrelerle birlikte kötü amaçlı betikleri yürütmeyi engeller.
* __Resim URL 'si__ ve __markı bağlantıları__ , kötü amaçlı değerleri denetleyen, Microsoft 'a ait bir uç noktaya gönderilir. Kötü amaçlı bir değer algılanırsa, uç nokta isteği reddeder.

__Önerilen Eylemler__:
* Studio 'ya yüklemeden önce dosyaların içeriğine güvendiğinizden emin olun. Karşıya yüklerken, güvenilen dosyaları karşıya yüklediğiniz kabul etmeniz gerekir.
* Bir dış uygulamayı açmak için bir bağlantı seçerken uygulamaya güvenmeniz istenir.

## <a name="azure-ml-compute-instance"></a>Azure ML işlem örneği

Azure Machine Learning işlem örneği __jupyıter__ ve __Jupyıter Laboratuvarı__ barındırır. Herhangi birini kullanırken, içindeki bir not defterinde veya kodda bulunan hücreler, HTML belgelerinin veya kötü amaçlı kod içeren parçaların çıktısını alabilir. Çıktı işlendiğinde kod çalıştırılabilir. Aynı tehditler Ayrıca bir işlem örneği üzerinde barındırılan __rstudio__ kullanılırken de geçerlidir.

__Olası tehditler__:
* Çapraz site betiği oluşturma (XSS)
* Çapraz site isteği sahteciliğini önleme (CSRF)

__Azure Machine Learning tarafından sunulan azaltmaları__:
* Yok. Jupyıter ve jupi Laboratuvarı, Azure Machine Learning işlem örneğinde barındırılan açık kaynaklı uygulamalardır.

__Önerilen Eylemler__:
* Studio 'ya yüklemeden önce dosyaların içeriğine güvendiğinizden emin olun. Karşıya yüklerken, güvenilen dosyaları karşıya yüklediğiniz kabul etmeniz gerekir.

## <a name="report-security-issues-or-concerns"></a>Güvenlik sorunlarını veya kaygılarını bildirin 

Azure Machine Learning, Microsoft Azure sıçramış program altında uygun. Daha fazla bilgi için, adresini ziyaret edin  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Machine Learning için Kuruluş Güvenliği](concept-enterprise-security.md)