# Serving

## 1. convert to mar file via torch-model-archiver'

refer to [model-archiver](https://github.com/pytorch/serve/tree/master/model-archiver)

```bash
torch-model-archiver --model-name densenet161 --version 1.0 --model-file ../image_classifier/densenet_161/model.py --serialized-file densenet161-8d451a50.pth --export-path model_store --extra-files ../image_classifier/index_to_name.json --handler image_classifier
```

## 2, start torch-serve-archiver

refer to [torch-serve-archiver](https://github.com/pytorch/serve/blob/master/docs/getting_started.md)

```bash
torchserve --start --ncs --model-store model_store --models densenet161.mar
```

## 3, prepare client

```bash
pip install protobuf
pip install grpcio grpcio-tools
```

* Generate inference client using proto files

```bash
python -m grpc_tools.protoc --proto_path=frontend/server/src/main/resources/proto/ --python_out=ts_scripts --grpc_python_out=ts_scripts frontend/server/src/main/resources/proto/inference.proto frontend/server/src/main/resources/proto/management.proto
```

* Run inference using a sample client gRPC python client

```bash
python ts_scripts/torchserve_grpc_client.py infer densenet161 examples/image_classifier/kitten.jpg
```

## 4, using REST

prepare pictures and call api

```bash
curl -O https://raw.githubusercontent.com/pytorch/serve/master/docs/images/kitten_small.jpg
curl http://127.0.0.1:8080/predictions/densenet161 -T kitten_small.jpg
```