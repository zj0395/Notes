# The Design and Implementation of Redis
## 第二章 简单动态字符串
- simple dynamic string(SDS)
- O(1)时间获取长度
- 杜绝了缓存区溢出
- 空间预分配
    1. SDS 长度小于1MB，就分配同样大小未使用空间
    2. SDS 长度大于1MB，就分配1MB未使用空间
- 惰性空间释放，不立刻释放未使用内存
- 二进制安全，读取会和写入完全一样
- 兼容部分 C 字符串函数，最后以`\0`结尾
## 第三章 Redis 的链表
- 广泛用于实现 Redis 的各种功能，比如列表键、发布与订阅、慢查询、监视器等
- 双端
- 无环，表头的 prev 和表尾的 next 都是 NULL
- 带表头指针和表尾指针，可从头也可从尾遍历
- 带长度计数器
- 多态，用`void*`来保存节点值，可保存各种类型
## 第四章 Redis 的字典
### 哈希表的数据结构
```c
typedef struct dictht {
    // 哈希表数组
    dictEntry ** table;

    // 哈希表大小
    unsigned long size;

    // 哈希表大小的掩码，与运算可直接获得索引
    // 值是 size-1
    unsigned long sizemask;

    // 已有节点数量
    unsigned long used;
} dictht;

typedef struct dictEntry {
    
    // 键
    void *key;

    //值
    union{
        void* val;
        uint64_t u64;
        int64_t s64;
    } v;

    // 指向下一个节点，形成链表，以处理冲突(collision)
    struct dictEntry *next;
} dictEntry;
```
### 字典的数据结构
```
typedef struct dict {
    
    // 类型特定函数
    dictType *type;

    // 私有数据，作为参数传给某些需要的函数
    void *private;

    // 哈希表，用两个，以便 rehash
    dictht ht[2];

    // rehash 索引
    // 当 rehash 不在进行中时，值为-1
    int terhashidx;
} dict;

typedef struct dictType {

    // 计算哈希值的函数
    unsigned int (*hashFunction)(const void *key);

    // 复制键的函数
    void *(*keydup)(void *privdata, const void *key);

    // 复制值的函数
    void *(*valdup)(void *privdata, const void *obj);

    //对比键的函数
    int (*keyCompare)(void *privdata, const void *key1, const void *key2);

    // 销毁键的函数
    void (*keyDestructor)(void *privdata, void *key);

    // 销毁值的函数
    void (*valDestructor)(void *privdata, void *obj);

} dictType;
```
### 哈希算法
```
    // 使用字典设置的哈希函数，计算 key 的哈希值
    hash = dict->type->hashFunction(key);

    // 根据 sizemask 计算索引
    // 根据情况不同，ht[x] 可以是 ht[0] 或 ht[1]
    index = hash & dict->ht[x].sizemask;
```
- Redis 使用 MurmurHash
### 解决键冲突
- 每个 `dictEntry` 实际上是一个链表
### rehash
- 随着操作不断执行，哈希表保存的键值对会逐渐增多或减少，为了让哈希表的负载银子（load factor）维持在一个合理范围内，当哈希表保存的键值对数量太多或太少时，程序需要对哈希表进行扩展或收缩
- 从 `ht[0]` rehash 到 `ht[1]` 步骤如下：
    1. 分配空间：如果是扩展，那么`ht[1]`的大小是第一个大于等于`ht[0].used\*2`的2的n次方幂；如果是收缩，那么ht[1]的大小是第一个大于等于ht[0].used的2的n次方幂
    2. 将保存在 ht[0] 中的所有键值对rehash到ht[1]上面，重新计算哈希值和索引值，放到ht[1]上
    3. 将ht[0]全部迁移到ht[1]后，释放ht[0]，将ht[1]设置为ht[0]，并在ht[1]上shel创建一个空的哈希表
- 以下任意两个条件满足时，程序会自动开始进行扩展操作
    1. 服务器目前没有执行 *BGSAVE* 命令或 *BGREWRITEAOF* 命令，并且哈希表的负载因子大于等于1
    2. 服务其目前执行 *BGSAVE* 命令或 *BGREWRITEAOF* 命令，并且哈希表的负载因子大于等于5
