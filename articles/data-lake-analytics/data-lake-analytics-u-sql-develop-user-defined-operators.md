---
title: U-SQL Kullanıcı tanımlı işleçler geliştirme-Azure Data Lake Analytics
description: Azure Data Lake Analytics işlerinde kullanılmak ve yeniden kullanmak üzere Kullanıcı tanımlı işleçler geliştirmeyi öğrenin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316533"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>U-SQL Kullanıcı tanımlı işleçler (UDOs) geliştirme
Bu makalede, bir U-SQL işinde verileri işlemek için Kullanıcı tanımlı işleçlerin nasıl geliştirileceği açıklanır.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>U-SQL içinde Kullanıcı tanımlı bir işleç tanımlama ve kullanma
**Bir U-SQL işi oluşturmak ve göndermek için**

1. Visual Studio 'da **yeni > project > U-SQL projesi > dosya**seçin.
2. **Tamam**'ı tıklatın. Visual Studio, Script. usql dosyası ile bir çözüm oluşturur.
3. **Çözüm Gezgini**, Script. usql öğesini genişletin ve ardından **Script.usql.cs**öğesine çift tıklayın.
4. Aşağıdaki kodu dosyasına yapıştırın:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. **Script. usql dosyasını**açın ve şu U-SQL betiğini yapıştırın:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Data Lake Analytics hesabını, Veritabanı'nı ve Şema'yı belirtin.
8. **Çözüm Gezgini**'nden, **Script.usql** öğesine sağ tıklayın ve ardından **Betik Oluştur**'a tıklayın.
9. **Çözüm Gezgini**'nden, **Script.usql** öğesine sağ tıklayın ve ardından **Betiği Gönder**'e tıklayın.
10. Azure aboneliğinize bağlanmadıysanız Azure hesabı kimlik bilgilerinizi girmeniz istenir.
11. **Gönder**'e tıklayın. Gönderim tamamlandığında, sonuç penceresinde gönderim sonuçları ve iş bağlantısı bulunur.
12. En son iş durumunu görmek ve ekranı yenilemek için **Yenile** düğmesine tıklayın.

**Çıktıyı görmek için**

1. **Sunucu Gezgini**, **Azure**' ı genişletin, **Data Lake Analytics**' i genişletin, Data Lake Analytics hesabınızı genişletin, **depolama hesapları**' nı genişletin, varsayılan depolama alanına sağ tıklayın ve ardından **Gezgin**' e tıklayın.
2. Örnekler ' i genişletin, çıktılar ' i genişletin ve ardından **Drivers. csv**' ye çift tıklayın.

## <a name="see-also"></a>Ayrıca bkz.
* [U-SQL Ifadelerini Kullanıcı kodu ile genişletme](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Visual Studio için Data Lake araçları 'nı kullanarak U-SQL uygulamaları geliştirme](data-lake-analytics-data-lake-tools-get-started.md)
