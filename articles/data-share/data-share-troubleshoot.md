---
title: Azure Veri Paylaşımı sorunlarını giderme
description: Azure veri paylaşımı ile veri paylaşımları oluştururken veya alırken davetiye ve hatalarla ilgili sorunları nasıl giderebileceğinizi öğrenin.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490562"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Azure veri paylaşımında sık karşılaşılan sorunları giderme 

Bu makalede, Azure veri paylaşımında yaygın sorunların nasıl giderileceği gösterilmektedir. 

## <a name="azure-data-share-invitations"></a>Azure Data Share davetleri 

Bazı durumlarda, yeni bir kullanıcı gönderilen e-posta **davetinde daveti kabul et** ' i tıklattığında, bu kişiler boş bir davetiye listesi ile sunulabilir. 

![Davetiye yok](media/no-invites.png)

Yukarıdaki hata, hizmette bilinen bir sorundur ve şu anda giderilmekte. Geçici bir çözüm olarak, aşağıdaki adımları izleyin. 

1. Azure portal **abonelikler** ' e gidin
1. Azure veri paylaşımında kullandığınız aboneliği seçin
1. **Kaynak sağlayıcıları** ' na tıklayın
1. Microsoft. DataShare araması yapın
1. **Kaydet** 'e tıklayın

Bu adımları tamamlayabilmeniz için [Azure KATıLıMCıSı RBAC rolüne](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) sahip olmanız gerekir. 

Hala bir veri paylaşma daveti göremiyorsanız, veri sağlayıcınızla iletişim kurun ve daveti e-posta diğer adınızla *değil* Azure oturum açma e-posta adresinize gönderdiklerinden emin olun. 

> [!IMPORTANT]
> Azure Data Share davetini zaten kabul etmiş ve depolamayı yapılandırmadan önce hizmetten çıkdıysanız, alınan veri paylaşımınızı yapılandırmayı nasıl tamamlayacağınızı öğrenmek için [bir veri kümesi eşleme](how-to-configure-mapping.md) ile nasıl yapılır Kılavuzu ' nda açıklanan yönergeleri izleyin ve veri almaya başlayın. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Yeni veri paylaşımının oluşturulması veya alınması sırasında hata oluştu

"Hata: Işlem geçersiz bir durum kodu döndürdü: ' BadRequest '"

"Hata: AuthorizationFailed"

"Hata: depolama hesabına rol ataması"

![Ayrıcalık hatası](media/error-write-privilege.png)

Yeni veri paylaşımı oluştururken veya alırken yukarıdaki hatalardan birini alırsanız, bunun nedeni depolama hesabı üzerinde yeterli izinlerin olmamasıdır. Gerekli izinler, depolama sahibi rolünde bulunan veya özel bir role atanabilecek *Microsoft. Authorization/role atamaları/yazma*' dır. Depolama hesabını siz oluşturmuş olsanız bile, bu sizi otomatik olarak depolama hesabının sahibi yapmaz. Kendinize depolama hesabı sahipliği vermek için şu adımları izleyin. Alternatif olarak, ' ye ' ye ekleyebileceğiniz Bu izinle özel bir rol oluşturulabilir.  

1. Azure portalında Depolama hesabına gidin
1. **Erişim denetimi (IAM)** seçeneğini belirleyin
1. **Ekle** 'ye tıklayın
1. Sahibini sahip olarak içine ekleyin.

## <a name="troubleshooting-sql-based-sharing"></a>SQL tabanlı Paylaşım sorunlarını giderme

"Hata: x veri kümeleri eklenemedi çünkü paylaşmak için gerekli izinlere sahip değilsiniz."

SQL tabanlı bir kaynaktan veri kümesi eklerken bu hatayı alırsanız, bu durum SQL Server Azure Data Share MSI için bir Kullanıcı oluşturmadınız.  Bu sorunu çözmek için aşağıdaki betiği çalıştırın:

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
*< Share_acc_name >* , veri paylaşma hesabınızın adı olduğunu unutmayın. Henüz bir veri paylaşma hesabı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.         

[Veri öğreticinizi paylaşma](share-your-data.md) bölümünde listelenen tüm önkoşulları izlediğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Verileri paylaşmaya nasıl başlayacağınızı öğrenmek için [verilerinizi paylaşma](share-your-data.md) öğreticiye geçin.

