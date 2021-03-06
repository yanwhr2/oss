# Set access logging {#concept_t3h_4hd_5db .concept}

When you access OSS, a large number of access logs are generated. After the access logging function is enabled for a bucket, OSS automatically accesses the access logs stored in the bucket on an hourly basis, and generates an object in a specified bucket \(target bucket\). The generated object complies with OSS naming conventions. You can analyze the access logs by using Alibaba Cloud Data Lake Analytics or through establishing a Spark cluster. You can also configure lifecycle rules for the target bucket to convert the storage class of the log object to Archive for archiving.

## Naming convention for objects that store access logs {#section_pd1_b3d_5db .section}

```
<TargetPrefix><SourceBucket>YYYY-mm-DD-HH-MM-SS-UniqueString
```

Fields included in the naming convention are described as follows:

-   `TargetPrefix` indicates the name prefix of the object that stores access logs. This field is user-defined and can be left blank.
-   `YYYY-mm-DD-HH-MM-SS` indicates the year, month, day, hour, minute, and second when the object is created \(note the number of digits\).
-   `UniqueString` is a string \(UUID\) generated by OSS, which is used to uniquely identify the log object.

An example name of an object that stores OSS access logs is as follows:

```
MyLog-oss-example2017-09-10-04-00-00-0000
```

In the preceding example:

-   `MyLog-` is the object prefix specified by the user.
-   `oss-example` is the name of the source bucket.
-   `2017-09-10-04-00-00` is the time when the object is created.
-   `0000` is a string generated by OSS.

## Log file format {#section_xp1_t3d_5db .section}

The following table describes the fields that compose a log file. In a log file, these fields are combined in order from left to right and are separated by spaces.

|Name|Example|Description|
|:---|:------|:----------|
|Remote IP|119.140.142.11|Indicates the IP address from which the request is initiated \(note that a proxy or firewall may block this field\).|
|Reserved|-|Indicates that this is a reserved field.|
|Reserved|-|Indicates that this is a reserved field.|
|Time|\[02/May/2012:00:00:04 +0800\]|Indicates the time when OSS receives a request.|
|Request-URI|“GET /aliyun-logo.png HTTP/1.1”|Indicates the URI of a user request \(including query-string\).|
|HTTP Status|200|Indicates the HTTP status code returned by OSS.|
|SentBytes|5576|Indicates the amount of traffic downloaded by the user from OSS.|
|RequestTime \(ms\)|71|Indicates the amount of time used to complete the request \(in ms\).|
|Referer|`http://www.aliyun.com/product/oss`|Indicates the requested HTTP referer.|
|User-Agent|curl/7.15.5|Indicates the HTTP User-Agent header.|
|HostName|oss-example.oss-cn-hangzhou.aliyuncs.com|Indicates the domain name that the request accesses.|
|Request ID|505B01695037C2AF032593A4|Indicates the UUID used to uniquely identify the request.|
|LoggingFlag|true|Indicates whether the access logging function is enabled|
|Requester Aliyun ID|1657136103983691|Indicates the Alibaba Cloud ID of the requester, which is “-“ for anonymous access.|
|Operation|GetObject|Indicates the reqest type.|
|Bucket|oss-example|Indicates the name of the request bucket.|
|Key|/aliyun-logo.png|Indicated the key that the user requests.|
|ObjectSize|5576|Indicates the object size.|
|Server Cost Time \(ms\)|17|Indicates the amount of time that the OSS server used to process the request \(in ms\).|
|Error Code|NoSuchBucket|Indicates the error code returned by OSS.|
|Request Length|302|Indicates the length of the user request \(in bytes\).|
|UserID|1657136103983691|Indicates the ID of the bucket owner.|
|Delta DataSize|280|Indicates the bucket size variation, which is `-` if the bucket size has not changed.|
|Sync Request|-|Indicates whether the request is a CDN back-to-origin request, which is `-` if the request is not a back-to-origin request.|
|Reserved|-|Indicates that this is a reserved field.|

## Detail analysis {#section_igf_x3d_5db .section}

-   The source bucket and target bucket can be the same bucket, or different buckets that are owned by the same account and belong to the same region. You can also store the logs of multiple source buckets in the same target bucket. In this case, we recommend that you specify different TargetPrefix values for logs of different source buckets.
-   OSS generates an object that stores bucket aceess logs on an hourly basis. However, requests in the last hour may be recorded in the object generated for the previous hour or the next hour.
-   Each time OSS generates an object that stores bucket access logs, a PUT operation and the storage space that the operation occupies are recorded. However, traffic generated by the PUT operation is not recorded. You can perform common OSS operations on a generated object that stores access logs.
-   OSS ignores all query-string parameters prefixed by `x-`. However, these parameters are recorded in the access logs. To easily identify a specific request from a large amount of access logs, add a query-string parameter prefixed by `x-` to the URL of the request. For example:

    `http://oss-example.oss-cn-hangzhou.aliyuncs.com/aliyun-logo.png`

    `http://oss-example.oss-cn-hangzhou.aliyuncs.com/aliyun-logo.png?x-user=admin`

    OSS returns the same result for the preceeding two requests. However, you can easily locate the request with `x-user=admin` by searching this parameter.

-   A `-` may appear in any field in OSS logs. It indicates that data is unknown or the field is invalid for the current request.
-   More fields will be added to the end of OSS logs in the future. We recommend that developers take note of potential compatibility issues when developing log processing tools.

## Reference {#section_oh4_y3d_5db .section}

-   For more information about set logging in the OSS console, see [Set logging](../../../../../reseller.en-US/Console User Guide/Manage logs/Set logging.md#).
-   For more information about related APIs, see [PutBucketLogging](../../../../../reseller.en-US/API Reference/Bucket operations/PutBucketLogging.md#), [DeleteBucketLogging](../../../../../reseller.en-US/API Reference/Bucket operations/DeleteBucketLogging.md#), and [GetBucketLogging](../../../../../reseller.en-US/API Reference/Bucket operations/GetBucketLogging.md#).
-   For more information about related Java SDKs, see [Set logging](https://partners-intl.aliyun.com/help/doc-detail/31868.htm).

