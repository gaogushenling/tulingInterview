# 实现一个基本的图数据结构，并实现深度优先搜索（DFS）和广度优先搜索（BFS）

<font style="color:rgba(0, 0, 0, 0.82);">实现一个基本的图数据结构可以使用邻接表来表示图。邻接表是一种常用的图表示方法，其中每个节点都有一个列表，存储与其相邻的节点。我们将实现图的基本结构，并提供深度优先搜索（DFS）和广度优先搜索（BFS）算法。以下是详细的代码示例和解释：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">图数据结构及DFS和BFS实现</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">思路</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用一个</font>`<font style="color:rgba(0, 0, 0, 0.82);">Map</font>`<font style="color:rgba(0, 0, 0, 0.82);">来存储图的邻接表，其中键是节点，值是与该节点相邻的节点列表。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">实现DFS和BFS算法来遍历图。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">代码示例</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
import java.util.*;  

public class Graph {  
    private Map<Integer, List<Integer>> adjList; // 邻接表  

    // 构造函数  
    public Graph() {  
        adjList = new HashMap<>();  
    }  

    // 添加边  
    public void addEdge(int source, int destination) {  
        adjList.computeIfAbsent(source, k -> new ArrayList<>()).add(destination);  
        adjList.computeIfAbsent(destination, k -> new ArrayList<>()).add(source); // 无向图  
    }  

    // 深度优先搜索  
    public void dfs(int start) {  
        Set<Integer> visited = new HashSet<>();  
        dfsUtil(start, visited);  
    }  

    private void dfsUtil(int node, Set<Integer> visited) {  
        visited.add(node);  
        System.out.print(node + " ");  

        for (int neighbor : adjList.getOrDefault(node, new ArrayList<>())) {  
            if (!visited.contains(neighbor)) {  
                dfsUtil(neighbor, visited);  
            }  
        }  
    }  

    // 广度优先搜索  
    public void bfs(int start) {  
        Set<Integer> visited = new HashSet<>();  
        Queue<Integer> queue = new LinkedList<>();  

        visited.add(start);  
        queue.add(start);  

        while (!queue.isEmpty()) {  
            int node = queue.poll();  
            System.out.print(node + " ");  

            for (int neighbor : adjList.getOrDefault(node, new ArrayList<>())) {  
                if (!visited.contains(neighbor)) {  
                    visited.add(neighbor);  
                    queue.add(neighbor);  
                }  
            }  
        }  
    }  

    public static void main(String[] args) {  
        Graph graph = new Graph();  
        graph.addEdge(0, 1);  
        graph.addEdge(0, 2);  
        graph.addEdge(1, 2);  
        graph.addEdge(2, 3);  
        graph.addEdge(3, 4);  

        System.out.println("Depth First Search starting from node 0:");  
        graph.dfs(0);  

        System.out.println("\nBreadth First Search starting from node 0:");  
        graph.bfs(0);  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">详细解释</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">Graph类</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">成员变量</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
        * `<font style="color:rgba(0, 0, 0, 0.82);">Map<Integer, List<Integer>> adjList</font>`<font style="color:rgba(0, 0, 0, 0.82);">: 用于存储图的邻接表。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">构造函数</font>**<font style="color:rgba(0, 0, 0, 0.82);">：初始化邻接表。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">addEdge方法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：添加边到图中。</font>
        * <font style="color:rgba(0, 0, 0, 0.82);">使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">computeIfAbsent</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法确保每个节点都有一个列表。</font>
        * <font style="color:rgba(0, 0, 0, 0.82);">对于无向图，添加双向边。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">深度优先搜索（DFS）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">dfs方法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：初始化一个</font>`<font style="color:rgba(0, 0, 0, 0.82);">Set</font>`<font style="color:rgba(0, 0, 0, 0.82);">用于存储已访问节点，并调用</font>`<font style="color:rgba(0, 0, 0, 0.82);">dfsUtil</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">dfsUtil方法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：递归地访问节点。</font>
        * <font style="color:rgba(0, 0, 0, 0.82);">将当前节点标记为已访问并打印。</font>
        * <font style="color:rgba(0, 0, 0, 0.82);">递归访问所有未访问的邻居节点。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">广度优先搜索（BFS）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">bfs方法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用队列实现。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">初始化一个</font>`<font style="color:rgba(0, 0, 0, 0.82);">Set</font>`<font style="color:rgba(0, 0, 0, 0.82);">用于存储已访问节点和一个</font>`<font style="color:rgba(0, 0, 0, 0.82);">Queue</font>`<font style="color:rgba(0, 0, 0, 0.82);">用于存储待访问节点。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">将起始节点标记为已访问并加入队列。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">迭代访问队列中的节点，打印节点并将未访问的邻居节点加入队列。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">main方法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">创建一个</font>`<font style="color:rgba(0, 0, 0, 0.82);">Graph</font>`<font style="color:rgba(0, 0, 0, 0.82);">实例。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">添加一些边。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">调用</font>`<font style="color:rgba(0, 0, 0, 0.82);">dfs</font>`<font style="color:rgba(0, 0, 0, 0.82);">和</font>`<font style="color:rgba(0, 0, 0, 0.82);">bfs</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法从节点0开始遍历图，并打印结果。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这种实现，我们可以创建一个简单的图数据结构，并使用DFS和BFS算法遍历图。</font>



> 更新: 2024-08-30 22:40:40  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/vfdpb50bh04diqwy>