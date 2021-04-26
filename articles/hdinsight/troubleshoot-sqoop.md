---
title: Sqoop içeri/dışarı aktarma komutu, ESP kümelerindeki bazı kullanıcılar için başarısız oluyor-Azure HDInsight
description: 'Apache Sqoop içeri/dışarı aktarma komutu "Import Failed: Java. IO. IOException:/user/YourUserName/.exe hazırlama dizinindeki sahiplik beklenen şekilde değil" Azure HDInsight ESP kümesindeki bazı kullanıcılar için bu hata ile başarısız oluyor'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387371"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Senaryo: Sqoop içeri/dışarı aktarma komutu, Azure HDInsight ESP kümelerinde 20 karakterden daha uzun Kullanıcı adları için başarısız oluyor

Bu makalede, ADLS 2. (ABFS) depolama hesabı kullanılarak Azure HDInsight ESP (kurumsal güvenlik paketi) etkin kümeler kullanılırken bilinen bir sorun ve geçici çözüm açıklanmaktadır.

## <a name="issue"></a>Sorun

Sqoop içeri/dışarı aktarma komutu çalıştırılırken, bazı kullanıcılar için aşağıdaki hatayla başarısız olur:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

Yukarıdaki örnekte, `/user/yourlongdomainuserna/.staging` Kullanıcı adı için kesilmiş 20 karakter Kullanıcı adını görüntüler `yourlongdomainusername` .

## <a name="cause"></a>Nedeni

Kullanıcı adının uzunluğu 20 karakteri aşıyor. 

Daha ayrıntılı bilgi için [nesnelerin ve kimlik bilgilerinin Azure Active Directory Domain Services yönetilen bir etki alanında nasıl eşitleneceğini](../active-directory-domain-services/synchronization.md) inceleyin.

## <a name="workaround"></a>Geçici çözüm

20 karakterden daha küçük veya bu değere eşit bir Kullanıcı adı kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
