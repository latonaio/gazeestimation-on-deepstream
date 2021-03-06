# gazeestimation-on-deepstream
gazeestimation-on-deepstream は、DeepStream 上で GazeEstimation の AIモデル を動作させるマイクロサービスです。  

## 動作環境
- NVIDIA 
    - DeepStream
- GazeEstimation
- Docker
- TensorRT Runtime

## GazeEstimationについて
GazeEstimation は、画像内の顔、顔の主なランドマークを検出し、視線位置と視線ベクトル推測するAIモデルです。  

## 動作手順
### Dockerコンテナの起動
Makefile に記載された以下のコマンドにより、GazeEstimation の Dockerコンテナ を起動します。
```
docker-run: ## docker container の立ち上げ
	docker-compose up -d
```
### ストリーミングの開始
Makefile に記載された以下のコマンドにより、DeepStream 上の GazeEstimation でストリーミングを開始します。  
```
stream-start: ## ストリーミングを開始する
	xhost +
	docker exec -it gazeestimation-camera cp /app/mnt/deepstream-gaze-app /app/src/deepstream_tao_apps/apps/tao_others/deepstream-gaze-app/
	docker exec -it gazeestimation-camera cp /app/mnt/facedetect.engine /app/src/deepstream_tao_apps/models/faciallandmark/facenet.etlt_b1_gpu0_fp16.engine
	docker exec -it gazeestimation-camera cp /app/mnt/faciallandmarks.engine /app/src/deepstream_tao_apps/models/faciallandmark/faciallandmarks.etlt_b32_gpu0_fp16.engine
	docker exec -it gazeestimation-camera cp /app/mnt/gazeestimation.engine /app/src/deepstream_tao_apps/models/gazenet/gazenet_facegrid.etlt_b8_gpu0_fp16.engine
	docker exec -it gazeestimation-camera cp /app/mnt/config_infer_primary_facenet.txt /app/src/deepstream_tao_apps/configs/facial_tao/config_infer_primary_facenet.txt
	docker exec -it gazeestimation-camera cp /app/mnt/faciallandmark_sgie_config.txt /app/src/deepstream_tao_apps/configs/facial_tao/faciallandmark_sgie_config.txt
	docker exec -it gazeestimation-camera cp /app/mnt/libnvds_gazeinfer.so /app/src/deepstream_tao_apps/apps/tao_others/deepstream-gaze-app/gazeinfer_impl/
	docker exec -it -w /app/src/deepstream_tao_apps/apps/tao_others/deepstream-gaze-app gazeestimation-camera \
	       	./deepstream-gaze-app 3 ../../../configs/facial_tao/sample_faciallandmarks_config.txt /dev/video0 gazenet
```
## 相互依存関係にあるマイクロサービス  
本マイクロサービスを実行するために GazeEstimation の AIモデルを最適化する手順は、[gazeestimation-on-tao-toolkit](https://github.com/latonaio/gazeestimation-on-tao-toolkit)を参照してください。  


## engineファイルについて
engineファイルである gazeestimation.engine は、[gazeestimation-on-tao-toolkit](https://github.com/latonaio/gazeestimation-on-tao-toolkit)と共通のファイルであり、当該レポジトリで作成した engineファイルを、本リポジトリで使用しています。  
