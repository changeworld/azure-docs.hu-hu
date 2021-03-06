---
title: Gyakori kérdések – GYIK
titleSuffix: Microsoft Genomics
description: Választ kaphat a Microsoft Genomics szolgáltatás használatával kapcsolatos gyakori kérdésekre, beleértve a technikai információkat, az SLA-t és a számlázást is.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986036"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: gyakori kérdések

Ez a cikk a Microsoft Genomicshöz kapcsolódó leggyakoribb lekérdezéseket sorolja fel. A Microsoft Genomics szolgáltatással kapcsolatos további információkért lásd: [Mi az a Microsoft Genomics?](overview-what-is-genomics.md) A hibaelhárítással kapcsolatos további információkért tekintse meg a [hibaelhárítási útmutatót](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Hogyan GATK4-munkafolyamatokat futtathat a Microsoft Genomicson?
A Microsoft Genomics szolgáltatás config. txt fájljában válassza ki a `gatk4`process_name. Vegye figyelembe, hogy a számlázás a szokásos számlázási díjszabás szerint történik.

## <a name="how-do-i-enable-output-compression"></a>Hogyan engedélyezi a kimeneti tömörítést?
A kimeneti vcf vagy gvcf nem kötelező argumentumot használva tömörítheti a kimeneti tömörítést. Ez egyenértékű a `-bgzip` futtatásával, amelyet `-tabix` a vcf vagy gvcf kimeneten, `.gz` (bgzip output) és `.tbi` (tabix output) fájlok létrehozásához. `bgzip` tömöríti a vcf vagy a gvcf fájlt, és `tabix` létrehoz egy indexet a tömörített fájlhoz. Az argumentum egy logikai érték, amely a vcf kimenethez alapértelmezés szerint `false`ra van beállítva, és alapértelmezés szerint `true` a gcvf kimenetéhez. A parancssorban való használathoz adja meg a `-bz` vagy `--bgzip-output` `true`ként (futtassa a bgzip és a tabix) vagy a `false`. Ha ezt az argumentumot a config. txt fájlban szeretné használni, adja hozzá `bgzip_output: true` vagy `bgzip_output: false` a fájlhoz.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Mi a Microsoft Genomics SLA-ja?
Garantáljuk, hogy az idő Microsoft Genomics szolgáltatásának 99,9%-a elérhetővé válik a munkafolyamat API-kéréseinek fogadásához. További információ: [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hogyan jelenik meg az Microsoft Genomics használata a számlán?
A számlák Microsoft Genomics a munkafolyamatok által feldolgozott gigabázisok száma alapján. További információ: [díjszabás](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Hol található a `msgen`-ügyfélhez tartozó összes lehetséges parancs és argumentum listája?
Az elérhető parancsok és argumentumok teljes listáját a `msgen help`futtatásával érheti el. Ha nem ad meg további argumentumot, az megjeleníti az elérhető súgófájlok listáját, amely egy `submit`, `list`, `cancel`és `status`. Ha segítséget szeretne kérni egy adott parancshoz, írja be a következőt: `msgen help command`; a `msgen help submit` például felsorolja az összes beküldési beállítást.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Melyek az `msgen`-ügyfél leggyakrabban használt parancsai?
A leggyakrabban használt parancsok a `msgen`-ügyfél argumentumai a következők: 

 |**Parancs**          |  **Mező leírása** |
 |:--------------------|:-------------         |
 |`list`               |Az elküldött feladatok listáját adja vissza. Argumentumok: `msgen help list`.  |
 |`submit`             |Munkafolyamat-kérést küld a szolgáltatásnak. Argumentumok: `msgen help submit`.|
 |`status`             |A `--workflow-id`által megadott munkafolyamat állapotát adja vissza. Lásd még `msgen help status`. |
 |`cancel`             |A `--workflow-id`által meghatározott munkafolyamat feldolgozásának megszakítására vonatkozó kérelmet küld. Lásd még `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Honnan szerezhetem be a `--api-url-base`értékét?
Lépjen a Azure Portalra, és nyissa meg a genomikai fiók lapját. A **felügyelet** fejléc alatt válassza a **hozzáférési kulcsok**elemet. Itt megtalálja az API URL-címét és a hozzáférési kulcsokat is.

## <a name="where-do-i-get-the-value-for---access-key"></a>Honnan szerezhetem be a `--access-key`értékét?
Lépjen a Azure Portalra, és nyissa meg a genomikai fiók lapját. A **felügyelet** fejléc alatt válassza a **hozzáférési kulcsok**elemet. Itt megtalálja az API URL-címét és a hozzáférési kulcsokat is.

## <a name="why-do-i-need-two-access-keys"></a>Miért van szükség két hozzáférési kulcsra?
Ha a szolgáltatás használatának megszakítása nélkül szeretné frissíteni (újragenerálni), két hozzáférési kulcsra van szüksége. Ha például frissíteni szeretné az első kulcsot, az összes új munkafolyamatnak a második kulcsot kell használnia. Ezután várja meg az összes munkafolyamatot, amely az első kulcsot használja az első kulcs frissítése előtt.

## <a name="do-you-save-my-storage-account-keys"></a>Menti a Storage-fiók kulcsait?
A Storage-fiók kulcsa rövid távú hozzáférési jogkivonatok létrehozására szolgál a Microsoft Genomics szolgáltatás számára a bemeneti fájlok olvasásához és a kimeneti fájlok írásához. Az alapértelmezett jogkivonat időtartama 48 óra. A jogkivonat időtartama a Submit parancs `-sas/--sas-duration` kapcsolójának használatával módosítható. az érték órában van.

## <a name="what-genome-references-can-i-use"></a>Milyen genom-referenciákat használhatok?

A következő hivatkozások támogatottak:

 |Leírások              | `-pa/--process-args` értéke |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (nincs Alt-elemzés) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hogyan formázza a parancssori argumentumokat konfigurációs fájlként? 

a msgen a következő formátumban értelmezi a konfigurációs fájlokat:
* Az összes beállítás kulcs-érték párokként van megadva, és a kulcsok által kettősponttal elválasztott értékeket tartalmaz.
  A szóköz figyelmen kívül lesz hagyva.
* A `#`kal kezdődő vonalak figyelmen kívül lesznek hagyva.
* A hosszú formátumú parancssori argumentumok átállíthatók egy kulcsra, ha kiveszik a kezdő kötőjeleket, és lecserélik a kötőjeleket a szavak között aláhúzással. Íme néhány átalakítási példa:

  |Parancssori argumentum            | Konfigurációs fájl sora |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key: kulcs*              |      
  |`-pa/--process-args R=B37m1`     | *process_args: R-b37m1*        |  

## <a name="next-steps"></a>Következő lépések

A Microsoft Genomics használatának megkezdéséhez használja az alábbi forrásokat:
- Első lépésként futtassa az első munkafolyamatot a Microsoft Genomics szolgáltatáson keresztül. [Munkafolyamat futtatása a Microsoft Genomics szolgáltatással](quickstart-run-genomics-workflow-portal.md)
- Saját adatai elküldése a Microsoft Genomics szolgáltatás általi feldolgozáshoz: [párosított FASTQ](quickstart-input-pair-FASTQ.md) | [Bam](quickstart-input-BAM.md) | [több FASTQ vagy Bam](quickstart-input-multiple.md) 

