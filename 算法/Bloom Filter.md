**Bloom Filter**  布隆过滤器

Bloom Filter，被译作称布隆过滤器，是一种空间效率很高的随机数据结构，布隆过滤器其实是位图的一种扩展，不同的是要使用**多个哈希函数**。它包括一个很长的二进制向量(位图)和一系列随机映射函数。

首先建立一个m位的位图，然后对于每一个加入的元素，使用k个哈希函数求k个哈希值映射到位图的k个位置，然后将这k个位置的bit全设置为1。下图是k=3的布隆过滤器：

![img](https://img2018.cnblogs.com/blog/601033/201908/601033-20190803170818152-1307878123.jpg)

检索时，我们只要检索这些k个位是不是都是1就可以了：**如果这些位有任何一个0，则被检元素一定不在；如果都是1，则被检元素很可能在**。

> 给A,B两个文件，各存放50亿条URL，每条URL占用64字节，内存限制是4G，让你找出A,B文件共同的URL。

若**允许有一定的错误率**，可采用Bloom filter 。Bloom filter 是 BitMap 的扩展
–若直接采用 BitMap 。那么：
–可用内存 4G B=4*1024*1024*1024*8b 3 40 亿 bit 。由于每条URL 占用 64 字节，即每个 URL 对应的整数可以用log 2 (256 64 )bit=8 64 bit>340 亿 bit 来描述。因而不能用 BitMap 。
–Bloom filter ：通过极少的错误换取了存储空间的极大节省。
将每条 url 使用 Bloom filter 映射为这 340 亿 bit 中的 k 个 bit ，并将这 k 个 bit 置为 1 。
•将文件 a 中的每条 url 使用 Bloom filter 映射为这 340 亿 bit；然后依次读取文件 b 中的每条 url ,检查其 Bloom filter映射的 k 比特是否都为 1 ，如果是，那么该 url 应该是两个文件共同的 url （注意：会有一定的错误率）。

适用范围：可以用来实现数据字典，进行数据的判重，或者集合求交集
**•基本原理及要点：位数组 +k 个独立 hash 函数**。
•常用操作：
 1.将所有的字符串都“记录”到 BitSet 中（插入）。对各个字符串，利用其计算得到的 k 个 hash 值的对应bit 置 1 。
2.检查字符串 str 是否被 BitSet 。
 3.删除字符串 str

如何将字符串 str“ 记录”到 BitSet 中？
–1 ）创建一个 m 位 BitSet ，先将所有位初始化为 0 ，然后选择 k 个不同的哈希函数。第 i 个哈希函数对字符串 str 哈希的结果记为 h (i ,str)且 h (i, str ）的范围是 0 到 m -1 。
–2 ）对于字符串 str ，分别计算 h(1, str) h(2,str) … h(k, str) 。然后将 BitSet 的第 h(1, str) 、h(2, str)…… h(k, str) 位设为 1 ，从而将字符串str 映射到 BitSet 中的 k bit

![image-20201030193555829](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201030193555829.png)

下面是检查字符串str是否被BitSet记录过的过程：

　　对于字符串str，分别计算h（1，str），h（2，str）…… h（k，str）。然后检查BitSet的第h（1，str）、h（2，str）…… h（k，str）位是否为1，若其中任何一位不为1则可以判定str一定没有被记录过。若全部位都是1，则“认为”字符串str存在。

　　若一个字符串对应的Bit不全为1，则可以肯定该字符串一定没有被Bloom Filter记录过。（这是显然的，因为字符串被记录过，其对应的二进制位肯定全部被设为1了）

　　但是若一个字符串对应的Bit全为1，实际上是不能100%的肯定该字符串被Bloom Filter记录过的。（因为有可能该字符串的所有位都刚好是被其他字符串所对应）这种将该字符串划分错的情况，称为false positive 。

(3) 删除字符串过程

  字符串加入了就被不能删除了，因为删除会影响到其他字符串。实在需要删除字符串的可以使用**Counting bloomfilter(CBF)**，这是一种基本Bloom Filter的变体，**CBF将基本Bloom Filter每一个Bit改为一个计数器**，这样就可以实现删除字符串的功能了。

　　Bloom Filter跟单哈希函数Bit-Map不同之处在于：Bloom Filter使用了k个哈希函数，每个字符串跟k个bit对应。从而降低了冲突的概率。



**如何根据输入元素个数 n ，确定位数组 m 的大小及hash 函数个数？**
–当 hash 函数个数 k=(ln2)*(m/n) 时错误率最小。
–在错误率不大于 E 的情况下， m 至少要等于 nlog 2 (1/E)才能表示任意 n 个元素的集合。但 m 还应该更大些，因为还要保证 bit 数组里至少一半为 0 ，则m≥nlog 2 (1/E)*log 2 e ≈nlog 2 (1/E)1.44 。
–例如：假设要求错误率不超过 0.001 ，则此时 m 应大概是 n 的 13 倍，因而 k≈8 。
•注意：这里 m 与 n 的单位不同， m 是 bit 为单位，而 n 则是以元素个数为单位 准确的说是不同元素的个数 。通常单个元素的长度都是有很多 bit 的。所以使用 bloom filter 内存上通常都是节省的。

 #### 总结：Bloom Filter
1.Bloom Filter 中冲突概率取决于：哈希函数的个数 k ，以及 Bitset 的大小。
2.**与 Bit Map 的不同之处在于：**
–Bloom Filter 使用了 k 个哈希函数，每个字符串与 k 个bit 对应，从而降低了冲突的概率。 K 越大，冲突的概率越小。
–**存在查询结果的误判** 。（但节省了存储开销）
–**无需处理碰撞**，因此它**在增加或查找集合元素时所用的时间完全恒定（等于哈希函数的计算时间）**，无论集合元素本身有多大，也无论多少集合元素已经加入到了位数组中。
3.当要存储的元素个数 n 很大时，使用 bloom filter可以 **节省内存开销** 。

4.**缺点**：

- **存在误判**。布隆过滤器可以100%确定一个元素不在集合之中，但不能100%确定一个元素在集合之中。当k个位都为1时，也有可能是其它的元素将这些bit置为1的。

- **删除困难。**一个放入容器的元素映射到位图的k个位置上是1，删除的时候不能简单的直接全部置为0，可能会影响其他元素的判断。

- 与 BitMap 类似，通过 Bitset **无法恢复**所表示的数据集合（因为 HASH 函数是不可逆操作）

  

