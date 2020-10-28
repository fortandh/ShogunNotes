## 线性表 ArrayList
```Java
import java.util.ArrayList;

// ArrayList声明
ArrayList<Integer> itemList = new ArrayList<Integer>();
// 指定初始长度
ArrayList<Integer> itemList = new ArrayList<Integer>(intialCapacity);

// 添加元素
itemList.add(item);
itemList.add(index, item);
itemList.addAll(itemSet);

// 删除元素
itemList.remove(item);
itemList.remove(index);
itemList.removeAll(itemSet);
itemList.removeRange(beginIndex, endIndex);
itemList.clear();

// 查看长度
itemList.size();

// 判断是否为空
itemList.isEmpty();
```

## 栈 Stack
```Java
import java.util.Stack;

// Stack声明
Stack<Integer> stack = new Stack<Integer>();

// 添加元素
stack.push(item);

// 删除元素
stack.pop(item);

// 查询栈顶
stack.peek();

// 判定是否为空
stack.empty();
```

## 队列 Queue
```Java
import java.util.Queue;
import java.util.LinkedList;

// 队列声明
Queue<Integer> queue = new LinkedList<Integer>();

// 添加元素
queue.add(item);
queue.offer(item);

// 删除元素
queue.remove();
queue.poll();

// 查询队顶
queue.element();
queue.peek();

// 查看大小
queue.size();

// 判定是否为空
set.isEmpty();
```

## 双向队列
```Java
import java.util.Deque;
import java.util.LinkedList;

// Deque声明
Deque<Integer> deque = new LinkedList<Integer>();

// 添加元素
deque.addFirst(item);
deque.addLast(item);
deque.offerFirst(item);
deque.offerLast(item);

// 删除元素
deque.removeFirst();
deque.removeLast();
deque.pollFirst();
deque.pollLast();

// 获取元素
deque.getFirst();
deque.getLast();
deque.peekFirst();
deque.peekLast();
```

## 集合 Set
必须提供```equals()```和```hashCode()```方法
```Java
import java.util.Set;
import java.util.HashSet;

// Set声明
Set<Integer> set = new HashSet<Integer>();

// 添加元素
set.add(item);
set.addAll(itemSet);

// 删除元素
set.remove(item);
set.removeAll(itemSet);
set.clear();

// 查看大小
set.size();

// 判定是否为空
set.isEmpty();
```

## 映射 Map
```Java
import java.util.Map;
import java.util.HashMap;

// Map声明
Map<Integer, String> map = new HashMap<Integer, String>();

// 添加元素
map.put(itemKey, itemValue);
map.putAll(itemMap);

// 删除元素
map.remove(itemKey);
map.clear();

// 查看大小
map.size();

// 判定是否为空
map.isEmpty();

// 查看元素是否存在
map.containsKey(itemKey);
map.containsValue(itemValue);

// 根据key获得value
map.get(itemKey);
```

## 堆 PriorityQueue
```Java
import java.util.PriorityQueue;
import java.util.Comparator;

// PriorityQueue声明
PriorityQueue<Integer> pq = new PriorityQueue<Integer>();
PriorityQueue<Integer> pq = new PriorityQueue<Integer>(queueSize, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1);
    }
});

// 添加元素
pq.add(item);
pq.offer(item);

// 删除元素
pq.remove();
pq.poll();
pq.clear();

// 查看堆顶
pq.peek();

// 查看大小
pq.size();

// 判定是否为空
pq.isEmpty();
```

## 排序 sort
```Java
import java.util.Arrays;
import java.util.Comparator;

// 排序代码
Arrays.sort(sortedArray, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1);
    }
});
Arrays.sort(sortedArray, beginIndex, endIndex, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1);
    }
});
```
