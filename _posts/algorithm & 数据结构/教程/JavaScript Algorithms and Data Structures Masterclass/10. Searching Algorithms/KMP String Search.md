没有视频，只有文档：
https://cs.slides.com/colt_steele/tries-21#/20

## KMP字符串搜索的简介

KMP字符串搜索
• Knutt-Morris-Pratt算法*对朴素方法进行了改进*
• 于1977年发表
• 该算法能*更智能地遍历较长字符串，以减少冗余搜索的量*
![[_posts/algorithm & 数据结构/教程/JavaScript Algorithms and Data Structures Masterclass/10. Searching Algorithms/media/02dae6d5d2ea5f3779898faf6eebe2c2_MD5.jpeg]]

---
## 例子

![[_posts/algorithm & 数据结构/教程/JavaScript Algorithms and Data Structures Masterclass/10. Searching Algorithms/media/926a781ffc619fcbf438bb9522887170_MD5.jpeg]]

![[_posts/algorithm & 数据结构/教程/JavaScript Algorithms and Data Structures Masterclass/10. Searching Algorithms/media/da3ff7c047b65b43411aeea1fbe8b291_MD5.jpeg]]

![[_posts/algorithm & 数据结构/教程/JavaScript Algorithms and Data Structures Masterclass/10. Searching Algorithms/media/6a5b5622ecb4486116e36b95e0657521_MD5.jpeg]]

![[_posts/algorithm & 数据结构/教程/JavaScript Algorithms and Data Structures Masterclass/10. Searching Algorithms/media/9938f142113d7275fb7bed4e1a3fee3c_MD5.jpeg]]

## 你怎么知道要移动多远呢？
你怎么知道要移动多远呢？
在长字符串的匹配部分中找到最长的（真）后缀……
这与短字符串匹配部分中的（正确的）前缀相匹配！
然后相应地移动短字符串！

![[_posts/algorithm & 数据结构/教程/JavaScript Algorithms and Data Structures Masterclass/10. Searching Algorithms/media/07f830e926b51d5384f8cb3a76ba88c1_MD5.jpeg]]

前缀和后缀
为了确定我们可以将较短的字符串移位多少，我们可以预先计算与（真）前缀相匹配的最长（真）后缀的长度。这种制表工作应该在开始在长字符串中查找短字符串之前进行。
![[_posts/algorithm & 数据结构/教程/JavaScript Algorithms and Data Structures Masterclass/10. Searching Algorithms/media/5fdc7506b8220fe1303407f8c133ad71_MD5.jpeg]]

![[_posts/algorithm & 数据结构/教程/JavaScript Algorithms and Data Structures Masterclass/10. Searching Algorithms/media/e8c8c718c77ba082c5ea529d14359eb7_MD5.jpeg]]

