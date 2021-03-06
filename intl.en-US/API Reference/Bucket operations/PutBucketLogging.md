# PutBucketLogging {#reference_t1g_zj5_tdb .reference}

Enables the access logging function for a bucket. When this function is enabled, OSS automatically records the details about the requests to this bucket, and follows the user-specified rules to write the access logs as an object into a user-specified bucket on an hourly basis.

**Note:** OSS provides bucket access logs for bucket owners to understand and analyze bucket access behaviors easily. The bucket access logs provided by OSS do not guarantee that every single access record is logged.

-   When the source bucket is deleted, the corresponding logging rules are also deleted.
-   OSS generates a bucket access log file every hour. However, all requests during the hour may not be recorded in the log file, but may get recorded in the previous or next log file.
-   Each time OSS generates a bucket access log file, this is considered a PUT operation and the occupied space is recorded, but the generated traffic is not recorded.  After log files are generated, you can operate these log files as common objects.
-   OSS ignores all query-string parameters prefixed by “x-“ but such query-string parameters are recorded in access logs.  If you want to mark a special request from massive access logs, you can add a query-string parameter prefixed by “x-“ to the URL.  For example:

    `http://oss-example.regionid.example.com/aliyun-logo.png`

    `http://oss-example.regionid.example.com/aliyun-logo.png?x-user=admin`


## Request syntax {#section_rfs_rnr_bz .section}

```
PUT /? logging HTTP/1.1
Date: GMT Date
Content-Length：ContentLength
Content-Type: application/xml
Authorization: SignatureValue 
Host: BucketName.oss-cn-hangzhou.aliyuncs.com
<? xml version="1.0" encoding="UTF-8"? >
<BucketLoggingStatus>
    <LoggingEnabled>
        <TargetBucket>TargetBucket</TargetBucket>
        <TargetPrefix>TargetPrefix</TargetPrefix>
    </LoggingEnabled>
</BucketLoggingStatus>
```

## Request elements {#section_dwh_vnr_bz .section}

**Note:** All PutBucketLogging requests must signed because the anonymous access is not supported.

|Element|Type|Required|Description|
|-------|----|--------|-----------|
|BucketLoggingStatus|Container|Yes|Specifies the container for storing access log status informationSub-node: LoggingEnabled

Parent node: None

|
|LoggingEnabled|Container|No|Specifies the container for storing access log information. This element is required only when server access logging is enabled. Sub-node: TargetBucket, TargetPrefix

Parent node: BucketLoggingStatus

|
|TargetBucket|String|This element is required when server access logging is enabled|Specifies the bucket for storing access logs. The source bucket and target bucket can be the same or different buckets. You can save logs from multiple source buckets to the same target bucket \(in this case, we recommend that you assign different values to TargetPrefix\).Sub-node: None

Parent node: BucketLoggingStatus.LoggingEnabled

|
|TargetPrefix|String|No|Specifies the prefix of the names of saved access log files, which can be null.  Sub-node: None

Parent node: BucketLoggingStatus.LoggingEnabled

|

## Naming rules for the objects storing access logs {#section_kv3_ynr_bz .section}

The format of an object name is as follows:

```
<TargetPrefix><SourceBucket>-YYYY-mm-DD-HH-MM-SS-UniqueString
```

The following table describes the parameters in an object name:

|Parameter|Description|
|:--------|:----------|
|TargetPrefix|Specifies the prefix of the object name.|
|YYYY-mm-DD-HH-MM-SS|Indicates the time when the object is created. YYYY, mm, DD, HH, MM, and SS indicate the year, month, day, hour, minutes, and seconds individually. For example: `2012-09-10-04-00-00`。|
|UniqueString|Indicates the unique UUID generated by OSS to identify a log.|

An example object name is as follows:

```
MyLog-oss-example-2012-09-10-04-00-00-0000
```

In the preceding example, MyLog- is the prefix specified by the user, oss-example is the name of the source bucket, 2012-09-10-04-00-00 is the time when the object is created, and 0000 is the UUID string generated by OSS.

## Log file format {#section_ugs_b4r_bz .section}

**Note:** 

-   You may see “-“ in any field of OSS logs. It indicates that data is unknown or the field is invalid for the current request.
-   Certain fields are added to the end of OSS log files in future based on the requirements. We recommend that developers consider compatibility issues when developing log processing tools.

|Field|Example|Description|
|-----|-------|-----------|
|Remote IP|119.140.142.11|IP address from which the request is initiated \(the proxy or user firewall may block this field\)|
|Reserved|-|Reserved field|
|Reserved|-|Reserved field|
|Time|\[02/May/2012:00:00:04 +0800\]|Time when OSS receives the request|
|Request-URL|“GET /aliyun-logo.png HTTP/1.1“|User-Requested URL \(including query-string\)|
|HTTP Status|200|HTTP status code returned by OSS|
|SentBytes|5576|Traffic that the user downloads from OSS|
|RequestTime \(ms\)|71|Time utilized in completing this request \(in ms\)|
|Referer| ```
http://www.aliyun.com/product/oss
```

 |HTTP Referer in the request|
