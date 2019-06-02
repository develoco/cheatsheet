aws, amazon, amazonwebservices

Install on ubuntu

    apt-get install awscli

S3 sync and cloudfront cache invalidation

    aws s3 sync build/ s3://${AWS_S3_BUCKET}
    aws cloudfront create-invalidation --distribution-id ${AWS_CLOUDFRONT_ID} --paths /\*


AWS CLI, you can enable this service by adding the following to your CLI
config file:

    [preview]
    cloudfront=true

or by running:

    aws configure set preview.cloudfront true
