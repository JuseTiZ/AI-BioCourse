## 课程作业

关于 Python 的操作，可以参考以下在线资料或书籍：

- [https://www.runoob.com/python3/python3-tutorial.html](https://www.runoob.com/python3/python3-tutorial.html)
- [Python编程：从入门到实践（第2版）](https://book.douban.com/subject/35196328/)

**不推荐使用 ChatGPT 等工具直接生成答案**，作业的重点在于理解和培养编程思维，每一个作业都不存在标准答案。如发现 AI 作答将视情况扣分。某些特定的代码操作（例如数据处理等）和报错信息可以询问 AI 以得知具体解决方法，以下为示例：

> Q：Python DataFrame 如何提取出某列的所有独特值？
>
> A：你可以使用 `unique()` 方法来提取某列的所有独特值。以下是一个示例：
>
> ```python
> import pandas as pd
> 
> # 创建示例 DataFrame
> data = {
>     'A': [1, 2, 3, 2, 1],
>     'B': ['x', 'y', 'x', 'z', 'y']
> }
> df = pd.DataFrame(data)
> 
> # 提取列 'A' 的所有独特值
> unique_values_A = df['A'].unique()
> 
> # 提取列 'B' 的所有独特值
> unique_values_B = df['B'].unique()
> 
> print("Column A's unique values:", unique_values_A)
> print("Column B's unique values:", unique_values_B)
> ```
>
> ```output
> Column A's unique values: [1 2 3]
> Column B's unique values: ['x' 'y' 'z']
> ```


```{toctree}
:caption: 作业目录
:maxdepth: 1

task/task1.md
task/task2.md
