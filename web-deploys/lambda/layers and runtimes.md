https://www.serverless.com/blog/publish-aws-lambda-layers-serverless-framework/
https://github.com/mthenw/awesome-layers?tab=readme-ov-file
https://docs.aws.amazon.com/lambda/latest/dg/runtimes-custom.html
   nodejs/
   └── node_modules/
       ├── package1/
       └── package2/

```sh
mkdir -p layer/nodejs/node_modules
cd layer/nodejs
pnpm init
pnpm add install fastify fastify-aws-lambda
cd ..
zip -r fastify-layer.zip nodejs
```

```sh
aws lambda publish-layer-version --layer-name fastify-layer --zip-file fileb://fastify-layer.zip --compatible-runtimes nodejs20.x nodejs18.x nodejs16.x nodejs14.x
```

