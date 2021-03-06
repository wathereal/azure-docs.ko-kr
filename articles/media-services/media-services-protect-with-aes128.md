---
title: "AES-128 동적 암호화 및 키 전달 서비스 사용 | Microsoft 문서"
description: "Microsoft Azure 미디어 서비스를 사용하면 AES 128비트 암호화 키로 암호화된 콘텐츠를 배달할 수 있습니다. 미디어 서비스는 권한 있는 사용자에게 암호화 키를 제공하는 키 배달 서비스도 제공합니다. 이 항목에서는 AES-128로 동적으로 암호화하는 방법과 키 배달 서비스를 사용하는 방법을 보여 줍니다."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: a441e76fae0bda829cb112d307b3b436809b9c9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>AES-128 동적 암호화 및 키 전달 서비스 사용
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>개요
> [!NOTE]
> AES 암호화를 사용하여 미디어 콘텐츠를 보호하는 방법에 대한 개요는 [이 비디오](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)를 참조하세요.
> 
> 

Microsoft Azure 미디어 서비스를 사용하면 128 비트 암호화 키를 사용하는 AES(Advanced Encryption Standard)로 암호화된 Http-Live-Streaming(HLS) 및 부드러운 스트림을 제공할 수 있습니다. 미디어 서비스는 권한 있는 사용자에게 암호화 키를 제공하는 키 배달 서비스도 제공합니다. 미디어 서비스에서 자산을 암호화하려는 경우 암호화 키를 자산에 연결하고 해당 키에 대해 권한 부여 정책도 구성해야 합니다. 플레이어가 스트림을 요청하면 미디어 서비스는 지정된 키를 사용하고 AES 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

