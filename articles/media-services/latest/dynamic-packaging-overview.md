---
title: Dinamikus csomagolás a Azure Media Services v3-ban
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt a Azure Media Services dinamikus csomagolásáról.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: d408a862c18038f64b816bb54fc235d1b9d84179
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240379"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dinamikus csomagolás a Media Services v3-ban

A Microsoft Azure Media Services számos médiafájl-formátum kódolására használható. A szolgáltatás különböző streaming protokollokon keresztül érhető el, tartalom védelme nélkül vagy anélkül, hogy elérje az összes jelentős eszközt (például az iOS és az Android rendszerű eszközöket). Ezek az ügyfelek megismerhetik a különböző protokollokat. Az iOS esetében például HTTP Live Streaming (HLS) formátumban kell továbbítani a streameket, és az Android-eszközök támogatják a HLS, valamint az MPEG DASH-t.

Media Services a streaming- [végpont](streaming-endpoint-concept.md) egy dinamikus (igény szerinti) csomagolási és forrás-szolgáltatást jelent, amely az élő és az igény szerinti tartalmat közvetlenül az ügyfélalkalmazás számára teszi elérhetővé. A következő szakaszban említett Common Streaming Media-protokollok egyikét használja. A dinamikus csomagolási funkciót minden Streamvégpont tartalmazza (a standard és a prémium szintűek egyaránt).

