numpy的核心数据结构ndarray是一种多维数据对象
### 基本的创建
numpy内部函数本身创建的就是numpy中的ndarray,比如`np.random.randn(x,y)`以及`np.arrange(x)`,而从外部创建一个需要array函数,这个函数接受基本都数组以及嵌套数组,会返回一个ndarray的对象.

### ndarray的属性
#### dtype
用ndarray.dtype访问,这个一般是numpy自动的分配给数组的属性,但是可以通过`astype`来修改  
```python
In [33]: arr1 = np.array([1, 2, 3], dtype=np.float64)
In [34]: arr2 = np.array([1, 2, 3], dtype=np.int32)
In [35]: arr1.dtype
Out[35]: dtype('float64')
In [36]: arr2.dtype
Out[36]: dtype('int32')
```
 #### 