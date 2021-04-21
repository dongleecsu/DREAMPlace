芯片EDA拥塞预测数据采集
=====

### 1. 安装DREAMPlace  
按照本repo的`README.md`来安装 DREAMPlace。   
   注意：安装本repo的DREAMPlace。
```shell
git clone --recursive https://github.com/dongleecsu/DREAMPlace.git
```

### 2. Superblue系列数据集下载
下载链接为：
- http://archive.sigda.org/dac2012/contest/dac2012_contest_benchmarks.html
- http://www.ispd.cc/contests/11/ispd2011_contest.html  

下载完数据后，将其解压缩，放置在`DREAMPlace/install/benchmarks`文件夹下面。

### 3. 数据采集
进入`DREAMPlace/install`，运行如下命令，可完成对指定芯片（如superblue2）的数据采集
```shell
python dreamplace/Placer.py test/dac2012/superblue2.json
```
同理，可输入不同的argument来采集更多其他芯片的数据。

说明：由于superblue系列的芯片在ispd2011和dac2012等比赛中均有涉及，且互不相同， 
DREAMPlace默认仅提供了dac2012下面的部分芯片设置json文件，
可拷贝并修改对应芯片名称(以及.json文件内的路径)，从而获得ispd2011比赛中的superblue芯片json。

### 4. 数据格式说明
具体数据采集核心代码请见文件`dreamplace/NonLinearPlace.py`文件，`NonLinearPlace.collect_dataset(…)`函数。

所采集的数据为pklz格式，可采用以下命令读取：
```python
with gzip.open(‘filename.pklz’, ‘rb’) as f:
	data = pickle.load(f)
    # data is a dictionary with keys: 'inputs' and 'outputs'
```
**数据介绍**
```python
# Check NonLinearPlace.collect_dataset() for details.
inputs['db_routing_info']   # 芯片routing阶段的metadata
inputs['db_place_info']     # 芯片placement阶段的metadata
inputs['node_info']         # 芯片所有的cell信息
inputs['pin_info']          # 芯片所有的引脚信息
inputs['net_info']          # 芯片网表信息，包含引脚的互联关系
inputs['net_density_info']  # 芯片网络连接密度

outputs['h_demand_map']     # 水平方向demand图，label
outputs['v_demand_map']     # 垂直方向demand图，label
```
对于图像为输入的模型，可以采用如下输入输出，注意数据为*未归一化特征*：
```python
model_input = [
   inputs['pin_info']['pin_density_map'],   # pin density map
   inputs['net_density_info']['h_net_density_map'],
   inputs['net_density_info']['v_net_density_map']
]

model_output = [
   outputs['h_demand_map'], outputs['v_demand_map']
]
```