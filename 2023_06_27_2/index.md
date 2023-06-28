# Presto求解严格中位数的一种方法


<!--more-->


```presto
SELECT target_col*(1-p)+target_col_next*(p-0) median
FROM
  (SELECT target_col,
          row_number() over(
                            ORDER BY target_col) AS rn,
          lead(target_col, 1) over(
                                   ORDER BY target_col) target_col_next
   FROM your_table) t1
JOIN
  (SELECT (count(target_col)+1)/2 mid_index,
          (count(target_col)+1)*0.5%1 p
   FROM your_table) t2 ON t1.rn=t2.mid_index
```

#### 使用

> your_table替换为你需要使用的表名，target_col替换为你需要求中位数的字段名

#### 解释
&nbsp;&nbsp;&nbsp;&nbsp;令cnt = count(target_col)，cnt为奇数则中位数取[cnt+1]/[2]位置的target_col值，
cnt为偶数则中位数为[cnt+1]/[2]、1 + [cnt+1]/[2]位置target_col值和的均值。

&nbsp;&nbsp;&nbsp;&nbsp;rn = [cnt+1]/[2]时，lead函数求得的target_col_next正是1 + [cnt+1]/[2]位置的值。


&nbsp;&nbsp;&nbsp;&nbsp;cnt为奇数则 p=0，那么
\begin{equation*} median = target\\_col \end{equation*}


&nbsp;&nbsp;&nbsp;&nbsp;cnt为偶数则 p=0.5，那么
\begin{equation*} median=target\\_col\*0.5+target\\_col\\_next\*0.5  \end{equation*}
 

