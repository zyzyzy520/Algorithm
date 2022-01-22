## 基础代码

- 实现的重要js方法，`shift`弹出第一个字符，`push`压入字符

``` javascript

graph = {
    A: ['B', 'C'],
    B: ['A', 'C', 'D'],
    C: ['A', 'B', 'D', 'E'],
    D: ['B', 'C', 'E', 'F'],
    E: ['C', 'D'],
    F: ['D']
}

// 图和起始点
function BFS(graph, start) {
    // 广度优先遍历队列，用于添加元素
    let queue = [];
    // 将起点加入队列
    queue.push(start);

    // 存储已经访问过的元素，只要是加队列里的，就是已访问，如果是弹出在设置已访问，会出现同一个顶点被压入多次，因为它可能是多个节点的邻接点。避免走回头路
    let visited = new Set();
    visited.add(start);
    // 只要不为空，就说明还有节点可以继续遍历
    while (queue.length != 0) {
        // 从队列里弹出第一个元素
        let vertex = queue.shift();
        // 获得该元素的邻居[a,v]
        let nodes = graph[vertex];
        // 将未被访问过的邻居加入队列
        nodes.forEach(element => {
            if (!visited.has(element)) {
                queue.push(element);
                // 设置已访问
                visited.add(element);
            }
        });
        console.log(vertex);
    }
}

BFS(graph, 'A');
```

