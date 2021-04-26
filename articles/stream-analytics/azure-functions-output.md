---
title: Azure Stream Analytics Azure Işlevleri çıkışı
description: Bu makalede, Azure işlevleri Azure Stream Analytics çıktı olarak açıklanmaktadır.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019627"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Stream Analytics Azure Işlevleri çıkışı

Azure Işlevleri, altyapıyı açıkça sağlamak veya yönetmek zorunda kalmadan kodu isteğe bağlı olarak çalıştırmak için kullanabileceğiniz sunucusuz bir işlem hizmetidir. Azure veya iş ortağı hizmetlerinde gerçekleşen olaylar tarafından tetiklenen kodu uygulamanıza olanak tanır. Azure Işlevlerinin tetikleyicilere yanıt vermesi bu özelliği Azure Stream Analytics için doğal bir çıkış yapar. Bu çıkış bağdaştırıcısı, kullanıcıların Azure Işlevlerine Stream Analytics bağlanmasına ve çeşitli olaylara yanıt olarak bir betiği ya da kod parçasını çalıştırmasına olanak sağlar.

Azure Işlevleri Stream Analytics çıkışı, Azure Çin 21Vianet ve Azure Almanya (T-Systems Uluslararası) bölgelerinde kullanılamaz. Çoklu kiracı kümesinde çalışan bir Stream Analytics işinden bir sanal ağ (VNet) içindeki Azure Işlevlerine bağlantı de desteklenmez.

Azure Stream Analytics HTTP Tetikleyicileri aracılığıyla Azure Işlevlerini çağırır. Azure Işlevleri çıkış bağdaştırıcısı, aşağıdaki yapılandırılabilir özelliklerle kullanılabilir:

| Özellik adı | Description |
| --- | --- |
| İşlev uygulaması |Azure Işlevleri uygulamanızın adı. |
| İşlev |Azure Işlevleri uygulamanızdaki işlevin adı. |
| Anahtar |Başka bir abonelikteki bir Azure Işlevi kullanmak istiyorsanız, işlevinizin erişimine yönelik anahtarı sağlayarak bunu yapabilirsiniz. |
| En fazla toplu iş boyutu |Azure işlevinizde gönderilen her çıkış toplu işi için en büyük boyutu ayarlamanızı sağlayan bir özellik. Giriş birimi bayt cinsinden. Varsayılan olarak, bu değer 262.144 bayttır (256 KB). |
| En fazla toplu iş sayısı  |Azure Işlevlerine gönderilen her bir yığında en fazla olay sayısını belirtmenize olanak tanıyan bir özellik. Varsayılan değer 100’dür. |

Azure Stream Analytics, başarıyla işlenen toplu işlemler için Işlevler uygulamasından HTTP durum 200 ' i bekliyor.

Azure Stream Analytics bir Azure işlevinden 413 ("http Istek varlığı çok büyük") özel durumu aldığında, Azure Işlevlerine gönderdiği toplu işlerin boyutunu azaltır. Azure işlev kodunuzda Azure Stream Analytics büyük bir toplu iş göndermediğinden emin olmak için bu özel durumu kullanın. Ayrıca, işlevde kullanılan en büyük toplu iş sayısı ve boyut değerlerinin Stream Analytics portalına girilen değerlerle tutarlı olduğundan emin olun.

> [!NOTE]
> Sınama bağlantısı sırasında, Stream Analytics iki çalışma arasındaki bağlantının test olması için Azure Işlevlerine boş bir toplu işlem gönderir. Test bağlantısının başarılı olduğundan emin olmak için Işlevler uygulamanızın boş toplu iş isteklerini işlediğinizden emin olun.

Ayrıca, bir zaman penceresinde olay sahanlığı olmadığı durumlarda, hiçbir çıkış oluşturulmaz. Sonuç olarak, **Computeresult** işlevi çağrılmaz. Bu davranış yerleşik pencere toplamı işlevleriyle tutarlıdır.

## <a name="partitioning"></a>Bölümleme

Bölüm anahtarı, sorgudaki bölüm BY yan tümcesine dayalıdır. Çıktı yazıcılarının sayısı, [tam paralelleştirilmiş sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler.

## <a name="output-batch-size"></a>Çıkış toplu iş boyutu

Varsayılan yığın boyutu 262.144 bayttır (256 KB). Batch başına varsayılan olay sayısı 100 ' dir. Toplu iş boyutu yapılandırılabilir ve Stream Analytics çıktı seçeneklerinde artırılabilir veya azaltılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Azure portalını kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md)
* [Hızlı başlangıç: Azure CLı kullanarak Azure Stream Analytics işi oluşturma](quick-create-azure-cli.md)
* [Hızlı başlangıç: ARM şablonu kullanarak Azure Stream Analytics işi oluşturma](quick-create-azure-resource-manager.md)
* [Hızlı başlangıç: Azure PowerShell kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-powershell.md)
* [Hızlı başlangıç: Visual Studio 'Yu kullanarak Azure Stream Analytics işi oluşturma](stream-analytics-quick-create-vs.md)
* [Hızlı başlangıç: Visual Studio Code Azure Stream Analytics iş oluşturma](quick-create-visual-studio-code.md)