<h1>Table of Contents<span class="tocSkip"></span></h1>
<div class="toc"><ul class="toc-item"><li><span><a href="#Dask-:-flexible-library-for-parallel-computing-in-Python" data-toc-modified-id="Dask-:-flexible-library-for-parallel-computing-in-Python-1"><span class="toc-item-num">1&nbsp;&nbsp;</span>Dask : flexible library for parallel computing in Python</a></span><ul class="toc-item"><li><span><a href="#dask.array" data-toc-modified-id="dask.array-1.1"><span class="toc-item-num">1.1&nbsp;&nbsp;</span>dask.array</a></span><ul class="toc-item"><li><span><a href="#Blocked-Algorithm" data-toc-modified-id="Blocked-Algorithm-1.1.1"><span class="toc-item-num">1.1.1&nbsp;&nbsp;</span>Blocked Algorithm</a></span></li></ul></li><li><span><a href="#dask.dataframe" data-toc-modified-id="dask.dataframe-1.2"><span class="toc-item-num">1.2&nbsp;&nbsp;</span>dask.dataframe</a></span></li><li><span><a href="#dask.delayed" data-toc-modified-id="dask.delayed-1.3"><span class="toc-item-num">1.3&nbsp;&nbsp;</span>dask.delayed</a></span></li></ul></li><li><span><a href="#Xarray" data-toc-modified-id="Xarray-2"><span class="toc-item-num">2&nbsp;&nbsp;</span>Xarray</a></span><ul class="toc-item"><li><span><a href="#Dataset" data-toc-modified-id="Dataset-2.1"><span class="toc-item-num">2.1&nbsp;&nbsp;</span>Dataset</a></span></li><li><span><a href="#DataArray" data-toc-modified-id="DataArray-2.2"><span class="toc-item-num">2.2&nbsp;&nbsp;</span>DataArray</a></span></li><li><span><a href="#참고-자료" data-toc-modified-id="참고-자료-2.3"><span class="toc-item-num">2.3&nbsp;&nbsp;</span>참고 자료</a></span><ul class="toc-item"><li><span><a href="#Xarray" data-toc-modified-id="Xarray-2.3.1"><span class="toc-item-num">2.3.1&nbsp;&nbsp;</span>Xarray</a></span><ul class="toc-item"><li><span><a href="#Xarray-in-45-minutes" data-toc-modified-id="Xarray-in-45-minutes-2.3.1.1"><span class="toc-item-num">2.3.1.1&nbsp;&nbsp;</span><a href="https://xarray-contrib.github.io/xarray-tutorial/oceanhackweek-2020/xarray-oceanhackweek20.html" target="_blank">Xarray in 45 minutes</a></a></span></li><li><span><a href="#Xarray-Fundamentals" data-toc-modified-id="Xarray-Fundamentals-2.3.1.2"><span class="toc-item-num">2.3.1.2&nbsp;&nbsp;</span><a href="https://xarray-contrib.github.io/xarray-tutorial/online-tutorial-series/01_xarray_fundamentals.html" target="_blank">Xarray Fundamentals</a></a></span></li><li><span><a href="#Indexing-and-Selecting-Data" data-toc-modified-id="Indexing-and-Selecting-Data-2.3.1.3"><span class="toc-item-num">2.3.1.3&nbsp;&nbsp;</span><a href="https://xarray-contrib.github.io/xarray-tutorial/online-tutorial-series/02_indexing.html" target="_blank">Indexing and Selecting Data</a></a></span></li><li><span><a href="#Computation" data-toc-modified-id="Computation-2.3.1.4"><span class="toc-item-num">2.3.1.4&nbsp;&nbsp;</span><a href="https://xarray-contrib.github.io/xarray-tutorial/online-tutorial-series/03_computation.html" target="_blank">Computation</a></a></span></li><li><span><a href="#Computation-with-Xarray" data-toc-modified-id="Computation-with-Xarray-2.3.1.5"><span class="toc-item-num">2.3.1.5&nbsp;&nbsp;</span><a href="https://xarray-contrib.github.io/xarray-tutorial/scipy-tutorial/03_computation_with_xarray.html" target="_blank">Computation with Xarray</a></a></span></li></ul></li><li><span><a href="#Dask" data-toc-modified-id="Dask-2.3.2"><span class="toc-item-num">2.3.2&nbsp;&nbsp;</span>Dask</a></span><ul class="toc-item"><li><span><a href="#Introduction-to-Dask" data-toc-modified-id="Introduction-to-Dask-2.3.2.1"><span class="toc-item-num">2.3.2.1&nbsp;&nbsp;</span><a href="https://xarray-contrib.github.io/xarray-tutorial/scipy-tutorial/05_intro_to_dask.html" target="_blank">Introduction to Dask</a></a></span></li></ul></li><li><span><a href="#Tutorial-on-reading-large-datasets" data-toc-modified-id="Tutorial-on-reading-large-datasets-2.3.3"><span class="toc-item-num">2.3.3&nbsp;&nbsp;</span><a href="https://www.kaggle.com/rohanrao/tutorial-on-reading-large-datasets" target="_blank">Tutorial on reading large datasets</a></a></span></li></ul></li></ul></li></ul></div>

# Dask : flexible library for parallel computing in Python

## dask.array

### Blocked Algorithm


```python
import numpy as np
```

**정말 큰 데이터를 사용할 경우**
>- 데이터의 일부만을 불러와서 계산


```python
random_data = np.random.normal(10,0.2, size=(1_000_000_000))
```

###### using blocked algorithm


```python
%%time
sums = []

for i in range(0, len(random_data), 1_000_000):
    chunk = random_data[i:i+1_000_000]
    sums.append(chunk.sum())

total = sum(sums)
total
```

    Wall time: 2.04 s
    




    10000005768.362103




```python
%%time
random_data.sum()
```

    Wall time: 2.07 s
    




    10000005768.362015



###### dask.array


```python
import dask.array as da
```


```python
x=da.from_array(random_data, chunks=(1_000_000))
x
```




<table>
<tr>
<td>
<table>
  <thead>
    <tr><td> </td><th> Array </th><th> Chunk </th></tr>
  </thead>
  <tbody>
    <tr><th> Bytes </th><td> 8.00 GB </td> <td> 8.00 MB </td></tr>
    <tr><th> Shape </th><td> (1000000000,) </td> <td> (1000000,) </td></tr>
    <tr><th> Count </th><td> 1001 Tasks </td><td> 1000 Chunks </td></tr>
    <tr><th> Type </th><td> float64 </td><td> numpy.ndarray </td></tr>
  </tbody>