> [!NOTE]
> A [Azure Portal](https://portal.azure.com/) a v3 [élő események](live-events-outputs-concept.md)kezelésére, a v3- [eszközök](assets-concept.md)megtekintésére, az API-k elérésére vonatkozó információk beszerzésére használható. Az összes többi felügyeleti feladathoz (például átalakításokhoz és feladatokhoz) használja a [REST API](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)vagy az egyik támogatott [SDK](media-services-apis-overview.md#sdks)-t.

## <a name="a-iddelivery-protocolsto-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>a forrásfájlok szállításra való előkészítéséhez

A dinamikus csomagolás kihasználása érdekében a közbenső (forrás) fájlt több bitrátás MP4-(ISO Base Media 14496-12) fájlba kell [kódolnia](encoding-concept.md) . Media Services dinamikus csomagoláshoz szükséges, kódolt MP4-és folyamatos átviteli konfigurációs fájlokkal rendelkező [eszközre](assets-concept.md) van szükség. Ebből az MP4-fájlokból a dinamikus csomagolás használatával továbbíthatja a videókat az alább ismertetett Streaming Media-protokollok segítségével.

> [!TIP]
> Az MP4 és a streaming konfigurációs fájlok beszerzésének egyik módja, ha a [köztes fájlt a Media Services használatával kódolja](#encode-to-adaptive-bitrate-mp4s). 

Ahhoz, hogy a kódolt eszközön a videók elérhetők legyenek az ügyfelek számára a lejátszáshoz, létre kell hoznia egy [folyamatos átviteli lokátort](streaming-locators-concept.md) és a streaming URL-címeket. Ezután a streaming Client manifest (HLS, MPEG DASH vagy Smooth Streaming) megadott formátuma alapján megkapja az adatfolyamot a választott protokollon.

Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Ha a tartalom Media Services dinamikus titkosítással való védelemmel való ellátását tervezi, tekintse meg a [streaming protokollok és a titkosítási típusok](content-protection-overview.md#streaming-protocols-and-encryption-types)című témakört.

### <a name="hls-protocol"></a>HLS protokoll

A streaming-ügyfél a következő HLS-formátumokat adhatja meg:

|Protokoll|Példa|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>MPEG-DASH protokoll

A streaming-ügyfél a következő MPEG-DASH formátumokat adhatja meg:

|Protokoll|Példa|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Smooth Streaming protokoll

A folyamatos átviteli ügyfél a következő Smooth Streaming formátumokat adhatja meg:

|Protokoll|Megjegyzések/példák| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2,0 (örökölt jegyzékfájl)|Alapértelmezés szerint Smooth Streaming jegyzékfájl formátuma az ismétlés címkét (r-tag) tartalmazza. Bizonyos játékosok azonban nem támogatják a `r-tag`. Az ezekkel a játékosokkal rendelkező ügyfelek használhatnak olyan formátumot, amely letiltja az r-címkét:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

## <a name="on-demand-streaming-workflow"></a>Igény szerinti folyamatos átviteli munkafolyamat

Az alábbi lépések egy közös Media Services streaming-munkafolyamatot mutatnak be, amelyben a dinamikus csomagolás a Azure Media Services standard kódolóval együtt használható.

1. Töltsön fel egy bemeneti fájlt, például egy QuickTime-/MOV-vagy MXF-fájlt. Ezt a fájlt a köztes vagy a forrásfájl is nevezik. A támogatott formátumok listáját a [Media Encoder standard által támogatott formátumok](media-encoder-standard-formats.md)között tekintheti meg.
1. A köztes fájl [kódolása](#encode-to-adaptive-bitrate-mp4s) egy H. 264/AAC MP4 adaptív sávszélesség-készletbe.
1. Tegye közzé az adaptív sávszélességű MP4-készletet tartalmazó kimeneti eszközt. Egy adatfolyam-kereső létrehozásával teheti közzé.
1. Különböző formátumokat (HLS, MPEG-DASH és Smooth Streaming) célzó URL-címeket hozhat létre. A **folyamatos átviteli végpont** gondoskodik a megfelelő jegyzékfájl és kérések kiszolgálásáról a különböző formátumokhoz.

A következő ábra az igény szerinti folyamatos átvitelt mutatja be dinamikus csomagolási munkafolyamattal.

![Az igény szerinti folyamatos átvitelre szolgáló munkafolyamat ábrája dinamikus csomagolással](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódolás adaptív sávszélességű MP4

A következő cikkek példákat mutatnak [be a videók Media Services használatával történő kódolására](encoding-concept.md):

* [KÓDOLÁS HTTPS-URL-címről beépített beállításkészletek használatával](job-input-from-http-how-to.md).
* [Helyi fájl kódolása beépített beállításkészletek használatával](job-input-from-local-file-how-to.md).
* [Hozzon létre egy egyéni beállításkészletet az adott forgatókönyv vagy eszköz követelményeinek megcélzásához](customize-encoder-presets-how-to.md).

Tekintse meg Media Encoder Standard [formátumok és kodekek](media-encoder-standard-formats.md)listáját.

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Egy élő esemény lehet egy *átmenő* (egy helyszíni élő kódoló több bitrátás streamet küld) vagy *élő kódolást* (a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld). 

Az alábbi gyakori munkafolyamat a dinamikus csomagolással folytatott élő közvetítéshez használható:

1. Hozzon létre egy [élő eseményt](live-events-outputs-concept.md).
1. Szerezze be a betöltési URL-címet, és konfigurálja a helyszíni kódolót úgy, hogy az URL-cím használatával küldje el a hozzájárulási csatornát.
1. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a rendszer beolvassa-e a kódolótól érkező adatokat.
1. Hozzon létre egy új eszközt.
1. Hozzon létre egy élő kimenetet, és használja a létrehozott eszköz nevét.<br />Az élő kimenet archiválja a streamet az eszközre.
1. Hozzon létre egy streaming-keresőt a beépített folyamatos átviteli házirend-típusokkal.<br />Ha titkosítani szeretné a tartalmakat, tekintse át a [tartalomvédelem áttekintését](content-protection-overview.md).
1. Listázza a streaming-lokátor elérési útját a használandó URL-címek lekéréséhez.
1. Szerezze be annak a streaming-végpontnak az állomásnevét, amelyről streamet szeretne továbbítani.
1. Különböző formátumokat (HLS, MPEG-DASH és Smooth Streaming) célzó URL-címeket hozhat létre. A folyamatos átviteli végpont gondoskodik a megfelelő jegyzékfájl és kérések kiszolgálásáról a különböző formátumokban.

Ez az ábra az élő adatfolyamok dinamikus csomagolással való működésének munkafolyamatát mutatja be:

![A dinamikus csomagolással rendelkező, átmenő kódolásra szolgáló munkafolyamat ábrája](./media/live-streaming/pass-through.svg)

További információ a Media Services v3 élő közvetítéséről: [élő közvetítés – áttekintés](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Dinamikus csomagolás által támogatott videó-kodekek

A dinamikus csomagolás támogatja a [h. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC vagy AVC1) és a [h. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 vagy hvc1) kódolású videókat tartalmazó MP4-fájlokat.

> [!NOTE]
> Akár 4K-ig terjedő, akár 60-es képkockákból álló és a frame sebességű, dinamikus csomagolással tesztelt felbontások is megoldhatók. A [prémium szintű kódoló](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) támogatja a H. 265 kódolást az örökölt v2 API-kon keresztül.

## <a name="a-idaudio-codecsaudio-codecs-supported-by-dynamic-packaging"></a>Dinamikus csomagolás által támogatott hangkodekek <a id="audio-codecs"/>

A dinamikus csomagolás támogatja a következő protokollokkal kódolt hanganyagot:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, it-AAC v1 vagy it-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (bővített AC-3 vagy E-AC3)
* Dolby Atmos<br />
   A folyamatos átviteli Dolby Atmos-tartalom olyan szabványok esetében támogatott, mint például az MPEG-DASH protokoll a Common Streaming Format (CSF) vagy a Common Media Application Format (CMAF) darabolt MP4-vel, valamint a (z) HTTP Live Streaming (HLS) és a CMAF használatával.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   A DASH-CSF, DASH-CMAF, HLS-M2TS és HLS-CMAF csomagolási formátumok által támogatott DTS-kodekek a következők:  

    * DTS digitális surround (dtsc)
    * DTS-HD nagy felbontású és DTS-HD Master hang (dtsh)
    * DTS Express (dtse)
    * DTS-HD veszteségmentes (nincs mag) (DTSL)

A dinamikus csomagolás több hangsávot is támogat a DASH vagy a HLS (4-es vagy újabb verzió) esetében, így több, több kodekkel és nyelvtel rendelkező hangsávot tartalmazó adatfolyam-továbbítási eszköz is használható.

### <a name="additional-notes"></a>További megjegyzések

A dinamikus csomagolás nem támogatja a [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) hanganyagot tartalmazó fájlokat (ez egy örökölt kodek).

> [!NOTE]
> A [prémium szintű kódoló](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) támogatja a Dolby Digital Plus kódolását az örökölt v2 API-kon keresztül.

## <a name="manifests"></a>Jegyzékek

Media Services dinamikus csomagolásban a HLS, MPEG-DASH és Smooth Streaming adatfolyam-ügyfél-jegyzékfájlok dinamikusan jönnek létre az URL-cím kiválasztó alapján.  

A jegyzékfájlok olyan adatfolyam-metaadatokat tartalmaznak, mint például a követés típusa (hang, videó vagy szöveg), a nyomon követési idő, a kezdő és a befejező időpont, a bitráta (Tulajdonságok), a nyelvek nyomon követése, a megjelenítési időszak (rögzített időtartamú ablak) és a video codec (FourCC). Arra is utasítja a lejátszót, hogy a következő töredéket olvassa be a rendelkezésre álló, valamint a helyükre tartozó következő lejátszható videó-töredékek adatainak megadásával. A töredékek (vagy szegmensek) a videotartalom tényleges "darabjai".

### <a name="examples"></a>Példák

#### <a name="hls"></a>HLS

Íme egy példa egy HLS manifest-fájlra, más néven HLS fő lejátszási listára: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Íme egy példa egy MPEG-DASH manifest-fájlra, más néven MPEG-DASH Media Presentation Description (MPD):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Példa Smooth Streaming manifest-fájlra:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>A jegyzékben szereplő számok elnevezése

Ha meg van adva egy hangsáv neve az. ISM fájlban, Media Services hozzáadja a `AdaptationSet`n belüli `Label` elemet az adott hangsávokhoz tartozó anyagminta-információk megadásához. Példa a kimeneti kötőjel jegyzékfájlra:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

A lejátszó használhatja a `Label` elemet a felhasználói felületen való megjelenítéshez.

### <a name="signaling-audio-description-tracks"></a>A hangjelzések leírásának nyomon követése

Kísérőszöveget adhat a videóhoz, hogy az ügyfelek vizuálisan korlátozott számú ügyfelet a Kísérőszöveg figyelésével kövessék. A jegyzékfájlban hanganyagként kell jegyzeteket adni. Ehhez adja hozzá a "kisegítő adatok" és a "szerepkör" paramétereket az. ISM fájlhoz. Az Ön felelőssége, hogy ezeket a paramétereket helyesen adja meg, hogy hangvételt jelezzen a hanganyagok leírásaként. Például vegyen fel `<param name="accessibility" value="description" />`t, és `<param name="role" value="alternate"` az. ISM-fájlba egy adott hangfelvételhez. 

További információ: [útmutató a Description hangfrekvenciás követéséhez](signal-descriptive-audio-howto.md) .

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming jegyzékfájl

Ha Smooth Streaming adatfolyamot játszik, a jegyzékfájl `Accessibility` és `Role` attribútumok értékeit fogja továbbítani az adott hangsávokhoz. Például a rendszer a `StreamIndex` elemben adja hozzá a `Role="alternate" Accessibility="description"`t, hogy jelezze a hang leírását.

#### <a name="dash-manifest"></a>KÖTŐJEL jegyzékfájlja

A DASH manifest esetében a következő két elemet kell felvenni a hang leírásának jelzéséhez:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS lejátszási lista

A HLS v7 és a fenti `(format=m3u8-cmaf)`esetén a lejátszási lista a hang leírásának nyomon követése után `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"`.

#### <a name="example"></a>Példa

További információ: [a hangleírás nyomon követése](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Dinamikus jegyzékfájl

A játékosok számára eljuttatott zeneszámok, formátumok, bitráták és a bemutató időpontok számának szabályozásához dinamikus szűrést használhat a Media Services dinamikus csomagoló használatával. További információkért lásd: [a dinamikus csomagolóval kapcsolatos előzetes szűrési jegyzékek](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A *dinamikus titkosítás* használatával az élő vagy igény szerinti tartalmakat az AES-128 vagy a három nagy digitális jogkezelési (DRM) rendszerű rendszeren keresztül dinamikusan titkosíthatja: Microsoft PlayReady, Google Widevine és Apple Fairplay. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a DRM-licencek engedélyezésére a hitelesítő ügyfelek számára. További információ: [dinamikus titkosítás](content-protection-overview.md).

> [!NOTE]
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="more-information"></a>További információ

Tekintse meg [Azure Media Services közösségét](media-services-community.md) , hogy különböző módokon lásson kérdéseket, visszajelzést küldjön, és frissítéseket kérjen a Media Servicesról.

## <a name="need-help"></a>Segítségre van szüksége?

A támogatási jegyet az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)való navigálással nyithatja meg.

## <a name="next-steps"></a>Következő lépések

[Videók feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
