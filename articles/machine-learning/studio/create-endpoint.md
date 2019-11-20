---
title: Web hizmeti uç noktaları oluşturma
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) Web hizmeti uç noktaları oluşturun. Web hizmetindeki her bir uç nokta bağımsız olarak karşılanır, kısıtlandı ve yönetilir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 891f680485ec3ee65c0bef102eb7f52cea0611c6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839640"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Dağıtılan Azure Machine Learning Studio (klasik) Web Hizmetleri için uç noktalar oluşturma

> [!NOTE]
> Bu konu, **Klasik** bir Machine Learning Web hizmeti için geçerli olan teknikleri açıklamaktadır.

Bir web hizmeti dağıtıldıktan sonra, bu hizmet için varsayılan bir uç noktası oluşturulur. Varsayılan uç noktası, API anahtarı kullanılarak çağrılabilir. Web Hizmetleri portalından kendi anahtarlarıyla daha fazla uç nokta ekleyebilirsiniz.
Web hizmetindeki her bir uç nokta bağımsız olarak karşılanır, kısıtlandı ve yönetilir. Her uç nokta, müşterilerinize dağıtabileceğiniz bir yetkilendirme anahtarına sahip benzersiz bir URL 'dir.

## <a name="add-endpoints-to-a-web-service"></a>Web hizmetine uç noktalar ekleme

Web hizmetine Azure Machine Learning Web Hizmetleri portalını kullanarak bir uç nokta ekleyebilirsiniz. Uç nokta oluşturulduktan sonra, bunu zaman uyumlu API 'ler, Batch API 'Leri ve Excel çalışma sayfaları aracılığıyla kullanabilirsiniz.

> [!NOTE]
> Web hizmetine ek uç noktalar eklediyseniz, varsayılan uç noktayı silemezsiniz.

1. Machine Learning Studio (klasik) ' de, sol gezinti sütununda Web Hizmetleri ' ne tıklayın.
2. Web hizmeti panosunun alt kısmındaki **uç noktaları Yönet**' e tıklayın. Web Hizmetleri portalı Azure Machine Learning Web hizmeti için uç noktalar sayfasına açılır.
3. **Yeni**’ye tıklayın.
4. Yeni uç nokta için bir ad ve açıklama yazın. Uç nokta adları 24 karakter uzunluğunda veya daha az olmalıdır ve küçük harfli harfler veya rakamlardan oluşur. Günlüğe kaydetme düzeyini ve örnek verilerin etkinleştirilip etkinleştirilmeyeceğini seçin. Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [Machine Learning Web Hizmetleri için günlüğü etkinleştirme](web-services-logging.md).

## <a id="scaling"></a>Ek uç noktalar ekleyerek bir Web hizmetini ölçeklendirin

Varsayılan olarak, yayımlanan her Web hizmeti 20 eşzamanlı isteği destekleyecek şekilde yapılandırılır ve 200 eşzamanlı istek kadar yüksek olabilir. Azure Machine Learning Studio klasik sürümü, Web hizmetiniz için en iyi performansı sağlamak üzere ayarı otomatik olarak iyileştirir ve Portal değeri yok sayılır.

API 'yi, en fazla eşzamanlı çağrı değeri olan 200 ' den daha yüksek bir yük ile çağırmayı planlıyorsanız, aynı Web hizmetinde birden fazla uç nokta oluşturmanız gerekir. Daha sonra yüklerinizi tüm bunlar arasında rastgele dağıtabilirsiniz.

Bir Web hizmetinin ölçeklendirilmesi ortak bir görevdir. Ölçeklendirmenin bazı nedenleri 200 'den fazla eşzamanlı isteği desteklemek, birden fazla uç nokta aracılığıyla kullanılabilirliği artırmanız veya Web hizmeti için ayrı uç noktalar sağlamaktır. [Azure Machine Learning Web hizmeti](https://services.azureml.net/) portalı aracılığıyla aynı Web hizmeti için ek uç noktalar ekleyerek ölçeği artırabilirsiniz.

API 'YI karşılık gelen yüksek bir ücret ile çağırmadan, yüksek eşzamanlılık sayısı kullanmanın, en fazla bir tutarlılık olabileceğini aklınızda bulundurun. Yüksek yük için yapılandırılmış bir API 'ye görece düşük bir yük yerleştirmeniz durumunda, tek tek zaman aşımları ve/veya gecikmede ani artışlar görebilirsiniz.

Zaman uyumlu API 'Ler genellikle düşük bir gecikme süresinin istendiği durumlarda kullanılır. Burada gecikme süresi, API 'nin bir isteği tamamlaması için geçen süreyi gösterir ve herhangi bir ağ gecikmesi için hesap yapmaz. Bir API 'nin 50-ms gecikme süresine sahip olduğunu varsayalım. Sınır düzeyi yüksek ve en fazla eşzamanlı aramalar = 20 olan kullanılabilir kapasiteyi tam olarak kullanmak için, bu API 20 * 1000/50 = 400 kez saniye başına çağırmanız gerekir. Bu daha fazla genişlemekle, en fazla eşzamanlı 200 çağrısı, dakikada bir API 4000 kez çağrı yapmanıza olanak sağlar. Bu, 50-MS gecikmesini kabul ediyor.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Machine Learning Web hizmeti kullanma](consume-web-services.md).
