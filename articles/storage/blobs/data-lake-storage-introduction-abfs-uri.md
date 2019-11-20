---
title: Azure Data Lake Storage 2. URI 'sini kullanma
description: Azure Data Lake Storage 2. URI 'sini kullanma
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855555"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Azure Data Lake Storage 2. URI 'sini kullanma

Azure Data Lake Storage 2. ile uyumlu [Hadoop FileSystem](https://www.aosabook.org/en/hdfs.html) sürücüsü, kendi düzen tanımlayıcısı `abfs` (Azure blob dosya sistemi) tarafından bilinir. Diğer Hadoop dosya sistemi sürücüleriyle tutarlı, ABFS sürücüsü Data Lake Storage 2. özellikli bir hesap içindeki dosya ve dizinleri adreslemesine yönelik bir URI biçimi kullanır.

## <a name="uri-syntax"></a>URI sözdizimi

Data Lake Storage 2. URI sözdizimi, depolama hesabınızın varsayılan dosya sistemi olarak Data Lake Storage 2. sahip olmak üzere ayarlanmış olup olmadığına bağlıdır.

Adres eklemek istediğiniz Data Lake Storage 2. yetenekli hesap, hesap oluşturma sırasında varsayılan dosya sistemi olarak ayarlanmamışsa, toplu URI sözdizimi şöyledir:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Düzen tanımlayıcısı**: `abfs` Protokol, düzen tanımlayıcısı olarak kullanılır. Güvenli Yuva Katmanı (SSL) bağlantısı olmadan veya bu bağlantıyla bağlama seçeneğiniz vardır. Güvenli `abfss` yuva katmanı bağlantısıyla bağlanmak için kullanın.

2. **Dosya sistemi**: Dosya ve klasörleri tutan üst konum. Bu, Azure depolama Blobları hizmetindeki kapsayıcılarla aynıdır.

3. **Hesap adı**: Oluşturma sırasında depolama hesabınıza verilen ad.

4. **Yollar**: Dizin yapısının eğik çizgi sınırlı`/`() temsili.

5. **Dosya adı**: Tek dosyanın adı. Bir dizinin adreslenmesi durumunda bu parametre isteğe bağlıdır.

Ancak, adres eklemek istediğiniz hesap, hesap oluşturma sırasında varsayılan dosya sistemi olarak ayarlandıysa, toplu URI sözdizimi şöyledir:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Yol**: Dizin yapısının eğik çizgi sınırlı`/`() temsili.

2. **Dosya adı**: Tek dosyanın adı.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
