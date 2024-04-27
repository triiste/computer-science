# 使用Python出现keras报错：AttributeError: ‘str‘ object has no attribute ‘decode‘

## 1.问题说明

keras保存的h5模型，在使用load_model函数时，出现AttributeError: ‘str‘ object has no attribute ‘decode‘问题。

## 2.解决办法

由于h5py模块的版本问题，改用2.10版本

~~~python
pip install h5py==2.10
~~~

至此完美解决！



