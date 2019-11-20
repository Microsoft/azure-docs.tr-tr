---
title: Azure Içeri/dışarı aktarma işlerinin durumunu görüntüleme | Microsoft Docs
description: Içeri/dışarı aktarma işlerinin ve kullanılan sürücülerin durumunu görüntülemeyi öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821433"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Azure Içeri/dışarı aktarma işlerinin durumunu görüntüleme

Bu makalede, Azure Içeri/dışarı aktarma işlerinin sürücü ve iş durumunun nasıl görüntüleneceği hakkında bilgi verilmektedir. Azure Içeri/dışarı aktarma hizmeti, büyük miktarlarda verileri Azure Blob 'larına ve Azure dosyalarına güvenli bir şekilde aktarmak için kullanılır. Hizmet, verileri Azure Blob depolamadan dışarı aktarmak için de kullanılır.  

## <a name="view-job-and-drive-status"></a>İşi ve sürücü durumunu görüntüleme
Azure portal içeri aktarma veya dışarı aktarma işlerinizin durumunu izleyebilirsiniz. **İçeri/dışarı aktarma** sekmesine tıklayın. İşlerin bir listesi sayfada görüntülenir.

![Iş durumunu görüntüle](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>İş durumunu görüntüleme

Sürücünüzün işlemde bulunduğu yere bağlı olarak aşağıdaki iş durumlardan birini görürsünüz.

| İş durumu | Açıklama |
|:--- |:--- |
| Oluşturma | Bir iş oluşturulduktan sonra, durumu **oluşturma**olarak ayarlanır. İş **oluşturuluyor** durumundaysa, Içeri/dışarı aktarma hizmeti, sürücülerin veri merkezine gönderilmediğinden varsayılmaktadır. Bir iş, iki hafta boyunca bu durumda kalabilir ve sonrasında hizmet tarafından otomatik olarak silinir. |
| Servisinin | Paketinizi gönderdikten sonra, Azure portal izleme bilgilerini güncelleştirmeniz gerekir.  Bu işlem, işi **teslim** durumuna getirir. İş, en fazla iki hafta boyunca **Sevkiyat** durumunda kalır. 
| Alındı | Tüm sürücüler veri merkezinde alındıktan sonra iş durumu **alındı**olarak ayarlanır. |
| Aktarma | En az bir sürücü işlemeye başladıktan sonra, iş durumu **aktarma**olarak ayarlanır. Daha fazla bilgi için [Sürücü durumları](#view-drive-status)' na gidin. |
| Paketleme | Tüm sürücüler işlemeyi tamamladıktan sonra, diskler size geri gönderilene kadar iş **paketleme** durumuna yerleştirilir. |
| Tamamlandı | Tüm sürücüler size geri gönderildikten sonra, iş hatasız tamamlanırsa, iş **tamamlandı**olarak ayarlanır. İş, **tamamlandı** durumunda 90 gün sonra otomatik olarak silinir. |
| kapandı | Tüm sürücüler size geri gönderildikten sonra, işin işlenmesi sırasında herhangi bir hata oluşursa iş **kapalı**olarak ayarlanır. İş, **kapalı** durumda 90 gün sonra otomatik olarak silinir. |

## <a name="view-drive-status"></a>Sürücü durumunu görüntüleme

Aşağıdaki tabloda, bir içeri aktarma veya dışarı aktarma işi aracılığıyla geçiş yaparken tek bir sürücünün yaşam döngüsü açıklanmaktadır. Bir işteki her sürücünün geçerli durumu Azure portal görülür.

Aşağıdaki tabloda, bir işteki her bir sürücünün geçeme durumu açıklanmaktadır.

| Sürücü durumu | Açıklama |
|:--- |:--- |
| Belirtilir | İçeri aktarma işi için, iş Azure portal oluşturulduğunda, bir sürücü için ilk durum **belirtilir**. Bir dışarı aktarma işi için, iş oluşturulduğunda bir sürücü belirtilmediğinden, ilk sürücü durumu **alınır**. |
| Alındı | Içeri/dışarı aktarma hizmeti bir içeri aktarma işi için sevkiyat şirketten alınan sürücüleri işlediğinde, sürücü **alındı** durumuna geçer. Bir dışarı aktarma işi için ilk sürücü durumu **alınan** durumudur. |
| NeverReceived | Bir işin paketi ulaştığında, ancak paket sürücüyü içermiyorsa, sürücü **Neverreceived** durumuna gider. Ayrıca bir sürücü, hizmetin sevkiyat bilgilerini almasından bu yana iki hafta sonra da bu duruma gider, ancak paket henüz veri merkezinde gelmedi. |
| Aktarma | Hizmet, sürücüden verileri Azure depolama 'ya aktarmaya başladığında bir sürücü **aktarma** durumuna gider. |
| Tamamlandı | Hizmet, tüm verileri hatasız bir şekilde aktarmışsa, bir sürücü **tamamlandı** durumuna gider.
| Completedınfo | Bir sürücü, verileri sürücüden ya da sürücüden kopyalarken bazı sorunlarla karşılaşıldığında, bir sürücü **Completedınfoınfo** durumuna gider. Bilgiler, Blobların üzerine yazma hakkında hata, uyarı veya bilgi iletileri içerebilir.
| ShippedBack | Bir sürücü, veri merkezinden dönüş adresine geri gönderildiğinde **ShippedBack** durumuna gider. |

Azure portal bu görüntüde örnek bir işin sürücü durumu görüntülenir:

![Sürücü durumunu görüntüle](./media/storage-import-export-service/drivestate.png)

Aşağıdaki tabloda, her durum için sürücü hatası durumları ve gerçekleştirilen eylemler açıklanmaktadır.

| Sürücü durumu | Olay | Çözüm/sonraki adım |
|:--- |:--- |:--- |
| NeverReceived | **Neverreceived** olarak işaretlenen bir sürücü (iş sevkiyatının bir parçası olarak alınmadığı için) başka bir sevkiyata ulaştı. | İşlemler ekibi, sürücüyü **alındı**olarak kaydırır. |
| Yok | Herhangi bir işin parçası olmayan bir sürücü, veri merkezinde başka bir işin parçası olarak ulaşır. | Sürücü ek bir sürücü olarak işaretlenir ve özgün paketle ilişkili iş tamamlandığında size döndürülür. |

## <a name="time-to-process-job"></a>İşi işleme süresi
İçeri/dışarı aktarma işinin işlenmesi için gereken süre, şöyle bir dizi etkene göre farklılık gösterir:

-  Sevkiyat Zamanı
-  Veri merkezinde yük
-  Kopyalandığı verilerin iş türü ve boyutu
-  Bir işteki disk sayısı. 

İçeri/dışarı aktarma hizmeti bir SLA 'ya sahip değil, ancak hizmet, diskler alındıktan sonra 7 güne kadar kopyayı tamamlamaya çalışır. Azure portalında gönderilen duruma ek olarak, REST API 'Leri iş ilerlemesini izlemek için kullanılabilir. [Işleri Listele](/previous-versions/azure/dn529083(v=azure.100)) işlem API çağrısı ' nda tamamlanma yüzdesi parametresi, kopyalama ilerleme durumunu yüzdesini sağlar.


## <a name="next-steps"></a>Sonraki adımlar

* [Waımportexport aracını ayarlama](storage-import-export-tool-how-to.md)
* [AzCopy komut satırı yardımcı programıyla veri aktarma](storage-use-azcopy.md)
* [Azure Içeri aktarma dışarı aktarma REST API örneği](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
