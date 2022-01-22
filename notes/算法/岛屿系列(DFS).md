## 核心考点

- **用 `DFS/BFS` 算法遍历二维数组**。
- 深度优先遍历，体现在递归那部分

## 基本框架

- 二维数组`行数列数`的求法

``` javascript
  let m = grid.length, n = grid[0].length,area = 0;
//	初始化一个全为0的二维数组先是生成[0,0,0],再对每一个进行映射[[],[],[]]
    let visited = new Array(m).fill(0).map(Element => {
        return new Array(n).fill(0);
    })
 // 移动的四个方向
    let direction = [[1,0],[-1,0],[0,1],[0,-1]];
    function DFS(grid,i,j){
        // 当超出边界或者是水或者陆地已经被访问过
        if(i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == 0 || visited[i][j] == 1)
        return 

        // 找到了岛屿就遍历其上下左右所有岛屿，放入已访问
        visited[i][j] = 1;
        for(let k = 0; k < 4; k++){
            // 移动的方向
            let move = direction[k]
            DFS(grid,i+move[0],j+move[1]);
        }
    }
```

## 200.岛屿数量

- 只要遍历`发现一片陆地`，就说明`有一个岛屿`x
- 然后`对该片陆地进行深度优先遍历DFS`，将`其本身和邻居大陆都变为海水`
- 因为`dfs`函数遍历到值为`0`的位置会直接返回，所以只要把经过的位置都设置为`0`，就可以起到不走回头路的作用。

``` javascript
var numIslands = function(grid) {
    let m = grid.length, n = grid[0].length,area = 0;
    let direction = [[1,0],[-1,0],[0,1],[0,-1]];
    function DFS(grid,i,j){
        // 当超出边界或者是水或者陆地已经被访问过
        if(i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == 0)
        return 

        // 找到了大陆，就将其变为水
        grid[i][j] = 0;
        for(let k = 0; k < 4; k++){
            // 移动的方向
            let move = direction[k]
            DFS(grid,i+move[0],j+move[1]);
        }

    }
    for(let i = 0; i < m; i++){
        for(let j = 0; j < n; j++){
            if(grid[i][j] == 1){
                DFS(grid, i, j);
                area++;
            }
        }
    }
    return area;
};
```

## [1254. 统计封闭岛屿的数目]

- 这道题只要想清楚，只要岛屿中有一片陆地位于边界上，就不是封闭岛屿
- 因此在遍历到一片陆地，对其进行DFS时，只要有一片岛屿位于边界，将flag设置为false
- 然后对flag进行判断即可。
- 注意，岛屿的题目，可以不用visited，将陆地淹没掉即可

``` javascript
var closedIsland = function(grid) {
// 没有一片陆地处于边界的岛屿，只要在中间就会被包裹
// 只要岛屿有一片陆地处于边界，那么就设置全局变量flag为1
    const m = grid.length, n = grid[0].length;
    let flag,area = 0;
    function DFS(grid, i, j){
        // 出边界，或者是水域
        if(i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == 1)return;
        // 位于边界上且是岛屿
        if(((i == 0 || i == m -1) && grid[i][j] == 0) || ((j == 0 || j == n -1) && grid[i][j] == 0))       {
            flag = false;
        }
        // 将访问过的陆地变成水域
        grid[i][j] = 1;
        //体现深度优先遍历
        DFS(grid, i - 1, j);
        DFS(grid, i + 1, j);
        DFS(grid, i, j - 1);
        DFS(grid, i, j + 1);
    }
    for(let i = 0; i < m; i++){
        for(let j = 0; j < n; j++){
            // 是陆地才进行深度遍历，组成岛屿，在组成岛屿的过程中，有一个陆地处于边界上，那么就不是封闭岛屿
            if(grid[i][j] == 0){
                flag = true;
                DFS(grid, i ,j);
                if(flag) area++;
            }
        }
    }
    return area;
};
```

## 695.岛屿的最大面积

- 在遍历的时候，遇到陆地进行深度遍历
- 每遇到一片陆地，在淹掉前length++；然后进行深度优先遍历

``` javascript
var maxAreaOfIsland = function(grid) {
let m = grid.length, n = grid[0].length, max = 0, length = 0, visited = new Array(m).fill(0).map(Element => {
    return new Array(n).fill(0);
})
// visited[0][2] = 1;
console.log(visited);
function DFS(grid, x, y) {
    //如果是0，或者超出边界，或者已经被访问，则退出;
    // console.log(visited[x][y])
    if (x < 0 || x >= m || y < 0 || y >= n || grid[x][y] == 0 || visited[x][y] == 1) {
        return;
    }

    // 是1且未被访问
    length++;
    visited[x][y] = 1;
    // console.log(visited)
    DFS(grid, x - 1, y);
    DFS(grid, x + 1, y);
    DFS(grid, x, y - 1);
    DFS(grid, x, y + 1);
}

for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
        // 对每个点进行DFS
        length = 0;
        DFS(grid, i, j);
        max = length > max ? length : max;
    }
}
return max;
};
```

## 1905 统计子岛屿

- 重点就是，`在对第二个grid2进行深度优先遍历时，判断陆地是否在grid1中也是陆地`（即grid1[i][j]是0还是1）。如果不是，则说明不是子岛屿

``` javascript
var countSubIslands = function(grid1, grid2) {
    let m = grid2.length, n = grid2[0].length, flag, area = 0;
    // 对第二个岛屿进行深度优先遍历，在通过陆地组成岛屿中，只要有有一片陆地在grid1是水域就不行
    function DFS(grid2,i ,j){
        // 超出边界或是水域，停止
        if(i < 0 || i >= m || j < 0 || j >= n || grid2[i][j] == 0) return;

        // 是陆地，判断在grid1中是否也是陆地，如果是水，则未被包含
        if(grid1[i][j] == 0) flag = false;
        grid2[i][j] = 0;
        DFS(grid2, i - 1, j);
        DFS(grid2, i + 1, j);
        DFS(grid2, i, j - 1);
        DFS(grid2, i, j + 1);
    }
    for(let i = 0; i < m; i++){
        for(let j = 0; j < n; j++){
            // 只要发现一片陆地，就对其进行深度优先遍历
            if(grid2[i][j]==1){
                flag = true;
                DFS(grid2,i,j);
                if(flag) area++;
            }
        }
    }
    return area;
};
```