미디어 서비스는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 권한 부여 정책에는 열기 또는 토큰 제한과 같은 하나 이상의 권한 부여 제한이 있을 수 있습니다. 토큰 제한 정책은 보안 토큰 서비스(STS)에 의해 발급된 토큰이 수반되어야 합니다. Media Services 지원 토큰에는 [단순 웹 토큰](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)(SWT) 형식 및 [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)(JWT) 형식의 토큰을 지원합니다. 자세한 내용은 [콘텐츠 키의 권한 부여 정책 구성](media-services-protect-with-aes128.md#configure_key_auth_policy)을 참조하세요.

동적 암호화를 이용하려면 다중 비트 전송률 MP4 파일 또는 다중 비트 전송률 부드러운 스트리밍 원본 파일의 집합이 포함된 자산을 만들어야 합니다. 또한 자산의 배달 정책을 구성해야 합니다(이 항목의 뒷부분에서 설명). 이렇게 하면 스트리밍 URL에 지정된 형식에 따라 주문형 스트리밍 서버는 사용자가 선택한 프로토콜로 스트림이 배달되도록 합니다. 따라서 사용자는 단일 저장소 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, 미디어 서비스에서 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다.

이 항목은 보호된 미디어를 배달하는 응용 프로그램에 대한 작업을 수행하는 개발자에게 유용합니다. 이 항목에서는 권한 부여 정책으로 키 배달 서비스를 구성하여 권한이 있는 클라이언트만 암호화 키를 받을 수 있도록 하는 방법을 보여 줍니다. 또한 동적 암호화를 사용하는 방법도 보여 줍니다.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 동적 암호화 및 키 배달 서비스 워크플로

다음은 AES로 자산을 암호화하고 미디어 서비스 키 배달 서비스를 사용하며 동적 암호화를 사용할 때 수행해야 하는 일반적인 단계입니다.

1. [자산을 만들고 파일을 자산에 업로드합니다](media-services-protect-with-aes128.md#create_asset).
2. [파일이 들어 있는 자산을 적응 비트 전송률 MP4 집합으로 인코딩합니다](media-services-protect-with-aes128.md#encode_asset).
3. [콘텐츠 키를 만들어 인코딩된 자산에 연결합니다](media-services-protect-with-aes128.md#create_contentkey). 미디어 서비스에서 콘텐츠 키에는 자산의 암호화 키가 들어 있습니다.
4. [콘텐츠 키의 권한 부여 정책을 구성합니다](media-services-protect-with-aes128.md#configure_key_auth_policy). 콘텐츠 키 권한 부여 정책은 사용자가 구성해야 하며 콘텐츠 키를 클라이언트에 배달하기 위해서는 해당 클라이언트를 충족시켜야 합니다.
5. [자산에 대한 배달 정책을 구성합니다](media-services-protect-with-aes128.md#configure_asset_delivery_policy). 배달 정책 구성에는 키 획득 URL 및 IV(Initialization Vector)(AES 128에는 암호화 및 해독 시 동일한 IV를 제공해야 함), 배달 프로토콜(예: MPEG DASH, HLS, 부드러운 스트리밍 또는 모두), 동적 암호화 유형(예: 봉투(envelope) 또는 동적이지 않은 암호화)이 포함됩니다.

    동일한 자산의 각 프로토콜에 다른 정책을 적용할 수 있습니다. 예를 들어, Smooth/DASH에 PlayReady 암호화를, HLS에 AES 봉투(envelope)를 적용할 수 있습니다. 배달 정책에 정의되지 않은 모든 프로토콜(예: HLS만 프로토콜로 지정하는 단일 정책)은 스트리밍에서 차단됩니다. 정의한 자산 배달 정책이 없는 경우는 예외입니다. 이렇게 하면 모든 프로토콜이 허용됩니다.

6. [주문형 로케이터를 만듭니다](media-services-protect-with-aes128.md#create_locator) .

또한 이 항목에서는 [클라이언트 응용 프로그램이 키 배달 서비스로부터 키를 요청하는 방법](media-services-protect-with-aes128.md#client_request)도 보여 줍니다.

이 항목의 끝부분에서 전체 .NET [예제](media-services-protect-with-aes128.md#example) 가 나와 있습니다.

다음 이미지는 위에서 설명한 워크플로를 보여 줍니다. 여기서는 인증에 토큰을 사용합니다.

![AES-128로 보호](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

이 항목의 나머지 부분에서는 자세한 설명, 코드 예제 및 위에서 설명한 작업을 수행하는 방법을 보여주는 항목에 대한 링크를 제공합니다.

## <a name="current-limitations"></a>현재 제한 사항
자산 배달 정책을 추가하거나 업데이트하는 경우 기존 로케이터(있는 경우)를 삭제하고 새 로케이터를 만들어야 합니다.

## <a id="create_asset"></a>자산 만들기 및 파일을 자산에 업로드
관리, 인코딩 및 비디오 스트림을 수행하려면 먼저 콘텐츠를 Microsoft Azure 미디어 서비스에 업로드해야 합니다. 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다. 

자세한 내용은 [미디어 서비스 계정에 파일 업로드](media-services-dotnet-upload-files.md)를 참조하세요.

## <a id="encode_asset"></a>파일이 들어 있는 자산을 적응 비트 전송률 MP4 집합으로 인코딩
동적 암호화를 사용하는 경우 다중 비트 전송률 MP4 파일 또는 다중 비트 전송률 부드러운 스트리밍 원본 파일의 집합이 포함된 자산을 만들기만 하면 됩니다. 이렇게 하면 매니페스트 또는 조각 요청의 지정된 형식에 따라 주문형 스트리밍 서버는 사용자가 선택한 프로토콜로 스트림을 받을 수 있도록 합니다. 따라서 사용자는 단일 저장소 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, 미디어 서비스에서 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다. 자세한 내용은 [동적 패키징 개요](media-services-dynamic-packaging-overview.md) 항목을 참조하세요.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 끝점은 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 끝점은 **실행** 상태에 있어야 합니다. 
>
>또한 동적 패키징 및 동적 암호화를 사용하려면 자산이 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함해야 합니다.

인코딩하는 방법에 관한 지침은 [미디어 인코더 표준을 사용하여 자산을 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md)을 참조하세요.

## <a id="create_contentkey"></a>콘텐츠 키를 만들어 인코딩된 자산에 연결
미디어 서비스에서 콘텐츠 키에는 자산을 암호화할 키가 들어 있습니다.

자세한 내용은 [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md)를 참조하세요.

## <a id="configure_key_auth_policy"></a>콘텐츠 키의 인증 정책을 구성합니다.
미디어 서비스는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 권한 부여 정책은 사용자가 구성해야 하며 이 키를 클라이언트에 배달하기 위해서는 해당 클라이언트(플레이어)를 충족시켜야 합니다. 콘텐츠 키 권한 부여 정책에는 열기, 토큰 제한 또는 IP 제한과 같은 하나 이상의 권한 부여 제한이 있을 수 있습니다.

자세한 내용은 [콘텐츠 키 권한 부여 정책 구성](media-services-dotnet-configure-content-key-auth-policy.md)을 참조하세요.

## <a id="configure_asset_delivery_policy"></a>자산 배달 정책 구성
자산에 대한 배달 정책을 구성합니다. 자산 배달 정책 구성에는 다음이 포함됩니다.

* 키 획득 URL. 
* 봉투(envelope) 암호화에 사용할 IV(Initialization Vector). AES 128에는 암호화 및 해독 시 동일한 IV를 제공해야 합니다. 
* 자산 배달 프로토콜(예: MPEG DASH, HLS, 부드러운 스트리밍 또는 모두).
* 동적 암호화 유형(예: AES 봉투) 또는 동적이지 않은 암호화. 

자세한 내용은 [자산 배달 정책 구성 ](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.

## <a id="create_locator"></a>스트리밍 URL을 얻기 위해 주문형 스트리밍 로케이터 만들기
사용자에게 Smooth, DASH 또는 HLS에 대한 스트리밍 URL을 제공해야 합니다.

> [!NOTE]
> 자산 배달 정책을 추가하거나 업데이트하는 경우 기존 로케이터(있는 경우)를 삭제하고 새 로케이터를 만들어야 합니다.
> 
> 

자산을 게시하고 스트리밍 URL을 작성하는 방법은 [스트리밍 URL 작성](media-services-deliver-streaming-content.md)을 참조하세요.

## <a name="get-a-test-token"></a>테스트 토큰 가져오기
키 권한 부여 정책에 사용된 토큰 제한에 따라 테스트 토큰을 가져옵니다.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

[AMS 플레이어](http://amsplayer.azurewebsites.net/azuremediaplayer.html) 를 사용하여 스트림을 테스트할 수 있습니다.

## <a id="client_request"></a>클라이언트가 키 배달 서비스로부터 키를 요청하는 방법
이전 단계에서는 매니페스트 파일을 가리키는 URL을 생성했습니다. 클라이언트는 키 배달 서비스에 요청을 수행하기 위해 스트리밍 매니페스트 파일에서 필요한 정보를 추출해야 합니다.

### <a name="manifest-files"></a>매니페스트 파일
클라이언트는 매니페스트 파일에서 URL(콘텐츠 키 Id도 포함(kid)) 값을 추출해야 합니다. 그런 다음 키 배달 서비스로부터 암호화 키 가져오기를 시도합니다. 또한 IV 값을 추출하고 이 값을 사용하여 스트림을 해독합니다. 다음 코드 조각은 부드러운 스트리밍 매니페스트의 <Protection> 요소를 보여줍니다.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

HLS의 경우 루트 매니페스트는 세그먼트 파일로 나뉩니다. 

예를 들어 루트 매니페이스는 http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl)이고 세그먼트 파일 이름 목록을 포함합니다.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

세그먼트 파일 중 하나를 텍스트 편집기에서 열면(예: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl)), #EXT-X-KEY가 포함되어 있어야 하며 이는 파일이 암호화되었음을 나타냅니다.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Safari에서 AES 암호화 HLS를 재생하려는 경우 [이 블로그](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)를 참조하세요.

### <a name="request-the-key-from-the-key-delivery-service"></a>키 배달 서비스로부터 키 요청

다음 코드에서는 키 배달 Uri(매니페스트에서 추출됨) 및 토큰을 사용하여 미디어 서비스 키 배달 서비스로 요청을 보내는 방법을 보여 줍니다(이 항목에서는 보안 토큰 서비스에서 간단한 웹 토큰을 가져오는 방법에 대해서는 다루지 않음).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-using-net"></a>.NET을 사용하여 AES-128로 콘텐츠 보호

### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

1. 개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다. 
2. 다음 요소를 app.config 파일에 정의된 **appSettings**에 추가합니다.

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>예제

Program.cs 파일에 있는 코드를 이 섹션에 나와 있는 코드로 덮어씁니다.
 
>[!NOTE]
>다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 배치되는 로케이터에 대한 정책) 동일한 정책 ID를 사용해야 합니다. 자세한 내용은 [이 항목](media-services-dotnet-manage-entities.md#limit-access-policies) 을 참조하세요.

입력 파일이 있는 폴더를 가리키도록 변수를 업데이트해야 합니다.

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

