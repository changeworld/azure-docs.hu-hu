---
title: 'Átalakítás adatkészletbe: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alakíthatja át az adatbevitelt a Microsoft Azure Machine Learning által használt belső adatkészlet-formátumba a Azure Machine Learning átalakítás adatkészletre moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dc3630f4b832a9b15217e8f5cdc03830f15ee2a7
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546758"
---
# <a name="convert-to-dataset"></a>Átalakítás adathalmazzá

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designer (előzetes verzió) adatkészletre való konvertálása a folyamat összes adatának a tervező belső formátumára való átalakításához.
  
A legtöbb esetben nem szükséges a konverzió. Azure Machine Learning implicit módon átalakítja az adatokat a natív adatkészlet formátumára, amikor az adatokat az összes művelet elvégzi. 

Azt javasoljuk, hogy az adatokat az adatkészlet formátumára mentse, ha valamilyen normalizálás vagy tisztítás történt egy adott adathalmazon, és biztosítani szeretné, hogy a módosítások más folyamatokban is használhatók legyenek.  
  
> [!NOTE]
> Az adatkészletre való átalakítás csak az adat formátumát módosítja. Nem menti a munkaterületen lévő új másolatát. Az adatkészlet mentéséhez kattintson duplán a kimeneti portra, válassza a **Mentés adatkészletként**lehetőséget, és adjon meg egy új nevet.  
  
## <a name="how-to-use-convert-to-dataset"></a>A convert for DataSet használata  

Azt javasoljuk, hogy a [metaadatok szerkesztése](edit-metadata.md) modul használatával készítse elő az adatkészletet az adatkészletre való átalakítás használata előtt. Hozzáadhat vagy módosíthat oszlopokat, módosíthatja az adattípusokat, és szükség szerint más módosításokat is végezhet.

1.  Adja hozzá az átalakítás adatkészlethez modult a folyamathoz. Ezt a modult a Designer **Adatátalakítási** kategóriájában találja. 

2. Csatlakoztatása bármely modulhoz, amely egy adatkészletet eredményez.   

    Ha az adat [táblázatos](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), akkor átalakíthatja azt egy adatkészletbe. Ez magában foglalja az [importálási adaton](import-data.md)keresztül betöltött adatokat, az [adatbevitel manuális](enter-data-manually.md)használatával létrehozott adatokat, vagy az átalakított adatkészleteket az [alkalmazás-átalakítás](apply-transformation.md)használatával.

3.  A **művelet** legördülő listában adja meg, hogy az adatkészlet mentése előtt szeretné-e elvégezni az adattisztítást:  
  
    - **Nincs**: használja az-t a következőképpen.  
  
    - **SetMissingValue**: egy adott értéket állít be egy hiányzó értékre az adatkészletben. Az alapértelmezett helyőrző a kérdőjel (?) karakter, de az **Egyéni hiányzó érték** lehetőséggel más értéket is megadhat. Ha például az **Egyéni hiányzó értékhez**a **taxit** adja meg, akkor az adatkészlet összes példánya a hiányzó értékre lesz módosítva.
  
    - **ReplaceValues**: ezzel a beállítással egyetlen pontos értéket adhat meg, amelyet más pontos értékkel kell helyettesíteni. A hiányzó értékeket vagy egyéni értékeket a **replace** metódus beállításával helyettesítheti:

      - **Hiányzik**: válassza ezt a lehetőséget a hiányzó értékek lecserélése a bemeneti adatkészletben. Az **új érték**mezőben adja meg a hiányzó értékeket a értékkel lecserélő értéket.
      - **Egyéni**: válassza ezt a lehetőséget, ha az egyéni értékeket a bemeneti adatkészletben szeretné lecserélni. **Egyéni érték**esetén adja meg a keresett értéket. Ha például az adatok a hiányzó értékek helyőrzőként használt karakterláncot tartalmazzák `obs`, akkor `obs`kell megadnia. Az **új érték**mezőben adja meg az új értéket az eredeti sztring helyett.
  
    Vegye figyelembe, hogy a **ReplaceValues** művelet csak a pontos egyezésekre vonatkozik. Ezek a karakterláncok például nem érintettek: `obs.`, `obsolete`.  
 
  
5.  A folyamat futtatása.  

## <a name="results"></a>Eredmények

+  Ha az eredményül kapott adatkészletet új névvel szeretné menteni, akkor a modul jobb oldali paneljének **outputs (kimenet** ) lapján válassza a (z) ikon **regisztrálása adatkészletet** .  
  
## <a name="technical-notes"></a>Technikai megjegyzések  

-   Az adatkészletet bemenetként használó modulok a CSV-fájlban vagy a TSV-fájlban is végezhetnek adatokat. A modul kódjának futtatása előtt a rendszer elődolgozza a bemeneteket. Az előfeldolgozás egyenértékű a konverzió adatkészlet-modulba való futtatásával a bemeneten.  
  
-   A SVMLight formátuma nem alakítható át adatkészletre.  
  
-   Ha egyéni csere műveletet ad meg, a keresés és csere művelet a teljes értékekre vonatkozik. A részleges egyezések nem engedélyezettek. Például lecserélheti a 3 értéket a-1 vagy a 33-es értékre, de a 3 karakter nem helyettesíthető két számjegyből (például 35).  
  
-   Az egyéni csere műveletei esetén a csere csendesen meghiúsul, ha helyettesítő karaktert használ, amely nem felel meg az oszlop aktuális adattípusának.  

  
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
