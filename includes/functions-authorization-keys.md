---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 617608f382d3331e59ae92c9eb272347c736b768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91828928"
---
İşlevler, geliştirme sırasında HTTP işlev uç noktalarınıza erişmeyi daha zor hale getirmek için anahtarları kullanmanıza olanak sağlar. HTTP ile tetiklenen bir işlev üzerinde HTTP erişim düzeyi olarak ayarlanmadığı takdirde `anonymous` istekler istekte BIR API erişim anahtarı içermelidir. 

Anahtarlar varsayılan bir güvenlik mekanizması sağlarken, üretimde bir HTTP uç noktasının güvenliğini sağlamak için ek seçenekler düşünmek isteyebilirsiniz. Örneğin, ortak uygulamalarda paylaşılan gizli dizi dağıtımı genellikle iyi bir uygulamadır. İşleviniz ortak bir istemciden çağrılırsa, başka bir güvenlik mekanizması uygulamayı düşünmek isteyebilirsiniz. Daha fazla bilgi edinmek için bkz. [üretimde BIR HTTP uç noktası güvenli hale getirme](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

İşlev anahtarı değerlerinizi yenilediğinizde, güncelleştirilmiş anahtar değerlerini işlevinizi çağıran tüm istemcilere el ile yeniden dağıtmanız gerekir.  

#### <a name="authorization-scopes-function-level"></a>Yetkilendirme kapsamları (işlev düzeyi)

İşlev düzeyi anahtarlar için iki erişim kapsamı vardır:

* **İşlev**: Bu anahtarlar yalnızca tanımlandıkları belirli işlevler için geçerlidir. API anahtarı olarak kullanıldığında, bunlar yalnızca bu işleve erişime izin verir.

* **Konak**: işlev uygulaması içindeki tüm işlevlere erişmek için konak kapsamına sahip anahtarlar kullanılabilir. API anahtarı olarak kullanıldığında, bu, işlev uygulaması içindeki herhangi bir işleve erişime izin verir. 

Her anahtar başvuru için adlandırılır ve işlev ve ana bilgisayar düzeyinde bir varsayılan anahtar ("varsayılan" olarak adlandırılır) vardır. İşlev anahtarları ana bilgisayar anahtarlarına göre önceliklidir. Aynı ada sahip iki anahtar tanımlandığında, işlev anahtarı her zaman kullanılır.

#### <a name="master-key-admin-level"></a>Ana anahtar (yönetici düzeyi) 

Her işlev uygulamasının adlı bir yönetim düzeyi ana bilgisayar anahtarı da vardır `_master` . Ana anahtar, uygulamadaki tüm işlevlere ana bilgisayar düzeyinde erişim sağlamaya ek olarak, çalışma zamanı REST API 'Lerine da yönetici erişimi sağlar. Bu anahtar iptal edilemez. Bir erişim düzeyi belirlediğinizde `admin` , istekler ana anahtarı kullanmalıdır; erişim hatası ile ilgili başka herhangi bir anahtar oluşur.

> [!CAUTION]  
> Ana anahtar tarafından verilen işlev uygulamanızda yükseltilmiş izinler nedeniyle, bu anahtarı üçüncü taraflarla paylaşmamalıdır veya yerel istemci uygulamalarında dağıtmanız gerekir. Yönetici erişim düzeyini seçerken dikkatli olun.
