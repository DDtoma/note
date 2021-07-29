## ConcurrentHashMap数据结构

```java
public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
    implements ConcurrentMap<K,V>, Serializable {
	static class Node<K,V> implements Map.Entry<K,V> {}
	static final class TreeNode<K,V> extends Node<K,V> {}
	transient volatile Node<K,V>[] table;
}
```

ConcurrentHashMap基本的数据结构是数组+链表或者树的结构构成。

## ConcurrentHashMap的分段锁

在1.8之前ConcurrentHashMap是使用segmet来实现分段锁，默认16分段，最大支持16个线程的并发。

在1.8后ConcurrentHashMap使用了synchronized+cas的操作来实现分段锁的功能。

```java
final V putVal(K key, V value, boolean onlyIfAbsent) {
	...
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh; K fk; V fv;  
        if (tab == null || (n = tab.length) == 0)  
            tab = initTable();  
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value)))  
                break;                   // no lock when adding to empty bin  
 		}  
        else if ((fh = f.hash) == MOVED)  
            tab = helpTransfer(tab, f);  
        else if (onlyIfAbsent 
 				 && fh == hash  
                 && ((fk = f.key) == key || (fk != null && key.equals(fk)))  
                 && (fv = f.val) != null)  
            return fv;  
        else {  
            V oldVal = null;  
            synchronized (f) {  // 这里代理了以前的segment分段锁
     		...
            }  
            if (binCount != 0) {  
                if (binCount >= TREEIFY_THRESHOLD)  
                    treeifyBin(tab, i);  
                if (oldVal != null)  
                    return oldVal;  
                break;  
            }  
        }  
    }  
    addCount(1L, binCount);  
    return null;  
}
```

当插入的node是hash下的头节点通过，cas操作写入tab。如果不是使用第一个node作为锁条件进行同步操作，使用synchronized同步关键字代替以前的segment分段锁。

### ConcurrentHashMap扩容和转化
binCount记录在同一hash下，node的个数。当node大于8时，一般的node链表转化为treeNode的树结构，使用的函数为`treeifyBin`。

ConcurrentHashMap的扩容条件不再是0.75的扩容因子`LOAD_FACTOR`，而是在hash表中node个数大于8的情况下在链表向树的转化过程中进行。

```java
private final void treeifyBin(Node<K,V>[] tab, int index) {  
    Node<K,V> b; int n, sc;  
    if (tab != null) {  
        if ((n = tab.length) < MIN_TREEIFY_CAPACITY)  
            tryPresize(n << 1);  // 这里进行扩容
        else if ((b = tabAt(tab, index)) != null && b.hash >= 0) {  
            synchronized (b) {  
                if (tabAt(tab, index) == b) {  
                    TreeNode<K,V> hd = null, tl = null;  
                    for (Node<K,V> e = b; e != null; e = e.next) {  
                        TreeNode<K,V> p =  
                            new TreeNode<K,V>(e.hash, e.key, e.val,  
                                              null, null);  
                        if ((p.prev = tl) == null)  
                            hd = p;  
                        else  
 tl.next = p;  
                        tl = p;  
                    }  
                    setTabAt(tab, index, new TreeBin<K,V>(hd));  
                }  
            }  
        }  
    }  
}
```