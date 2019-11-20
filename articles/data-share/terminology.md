---
title: Azure Veri Paylaşımı terminolojisi
description: Azure veri paylaşımında (veri sağlayıcısı, veri tüketicisi, veri paylaşma, paylaşma aboneliği, anlık görüntü, davet, alıcı) kullanılan kaynakları anlatmak için kullanılan genel terimler hakkında bilgi edinin.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468546"
---
# <a name="azure-data-share-concepts"></a>Azure veri paylaşımında kavramlar 

Azure veri paylaşımı, veri paylaşımıyla ilgili bazı yeni terminoloji sunmaktadır. Bu makalede, hizmet boyunca kullanılan bazı sık kullanılan terimler açıklanmaktadır. 

## <a name="data-provider"></a>Veri sağlayıcısı

Veri sağlayıcısı tüketicilerle veri paylaşan kuruluştur. Genellikle veri sağlayıcısı, verilerin sahibi veya bir Seçicisi olabilir. Veri sağlayıcıları çeşitli türlerdeki verileri paylaşmak istiyor. Veri sağlayıcısının paylaştırmak isteyebileceğiniz verilerin bazı örnekleri, satış noktaları veya zaman serisi verileri gibi ham verileri içerir. Veri sağlayıcısı, zaten analiz ve Öngörüler içeren, önceden işlenmiş, seçkin verileri de paylaşmak isteyebilir. 

## <a name="data-consumer"></a>Veri tüketicisi 

Veri tüketicisi, veri sağlayıcısından veri alan kuruluştur. Veri TÜKETİCİSİNDE, Öngörüler elde etmek için paylaşılan verileri kendi verileriyle birleştirmek isteyen bir sorun olabilir. Bazı durumlarda, veri tüketicisi zaten işlenmiş olan verileri alıyor olabilir. 

## <a name="data-share"></a>Veri paylaşma

Veri paylaşım, tek bir varlık olarak paylaşılan bir veri kümesi grubudur. Veri kümeleri, Azure veri paylaşımında desteklenen bir dizi Azure veri kaynağından olabilir. Şu anda Azure veri paylaşımında Azure Blob depolamayı ve Azure Data Lake Store desteklenir. 

## <a name="share-subscription"></a>Abonelik paylaşma 

Bir veri tüketicisi bir veri sağlayıcısından veri paylaşımının davetini kabul ettiğinde bir paylaşma aboneliği oluşturulur. Veri sağlayıcıları, Azure veri paylaşımı hesabındaki **gönderilen paylaşımlara** giderek ve **abonelikleri paylaşma**' yı seçerek etkin paylaşım aboneliklerini görüntüleyebilir.

Bir veri tüketicisi, **alınan paylaşımlara** giderek ve alınan paylaşımlarının durumunu görüntüleyerek etkin bir paylaşım aboneliğine sahip olup olmadığını denetleyebilir. 

## <a name="snapshot"></a>Anlık Görüntü

Veri Paylaşma davetini kabul ettiğinde bir veri tüketicisi tarafından bir anlık görüntü oluşturulabilir. Bir daveti kabul ettiğinde, bunlarla paylaşılan verilerin tam anlık görüntüsünü tetikleyebilirler. Anlık görüntü, veri tüketicisinin anlık görüntüyü oluşturduğu zaman içindeki bir kopyasıdır. 

İki tür anlık görüntü vardır-tam ve artımlı. Tam anlık görüntü, veri paylaşımında bulunan tüm verileri içerir. Artımlı bir anlık görüntü, son anlık görüntünün tetiklenmesinden sonra güncelleştirilmiş/eklenen tüm verileri içerir. 

## <a name="snapshot-settings-in-azure-data-share"></a>Azure veri paylaşımında anlık görüntü ayarları
 
Veri sağlayıcısı, bir veri paylaşımının anlık görüntü ayarını etkinleştirebilir. Bu ayar, veri tüketicilerinin, ortaya çıktığında artımlı güncelleştirmeler almasına olanak sağlar. Veri sağlayıcının, veri tüketicilerinin paylaşılan verilere yönelik güncelleştirmeleri almasını istiyorsanız, bu ayar etkinleştirilmelidir. 

Bir veri sağlayıcısı bu ayarı etkinleştirmesine izin verirseniz bir yinelenme aralığı seçilebilir. Yinelenme aralığı saatlik veya günlük olabilir. 

Bir veri tüketicisi, bu anlık görüntü zamanlamasını, ilk olarak yeni bir anlık görüntü oluşturduklarında bu yana değiştirilen tüm verileri içeren artımlı güncelleştirmeler almak üzere kabul etme seçeneğine sahiptir. 

## <a name="invitation"></a>Esinin

Veri sağlayıcısı, veri paylaşımında birden fazla alıcıyı davet edebilir. Bu kişiler, veri paylaşımında alıcı ekleyerek yapabilir. Davetler, bir veri paylaşımının oluşturulduktan sonra da eklenebilir. 

Bir veri sağlayıcısı, kabul edilmediyse gönderildikten sonra bir davetiyeyi silebilir. Veri sağlayıcısı bir daveti silerse ve henüz kabul edilmediyse, veri tüketicisini kabul etmez. 

Davetler günde beş kata kadar yeniden kullanılabilir. 

## <a name="recipient"></a>Alıcı

Alıcı, bir veri paylaşımında davetiye alan kişidir. Genellikle, bir veri sağlayıcısı, oluşturdukları veri paylaşımında alıcıları ekler. Bir davetin alıcısı daveti kabul ettiğinde bir veri tüketicisi haline gelir.  

## <a name="next-steps"></a>Sonraki adımlar

Verileri paylaşmaya nasıl başlayacağınızı öğrenmek için [verilerinizi paylaşma](share-your-data.md) öğreticiye geçin.
