---
title: Özel bulut etkinliğini izle
titleSuffix: Azure VMware Solution by CloudSimple
description: Azure VMware çözümünde, uyarılar, olaylar, görevler ve denetim dahil olmak üzere CloudSimple ortamı tarafından kullanılabilen bilgiler açıklanmaktadır.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb597cc71d228a1daaaa4e52d76b301764ff9292
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899312"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>CloudSimple etkinliğine göre VMware çözümünü izleme

CloudSimple etkinlik günlükleri CloudSimple Portal 'da gerçekleştirilen işlemlere bir öngörüler sağlar.  Listede uyarılar, olaylar, görevler ve denetim bulunur.  Hangi işlemlerin ne zaman ve ne zaman yapılacağını belirlemek için etkinlik günlüklerini kullanın.  Etkinlik günlükleri, bir kullanıcı tarafından gerçekleştirilen okuma işlemlerini içermez.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple portalına erişim

[Cloudsimple portalına](access-cloudsimple-portal.md)erişin.

## <a name="activity-information"></a>Etkinlik bilgileri

Etkinlik sayfalarına erişmek için yan menüdeki **etkinlik** ' i seçin.

![Etkinlik sayfasına genel bakış](media/activity-page-overview.png)

Etkinlik sayfasındaki etkinliklerin ayrıntılarını görüntülemek için etkinliğini seçin. Sağ tarafta bir Ayrıntılar bölmesi açılır. Paneldeki eylemler etkinlik türüne bağlıdır. Paneli kapatmak için **X** simgesini tıklatın.

Görüntüyü sıralamak için bir sütun başlığına tıklayın.  Görüntülenecek belirli değerler için sütunları filtreleyebilirsiniz.  **CSV olarak indir** simgesine tıklayarak Etkinlik raporunu indirin.

## <a name="alerts"></a>Uyarılar

Uyarılar, CloudSimple ortamınızdaki önemli etkinliklerin bildirimidir.  Uyarılar, faturalandırma veya Kullanıcı erişimini etkileyen olayları içerir.

Uyarıları doğrulamak ve listeden kaldırmak için listeden bir veya daha fazla seçim yapın ve **Onayla**' ya tıklayın.

Aşağıdaki bilgi sütunları uyarılar için kullanılabilir. **Sütunları Düzenle** ' ye tıklayın ve görüntülemek istediğiniz sütunları seçin.

| Sütun | Açıklama |
------------ | ------------- |
| Uyarı türü | Uyarı kategorisi.|
| Saat | Uyarının gerçekleştiği zaman. |
| Önem derecesi | Uyarının önemi.|
| Kaynak Adı | Kaynağa atanan, özel bulut adı gibi ad. |
| Kaynak Türü | Kaynak kategorisi: özel bulut, bulut rafı. |
| Kaynak kimliği | Kaynağın tanımlayıcısı. |
| Description | Uyarının tetiklendiği açıklama. |
| Onaylandı | Uyarının kabul edilip edilmeyeceğini belirtir. |

## <a name="events"></a>Ekinlikler

Olaylar CloudSimple portalında Kullanıcı ve sistem etkinliğini gösterir. Olaylar sayfası, belirli bir kaynakla ilişkili etkinliği ve etkinin önem derecesini listeler.

Aşağıdaki bilgi sütunları uyarılar için kullanılabilir. **Sütunları Düzenle** ' ye tıklayın ve görüntülemek istediğiniz sütunları seçin.

| Sütun | Açıklama |
------------ | ------------- |
| Saat | Olayın gerçekleştiği tarih ve saat. |
| Olay Türü | Olayı tanımlayan sayısal kod. |
| Önem derecesi | Olay önem derecesi.|
| Kaynak Adı | Kaynağa atanan, özel bulut adı gibi ad. |
| Kaynak Türü | Kaynak kategorisi: özel bulut, bulut rafı. |
| Description | Uyarının tetiklendiği açıklama. |

## <a name="tasks"></a>Görevler

Görevler, 30 saniye veya daha fazla tamamlanması beklenen özel bulut etkinlikleridir. (30 saniyeden az olması beklenen etkinlikler yalnızca olaylar olarak raporlanır.) Özel bulutunuzun görevlerinin ilerlemesini izlemek için görevler sayfalarını açın.

Aşağıdaki bilgi sütunları uyarılar için kullanılabilir. **Sütunları Düzenle** ' ye tıklayın ve görüntülemek istediğiniz sütunları seçin.

| Sütun | Açıklama |
------------ | ------------- |
| Görev Kimliği | Görevin benzersiz tanımlayıcısı. |
| İşlem | Görevin gerçekleştirdiği eylem. |
| Kullanıcı | Görevi tamamlamaya yönelik Kullanıcı atandı. |
| Kaynak Adı | Kaynağa atanan ad. |
| Kaynak Türü | Kaynak kategorisi: özel bulut, bulut rafı. |
| Kaynak kimliği | Kaynağın tanımlayıcısı. |
| Başlangıç | Görevin başlangıç zamanı. |
| End | Görevin bitiş saati. |
| Durum | Geçerli görev durumu. |
| Geçen Süre | Görevin tamamlanması (tamamlandıysa) veya şu anda (devam ediyorsa) alınması için geçen süre. |
| Description | Görev açıklaması. |

## <a name="audit"></a>Denetim

Denetim günlükleri Kullanıcı etkinliğini izler. Tüm kullanıcılar için Kullanıcı etkinliğini izlemek üzere Denetim günlüklerini kullanabilirsiniz.

Aşağıdaki bilgi sütunları uyarılar için kullanılabilir. **Sütunları Düzenle** ' ye tıklayın ve görüntülemek istediğiniz sütunları seçin.

| Sütun | Açıklama |
------------ | ------------- |
| Saat | Denetim girişinin saati. |
| İşlem | Görevin gerçekleştirdiği eylem. |
| Kullanıcı | Göreve atanan kullanıcı. |
| Kaynak Adı | Kaynağa atanan ad. |
| Kaynak Türü | Kaynak kategorisi: özel bulut, bulut rafı. |
| Kaynak kimliği | Kaynağın tanımlayıcısı. |
| Sonuç | Etkinliğin sonucu, örneğin **başarılı**. |
| Geçen Süre | Görevin tamamlanma zamanı. |
| Description | Eylemin açıklaması. |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure'da VMware sanal makinelerini kullanma](quickstart-create-vmware-virtual-machine.md)
* [Özel bulutlar](cloudsimple-private-cloud.md) hakkında daha fazla bilgi edinin