</table>
</td>
<td>
<svg width="170" height="75" style="stroke:rgb(0,0,0);stroke-width:1" >

  <!-- Horizontal lines -->
  <line x1="0" y1="0" x2="120" y2="0" style="stroke-width:2" />
  <line x1="0" y1="25" x2="120" y2="25" style="stroke-width:2" />

  <!-- Vertical lines -->
  <line x1="0" y1="0" x2="0" y2="25" style="stroke-width:2" />
  <line x1="0" y1="0" x2="0" y2="25" />
  <line x1="0" y1="0" x2="0" y2="25" />
  <line x1="0" y1="0" x2="0" y2="25" />
  <line x1="0" y1="0" x2="0" y2="25" />
  <line x1="0" y1="0" x2="0" y2="25" />
  <line x1="0" y1="0" x2="0" y2="25" />
  <line x1="0" y1="0" x2="0" y2="25" />
  <line x1="0" y1="0" x2="0" y2="25" />
  <line x1="1" y1="0" x2="1" y2="25" />
  <line x1="1" y1="0" x2="1" y2="25" />
  <line x1="1" y1="0" x2="1" y2="25" />
  <line x1="1" y1="0" x2="1" y2="25" />
  <line x1="1" y1="0" x2="1" y2="25" />
  <line x1="1" y1="0" x2="1" y2="25" />
  <line x1="1" y1="0" x2="1" y2="25" />
  <line x1="1" y1="0" x2="1" y2="25" />
  <line x1="2" y1="0" x2="2" y2="25" />
  <line x1="2" y1="0" x2="2" y2="25" />
  <line x1="2" y1="0" x2="2" y2="25" />
  <line x1="2" y1="0" x2="2" y2="25" />
  <line x1="2" y1="0" x2="2" y2="25" />
  <line x1="2" y1="0" x2="2" y2="25" />
  <line x1="2" y1="0" x2="2" y2="25" />
  <line x1="2" y1="0" x2="2" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="3" y1="0" x2="3" y2="25" />
  <line x1="4" y1="0" x2="4" y2="25" />
  <line x1="4" y1="0" x2="4" y2="25" />
  <line x1="4" y1="0" x2="4" y2="25" />
  <line x1="4" y1="0" x2="4" y2="25" />
  <line x1="4" y1="0" x2="4" y2="25" />
  <line x1="4" y1="0" x2="4" y2="25" />
  <line x1="4" y1="0" x2="4" y2="25" />
  <line x1="4" y1="0" x2="4" y2="25" />
  <line x1="5" y1="0" x2="5" y2="25" />
  <line x1="5" y1="0" x2="5" y2="25" />
  <line x1="5" y1="0" x2="5" y2="25" />
  <line x1="5" y1="0" x2="5" y2="25" />
  <line x1="5" y1="0" x2="5" y2="25" />
  <line x1="5" y1="0" x2="5" y2="25" />
  <line x1="5" y1="0" x2="5" y2="25" />
  <line x1="5" y1="0" x2="5" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="6" y1="0" x2="6" y2="25" />
  <line x1="7" y1="0" x2="7" y2="25" />
  <line x1="7" y1="0" x2="7" y2="25" />
  <line x1="7" y1="0" x2="7" y2="25" />
  <line x1="7" y1="0" x2="7" y2="25" />
  <line x1="7" y1="0" x2="7" y2="25" />
  <line x1="7" y1="0" x2="7" y2="25" />
  <line x1="7" y1="0" x2="7" y2="25" />
  <line x1="7" y1="0" x2="7" y2="25" />
  <line x1="8" y1="0" x2="8" y2="25" />
  <line x1="8" y1="0" x2="8" y2="25" />
  <line x1="8" y1="0" x2="8" y2="25" />
  <line x1="8" y1="0" x2="8" y2="25" />
  <line x1="8" y1="0" x2="8" y2="25" />
  <line x1="8" y1="0" x2="8" y2="25" />
  <line x1="8" y1="0" x2="8" y2="25" />
  <line x1="8" y1="0" x2="8" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="9" y1="0" x2="9" y2="25" />
  <line x1="10" y1="0" x2="10" y2="25" />
  <line x1="10" y1="0" x2="10" y2="25" />
  <line x1="10" y1="0" x2="10" y2="25" />
  <line x1="10" y1="0" x2="10" y2="25" />
  <line x1="10" y1="0" x2="10" y2="25" />
  <line x1="10" y1="0" x2="10" y2="25" />
  <line x1="10" y1="0" x2="10" y2="25" />
  <line x1="10" y1="0" x2="10" y2="25" />
  <line x1="11" y1="0" x2="11" y2="25" />
  <line x1="11" y1="0" x2="11" y2="25" />
  <line x1="11" y1="0" x2="11" y2="25" />
  <line x1="11" y1="0" x2="11" y2="25" />
  <line x1="11" y1="0" x2="11" y2="25" />
  <line x1="11" y1="0" x2="11" y2="25" />
  <line x1="11" y1="0" x2="11" y2="25" />
  <line x1="11" y1="0" x2="11" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="12" y1="0" x2="12" y2="25" />
  <line x1="13" y1="0" x2="13" y2="25" />
  <line x1="13" y1="0" x2="13" y2="25" />
  <line x1="13" y1="0" x2="13" y2="25" />
  <line x1="13" y1="0" x2="13" y2="25" />
  <line x1="13" y1="0" x2="13" y2="25" />
  <line x1="13" y1="0" x2="13" y2="25" />
  <line x1="13" y1="0" x2="13" y2="25" />
  <line x1="13" y1="0" x2="13" y2="25" />
  <line x1="14" y1="0" x2="14" y2="25" />
  <line x1="14" y1="0" x2="14" y2="25" />
  <line x1="14" y1="0" x2="14" y2="25" />
  <line x1="14" y1="0" x2="14" y2="25" />
  <line x1="14" y1="0" x2="14" y2="25" />
  <line x1="14" y1="0" x2="14" y2="25" />
  <line x1="14" y1="0" x2="14" y2="25" />
  <line x1="14" y1="0" x2="14" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="15" y1="0" x2="15" y2="25" />
  <line x1="16" y1="0" x2="16" y2="25" />
  <line x1="16" y1="0" x2="16" y2="25" />
  <line x1="16" y1="0" x2="16" y2="25" />
  <line x1="16" y1="0" x2="16" y2="25" />
  <line x1="16" y1="0" x2="16" y2="25" />
  <line x1="16" y1="0" x2="16" y2="25" />
  <line x1="16" y1="0" x2="16" y2="25" />
  <line x1="16" y1="0" x2="16" y2="25" />
  <line x1="17" y1="0" x2="17" y2="25" />
  <line x1="17" y1="0" x2="17" y2="25" />
  <line x1="17" y1="0" x2="17" y2="25" />
  <line x1="17" y1="0" x2="17" y2="25" />
  <line x1="17" y1="0" x2="17" y2="25" />
  <line x1="17" y1="0" x2="17" y2="25" />
  <line x1="17" y1="0" x2="17" y2="25" />
  <line x1="17" y1="0" x2="17" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="18" y1="0" x2="18" y2="25" />
  <line x1="19" y1="0" x2="19" y2="25" />
  <line x1="19" y1="0" x2="19" y2="25" />
  <line x1="19" y1="0" x2="19" y2="25" />
  <line x1="19" y1="0" x2="19" y2="25" />
  <line x1="19" y1="0" x2="19" y2="25" />
  <line x1="19" y1="0" x2="19" y2="25" />
  <line x1="19" y1="0" x2="19" y2="25" />
  <line x1="19" y1="0" x2="19" y2="25" />
  <line x1="20" y1="0" x2="20" y2="25" />
  <line x1="20" y1="0" x2="20" y2="25" />
  <line x1="20" y1="0" x2="20" y2="25" />
  <line x1="20" y1="0" x2="20" y2="25" />
  <line x1="20" y1="0" x2="20" y2="25" />
  <line x1="20" y1="0" x2="20" y2="25" />
  <line x1="20" y1="0" x2="20" y2="25" />
  <line x1="20" y1="0" x2="20" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="21" y1="0" x2="21" y2="25" />
  <line x1="22" y1="0" x2="22" y2="25" />
  <line x1="22" y1="0" x2="22" y2="25" />
  <line x1="22" y1="0" x2="22" y2="25" />
  <line x1="22" y1="0" x2="22" y2="25" />
  <line x1="22" y1="0" x2="22" y2="25" />
  <line x1="22" y1="0" x2="22" y2="25" />
  <line x1="22" y1="0" x2="22" y2="25" />
  <line x1="22" y1="0" x2="22" y2="25" />
  <line x1="23" y1="0" x2="23" y2="25" />
  <line x1="23" y1="0" x2="23" y2="25" />
  <line x1="23" y1="0" x2="23" y2="25" />
  <line x1="23" y1="0" x2="23" y2="25" />
  <line x1="23" y1="0" x2="23" y2="25" />
  <line x1="23" y1="0" x2="23" y2="25" />
  <line x1="23" y1="0" x2="23" y2="25" />
  <line x1="23" y1="0" x2="23" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="24" y1="0" x2="24" y2="25" />
  <line x1="25" y1="0" x2="25" y2="25" />
  <line x1="25" y1="0" x2="25" y2="25" />
  <line x1="25" y1="0" x2="25" y2="25" />
  <line x1="25" y1="0" x2="25" y2="25" />
  <line x1="25" y1="0" x2="25" y2="25" />
  <line x1="25" y1="0" x2="25" y2="25" />
  <line x1="25" y1="0" x2="25" y2="25" />
  <line x1="25" y1="0" x2="25" y2="25" />
  <line x1="26" y1="0" x2="26" y2="25" />
  <line x1="26" y1="0" x2="26" y2="25" />
  <line x1="26" y1="0" x2="26" y2="25" />
  <line x1="26" y1="0" x2="26" y2="25" />
  <line x1="26" y1="0" x2="26" y2="25" />
  <line x1="26" y1="0" x2="26" y2="25" />
  <line x1="26" y1="0" x2="26" y2="25" />
  <line x1="26" y1="0" x2="26" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="27" y1="0" x2="27" y2="25" />
  <line x1="28" y1="0" x2="28" y2="25" />
  <line x1="28" y1="0" x2="28" y2="25" />
  <line x1="28" y1="0" x2="28" y2="25" />
  <line x1="28" y1="0" x2="28" y2="25" />
  <line x1="28" y1="0" x2="28" y2="25" />
  <line x1="28" y1="0" x2="28" y2="25" />
  <line x1="28" y1="0" x2="28" y2="25" />
  <line x1="28" y1="0" x2="28" y2="25" />
  <line x1="29" y1="0" x2="29" y2="25" />
  <line x1="29" y1="0" x2="29" y2="25" />
  <line x1="29" y1="0" x2="29" y2="25" />
  <line x1="29" y1="0" x2="29" y2="25" />
  <line x1="29" y1="0" x2="29" y2="25" />
  <line x1="29" y1="0" x2="29" y2="25" />
  <line x1="29" y1="0" x2="29" y2="25" />
  <line x1="29" y1="0" x2="29" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="30" y1="0" x2="30" y2="25" />
  <line x1="31" y1="0" x2="31" y2="25" />
  <line x1="31" y1="0" x2="31" y2="25" />
  <line x1="31" y1="0" x2="31" y2="25" />
  <line x1="31" y1="0" x2="31" y2="25" />
  <line x1="31" y1="0" x2="31" y2="25" />
  <line x1="31" y1="0" x2="31" y2="25" />
  <line x1="31" y1="0" x2="31" y2="25" />
  <line x1="31" y1="0" x2="31" y2="25" />
  <line x1="32" y1="0" x2="32" y2="25" />
  <line x1="32" y1="0" x2="32" y2="25" />
  <line x1="32" y1="0" x2="32" y2="25" />
  <line x1="32" y1="0" x2="32" y2="25" />
  <line x1="32" y1="0" x2="32" y2="25" />
  <line x1="32" y1="0" x2="32" y2="25" />
  <line x1="32" y1="0" x2="32" y2="25" />
  <line x1="32" y1="0" x2="32" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="33" y1="0" x2="33" y2="25" />
  <line x1="34" y1="0" x2="34" y2="25" />
  <line x1="34" y1="0" x2="34" y2="25" />
  <line x1="34" y1="0" x2="34" y2="25" />
  <line x1="34" y1="0" x2="34" y2="25" />
  <line x1="34" y1="0" x2="34" y2="25" />
  <line x1="34" y1="0" x2="34" y2="25" />
  <line x1="34" y1="0" x2="34" y2="25" />
  <line x1="34" y1="0" x2="34" y2="25" />
  <line x1="35" y1="0" x2="35" y2="25" />
  <line x1="35" y1="0" x2="35" y2="25" />
  <line x1="35" y1="0" x2="35" y2="25" />
  <line x1="35" y1="0" x2="35" y2="25" />
  <line x1="35" y1="0" x2="35" y2="25" />
  <line x1="35" y1="0" x2="35" y2="25" />
  <line x1="35" y1="0" x2="35" y2="25" />
  <line x1="35" y1="0" x2="35" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="36" y1="0" x2="36" y2="25" />
  <line x1="37" y1="0" x2="37" y2="25" />
  <line x1="37" y1="0" x2="37" y2="25" />
  <line x1="37" y1="0" x2="37" y2="25" />
  <line x1="37" y1="0" x2="37" y2="25" />
  <line x1="37" y1="0" x2="37" y2="25" />
  <line x1="37" y1="0" x2="37" y2="25" />
  <line x1="37" y1="0" x2="37" y2="25" />
  <line x1="37" y1="0" x2="37" y2="25" />
  <line x1="38" y1="0" x2="38" y2="25" />
  <line x1="38" y1="0" x2="38" y2="25" />
  <line x1="38" y1="0" x2="38" y2="25" />
  <line x1="38" y1="0" x2="38" y2="25" />
  <line x1="38" y1="0" x2="38" y2="25" />
  <line x1="38" y1="0" x2="38" y2="25" />
  <line x1="38" y1="0" x2="38" y2="25" />
  <line x1="38" y1="0" x2="38" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="39" y1="0" x2="39" y2="25" />
  <line x1="40" y1="0" x2="40" y2="25" />
  <line x1="40" y1="0" x2="40" y2="25" />
  <line x1="40" y1="0" x2="40" y2="25" />
  <line x1="40" y1="0" x2="40" y2="25" />
  <line x1="40" y1="0" x2="40" y2="25" />
  <line x1="40" y1="0" x2="40" y2="25" />
  <line x1="40" y1="0" x2="40" y2="25" />
  <line x1="40" y1="0" x2="40" y2="25" />
  <line x1="41" y1="0" x2="41" y2="25" />
  <line x1="41" y1="0" x2="41" y2="25" />
  <line x1="41" y1="0" x2="41" y2="25" />
  <line x1="41" y1="0" x2="41" y2="25" />
  <line x1="41" y1="0" x2="41" y2="25" />
  <line x1="41" y1="0" x2="41" y2="25" />
  <line x1="41" y1="0" x2="41" y2="25" />
  <line x1="41" y1="0" x2="41" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="42" y1="0" x2="42" y2="25" />
  <line x1="43" y1="0" x2="43" y2="25" />
  <line x1="43" y1="0" x2="43" y2="25" />
  <line x1="43" y1="0" x2="43" y2="25" />
  <line x1="43" y1="0" x2="43" y2="25" />
  <line x1="43" y1="0" x2="43" y2="25" />
  <line x1="43" y1="0" x2="43" y2="25" />
  <line x1="43" y1="0" x2="43" y2="25" />
  <line x1="43" y1="0" x2="43" y2="25" />
  <line x1="44" y1="0" x2="44" y2="25" />
  <line x1="44" y1="0" x2="44" y2="25" />
  <line x1="44" y1="0" x2="44" y2="25" />
  <line x1="44" y1="0" x2="44" y2="25" />
  <line x1="44" y1="0" x2="44" y2="25" />
  <line x1="44" y1="0" x2="44" y2="25" />
  <line x1="44" y1="0" x2="44" y2="25" />
  <line x1="44" y1="0" x2="44" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="45" y1="0" x2="45" y2="25" />
  <line x1="46" y1="0" x2="46" y2="25" />
  <line x1="46" y1="0" x2="46" y2="25" />
  <line x1="46" y1="0" x2="46" y2="25" />
  <line x1="46" y1="0" x2="46" y2="25" />
  <line x1="46" y1="0" x2="46" y2="25" />
  <line x1="46" y1="0" x2="46" y2="25" />
  <line x1="46" y1="0" x2="46" y2="25" />
  <line x1="46" y1="0" x2="46" y2="25" />
  <line x1="47" y1="0" x2="47" y2="25" />
  <line x1="47" y1="0" x2="47" y2="25" />
  <line x1="47" y1="0" x2="47" y2="25" />
  <line x1="47" y1="0" x2="47" y2="25" />
  <line x1="47" y1="0" x2="47" y2="25" />
  <line x1="47" y1="0" x2="47" y2="25" />
  <line x1="47" y1="0" x2="47" y2="25" />
  <line x1="47" y1="0" x2="47" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="48" y1="0" x2="48" y2="25" />
  <line x1="49" y1="0" x2="49" y2="25" />
  <line x1="49" y1="0" x2="49" y2="25" />
  <line x1="49" y1="0" x2="49" y2="25" />
  <line x1="49" y1="0" x2="49" y2="25" />
  <line x1="49" y1="0" x2="49" y2="25" />
  <line x1="49" y1="0" x2="49" y2="25" />
  <line x1="49" y1="0" x2="49" y2="25" />
  <line x1="49" y1="0" x2="49" y2="25" />
  <line x1="50" y1="0" x2="50" y2="25" />
  <line x1="50" y1="0" x2="50" y2="25" />
  <line x1="50" y1="0" x2="50" y2="25" />
  <line x1="50" y1="0" x2="50" y2="25" />
  <line x1="50" y1="0" x2="50" y2="25" />
  <line x1="50" y1="0" x2="50" y2="25" />
  <line x1="50" y1="0" x2="50" y2="25" />
  <line x1="50" y1="0" x2="50" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="51" y1="0" x2="51" y2="25" />
  <line x1="52" y1="0" x2="52" y2="25" />
  <line x1="52" y1="0" x2="52" y2="25" />
  <line x1="52" y1="0" x2="52" y2="25" />
  <line x1="52" y1="0" x2="52" y2="25" />
  <line x1="52" y1="0" x2="52" y2="25" />
  <line x1="52" y1="0" x2="52" y2="25" />
  <line x1="52" y1="0" x2="52" y2="25" />
  <line x1="52" y1="0" x2="52" y2="25" />
  <line x1="53" y1="0" x2="53" y2="25" />
  <line x1="53" y1="0" x2="53" y2="25" />
  <line x1="53" y1="0" x2="53" y2="25" />
  <line x1="53" y1="0" x2="53" y2="25" />
  <line x1="53" y1="0" x2="53" y2="25" />
  <line x1="53" y1="0" x2="53" y2="25" />
  <line x1="53" y1="0" x2="53" y2="25" />
  <line x1="53" y1="0" x2="53" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="54" y1="0" x2="54" y2="25" />
  <line x1="55" y1="0" x2="55" y2="25" />
  <line x1="55" y1="0" x2="55" y2="25" />
  <line x1="55" y1="0" x2="55" y2="25" />
  <line x1="55" y1="0" x2="55" y2="25" />
  <line x1="55" y1="0" x2="55" y2="25" />
  <line x1="55" y1="0" x2="55" y2="25" />
  <line x1="55" y1="0" x2="55" y2="25" />
  <line x1="55" y1="0" x2="55" y2="25" />
  <line x1="56" y1="0" x2="56" y2="25" />
  <line x1="56" y1="0" x2="56" y2="25" />
  <line x1="56" y1="0" x2="56" y2="25" />
  <line x1="56" y1="0" x2="56" y2="25" />
  <line x1="56" y1="0" x2="56" y2="25" />
  <line x1="56" y1="0" x2="56" y2="25" />
  <line x1="56" y1="0" x2="56" y2="25" />
  <line x1="56" y1="0" x2="56" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="57" y1="0" x2="57" y2="25" />
  <line x1="58" y1="0" x2="58" y2="25" />
  <line x1="58" y1="0" x2="58" y2="25" />
  <line x1="58" y1="0" x2="58" y2="25" />
  <line x1="58" y1="0" x2="58" y2="25" />
  <line x1="58" y1="0" x2="58" y2="25" />
  <line x1="58" y1="0" x2="58" y2="25" />
  <line x1="58" y1="0" x2="58" y2="25" />
  <line x1="58" y1="0" x2="58" y2="25" />
  <line x1="59" y1="0" x2="59" y2="25" />
  <line x1="59" y1="0" x2="59" y2="25" />
  <line x1="59" y1="0" x2="59" y2="25" />
  <line x1="59" y1="0" x2="59" y2="25" />
  <line x1="59" y1="0" x2="59" y2="25" />
  <line x1="59" y1="0" x2="59" y2="25" />
  <line x1="59" y1="0" x2="59" y2="25" />
  <line x1="59" y1="0" x2="59" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="60" y1="0" x2="60" y2="25" />
  <line x1="61" y1="0" x2="61" y2="25" />
  <line x1="61" y1="0" x2="61" y2="25" />
  <line x1="61" y1="0" x2="61" y2="25" />
  <line x1="61" y1="0" x2="61" y2="25" />
  <line x1="61" y1="0" x2="61" y2="25" />
  <line x1="61" y1="0" x2="61" y2="25" />
  <line x1="61" y1="0" x2="61" y2="25" />
  <line x1="61" y1="0" x2="61" y2="25" />
  <line x1="62" y1="0" x2="62" y2="25" />
  <line x1="62" y1="0" x2="62" y2="25" />
  <line x1="62" y1="0" x2="62" y2="25" />
  <line x1="62" y1="0" x2="62" y2="25" />
  <line x1="62" y1="0" x2="62" y2="25" />
  <line x1="62" y1="0" x2="62" y2="25" />
  <line x1="62" y1="0" x2="62" y2="25" />
  <line x1="62" y1="0" x2="62" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="63" y1="0" x2="63" y2="25" />
  <line x1="64" y1="0" x2="64" y2="25" />
  <line x1="64" y1="0" x2="64" y2="25" />
  <line x1="64" y1="0" x2="64" y2="25" />
  <line x1="64" y1="0" x2="64" y2="25" />
  <line x1="64" y1="0" x2="64" y2="25" />
  <line x1="64" y1="0" x2="64" y2="25" />
  <line x1="64" y1="0" x2="64" y2="25" />
  <line x1="64" y1="0" x2="64" y2="25" />
  <line x1="65" y1="0" x2="65" y2="25" />
  <line x1="65" y1="0" x2="65" y2="25" />
  <line x1="65" y1="0" x2="65" y2="25" />
  <line x1="65" y1="0" x2="65" y2="25" />
  <line x1="65" y1="0" x2="65" y2="25" />
  <line x1="65" y1="0" x2="65" y2="25" />
  <line x1="65" y1="0" x2="65" y2="25" />
  <line x1="65" y1="0" x2="65" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="66" y1="0" x2="66" y2="25" />
  <line x1="67" y1="0" x2="67" y2="25" />
  <line x1="67" y1="0" x2="67" y2="25" />
  <line x1="67" y1="0" x2="67" y2="25" />
  <line x1="67" y1="0" x2="67" y2="25" />
  <line x1="67" y1="0" x2="67" y2="25" />
  <line x1="67" y1="0" x2="67" y2="25" />
  <line x1="67" y1="0" x2="67" y2="25" />
  <line x1="67" y1="0" x2="67" y2="25" />
  <line x1="68" y1="0" x2="68" y2="25" />
  <line x1="68" y1="0" x2="68" y2="25" />
  <line x1="68" y1="0" x2="68" y2="25" />
  <line x1="68" y1="0" x2="68" y2="25" />
  <line x1="68" y1="0" x2="68" y2="25" />
  <line x1="68" y1="0" x2="68" y2="25" />
  <line x1="68" y1="0" x2="68" y2="25" />
  <line x1="68" y1="0" x2="68" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="69" y1="0" x2="69" y2="25" />
  <line x1="70" y1="0" x2="70" y2="25" />
  <line x1="70" y1="0" x2="70" y2="25" />
  <line x1="70" y1="0" x2="70" y2="25" />
  <line x1="70" y1="0" x2="70" y2="25" />
  <line x1="70" y1="0" x2="70" y2="25" />
  <line x1="70" y1="0" x2="70" y2="25" />
  <line x1="70" y1="0" x2="70" y2="25" />
  <line x1="70" y1="0" x2="70" y2="25" />
  <line x1="71" y1="0" x2="71" y2="25" />
  <line x1="71" y1="0" x2="71" y2="25" />
  <line x1="71" y1="0" x2="71" y2="25" />
  <line x1="71" y1="0" x2="71" y2="25" />
  <line x1="71" y1="0" x2="71" y2="25" />
  <line x1="71" y1="0" x2="71" y2="25" />
  <line x1="71" y1="0" x2="71" y2="25" />
  <line x1="71" y1="0" x2="71" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="72" y1="0" x2="72" y2="25" />
  <line x1="73" y1="0" x2="73" y2="25" />
  <line x1="73" y1="0" x2="73" y2="25" />
  <line x1="73" y1="0" x2="73" y2="25" />
  <line x1="73" y1="0" x2="73" y2="25" />
  <line x1="73" y1="0" x2="73" y2="25" />
  <line x1="73" y1="0" x2="73" y2="25" />
  <line x1="73" y1="0" x2="73" y2="25" />
  <line x1="73" y1="0" x2="73" y2="25" />
  <line x1="74" y1="0" x2="74" y2="25" />
  <line x1="74" y1="0" x2="74" y2="25" />
  <line x1="74" y1="0" x2="74" y2="25" />
  <line x1="74" y1="0" x2="74" y2="25" />
  <line x1="74" y1="0" x2="74" y2="25" />
  <line x1="74" y1="0" x2="74" y2="25" />
  <line x1="74" y1="0" x2="74" y2="25" />
  <line x1="74" y1="0" x2="74" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="75" y1="0" x2="75" y2="25" />
  <line x1="76" y1="0" x2="76" y2="25" />
  <line x1="76" y1="0" x2="76" y2="25" />
  <line x1="76" y1="0" x2="76" y2="25" />
  <line x1="76" y1="0" x2="76" y2="25" />
  <line x1="76" y1="0" x2="76" y2="25" />
  <line x1="76" y1="0" x2="76" y2="25" />
  <line x1="76" y1="0" x2="76" y2="25" />
  <line x1="76" y1="0" x2="76" y2="25" />
  <line x1="77" y1="0" x2="77" y2="25" />
  <line x1="77" y1="0" x2="77" y2="25" />
  <line x1="77" y1="0" x2="77" y2="25" />
  <line x1="77" y1="0" x2="77" y2="25" />
  <line x1="77" y1="0" x2="77" y2="25" />
  <line x1="77" y1="0" x2="77" y2="25" />
  <line x1="77" y1="0" x2="77" y2="25" />
  <line x1="77" y1="0" x2="77" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="78" y1="0" x2="78" y2="25" />
  <line x1="79" y1="0" x2="79" y2="25" />
  <line x1="79" y1="0" x2="79" y2="25" />
  <line x1="79" y1="0" x2="79" y2="25" />
  <line x1="79" y1="0" x2="79" y2="25" />
  <line x1="79" y1="0" x2="79" y2="25" />
  <line x1="79" y1="0" x2="79" y2="25" />
  <line x1="79" y1="0" x2="79" y2="25" />
  <line x1="79" y1="0" x2="79" y2="25" />
  <line x1="80" y1="0" x2="80" y2="25" />
  <line x1="80" y1="0" x2="80" y2="25" />
  <line x1="80" y1="0" x2="80" y2="25" />
  <line x1="80" y1="0" x2="80" y2="25" />
  <line x1="80" y1="0" x2="80" y2="25" />
  <line x1="80" y1="0" x2="80" y2="25" />
  <line x1="80" y1="0" x2="80" y2="25" />
  <line x1="80" y1="0" x2="80" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="81" y1="0" x2="81" y2="25" />
  <line x1="82" y1="0" x2="82" y2="25" />
  <line x1="82" y1="0" x2="82" y2="25" />
  <line x1="82" y1="0" x2="82" y2="25" />
  <line x1="82" y1="0" x2="82" y2="25" />
  <line x1="82" y1="0" x2="82" y2="25" />
  <line x1="82" y1="0" x2="82" y2="25" />
  <line x1="82" y1="0" x2="82" y2="25" />
  <line x1="82" y1="0" x2="82" y2="25" />
  <line x1="83" y1="0" x2="83" y2="25" />
  <line x1="83" y1="0" x2="83" y2="25" />
  <line x1="83" y1="0" x2="83" y2="25" />
  <line x1="83" y1="0" x2="83" y2="25" />
  <line x1="83" y1="0" x2="83" y2="25" />
  <line x1="83" y1="0" x2="83" y2="25" />
  <line x1="83" y1="0" x2="83" y2="25" />
  <line x1="83" y1="0" x2="83" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="84" y1="0" x2="84" y2="25" />
  <line x1="85" y1="0" x2="85" y2="25" />
  <line x1="85" y1="0" x2="85" y2="25" />
  <line x1="85" y1="0" x2="85" y2="25" />
  <line x1="85" y1="0" x2="85" y2="25" />
  <line x1="85" y1="0" x2="85" y2="25" />
  <line x1="85" y1="0" x2="85" y2="25" />
  <line x1="85" y1="0" x2="85" y2="25" />
  <line x1="85" y1="0" x2="85" y2="25" />
  <line x1="86" y1="0" x2="86" y2="25" />
  <line x1="86" y1="0" x2="86" y2="25" />
  <line x1="86" y1="0" x2="86" y2="25" />
  <line x1="86" y1="0" x2="86" y2="25" />
  <line x1="86" y1="0" x2="86" y2="25" />
  <line x1="86" y1="0" x2="86" y2="25" />
  <line x1="86" y1="0" x2="86" y2="25" />
  <line x1="86" y1="0" x2="86" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="87" y1="0" x2="87" y2="25" />
  <line x1="88" y1="0" x2="88" y2="25" />
  <line x1="88" y1="0" x2="88" y2="25" />
  <line x1="88" y1="0" x2="88" y2="25" />
  <line x1="88" y1="0" x2="88" y2="25" />
  <line x1="88" y1="0" x2="88" y2="25" />
  <line x1="88" y1="0" x2="88" y2="25" />
  <line x1="88" y1="0" x2="88" y2="25" />
  <line x1="88" y1="0" x2="88" y2="25" />
  <line x1="89" y1="0" x2="89" y2="25" />
  <line x1="89" y1="0" x2="89" y2="25" />
  <line x1="89" y1="0" x2="89" y2="25" />
  <line x1="89" y1="0" x2="89" y2="25" />
  <line x1="89" y1="0" x2="89" y2="25" />
  <line x1="89" y1="0" x2="89" y2="25" />
  <line x1="89" y1="0" x2="89" y2="25" />
  <line x1="89" y1="0" x2="89" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="90" y1="0" x2="90" y2="25" />
  <line x1="91" y1="0" x2="91" y2="25" />
  <line x1="91" y1="0" x2="91" y2="25" />
  <line x1="91" y1="0" x2="91" y2="25" />
  <line x1="91" y1="0" x2="91" y2="25" />
  <line x1="91" y1="0" x2="91" y2="25" />
  <line x1="91" y1="0" x2="91" y2="25" />
  <line x1="91" y1="0" x2="91" y2="25" />
  <line x1="91" y1="0" x2="91" y2="25" />
  <line x1="92" y1="0" x2="92" y2="25" />
  <line x1="92" y1="0" x2="92" y2="25" />
  <line x1="92" y1="0" x2="92" y2="25" />
  <line x1="92" y1="0" x2="92" y2="25" />
  <line x1="92" y1="0" x2="92" y2="25" />
  <line x1="92" y1="0" x2="92" y2="25" />
  <line x1="92" y1="0" x2="92" y2="25" />
  <line x1="92" y1="0" x2="92" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="93" y1="0" x2="93" y2="25" />
  <line x1="94" y1="0" x2="94" y2="25" />
  <line x1="94" y1="0" x2="94" y2="25" />
  <line x1="94" y1="0" x2="94" y2="25" />
  <line x1="94" y1="0" x2="94" y2="25" />
  <line x1="94" y1="0" x2="94" y2="25" />
  <line x1="94" y1="0" x2="94" y2="25" />
  <line x1="94" y1="0" x2="94" y2="25" />
  <line x1="94" y1="0" x2="94" y2="25" />
  <line x1="95" y1="0" x2="95" y2="25" />
  <line x1="95" y1="0" x2="95" y2="25" />
  <line x1="95" y1="0" x2="95" y2="25" />
  <line x1="95" y1="0" x2="95" y2="25" />
  <line x1="95" y1="0" x2="95" y2="25" />
  <line x1="95" y1="0" x2="95" y2="25" />
  <line x1="95" y1="0" x2="95" y2="25" />
  <line x1="95" y1="0" x2="95" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="96" y1="0" x2="96" y2="25" />
  <line x1="97" y1="0" x2="97" y2="25" />
  <line x1="97" y1="0" x2="97" y2="25" />
  <line x1="97" y1="0" x2="97" y2="25" />
  <line x1="97" y1="0" x2="97" y2="25" />
  <line x1="97" y1="0" x2="97" y2="25" />
  <line x1="97" y1="0" x2="97" y2="25" />
  <line x1="97" y1="0" x2="97" y2="25" />
  <line x1="97" y1="0" x2="97" y2="25" />
  <line x1="98" y1="0" x2="98" y2="25" />
  <line x1="98" y1="0" x2="98" y2="25" />
  <line x1="98" y1="0" x2="98" y2="25" />
  <line x1="98" y1="0" x2="98" y2="25" />
  <line x1="98" y1="0" x2="98" y2="25" />
  <line x1="98" y1="0" x2="98" y2="25" />
  <line x1="98" y1="0" x2="98" y2="25" />
  <line x1="98" y1="0" x2="98" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="99" y1="0" x2="99" y2="25" />
  <line x1="100" y1="0" x2="100" y2="25" />
  <line x1="100" y1="0" x2="100" y2="25" />
  <line x1="100" y1="0" x2="100" y2="25" />
  <line x1="100" y1="0" x2="100" y2="25" />
  <line x1="100" y1="0" x2="100" y2="25" />
  <line x1="100" y1="0" x2="100" y2="25" />
  <line x1="100" y1="0" x2="100" y2="25" />
  <line x1="100" y1="0" x2="100" y2="25" />
  <line x1="101" y1="0" x2="101" y2="25" />
  <line x1="101" y1="0" x2="101" y2="25" />
  <line x1="101" y1="0" x2="101" y2="25" />
  <line x1="101" y1="0" x2="101" y2="25" />
  <line x1="101" y1="0" x2="101" y2="25" />
  <line x1="101" y1="0" x2="101" y2="25" />
  <line x1="101" y1="0" x2="101" y2="25" />
  <line x1="101" y1="0" x2="101" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="102" y1="0" x2="102" y2="25" />
  <line x1="103" y1="0" x2="103" y2="25" />
  <line x1="103" y1="0" x2="103" y2="25" />
  <line x1="103" y1="0" x2="103" y2="25" />
  <line x1="103" y1="0" x2="103" y2="25" />
  <line x1="103" y1="0" x2="103" y2="25" />
  <line x1="103" y1="0" x2="103" y2="25" />
  <line x1="103" y1="0" x2="103" y2="25" />
  <line x1="103" y1="0" x2="103" y2="25" />
  <line x1="104" y1="0" x2="104" y2="25" />
  <line x1="104" y1="0" x2="104" y2="25" />
  <line x1="104" y1="0" x2="104" y2="25" />
  <line x1="104" y1="0" x2="104" y2="25" />
  <line x1="104" y1="0" x2="104" y2="25" />
  <line x1="104" y1="0" x2="104" y2="25" />
  <line x1="104" y1="0" x2="104" y2="25" />
  <line x1="104" y1="0" x2="104" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="105" y1="0" x2="105" y2="25" />
  <line x1="106" y1="0" x2="106" y2="25" />
  <line x1="106" y1="0" x2="106" y2="25" />
  <line x1="106" y1="0" x2="106" y2="25" />
  <line x1="106" y1="0" x2="106" y2="25" />
  <line x1="106" y1="0" x2="106" y2="25" />
  <line x1="106" y1="0" x2="106" y2="25" />
  <line x1="106" y1="0" x2="106" y2="25" />
  <line x1="106" y1="0" x2="106" y2="25" />
  <line x1="107" y1="0" x2="107" y2="25" />
  <line x1="107" y1="0" x2="107" y2="25" />
  <line x1="107" y1="0" x2="107" y2="25" />
  <line x1="107" y1="0" x2="107" y2="25" />
  <line x1="107" y1="0" x2="107" y2="25" />
  <line x1="107" y1="0" x2="107" y2="25" />
  <line x1="107" y1="0" x2="107" y2="25" />
  <line x1="107" y1="0" x2="107" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="108" y1="0" x2="108" y2="25" />
  <line x1="109" y1="0" x2="109" y2="25" />
  <line x1="109" y1="0" x2="109" y2="25" />
  <line x1="109" y1="0" x2="109" y2="25" />
  <line x1="109" y1="0" x2="109" y2="25" />
  <line x1="109" y1="0" x2="109" y2="25" />
  <line x1="109" y1="0" x2="109" y2="25" />
  <line x1="109" y1="0" x2="109" y2="25" />
  <line x1="109" y1="0" x2="109" y2="25" />
  <line x1="110" y1="0" x2="110" y2="25" />
  <line x1="110" y1="0" x2="110" y2="25" />
  <line x1="110" y1="0" x2="110" y2="25" />
  <line x1="110" y1="0" x2="110" y2="25" />
  <line x1="110" y1="0" x2="110" y2="25" />
  <line x1="110" y1="0" x2="110" y2="25" />
  <line x1="110" y1="0" x2="110" y2="25" />
  <line x1="110" y1="0" x2="110" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="111" y1="0" x2="111" y2="25" />
  <line x1="112" y1="0" x2="112" y2="25" />
  <line x1="112" y1="0" x2="112" y2="25" />
  <line x1="112" y1="0" x2="112" y2="25" />
  <line x1="112" y1="0" x2="112" y2="25" />
  <line x1="112" y1="0" x2="112" y2="25" />
  <line x1="112" y1="0" x2="112" y2="25" />
  <line x1="112" y1="0" x2="112" y2="25" />
  <line x1="112" y1="0" x2="112" y2="25" />
  <line x1="113" y1="0" x2="113" y2="25" />
  <line x1="113" y1="0" x2="113" y2="25" />
  <line x1="113" y1="0" x2="113" y2="25" />
  <line x1="113" y1="0" x2="113" y2="25" />
  <line x1="113" y1="0" x2="113" y2="25" />
  <line x1="113" y1="0" x2="113" y2="25" />
  <line x1="113" y1="0" x2="113" y2="25" />
  <line x1="113" y1="0" x2="113" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="114" y1="0" x2="114" y2="25" />
  <line x1="115" y1="0" x2="115" y2="25" />
  <line x1="115" y1="0" x2="115" y2="25" />
  <line x1="115" y1="0" x2="115" y2="25" />
  <line x1="115" y1="0" x2="115" y2="25" />
  <line x1="115" y1="0" x2="115" y2="25" />
  <line x1="115" y1="0" x2="115" y2="25" />
  <line x1="115" y1="0" x2="115" y2="25" />
  <line x1="115" y1="0" x2="115" y2="25" />
  <line x1="116" y1="0" x2="116" y2="25" />
  <line x1="116" y1="0" x2="116" y2="25" />
  <line x1="116" y1="0" x2="116" y2="25" />
  <line x1="116" y1="0" x2="116" y2="25" />
  <line x1="116" y1="0" x2="116" y2="25" />
  <line x1="116" y1="0" x2="116" y2="25" />
  <line x1="116" y1="0" x2="116" y2="25" />
  <line x1="116" y1="0" x2="116" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="117" y1="0" x2="117" y2="25" />
  <line x1="118" y1="0" x2="118" y2="25" />
  <line x1="118" y1="0" x2="118" y2="25" />
  <line x1="118" y1="0" x2="118" y2="25" />
  <line x1="118" y1="0" x2="118" y2="25" />
  <line x1="118" y1="0" x2="118" y2="25" />
  <line x1="118" y1="0" x2="118" y2="25" />
  <line x1="118" y1="0" x2="118" y2="25" />
  <line x1="118" y1="0" x2="118" y2="25" />
  <line x1="119" y1="0" x2="119" y2="25" />
  <line x1="119" y1="0" x2="119" y2="25" />
  <line x1="119" y1="0" x2="119" y2="25" />
  <line x1="119" y1="0" x2="119" y2="25" />
  <line x1="119" y1="0" x2="119" y2="25" />
  <line x1="119" y1="0" x2="119" y2="25" />
  <line x1="119" y1="0" x2="119" y2="25" />
  <line x1="119" y1="0" x2="119" y2="25" />
  <line x1="120" y1="0" x2="120" y2="25" style="stroke-width:2" />

  <!-- Colored Rectangle -->
  <polygon points="0.000000,0.000000 120.000000,0.000000 120.000000,25.412617 0.000000,25.412617" style="fill:#ECB172A0;stroke-width:0"/>

  <!-- Text -->
  <text x="60.000000" y="45.412617" font-size="1.0rem" font-weight="100" text-anchor="middle" >1000000000</text>
  <text x="140.000000" y="12.706308" font-size="1.0rem" font-weight="100" text-anchor="middle" transform="rotate(0,140.000000,12.706308)">1</text>
