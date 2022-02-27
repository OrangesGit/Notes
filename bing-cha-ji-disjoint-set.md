# 并查集(Disjoint Set)

[教程](https://www.techiedelight.com/disjoint-set-data-structure-union-find-algorithm/)

此代码没有定义constructor，而是通过makeSet方法指定，两者都行。

## 基本构造

主要包含两个方法：

1.  Find(int child)

    &#x20;用来找到当前child的parent是谁
2.  Union(int child, int parent)

    将child和parent合并

同时需要一个数据类型记录他们的关系

## HashMap实现

可以选HashMap，通过get方法找到父亲是谁

```java
package com.company;
import java.util.*;

public class DJSHashMap {
    private HashMap<Integer, Integer> parent = new HashMap<>();
    public DJSHashMap(int n){
        for(int i=0; i<n; i++){
            parent.put(i,i);
        }
    }
    public int Find(int curr){
        if(parent.get(curr) == curr) return curr;
        return Find(parent.get(curr));
    }
    public void Union(int ch, int pa){
        int x = Find(ch);
        int y = Find(pa);
        parent.put(x, y);
    }
    public void printSets(int n){
        for(int i=0; i<n; i++){
            System.out.println(i + "-" + Find(i));
        }
    }
}
```

## int\[] 实现

```java
package com.company;

import java.util.HashMap;

public class DJSInt {
    private int[] parent;
    public DJSInt(int dummy){
        parent = new int[dummy+1];
        for(int i=0; i<=dummy; i++){
            parent[i] = i;
        }
    }
    public int Find(int currNode){
        while (currNode != parent[currNode]) {
            currNode = parent[currNode];
        }
        return currNode;
    }
    public void Union(int a, int b){
        int x = Find(a);
        int y = Find(b);
        if(x != y) parent[x] = y;
    }
    public void printSets(int n){
        for(int i=0; i<n; i++){
            System.out.println(i + "-" + Find(i));
        }
    }
}

```

# Main

```java
package com.company;
import factoryMethod.BenChi;

import java.util.*;
import java.util.Map.Entry;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.atomic.AtomicInteger;

public class Main{

    public static void main(String[] args) throws Exception
    {
        int n = 5;
        DJSInt djs = new DJSInt(n);
        djs.Union(4,3);
        djs.printSets(n);
        djs.Union(2,1);
        djs.printSets(n);
        djs.Union(1,3);
        djs.printSets(n);
    }
}
```

