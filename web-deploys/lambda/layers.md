
   nodejs/
   └── node_modules/
       ├── package1/
       └── package2/

```sh
mkdir -p layer/nodejs/node_modules
cd layer/nodejs
npm install package1 package2
cd ..
zip -r my-layer.zip nodejs
```

```sh
aws lambda publish-layer-version --layer-name my-custom-layer --zip-file fileb://my-layer.zip --compatible-runtimes nodejs14.x nodejs16.x
```