</svg>
</td>
</tr>
</table>




```python
result = x.sum()
```


```python
%%time
result.compute()
```

    Wall time: 910 ms
    




    10000005768.3621



###### using numpy array


```python
import numpy as np
```


```python
%%time
x_np=np.random.normal(10, 0.1, size=(20000,20000))
y_np=x_np.mean(axis=0)[::100]
```

###### using dask array


```python
import dask.array as da
```


```python
%%time
x_da=da.random.normal(10, 0.1, size=(20000,20000), chunks=(1000,1000))
y_da=x_da.mean(axis=0)[::100]

y_da.compute()
```


```python
x_da
```


```python
y_da.visualize()
```

## dask.dataframe

## dask.delayed


```python
from time import sleep
```


```python
def apply_discount(x):
    sleep(1)
    x=x-0.2*x
    return x

def get_total(a,b):
    sleep(1)
    return a+b

def get_total_price(x,y):
    sleep(1)
    a=apply_discount(x)
    b=apply_discount(y)
    get_total(a,b)
```


```python
%%time
x = apply_discount(100)
y = apply_discount(200)
z = get_total_price(x,y)
```

###### dask.delayed


```python
from dask import delayed
```


```python
x1 = delayed(apply_discount)(100)
y1 = delayed(apply_discount)(200)
z1 = delayed(get_total_price)(x1,y1)
```


