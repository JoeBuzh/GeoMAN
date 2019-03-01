# GeoMAN
a easy pytorch implement of GeoMAN

[GeoMAN](http://yuxuanliang.com/assets/pdf/ijcai-18/paper.pdf) : Multi-level Attention Networks for Geo-sensory Time Series Prediction.

## Paper
Yuxuan Liang, Songyu Ke, Junbo Zhang, Xiuwen Yi, Yu Zheng, "GeoMAN: Multi-level Attention Networks for Geo-sensory Time Series Prediction", IJCAI, 2018.

## Model Input
The model has the following inputs:
local_inputs: the input of local spatial attention, shape->[batch_size, n_steps_encoder, n_input_encoder]
global_inputs: the input of global spatial attention, shape->[batch_size, n_steps_encoder, n_sensors]
external_inputs: the input of external factors, shape->[batch_size, n_steps_decoder, n_external_input]
local_attn_states: shape->[batch_size, n_input_encoder, n_steps_encoder]
global_attn_states: shape->[batch_size, n_sensors, n_input_encoder, n_steps_encoder]
labels: ground truths, shape->[batch_size, n_steps_decoder, n_output_decoder]

### How to understand the input data?

you can see this(form the issue answer of [GeoMAN](https://github.com/yoshall/GeoMAN)):<br>
（1）local_inputs.npy: shape is (100, 12, 19), this is the data of target sensor, 100 means there are 100 samples, 12 is the encoder time steps, 19 is the N_l in your paper, which means one sensor generate 19 kinds of time series; Local inputs are x^{i,k} in the paper.

（2）global_input_indics.npy: shape is (100,), this is the indics of input samples in the global data;

（3）global_attn_state_indics.npy: shape is (100,), in fact this one is the same with global_input_indics;

（4）external_inputs.npy: shape is (100, 6, 83), 6 is the decoder time steps, 83 is the number of external features;

（5）decoder_gts.npy: shape is (100, 6), 6 is the decoder time steps, this is the target time series value;

（6）global_inputs.npy: shape is (500, 35), where 35 is the number of tensors (N_g in the paper). In fact 500 is meaningless considering that only 100 local inputs, only 100(+12) samples are useful and these 100 samples are extracted by global_input_indics, to form a the true glabal inputs shaped (100, 12, 35). Global inputs are y^l in the paper.

（7）global_attn_state.npy: shape is (500, 35, 19, 12). This is the all the sensed data by 35 sensors. 12 is the enrolled encoder time steps as above. Global attention states are X^l in the paper.

（8） global_attn_state.npy is enough for the input. All others can be generated by this file.

以上和我自己的理解相差不大，具体用中文讲一下我的理解：<br>
（1）每个传感器可以记录多个维度的数据（文中是19个），其中一个是目标属性，其它是相关属性

（2）一个空间里面，可以部署多个传感器（文中是35个）

（3）所以，总的数据格式是：T_data = [500条（每一条数据在时间上是顺移的关系）数据：[35个传感器[每个传感器包含19个其它维度[每个维度包含12个时间步]]]]

（4）在没有确定时间步的原始数据应该是：global_data = [N个时刻：[35个传感器[每个传感器包含19个维度]]]

所有数据都由global_data产生，先由global_data根据时间步产生T_data，再产生如下的数据：

- local_inputs.npy: (100, 12, 19) 是研究目标，也就是选定一个传感器，作为研究对象 (所以传感器维度消失)<br>
- global_input_indics.npy: (100,) 选定一个传感器的情况下，选取100条数据<br>
- global_attn_state_indics.npy:(100,) 选定一个传感器的情况下，选取100条数据<br>
- external_inputs.npy: (100, 6, 83) 选定一个传感器的情况下，选取100条该传感器其它的特征数据（83维，按6个时间步）<br>
- decoder_gts.npy: (100, 6) 选定一个传感器的情况下，对应选取100条目标属性的真实值<br>
- global_inputs.npy: (500, 35) 所有的传感器目标属性数据<br>
- global_attn_state.npy: (500, 35, 19, 12) 所有的传感器数据<br>

Tensorflow implements<br>
[GeoMAN](https://github.com/yoshall/GeoMAN)<br>
