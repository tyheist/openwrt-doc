## 实用函数(utils.c/h) 
```c
/**
 * 分配一段连续内存给多个指针对象使用
 * 多个指针对象必须保证同时进行free()操作
 */
#define calloc_a(len, ...) __calloc_a(len, ##__AV_ARGS__, NULL)
```

例子：
```c
struct obj {
    char *name; /** object name */
    int  id;    /** ojbect id */
};

struct obj* obj_new(char *name, int id)
{
    struct obj *new = NULL;
    char *obj_name = NULL;
    
    new = calloc_a(sizoef(*new), &obj_name, strlen(name)+1);
    new->id = id;
    new->name = strcpy(obj_name, name);
    
    return new;
}

void obj_free(struct obj *obj)
{
    /** 同时销毁obj->name, obj指向的内存空间 */
    free(obj);
}
```

```c
/**
 * 计算对象数组个数
 */
#define ARRAY_SIZE(arr) (sizeof(arr) / sizeof((arr)[0]))  
```

---

## socket帮助函数(usock.c/h)
### 类型标志
```c
#define USOCK_TCP 0
#define USOCK_UDP 1

#define USOCK_SERVER		0x0100
#define USOCK_NOCLOEXEC	    0x0200
#define USOCK_NONBLOCK		0x0400
#define USOCK_NUMERIC		0x0800
#define USOCK_IPV6ONLY		0x2000
#define USOCK_IPV4ONLY		0x4000
#define USOCK_UNIX			0x8000
```

### 接口说明
```c
/**
 * 创建一个新的网络sock
 * 
 * @param type - 类型标志
 * @param host - 作为server表示绑定本地地址；作为client表示需连接的地址
 * @param service - 端口
 * @return - sock fd > 0； 错误 < 0
 */
int usock(int type, const char *host, const char *service)
```

----

## 双向链表(list.h)
### 数据结构
```c
struct list_head {
	struct list_head *next;
	struct list_head *prev;
};
```

### 初始化
```c
#define LIST_HEAD_INIT(name) { &(name), &(name) }
#define LIST_HEAD(name)	struct list_head name = LIST_HEAD_INIT(name)
static inline void INIT_LIST_HEAD(struct list_head *list)
```

### 基本操作
#### 加入
```c
/** 
 * 加入链表头部
 */
list_add(struct list_head *_new, struct list_head *head)

/**
 * 加入链表尾部
 */
list_add_tail(struct list_head *_new, struct list_head *head)
```

#### 移动
```c
/**
 * 把指定节点移动到链表头部
 */
list_move(struct list_head *list, struct list_head *head)

/**
 * 把指定节点移动到链表尾部
 */
list_move_tail(struct list_head *entry, struct list_head *head)
```

#### 拼接
```c
/**
 * 将list链表拼接到head链表头部
 */
list_splice(const struct list_head *list, struct list_head *head)

/**
 * 将list链表拼接到head链表尾部
 */
list_splice_tail(struct list_head *list, struct list_head *head)

/**
 * 将list链表拼接到head链表头部，并初始化list
 */
list_splice_init(struct list_head *list, struct list_head *head)

/**
 * 将list链表拼接到head链表尾部，并初始化list
 */
list_splice_tail_init(struct list_head *list, struct list_head *head)
```

#### 删除
```c
/**
 * 把指定节点从链表中删除
 */
list_del(struct list_head *entry)

/**
 * 把指定节点从链表中删除，并初始此节点
 */
list_del_init(struct list_head *entry)
```

#### 获取节点元素
```c
/**
 * 获取当前节点元素
 */
list_entry(ptr, type, field)

/**
 * 获取后一个节点元素
 */
list_first_entry(ptr, type, field)

/**
 * 获取前一个节点元素
 */
list_last_entry(ptr, type, field)
```

### 状态判断
```c
/**
 * 获取当前节点元素
 */
list_entry(ptr, type, field)

/**
 * 获取后一个节点元素
 */
list_first_entry(ptr, type, field)

/**
 * 获取前一个节点元素
 */
list_last_entry(ptr, type, field)
```

### 遍历链表
```c
/**
 * 向后遍历链表，遍历过程不能操作链表，p为链表结构体
 */
list_for_each(p, head)

/**
 * 向后遍历链表，遍历过程可操作链表，p为链表结构体
 */
list_for_each_safe(p, n, head)

/**
 * 向前遍历链表，遍历过程不能操作链表，p为链表结构体
 */
list_for_each_prev(p, h)

/**
 * 向前遍历链表，遍历过程可操作链表，p为链表结构体
 */
list_for_each_prev_safe(p, n, h)

/**
 * 向后遍历链表，遍历过程不能操作链表，p为节点元素结构体
 */
list_for_each_entry(p, h, field)

/**
 * 向后遍历链表，遍历过程可操作链表，p为节点元素结构体
 */
list_for_each_entry_safe(p, n, h, field)

/**
 * 向前遍历链表，遍历过程不能操作链表，p为节点元素结构体
 */
list_for_each_entry_reverse(p, h, field)
```