- 负载因子: `load_factor = ht[0].used / ht[0].size`
- 负载因子小于0.1时，程序自动开始进行哈希表执行收缩操作
### 渐进式rehash
- 需要 rehash 的值可能非常多，一次性全部rehash可能会导致服务器一段时间内停止工作
- 渐进式 rehash 的步骤
    1. 为ht[1]分配空间，让字典同时持有ht[0]和ht[1]两个哈希表
    2. 在字典中维持一个索引计数器变量rehashidx，设值为0，表示rehash开始
    3. rehash期间，每次对字典进行添加、删除、查找或更新操作时，程序还会顺带将ht[0]哈希表在rehashidx上的所有键值对rehash到ht[1]，完成后将rehashidx加1
    4. 随着字典操作不断进行，在某个时间点ht[0]会全部rehash到ht[1]，程序将rehashidx设置为-1，表示已完成
- 好处是分而治之，避免了集中式rehash带来的庞大计算量
- 渐进式rehash期间，新添加的键值对都会被保存到ht[1]上，删除、查找、更新操作会在两个哈希表上进行
## 第五章 跳跃表 skiplist
- 支持平均O(logN)，最坏O(N)的查找
- 实现比平衡树简单的多，所以不少程序使用跳跃树代替平衡树
- Redis 使用跳跃表作为有序集合键的底层实现之以
- 只有两个地方用到：一个是实现有序集合键，另一个是在集群节点中用作内部数据结构
### 跳跃表的实现
- header : 头节点
- tail : 尾节点
- level : 记录目前跳跃内，层数最大的那个节点的层数（表头节点的层数不算在内）
- length : 记录跳跃表的长度，即跳跃表目前包含的几点数量（表头节点不算在内）
```
typedef struct zskiplistNode {

    // 后退指针，与层无关，始终指向前一个节点
    struct zskiplistNode *backward;

    // 分值
    double score;

    //成员对象
    robj *obj;

    //层
    struct zskiplistLevel {
            
            //前进指针
            struct zskiplistNode *forward;

            // 跨度
            unsigned int span;
    } level[];
} zskiplistNode;
```
- 每一个节点的层高是1-32之间的随机数
- 跳跃表的结构如下
```
typedef struct zskiplist {

    // 表头节点和表尾节点
    struct zskiplist *headr, *tail;

    // 表中节点数量
    unsigned long length;

    // 表中最大层数的节点的层数
    int level;
};
```
## 第六章 整数集合(intset)
- 有序且不重复
```
typedef struct intset {
    
    // 编码方式，如int16,int32,int64
    uint32_t encoding;

    // 集合包含的元素数量
    uint32_t length;

    // 保存元素的数组，取决于编码方式
    int8_t contents[];
}
```
### 整数升级
- 16位升级为32位，先升级空间，复制值，再改变编码方式
- 每次添加新元素都可能会引起升级
### 整数降级
- 整数不会被自动降级
## 第七章 压缩列表(ziplist)
- 每个列表项要么是小整数值，要么是长度比较短的字符串

| 属性    | 类型     | 长度  | 用途                                            |
| :--:    | :--:     | :-:   | :--:                                            |
| zlbytes | uint32_t | 4字节 | 记录压缩列表的长度，用于重新分配内存或计算zlend |
| zltail  | uint32_t | 4字节 | 记录尾节点的偏移，以快速取得尾节点              |
| zllen   | uint16_t | 2字节 | 记录节点的数量                                  |
| entryX  | 列表节点 | 不定  | 节点，长度取决于内容                            |
| zlend   | uint_8   | 1字节 | 0xFF，标记压缩列表的结束                        |
### 节点
- 每个节点由`previous_entry_length`、`encoding`、`content`三部分组成
- `previous_entry_length`的长度可以是1个字节或5个字节。前一节点的长度小于254，就只用1个字节；前一节点的长度大于等于254时，占5个字节，第一个字节是0xFE，后四个字节是前一个节点的长度
- 利用`previous_entry_length`可实现从尾到头的遍历
