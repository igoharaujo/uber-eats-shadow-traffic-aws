# Shadow Traffic

### get container
```bash
docker pull shadowtraffic/shadowtraffic:latest
```

### uber eats: AWS S3
The container needs AWS credentials with access to `s3://owshq-shadow-traffic`. Grant `s3:ListBucket` on the bucket and `s3:GetObject`/`s3:PutObject` on its objects. Provide credentials through exported AWS environment variables or an attached IAM role; do not add access keys to the generator JSON.

```shell
docker run \
  --env-file st-key.env \
  -e AWS_REGION=us-east-1 \
  -e AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY \
  -e AWS_SESSION_TOKEN \
  -v $(pwd)/gen/aws/uber-eats.json:/home/config.json \
  shadowtraffic/shadowtraffic:latest \
  --config /home/config.json
```

### uber eats: AWS S3 [cdc]
```shell
docker run \
  --env-file st-key.env \
  -e AWS_REGION=us-east-1 \
  -e AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY \
  -e AWS_SESSION_TOKEN \
  -v $(pwd)/gen/aws/uber-eats-cdc.json:/home/config.json \
  shadowtraffic/shadowtraffic:latest \
  --config /home/config.json
```

## postgres [driver]
```shell
docker run \
  --env-file st-key.env \
  -v $(pwd)/gen/postgres/drivers.json:/home/config.json \
  shadowtraffic/shadowtraffic:latest \
  --config /home/config.json
```

### uber eats: kafka
```shell
docker run \
  --env-file st-key.env \
  -v $(pwd)/gen/kafka/uber-eats.json:/home/config.json \
  shadowtraffic/shadowtraffic:latest \
  --config /home/config.json
```