|User-Agent|curl/7.15.5|HTTP User-Agent header|
|HostName|oss-example.regionid.example.com|Domain name for access request|
|Request ID|505B01695037C2AF032593A4|UUID used to uniquely identify this request|
|LoggingFlag|true|Whether the access logging function is enabled|
|Requester Aliyun ID|1657136103983691|Alibaba Cloud ID of the requester, “-“ for an anonymous access|
|Operation|GetObject|Request type|
|Bucket|oss-example|Name of the bucket requested for access|
|Key|/aliyun-logo.png|Key of user request|
|ObjectSize|5576|Object size|
|Server Cost Time \(ms\)|17|Time utilized by OSS server to process this request \(in ms\)|
|Error Code|NoSuchBucket|Error code returned by OSS|
|Request Length|302|Length of user request \(byte\)|
|UserID|1657136103983691| ID of the bucket owner|
|Delta DataSize|280|Bucket size variation, “-“ for no change|
|Sync Request|-|Whether this is an origin retrieval request from CDN, “-“ for no|
|Reserved|-|Reserved field|

## Examples {#section_byq_m4r_bz .section}

**Example of a request for enabling bucket access logging:**

```
PUT /? logging HTTP/1.1
Host: oss-example.oss-cn-hangzhou.aliyuncs.com
Content-Length: 186
Date: Fri, 04 May 2012 03:21:12 GMT
Authorization: OSS qn6qrrqxo2oawuk53otfjbyc:KU5h8YMUC78M30dXqf3JxrTZHiA=
<? xml version="1.0" encoding="UTF-8"? >
<BucketLoggingStatus>
<LoggingEnabled>
<TargetBucket>doc-log</TargetBucket>
<TargetPrefix>MyLog-</TargetPrefix>
</LoggingEnabled>
</BucketLoggingStatus>
```

**Response example:**

```
HTTP/1.1 200 OK
x-oss-request-id: 534B371674E88A4D8906008B
Date: Fri, 04 May 2012 03:21:12 GMT
Content-Length: 0
Connection: keep-alive
Server: AliyunOSS
```

**Example of a request for disabling bucket access logging:**

```
PUT /? logging HTTP/1.1
Host: oss-example.oss-cn-hangzhou.aliyuncs.com
Content-Type: application/xml
Content-Length: 86
Date: Fri, 04 May 2012 04:21:12 GMT
Authorization: OSS qn6qrrqxo2oawuk53otfjbyc:KU5h8YMUC78M30dXqf3JxrTZHiA=
<? xml version="1.0" encoding="UTF-8"? >
<BucketLoggingStatus>
</BucketLoggingStatus>
```

**Response example:**

```
HTTP/1.1 200 OK
x-oss-request-id: 534B371674E88A4D8906008B
Date: Fri, 04 May 2012 04:21:12 GMT
Content-Length: 0
Connection: keep-alive
Server: AliyunOSS
```

## SDK {#section_egl_m2c_5gb .section}

The SDKs of this API are as follows:

-   [Java](../../../../../intl.en-US/SDK Reference/Java/Set logging.md)
-   [Python](../../../../../intl.en-US/SDK Reference/Python/Set logging.md)
-   [PHP](../../../../../intl.en-US/SDK Reference/PHP/Set logging.md)
-   [Go](../../../../../intl.en-US/SDK Reference/Go/Set logging.md)
-   [C](../../../../../intl.en-US/SDK Reference/C/Set logging.md)
-   [.NET](../../../../../intl.en-US/SDK Reference/. NET/Set logging.md)
-   [Node.js](../../../../../intl.en-US/SDK Reference/Node. js/Set logging.md)
-   [Ruby](../../../../../intl.en-US/SDK Reference/Ruby/Set logging.md)

## Error codes {#section_dsv_grs_qgb .section}

|Error code|HTTP status code|Description|
|:---------|:---------------|:----------|
|NoSuchBucket|404|The source bucket does not exist. The source bucket and the target bucket must be owned by the same user.|
|InvalidTargetBucketForLogging|400|The source bucket and the target bucket are in different regions.|
|InvalidDigest|400|If you include the Content-MD5 header in the request, OSS calculates the Content-MD5 of the request body and checks if the two are the same. If the two values are different, this error is returned.|
|MalformedXML|400|The XML file in the request is invalid.|
|InvalidTargetBucketForLogging|403|The user who initiates the request is not the owner of the target bucket.|
|AccessDenied|403|The user who initiates the request is not the owner of the source bucket,|

