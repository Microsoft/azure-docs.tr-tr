---
title: Azure Marketi için ortak SAS URL 'SI sorunları ve düzeltmeleri
description: Paylaşılan erişim imzası URI 'Leri ve olası çözümleri kullanma hakkında yaygın sorunları listeleyin.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 502ba1a65f9b0740a51c7a4da219cc87af494f27
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813309"
---
# <a name="common-sas-url-issues-and-fixes"></a>Ortak SAS URL 'SI sorunları ve düzeltmeleri

Aşağıdaki tabloda, önerilen çözümlerin yanı sıra paylaşılan erişim imzaları (çözümünüz için karşıya yüklenen VHD 'leri tanımlamak ve paylaşmak için kullanılır) ile çalışırken karşılaşılan bazı yaygın sorunlar listelenmektedir.

| **Konuda** | **Hata Iletisi** | **Onar** | 
| --------- | ------------------- | ------- | 
| *görüntüleri kopyalarken hata* &emsp; |  |  |
| SAS URL 'sinde "?" bulunamadı | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Önerilen araçları kullanarak SAS URL 'sini güncelleştirin. |
| SAS URL 'SI içinde "St" ve "sa" parametreleri yok | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URL 'sini, içinde uygun **Başlangıç tarihi** ve **bitiş tarihi** değerleriyle güncelleştirin. | 
| "SP = rl" SAS URL 'SI içinde değil | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | `Read` ve `List`olarak ayarlanan izin kümesi SAS URL 'sini güncelleştirin. | 
| SAS URL 'sinde, VHD adında boşluk vardır | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Boşluk kaldırmak için SAS URL 'sini güncelleştirin. |
| SAS URL 'SI yetkilendirme hatası | `Failure: Copying Images. Not able to download blob due to authorization error` | SAS URI biçimini gözden geçirin ve düzeltin. Gerekirse yeniden oluşturun. |
| SAS URL 'SI "St" ve "o" parametrelerinin tam tarih-saat belirtimi yoktur | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS URL 'SI **Başlangıç tarihi** ve **bitiş tarihi** parametrelerinin (`st` ve `se` alt dizelerinin), `11-02-2017T00:00:00Z`gibi tam bir tarih saat biçimi olması gerekir. Kısaltılmış sürümler geçerli değil. (Azure CLı 'deki bazı komutlar varsayılan olarak kısaltılmış değerler oluşturabilir.) | 
|  |  |  |

Daha fazla bilgi için bkz. [paylaşılan erişim Imzalarını kullanma (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
