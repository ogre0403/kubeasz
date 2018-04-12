# 配置 GPU 節點

推薦閱讀[官方GPU節點配置文檔](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)

## 安裝GPU節點驅動

至[官方驅動網頁](http://www.nvidia.com/Download/index.aspx?lang=en-uk)下載對應操作系統與顯卡的驅動後，安裝完成。

## 安装

執行`90.setup.yml`搭建k8s即可實現此目標。或是一步一步
執行命令 `ansible-playbook 31.gpunode.yml`

## 原理 

執行 `31.gpunode.yml` 相當於完成以下任務

### 1. 啟用 Device Plugins

實現方式：通過修改 kubelet 配置文件模板，加入`--feature-gates="DevicePlugins=true"` 參數

### 2. GPU 節點安裝 nvidia docker 2.0
這一部分可能失敗，原因可能是系統所用docker版本與nvidia docker 2.0依賴的docker版本不一致，
具體參考[文檔](https://github.com/NVIDIA/nvidia-docker/wiki/Frequently-Asked-Questions)

    
可以通過命令`apt-cache madison nvidia-docker2 nvidia-container-runtime`查詢可以安裝的nvidia docker 2.0 
的 docker 版本
    
可以通過命令`dpkg -l | grep docker`來查看實際安裝docker 版本
    
nvidia docker 安裝失敗，可以[手動更新docker](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#upgrade-docker-ce),
再重新運行安裝playbook


### 3. GPU 節點配置 nvidia-container-runtime 為 docker default runtime

通過修改`/etc/docker/daemon.json`實現

### 4. 配置Nvidia device plugin
執行命令 `kubectl create -f manifests/gpu-device-plugin/v1.9/nvidia-device-plugin.yml`

可以通過執行`kubectl describe nodes | grep nvidia.com/gpu` 來查看GPU節點配置是否成功