```python
x1, y1, z1
```


```python
%%time
z1.compute()
```


```python
z1.visualize()
```

###### @delayed


```python
@delayed
def apply_discount1(x):
    sleep(1)
    x=x-0.2*x
    return x

@delayed
def get_total1(a,b):
    sleep(1)
    return a+b

@delayed
def get_total_price1(x,y):
    sleep(1)
    a=apply_discount(x)
    b=apply_discount(y)
    get_total(a,b)
```


```python
x2 = apply_discount1(100)
y2 = apply_discount1(200)
z2 = get_total_price1(x2,y2)
```


```python
z2.visualize()
```


```python
%%time
z2.compute()
```

# Xarray


```python
import matplotlib.pyplot as plt
import numpy as np
import xarray as xr
```

## Dataset


```python
ds = xr.tutorial.load_dataset('air_temperature')
```


```python
ds
```


```python
xr.set_options(display_style='html')
```


```python
ds
```


```python
ds.dims
```


```python
ds.coords
```


```python
ds.var
```


```python
ds.attrs
```


```python
ds.air
```

## DataArray


```python
da = ds.air
```


```python
da
```


```python
da.dims, da.coords, da.attrs
```


```python
da.data
```


```python
type(da.data)
```

###### numpy array style


```python
lat = ds.lat.data
lon = ds.lon.data
air_temperature = ds.air.data
```


