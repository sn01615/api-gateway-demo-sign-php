# api-gateway-demo-sign-php
aliyun api gateway request signature demo by php

智能认证U-Verify SDK

使用方法：
```php

use Aliyun\ApiGatewaySign\Constant\ContentType;
use Aliyun\ApiGatewaySign\Constant\HttpHeader;
use Aliyun\ApiGatewaySign\Constant\HttpMethod;
use Aliyun\ApiGatewaySign\Constant\SystemHeader;
use Aliyun\ApiGatewaySign\Http\HttpClient;


 //域名后、query前的部分
        $path = "/api/v1/mobile/info";
        $request = new \Aliyun\ApiGatewaySign\Http\HttpRequest(
            'https://verify5.market.alicloudapi.com',
            $path, HttpMethod::POST,
            '11111111', # TODO 修改为阿里云参数
            'xxxxxxxxxxxxxxxxx' # TODO 修改为阿里云参数
            );
        //传入内容是json格式的字符串
        $bodyContent = '{
	"token":"1234"
}';

        //设定Content-Type，根据服务器端接受的值来设置
        $request->setHeader(HttpHeader::HTTP_HEADER_CONTENT_TYPE, ContentType::CONTENT_TYPE_JSON);

        //设定Accept，根据服务器端接受的值来设置
        $request->setHeader(HttpHeader::HTTP_HEADER_ACCEPT, ContentType::CONTENT_TYPE_JSON);
        //如果是调用测试环境请设置
        //$request->setHeader(SystemHeader::X_CA_STAG, "TEST");


        //注意：业务header部分，如果没有则无此行(如果有中文，请做Utf8ToIso88591处理)
        //mb_convert_encoding("headervalue2中文", "ISO-8859-1", "UTF-8");
//		$request->setHeader("b-header2", "headervalue2");
//		$request->setHeader("a-header1", "headervalue1");

        //注意：业务query部分，如果没有则无此行；请不要、不要、不要做UrlEncode处理
        $request->setQuery("appkey", "xxxxxxxxx"); # TODO 修改为友盟参数
//		$request->setQuery("a-query1", "queryvalue1");

        //注意：业务body部分，不能设置key值，只能有value
        if (0 < strlen($bodyContent)) {
            $request->setHeader(HttpHeader::HTTP_HEADER_CONTENT_MD5, base64_encode(md5($bodyContent, true)));
            $request->setBodyString($bodyContent);
        }

        //指定参与签名的header
        $request->setSignHeader(SystemHeader::X_CA_TIMESTAMP);
//		$request->setSignHeader("a-header1");
//		$request->setSignHeader("b-header2");

        $response = HttpClient::execute($request);
        print_r($response->getBody());
```