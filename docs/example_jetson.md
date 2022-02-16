## example-jetson

#### 人体检测示例

> 使用cmake构建，调用SDK编译时生成的库文件和头文件

1. 复制所需的依赖项

   ```
   mkdir tengine
   cp -r <tengine-lite-root-dir>/3rdparty/tim-vx ./tengine/
   cp -r <tengine-lite-root-dir>/build/install/* ./tengine/
   mkdir include
   cp <inferpipe-root-dir>/mediapipe/interface/inferpipe_tengine.h ./include/
   mkdir libs
   cp <inferpipe-root-dir>/bazel-bin/mediapipe/examples/desktop/object_detection/libdesktop_tengine_calculators.so ./libs/
   ```
   
   注意Demo中如果使用trt作为推理后端，需要使用inferpipe_tensorrt.h中的InferTensorrtPipe创建对象。

2. 编译工程

   ```
   mkdir build
   cd build
   cmake ..
   make -j`nproc`
   ```

   得到demo_run_detect_main文件用于测试检测
   
   如果编译Debug版本使用下列命令：
   ```
   cmake -DCMAKE_BUILD_TYPE=Debug ..
   ```

3. 测试检测示例

   执行命令

    ```
   ./demo_run_detect_main ${构建计算流程的配置文件} ${计算流程输入节点的名称} ${计算流程输出节点的名称} ${测试视频路径}
    ```

   其中${计算流程输入节点的名称} ${计算流程输出节点的名称}两个值可以参考${构建计算流程的配置文件}中的`input_stream`

   例如

   1. yolov5

      ```
      ./demo_run_detect_main ../object_detection_yolov5_jetson.pbtxt input_frame output_detect ../test.mp4
      ```
      默认使用FP32推理，如果使用FP16，需要修改配置文件object_detection_yolov5_jetson.pbtxt，将use_fp16字段设置为true。
      ```
      node {
       calculator: "TensorrtInferenceCalculator"
       input_stream: "ARRAYS:image_tensor"
       output_stream: "ARRAYS:detection_tensors"
       output_stream: "TENSOR_SHAPE:tensor_shapes"
       node_options: {
         [type.googleapis.com/mediapipe.TensorrtInferenceCalculatorOptions] {
         onnx_path: "/data/fc/tengine/TengineInferPipe/examples/PersonDemo/models/yolov5_pcb.onnx"
         engine_path: "/data/fc/tengine/TengineInferPipe/examples/PersonDemo/models/yolov5_pcb.te"
         output_num: 1
	      use_fp16: true
         }
        }
      }      
      ```

      
