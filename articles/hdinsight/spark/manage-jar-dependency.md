---
title: JAR-függőségek kezelése – Azure HDInsight
description: Ez a cikk a Java Archive (JAR) függőségeinek HDInsight-alkalmazásokhoz való kezelésének ajánlott eljárásait ismerteti.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135732"
---
# <a name="jar-dependency-management-best-practices"></a>A JAR függőségek kezelése – ajánlott eljárások

A HDInsight-fürtökre telepített összetevők függőségekkel rendelkeznek a harmadik féltől származó tárakban. Általában a gyakori modulok (például a guava) egy adott verzióját a beépített összetevők hivatkoznak rá. Ha az alkalmazást a függőségeivel együtt küldi el, az ütközést eredményezhet ugyanazon modul különböző verziói között. Ha a osztályútvonal elsőként hivatkozott összetevő-verzió szerepel, a beépített összetevők kivételeket okozhatnak a verzió inkompatibilitása miatt. Ha azonban a beépített összetevők beinjektálják a függőségeiket a osztályútvonal, az alkalmazás olyan hibákat okozhat, mint például a `NoSuchMethod`.

A verziók ütközésének elkerülése érdekében érdemes megfontolni az alkalmazás függőségeinek árnyékolását.

## <a name="what-does-package-shading-mean"></a>Mit jelent a csomagok árnyékolása?
Az árnyékolás lehetővé teszi a függőségek belefoglalását és átnevezését. Visszakeresi az osztályokat, és átírja az érintett bytecode és erőforrásokat a függőségek privát másolatának létrehozásához.

## <a name="how-to-shade-a-package"></a>Hogyan lehet egy csomagot árnyékolni?

### <a name="use-uber-jar"></a>Az Über-jar használata
Az Über-jar egyetlen jar-fájl, amely az alkalmazás jar-t és annak függőségeit is tartalmazza. Az Über-jar függőségei alapértelmezés szerint nem árnyékoltak. Bizonyos esetekben ez a verziók ütközését eredményezheti, ha más összetevők vagy alkalmazások a könyvtárak egy másik verziójára hivatkoznak. Ennek elkerüléséhez létrehozhat egy über-jar-fájlt a függőségek árnyékolt (vagy az összes) használatával.

### <a name="shade-package-using-maven"></a>Csomag árnyékolása a Maven használatával
A Maven a Java és a Scala nyelveken írt alkalmazásokat is felépítheti. A Maven-Shade-plugin segítségével egyszerűen hozhat létre árnyékolt über-jar-t.

Az alábbi példa egy olyan `pom.xml` fájlt mutat be, amely frissítve lett egy csomagnak a Maven-Shade-beépülő modullal való árnyékolása céljából.  Az XML szakasz `<relocation>…</relocation>` az osztályokat `com.google.guava` csomagba helyezi át a csomagba `com.google.shaded.guava` a megfelelő JAR-fájlok bejegyzéseinek áthelyezésével és az érintett bytecode újraírásával.

`pom.xml`módosítása után `mvn package` hajthat végre az árnyékolt über-jar létrehozásához.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>SBT használatával árnyékolt csomag
A SBT egy, a Scala és a Java rendszerhez készült Build eszköz is. A SBT nem rendelkezik olyan árnyalatos beépülő modullal, mint a Maven-Shade-plugin. `build.sbt` fájlt módosíthatja árnyalatos csomagokba. 

A `com.google.guava`árnyalatának árnyékában például az alábbi parancsot adhatja hozzá a `build.sbt` fájlhoz:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Ezután futtathatja `sbt clean` és `sbt assembly` az árnyékolt jar-fájl létrehozásához. 

## <a name="next-steps"></a>Következő lépések

* [HDInsight IntelliJ-eszközök használata](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Scala Maven-alkalmazás létrehozása a Sparkhoz a IntelliJ-ben](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
