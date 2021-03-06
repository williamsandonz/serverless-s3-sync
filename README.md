# Serverless S3 Sync [![npm](https://img.shields.io/npm/v/serverless-s3-sync.svg)](https://www.npmjs.com/package/serverless-s3-sync)

> A plugin to sync local directories and S3 prefixes for Serverless Framework :zap: .

## Use Case

- Static Website ( `serverless-s3-sync` ) & Contact form backend ( `serverless` ) .
- SPA ( `serverless` ) & assets ( `serverless-s3-sync` ) .

## Install

Run `npm install` in your Serverless project.

```sh
$ npm install --save serverless-s3-sync
```

Add the plugin to your serverless.yml file

```yaml
plugins:
  - serverless-s3-sync
```

## Setup

```yaml
custom:
  s3Sync:
    # A simple configuration for copying static assets
    - bucketName: my-static-site-assets # required
      bucketPrefix: assets/ # optional
      localDir: dist/assets # required

    # An example of possible configuration options
    - bucketName: my-other-site
      localDir: path/to/other-site
      deleteRemoved: true # optional, indicates whether sync deletes files no longer present in localDir. Defaults to 'true'
      acl: public-read # optional
      followSymlinks: true # optional
      defaultContentType: text/html # optional
      params: # optional
        - index.html:
            CacheControl: 'no-cache'
        - "*.js":
            CacheControl: 'public, max-age=31536000'
      bucketTags: # optional, these are appended to existing S3 bucket tags (overwriting tags with the same key)
        tagKey1: tagValue1
        tagKey2: tagValue2

    # This references bucket name from the output of the current stack
    - bucketNameKey: AnotherBucketNameOutputKey
      localDir: path/to/another

    # ... but can also reference it from the output of another stack,
    # see https://www.serverless.com/framework/docs/providers/aws/guide/variables#reference-cloudformation-outputs
    - bucketName: ${cf:another-cf-stack-name.ExternalBucketOutputKey}
      localDir: path

resources:
  Resources:
    AssetsBucket:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: my-static-site-assets
    OtherSiteBucket:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: my-other-site
        AccessControl: PublicRead
        WebsiteConfiguration:
          IndexDocument: index.html
          ErrorDocument: error.html
    AnotherBucket:
      Type: AWS::S3::Bucket
  Outputs:
    AnotherBucketNameOutputKey:
      Value: !Ref AnotherBucket
```

## Usage

Run `sls deploy`, local directories and S3 prefixes are synced.

Run `sls remove`, S3 objects in S3 prefixes are removed.

Run `sls deploy --nos3sync`, deploy your serverless stack without syncing local directories and S3 prefixes.

### `sls s3sync`

Sync local directories and S3 prefixes.