```python
plt.figure()
plt.pcolormesh(lon, lat, air_temperature[0])
```


```python
ds.time[0]
```

###### xarray


```python
ds.air.isel(time=0).plot()
```


```python
ds.air.mean('time').plot()
```


```python
ds.sel(time='2013-05-05').air
```

###### indexing


```python
da.isel(lat=20, lon=40).plot()
```


```python
lat_jgrc = 33.56316981293892
lon_jgrc = 126.77427747103866
```


```python
da.sel(lat=lat_jgrc, lon=lon_jgrc, method='nearest').plot()
```


```python
da_c = da
```


```python
da_c.data = da_c.data-273.15
```


```python
da_c.sel(lat=lat_jgrc, lon=lon_jgrc, method='nearest').plot()
```

###### groupby


```python
seasonal_mean = ds.groupby('time.season').mean()
```


```python
seasonal_mean
```


```python
seasonal_mean = seasonal_mean.reindex(season=['DJF', 'MAM', 'JJA', 'SON'])
seasonal_mean
```


```python
seasonal_mean.air.plot(col='season')
```

## 참고 자료

### Xarray

#### [Xarray in 45 minutes](https://xarray-contrib.github.io/xarray-tutorial/oceanhackweek-2020/xarray-oceanhackweek20.html)

#### [Xarray Fundamentals](https://xarray-contrib.github.io/xarray-tutorial/online-tutorial-series/01_xarray_fundamentals.html)

#### [Indexing and Selecting Data](https://xarray-contrib.github.io/xarray-tutorial/online-tutorial-series/02_indexing.html)

#### [Computation](https://xarray-contrib.github.io/xarray-tutorial/online-tutorial-series/03_computation.html)

#### [Computation with Xarray](https://xarray-contrib.github.io/xarray-tutorial/scipy-tutorial/03_computation_with_xarray.html)

### Dask

#### [Introduction to Dask](https://xarray-contrib.github.io/xarray-tutorial/scipy-tutorial/05_intro_to_dask.html)

### [Tutorial on reading large datasets](https://www.kaggle.com/rohanrao/tutorial-on-reading-large-datasets)
