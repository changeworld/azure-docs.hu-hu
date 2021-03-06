---
title: Azure Notebooks előnézeti projekt létrehozása egyéni környezettel
description: Hozzon létre egy új projektet Azure Notebooks előzetes verzióban, amely a telepített csomagok és indítási parancsfájlok meghatározott készletével van konfigurálva.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196841"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Rövid útmutató: projekt létrehozása egyéni környezettel Azure Notebooks előzetes verzióban

A projekt Azure notebookok gyűjteménye, fájlok, például a notebookok, adatfájlokat, dokumentáció, lemezképek és így tovább, és a egy környezetben, amelyek adott telepítő parancsokkal konfigurálhatók. A környezet a projekttel definiálásával bárki, aki a projekt klónozza a saját Azure-jegyzetfüzetek fiókba, létre kell hoznia a szükséges környezetet minden adattal rendelkezik.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Projekt létrehozása

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. (Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksre](quickstart-sign-in-azure-notebooks.md)).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n); a gomb csak akkor szerepelhet **+** , ha a böngészőablak keskeny:

    ![Új projekt parancsot a saját projektek lapon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban írja be vagy adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

    - **Projekt neve**: projekt egyéni környezettel
    - **Projekt azonosítója**: projekt – egyéni – környezet
    - **Nyilvános projekt**: (törölve)
    - **Hozzon létre egy readme.MD**: (törölve)

1. Néhány pillanat múlva Azure notebookok nyit meg az új projekt. Vegyen fel egy jegyzetfüzetet a projektbe az **+ új** legördülő menü kiválasztásával (amely csak **+ként** jelenhet meg), majd a **Jegyzetfüzet**lehetőséget választva.

1. Adjon nevet a jegyzetfüzetnek, például *Egyéni környezet. ipynb*, válassza a **Python 3,6** lehetőséget a nyelvhez, majd válassza az **új**lehetőséget.

## <a name="add-a-custom-setup-step"></a>Egyéni telepítés lépés hozzáadása

1. A projekt lapon válassza a **projekt beállításai**lehetőséget.

    ![Projekt beállítások parancs](media/quickstarts/project-settings-command.png)

1. A **projekt beállításai** előugró ablakban válassza a **környezet** lapot, majd a **környezet beállítása lépésnél**válassza a **+ Hozzáadás**lehetőséget:

    ![Parancs használatával adja hozzá az új környezet beállítása lépés](media/quickstarts/environment-add-command.png)

1. A **+ Add** parancs egy művelet és egy, a projektben lévő fájlokból kiválasztott célfájl által meghatározott lépést hoz létre. A következő műveletek támogatottak:

   | Művelet | Leírás |
   | --- | --- |
   | A Requirements.txt | Python-projektek függősége a requirements.txt fájl adja meg. Ezzel a beállítással válassza ki a megfelelő fájlt a projekt fájl listából, és is kiválaszthatja a további legördülő, amely akkor jelenik meg a Python-verzió. Ha szükséges, a **Mégse** gombra kattintva térjen vissza a projekthez, töltse fel vagy hozza létre a fájlt, majd térjen vissza a **projekt beállításai** > **környezet** lapra, és hozzon létre egy új lépést. Ezzel a lépéssel automatikusan futtat egy jegyzetfüzetet a projektben `pip install -r <file>` |
   | Héjparancsfájl | A használatával egy bash rendszerhéj-parancsfájlt (jellemzően egy *. sh* kiterjesztésű fájlt) jelezhet, amely a környezet inicializálásához futtatni kívánt parancsokat tartalmazza. |
   | Environment.yml | A környezetek kezelésére szolgáló Conda használó Python-projekt egy Environment *. YML* fájlt használ a függőségek leírására. Ezzel a beállítással a projekt fájl listából válassza ki a megfelelő fájlt. |

   > [!WARNING]
   > Mivel ez egy előzetes verziójú szolgáltatás a fejlesztés alatt, jelenleg egy ismert probléma van, ahol a `Environment.yml` beállítás nem lesz alkalmazva a projektre a várt módon. A projekt és a Jupyter-jegyzetfüzetek nem töltik be a megadott környezeti fájlt.

1. A telepítési lépések eltávolításához válassza a lépéstől jobbra lévő **X-et** .

1. Ha az összes telepítési lépés bekerül, válassza a **Mentés**lehetőséget. (A módosítások elvetéséhez kattintson a **Mégse** gombra).

1. A környezet teszteléséhez hozzon létre és futtasson egy új jegyzetfüzetet, majd hozzon létre egy kódot tartalmazó cellát olyan utasításokkal, amelyek a környezetben lévő csomagtól függenek, például egy Python `import` utasítás használatával. Ha az utasítás végrehajtása sikeres, majd a szükséges csomag sikeresen telepítve a környezetben.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Projektek kezelése és konfigurálása Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet futtatásának létrehozása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
