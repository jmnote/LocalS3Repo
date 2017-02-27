## LocalS3Repo - MediaWiki Extension for storing File Uploads/Images on S3

LocalS3Repo modified to work with MediaWiki 1.27.1, CloudFront CDN, and rotating IAM keys.
Modified further to support both IAM keys and static auth (was hardcoded to IAM keys only in some places)

Based on
* https://github.com/oborseth/LocalS3Repo2
* https://www.mediawiki.org/wiki/Extension:LocalS3Repo


## Settings

Modify the below as required and put them in your LocalSettings.php:

$wgUploadDirectory is the directory in your bucket where the image directories and images will be stored.

If "images" doesn't work for you, change it.

```php
$wgUploadDirectory = 'images';
$wgUploadS3Bucket = 'YOUR S3 BUCKET';
$wgUploadS3SSL = false; // true if SSL should be used
$wgPublicS3 = true; // true if public, false if authentication should be used

$wgS3BaseUrl = "http".($wgUploadS3SSL?"s":"")."://s3.amazonaws.com/$wgUploadS3Bucket";
$wgUploadBaseUrl = "$wgS3BaseUrl/$wgUploadDirectory"
$wgCloudFrontUrl = "http".($wgUploadS3SSL?"s":"").'://YOUR_CLOUDFRONT_SUBDOMAIN.cloudfront.net/';

$wgLocalFileRepo = array(
        'class' => 'LocalS3Repo',
        'name' => 's3',
        'directory' => $wgUploadDirectory,
        'url' => $wgUploadBaseUrl ? $wgUploadBaseUrl . $wgUploadPath : $wgUploadPath,
        'urlbase' => $wgS3BaseUrl ? $wgS3BaseUrl : "",
        'hashLevels' => $wgHashedUploadDirectory ? 2 : 0,
        'thumbScriptUrl' => $wgThumbnailScriptPath,
        'transformVia404' => !$wgGenerateThumbnailOnParse,
        'initialCapital' => $wgCapitalLinks,
        'deletedDir' => $wgUploadDirectory.'/deleted',
        'deletedHashLevels' => $wgFileStore['deleted']['hash'],
        'AWS_ACCESS_KEY' => 'YOUR_AWS_ACCESS_KEY',
        'AWS_SECRET_KEY' => 'YOUR_AWS_SECRET_KEY',
        'AWS_S3_BUCKET' => $wgUploadS3Bucket,
        'AWS_S3_PUBLIC' => $wgPublicS3,
        'AWS_S3_SSL' => $wgUploadS3SSL,
        'cloudFrontUrl' => $wgCloudFrontUrl,
);
require_once("$IP/extensions/LocalS3Repo/LocalS3Repo.php");
```
