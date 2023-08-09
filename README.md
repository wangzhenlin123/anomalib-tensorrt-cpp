# 说明

> 适用于anomalib导出的onnx格式的模型
>
> 测试了 patchcore,fastflow,efficient_ad模型

```yaml
# 模型配置文件中设置为onnx,导出openvino会导出onnx
optimization:
  export_mode: onnx # options: torch, onnx, openvino
```

> 使用 `trtexec` 转换模型

```shell
trtexec --onnx=model.onnx --saveEngine=model.engine
```

# 其他推理方式

> [anomalib-onnxruntime-cpp](https://github.com/NagatoYuki0943/anomalib-onnxruntime-cpp)
>
> [anomalib-openvino-cpp](https://github.com/NagatoYuki0943/anomalib-openvino-cpp)
>
> [anomalib-tensorrt-cpp](https://github.com/NagatoYuki0943/anomalib-tensorrt-cpp)

# example

```C++
#include "inference.hpp"
#include <opencv2/opencv.hpp>


int main() {
    // patchcore模型训练配置文件删除了center_crop
    // trtexec --onnx=model.onnx --saveEngine=model.engine 转换模型
    string model_path = "D:/ml/code/anomalib/results/efficient_ad/mvtec/bottle/run/weights/openvino/model.engine";
    string meta_path  = "D:/ml/code/anomalib/results/efficient_ad/mvtec/bottle/run/weights/openvino/metadata.json";
    string image_path = "D:/ml/code/anomalib/datasets/MVTec/bottle/test/broken_large/000.png";
    string image_dir  = "D:/ml/code/anomalib/datasets/MVTec/bottle/test/broken_large";
    string save_dir   = "D:/ml/code/anomalib-tensorrt-cpp/result"; // 注意目录不会自动创建,要手动创建才会保存
    bool efficient_ad = true; // 是否使用efficient_ad模型

    // 创建推理器
    auto inference = Inference(model_path, meta_path, efficient_ad);

    // 单张图片推理
    cv::Mat result = inference.single(image_path, save_dir);
    cv::resize(result, result, {1500, 500});
    cv::imshow("result", result);
    cv::waitKey(0);

    // 多张图片推理
    inference.multi(image_dir, save_dir);
    return 0;
}
```

# 下载tensorrt和opencv

> 下载安装cuda,cudnn,tensorrt
>
> [CUDA Toolkit Archive | NVIDIA Developer](https://developer.nvidia.com/cuda-toolkit-archive)
>
> [cuDNN Archive | NVIDIA Developer](https://developer.nvidia.com/rdp/cudnn-archive)
>
> [NVIDIA Developer Program Membership Required | NVIDIA Developer](https://developer.nvidia.com/nvidia-tensorrt-download)

> https://opencv.org

## 配置环境变量

```yaml
# opencv
$opencv_path\build\x64\vc16\bin

# tensorrt
$tensorrt\bin
$tensorrt\lib
```

# 关于include文件夹

> include文件夹是rapidjson的文件，用来解析json

# Cmake

> cmake版本要设置 `CMakeLists.txt` 中 opencv，tensorrt 路径为自己的路径

# 查看是否缺失dll

> https://github.com/lucasg/Dependencies 这个工具可以查看exe工具是否缺失dll

# 第三方库

处理json使用了rapidjson https://rapidjson.org/zh-cn/

opencv方式参考了mmdeploy https://github.com/open-mmlab/mmdeploy/tree/master/csrc/mmdeploy/utils/opencv
